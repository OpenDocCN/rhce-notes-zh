# Linux培训第34期：第17章：iSCSI网络存储服务 🖥️

![](img/5cf49904607924b188e3698cc975c73f_1.png)

![](img/5cf49904607924b188e3698cc975c73f_3.png)

![](img/5cf49904607924b188e3698cc975c73f_5.png)

![](img/5cf49904607924b188e3698cc975c73f_7.png)

在本节课中，我们将学习如何配置和使用iSCSI（Internet Small Computer System Interface）服务。与之前学习的文件共享服务不同，iSCSI允许我们将一个完整的硬盘设备通过网络共享给其他主机，实现跨平台的远程存储挂载。我们将从搭建本地的RAID磁盘阵列组开始，然后通过iSCSI协议将其共享出去，最后分别在Linux和Windows客户端上进行挂载和使用。

![](img/5cf49904607924b188e3698cc975c73f_9.png)

![](img/5cf49904607924b188e3698cc975c73f_11.png)

## 实验环境准备

![](img/5cf49904607924b188e3698cc975c73f_13.png)

![](img/5cf49904607924b188e3698cc975c73f_15.png)

为了确保数据安全，我们首先需要还原虚拟机环境，并准备实验所需的硬盘。

![](img/5cf49904607924b188e3698cc975c73f_17.png)

![](img/5cf49904607924b188e3698cc975c73f_19.png)

上一节我们介绍了实验的整体目标，本节中我们来看看具体的环境搭建步骤。

![](img/5cf49904607924b188e3698cc975c73f_21.png)

![](img/5cf49904607924b188e3698cc975c73f_23.png)

以下是环境准备的具体操作：

![](img/5cf49904607924b188e3698cc975c73f_25.png)

![](img/5cf49904607924b188e3698cc975c73f_27.png)

![](img/5cf49904607924b188e3698cc975c73f_29.png)

![](img/5cf49904607924b188e3698cc975c73f_31.png)

1.  关闭当前虚拟机。
2.  为虚拟机添加四块新的硬盘，每块大小为5GB。我们将用这四块硬盘创建一个RAID 10阵列，实际可用空间为10GB（因为RAID 10的存储效率为50%）。
3.  启动虚拟机，并进入BIOS设置，将SATA硬盘的启动优先级调整到最高，以确保系统能从正确的硬盘启动。

![](img/5cf49904607924b188e3698cc975c73f_33.png)

![](img/5cf49904607924b188e3698cc975c73f_35.png)

![](img/5cf49904607924b188e3698cc975c73f_37.png)

![](img/5cf49904607924b188e3698cc975c73f_39.png)

环境准备完成后，我们就可以开始创建RAID阵列了。

![](img/5cf49904607924b188e3698cc975c73f_41.png)

![](img/5cf49904607924b188e3698cc975c73f_43.png)

![](img/5cf49904607924b188e3698cc975c73f_45.png)

## 创建RAID 10磁盘阵列

![](img/5cf49904607924b188e3698cc975c73f_47.png)

![](img/5cf49904607924b188e3698cc975c73f_49.png)

![](img/5cf49904607924b188e3698cc975c73f_51.png)

![](img/5cf49904607924b188e3698cc975c73f_53.png)

由于在第七章中我们已经详细讲解过RAID的创建，这里我们将快速回顾关键命令。

![](img/5cf49904607924b188e3698cc975c73f_55.png)

![](img/5cf49904607924b188e3698cc975c73f_57.png)

首先，使用 `mdadm` 命令创建名为 `/dev/md0` 的RAID 10设备。

![](img/5cf49904607924b188e3698cc975c73f_59.png)

```bash
mdadm -Cv /dev/md0 -n 4 -l 10 /dev/sd[b-e]
```

![](img/5cf49904607924b188e3698cc975c73f_61.png)

![](img/5cf49904607924b188e3698cc975c73f_63.png)

![](img/5cf49904607924b188e3698cc975c73f_65.png)

