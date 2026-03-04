# Linux运维进阶：P13：网络配置、命令别名与历史、时间管理 📚

![](img/a4ac88729c104656a9ed1d05da6cb564_1.png)

![](img/a4ac88729c104656a9ed1d05da6cb564_3.png)

在本节课中，我们将学习Linux系统中的几个实用技能，包括如何修改网络配置、使用域名解析命令、设置命令别名、管理命令历史记录以及查看和设置系统时间。这些技能对于日常的系统管理和故障排查非常有帮助。

![](img/a4ac88729c104656a9ed1d05da6cb564_5.png)

![](img/a4ac88729c104656a9ed1d05da6cb564_7.png)

---

![](img/a4ac88729c104656a9ed1d05da6cb564_9.png)

![](img/a4ac88729c104656a9ed1d05da6cb564_11.png)

## 修改网卡IP地址 🌐

![](img/a4ac88729c104656a9ed1d05da6cb564_13.png)

![](img/a4ac88729c104656a9ed1d05da6cb564_15.png)

![](img/a4ac88729c104656a9ed1d05da6cb564_17.png)

上一节我们介绍了使用`vim`编辑器修改配置文件的方法。本节中，我们来看看如何修改网卡的IP地址。

![](img/a4ac88729c104656a9ed1d05da6cb564_19.png)

![](img/a4ac88729c104656a9ed1d05da6cb564_21.png)

### 方法一：直接修改配置文件

![](img/a4ac88729c104656a9ed1d05da6cb564_23.png)

![](img/a4ac88729c104656a9ed1d05da6cb564_25.png)

![](img/a4ac88729c104656a9ed1d05da6cb564_27.png)

这是最常用且直观的方法。网卡的配置文件通常位于 `/etc/sysconfig/network-scripts/` 目录下，文件名类似 `ifcfg-ens32`。

![](img/a4ac88729c104656a9ed1d05da6cb564_29.png)

![](img/a4ac88729c104656a9ed1d05da6cb564_31.png)

以下是修改步骤：

![](img/a4ac88729c104656a9ed1d05da6cb564_33.png)

![](img/a4ac88729c104656a9ed1d05da6cb564_35.png)

1.  使用 `vim` 打开网卡配置文件。
    ```bash
    vim /etc/sysconfig/network-scripts/ifcfg-ens32
    ```
2.  在文件中找到 `IPADDR` 这一行，修改其后的IP地址。
    ```bash
    IPADDR=192.168.0.90
    ```
3.  保存并退出 `vim` 编辑器。
4.  重启网络服务使配置生效。
    ```bash
    systemctl restart network
    ```
    注意：重启网络后，如果IP地址改变，当前的SSH连接可能会断开，需要使用新IP重新连接。

![](img/a4ac88729c104656a9ed1d05da6cb564_37.png)

### 方法二：使用 `nmcli` 命令（红帽认证相关）

![](img/a4ac88729c104656a9ed1d05da6cb564_39.png)

![](img/a4ac88729c104656a9ed1d05da6cb564_41.png)

![](img/a4ac88729c104656a9ed1d05da6cb564_43.png)

![](img/a4ac88729c104656a9ed1d05da6cb564_45.png)

`nmcli` 是网络管理命令行工具。以下命令可以永久修改网卡IP，但命令较长，主要用于红帽认证考试。

![](img/a4ac88729c104656a9ed1d05da6cb564_47.png)

![](img/a4ac88729c104656a9ed1d05da6cb564_49.png)

```bash
nmcli connection modify ens32 ipv4.method manual ipv4.addresses 192.168.0.80/24 ipv4.gateway 192.168.0.1 connection.autoconnect yes
```
修改后，需要重新激活网卡连接：
```bash
nmcli connection up ens32
```

![](img/a4ac88729c104656a9ed1d05da6cb564_51.png)

![](img/a4ac88729c104656a9ed1d05da6cb564_53.png)

![](img/a4ac88729c104656a9ed1d05da6cb564_55.png)

---

![](img/a4ac88729c104656a9ed1d05da6cb564_57.png)

## 域名解析命令 🔍

![](img/a4ac88729c104656a9ed1d05da6cb564_59.png)

![](img/a4ac88729c104656a9ed1d05da6cb564_61.png)

![](img/a4ac88729c104656a9ed1d05da6cb564_63.png)

![](img/a4ac88729c104656a9ed1d05da6cb564_65.png)

