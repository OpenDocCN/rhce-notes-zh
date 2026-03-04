# Linux运维全套培训课程：P13：修改网卡地址、host、nslookup、alias、history、date管理 📚

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_1.png)

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_3.png)

在本节课中，我们将学习Linux系统中几个重要的网络和系统管理命令。主要内容包括如何修改网卡IP地址、使用`host`和`nslookup`进行域名解析、为命令设置别名、管理命令历史记录以及查看和设置系统日期时间。这些技能是日常系统运维的基础。

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_5.png)

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_7.png)

## 修改网卡IP地址 🌐

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_9.png)

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_11.png)

上一节我们学习了使用`vim`编辑器。本节中，我们来看看如何修改网卡的IP地址。在Linux中，网卡的配置文件通常位于 `/etc/sysconfig/network-scripts/` 目录下，文件名格式为 `ifcfg-<网卡名>`。

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_13.png)

### 方法一：直接修改配置文件

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_15.png)

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_17.png)

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_19.png)

这是最常用且简单的方法。以下是具体步骤：

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_21.png)

1.  使用`vim`打开目标网卡的配置文件。例如，对于网卡`ens32`：
    ```bash
    vim /etc/sysconfig/network-scripts/ifcfg-ens32
    ```
2.  在文件中找到 `IPADDR` 这一行，修改其后的IP地址为你想要的值。
3.  保存并退出`vim`（按 `Esc` 键，然后输入 `:wq!` 并回车）。
4.  修改配置文件后，需要重启网络服务使更改生效：
    ```bash
    systemctl restart network
    ```
    重启网络后，当前的SSH连接可能会因为IP地址改变而断开，需要使用新的IP地址重新连接。

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_23.png)

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_25.png)

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_27.png)

### 方法二：使用`nmcli`命令（红帽认证相关）

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_29.png)

`nmcli`是网络管理命令行工具。以下命令可以修改网卡IP，但命令较长，主要用于红帽RHCSA认证考试。
```bash
nmcli connection modify ens32 ipv4.method manual ipv4.addresses 192.168.0.90/24 connection.autoconnect yes
```
命令解释：
*   `nmcli connection modify ens32`: 修改名为`ens32`的网络连接。
*   `ipv4.method manual`: 设置IPv4为手动配置。
*   `ipv4.addresses 192.168.0.90/24`: 设置IP地址和子网掩码。
*   `connection.autoconnect yes`: 设置开机自动连接。

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_31.png)

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_33.png)

执行后，需要激活网卡以使更改生效：
```bash
nmcli connection up ens32
```
此方法同样是永久修改。

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_35.png)

---

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_37.png)

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_39.png)

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_41.png)

## 域名解析命令 🔍

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_43.png)

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_45.png)

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_47.png)

网络连通性排查时，经常需要检查域名解析是否正常。以下是两个相关的命令。

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_49.png)

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_51.png)

### `host`命令

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_53.png)

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_55.png)

`host`命令用于将域名解析到对应的IP地址。
```bash
host www.baidu.com
```
执行该命令会显示百度域名对应的IP地址列表。

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_57.png)

### `nslookup`命令

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_59.png)

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_61.png)

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_63.png)

`nslookup`命令用于查询域名解析是否正常，常用来诊断DNS服务器问题。
```bash
nslookup www.baidu.com
```
如果命令能返回域名服务器地址和解析出的IP，说明DNS工作正常。如果返回超时或错误，则可能是DNS服务器地址配置有误。此时，需要检查网卡配置文件中的`DNS`设置项。

---

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_65.png)

## 命令别名管理 🏷️

`alias`命令可以为较长的命令设置一个简短的别名，提高操作效率。

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_67.png)

### 查看与设置别名

直接输入`alias`可以查看系统当前已定义的所有别名。
```bash
alias
```
系统已预置了一些常用别名，例如`ll`实际上是`ls -l`，`cp`、`mv`、`rm`等命令默认也带有交互提示的别名。

自定义别名格式如下（等号两边不能有空格）：
```bash
alias 别名='原命令 [选项]'
```
例如，为`ls -lh`创建一个别名`lh`：
```bash
alias lh='ls -lh'
```
定义后，输入`lh`即等同于输入`ls -lh`。

### 别名生效范围

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_69.png)

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_71.png)

以上方法定义的别名是临时的，仅在当前终端会话有效。退出终端或重启系统后失效。

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_73.png)

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_75.png)

若需永久生效，需要将别名定义写入用户家目录下的 `~/.bashrc` 配置文件中：
```bash
vim ~/.bashrc
```
在文件末尾按相同格式（`alias lh='ls -lh'`）添加别名，保存退出即可。之后新开的终端会话都会加载此别名。

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_77.png)

