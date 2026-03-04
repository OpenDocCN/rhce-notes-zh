# Linux运维RHCSA+RHC培训教程：P13：网络配置与系统管理基础

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_1.png)

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_3.png)

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_5.png)

在本节课中，我们将学习Linux系统中几个重要的基础管理技能，包括修改网络配置、使用域名解析工具、管理命令别名和历史记录，以及查看和设置系统时间。这些技能是日常系统运维工作的基础。

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_7.png)

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_9.png)

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_11.png)

## 修改网卡IP地址

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_13.png)

上一节我们学习了使用VIM编辑器，本节中我们来看看如何利用它来修改网络配置。修改网卡IP地址有两种主要方式：直接编辑配置文件和使用`nmcli`命令。

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_15.png)

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_17.png)

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_19.png)

### 方法一：编辑配置文件

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_21.png)

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_23.png)

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_24.png)

网卡的配置文件通常位于 `/etc/sysconfig/network-scripts/` 目录下，文件名格式为 `ifcfg-<网卡名>`，例如 `ifcfg-ens32`。

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_26.png)

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_28.png)

以下是修改步骤：
1.  使用VIM打开配置文件：
    ```bash
    vim /etc/sysconfig/network-scripts/ifcfg-ens32
    ```
2.  在文件中找到 `IPADDR` 这一行，修改其后的IP地址。
3.  保存并退出VIM编辑器。
4.  重启网络服务使配置生效：
    ```bash
    systemctl restart network
    ```
    或者，也可以先关闭再启动指定网卡：
    ```bash
    ifdown ens32
    ifup ens32
    ```

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_30.png)

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_32.png)

### 方法二：使用 `nmcli` 命令（适用于红帽认证考试）

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_34.png)

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_36.png)

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_38.png)

`nmcli` 是网络管理命令行工具，其命令较长，但功能强大。修改IP地址的命令格式如下：
```bash
nmcli connection modify ens32 ipv4.addresses 192.168.0.90/24 ipv4.method manual connection.autoconnect yes
```
*   `ens32`：需要修改的网卡名称。
*   `192.168.0.90/24`：要设置的新IP地址和子网掩码。
*   `manual`：表示手动配置IP。
*   `yes`：表示开机自动连接此网卡。

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_40.png)

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_42.png)

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_44.png)

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_46.png)

执行上述命令后，需要重新激活该网络连接：
```bash
nmcli connection up ens32
```

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_48.png)

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_50.png)

> **注意**：如果不需要参加红帽认证，方法一（直接编辑配置文件）更为常用和直观。

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_52.png)

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_54.png)

## 域名解析相关命令

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_56.png)

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_58.png)

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_60.png)

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_62.png)

网络配置好后，我们常常需要检查域名解析是否正常。以下是两个有用的诊断工具。

### `host` 命令

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_64.png)

`host` 命令用于查询域名对应的IP地址。
```bash
host www.baidu.com
```
执行该命令会显示 `www.baidu.com` 这个域名所解析到的IP地址列表。

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_66.png)

### `nslookup` 命令

`nslookup` 命令也用于域名解析查询，但它会显示是哪台DNS服务器提供的解析结果，常用于诊断DNS问题。
```bash
nslookup www.baidu.com
```
如果命令返回超时或无法连接服务器，通常意味着当前系统的DNS配置有误，需要检查 `/etc/sysconfig/network-scripts/ifcfg-ens32` 文件中的 `DNS1` 等配置项。

## 命令别名管理 (`alias`)

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_68.png)

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_70.png)

在Linux中，可以为常用的长命令设置一个简短的别名，以提高工作效率。

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_72.png)

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_74.png)

### 查看与设置别名

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_76.png)

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_78.png)

*   **查看现有别名**：直接输入 `alias` 命令。
*   **设置临时别名**：使用 `alias 别名='原命令'` 的格式。等号两边不能有空格。
    ```bash
    alias ll='ls -lh'
    ```
    设置后，输入 `ll` 就等同于输入 `ls -lh`。

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_80.png)

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_82.png)

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_84.png)

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_86.png)

