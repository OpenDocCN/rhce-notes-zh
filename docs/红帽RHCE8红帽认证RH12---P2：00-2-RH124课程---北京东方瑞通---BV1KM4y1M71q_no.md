# 红帽RHCE8认证课程：RH124：实验环境虚拟机控制指南 🖥️

![](img/46b5629fd58abce2f26d4b53d6726abc_1.png)

![](img/46b5629fd58abce2f26d4b53d6726abc_2.png)

在本节课中，我们将学习如何管理和控制红帽RH124课程提供的实验环境虚拟机。我们将重点介绍一个核心命令行工具，并了解虚拟机的启动、查看、关闭和重置等基本操作。

![](img/46b5629fd58abce2f26d4b53d6726abc_4.png)

![](img/46b5629fd58abce2f26d4b53d6726abc_6.png)

## 概述

![](img/46b5629fd58abce2f26d4b53d6726abc_8.png)

![](img/46b5629fd58abce2f26d4b53d6726abc_10.png)

![](img/46b5629fd58abce2f26d4b53d6726abc_11.png)

实验环境由一台主虚拟机（RHCE8）和其内部嵌套的5台虚拟机组成。这些嵌套虚拟机包括：`classroom`、`bastion`、`workstation`、`servera`和`serverb`。我们将使用特定的工具来管理这些虚拟机。

## 实验环境登录与设置

![](img/46b5629fd58abce2f26d4b53d6726abc_13.png)

![](img/46b5629fd58abce2f26d4b53d6726abc_15.png)

![](img/46b5629fd58abce2f26d4b53d6726abc_16.png)

首先，需要启动并登录主虚拟机。

![](img/46b5629fd58abce2f26d4b53d6726abc_18.png)

1.  打开提供的虚拟机文件（例如位于E盘的`redhat认证HC8L201908`目录）。
2.  使用密码 `redhat` 解锁虚拟机。
3.  启动虚拟机，使用 `kiosk` 账户登录，密码同样是 `redhat`。
4.  登录后，在设置界面（小齿轮图标）中选择 **“经典的微烂模式”** 以获得更熟悉的操作界面。
5.  根据个人显示器调整分辨率至合适大小（例如1920x1080），并选择“自由拉伸”以消除黑边。

![](img/46b5629fd58abce2f26d4b53d6726abc_20.png)

完成上述步骤后，实验环境的主虚拟机便准备就绪。

![](img/46b5629fd58abce2f26d4b53d6726abc_22.png)

![](img/46b5629fd58abce2f26d4b53d6726abc_24.png)

## 虚拟机架构与控制工具

实验环境中的虚拟机采用嵌套虚拟化架构。主虚拟机（RHCE8）内部运行着5台子虚拟机。

为了管理这些嵌套的虚拟机，红帽提供了一个专用的命令行工具：**`rht-vmctl`**。

该命令的结构如下：
```
rht-vmctl <VMCMD> <VMNAME>
```
其中：
*   `<VMCMD>` 代表要执行的操作命令，例如 `status`（查看状态）、`start`（启动）。
*   `<VMNAME>` 代表目标虚拟机的名称，例如 `servera`、`all`。

## 核心虚拟机管理操作

![](img/46b5629fd58abce2f26d4b53d6726abc_26.png)

以下是使用 `rht-vmctl` 进行日常管理的关键操作。

![](img/46b5629fd58abce2f26d4b53d6726abc_28.png)

### 查看虚拟机状态

![](img/46b5629fd58abce2f26d4b53d6726abc_30.png)

使用 `status` 命令可以查看指定虚拟机的当前状态。

**命令示例：**
```bash
rht-vmctl status servera
rht-vmctl status all
```
*   首次查看时，状态可能显示为 `missing`（未定义）或 `defined`（已定义但未运行），这属于正常现象。
*   使用 `all` 参数可以一次性查看 `workstation`、`servera`、`serverb` 和 `bastion` 四台虚拟机的状态。`classroom` 虚拟机需要单独查看。

### 启动虚拟机

启动虚拟机有严格的顺序要求，必须首先启动 `classroom`，然后再启动其他虚拟机。

