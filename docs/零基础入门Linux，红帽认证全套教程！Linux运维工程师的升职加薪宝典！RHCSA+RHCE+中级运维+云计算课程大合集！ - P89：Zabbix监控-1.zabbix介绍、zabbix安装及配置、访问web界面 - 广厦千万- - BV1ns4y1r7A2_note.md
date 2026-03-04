# Linux运维工程师的升职加薪宝典：P89：Zabbix监控-1.zabbix介绍、zabbix安装及配置、访问web界面

## 概述 📖

![](img/400e33df055e59b8b539040e13a3e622_1.png)

在本节课中，我们将要学习企业级监控系统Zabbix。我们将从监控系统的基本概念讲起，了解为什么监控在运维工作中至关重要，并详细介绍Zabbix的功能、特点以及与其他监控软件的对比。课程的核心部分将带领大家一步步完成Zabbix 5.0 LTS版本的安装、配置，并最终成功访问其Web管理界面。通过本节课的学习，你将能够独立搭建一个基础的Zabbix监控环境。

---

## 监控系统的基本概念 🎯

对于一个企业IT架构而言，监控系统虽然不是最新的技术，但却是必不可少的重要组成部分。所谓“无监控，不运维”。

上一节我们介绍了监控的重要性，本节中我们来看看一个完善的监控软件应该具备哪些核心功能。

一个优秀的监控软件通常需要具备以下四大功能：

1.  **指标数据采集**：这是监控的基础。指标（Metrics）指的是被监控对象的具体参数，例如服务器的CPU使用率、内存占用、磁盘空间等。监控系统需要有能力从目标设备上抓取这些数据。
2.  **指标数据存储**：采集到的数据需要被持久化存储，以便后续查看历史趋势、进行容量规划和故障分析。
3.  **指标数据可视化**：通过友好的Web界面，将存储的数据以图表、仪表盘等形式展示出来，使运维人员能够直观、快速地了解系统状态。
4.  **故障告警功能**：当监控的指标超过预设的阈值或服务出现异常时，监控系统能够通过邮件、短信、钉钉、企业微信等多种渠道及时通知相关人员。

这四大功能也涵盖了**白盒监控**和**黑盒监控**的概念：
*   **白盒监控**：侧重于系统内部指标的采集与存储（对应功能1和2），关注的是“为什么”出问题。
*   **黑盒监控**：侧重于从外部探测服务的可用性并在故障时告警（对应功能4），关注的是“是否”出问题。

---

## 监控对象与常用软件 🖥️

了解了监控系统的功能后，我们来看看在实际工作中，通常需要对哪些对象进行监控。

以下是常见的监控对象分类：

*   **系统层监控**：服务器本身的健康状况，如CPU、内存、磁盘I/O、进程、内核参数等。
*   **网络设备监控**：路由器、交换机等网络设备的端口状态、流量、丢包率等。Zabbix可以通过**SNMP协议**来监控这些设备。
*   **服务软件监控**：各种中间件和服务的状态，例如Nginx、Apache、Tomcat、Kafka、MySQL、Redis等。
*   **业务层监控**：更上层的业务指标，如网站登录数、订单量、用户地域分布等。这通常需要结合日志分析系统（如ELK）来实现。

![](img/400e33df055e59b8b539040e13a3e622_3.png)

![](img/400e33df055e59b8b539040e13a3e622_5.png)

在Linux系统下，我们也可以使用一些命令来临时查看监控指标，例如 `top`、`vmstat`、`iostat` 等。但有了专业的监控系统后，就无需手动执行这些命令了。

![](img/400e33df055e59b8b539040e13a3e622_7.png)

![](img/400e33df055e59b8b539040e13a3e622_8.png)

接下来，我们对比一下行业内常见的监控软件：

![](img/400e33df055e59b8b539040e13a3e622_10.png)

![](img/400e33df055e59b8b539040e13a3e622_11.png)

![](img/400e33df055e59b8b539040e13a3e622_13.png)

![](img/400e33df055e59b8b539040e13a3e622_15.png)

*   **老牌/过时软件**：
    *   **Nagios**：功能较为单一，主要做实时监控，数据难以长期存储。
    *   **Cacti**：主要用于网络流量监控，Web界面不友好，且**本身不具备告警功能**。
    *   **Ganglia**：与Cacti类似，**告警功能依赖第三方插件**，兼容性是个问题。
    *   **Open-Falcon**：小米公司开源，相对冷门，对部分中间件监控支持不足。

