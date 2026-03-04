# Linux运维基础：P13：网络配置与系统管理

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_1.png)

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_3.png)

在本节课中，我们将学习Linux系统中的网络配置、域名解析、命令别名、历史命令管理以及日期时间管理。这些是日常系统管理和故障排查中常用的基础技能。

## 修改网卡IP地址

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_5.png)

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_7.png)

上一节我们介绍了使用`vim`编辑配置文件的方法。本节中我们来看看如何修改网卡的IP地址。

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_9.png)

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_11.png)

修改网卡IP地址主要有两种方式：直接编辑配置文件和使用`nmcli`命令。

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_13.png)

### 方法一：编辑配置文件

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_15.png)

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_17.png)

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_19.png)

网卡的配置文件通常位于 `/etc/sysconfig/network-scripts/` 目录下，文件名格式为 `ifcfg-<网卡名>`。

以下是修改步骤：

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_21.png)

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_23.png)

1.  使用 `ifconfig` 或 `ip addr` 命令查看当前使用的网卡名称。
    ```bash
    ifconfig
    # 或
    ip addr
    ```

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_25.png)

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_27.png)

2.  使用 `vim` 编辑对应网卡的配置文件。
    ```bash
    vim /etc/sysconfig/network-scripts/ifcfg-ens32
    ```

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_29.png)

3.  在配置文件中找到 `IPADDR` 这一行，修改其后的IP地址。
    ```
    IPADDR=192.168.0.90
    ```

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_31.png)

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_33.png)

4.  保存并退出 `vim`（按 `ESC` 键，然后输入 `:wq!` 并回车）。

5.  重启网络服务使配置生效。由于IP地址改变，当前SSH连接可能会断开。
    ```bash
    systemctl restart network
    ```

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_35.png)

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_37.png)

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_39.png)

6.  使用新的IP地址重新连接服务器，并使用 `ip addr` 命令验证修改是否成功。

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_41.png)

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_43.png)

### 方法二：使用 `nmcli` 命令（红帽认证相关）

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_45.png)

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_47.png)

`nmcli` 是网络管理命令行工具。以下命令可以修改网卡IP并永久生效，但命令较长，主要用于红帽认证考试。

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_49.png)

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_51.png)

```bash
nmcli connection modify ens32 ipv4.method manual ipv4.addresses 192.168.0.80/24 ipv4.gateway 192.168.0.1 connection.autoconnect yes
```

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_53.png)

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_55.png)

命令执行后，需要重新激活网卡连接：

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_57.png)

```bash
nmcli connection up ens32
```

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_59.png)

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_61.png)

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_63.png)

**注意**：如果不准备参加红帽认证，方法一（编辑配置文件）更为常用和直观。

## 域名解析相关命令

接下来，我们学习两个用于诊断域名解析问题的命令。

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_65.png)

### `host` 命令

`host` 命令用于将域名解析为IP地址。

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_67.png)

```bash
host www.baidu.com
```
执行该命令会显示百度域名对应的IP地址列表。

### `nslookup` 命令

`nslookup` 命令用于查询域名解析是否正常，常用来诊断DNS服务器问题。

```bash
nslookup www.baidu.com
```
如果命令能返回域名对应的IP地址和使用的DNS服务器信息，则说明DNS解析正常。如果返回超时或错误，则可能是DNS服务器配置有问题。

此时，应检查网卡配置文件中的 `DNS1` 和 `DNS2` 配置项，确保其指向可用的DNS服务器地址（如 `223.5.5.5` 或 `8.8.8.8`），然后重启网络服务。

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_69.png)

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_71.png)

## 命令别名管理

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_73.png)

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_75.png)

`alias` 命令可以为较长的命令设置一个简短的别名，提高输入效率。

### 查看现有别名

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_77.png)

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_79.png)

直接输入 `alias` 可以查看系统当前定义的所有命令别名。
```bash
alias
```
你会发现，系统默认已经为一些常用命令设置了别名，例如 `ll` 实际上是 `ls -l --color=auto`。

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_81.png)

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_83.png)

### 定义新别名

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_85.png)

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_87.png)

使用 `alias 别名='原命令'` 的格式定义别名。等号两边不能有空格。
```bash
alias lh='ls -lh'
```
定义后，输入 `lh` 就等同于输入 `ls -lh`。

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_89.png)

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_91.png)

### 永久生效

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_93.png)

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_95.png)

通过命令行定义的别名只在当前终端会话有效。退出后就会失效。若想永久生效，需要将别名定义写入用户家目录下的 `~/.bashrc` 文件中。
```bash
vim ~/.bashrc
# 在文件末尾添加：alias lh='ls -lh'
```
保存退出后，执行 `source ~/.bashrc` 使配置立即生效，或重新登录终端。

