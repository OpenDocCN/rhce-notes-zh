# Linux运维：P49：Shell四剑客之awk高级过滤

![](img/f8152be81f48b1adc08d8ceb76d0f8da_0.png)

![](img/f8152be81f48b1adc08d8ceb76d0f8da_2.png)

在本节课中，我们将深入学习AWK命令的高级用法，包括其完整的语法结构、内置变量、以及如何结合正则表达式和管道进行复杂的数据过滤。我们将通过多个实际案例，如过滤系统内存、网卡流量和磁盘分区信息，来掌握AWK在运维工作中的强大数据处理能力。

![](img/f8152be81f48b1adc08d8ceb76d0f8da_4.png)

![](img/f8152be81f48b1adc08d8ceb76d0f8da_6.png)

## AWK命令的完整结构

上一节我们介绍了AWK的基本过滤功能，本节中我们来看看AWK命令的完整语法结构。一个完整的AWK命令可以包含三个部分：`BEGIN`、主体命令块和`END`。

![](img/f8152be81f48b1adc08d8ceb76d0f8da_8.png)

**基本语法格式**：
```bash
awk 'BEGIN{前置命令} /模式/{主体命令} END{结束命令}' 文件名
```
*   `BEGIN{}`：在处理文件**之前**执行一次，常用于打印表头或初始化变量。
*   `主体命令`：对文件中**每一行**匹配指定模式的行执行的操作。
*   `END{}`：在处理完文件**所有行之后**执行一次，常用于打印汇总信息。

以下是各部分命令的执行顺序和作用：
1.  **BEGIN块命令**：首先执行，且只执行一次。
2.  **主体命令**：然后逐行读取文件，对匹配模式的行执行相应命令。
3.  **END块命令**：最后执行，且只执行一次。

让我们通过一个例子来理解。假设我们想处理`/etc/passwd`文件，先输出一个标题，再打印以`root`开头的行的用户名和解释器，最后输出总行数。

![](img/f8152be81f48b1adc08d8ceb76d0f8da_10.png)

**示例命令**：
```bash
awk 'BEGIN{print "用户名\t\t解释器"} /^root/{print $1, $7} END{print "总行数:", NR}' /etc/passwd
```
*   `BEGIN{print "用户名\t\t解释器"}`：在开始处理文件前，打印“用户名”和“解释器”作为标题，`\t`表示制表符用于对齐。
*   `/^root/{print $1, $7}`：对于以`root`开头的行，打印其第1列（用户名）和第7列（解释器）。
*   `END{print "总行数:", NR}`：处理完所有行后，打印内置变量`NR`的值，它代表了已读的总行数。

**注意**：AWK命令中，`print`等内置函数与其参数之间的空格要求不严格，但为了代码清晰，建议加上空格。另外，非AWK内置的字符（如中文、制表符`\t`）通常需要用引号括起来。

---

## 使用AWK进行复杂数据提取

![](img/f8152be81f48b1adc08d8ceb76d0f8da_12.png)

了解了AWK的完整结构后，我们来看看它在实际运维场景中如何替代`grep`进行更精准的数据提取。

![](img/f8152be81f48b1adc08d8ceb76d0f8da_14.png)

![](img/f8152be81f48b1adc08d8ceb76d0f8da_16.png)

### 过滤系统内存信息

![](img/f8152be81f48b1adc08d8ceb76d0f8da_18.png)

![](img/f8152be81f48b1adc08d8ceb76d0f8da_20.png)

![](img/f8152be81f48b1adc08d8ceb76d0f8da_22.png)

如果我们想查看系统剩余内存，使用`grep`可能不够精确。而`awk`可以轻松地提取特定列的数据。

![](img/f8152be81f48b1adc08d8ceb76d0f8da_24.png)

![](img/f8152be81f48b1adc08d8ceb76d0f8da_26.png)