*   **主流开源监控软件**：
    *   **Zabbix**：诞生于2012年，是一款**成熟的分布式监控系统**。功能完善，支持数据采集、存储、可视化、告警全链路。擅长监控传统物理机、虚拟机、网络设备等，Web界面友好，支持中文。
    *   **Prometheus**：同样诞生于2012年，2016年后随着容器技术兴起而流行。同样功能完善，但**主要专注于容器和云原生环境的监控**，是目前监控Kubernetes的事实标准。

除了开源版本，这些软件通常也有功能更强大的企业版。国内也有如监控宝、博瑞等优秀的国产监控产品可供选择。

对于本课程，我们将重点学习 **Zabbix**，并在后续容器章节中学习 **Prometheus**。

---

## Zabbix安装前准备 ⚙️

在开始安装Zabbix之前，我们需要做一些准备工作，包括环境初始化、版本选择和架构理解。

我们选择安装 **Zabbix 5.0 LTS** 版本。LTS（Long-Term Support）意为长期支持版本，官方会提供长达数年的技术支持和安全更新，更适合企业生产环境。

Zabbix采用经典的C/S（客户端/服务器）架构：
*   **Zabbix Server**：监控系统的核心，负责接收Agent报告的数据、处理数据、触发告警等。
*   **Zabbix Agent**：安装在需要被监控的主机上，负责采集本地指标数据并发送给Server。
*   **数据库**：用于存储配置信息、监控历史数据和趋势数据。支持MySQL（或MariaDB）和PostgreSQL。
*   **Web界面**：提供基于PHP的图形化管理界面，方便用户配置和查看数据。

![](img/400e33df055e59b8b539040e13a3e622_17.png)

![](img/400e33df055e59b8b539040e13a3e622_19.png)

![](img/400e33df055e59b8b539040e13a3e622_21.png)

我们将使用两台CentOS 7虚拟机进行实验：
*   **Zabbix Server (192.168.0.70)**：安装Server、Web前端、数据库和Agent。
*   **Zabbix Client (192.168.0.71)**：仅安装Agent，作为被监控主机。

![](img/400e33df055e59b8b539040e13a3e622_23.png)

首先，对两台服务器进行系统环境初始化（关闭防火墙/SELinux、配置yum源、同步时间等）。这里我们使用一个初始化脚本完成。

![](img/400e33df055e59b8b539040e13a3e622_25.png)

![](img/400e33df055e59b8b539040e13a3e622_27.png)

![](img/400e33df055e59b8b539040e13a3e622_29.png)

![](img/400e33df055e59b8b539040e13a3e622_31.png)

```bash
# 将初始化脚本上传至服务器并执行
bash system_init.sh
```

![](img/400e33df055e59b8b539040e13a3e622_33.png)

![](img/400e33df055e59b8b539040e13a3e622_35.png)

初始化完成后，为虚拟机拍摄快照以便后续恢复。然后修改主机名：
*   Server端：`hostnamectl set-hostname zabbix-server`
*   Client端：`hostnamectl set-hostname zabbix-client`

![](img/400e33df055e59b8b539040e13a3e622_37.png)

![](img/400e33df055e59b8b539040e13a3e622_39.png)

---

![](img/400e33df055e59b8b539040e13a3e622_41.png)

![](img/400e33df055e59b8b539040e13a3e622_42.png)

![](img/400e33df055e59b8b539040e13a3e622_44.png)

## 安装Zabbix Server 🛠️

![](img/400e33df055e59b8b539040e13a3e622_46.png)

所有准备工作就绪，现在我们在 `zabbix-server` 主机上开始安装Zabbix Server及其相关组件。

![](img/400e33df055e59b8b539040e13a3e622_48.png)

以下是详细的安装步骤：

![](img/400e33df055e59b8b539040e13a3e622_50.png)

![](img/400e33df055e59b8b539040e13a3e622_51.png)

1.  **配置Zabbix官方Yum仓库**
    首先，我们需要从Zabbix官网下载并安装其官方仓库的RPM包，这样我们就可以通过yum命令方便地安装Zabbix。

    ```bash
    rpm -Uvh https://repo.zabbix.com/zabbix/5.0/rhel/7/x86_64/zabbix-release-5.0-1.el7.noarch.rpm
    ```