了解网络连通性时，我们常常需要检查域名解析是否正常。

### `host` 命令

![](img/a4ac88729c104656a9ed1d05da6cb564_67.png)

`host` 命令用于将域名解析为IP地址。

![](img/a4ac88729c104656a9ed1d05da6cb564_69.png)

```bash
host www.baidu.com
```
执行上述命令，会显示百度域名对应的IP地址列表。

### `nslookup` 命令

`nslookup` 命令用于查询域名解析过程，常用来诊断DNS（域名解析服务）是否工作正常。

```bash
nslookup www.baidu.com
```
如果命令返回超时或错误，通常意味着本机配置的DNS服务器地址有问题，需要检查 `/etc/sysconfig/network-scripts/ifcfg-ens32` 文件中的 `DNS1` 配置项。

![](img/a4ac88729c104656a9ed1d05da6cb564_71.png)

![](img/a4ac88729c104656a9ed1d05da6cb564_73.png)

---

![](img/a4ac88729c104656a9ed1d05da6cb564_75.png)

![](img/a4ac88729c104656a9ed1d05da6cb564_77.png)

## 命令别名管理 🏷️

![](img/a4ac88729c104656a9ed1d05da6cb564_79.png)

对于较长或带复杂选项的常用命令，我们可以为其设置一个简短的别名（Alias），提高工作效率。

![](img/a4ac88729c104656a9ed1d05da6cb564_81.png)

![](img/a4ac88729c104656a9ed1d05da6cb564_83.png)

![](img/a4ac88729c104656a9ed1d05da6cb564_85.png)

### 查看与设置别名

![](img/a4ac88729c104656a9ed1d05da6cb564_87.png)

![](img/a4ac88729c104656a9ed1d05da6cb564_89.png)

*   **查看现有别名**：直接输入 `alias` 命令。
*   **设置临时别名**：使用 `alias 别名='原命令'` 的格式。等号两边不能有空格。
    ```bash
    alias ll='ls -lh'
    alias lh='ls -lh'
    ```
    这样，输入 `ll` 或 `lh` 就等同于输入 `ls -lh`。
*   **设置永久别名**：将别名定义写入用户家目录下的 `.bashrc` 文件。
    ```bash
    vim ~/.bashrc
    # 在文件末尾添加：alias ll='ls -lh'
    ```
    保存后，执行 `source ~/.bashrc` 或重新登录即可生效。

![](img/a4ac88729c104656a9ed1d05da6cb564_91.png)

![](img/a4ac88729c104656a9ed1d05da6cb564_93.png)

**注意**：定义别名时，应避免与系统已有的命令名冲突。

![](img/a4ac88729c104656a9ed1d05da6cb564_95.png)

![](img/a4ac88729c104656a9ed1d05da6cb564_97.png)

---

![](img/a4ac88729c104656a9ed1d05da6cb564_99.png)

## 管理命令历史 📜

![](img/a4ac88729c104656a9ed1d05da6cb564_101.png)

![](img/a4ac88729c104656a9ed1d05da6cb564_103.png)

![](img/a4ac88729c104656a9ed1d05da6cb564_105.png)

![](img/a4ac88729c104656a9ed1d05da6cb564_107.png)

`history` 命令用于管理用户在终端中执行过的命令历史记录，方便回溯和快速调用。

### 常用操作

![](img/a4ac88729c104656a9ed1d05da6cb564_109.png)

![](img/a4ac88729c104656a9ed1d05da6cb564_111.png)

![](img/a4ac88729c104656a9ed1d05da6cb564_113.png)

以下是 `history` 命令的一些实用操作：

![](img/a4ac88729c104656a9ed1d05da6cb564_115.png)

*   **查看历史命令**：直接输入 `history`。
*   **清空当前会话的历史记录**：`history -c`（仅清空内存中的记录，文件中的记录需手动删除）。
*   **删除指定历史命令**：`history -d 行号`。
*   **立即同步历史记录到文件**：`history -a`。
*   **调用历史命令**：
    *   `!!`：执行上一条命令。
    *   `!字符串`：执行最近一条以该字符串开头的命令。
    *   `!行号`：执行历史记录中对应行号的命令。

![](img/a4ac88729c104656a9ed1d05da6cb564_117.png)

![](img/a4ac88729c104656a9ed1d05da6cb564_119.png)

![](img/a4ac88729c104656a9ed1d05da6cb564_121.png)

