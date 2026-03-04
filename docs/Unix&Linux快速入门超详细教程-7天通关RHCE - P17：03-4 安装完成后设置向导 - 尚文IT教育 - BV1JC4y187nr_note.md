# Unix&Linux快速入门超详细教程：P17：03-4 安装完成后设置向导 🚀

![](img/28dbb82072fd100925aea0ddea1ad433_1.png)

![](img/28dbb82072fd100925aea0ddea1ad433_3.png)

在本节课中，我们将学习完成红帽企业版Linux（RHEL）系统安装后的首次启动设置向导。这个过程包括接受许可协议、配置网络、管理订阅、创建用户以及进行基本的系统个性化设置。掌握这些步骤是成功部署和使用RHEL系统的关键。

---

## 重启与初始引导

系统安装完成后，首先需要重启。点击“Reboot”按钮。



重启后，系统会进入红帽操作系统的启动引导界面。如果5秒内不进行任何操作，系统将自动进入下一步。



![](img/28dbb82072fd100925aea0ddea1ad433_5.png)

![](img/28dbb82072fd100925aea0ddea1ad433_6.png)

## 首次启动向导设置

接下来，系统会引导您完成一系列初始配置。

### 许可协议

![](img/28dbb82072fd100925aea0ddea1ad433_8.png)

![](img/28dbb82072fd100925aea0ddea1ad433_10.png)

首先需要接受最终用户许可协议（EULA）。点击“Accept”按钮表示同意。

### 网络与主机名

网络配置在安装阶段已经完成。例如，IP地址可能已设置为 `10.60.100.2`。请根据您的实际环境进行配置。

同时，可以在此设置主机名。例如，设置为 `rhel7-6-1`。

![](img/28dbb82072fd100925aea0ddea1ad433_12.png)

![](img/28dbb82072fd100925aea0ddea1ad433_13.png)

![](img/28dbb82072fd100925aea0ddea1ad433_14.png)

### 订阅管理

![](img/28dbb82072fd100925aea0ddea1ad433_16.png)

红帽是一个商业化的Linux发行版，需要注册订阅才能获得官方支持和服务更新。这类似于购买正版产品后到官网注册。此步骤通常需要网络连接。

![](img/28dbb82072fd100925aea0ddea1ad433_18.png)

### 用户设置

![](img/28dbb82072fd100925aea0ddea1ad433_20.png)

在此可以创建新的系统用户。完成所有设置后，点击“Finish Configuration”。



![](img/28dbb82072fd100925aea0ddea1ad433_22.png)

## 系统个性化向导

![](img/28dbb82072fd100925aea0ddea1ad433_24.png)

完成基础配置后，会进入一个更详细的个性化设置向导。

### 欢迎与语言选择

![](img/28dbb82072fd100925aea0ddea1ad433_26.png)

![](img/28dbb82072fd100925aea0ddea1ad433_28.png)

![](img/28dbb82072fd100925aea0ddea1ad433_30.png)

“Welcome”向导用于选择界面语言，而非系统语言。我们保持默认的英文设置，点击“Next”。

![](img/28dbb82072fd100925aea0ddea1ad433_32.png)

### 键盘布局与隐私

![](img/28dbb82072fd100925aea0ddea1ad433_34.png)

选择适合的键盘布局。隐私设置（Privacy）询问是否启用定位服务，我们可以先取消勾选。

### 时区设置

![](img/28dbb82072fd100925aea0ddea1ad433_36.png)

时区（Time Zone）选择“Asia/Shanghai”。

![](img/28dbb82072fd100925aea0ddea1ad433_38.png)

### 在线账户

![](img/28dbb82072fd100925aea0ddea1ad433_40.png)

![](img/28dbb82072fd100925aea0ddea1ad433_41.png)

此步骤可选，用于关联您的在线账户（如Google、Microsoft账户）。我们选择“Skip”跳过。

![](img/28dbb82072fd100925aea0ddea1ad433_43.png)

![](img/28dbb82072fd100925aea0ddea1ad433_45.png)

### 创建本地用户

![](img/28dbb82072fd100925aea0ddea1ad433_47.png)

现在需要创建一个用于日常登录的本地用户。有两种方式：
*   **企业/域登录**：如果您的环境在域中，需输入完整域名（FQDN）和用户名，格式如 `test@upwen.com`。
*   **本地账户**：创建独立的本地账户。例如，创建用户 `t` 并为其设置密码。

设置完成后，点击“Start using Red Hat Enterprise Linux Server”。



系统将以新创建的用户（如 `test`）身份登录到图形化桌面。



## 切换到管理员（root）账户

为了进行系统级管理，我们需要使用管理员账户登录。

首先，从当前用户账户注销（Log Out）。在登录界面，点击“Not listed？”。

![](img/28dbb82072fd100925aea0ddea1ad433_49.png)

输入管理员用户名 `root`，然后输入在安装阶段为root账户设置的密码进行登录。



同样，完成root账户的个性化向导（可快速点击下一步跳过），即可进入root用户的桌面环境。



## 图形化界面初览

![](img/28dbb82072fd100925aea0ddea1ad433_51.png)

红帽的图形化界面与Windows类似，包含以下元素：
*   **桌面**：有回收站（Trash）和用户主目录文件夹。
*   **文件管理器**：通过“Computer”可以查看Linux文件系统结构，如 `/`, `/boot`, `/home`, `/usr` 等目录。我们之前自定义的 `/app` 分区也会显示在这里。
*   **应用程序菜单**：集成了各种工具，如Firefox浏览器、计算器、文本编辑器、防火墙配置、系统监控、日志查看器等。