![](img/400e33df055e59b8b539040e13a3e622_53.png)

2.  **安装Zabbix Server、Agent和前端所需的仓库**
    安装Server和Agent的核心包，以及用于提供Web前端所需软件包的SCL（Software Collections）仓库。

    ```bash
    yum install -y zabbix-server-mysql zabbix-agent
    yum install -y centos-release-scl
    ```

![](img/400e33df055e59b8b539040e13a3e622_55.png)

3.  **启用Zabbix前端仓库并安装Web组件**
    编辑Zabbix仓库文件，启用前端仓库，然后安装Zabbix的Web界面和Apache集成包。

    ```bash
    # 编辑仓库文件，确保 zabbix-frontend 仓库被启用
    vi /etc/yum.repos.d/zabbix.repo
    # 找到 [zabbix-frontend] 部分，确保 enabled=1
    yum install -y zabbix-web-mysql-scl zabbix-apache-conf-scl
    ```

![](img/400e33df055e59b8b539040e13a3e622_57.png)

![](img/400e33df055e59b8b539040e13a3e622_59.png)

4.  **安装并配置数据库（MariaDB）**
    Zabbix需要数据库来存储数据。我们选择安装MariaDB。

    ```bash
    yum install -y mariadb-server
    systemctl start mariadb
    systemctl enable mariadb
    ```
    接着，登录MariaDB，为Zabbix创建专用的数据库和用户。

    ```sql
    mysql
    > create database zabbix character set utf8 collate utf8_bin;
    > create user zabbix@localhost identified by ‘123456‘;
    > grant all privileges on zabbix.* to zabbix@localhost;
    > quit;
    ```
    然后，将Zabbix自带的初始数据库结构导入到刚创建的 `zabbix` 数据库中。

    ```bash
    zcat /usr/share/doc/zabbix-server-mysql*/create.sql.gz | mysql -uzabbix -p123456 zabbix
    ```

![](img/400e33df055e59b8b539040e13a3e622_61.png)

![](img/400e33df055e59b8b539040e13a3e622_63.png)

5.  **配置Zabbix Server连接数据库**
    修改Zabbix Server的配置文件，指定其连接数据库的详细信息。

    ```bash
    vi /etc/zabbix/zabbix_server.conf
    # 修改以下参数：
    DBHost=localhost
    DBName=zabbix
    DBUser=zabbix
    DBPassword=123456
    ```

![](img/400e33df055e59b8b539040e13a3e622_65.png)

![](img/400e33df055e59b8b539040e13a3e622_66.png)

![](img/400e33df055e59b8b539040e13a3e622_68.png)

6.  **配置PHP时区**
    修改Zabbix前端PHP的配置文件，设置正确的时区。

    ```bash
    vi /etc/opt/rh/rh-php72/php-fpm.d/zabbix.conf
    # 找到 ;php_value[date.timezone] = Europe/Riga
    # 取消注释并修改为：
    php_value[date.timezone] = Asia/Shanghai
    ```

![](img/400e33df055e59b8b539040e13a3e622_70.png)

![](img/400e33df055e59b8b539040e13a3e622_72.png)

7.  **启动所有服务并设置开机自启**
    启动Zabbix Server、Agent、Apache和PHP-FPM服务，并确保它们随系统启动。

    ```bash
    systemctl restart zabbix-server zabbix-agent httpd rh-php72-php-fpm
    systemctl enable zabbix-server zabbix-agent httpd rh-php72-php-fpm
    ```

![](img/400e33df055e59b8b539040e13a3e622_74.png)

![](img/400e33df055e59b8b539040e13a3e622_76.png)

至此，Zabbix Server端的安装与配置已经完成。可以通过 `netstat -ntlp` 命令查看相关服务端口（如10051）是否已正常监听。

![](img/400e33df055e59b8b539040e13a3e622_78.png)

![](img/400e33df055e59b8b539040e13a3e622_80.png)

![](img/400e33df055e59b8b539040e13a3e622_82.png)

---

![](img/400e33df055e59b8b539040e13a3e622_84.png)

![](img/400e33df055e59b8b539040e13a3e622_86.png)

