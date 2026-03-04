# Linux零基础入门与红帽认证：RH124课程 - P2：00.2 环境介绍与设置 🖥️

![](img/15fa27900448c616599ecbad711983cb_1.png)

## 概述
在本节课中，我们将学习RH124课程所使用的实验环境架构、各虚拟机的作用，并完成环境的初始化和网络配置，为后续的动手实验做好准备。

## 环境架构介绍
上一节我们了解了课程背景，本节中我们来看看实验环境的组成。标准的RHCE教学环境通常部署在物理PC上，包含讲师端和学生端。讲师端主机名为 `foundation0`，学生端主机名则为 `foundationX`（X为1到20）。这种架构称为ILT（讲师引导式培训）。

![](img/15fa27900448c616599ecbad711983cb_3.png)

![](img/15fa27900448c616599ecbad711983cb_5.png)

随着线上教学的发展，我们将这套环境封装成了VMware虚拟机镜像 `labRHSC9-V9.0`。学员在自己的电脑（Windows或macOS）上使用VMware Workstation加载此镜像，即可获得完整的实验环境。

![](img/15fa27900448c616599ecbad711983cb_7.png)

加载该镜像后，启动的虚拟机就是讲师端 `foundation0`。我们可以通过终端验证主机名：
```bash
hostname
```
输出应为 `foundation0.lab.example.com`。

在 `foundation0` 上，运行着多台用于不同角色的虚拟机。我们需要通过虚拟机管理软件来查看和管理它们。

## 虚拟机角色详解
以下是环境中各虚拟机的作用介绍：

*   **classroom**：核心服务器。提供两个重要站点：
    *   `http://content.example.com`：提供系统光盘和软件包。
    *   `http://materials.example.com`：提供实验所需的文件。此虚拟机必须保持运行，并会随 `foundation0` 开机自动启动。
*   **bastion**：关键网络节点。充当学生网络（172.25.250.0/24）与教室网络（172.25.252.0/24）之间的路由器和DNS服务器。学生端虚拟机需通过它下载启动脚本。
*   **workstation, servera, serverb**：学生实验用机。**所有实验操作都在这三台虚拟机中执行**，不在 `foundation0`、`classroom` 或 `bastion` 上操作。
*   **utility**：辅助服务器。在RH124课程中暂时用不到，在后续的RH134课程中会用作容器镜像仓库（Registry）。

![](img/15fa27900448c616599ecbad711983cb_9.png)

![](img/15fa27900448c616599ecbad711983cb_11.png)

![](img/15fa27900448c616599ecbad711983cb_13.png)

![](img/15fa27900448c616599ecbad711983cb_14.png)

![](img/15fa27900448c616599ecbad711983cb_15.png)

![](img/15fa27900448c616599ecbad711983cb_17.png)

**核心关系**：`workstation`、`servera`、`serverb` 处于学生网络（172.25.250.0/24），其流量通过 `bastion` 路由至 `classroom`（172.25.252.0/24）。`foundation0` 可通过额外配置连通外部互联网。

![](img/15fa27900448c616599ecbad711983cb_19.png)

![](img/15fa27900448c616599ecbad711983cb_21.png)

## 环境初始化与启动
了解了架构后，我们需要初始化并启动实验环境。请按顺序执行以下步骤：

![](img/15fa27900448c616599ecbad711983cb_23.png)

1.  **解锁桌面**：若 `foundation0` 桌面锁屏，用户名为 `kiosk`，密码为 `redhat`。
2.  **打开终端**：点击左上角“Activities”，在仪表板中找到并打开“Terminal”。
3.  **打开虚拟机管理器**：同样在“Activities”仪表板中，找到并打开“Virtual Machine Manager”，可右键窗口选择“Always on Top”使其置顶显示。

![](img/15fa27900448c616599ecbad711983cb_25.png)

![](img/15fa27900448c616599ecbad711983cb_27.png)

![](img/15fa27900448c616599ecbad711983cb_29.png)

以下是初始化环境的命令序列：

![](img/15fa27900448c616599ecbad711983cb_31.png)

![](img/15fa27900448c616599ecbad711983cb_32.png)

![](img/15fa27900448c616599ecbad711983cb_34.png)

*   **设置课程**：此命令配置课程环境。
    ```bash
    sudo rht-setcourse rh199
    ```
*   **完全重置并启动classroom**：此命令会重置并启动 `classroom` 虚拟机。
    ```bash
    rht-vmctl fullreset classroom --yes
    ```
    执行后，可在虚拟机管理器中看到 `classroom` 状态变为 `running`。可双击打开其控制台确认启动完成。
*   **获取所有虚拟机**：此命令会下载所有需要的虚拟机镜像。
    ```bash
    rht-vmctl get all
    ```
    执行后，虚拟机管理器中将出现所有虚拟机（状态为 `shut off`）。
*   **验证环境配置**：使用以下命令检查课程和虚拟机列表是否配置正确。
    ```bash
    cat /etc/rht
    ```
    输出应显示课程为 `rh199`，并列出 `bastion`、`workstation`、`utility`、`servera`、`serverb` 等虚拟机。
