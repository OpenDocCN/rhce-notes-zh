# Linux运维培训教程：P13：修改网卡地址、host、nslookup、alias、history、date命令管理 📚

![](img/0094af9c866ab3a976e137ffc050ce1d_1.png)

![](img/0094af9c866ab3a976e137ffc050ce1d_3.png)

![](img/0094af9c866ab3a976e137ffc050ce1d_5.png)

![](img/0094af9c866ab3a976e137ffc050ce1d_7.png)

在本节课中，我们将学习一系列实用的Linux系统管理命令。我们将了解如何修改网卡IP地址、使用域名解析工具、管理命令别名和历史记录，以及查看和设置系统时间。这些技能是日常系统运维工作的基础。

![](img/0094af9c866ab3a976e137ffc050ce1d_9.png)

![](img/0094af9c866ab3a976e137ffc050ce1d_11.png)

![](img/0094af9c866ab3a976e137ffc050ce1d_13.png)

## 修改网卡IP地址 🌐

![](img/0094af9c866ab3a976e137ffc050ce1d_15.png)

![](img/0094af9c866ab3a976e137ffc050ce1d_17.png)

![](img/0094af9c866ab3a976e137ffc050ce1d_19.png)

上一节我们学习了使用VIM编辑器，本节中我们来看看如何利用它来修改网络配置。

![](img/0094af9c866ab3a976e137ffc050ce1d_21.png)

![](img/0094af9c866ab3a976e137ffc050ce1d_23.png)

![](img/0094af9c866ab3a976e137ffc050ce1d_24.png)

![](img/0094af9c866ab3a976e137ffc050ce1d_25.png)

![](img/0094af9c866ab3a976e137ffc050ce1d_27.png)

![](img/0094af9c866ab3a976e137ffc050ce1d_29.png)

网卡的配置文件位于 `/etc/sysconfig/network-scripts/` 目录下，文件名通常为 `ifcfg-网卡名`（例如 `ifcfg-ens32`）。

![](img/0094af9c866ab3a976e137ffc050ce1d_31.png)

以下是修改网卡IP地址的步骤：
1.  使用VIM打开网卡配置文件：`vim /etc/sysconfig/network-scripts/ifcfg-ens32`
2.  找到 `IPADDR` 这一行，修改其后的IP地址。
3.  保存并退出VIM编辑器（按 `ESC` 键，然后输入 `:wq!`）。
4.  重启网络服务使配置生效：`systemctl restart network`

![](img/0094af9c866ab3a976e137ffc050ce1d_33.png)

![](img/0094af9c866ab3a976e137ffc050ce1d_35.png)

![](img/0094af9c866ab3a976e137ffc050ce1d_37.png)

![](img/0094af9c866ab3a976e137ffc050ce1d_39.png)

**注意**：重启网络后，如果IP地址改变，当前的SSH连接可能会断开，需要使用新的IP地址重新连接。

![](img/0094af9c866ab3a976e137ffc050ce1d_41.png)

![](img/0094af9c866ab3a976e137ffc050ce1d_43.png)

![](img/0094af9c866ab3a976e137ffc050ce1d_45.png)

![](img/0094af9c866ab3a976e137ffc050ce1d_47.png)

![](img/0094af9c866ab3a976e137ffc050ce1d_49.png)

除了直接修改配置文件，还可以使用 `nmcli` 命令修改IP地址，但该命令较长，主要用于红帽认证考试。其基本格式如下：
```bash
nmcli connection modify ens32 ipv4.method manual ipv4.addresses 192.168.0.90/24 connection.autoconnect yes
```
修改后，需要激活网卡：`nmcli connection up ens32`

![](img/0094af9c866ab3a976e137ffc050ce1d_51.png)

![](img/0094af9c866ab3a976e137ffc050ce1d_53.png)

![](img/0094af9c866ab3a976e137ffc050ce1d_55.png)

## 域名解析命令 🔍

![](img/0094af9c866ab3a976e137ffc050ce1d_57.png)

![](img/0094af9c866ab3a976e137ffc050ce1d_59.png)

![](img/0094af9c866ab3a976e137ffc050ce1d_61.png)

