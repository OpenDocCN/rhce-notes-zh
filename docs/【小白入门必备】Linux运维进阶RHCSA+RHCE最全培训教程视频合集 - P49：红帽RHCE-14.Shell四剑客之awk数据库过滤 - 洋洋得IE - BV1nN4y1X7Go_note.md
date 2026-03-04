# Linux运维进阶：P49：Shell四剑客之awk数据库过滤 🔍

![](img/82edcafb6c9bd70842e9dc7baadc03b0_0.png)

![](img/82edcafb6c9bd70842e9dc7baadc03b0_2.png)

在本节课中，我们将深入学习AWK工具，它是Shell“四剑客”中功能强大的文本处理和数据分析工具。我们将重点讲解AWK的`BEGIN`和`END`模式、结合系统命令进行数据过滤，以及如何编写简单的监控脚本。

![](img/82edcafb6c9bd70842e9dc7baadc03b0_4.png)

![](img/82edcafb6c9bd70842e9dc7baadc03b0_6.png)

## 使用BEGIN和END模式

上一节我们介绍了AWK的基本过滤和打印功能。本节中我们来看看AWK的`BEGIN`和`END`模式，它们允许我们在处理数据之前和之后执行特定的命令。

`BEGIN`模式中的命令会在读取任何输入行之前执行一次，通常用于初始化或打印表头。`END`模式中的命令则会在所有输入行处理完毕后执行一次，常用于输出汇总信息。

![](img/82edcafb6c9bd70842e9dc7baadc03b0_8.png)

以下是AWK命令的完整结构示例：
```bash
awk 'BEGIN {前置命令} /正则表达式/ {中间命令} END {后置命令}' 文件名
```
在这个结构中：
*   `BEGIN {前置命令}`：在处理文件前执行一次。
*   `/正则表达式/ {中间命令}`：对文件中匹配正则表达式的每一行执行中间命令。
*   `END {后置命令}`：在处理完所有行后执行一次。

![](img/82edcafb6c9bd70842e9dc7baadc03b0_10.png)

![](img/82edcafb6c9bd70842e9dc7baadc03b0_12.png)

![](img/82edcafb6c9bd70842e9dc7baadc03b0_14.png)

![](img/82edcafb6c9bd70842e9dc7baadc03b0_16.png)

例如，我们想在打印`/etc/passwd`文件中以`mail`开头的行的用户名和解释器之前，先输出一个表头：
```bash
awk 'BEGIN {print "用户名\t\t解释器"} /^mail/ {print $1, $7}' /etc/passwd
```
**注意**：像`print “用户名\t\t解释器”`中的中文和制表符`\t`需要放在引号内，否则AWK无法识别。

![](img/82edcafb6c9bd70842e9dc7baadc03b0_18.png)

![](img/82edcafb6c9bd70842e9dc7baadc03b0_20.png)

![](img/82edcafb6c9bd70842e9dc7baadc03b0_22.png)

我们还可以在`END`部分添加命令，例如输出文件的总行数：
```bash
awk 'BEGIN {print "用户名\t\t解释器"} /^mail/ {print $1, $7} END {print “总行数：”, NR}' /etc/passwd
```
AWK对空格的要求不严格，但为了代码清晰，建议在`print`和变量之间加上空格。

此外，在AWK中可以直接进行数学运算：
```bash
awk 'BEGIN {print 10+10, 5*2, 10/2, 10%3}'
```
AWK本身是一门编程语言，支持**if判断**、**循环**和**数组**等复杂操作。但对于日常运维中的文本处理，我们通常用不到这些高级功能，了解其具备这些能力即可。

## 结合系统命令进行过滤

![](img/82edcafb6c9bd70842e9dc7baadc03b0_24.png)

AWK的强大之处在于它能轻松处理其他命令（如`grep`）难以直接处理的格式化输出。我们来看看如何结合系统命令进行数据过滤。

### 过滤内存信息

![](img/82edcafb6c9bd70842e9dc7baadc03b0_26.png)

如果我们想用`free -h`查看内存，但只关心“可用内存”这一项，使用`grep`会显示整行，不够精确。

![](img/82edcafb6c9bd70842e9dc7baadc03b0_28.png)

![](img/82edcafb6c9bd70842e9dc7baadc03b0_30.png)

![](img/82edcafb6c9bd70842e9dc7baadc03b0_32.png)

这时可以使用AWK：
```bash
free -h | awk '/Mem:/ {print $4}'
```
这条命令先匹配包含`Mem:`的行，然后打印该行的第4列，即可用内存。

![](img/82edcafb6c9bd70842e9dc7baadc03b0_34.png)

![](img/82edcafb6c9bd70842e9dc7baadc03b0_36.png)

我们还可以在输出前加上说明：
```bash
free -h | awk 'BEGIN {print "可用内存"} /Mem:/ {print $4}'
```

![](img/82edcafb6c9bd70842e9dc7baadc03b0_38.png)

![](img/82edcafb6c9bd70842e9dc7baadc03b0_40.png)

![](img/82edcafb6c9bd70842e9dc7baadc03b0_42.png)

![](img/82edcafb6c9bd70842e9dc7baadc03b0_44.png)

### 过滤网络接口信息

![](img/82edcafb6c9bd70842e9dc7baadc03b0_46.png)

![](img/82edcafb6c9bd70842e9dc7baadc03b0_48.png)

![](img/82edcafb6c9bd70842e9dc7baadc03b0_50.png)

