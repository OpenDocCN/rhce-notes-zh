# Linux运维／RHCE／红帽认证／云计算-1：Jumpserver概述和安装 🚀

![](img/7612cb33bfaefb843cacd4b64c7cb958_1.png)

在本节课中，我们将要学习什么是堡垒机（跳板机），并动手安装一个开源的堡垒机系统——Jumpserver。通过本教程，你将理解堡垒机的作用，并掌握其基本的安装与配置方法。

![](img/7612cb33bfaefb843cacd4b64c7cb958_3.png)

---

## 什么是堡垒机/跳板机？

![](img/7612cb33bfaefb843cacd4b64c7cb958_5.png)

![](img/7612cb33bfaefb843cacd4b64c7cb958_6.png)

![](img/7612cb33bfaefb843cacd4b64c7cb958_7.png)

上一节我们介绍了课程目标，本节中我们来看看核心概念。堡垒机，也称为跳板机，是一台用于运维安全管理的特殊服务器。

![](img/7612cb33bfaefb843cacd4b64c7cb958_9.png)

![](img/7612cb33bfaefb843cacd4b64c7cb958_10.png)

它的核心工作流程可以用以下方式描述：

**开发/运维人员 -> 堡垒机 -> 目标服务器**

![](img/7612cb33bfaefb843cacd4b64c7cb958_12.png)

具体来说，开发或运维人员不能直接登录到生产环境的服务器。他们必须先登录到堡垒机，然后通过堡垒机跳转到目标服务器进行操作。这样做的主要目的是为了集中管理访问入口，并增强安全审计能力。

![](img/7612cb33bfaefb843cacd4b64c7cb958_14.png)

---

## 堡垒机与跳板机的区别

了解了基本概念后，我们进一步区分两个易混淆的概念。

*   **跳板机**：功能相对简单，主要提供一台中间服务器作为跳板。但它有一个显著问题：一旦出现操作事故，很难快速定位具体的操作人员和原因，因为缺乏详细的审计日志。
*   **堡垒机**：在跳板机功能的基础上，增加了强大的**安全审计**和**监控**功能。它能够实时收集和记录所有运维操作，确保任何行为都可追溯，从而满足企业安全合规的要求。

![](img/7612cb33bfaefb843cacd4b64c7cb958_16.png)

简单来说，堡垒机是功能更完善、安全性更高的跳板机。

![](img/7612cb33bfaefb843cacd4b64c7cb958_18.png)

---

![](img/7612cb33bfaefb843cacd4b64c7cb958_20.png)

## 主角：Jumpserver 介绍

在众多堡垒机解决方案中，我们将使用 Jumpserver。Jumpserver 是一款使用 Python + Django 开发的开源堡垒机系统，由国内团队维护。它为互联网企业提供了认证、授权、审计和自动化运维等功能。

其官方网站是：`https://www.jumpserver.org/`

---

## 安装环境准备

![](img/7612cb33bfaefb843cacd4b64c7cb958_22.png)

理论部分已经介绍完毕，接下来我们进入实践环节，开始安装 Jumpserver。本次安装基于 CentOS 7.4 系统。

![](img/7612cb33bfaefb843cacd4b64c7cb958_24.png)

![](img/7612cb33bfaefb843cacd4b64c7cb958_26.png)

以下是安装前的准备工作：

![](img/7612cb33bfaefb843cacd4b64c7cb958_28.png)

![](img/7612cb33bfaefb843cacd4b64c7cb958_30.png)

1.  确保系统可以访问互联网，以下载必要的安装包。
2.  准备一个可用的邮箱（如 163 邮箱），用于接收系统邮件，并确保已开启该邮箱的 SMTP/POP3 服务。

![](img/7612cb33bfaefb843cacd4b64c7cb958_32.png)

---

![](img/7612cb33bfaefb843cacd4b64c7cb958_34.png)

## 安装步骤详解

我们将通过一系列命令来完成 Jumpserver 的安装。

### 步骤一：获取安装脚本

首先，我们需要从 GitHub 上获取 Jumpserver 的安装脚本。

```bash
cd /opt
git clone https://github.com/jumpserver/jumpserver.git
cd jumpserver
git checkout master
```

![](img/7612cb33bfaefb843cacd4b64c7cb958_36.png)

### 步骤二：安装系统依赖包

![](img/7612cb33bfaefb843cacd4b64c7cb958_38.png)

Jumpserver 的运行依赖于许多系统软件包。为了节省在线安装时间，我们可以将预先下载好的 RPM 包上传到服务器 `/opt` 目录下，然后批量安装。

![](img/7612cb33bfaefb843cacd4b64c7cb958_40.png)

```bash
cd /opt
rpm -Uvh *.rpm
```
> **注意**：请确保上传的 RPM 包完整，如果其中包含无关的包（如 `epel-release`），请手动删除。

### 步骤三：运行安装脚本

依赖包安装完成后，我们进入安装目录并执行 Python 安装脚本。

```bash
cd /opt/jumpserver/install
python install.py
```

执行脚本后，安装程序会以交互式提问的方式引导我们完成配置。以下是关键配置项的说明：

![](img/7612cb33bfaefb843cacd4b64c7cb958_42.png)

*   **服务器IP地址**：通常直接回车，使用系统检测到的默认IP（如 `192.168.1.63`）。
*   **安装MySQL数据库**：输入 `yes` 以安装并配置新的 MySQL 数据库。
*   **邮件服务器设置**：
    *   SMTP 主机：例如 `smtp.163.com`
    *   SMTP 端口：默认 `25`
    *   邮箱账号：你的完整邮箱地址
    *   邮箱密码：输入密码（注意，此处为明文输入，需谨慎操作）
*   **创建管理员账户**：
    *   管理员用户名：例如 `admin`
    *   管理员密码：例如 `123456`

![](img/7612cb33bfaefb843cacd4b64c7cb958_44.png)

安装脚本会自动配置所有服务。当看到提示“请登录 IP:8000 正常使用”时，表示安装成功。

---

## 访问与登录

安装完成后，我们就可以通过浏览器访问 Jumpserver 的 Web 管理界面了。

![](img/7612cb33bfaefb843cacd4b64c7cb958_46.png)

![](img/7612cb33bfaefb843cacd4b64c7cb958_48.png)

1.  打开浏览器，输入地址：`http://你的服务器IP:8000`
2.  使用上一步设置的管理员账号（如 `admin`）和密码（如 `123456`）登录。
3.  成功登录后，你将看到 Jumpserver 的管理面板，其中包含了用户管理、资产管理、权限管理和日志审计等核心功能模块。

![](img/7612cb33bfaefb843cacd4b64c7cb958_50.png)

---

## 总结

![](img/7612cb33bfaefb843cacd4b64c7cb958_52.png)

本节课中我们一起学习了堡垒机（跳板机）的核心概念及其在企业运维安全中的重要性。我们重点实践了开源堡垒机系统 Jumpserver 的完整安装流程，包括环境准备、依赖安装、交互式配置以及最终的登录验证。

![](img/7612cb33bfaefb843cacd4b64c7cb958_54.png)

通过本次学习，你已经成功搭建了一个具备基础功能的堡垒机环境，为后续学习资产管理和运维审计打下了坚实的基础。