![](img/0094af9c866ab3a976e137ffc050ce1d_63.png)

了解完网络配置，我们来看看两个用于诊断网络问题的域名解析命令。

![](img/0094af9c866ab3a976e137ffc050ce1d_65.png)

![](img/0094af9c866ab3a976e137ffc050ce1d_67.png)

### host命令

![](img/0094af9c866ab3a976e137ffc050ce1d_69.png)

`host` 命令用于将域名解析到对应的IP地址。
```bash
host www.baidu.com
```
执行该命令会显示百度域名对应的IP地址信息。

![](img/0094af9c866ab3a976e137ffc050ce1d_71.png)

### nslookup命令

`nslookup` 命令用于查询域名解析是否正常，常用来诊断DNS服务器问题。
```bash
nslookup www.baidu.com
```
如果命令能返回域名服务器地址和解析出的IP，则说明DNS工作正常；如果返回超时或错误，则可能是DNS配置有问题。

## 命令别名管理 🏷️

![](img/0094af9c866ab3a976e137ffc050ce1d_73.png)

![](img/0094af9c866ab3a976e137ffc050ce1d_75.png)

![](img/0094af9c866ab3a976e137ffc050ce1d_77.png)

![](img/0094af9c866ab3a976e137ffc050ce1d_79.png)

接下来，我们学习如何为复杂的命令设置一个简短的别名，以提高工作效率。

![](img/0094af9c866ab3a976e137ffc050ce1d_81.png)

![](img/0094af9c866ab3a976e137ffc050ce1d_83.png)

`alias` 命令可以为命令设置别名（外号）。定义格式为 `alias 别名='命令'`，等号两边不能有空格。

![](img/0094af9c866ab3a976e137ffc050ce1d_85.png)

![](img/0094af9c866ab3a976e137ffc050ce1d_87.png)

![](img/0094af9c866ab3a976e137ffc050ce1d_89.png)

![](img/0094af9c866ab3a976e137ffc050ce1d_91.png)

以下是定义和使用别名的方法：
1.  查看当前已定义的别名：`alias`
2.  定义一个新别名（例如，让 `lh` 代表 `ls -lh`）：`alias lh='ls -lh'`
3.  使用别名：输入 `lh` 即可执行 `ls -lh`。

![](img/0094af9c866ab3a976e137ffc050ce1d_93.png)

![](img/0094af9c866ab3a976e137ffc050ce1d_95.png)

![](img/0094af9c866ab3a976e137ffc050ce1d_97.png)

**注意**：这样定义的别名是临时的，终端关闭后失效。若需永久生效，需要将别名定义写入用户家目录下的 `~/.bashrc` 文件中。

![](img/0094af9c866ab3a976e137ffc050ce1d_99.png)

![](img/0094af9c866ab3a976e137ffc050ce1d_101.png)

![](img/0094af9c866ab3a976e137ffc050ce1d_103.png)

![](img/0094af9c866ab3a976e137ffc050ce1d_105.png)

## 历史命令管理 ⏳

![](img/0094af9c866ab3a976e137ffc050ce1d_107.png)

![](img/0094af9c866ab3a976e137ffc050ce1d_109.png)

![](img/0094af9c866ab3a976e137ffc050ce1d_110.png)

在系统中执行的命令会被记录下来，方便我们回顾和再次调用。

![](img/0094af9c866ab3a976e137ffc050ce1d_112.png)

![](img/0094af9c866ab3a976e137ffc050ce1d_114.png)

`history` 命令用于显示之前执行过的命令列表。
```bash
history
```
历史命令默认存储在 `~/.bash_history` 文件中，系统登录时读取到内存，退出时同步更新到文件。

![](img/0094af9c866ab3a976e137ffc050ce1d_116.png)

![](img/0094af9c866ab3a976e137ffc050ce1d_118.png)

以下是管理历史命令的常用操作：
*   `history -c`：清空当前终端的历史命令记录（内存中）。
*   `history -d 编号`：删除历史记录中指定编号的命令。
*   `history -a`：立即将当前终端新执行的命令追加到历史文件中。

![](img/0094af9c866ab3a976e137ffc050ce1d_120.png)

