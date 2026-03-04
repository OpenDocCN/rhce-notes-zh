# 红帽认证系列工程师RHCE RH124-Chapter16：分析服务器和获取支持 - P1：16-1-分析和管理远程服务器

![](img/499f44cd9243aff55aa149e25540f8ff_0.png)

![](img/499f44cd9243aff55aa149e25540f8ff_2.png)

## 概述
在本节课程中，我们将学习一种全新的服务器管理方式。我们将介绍红帽企业版Linux 8中引入的Cockpit工具，它提供了一个基于Web的图形界面，用于监控和管理远程服务器。此外，我们还将了解如何通过红帽门户网站获取技术支持，并简要提及红帽7和8版本中推出的智能分析工具Insights。

![](img/499f44cd9243aff55aa149e25540f8ff_4.png)

## 引入Cockpit工具
上一节我们概述了本章内容，本节中我们来看看Cockpit工具的具体应用。Cockpit，其名称源自飞行员的驾驶舱，寓意着管理员可以通过它集中控制服务器的各项功能，就像飞行员控制飞机一样。在红帽企业版Linux 7.6版本中，Cockpit作为临时功能引入。而从红帽企业版Linux 8开始，它已成为默认安装的系统组件，管理员只需激活即可使用。

![](img/499f44cd9243aff55aa149e25540f8ff_6.png)

![](img/499f44cd9243aff55aa149e25540f8ff_8.png)

![](img/499f44cd9243aff55aa149e25540f8ff_10.png)

## 安装与激活Cockpit
以下是安装和激活Cockpit服务的步骤：

1.  **安装软件包**：使用`yum`或`dnf`命令安装`cockpit`软件包。
    ```bash
    sudo yum install cockpit
    ```
2.  **启动服务**：Cockpit服务通过`socket`单元管理，需要启动`cockpit.socket`服务。
    ```bash
    sudo systemctl start cockpit.socket
    ```
3.  **设置开机自启**：确保服务在系统启动时自动运行。
    ```bash
    sudo systemctl enable cockpit.socket
    ```
4.  **配置防火墙**：Cockpit默认使用9090端口，需要在防火墙中放行该端口。
    ```bash
    sudo firewall-cmd --permanent --add-port=9090/tcp
    sudo firewall-cmd --reload
    ```

![](img/499f44cd9243aff55aa149e25540f8ff_12.png)

![](img/499f44cd9243aff55aa149e25540f8ff_14.png)

## 访问与使用Cockpit
完成上述配置后，即可通过浏览器访问Cockpit。访问地址格式为：`https://<服务器IP地址或主机名>:9090`。使用系统管理员账户（如`root`）登录后，将看到一个功能丰富的Web管理界面。

![](img/499f44cd9243aff55aa149e25540f8ff_16.png)

Cockpit的左侧导航栏提供了多个管理模块，以下是各模块功能的简要介绍：

![](img/499f44cd9243aff55aa149e25540f8ff_18.png)

![](img/499f44cd9243aff55aa149e25540f8ff_20.png)

![](img/499f44cd9243aff55aa149e25540f8ff_22.png)

*   **概览**：显示系统的基本信息，如主机名、操作系统版本、硬件资源（CPU、内存、磁盘）使用情况等。
*   **日志**：可以按时间和日志级别（如信息、警告、错误）查看系统日志，便于故障排查。
*   **网络**：监控网络流量，查看网络接口状态，并能在此界面直接启用、禁用防火墙或添加简单的防火墙规则。
*   **账户**：管理系统用户账户，可以创建新账户或修改现有账户。
*   **服务**：管理系统的服务、套接字、定时器和路径。可以在此启动、停止、重启服务，或创建自定义的定时器任务。
*   **软件更新**：检查并安装可用的系统更新。
*   **订阅**：查看和管理系统的红帽订阅状态。
*   **终端**：提供一个在浏览器中直接运行的命令行终端，方便执行命令。
*   **诊断报告**：可以快速生成系统诊断报告（SOS报告）。此报告包含系统配置、日志和命令输出，不包含敏感信息，可用于提交给红帽技术支持或分享给受信任的第三方进行分析。
*   **内核转储**：配置系统内核崩溃转储机制（默认未开启）。

![](img/499f44cd9243aff55aa149e25540f8ff_24.png)

![](img/499f44cd9243aff55aa149e25540f8ff_26.png)

![](img/499f44cd9243aff55aa149e25540f8ff_28.png)

## 使用Cockpit管理多台服务器
Cockpit不仅能够管理本地服务器，还可以通过“仪表板”功能集中管理多台远程服务器。

![](img/499f44cd9243aff55aa149e25540f8ff_30.png)

![](img/499f44cd9243aff55aa149e25540f8ff_32.png)

![](img/499f44cd9243aff55aa149e25540f8ff_34.png)

![](img/499f44cd9243aff55aa149e25540f8ff_36.png)

若要在A服务器上管理B服务器，需要在A服务器上安装`cockpit-dashboard`软件包。

![](img/499f44cd9243aff55aa149e25540f8ff_38.png)

![](img/499f44cd9243aff55aa149e25540f8ff_40.png)

![](img/499f44cd9243aff55aa149e25540f8ff_42.png)

![](img/499f44cd9243aff55aa149e25540f8ff_44.png)

```bash
sudo yum install cockpit-dashboard
```

![](img/499f44cd9243aff55aa149e25540f8ff_46.png)

![](img/499f44cd9243aff55aa149e25540f8ff_48.png)

![](img/499f44cd9243aff55aa149e25540f8ff_50.png)

![](img/499f44cd9243aff55aa149e25540f8ff_52.png)

![](img/499f44cd9243aff55aa149e25540f8ff_54.png)

安装完成后，重启Cockpit服务，然后在Cockpit界面的“仪表板”中，通过“添加主机”功能，输入远程服务器的主机名/IP地址和管理员凭据，即可将其添加到管理列表中。之后，便可在一个统一的界面中切换并管理不同的服务器。

![](img/499f44cd9243aff55aa149e25540f8ff_56.png)

![](img/499f44cd9243aff55aa149e25540f8ff_58.png)

![](img/499f44cd9243aff55aa149e25540f8ff_60.png)

![](img/499f44cd9243aff55aa149e25540f8ff_62.png)

## 总结
本节课中我们一起学习了红帽Cockpit工具。我们了解了Cockpit的用途，掌握了其安装、激活和基本访问方法。通过Cockpit的Web界面，我们可以直观地监控系统状态、管理服务、网络、账户等，并能生成诊断报告。此外，利用其仪表板功能，我们还能实现对多台服务器的集中管理，这为日常的系统运维工作提供了极大的便利。