**命令解析：**
*   `-Cv`：表示创建（Create）并显示详细过程（Verbose）。
*   `/dev/md0`：指定新RAID设备的名称。
*   `-n 4`：指定使用4块硬盘。
*   `-l 10`：指定RAID级别为10。
*   `/dev/sd[b-e]`：指定用于创建RAID的硬盘设备，这里使用了通配符匹配sdb、sdc、sdd、sde四块硬盘。

创建完成后，系统会开始同步阵列。在同步过程中，我们可以先安装配置iSCSI服务所需的工具。

![](img/5cf49904607924b188e3698cc975c73f_67.png)

## 安装与配置iSCSI服务端

![](img/5cf49904607924b188e3698cc975c73f_69.png)

在RHEL 8中，配置iSCSI服务端变得非常简单，这得益于 `targetcli` 这个交互式配置工具。它通过一个虚拟的文件系统目录结构来管理配置参数，我们只需将正确的参数放入对应的“目录”即可。

![](img/5cf49904607924b188e3698cc975c73f_71.png)

![](img/5cf49904607924b188e3698cc975c73f_73.png)

首先，安装 `targetcli` 工具包。

![](img/5cf49904607924b188e3698cc975c73f_75.png)

![](img/5cf49904607924b188e3698cc975c73f_77.png)

```bash
dnf install -y targetcli
```

![](img/5cf49904607924b188e3698cc975c73f_79.png)

![](img/5cf49904607924b188e3698cc975c73f_81.png)

![](img/5cf49904607924b188e3698cc975c73f_83.png)

![](img/5cf49904607924b188e3698cc975c73f_85.png)

安装完成后，运行 `targetcli` 命令进入交互式配置界面。

![](img/5cf49904607924b188e3698cc975c73f_87.png)

![](img/5cf49904607924b188e3698cc975c73f_89.png)

```bash
targetcli
```

你会看到一个以 `/` 开头的提示符，输入 `help` 可以查看可用命令。

### 配置步骤详解

![](img/5cf49904607924b188e3698cc975c73f_91.png)

![](img/5cf49904607924b188e3698cc975c73f_93.png)

![](img/5cf49904607924b188e3698cc975c73f_95.png)

整个服务端的配置逻辑可以概括为：定义共享的设备 -> 创建访问入口（IQN名称） -> 设置访问控制 -> 将设备与入口绑定 -> 指定服务监听地址。

![](img/5cf49904607924b188e3698cc975c73f_97.png)

![](img/5cf49904607924b188e3698cc975c73f_99.png)

![](img/5cf49904607924b188e3698cc975c73f_101.png)

以下是具体的配置流程：

1.  **定义共享的存储设备**：进入 `/backstores/block` 目录，为我们创建的RAID设备（`/dev/md0`）创建一个别名（例如 `disk0`）。这样客户端看到的是这个别名。
    ```bash
    cd /backstores/block
    create disk0 /dev/md0
    ```

2.  **创建iSCSI访问入口**：返回到根目录，进入 `/iscsi` 目录。在这里创建一个iSCSI Qualified Name（IQN），这是客户端访问时使用的唯一标识符。
    ```bash
    cd /iscsi
    create
    ```
    系统会自动生成一个IQN名称（例如 `iqn.2003-01.org.linux-iscsi.localhost.x8664:sn.4c2c6c0f2a3d`）。**请注意**：复制这个名称时，要仔细区分，只有最后一个点号（.）不是名称的一部分，需要去掉。

![](img/5cf49904607924b188e3698cc975c73f_103.png)

3.  **设置访问控制列表（ACL）**：进入刚创建的IQN目录下的 `tpg1/acls` 目录。我们需要创建一个条目，其名称将作为客户端的“密码”。为了方便记忆，建议使用IQN名称加上一个后缀（例如 `-client`）。
    ```bash
    cd iqn.2003-01.org.linux-iscsi.localhost.x8664:sn.4c2c6c0f2a3d/tpg1/acls
    create iqn.2003-01.org.linux-iscsi.localhost.x8664:sn.4c2c6c0f2a3d-client
    ```
    将此名称记录下来，后续客户端配置需要用到。