**启动顺序命令：**
```bash
rht-vmctl start classroom
rht-vmctl start all
```
首次启动时，虚拟机会从 `classroom` 服务器下载所需的磁盘镜像文件，因此需要一些时间。下载完成后，后续启动将不再需要此步骤。

可以使用 `ping` 命令测试虚拟机是否成功启动并具备网络连接。
```bash
ping classroom
ping servera
```

### 查看虚拟机控制台

如果想查看某台虚拟机的图形化控制台界面，可以使用 `view` 命令。

**命令示例：**
```bash
rht-vmctl view servera
```
此操作会打开一个独立窗口显示 `servera` 的桌面环境。关闭此窗口**不会**关闭虚拟机，虚拟机仍在后台运行。

![](img/46b5629fd58abce2f26d4b53d6726abc_32.png)

### 停止与关闭虚拟机

![](img/46b5629fd58abce2f26d4b53d6726abc_34.png)

![](img/46b5629fd58abce2f26d4b53d6726abc_35.png)

有多种方式可以停止虚拟机。

![](img/46b5629fd58abce2f26d4b53d6726abc_37.png)

![](img/46b5629fd58abce2f26d4b53d6726abc_38.png)

**正常关机：**
使用 `stop` 命令，相当于向操作系统发送关机指令。
```bash
rht-vmctl stop servera
```

![](img/46b5629fd58abce2f26d4b53d6726abc_40.png)

**强制断电：**
使用 `poweroff` 命令，相当于直接切断虚拟机电源。
```bash
rht-vmctl poweroff servera
```
默认情况下，该命令会进行交互式确认。可以添加 `-y` 或 `-q` 参数来跳过确认，直接执行。
```bash
rht-vmctl poweroff -y servera
```

**交互式选择关机：**
使用 `-i` 参数可以对多台虚拟机进行交互式选择关机。
```bash
rht-vmctl poweroff -i all
```

![](img/46b5629fd58abce2f26d4b53d6726abc_42.png)

![](img/46b5629fd58abce2f26d4b53d6726abc_44.png)

### 重置虚拟机

![](img/46b5629fd58abce2f26d4b53d6726abc_46.png)

在实验过程中，如果需要将虚拟机恢复至初始干净状态，可以使用 `reset` 命令。

**命令示例：**
```bash
rht-vmctl reset servera
```
此操作会关闭 `servera` 并将其磁盘状态回滚到初始快照点，非常适合在每次实验开始前确保环境一致。

如果 `reset` 无效（例如因之前下载镜像中断导致虚拟机文件损坏），可以使用 `fullreset` 命令。该命令会从服务器重新下载完整的虚拟机镜像文件。
```bash
rht-vmctl fullreset -y servera
```

![](img/46b5629fd58abce2f26d4b53d6726abc_48.png)

## 使用技巧与图形化工具

### 命令行补全技巧

在输入 `rht-vmctl` 命令时，可以善用 `Tab` 键进行自动补全，提高效率并避免拼写错误。
*   输入 `rht-` 后按 `Tab` 键，可补全为 `rht-vmctl`。
*   输入 `start ` 后按两次 `Tab` 键，会列出所有可用的虚拟机名称供选择。

### 图形化管理工具（可选）

除了命令行，实验环境也提供了图形化的虚拟机管理工具 `virt-manager`。通过它，可以以图形界面方式查看、启动、关闭虚拟机。然而，**强烈建议管理员使用 `rht-vmctl` 命令行工具**，这是更标准、更高效的系统管理方式。

## 总结

本节课我们一起学习了红帽RH124实验环境中虚拟机的核心管理方法。

我们掌握了使用 **`rht-vmctl`** 这一关键工具来执行以下操作：
*   **查看状态** (`status`)
*   **按顺序启动** (`start classroom` -> `start all`)
*   **访问控制台** (`view`)
*   **停止/关闭** (`stop`, `poweroff`)
*   **重置环境** (`reset`, `fullreset`)

![](img/46b5629fd58abce2f26d4b53d6726abc_50.png)

记住启动顺序和 `reset` 命令对于顺利进行后续实验至关重要。通过熟练运用这些命令，你将能够有效控制实验环境，为学习RH124课程内容打下坚实基础。