*   **启动所有学生实验机**：此命令启动所有用于实验的虚拟机。
    ```bash
    rht-vmctl start all
    ```
    启动后，建议双击 `bastion` 的控制台确认其正常启动。也可用 `ping` 命令测试连通性：
    ```bash
    ping -c 3 bastion
    ```

![](img/15fa27900448c616599ecbad711983cb_36.png)

![](img/15fa27900448c616599ecbad711983cb_38.png)

![](img/15fa27900448c616599ecbad711983cb_39.png)

## 登录实验机
环境启动后，我们需要登录到实验机进行操作。

![](img/15fa27900448c616599ecbad711983cb_41.png)

![](img/15fa27900448c616599ecbad711983cb_43.png)

![](img/15fa27900448c616599ecbad711983cb_45.png)

![](img/15fa27900448c616599ecbad711983cb_46.png)

*   **从foundation0远程登录**：这是推荐的高效方式。
    ```bash
    ssh student@workstation
    ```
    密码为 `student`。登录成功后，可进一步测试连接到 `servera`：
    ```bash
    ssh servera
    ```
*   **通过图形界面登录（不推荐）**：在虚拟机管理器中双击 `workstation` 图标，使用 `student` 用户和密码登录。此方式消耗资源较多，仅在前期熟悉时使用。

![](img/15fa27900448c616599ecbad711983cb_48.png)

![](img/15fa27900448c616599ecbad711983cb_50.png)

![](img/15fa27900448c616599ecbad711983cb_52.png)

**重要账户信息**：
*   学生实验机（workstation, servera, serverb）的默认用户是 `student`，密码为 `student`。
*   管理员用户是 `root`，密码为 `redhat`。
*   还有一个用户 `devops`，密码也是 `redhat`。

![](img/15fa27900448c616599ecbad711983cb_54.png)

![](img/15fa27900448c616599ecbad711983cb_55.png)

![](img/15fa27900448c616599ecbad711983cb_57.png)

## 配置互联网访问
默认环境下，虚拟机无法访问外部互联网。我们需要在 `foundation0` 上启用特定网卡来打通连接。

![](img/15fa27900448c616599ecbad711983cb_59.png)

![](img/15fa27900448c616599ecbad711983cb_60.png)

![](img/15fa27900448c616599ecbad711983cb_62.png)

1.  **识别可用网卡**：首先找出未用于内部桥接的网卡。
    ```bash
    ip link ls | grep ens
    ```
    假设输出有 `ens160` 和 `ens224`。
2.  **确定桥接网卡**：查看哪个网卡已用于内部桥接（不能用于外网）。
    ```bash
    bridge link show br0
    ```
    如果输出中包含 `ens160`，则说明 `ens160` 已桥接，那么外网网卡就是另一个（例如 `ens224`）。
3.  **配置外部网络**：使用识别出的外网网卡名进行配置。
    ```bash
    rht-external --config ens224
    ```
    等待命令执行完成，出现分配IP等成功消息。
4.  **测试网络连通性**：在 `foundation0` 或任何实验机（如 `workstation`）上测试。
    ```bash
    ping -c 3 www.redhat.com
    ```
    也可以打开 `foundation0` 上的 Firefox 浏览器访问红帽官网进行验证。

![](img/15fa27900448c616599ecbad711983cb_64.png)

![](img/15fa27900448c616599ecbad711983cb_66.png)

![](img/15fa27900448c616599ecbad711983cb_68.png)

**环境备份建议**：完成以上设置后，建议关闭 `foundation0`，并在 VMware Workstation 中为整个 `labRHSC9-V9.0` 虚拟机创建一个快照（例如“初始环境设置完成”），以便后续随时还原。

![](img/15fa27900448c616599ecbad711983cb_70.png)

![](img/15fa27900448c616599ecbad711983cb_71.png)

![](img/15fa27900448c616599ecbad711983cb_73.png)

![](img/15fa27900448c616599ecbad711983cb_74.png)

![](img/15fa27900448c616599ecbad711983cb_76.png)

## 故障恢复指南
实验过程中如果虚拟机出现问题，可以按以下方法重置：

*   **重置单个虚拟机**（如 `servera`）：
    ```bash
    rht-vmctl reset servera
    ```
*   **若重置无效，完全重置单个虚拟机**（需确保 `classroom` 正常运行）：
    ```bash
    rht-vmctl fullreset servera
    ```
*   **傻瓜式重置所有学生实验机**：
    ```bash
    rht-vmctl reset all --yes
    ```
    或完全重置：
    ```bash
    rht-vmctl fullreset all
    ```
    （注意：`all` 不包括 `classroom`，重置 `classroom` 需单独指定）
*   **如果 `foundation0` 本身出错**：建议还原之前创建的 VMware 虚拟机快照。

![](img/15fa27900448c616599ecbad711983cb_78.png)

## 总结
本节课中我们一起学习了RH124课程实验环境的整体架构，明确了 `classroom`、`bastion`、`workstation`、`servera`、`serverb` 等虚拟机的核心作用。我们逐步完成了环境的初始化、启动、登录验证以及互联网访问的配置。最后，掌握了在实验过程中遇到问题时的重置和恢复方法。现在，你的实验环境已经准备就绪，可以开始后续的Linux学习之旅了。