![](img/82edcafb6c9bd70842e9dc7baadc03b0_52.png)

![](img/82edcafb6c9bd70842e9dc7baadc03b0_54.png)

查看网卡`ens33`的入口流量（RX bytes）信息，`grep`会输出整行。

![](img/82edcafb6c9bd70842e9dc7baadc03b0_56.png)

![](img/82edcafb6c9bd70842e9dc7baadc03b0_58.png)

![](img/82edcafb6c9bd70842e9dc7baadc03b0_60.png)

![](img/82edcafb6c9bd70842e9dc7baadc03b0_62.png)

使用AWK可以更精确：
```bash
ifconfig ens33 | awk '/RX packets/ {print $6,$7}'
```
这条命令匹配包含`RX packets`的行，并打印第6和第7列。

![](img/82edcafb6c9bd70842e9dc7baadc03b0_64.png)

![](img/82edcafb6c9bd70842e9dc7baadc03b0_66.png)

![](img/82edcafb6c9bd70842e9dc7baadc03b0_68.png)

![](img/82edcafb6c9bd70842e9dc7baadc03b0_70.png)

如果想在输出时去掉流量数字后面的括号，可以尝试指定分隔符。例如，尝试以`(`或`)`作为分隔符：
```bash
ifconfig ens33 | awk -F ‘[()]‘ '/RX packets/ {print $2}'
```
这里`-F ‘[()]‘`表示以左括号`(`或右括号`)`作为字段分隔符。但实际应用时可能需要根据输出格式调整策略，这涉及到更复杂的文本处理技巧。

![](img/82edcafb6c9bd70842e9dc7baadc03b0_72.png)

## 编写简单的监控脚本

![](img/82edcafb6c9bd70842e9dc7baadc03b0_74.png)

我们可以将AWK命令嵌入Shell脚本，实现简单的系统监控功能。

![](img/82edcafb6c9bd70842e9dc7baadc03b0_76.png)

以下是一个监控网卡流量和IP地址的脚本示例：
```bash
#!/bin/bash
while true
do
    clear
    ip=`ifconfig ens33 | awk '/inet / {print $2}'`
    rx=`ifconfig ens33 | awk '/RX packets/ {print $6,$7}'`
    tx=`ifconfig ens33 | awk '/TX packets/ {print $6,$7}'`
    echo “IP地址：$ip”
    echo “入口流量：$rx”
    echo “出口流量：$tx”
    sleep 1
done
```
**脚本解析**：
*   `while true`：创建一个无限循环。
*   `clear`：每次循环前清屏，使输出更清晰。
*   使用反引号`` ` ``或`$()`执行命令并将结果赋值给变量。
    *   `ip`：获取`ens33`网卡的IP地址（`/etc/passwd`文件中`inet`后的第二列）。
    *   `rx`：获取接收流量。
    *   `tx`：获取发送流量。
*   `sleep 1`：让循环每秒执行一次，避免消耗过多CPU资源。

![](img/82edcafb6c9bd70842e9dc7baadc03b0_78.png)

**注意**：在过滤IP地址时，正则表达式`/inet /`里`inet`后有一个空格，这是为了精确匹配，避免匹配到`inet6`。

![](img/82edcafb6c9bd70842e9dc7baadc03b0_80.png)

### 监控磁盘空间并告警

我们还可以编写脚本监控根分区磁盘使用情况。

首先，获取根分区的可用空间（以G为单位）：
```bash
df -h | awk ‘$NF==“/” {print $4}’
```
`$NF==“/”`表示当最后一列等于根目录`/`时，打印第4列（可用空间）。

如果想进行数值判断（例如可用空间小于5G时告警），需要处理掉单位“G”。可以指定“G”为分隔符，只取数字部分：
```bash
df -h | awk ‘$NF==“/” {print $4}’ | awk -F‘G’ ‘{print $1}’
```
然后，可以将这个命令放入一个带有条件判断的循环脚本中。不过，在实际生产环境中，通常会使用专业的监控软件（如Zabbix、Prometheus）来完成这类任务，我们只需要能读懂和改编这类脚本即可。

### 设置开机自启脚本

如果希望脚本在系统启动时自动运行，可以将执行命令添加到`/etc/rc.d/rc.local`文件中。

**步骤如下**：
1.  确保`/etc/rc.d/rc.local`文件本身有执行权限：
    ```bash
    chmod +x /etc/rc.d/rc.local
    ```
2.  将你的脚本执行命令（建议使用绝对路径）添加到该文件末尾。例如：
    ```bash
    /bin/bash /root/monitor.sh &
    ```
3.  同时，记得给你自己编写的脚本`monitor.sh`也加上执行权限。

![](img/82edcafb6c9bd70842e9dc7baadc03b0_82.png)

这样，系统启动时就会自动执行`/etc/rc.d/rc.local`文件中的命令，从而运行你的监控脚本。

---

![](img/82edcafb6c9bd70842e9dc7baadc03b0_84.png)

![](img/82edcafb6c9bd70842e9dc7baadc03b0_86.png)

本节课中我们一起学习了AWK的`BEGIN`和`END`模式，掌握了如何将AWK与系统命令结合进行精准的数据过滤，例如提取内存、网络流量和磁盘空间信息。我们还初步了解了如何将这些AWK命令嵌入Shell脚本，构建简单的系统监控逻辑，并学习了如何让脚本开机自动运行。AWK是文本处理和数据提取的利器，熟练掌握它能极大提升运维工作效率。