历史命令默认保存在用户家目录的 `.bash_history` 文件中。系统默认记录1000条，可以通过修改 `/etc/profile` 文件中的 `HISTSIZE` 变量值来调整。

![](img/a4ac88729c104656a9ed1d05da6cb564_123.png)

![](img/a4ac88729c104656a9ed1d05da6cb564_125.png)

---

![](img/a4ac88729c104656a9ed1d05da6cb564_127.png)

![](img/a4ac88729c104656a9ed1d05da6cb564_129.png)

![](img/a4ac88729c104656a9ed1d05da6cb564_131.png)

![](img/a4ac88729c104656a9ed1d05da6cb564_133.png)

## 系统日期与时间管理 ⏰

![](img/a4ac88729c104656a9ed1d05da6cb564_135.png)

### `date` 命令

![](img/a4ac88729c104656a9ed1d05da6cb564_137.png)

![](img/a4ac88729c104656a9ed1d05da6cb564_139.png)

`date` 命令用于查看和设置系统时间。

![](img/a4ac88729c104656a9ed1d05da6cb564_141.png)

![](img/a4ac88729c104656a9ed1d05da6cb564_143.png)

![](img/a4ac88729c104656a9ed1d05da6cb564_145.png)

![](img/a4ac88729c104656a9ed1d05da6cb564_147.png)

![](img/a4ac88729c104656a9ed1d05da6cb564_149.png)

*   **查看时间**：
    ```bash
    date                          # 查看完整时间
    date "+%Y-%m-%d"              # 按“年-月-日”格式查看
    date "+%H:%M:%S"              # 按“时:分:秒”格式查看
    ```
*   **设置时间**：
    ```bash
    date -s "2022-12-25 15:30:00" # 同时设置日期和时间
    ```
    设置时间时，建议使用双引号将整个时间字符串引起来，避免空格导致错误。

### 系统时钟与硬件时钟

Linux系统中有两个时钟：
*   **系统时钟**：由Linux内核维护，使用 `date` 命令查看。
*   **硬件时钟**：即BIOS时间，使用 `clock` 或 `hwclock` 命令查看。

可以使用 `hwclock` 命令进行同步：
```bash
hwclock --systohc   # 将硬件时钟设置为与系统时钟相同
hwclock --hctosys   # 将系统时钟设置为与硬件时钟相同
```

在实际生产环境中，为了保证所有服务器时间一致，通常会配置 **NTP（网络时间协议）** 服务，自动与互联网上的标准时间服务器进行同步，而非手动修改。

![](img/a4ac88729c104656a9ed1d05da6cb564_151.png)

![](img/a4ac88729c104656a9ed1d05da6cb564_153.png)

![](img/a4ac88729c104656a9ed1d05da6cb564_155.png)

### `cal` 命令

![](img/a4ac88729c104656a9ed1d05da6cb564_157.png)

`cal` 命令用于显示日历。
```bash
cal          # 显示当月日历
cal 2022     # 显示2022年全年日历
```

![](img/a4ac88729c104656a9ed1d05da6cb564_159.png)

![](img/a4ac88729c104656a9ed1d05da6cb564_161.png)

![](img/a4ac88729c104656a9ed1d05da6cb564_163.png)

![](img/a4ac88729c104656a9ed1d05da6cb564_165.png)

![](img/a4ac88729c104656a9ed1d05da6cb564_167.png)

---

## 总结 🎯

![](img/a4ac88729c104656a9ed1d05da6cb564_169.png)

本节课我们一起学习了多个Linux系统管理中的实用知识点：
1.  **修改网卡IP**：掌握了通过编辑配置文件和使用 `nmcli` 命令两种方式。
2.  **域名解析**：学会了使用 `host` 和 `nslookup` 命令来检查域名和DNS状态。
3.  **命令别名**：了解了如何通过 `alias` 为常用命令设置快捷方式，提升操作效率。
4.  **历史命令**：熟悉了 `history` 命令的查看、管理以及快速调用的技巧。
5.  **时间管理**：学会了使用 `date`、`hwclock` 查看和设置时间，并了解了NTP同步的概念。

![](img/a4ac88729c104656a9ed1d05da6cb564_171.png)

![](img/a4ac88729c104656a9ed1d05da6cb564_173.png)

这些内容涵盖了系统网络、日常操作和基础维护的多个方面，是成为合格Linux运维工程师的重要基础。