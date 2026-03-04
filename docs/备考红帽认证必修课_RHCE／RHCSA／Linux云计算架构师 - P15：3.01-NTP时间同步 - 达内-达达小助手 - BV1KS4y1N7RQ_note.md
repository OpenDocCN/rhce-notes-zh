# 备考红帽认证必修课：P15：3.01-NTP时间同步 ⏰

![](img/c6e7bbd6cef51821f51be4fa239d2f2d_1.png)

在本节课中，我们将学习如何配置NTP时间客户端。这是RHCE/RHCSA考试中的一个常见任务，要求将您的系统配置为指定NTP服务器的客户端，以实现时间同步。

![](img/c6e7bbd6cef51821f51be4fa239d2f2d_3.png)

---

![](img/c6e7bbd6cef51821f51be4fa239d2f2d_5.png)

## 课程概述与练习环境

![](img/c6e7bbd6cef51821f51be4fa239d2f2d_7.png)

![](img/c6e7bbd6cef51821f51be4fa239d2f2d_9.png)

在练习环境中，您可以访问 `study.lab0.com/exam` 来查看练习题。其中，`EX200` 部分包含了上午考试的练习题目。

![](img/c6e7bbd6cef51821f51be4fa239d2f2d_11.png)

前几次课程中，我们讲解了红帽8操作系统的基本环境与基础命令。上一周，我们学习了软件仓库配置、系统调试、用户账号管理、计划任务和权限管理等内容。

本节我们将继续学习后续题目，重点是“配置NTP时间客户端”。

![](img/c6e7bbd6cef51821f51be4fa239d2f2d_13.png)

---

## 理解NTP时间同步

NTP（Network Time Protocol，网络时间协议）用于在计算机网络中同步系统时钟。提供标准时间的机器称为 **NTP服务器端**，使用该时间的机器称为 **NTP客户端**。时间同步的目标是让所有客户端与服务器的时间保持一致。

从红帽7开始，系统默认使用 `chrony` 软件包替代了旧的 `ntpd` 服务。`chrony` 对应的系统服务是 `chronyd`，它既可以作为NTP服务器，也可以作为客户端。考试要求我们配置的是 **NTP客户端**。

![](img/c6e7bbd6cef51821f51be4fa239d2f2d_15.png)

作为客户端，基本的配置流程分为三步：**安装软件包**、**修改配置文件**、**启动并启用服务**。

![](img/c6e7bbd6cef51821f51be4fa239d2f2d_17.png)

---

## 配置NTP客户端步骤

![](img/c6e7bbd6cef51821f51be4fa239d2f2d_19.png)

以下是配置NTP客户端的详细步骤。我们将以连接到服务器 `server1` 为例进行说明。

### 1. 确认或安装软件包

首先，确认 `chrony` 软件包是否已安装。该包在大多数情况下默认已安装。

```bash
yum -y install chrony
```

如果系统提示已安装，则可跳过此步。

![](img/c6e7bbd6cef51821f51be4fa239d2f2d_21.png)

![](img/c6e7bbd6cef51821f51be4fa239d2f2d_23.png)

### 2. 修改配置文件

NTP客户端的主要配置文件是 `/etc/chrony.conf`。我们需要修改其中的时间服务器地址。

![](img/c6e7bbd6cef51821f51be4fa239d2f2d_25.png)

使用文本编辑器（如 `vim`）打开该文件：

```bash
vim /etc/chrony.conf
```

在文件开头部分，您会看到类似以下的配置行（可能是 `pool` 或 `server` 开头）：

```bash
pool 2.rhel.pool.ntp.org iburst
```

![](img/c6e7bbd6cef51821f51be4fa239d2f2d_27.png)

**我们需要根据题目要求进行修改：**

![](img/c6e7bbd6cef51821f51be4fa239d2f2d_29.png)

*   **`pool`** 指令通常指向一个域名，该域名在DNS中解析为多个IP地址（即一个服务器池）。这适用于拥有多个冗余服务器的情况。
*   **`server`** 指令则指向一个特定的服务器（域名或IP）。当题目只指定单个服务器时，建议使用此指令。

**考试规范操作：**
将原有的 `pool` 行注释掉或删除，并添加一行 `server` 指令，指向题目要求的服务器（例如 `server1`），并保留 `iburst` 参数以加快初始同步。

![](img/c6e7bbd6cef51821f51be4fa239d2f2d_31.png)

修改后的配置行应类似：

![](img/c6e7bbd6cef51821f51be4fa239d2f2d_33.png)

```bash
#pool 2.rhel.pool.ntp.org iburst
server server1 iburst
```

**参数说明：**
*   `server1`：替换为题目中给出的NTP服务器地址。
*   `iburst`：此参数使客户端在初始同步时快速发送多个数据包，以便更快地与服务器建立连接并同步时间。

修改完成后，保存并退出编辑器。

![](img/c6e7bbd6cef51821f51be4fa239d2f2d_35.png)

### 3. 启动并启用服务