![](img/0094af9c866ab3a976e137ffc050ce1d_122.png)

![](img/0094af9c866ab3a976e137ffc050ce1d_124.png)

![](img/0094af9c866ab3a976e137ffc050ce1d_126.png)

调用历史命令的快捷方式：
*   `!!`：执行上一条命令。
*   `!字符串`：执行最近一条以该字符串开头的命令。
*   `!编号`：执行历史记录中对应编号的命令。

![](img/0094af9c866ab3a976e137ffc050ce1d_128.png)

![](img/0094af9c866ab3a976e137ffc050ce1d_130.png)

![](img/0094af9c866ab3a976e137ffc050ce1d_132.png)

![](img/0094af9c866ab3a976e137ffc050ce1d_134.png)

![](img/0094af9c866ab3a976e137ffc050ce1d_136.png)

历史命令的存储条数可以在 `/etc/profile` 文件中通过 `HISTSIZE` 变量修改。

![](img/0094af9c866ab3a976e137ffc050ce1d_138.png)

![](img/0094af9c866ab3a976e137ffc050ce1d_140.png)

## 日期时间管理 ⏰

![](img/0094af9c866ab3a976e137ffc050ce1d_142.png)

![](img/0094af9c866ab3a976e137ffc050ce1d_144.png)

![](img/0094af9c866ab3a976e137ffc050ce1d_146.png)

![](img/0094af9c866ab3a976e137ffc050ce1d_147.png)

![](img/0094af9c866ab3a976e137ffc050ce1d_149.png)

![](img/0094af9c866ab3a976e137ffc050ce1d_151.png)

最后，我们来学习如何查看和设置系统的日期与时间。

`date` 命令用于查看和设置系统时间。
*   查看当前完整时间：`date`
*   查看特定格式时间（例如只显示年月日）：`date +%F` 或 `date +%Y-%m-%d`

设置系统时间的格式为 `date -s "时间字符串"`。例如：
```bash
date -s "2022-11-25 15:30:00"
```
**注意**：修改时间时，如果字符串中包含空格，需要使用引号将其引为一个整体。

Linux系统中有系统时间和硬件时间两种时钟，可以使用 `hwclock` 命令查看硬件时间或进行同步：
*   `hwclock`：查看硬件时间。
*   `hwclock -w`：将硬件时间设置为与系统时间相同。
*   `hwclock -s`：将系统时间设置为与硬件时间相同。

![](img/0094af9c866ab3a976e137ffc050ce1d_153.png)

![](img/0094af9c866ab3a976e137ffc050ce1d_155.png)

![](img/0094af9c866ab3a976e137ffc050ce1d_157.png)

![](img/0094af9c866ab3a976e137ffc050ce1d_159.png)

在实际生产环境中，通常使用NTP服务来同步网络时间，以保证集群内服务器时间一致。

![](img/0094af9c866ab3a976e137ffc050ce1d_161.png)

![](img/0094af9c866ab3a976e137ffc050ce1d_163.png)

![](img/0094af9c866ab3a976e137ffc050ce1d_165.png)

![](img/0094af9c866ab3a976e137ffc050ce1d_167.png)

![](img/0094af9c866ab3a976e137ffc050ce1d_169.png)

此外，`cal` 命令可以用于查看日历：
```bash
cal
cal 2022
```

## 总结 📝

![](img/0094af9c866ab3a976e137ffc050ce1d_171.png)

![](img/0094af9c866ab3a976e137ffc050ce1d_173.png)

![](img/0094af9c866ab3a976e137ffc050ce1d_175.png)

本节课中我们一起学习了多个基础但重要的系统管理命令。我们掌握了通过修改配置文件或使用 `nmcli` 命令来设置网卡IP地址；了解了 `host` 和 `nslookup` 这两个域名解析工具的基本用法；学会了使用 `alias` 为命令创建别名来提升操作效率；探讨了如何使用 `history` 管理和调用命令历史记录；最后，学习了使用 `date`、`hwclock` 和 `cal` 命令来查看和管理系统时间与日历。这些命令是Linux系统日常运维的必备工具，需要多加练习以熟练运用。