# Linux小课堂：P4：Web故障排除系列 🔧

在本节课中，我们将学习Web服务中两种最常见的故障状态码：**403** 和 **404**。我们将了解它们的含义、可能的原因以及如何进行排查和修复。通过简单的实验演示，你将掌握处理这些问题的基本思路。

## 章节一：Web故障代码简介 📊

Web服务对每次请求都会返回一个固定的状态码。这些代码可以帮助我们快速判断请求的处理结果。

以下是常见的状态码系列：

*   **2xx系列**：代表成功。例如，**200** 表示请求已成功处理，页面正常响应。
*   **3xx系列**：代表重定向。例如，**301** 是永久重定向，**302** 是临时重定向。
*   **4xx系列**：代表客户端错误。这是我们本节重点，表示请求有问题，服务器无法处理。常见的如 **403** 和 **404**。
*   **5xx系列**：代表服务器端错误。例如，**502** 表示网关错误，通常是后端服务出现问题。

上一节我们介绍了状态码的基本分类，本节中我们来看看最常见的两种客户端错误。

## 章节二：403 Forbidden 故障排查 🚫

当访问网站的某些内容被禁止时，服务器会返回 **403 Forbidden** 状态码。这通常意味着服务器理解了请求，但拒绝执行。

以下是导致403错误的两种最常见原因：

1.  **IP地址被禁止访问**：服务器配置了规则，拒绝了特定IP的访问请求。
2.  **文件或目录权限不足**：Web服务进程（如Nginx）没有权限读取请求的文件或进入所在的目录。

### 实验一：IP地址被禁止

![](img/f95a220db35ba24b3a5c8f8a39cb3c32_1.png)

我们通过修改Nginx配置来模拟IP被禁的情况。

![](img/f95a220db35ba24b3a5c8f8a39cb3c32_3.png)

首先，查看正常的Nginx欢迎页面（状态码200）：
```bash
# 访问服务器IP，应看到Welcome to nginx页面
curl -I http://your_server_ip/
```

![](img/f95a220db35ba24b3a5c8f8a39cb3c32_5.png)

![](img/f95a220db35ba24b3a5c8f8a39cb3c32_7.png)

接着，编辑Nginx配置文件（如 `/etc/nginx/nginx.conf` 或站点配置文件），在相应的 `location` 块中添加拒绝规则：
```nginx
location / {
    deny 192.168.163.146; # 禁止特定IP
    allow all;            # 允许其他所有IP
    # ... 其他配置
}
```

![](img/f95a220db35ba24b3a5c8f8a39cb3c32_9.png)

![](img/f95a220db35ba24b3a5c8f8a39cb3c32_11.png)

![](img/f95a220db35ba24b3a5c8f8a39cb3c32_12.png)

重新加载Nginx配置后，再次从被禁止的IP访问：
```bash
sudo nginx -t        # 测试配置文件语法
sudo nginx -s reload # 重新加载配置
curl -I http://your_server_ip/
```
此时应返回 **403** 状态码。将配置中的 `deny` 行删除或注释掉，重新加载配置，访问即可恢复正常。

### 实验二：文件权限不足

![](img/f95a220db35ba24b3a5c8f8a39cb3c32_14.png)

![](img/f95a220db35ba24b3a5c8f8a39cb3c32_16.png)

即使IP被允许，如果请求的文件权限设置不正确，也会导致403错误。

我们修改Nginx默认首页文件的权限，使其对“其他用户”不可读：
```bash
# 进入Nginx的默认根目录（通常是 /usr/share/nginx/html 或 /var/www/html）
cd /usr/share/nginx/html
# 移除其他用户的所有权限（读、写、执行）
sudo chmod 600 index.html
```

![](img/f95a220db35ba24b3a5c8f8a39cb3c32_18.png)

此时，Nginx进程（通常以 `www-data` 或 `nginx` 用户运行）将无法读取 `index.html` 文件。刷新网页，会再次看到 **403** 错误。

![](img/f95a220db35ba24b3a5c8f8a39cb3c32_20.png)

![](img/f95a220db35ba24b3a5c8f8a39cb3c32_21.png)

![](img/f95a220db35ba24b3a5c8f8a39cb3c32_23.png)