### 永久生效的别名

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_88.png)

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_90.png)

临时别名在终端关闭后就会失效。若想永久生效，需要将别名定义写入用户家目录下的 `~/.bashrc` 文件中。
1.  使用VIM编辑该文件：
    ```bash
    vim ~/.bashrc
    ```
2.  在文件末尾按格式添加别名，例如：
    ```bash
    alias ll='ls -lh'
    ```
3.  保存退出后，执行 `source ~/.bashrc` 使配置立即生效，或重新登录终端。

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_92.png)

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_94.png)

> **注意**：定义别名时，应避免与系统已有的命令名冲突。

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_96.png)

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_98.png)

## 管理命令历史 (`history`)

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_100.png)

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_101.png)

Linux系统会记录用户执行过的命令，方便查询和再次调用。

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_103.png)

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_105.png)

### 基本用法

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_107.png)

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_109.png)

*   **查看历史命令**：输入 `history`。
*   **执行历史命令**：
    *   `!n`：执行历史记录中第 `n` 条命令。
    *   `!字符串`：执行最近一条以该“字符串”开头的命令。
    *   `!!`：执行上一条命令。

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_111.png)

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_113.png)

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_115.png)

### 历史记录的管理

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_117.png)

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_119.png)

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_121.png)

*   **清空当前会话的历史记录**：
    ```bash
    history -c
    ```
    > 注意：这仅清空内存中的记录，退出终端时，这些记录仍可能写入文件。
*   **删除指定历史记录**：
    ```bash
    history -d 行号
    ```
*   **立即将内存中的历史记录同步到文件**：
    ```bash
    history -a
    ```
*   **修改历史记录保存条数**：编辑 `/etc/profile` 文件，修改 `HISTSIZE=` 后面的数值。

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_123.png)

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_125.png)

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_127.png)

历史记录文件默认保存在用户家目录的 `~/.bash_history` 中。

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_129.png)

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_131.png)

## 系统日期时间管理 (`date`)

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_133.png)

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_135.png)

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_137.png)

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_138.png)

### 查看日期时间

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_140.png)

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_142.png)

*   **查看完整时间**：`date`
*   **按格式查看**：使用 `+%格式符`
    ```bash
    date +%F        # 显示年月日，如 2023-10-27
    date +%T        # 显示时分秒，如 14:30:00
    date +%Y-%m-%d" "%H:%M:%S # 自定义格式
    ```

### 设置系统时间

使用 `-s` 选项设置时间，日期和时间部分用空格隔开，整体建议用引号包围。
```bash
date -s "2023-10-27 15:30:00"
```

### 系统时间与硬件时间

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_144.png)

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_146.png)

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_148.png)

*   **系统时间**：由Linux内核维护的时间，用 `date` 查看。
*   **硬件时间**：主板BIOS中的时间，用 `clock` 或 `hwclock` 命令查看。
*   **时间同步**：
    ```bash
    hwclock --systohc   # 将系统时间同步到硬件时间
    hwclock --hctosys   # 将硬件时间同步到系统时间
    ```
在实际生产环境中，服务器通常配置为与NTP时间服务器自动同步，以保证集群内所有机器时间一致。

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_150.png)

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_152.png)

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_154.png)

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_156.png)

### 查看日历 (`cal`)

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_158.png)

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_160.png)

`cal` 命令可以方便地查看日历。
```bash
cal          # 查看当月日历
cal 2024     # 查看2024年全年日历
```

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_162.png)

---

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_164.png)

![](img/e7ad8e6394e16cb3f44e63e71ddd2ad3_166.png)

本节课中我们一起学习了Linux系统的基础管理操作。我们掌握了通过编辑配置文件和使用`nmcli`命令来修改网络IP地址；了解了`host`和`nslookup`这两个域名解析诊断工具；学会了使用`alias`为命令设置别名来提升效率；探讨了如何利用`history`管理和调用命令历史记录；最后，还学习了使用`date`、`hwclock`和`cal`命令来查看和管理系统时间与日历。这些内容构成了Linux系统日常运维的重要基础，希望大家能熟练掌握。