4.  **绑定存储设备到访问入口**：退回到 `tpg1` 目录，进入 `luns` 子目录。在这里创建LUN（逻辑单元号），将之前定义的存储设备（`disk0`）与这个iSCSI入口绑定。
    ```bash
    cd ../luns
    create /backstores/block/disk0
    ```
    可以使用Tab键自动补全路径。

![](img/5cf49904607924b188e3698cc975c73f_105.png)

5.  **指定服务监听地址**：进入 `tpg1/portals` 目录。默认会拒绝所有IP。我们需要删除默认设置，然后添加服务器提供服务的IP地址和端口（默认为3260）。
    ```bash
    cd ../portals
    delete 0.0.0.0 3260
    create 192.168.10.10
    ```

![](img/5cf49904607924b188e3698cc975c73f_107.png)

6.  **保存并退出**：完成以上配置后，输入 `exit` 退出 `targetcli`。工具会自动保存配置。
    ```bash
    exit
    ```

![](img/5cf49904607924b188e3698cc975c73f_109.png)

7.  **启动并设置防火墙**：启动 `target` 服务，并放行防火墙的相应端口（服务名为 `iscsi-target`）。
    ```bash
    systemctl restart target
    systemctl enable target
    firewall-cmd --permanent --add-service=iscsi-target
    firewall-cmd --reload
    ```

![](img/5cf49904607924b188e3698cc975c73f_111.png)

至此，iSCSI服务端配置完成。

![](img/5cf49904607924b188e3698cc975c73f_113.png)

![](img/5cf49904607924b188e3698cc975c73f_115.png)

![](img/5cf49904607924b188e3698cc975c73f_117.png)

![](img/5cf49904607924b188e3698cc975c73f_119.png)

## Linux客户端挂载iSCSI存储

![](img/5cf49904607924b188e3698cc975c73f_121.png)

![](img/5cf49904607924b188e3698cc975c73f_123.png)

![](img/5cf49904607924b188e3698cc975c73f_125.png)

![](img/5cf49904607924b188e3698cc975c73f_127.png)

![](img/5cf49904607924b188e3698cc975c73f_129.png)

![](img/5cf49904607924b188e3698cc975c73f_131.png)

现在，我们使用另一台Linux虚拟机作为客户端，来挂载刚才共享的网络存储。

首先，确保客户端与服务端网络互通（例如，ping 192.168.10.10）。

![](img/5cf49904607924b188e3698cc975c73f_133.png)

![](img/5cf49904607924b188e3698cc975c73f_135.png)

![](img/5cf49904607924b188e3698cc975c73f_137.png)

### 配置客户端

![](img/5cf49904607924b188e3698cc975c73f_139.png)

![](img/5cf49904607924b188e3698cc975c73f_141.png)

![](img/5cf49904607924b188e3698cc975c73f_143.png)

iSCSI客户端通过发起端名称（Initiator Name）进行认证，这个名称必须与服务端ACL中设置的名称完全一致。

1.  **修改发起端名称**：编辑 `/etc/iscsi/initiatorname.iscsi` 文件，将内容改为服务端ACL中设置的那个长名称。
    ```bash
    vim /etc/iscsi/initiatorname.iscsi
    ```
    将文件内容修改为：
    ```
    InitiatorName=iqn.2003-01.org.linux-iscsi.localhost.x8664:sn.4c2c6c0f2a3d-client
    ```

![](img/5cf49904607924b188e3698cc975c73f_145.png)

![](img/5cf49904607924b188e3698cc975c73f_147.png)

2.  **重启客户端服务**：
    ```bash
    systemctl restart iscsid
    systemctl enable iscsid
    ```

![](img/5cf49904607924b188e3698cc975c73f_149.png)

### 发现与登录存储

![](img/5cf49904607924b188e3698cc975c73f_151.png)

![](img/5cf49904607924b188e3698cc975c73f_153.png)

配置好名称后，需要两步来挂载存储：发现（Discover）和登录（Login）。

![](img/5cf49904607924b188e3698cc975c73f_155.png)