**注意**：定义别名时，应避免与系统已有的命令名冲突。

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_97.png)

## 历史命令管理

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_99.png)

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_101.png)

`history` 命令用于管理用户在终端中执行过的命令历史记录。

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_103.png)

### 查看历史命令

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_105.png)

直接输入 `history` 会列出所有执行过的命令，每条命令前有编号。
```bash
history
```

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_107.png)

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_109.png)

### 历史记录的存储机制

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_111.png)

*   用户执行命令时，命令会暂存于内存中。
*   当用户退出终端时，这些记录会追加保存到用户家目录的 `~/.bash_history` 文件中。
*   用户下次登录时，系统会读取该文件，将历史记录加载到内存。

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_113.png)

### 管理历史记录

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_115.png)

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_117.png)

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_119.png)

以下是 `history` 命令的常用选项：

*   `history -c`：清空当前终端内存中的历史命令列表（不影响 `~/.bash_history` 文件）。
*   `history -d 编号`：删除历史记录中指定编号的命令。
*   `history -a`：立即将当前终端新增的历史命令追加到 `~/.bash_history` 文件。

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_121.png)

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_123.png)

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_125.png)

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_127.png)

### 调用历史命令的快捷方式

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_129.png)

*   `!!`：执行上一条命令。
*   `!编号`：执行历史记录中对应编号的命令。
    ```bash
    !13
    ```
*   `!字符串`：执行最近一条以该字符串开头的命令。
    ```bash
    !vim
    ```

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_131.png)

### 设置历史记录条数

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_133.png)

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_135.png)

系统默认保存1000条历史命令。可以通过修改 `/etc/profile` 文件中的 `HISTSIZE` 值来调整。
```bash
vim /etc/profile
# 找到并修改：HISTSIZE=100
```

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_137.png)

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_139.png)

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_141.png)

## 日期与时间管理

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_143.png)

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_145.png)

最后，我们来学习如何查看和设置系统时间。

### `date` 命令：查看与设置系统时间

**查看时间**：
直接运行 `date` 显示完整的日期、时间和时区。
```bash
date
```
使用格式符可以自定义输出：
```bash
date +%F        # 显示年月日，如 2023-10-27
date +%T        # 显示时分秒，如 14:30:00
date "+%Y-%m-%d %H:%M:%S" # 组合显示
```

**设置时间**：
使用 `-s` 选项设置系统时间。设置是永久生效的。
```bash
date -s "2023-10-27 15:00:00"
```
注意，如果时间字符串中包含空格，需要用引号将其引为一个整体。

### 系统时钟与硬件时钟

Linux系统中有两个时钟：
*   **系统时钟**：由Linux内核维护，使用 `date` 命令查看和设置。
*   **硬件时钟**：即BIOS时间，使用 `clock` 或 `hwclock` 命令查看。

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_147.png)

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_149.png)

可以使用 `hwclock` 命令同步两者：
```bash
hwclock -w  # 将硬件时钟设置为与系统时钟相同
hwclock -s  # 将系统时钟设置为与硬件时钟相同
```

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_151.png)

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_153.png)

### 企业实践：NTP时间同步

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_155.png)

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_157.png)

在实际生产环境中，手动设置时间既不准确也不高效。通常会让服务器与**NTP（网络时间协议）服务器**进行同步，以确保集群内所有服务器时间一致。这是运维工作中的标准做法。

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_159.png)

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_161.png)

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_163.png)

### `cal` 命令：查看日历

`cal` 命令可以方便地查看日历。
```bash
cal          # 查看当月日历
cal 2023     # 查看2023年全年日历
cal 10 2023  # 查看2023年10月的日历
```

---

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_165.png)

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_167.png)

本节课中我们一起学习了：
1.  **修改网卡IP**的两种方法：编辑配置文件和使用`nmcli`命令。
2.  使用 **`host`** 和 **`nslookup`** 命令进行域名解析和DNS故障诊断。
3.  使用 **`alias`** 命令为常用命令创建简短的别名，提升工作效率。
4.  使用 **`history`** 命令查看、管理和快速调用历史命令。
5.  使用 **`date`**、**`hwclock`** 命令管理系统时间和硬件时钟，并了解了企业级的时间同步方案（NTP）。

![](img/7cb55b143e0a98ee23ac6bb9e6db4c81_169.png)

这些命令是Linux系统管理的基础，熟练掌握它们将有助于你更高效地进行日常维护和故障排查。