![](img/f8152be81f48b1adc08d8ceb76d0f8da_28.png)

![](img/f8152be81f48b1adc08d8ceb76d0f8da_30.png)

**目标**：从`free -h`命令输出中，仅提取“剩余内存”的数值。

![](img/f8152be81f48b1adc08d8ceb76d0f8da_32.png)

![](img/f8152be81f48b1adc08d8ceb76d0f8da_34.png)

![](img/f8152be81f48b1adc08d8ceb76d0f8da_36.png)

**实现命令**：
```bash
free -h | awk '/Mem:/ {print $4}'
```
*   `free -h`：查看内存使用情况（人类可读格式）。
*   `awk '/Mem:/ {print $4}'`：匹配包含`Mem:`的行，并打印该行的第4列，即剩余内存量。

![](img/f8152be81f48b1adc08d8ceb76d0f8da_38.png)

![](img/f8152be81f48b1adc08d8ceb76d0f8da_40.png)

这样就能清晰、直接地得到我们想要的数据，例如`2.0G`。

![](img/f8152be81f48b1adc08d8ceb76d0f8da_42.png)

![](img/f8152be81f48b1adc08d8ceb76d0f8da_44.png)

![](img/f8152be81f48b1adc08d8ceb76d0f8da_46.png)

### 过滤网卡流量信息

![](img/f8152be81f48b1adc08d8ceb76d0f8da_48.png)

![](img/f8152be81f48b1adc08d8ceb76d0f8da_50.png)

![](img/f8152be81f48b1adc08d8ceb76d0f8da_52.png)

监控网卡流量时，我们常常需要提取特定的数据字段，如入口(`RX`)和出口(`TX`)流量。

![](img/f8152be81f48b1adc08d8ceb76d0f8da_54.png)

![](img/f8152be81f48b1adc08d8ceb76d0f8da_56.png)

**目标**：从`ifconfig ens32`命令输出中，提取网卡`ens32`的入口流量数据。

**分析过程**：
1.  首先使用`ifconfig ens32`查看网卡信息。
2.  观察输出，找到包含入口流量数据的那一行（通常含有`RX packets`）。
3.  使用`awk`进行匹配和提取。

![](img/f8152be81f48b1adc08d8ceb76d0f8da_58.png)

**初步实现**：
```bash
ifconfig ens32 | awk '/RX packets/ {print $6, $7}'
```
这条命令会匹配包含`RX packets`的行，并打印第6和第7列，结果可能类似`123456 (1.2 MB)`。但括号和单位有时会影响后续处理。

![](img/f8152be81f48b1adc08d8ceb76d0f8da_60.png)

![](img/f8152be81f48b1adc08d8ceb76d0f8da_62.png)

**进阶处理（去除括号）**：
如果想更干净地获取纯数字，可以指定分隔符。例如，以空格和括号作为分隔符：
```bash
ifconfig ens32 | awk -F '[ ()]+' '/RX packets/ {print $6}'
```
*   `-F '[ ()]+'`：将空格、左括号`(`、右括号`)`中的一个或多个连续出现定义为字段分隔符。
*   `{print $6}`：打印按新分隔符划分后的第6列，即流量数值部分。

通过组合管道和多次`awk`处理，可以实现更复杂的数据清洗和格式化。

![](img/f8152be81f48b1adc08d8ceb76d0f8da_64.png)

---

## 实战：编写监控脚本

结合循环、条件判断和AWK，我们可以编写简单的监控脚本。

![](img/f8152be81f48b1adc08d8ceb76d0f8da_66.png)

### 监控根分区磁盘使用率

**目标**：编写一个脚本，监控根分区剩余空间，并在空间不足时告警。

**脚本思路**：
1.  使用`df -h`命令获取磁盘信息。
2.  用`awk`提取根分区（通常以`/`结尾）的剩余空间列。
3.  将提取的值存入变量。
4.  使用`if`条件判断，检查剩余空间是否小于阈值（例如5GB）。
5.  如果小于阈值，则打印告警信息。