修复权限，恢复网站访问：
```bash
sudo chmod 644 index.html # 允许所有者读写，其他用户只读
```

![](img/f95a220db35ba24b3a5c8f8a39cb3c32_25.png)

**总结**：遇到403错误，应优先检查：
1.  服务器配置中是否有针对客户端IP的访问限制（如 `deny` 指令）。
2.  网站根目录及目标文件的权限，确保Web服务进程有读取权限（对于目录还需要有执行`x`权限才能进入）。

![](img/f95a220db35ba24b3a5c8f8a39cb3c32_27.png)

![](img/f95a220db35ba24b3a5c8f8a39cb3c32_29.png)

## 章节三：404 Not Found 故障排查 🔍

![](img/f95a220db35ba24b3a5c8f8a39cb3c32_31.png)

当请求的资源在服务器上不存在时，服务器会返回 **404 Not Found** 状态码。

![](img/f95a220db35ba24b3a5c8f8a39cb3c32_33.png)

![](img/f95a220db35ba24b3a5c8f8a39cb3c32_34.png)

以下是导致404错误的两种常见原因：

![](img/f95a220db35ba24b3a5c8f8a39cb3c32_36.png)

![](img/f95a220db35ba24b3a5c8f8a39cb3c32_38.png)

1.  **请求的文件确实不存在**：URL拼写错误，或文件已被删除/移动。
2.  **服务器配置路径错误**：Web服务器（如Nginx）配置中指定的根目录（`root`）不正确，导致它到错误的位置去寻找文件。

![](img/f95a220db35ba24b3a5c8f8a39cb3c32_40.png)

![](img/f95a220db35ba24b3a5c8f8a39cb3c32_41.png)

### 实验一：文件不存在

![](img/f95a220db35ba24b3a5c8f8a39cb3c32_43.png)

最简单的模拟方式就是访问一个不存在的页面。
```bash
curl -I http://your_server_ip/this_file_does_not_exist.html
```
这必然会返回 **404** 状态码。

![](img/f95a220db35ba24b3a5c8f8a39cb3c32_45.png)

### 实验二：服务器根目录配置错误

![](img/f95a220db35ba24b3a5c8f8a39cb3c32_47.png)

![](img/f95a220db35ba24b3a5c8f8a39cb3c32_49.png)

这是运维中更常见的情况。假设Nginx配置的网站根目录是 `/var/www/html`，但实际程序文件被部署到了 `/home/new_project`。

![](img/f95a220db35ba24b3a5c8f8a39cb3c32_51.png)

错误的Nginx配置可能如下：
```nginx
server {
    listen 80;
    server_name your_domain.com;
    root /var/www/html; # 配置的根目录
    index index.html;
    # ...
}
```

![](img/f95a220db35ba24b3a5c8f8a39cb3c32_53.png)

即使 `/home/new_project/index.html` 文件存在，因为Nginx会去 `/var/www/html` 下寻找，所以仍然会返回404。

修复方法是更正 `root` 指令指向正确的路径：
```nginx
    root /home/new_project; # 更正为实际程序目录
```
修改后，记得测试并重载Nginx配置。

**总结**：遇到404错误，应检查：
1.  客户端请求的URL路径是否正确，文件是否存在于服务器。
2.  Web服务器配置中 `root` 指令指定的目录是否正确，程序文件是否部署在该目录下。

![](img/f95a220db35ba24b3a5c8f8a39cb3c32_55.png)

## 课程总结 📝

本节课我们一起学习了Web服务中两个高频故障状态码：

*   **403 Forbidden**：通常由**访问控制**（IP禁止）或**文件系统权限**不足引起。
*   **404 Not Found**：通常由**资源不存在**或**服务器配置路径错误**引起。

![](img/f95a220db35ba24b3a5c8f8a39cb3c32_57.png)

![](img/f95a220db35ba24b3a5c8f8a39cb3c32_58.png)

掌握这些状态码的含义和常见原因，能帮助我们在日常运维中快速定位问题方向。大部分故障都源于这些基础配置，按照上述思路进行排查，可以解决绝大多数403和404问题。记住，修改配置前做好测试和备份是良好的运维习惯。