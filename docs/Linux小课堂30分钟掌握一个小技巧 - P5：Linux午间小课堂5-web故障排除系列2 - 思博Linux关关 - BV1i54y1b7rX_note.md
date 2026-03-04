# Linux小课堂：P5：Web故障排除系列2 - 502错误分析与处理 🔧

在本节课中，我们将学习Web服务故障排除的第二个常见问题：**502 Bad Gateway** 错误。我们将了解502错误的含义，通过实验模拟其产生的原因，并学习定位和解决此类问题的基本思路。

上一节我们介绍了403和404错误，本节中我们来看看服务器内部通信错误——502。

## 502错误概述

![](img/5e75d1a55a69a3dd4a25b60dbd04d769_1.png)

![](img/5e75d1a55a69a3dd4a25b60dbd04d769_3.png)

502错误代码表示 **Bad Gateway**，即错误的网关。当作为网关或代理的服务器（如Nginx）无法从上游服务器（如PHP-FPM、Tomcat或其他后端服务）收到有效的响应时，就会向客户端返回此错误。

![](img/5e75d1a55a69a3dd4a25b60dbd04d769_5.png)

其核心含义是：**前端服务器与后端服务器之间的通信出现了故障**。

![](img/5e75d1a55a69a3dd4a25b60dbd04d769_7.png)

## 实验环境搭建

![](img/5e75d1a55a69a3dd4a25b60dbd04d769_9.png)

![](img/5e75d1a55a69a3dd4a25b60dbd04d769_11.png)

为了理解502错误，我们搭建一个简单的实验环境。环境包含两个核心组件：
1.  **前端代理服务器**：使用Nginx，负责接收用户请求并转发。
2.  **后端应用服务器**：运行PHP-FPM服务，处理具体的PHP请求。

正常的请求流程如下：
```
客户端 -> Nginx (代理) -> PHP-FPM (后端) -> Nginx -> 客户端
```

![](img/5e75d1a55a69a3dd4a25b60dbd04d769_13.png)

## 实验一：后端服务宕机导致502

这是最常见的情况。当后端服务（如PHP-FPM）进程停止时，Nginx无法与之通信，从而引发502错误。

以下是模拟步骤：

1.  **正常访问**：首先确保PHP服务正常运行，访问一个PHP页面（如 `test.php`），页面应能正常显示PHP信息。
2.  **停止后端服务**：关闭PHP-FPM进程。
    ```bash
    # 停止PHP-FPM服务（命令可能因系统而异）
    systemctl stop php-fpm
    ```
3.  **重现错误**：再次刷新 `test.php` 页面。此时，浏览器开发者工具的“网络”(Network)标签页中，该请求的状态码会变为 **502**。

![](img/5e75d1a55a69a3dd4a25b60dbd04d769_15.png)

**结论**：当后端服务不可用时，作为代理的Nginx无法完成请求，于是向客户端返回502错误。

![](img/5e75d1a55a69a3dd4a25b60dbd04d769_17.png)

![](img/5e75d1a55a69a3dd4a25b60dbd04d769_19.png)

![](img/5e75d1a55a69a3dd4a25b60dbd04d769_21.png)

## 实验二：负载均衡中部分节点故障导致间歇性502

![](img/5e75d1a55a69a3dd4a25b60dbd04d769_23.png)

![](img/5e75d1a55a69a3dd4a25b60dbd04d769_25.png)

![](img/5e75d1a55a69a3dd4a25b60dbd04d769_27.png)

![](img/5e75d1a55a69a3dd4a25b60dbd04d769_29.png)

在生产环境中，常用多台后端服务器组成集群，并通过Nginx进行负载均衡。如果集群中某台服务器故障，就可能导致用户访问时“一会儿正常，一会儿502”的情况。

![](img/5e75d1a55a69a3dd4a25b60dbd04d769_31.png)

![](img/5e75d1a55a69a3dd4a25b60dbd04d769_32.png)

![](img/5e75d1a55a69a3dd4a25b60dbd04d769_34.png)

![](img/5e75d1a55a69a3dd4a25b60dbd04d769_36.png)

以下是模拟架构：
-   Nginx作为负载均衡器，配置了一个 `upstream` 块，里面定义了两台后端服务器。
    ```nginx
    upstream backend_servers {
        server 192.168.1.100:7878; # 正常服务器A
        server 192.168.8.1:7878;   # 故障服务器B（实际不存在）
    }
    ```
-   Nginx将请求按轮询（默认）策略分发给这两台服务器。

![](img/5e75d1a55a69a3dd4a25b60dbd04d769_38.png)

![](img/5e75d1a55a69a3dd4a25b60dbd04d769_39.png)

![](img/5e75d1a55a69a3dd4a25b60dbd04d769_41.png)

以下是问题现象与分析：

![](img/5e75d1a55a69a3dd4a25b60dbd04d769_43.png)