配置文件修改后，需要重启 `chronyd` 服务以使更改生效，并设置其开机自动启动。

```bash
systemctl restart chronyd
systemctl enable chronyd
```

---

## 验证配置结果

配置完成后，有几种方法可以验证NTP客户端是否工作正常。

### 方法一：使用 `timedatectl` 命令

`timedatectl` 命令可以查看系统时间和日期相关的状态。

```bash
timedatectl
```

![](img/c6e7bbd6cef51821f51be4fa239d2f2d_37.png)

关注输出中的以下两行：
*   `NTP service: active` 表示NTP服务正在运行。
*   `NTP synchronized: yes` 表示系统时间已成功与NTP服务器同步。如果刚配置完，可能需要稍等片刻或重启服务后才会显示 `yes`。

### 方法二：使用 `chronyc` 命令追踪源状态

`chronyc` 是 `chrony` 的命令行控制工具，可以更详细地检查时间源状态。

```bash
chronyc sources -v
```

![](img/c6e7bbd6cef51821f51be4fa239d2f2d_39.png)

此命令会列出当前配置的所有时间源及其状态。请关注以下几点：

1.  在源列表中，应能看到您配置的服务器（如 `server1`）。
2.  查看最左侧的状态标识符：
    *   `^*`：这是理想状态。`^` 表示此源被选为当前同步的参考源，`*` 表示该源已被组合算法接受，正在被使用。
    *   `^?`：表示已配置此源，但暂时无法联系上（可能是网络、DNS或服务器问题）。
    *   如果列表为空或没有您的服务器，说明配置未生效。

![](img/c6e7bbd6cef51821f51be4fa239d2f2d_41.png)

### 方法三：手动修改时间并观察同步（可选）

![](img/c6e7bbd6cef51821f51be4fa239d2f2d_43.png)

为了快速验证同步功能，可以手动将系统时间设错，然后观察是否会被自动纠正。

![](img/c6e7bbd6cef51821f51be4fa239d2f2d_45.png)

1.  设置一个错误的时间：
    ```bash
    date -s “2002-02-02 10:00:00”
    ```
2.  重启 `chronyd` 服务或等待一段时间（通常几分钟内）：
    ```bash
    systemctl restart chronyd
    ```
3.  再次查看当前时间：
    ```bash
    date
    ```
    如果时间恢复到了正确（接近NTP服务器）的时间，说明同步成功。

![](img/c6e7bbd6cef51821f51be4fa239d2f2d_47.png)

> **考试提示**：在考试中，最快捷可靠的验证方法是使用 **`chronyc sources`**。只要能看到配置的服务器地址且状态为 `^*`，即可判定配置正确。

---

## 补充知识：硬件时钟同步

![](img/c6e7bbd6cef51821f51be4fa239d2f2d_49.png)

![](img/c6e7bbd6cef51821f51be4fa239d2f2d_51.png)

系统时间同步后，有时需要将正确的时间写入硬件时钟（BIOS时间）。可以使用 `hwclock` 命令。

![](img/c6e7bbd6cef51821f51be4fa239d2f2d_53.png)

*   将系统时间同步到硬件时钟：
    ```bash
    hwclock -w
    ```
*   根据硬件时钟来设置系统时间：
    ```bash
    hwclock -s
    ```

![](img/c6e7bbd6cef51821f51be4fa239d2f2d_55.png)

此项操作在考试中通常不要求，但在实际工作中可能用到。

![](img/c6e7bbd6cef51821f51be4fa239d2f2d_57.png)

![](img/c6e7bbd6cef51821f51be4fa239d2f2d_59.png)

---

![](img/c6e7bbd6cef51821f51be4fa239d2f2d_61.png)

## 配置文件深入说明（可选）

如果您想深入了解 `chrony.conf` 配置文件中的其他参数（如轮询间隔 `minpoll`/`maxpoll`），可以查看其手册页：

```bash
man chrony.conf
```

![](img/c6e7bbd6cef51821f51be4fa239d2f2d_63.png)

手册中详细解释了 `server`、`pool`、`iburst` 等所有配置指令的用途。

---

## 本节总结

本节课我们一起学习了如何配置Linux系统作为NTP时间客户端。核心步骤可总结为：

![](img/c6e7bbd6cef51821f51be4fa239d2f2d_65.png)

1.  **安装**：确保 `chrony` 软件包已安装。
2.  **配置**：编辑 `/etc/chrony.conf` 文件，使用 `server` 指令指定题目要求的NTP服务器地址，并保留 `iburst` 参数。
3.  **启用**：重启并启用 `chronyd` 服务。
4.  **验证**：使用 `chronyc sources` 命令检查时间源状态，确认服务器地址正确且状态为 `^*`。

![](img/c6e7bbd6cef51821f51be4fa239d2f2d_67.png)

掌握这个流程，您就能轻松应对红帽认证考试中关于NTP客户端配置的题目。现在，请在练习环境中完成相关题目以巩固所学知识。