![](img/28dbb82072fd100925aea0ddea1ad433_53.png)

## 验证网络与远程连接

![](img/28dbb82072fd100925aea0ddea1ad433_55.png)

上一节我们熟悉了图形界面，本节中我们来看看如何验证网络并建立远程管理连接。

![](img/28dbb82072fd100925aea0ddea1ad433_57.png)

![](img/28dbb82072fd100925aea0ddea1ad433_59.png)

点击桌面右上角的网络图标，确保网络处于“ON”状态。



![](img/28dbb82072fd100925aea0ddea1ad433_61.png)

![](img/28dbb82072fd100925aea0ddea1ad433_63.png)

为了从其他计算机（如您的物理机或办公室电脑）管理这台Linux服务器，需要使用终端工具通过SSH协议进行远程连接。

![](img/28dbb82072fd100925aea0ddea1ad433_65.png)

![](img/28dbb82072fd100925aea0ddea1ad433_67.png)

SSH服务使用 **TCP 22端口**。

![](img/28dbb82072fd100925aea0ddea1ad433_69.png)

常用的终端工具有SecureCRT、Xshell、FinalShell、PuTTY等。以下以SecureCRT为例：
1.  打开SecureCRT，点击“Quick Connect”。
2.  协议选择“SSH2”，主机名填写Linux服务器的IP地址（如 `10.60.100.2`），用户名填写 `root`。
3.  点击“Connect”，首次连接时会保存服务器的主机密钥，点击“Accept & Save”。
4.  输入root用户的密码，并可选“Save password”以便下次快速登录。

![](img/28dbb82072fd100925aea0ddea1ad433_71.png)

连接成功后，您将看到一个闪烁的光标，这就是命令行交互界面。按住 `Ctrl` 键并滚动鼠标滚轮可以调整终端字体大小。



![](img/28dbb82072fd100925aea0ddea1ad433_73.png)

![](img/28dbb82072fd100925aea0ddea1ad433_75.png)

## 虚拟机挂起问题与解决

![](img/28dbb82072fd100925aea0ddea1ad433_77.png)

在使用VMware虚拟机时，可能会遇到无法成功“挂起”（Suspend）虚拟机的问题。挂起功能可以保存当前系统状态，便于后续快速恢复工作。

![](img/28dbb82072fd100925aea0ddea1ad433_79.png)

问题表现为点击挂起后，提示“未能挂起虚拟机”，并且网络可能会断开。



以下是解决此问题的思路和步骤：

首先，检查Windows任务管理器中是否有残留的VMware相关进程（如`vmware-vmx.exe`）。如果有，结束这些进程。

如果问题依旧，可能与Linux系统的安全模块SELinux有关。我们需要临时禁用SELinux进行测试。

通过之前建立的SSH连接，在终端中执行以下命令：

1.  **检查当前SELinux状态**：
    ```bash
    getenforce
    ```
    如果返回 `Enforcing`，表示SELinux处于强制启用状态。

![](img/28dbb82072fd100925aea0ddea1ad433_81.png)

![](img/28dbb82072fd100925aea0ddea1ad433_82.png)

2.  **修改SELinux配置文件**（需root权限）：
    ```bash
    vi /etc/selinux/config
    ```
    找到 `SELINUX=` 这一行，将其值从 `enforcing` 改为 `disabled`。
    ```bash
    # 修改前
    SELINUX=enforcing
    # 修改后
    SELINUX=disabled
    ```
    保存并退出编辑器（在vi中按 `Esc`，输入 `:wq`，回车）。

3.  **临时设置SELinux为宽容模式**（无需立即重启）：
    ```bash
    setenforce 0
    ```
    再次运行 `getenforce` 命令，此时应返回 `Permissive`。

![](img/28dbb82072fd100925aea0ddea1ad433_84.png)

![](img/28dbb82072fd100925aea0ddea1ad433_86.png)

完成上述操作后，再次尝试在VMware中挂起虚拟机，通常可以成功。挂起后恢复虚拟机，检查网络连接是否正常。

![](img/28dbb82072fd100925aea0ddea1ad433_88.png)

![](img/28dbb82072fd100925aea0ddea1ad433_90.png)

> **注意**：将SELinux设置为 `disabled` 需要重启系统才能完全生效。`setenforce 0` 是临时生效的。在生产环境中，请谨慎修改SELinux策略，建议在充分了解其功能后再做调整。



![](img/28dbb82072fd100925aea0ddea1ad433_92.png)

## 总结

![](img/28dbb82072fd100925aea0ddea1ad433_94.png)

本节课中我们一起学习了红帽Linux系统安装后的完整设置流程。我们从重启系统开始，逐步完成了许可协议接受、网络确认、订阅管理、用户创建等初始配置。随后，我们登录系统，熟悉了基本的图形化界面，并学会了如何切换到root管理员账户。更重要的是，我们掌握了通过SSH协议使用终端工具（如SecureCRT）远程连接和管理Linux服务器的方法，这是后续学习和工作的主要方式。最后，我们还探讨并解决了VMware虚拟机可能遇到的挂起故障，涉及到了SELinux安全模块的临时禁用操作。这些步骤为您后续深入学习和使用Linux系统打下了坚实的基础。