![](img/5e75d1a55a69a3dd4a25b60dbd04d769_44.png)

![](img/5e75d1a55a69a3dd4a25b60dbd04d769_46.png)

1.  **访问现象**：用户多次刷新页面，请求有时由正常服务器A处理（返回200），有时被分配到故障服务器B（导致502）。
2.  **问题根源**：负载均衡池中包含了一个不可用的后端节点。

![](img/5e75d1a55a69a3dd4a25b60dbd04d769_48.png)

![](img/5e75d1a55a69a3dd4a25b60dbd04d769_50.png)

![](img/5e75d1a55a69a3dd4a25b60dbd04d769_52.png)

![](img/5e75d1a55a69a3dd4a25b60dbd04d769_53.png)

![](img/5e75d1a55a69a3dd4a25b60dbd04d769_55.png)

Nginx提供了 `proxy_next_upstream` 指令来处理此类问题。该指令定义了在什么情况下，Nginx应将请求转发给 `upstream` 中的下一个服务器。

![](img/5e75d1a55a69a3dd4a25b60dbd04d769_57.png)

![](img/5e75d1a55a69a3dd4a25b60dbd04d769_59.png)

![](img/5e75d1a55a69a3dd4a25b60dbd04d769_61.png)

例如，以下配置表示当与后端服务器通信发生错误（`error`）或超时（`timeout`）时，尝试下一个服务器：
```nginx
location / {
    proxy_pass http://backend_servers;
    proxy_next_upstream error timeout;
}
```

![](img/5e75d1a55a69a3dd4a25b60dbd04d769_62.png)

![](img/5e75d1a55a69a3dd4a25b60dbd04d769_64.png)

**处理思路**：
1.  **紧急处理**：立即从 `upstream` 配置中移除或注释掉故障的服务器IP，使流量全部导向健康的服务器。
    ```nginx
    upstream backend_servers {
        server 192.168.1.100:7878;
        # server 192.168.8.1:7878; # 暂时移除故障节点
    }
    ```
2.  **故障修复**：排查并修复故障服务器B的问题。
3.  **恢复上线**：问题修复后，将服务器B重新加入 `upstream` 配置。

## 故障排查步骤总结

![](img/5e75d1a55a69a3dd4a25b60dbd04d769_66.png)

当遇到502错误时，可以遵循以下步骤进行排查：

![](img/5e75d1a55a69a3dd4a25b60dbd04d769_68.png)

![](img/5e75d1a55a69a3dd4a25b60dbd04d769_70.png)

![](img/5e75d1a55a69a3dd4a25b60dbd04d769_72.png)

![](img/5e75d1a55a69a3dd4a25b60dbd04d769_74.png)

以下是基本的排查流程：

![](img/5e75d1a55a69a3dd4a25b60dbd04d769_76.png)

![](img/5e75d1a55a69a3dd4a25b60dbd04d769_77.png)

![](img/5e75d1a55a69a3dd4a25b60dbd04d769_79.png)

1.  **检查后端服务状态**：确认PHP-FPM、Tomcat等后端进程是否在运行。
    ```bash
    systemctl status php-fpm
    ```
2.  **检查网络连通性**：从Nginx服务器上测试是否能连接到后端服务器的指定端口。
    ```bash
    telnet <后端服务器IP> <后端服务端口>
    # 或
    nc -zv <后端服务器IP> <后端服务端口>
    ```
3.  **检查Nginx错误日志**：Nginx的错误日志（通常位于 `/var/log/nginx/error.log`）中通常会有更详细的错误信息，例如 `connect() failed`。
    ```bash
    tail -f /var/log/nginx/error.log
    ```
4.  **检查负载均衡配置**：如果使用了 `upstream`，检查配置中所有后端节点地址是否正确，服务是否健康。
5.  **检查资源限制**：有时后端服务可能因为内存、CPU资源耗尽而无法响应，需要检查系统资源使用情况。

![](img/5e75d1a55a69a3dd4a25b60dbd04d769_81.png)

## 课程总结

本节课中我们一起学习了Web服务中常见的502错误。

我们首先明确了502错误代表网关通信失败。然后通过两个实验深入理解了其成因：一是后端服务完全宕机；二是负载均衡集群中部分节点故障导致的间歇性问题。最后，我们总结了排查502错误的基本步骤和紧急处理方案。

![](img/5e75d1a55a69a3dd4a25b60dbd04d769_83.png)

![](img/5e75d1a55a69a3dd4a25b60dbd04d769_85.png)

![](img/5e75d1a55a69a3dd4a25b60dbd04d769_86.png)

![](img/5e75d1a55a69a3dd4a25b60dbd04d769_87.png)

![](img/5e75d1a55a69a3dd4a25b60dbd04d769_88.png)

关键点在于：**502错误的焦点是前端服务器与后端服务器之间的连接**。遇到此错误时，应首先检查后端服务状态和网络连通性。