---

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_79.png)

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_81.png)

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_83.png)

## 历史命令管理 📜

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_85.png)

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_87.png)

`history`命令用于管理用户在终端中执行过的命令历史记录。

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_89.png)

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_91.png)

### 查看历史命令

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_93.png)

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_95.png)

直接输入`history`会列出所有执行过的命令及其编号。
```bash
history
```
历史命令默认存储在用户家目录的 `~/.bash_history` 文件中。用户登录时，系统会读取此文件加载到内存；用户退出时，再将内存中的新命令追加回文件。

### 管理历史命令

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_97.png)

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_99.png)

`history`命令有几个常用选项：
*   `-c`: 清空当前终端内存中的历史命令列表（不影响`~/.bash_history`文件）。
*   `-d 编号`: 删除历史记录中指定编号的命令。
*   `-a`: 立即将当前终端新增的历史命令追加到`~/.bash_history`文件。

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_101.png)

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_103.png)

若要彻底清除痕迹，可以删除历史文件，但系统会在下次登录时重新生成。
```bash
rm -f ~/.bash_history
```

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_105.png)

### 调用历史命令的快捷方式

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_107.png)

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_109.png)

*   `!!`: 执行上一条命令。
*   `!编号`: 执行历史记录中对应编号的命令。
*   `!字符串`: 执行历史记录中**最近一条**以该字符串开头的命令。

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_111.png)

例如，执行最近一条以`vim`开头的命令：
```bash
!vim
```

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_113.png)

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_115.png)

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_117.png)

### 设置历史命令记录条数

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_119.png)

系统默认记录1000条历史命令。可以通过修改 `/etc/profile` 文件中的 `HISTSIZE` 变量值来调整。
```bash
vim /etc/profile
```
找到 `HISTSIZE=1000`，修改数字即可（如改为`HISTSIZE=100`）。修改后需要重新登录或执行 `source /etc/profile` 使配置生效。

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_121.png)

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_123.png)

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_125.png)

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_127.png)

---

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_129.png)

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_131.png)

## 日期时间管理 ⏰

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_133.png)

`date`命令用于查看和设置系统日期时间。

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_135.png)

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_137.png)

### 查看日期时间

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_139.png)

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_141.png)

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_143.png)

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_145.png)

直接输入`date`显示完整的日期、时间和时区。
```bash
date
```
可以使用格式符定制输出：
```bash
date +%F        # 显示年月日，格式：YYYY-MM-DD
date +%T        # 显示时分秒，格式：HH:MM:SS
date +"%Y-%m-%d %H:%M:%S" # 组合显示
```

### 设置日期时间

使用`-s`选项可以设置系统时间。设置后时间是永久生效的。
```bash
date -s "2022-12-25 15:30:00"
```
注意，设置包含空格的完整时间时，需要用引号将其引为一个整体。

### 系统时钟与硬件时钟

Linux中有两个时钟：
*   **系统时钟**：由Linux内核维护，用`date`命令查看。
*   **硬件时钟**：即BIOS时间，用`hwclock`或`clock`命令查看。

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_147.png)

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_149.png)

可以使用`hwclock`命令同步两者：
```bash
hwclock -w  # 将硬件时钟设置为与系统时钟相同
hwclock -s  # 将系统时钟设置为与硬件时钟相同
```
在实际生产环境中，服务器通常配置为与统一的NTP时间服务器同步，以确保集群内所有机器时间一致，而非手动设置。

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_151.png)

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_153.png)

### 查看日历

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_155.png)

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_157.png)

`cal`命令可以查看日历。
```bash
cal          # 查看当月日历
cal 2022     # 查看2022年全年日历
```

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_159.png)

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_161.png)

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_163.png)

---

## 总结 🎯

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_165.png)

本节课我们一起学习了Linux运维中的几个实用主题：
1.  **修改网卡IP**：掌握了通过直接编辑配置文件和使用`nmcli`命令两种方式。
2.  **域名解析**：了解了使用`host`和`nslookup`命令进行基本的DNS查询和诊断。
3.  **命令别名**：学会了使用`alias`为常用命令创建快捷方式，并区分了临时与永久生效的方法。
4.  **历史命令**：熟悉了`history`命令的查看、管理以及快速调用历史命令的技巧。
5.  **日期时间**：掌握了使用`date`命令查看和设置系统时间，并理解了系统时钟与硬件时钟的区别。

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_167.png)

![](img/f3e08437ee3181e0d1f5e9afdcb9c1e9_169.png)

这些命令和概念是Linux系统管理的基础组成部分，虽然部分命令使用频率不高，但它们是构建完整知识体系和应对特定场景（如认证考试、故障排查）所必需的。