![](img/400e33df055e59b8b539040e13a3e622_88.png)

## 访问与初始化Zabbix Web界面 🌐

![](img/400e33df055e59b8b539040e13a3e622_90.png)

![](img/400e33df055e59b8b539040e13a3e622_92.png)

服务启动后，我们就可以通过浏览器访问Zabbix的Web管理界面，完成最后的初始化配置。

![](img/400e33df055e59b8b539040e13a3e622_94.png)

以下是访问和初始化的流程：

![](img/400e33df055e59b8b539040e13a3e622_96.png)

1.  **访问Web界面**
    在浏览器中输入你的Zabbix Server的IP地址和路径：`http://192.168.0.70/zabbix`
    你将看到Zabbix的安装欢迎页面。

![](img/400e33df055e59b8b539040e13a3e622_98.png)

![](img/400e33df055e59b8b539040e13a3e622_100.png)

![](img/400e33df055e59b8b539040e13a3e622_102.png)

2.  **检查前置条件**
    点击“下一步”，系统会检查PHP的各项配置要求。如果之前的配置正确，所有项都应显示为绿色的“OK”。

![](img/400e33df055e59b8b539040e13a3e622_104.png)

![](img/400e33df055e59b8b539040e13a3e622_106.png)

![](img/400e33df055e59b8b539040e13a3e622_108.png)

3.  **配置数据库连接**
    在数据库配置页面，填写以下信息：
    *   数据库类型：**MySQL**
    *   数据库主机：**localhost**
    *   数据库端口：**3306**
    *   数据库名称：**zabbix**
    *   用户：**zabbix**
    *   密码：**123456**
    点击“下一步”。

![](img/400e33df055e59b8b539040e13a3e622_110.png)

![](img/400e33df055e59b8b539040e13a3e622_112.png)

![](img/400e33df055e59b8b539040e13a3e622_114.png)

4.  **配置Zabbix Server详细信息**
    在此页面，可以设置Zabbix Server的名称（可选），其他保持默认即可。点击“下一步”。

![](img/400e33df055e59b8b539040e13a3e622_116.png)

![](img/400e33df055e59b8b539040e13a3e622_118.png)

5.  **确认安装摘要并完成**
    检查所有配置信息无误后，点击“下一步”。系统会将配置写入文件 `zabbix.conf.php`。完成后，点击“完成”。

![](img/400e33df055e59b8b539040e13a3e622_120.png)

6.  **登录系统并设置中文**
    使用默认账号登录：
    *   用户名：**Admin** (注意首字母大写)
    *   密码：**zabbix**
    登录后，为了操作方便，我们可以将界面语言改为中文。点击页面左下角的用户头像（或“User settings”），在“Language”下拉菜单中选择“Chinese (zh_CN)”，然后点击“Update”保存。

![](img/400e33df055e59b8b539040e13a3e622_122.png)

![](img/400e33df055e59b8b539040e13a3e622_123.png)

![](img/400e33df055e59b8b539040e13a3e622_125.png)

恭喜！你现在已经成功进入了Zabbix的中文管理界面。在这里，你可以看到监控概况，并开始后续的主机配置、监控项添加、触发器设置和告警配置等工作。

---

## 总结 🎉

![](img/400e33df055e59b8b539040e13a3e622_127.png)

本节课中我们一起学习了企业级监控系统Zabbix的基础知识与实践操作。

![](img/400e33df055e59b8b539040e13a3e622_129.png)

我们首先了解了监控系统在企业运维中的核心地位及其应具备的四大功能。接着，对比了各类监控软件，明确了Zabbix在传统IT架构监控中的优势。然后，我们详细演示了如何在CentOS 7系统上从零开始，一步步安装和配置Zabbix 5.0 LTS版本，包括配置Yum仓库、安装Server/Agent/Web组件、设置MariaDB数据库，以及调整PHP时区等关键步骤。最后，我们通过浏览器访问Zabbix的Web界面，完成了初始化配置并将界面语言切换为中文，成功搭建起一个可用的Zabbix监控平台。

![](img/400e33df055e59b8b539040e13a3e622_131.png)

现在，你已经拥有了一个基础的监控环境。在接下来的课程中，我们将学习如何添加被监控主机、配置监控项和触发器，让这个监控系统真正运转起来。