**示例脚本 (`monitor_disk.sh`)**：
```bash
#!/bin/bash
# 这是一个磁盘监控示例脚本

while true; do
    # 提取根分区的可用空间（第4列），并使用`tr`删除‘G’单位以便进行数字比较
    available=$(df -h / | awk 'NR==2 {print $4}' | tr -d 'G')

    # 进行整数比较（注意：这里假设数据是整数G，且去掉了‘G’）
    if [ $available -lt 5 ]; then
        echo "警告：根分区剩余空间不足5G！当前剩余：${available}G"
        # 此处可以添加更实际的告警动作，如发送邮件
    fi

    # 每10秒检查一次
    sleep 10
done
```
**脚本说明**：
*   `df -h /`：查看根分区使用情况。
*   `awk 'NR==2 {print $4}'`：`NR==2`匹配第二行（通常是根分区的数据行），打印第4列（可用空间）。
*   `tr -d 'G'`：删除字符串中的字母`G`，将例如`“20G”`转换为`“20”`，以便进行整数比较。
*   `[ $available -lt 5 ]`：判断变量`available`的值是否小于5。
*   `while true; do ... done`：创建一个无限循环，持续监控。
*   `sleep 10`：每次循环暂停10秒，避免过度消耗CPU资源。

![](img/f8152be81f48b1adc08d8ceb76d0f8da_68.png)

![](img/f8152be81f48b1adc08d8ceb76d0f8da_70.png)

![](img/f8152be81f48b1adc08d8ceb76d0f8da_72.png)

![](img/f8152be81f48b1adc08d8ceb76d0f8da_74.png)

**如何让脚本开机自启**：
可以将执行该脚本的命令添加到系统启动脚本中。例如，在Red Hat系系统中，可以编辑`/etc/rc.d/rc.local`文件（需先赋予其执行权限`chmod +x /etc/rc.d/rc.local`），在文件末尾添加你的脚本的绝对路径：
```bash
/path/to/your/monitor_disk.sh &
```
**注意**：`&`符号表示在后台运行该脚本。

![](img/f8152be81f48b1adc08d8ceb76d0f8da_76.png)

![](img/f8152be81f48b1adc08d8ceb76d0f8da_78.png)

![](img/f8152be81f48b1adc08d8ceb76d0f8da_80.png)

---

![](img/f8152be81f48b1adc08d8ceb76d0f8da_82.png)

![](img/f8152be81f48b1adc08d8ceb76d0f8da_84.png)

## 总结

![](img/f8152be81f48b1adc08d8ceb76d0f8da_86.png)

本节课中我们一起学习了AWK命令的高级应用。
1.  我们首先剖析了AWK命令的完整结构，包括`BEGIN`、主体和`END`三个部分，理解了它们各自的执行时机和用途。
2.  接着，我们通过**过滤系统内存**和**网卡流量信息**的实例，掌握了如何利用AWK进行比`grep`更精确、更灵活的列数据提取，包括处理复杂的输出格式和使用自定义分隔符。
3.  最后，我们进行了一个实战演练，将AWK与Shell脚本的**循环**、**条件判断**结合，编写了一个简单的**磁盘空间监控脚本**，并了解了如何让脚本实现开机自动运行。

![](img/f8152be81f48b1adc08d8ceb76d0f8da_88.png)

![](img/f8152be81f48b1adc08d8ceb76d0f8da_90.png)

![](img/f8152be81f48b1adc08d8ceb76d0f8da_92.png)

![](img/f8152be81f48b1adc08d8ceb76d0f8da_94.png)

AWK作为一门强大的文本处理语言，其功能远不止于此，还支持数组、循环等复杂编程特性。但对于日常运维工作，熟练掌握其数据过滤、字段提取和格式化输出，已经能极大地提升工作效率。