1.  **发现目标存储**：使用 `iscsiadm` 命令探测服务端有哪些可用的共享。
    ```bash
    iscsiadm -m discovery -t st -p 192.168.10.10
    ```
    此命令会返回服务端共享的IQN名称。

![](img/5cf49904607924b188e3698cc975c73f_157.png)

![](img/5cf49904607924b188e3698cc975c73f_159.png)

2.  **登录并挂载存储**：使用获取到的IQN名称登录到存储。
    ```bash
    iscsiadm -m node -T iqn.2003-01.org.linux-iscsi.localhost.x8664:sn.4c2c6c0f2a3d -p 192.168.10.10 --login
    ```

登录成功后，在客户端使用 `lsblk` 或 `fdisk -l` 命令查看，会发现多出了一块硬盘（例如 `/dev/sdb`），这就是远程挂载的10GB RAID设备。之后就可以像本地硬盘一样对其进行分区、格式化和挂载使用了。

3.  **卸载存储**：当不再需要时，可以登出（Logout）以卸载远程存储。
    ```bash
    iscsiadm -m node -T iqn.2003-01.org.linux-iscsi.localhost.x8664:sn.4c2c6c0f2a3d -p 192.168.10.10 --logout
    ```

![](img/5cf49904607924b188e3698cc975c73f_161.png)

![](img/5cf49904607924b188e3698cc975c73f_163.png)

## Windows客户端挂载iSCSI存储

iSCSI是跨平台的协议，Windows系统也可以方便地挂载。我们以Windows 10为例。

1.  **确保网络连通**：将Windows客户端的IP地址设置为与服务端同一网段（如192.168.10.30），并测试连通性。

![](img/5cf49904607924b188e3698cc975c73f_165.png)

![](img/5cf49904607924b188e3698cc975c73f_167.png)

2.  **打开iSCSI发起程序**：在开始菜单搜索并打开“iSCSI发起程序”。首次使用会提示启用服务，点击“是”。

![](img/5cf49904607924b188e3698cc975c73f_169.png)

![](img/5cf49904607924b188e3698cc975c73f_171.png)

3.  **配置发起程序名称**：在“配置”选项卡中，将“发起程序名称”修改为与服务端ACL中完全一致的名称（即之前记录的长字符串）。

4.  **发现与连接目标**：
    *   切换到“发现”选项卡，点击“发现门户”，输入服务端IP地址（192.168.10.10），端口保持默认3260，点击“确定”。
    *   切换到“目标”选项卡，应该能看到可用的IQN目标。选中它，点击“连接”。
    *   在弹出窗口中，可以勾选“将此连接添加到收藏目标列表”，点击“确定”。

5.  **初始化和使用磁盘**：连接成功后，打开“磁盘管理”（diskmgmt.msc），会发现多出一块“未知”的10GB磁盘。右键点击该磁盘，选择“联机”，然后“初始化磁盘”，之后就可以像本地磁盘一样创建分区、格式化并分配盘符了。

6.  **断开连接**：在“iSCSI发起程序”的“目标”选项卡中，选中已连接的目标，点击“断开”，即可卸载网络存储。

## 本章总结

![](img/5cf49904607924b188e3698cc975c73f_173.png)

本节课中我们一起学习了iSCSI网络存储服务的配置与管理。我们首先在服务端创建了RAID 10阵列以保证本地数据安全，然后使用 `targetcli` 工具高效地配置了iSCSI服务端，定义了共享设备、访问控制和网络端口。接着，我们分别在Linux和Windows客户端上成功挂载并使用了该网络存储设备。

![](img/5cf49904607924b188e3698cc975c73f_175.png)

![](img/5cf49904607924b188e3698cc975c73f_176.png)

关键点在于理解iSCSI共享的是**块设备**本身，而非文件系统，因此客户端获得的是完整的磁盘控制权。同时，客户端的认证依赖于与服务端ACL严格匹配的发起端名称。虽然iSCSI的性能受网络带宽和延迟影响，但在稳定的局域网环境中，它为实现集中化存储和跨平台数据共享提供了强大的解决方案。