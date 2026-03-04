# Linux运维全套培训课程：P50：Shell四剑客之awk数据库过滤 🔍

![](img/fd9870ceb09a8605d87847e2fdb240d7_0.png)

![](img/fd9870ceb09a8605d87847e2fdb240d7_2.png)

![](img/fd9870ceb09a8605d87847e2fdb240d7_4.png)

在本节课中，我们将深入学习AWK命令的进阶用法，特别是如何利用`BEGIN`和`END`模式、结合正则表达式进行复杂的数据过滤，以及如何将AWK应用于实际运维场景，如监控系统资源。

![](img/fd9870ceb09a8605d87847e2fdb240d7_6.png)

---

## 使用BEGIN和END模式

![](img/fd9870ceb09a8605d87847e2fdb240d7_8.png)

![](img/fd9870ceb09a8605d87847e2fdb240d7_10.png)

![](img/fd9870ceb09a8605d87847e2fdb240d7_12.png)

上一节我们介绍了AWK的基本过滤功能。本节中，我们来看看如何利用`BEGIN`和`END`模式在数据处理前后执行特定命令。

![](img/fd9870ceb09a8605d87847e2fdb240d7_14.png)

![](img/fd9870ceb09a8605d87847e2fdb240d7_16.png)

![](img/fd9870ceb09a8605d87847e2fdb240d7_18.png)

![](img/fd9870ceb09a8605d87847e2fdb240d7_20.png)

`BEGIN`模式下的命令会在读取任何输入行之前执行一次，通常用于打印表头或初始化变量。`END`模式下的命令则会在所有输入行处理完毕后执行一次，常用于输出汇总信息。

以下是使用`BEGIN`模式输出自定义表头的示例：
```bash
awk 'BEGIN{print "用户名\t\t解释器"} /^main/{print $1, $7}' /etc/passwd
```
在这个命令中，`BEGIN`块先打印了“用户名”和“解释器”作为表头。注意，自定义的文本（如中文）和制表符`\t`需要放在引号内，否则AWK无法识别。

AWK命令对空格的要求并不严格，但为了代码清晰，建议在关键字和参数之间添加空格。

![](img/fd9870ceb09a8605d87847e2fdb240d7_22.png)

---

## AWK的完整命令结构

![](img/fd9870ceb09a8605d87847e2fdb240d7_24.png)

![](img/fd9870ceb09a8605d87847e2fdb240d7_26.png)

![](img/fd9870ceb09a8605d87847e2fdb240d7_28.png)

![](img/fd9870ceb09a8605d87847e2fdb240d7_30.png)

一个完整的AWK命令可以包含`BEGIN`、主体处理和`END`三个部分。

![](img/fd9870ceb09a8605d87847e2fdb240d7_32.png)

![](img/fd9870ceb09a8605d87847e2fdb240d7_34.png)

![](img/fd9870ceb09a8605d87847e2fdb240d7_36.png)

以下是AWK命令的完整结构示例：
```bash
awk 'BEGIN{前置命令} /正则表达式/{主体命令} END{结束命令}' 文件名
```
*   `BEGIN{前置命令}`：在处理文件前执行一次。
*   `/正则表达式/{主体命令}`：对文件中匹配正则表达式的每一行执行主体命令。
*   `END{结束命令}`：在处理完所有行后执行一次。

![](img/fd9870ceb09a8605d87847e2fdb240d7_38.png)

![](img/fd9870ceb09a8605d87847e2fdb240d7_40.png)

![](img/fd9870ceb09a8605d87847e2fdb240d7_42.png)

![](img/fd9870ceb09a8605d87847e2fdb240d7_44.png)

![](img/fd9870ceb09a8605d87847e2fdb240d7_46.png)

![](img/fd9870ceb09a8605d87847e2fdb240d7_48.png)

例如，下面的命令统计`/etc/passwd`文件中以“main”开头的行数：
```bash
awk 'BEGIN{print "开始处理..."} /^main/{print $1,$7} END{print "总行数:", NR}' /etc/passwd
```
AWK还支持在`BEGIN`或`END`块中进行简单的数学运算，如`print 10+10`或`print 5*2`。

![](img/fd9870ceb09a8605d87847e2fdb240d7_50.png)

![](img/fd9870ceb09a8605d87847e2fdb240d7_52.png)

![](img/fd9870ceb09a8605d87847e2fdb240d7_54.png)

![](img/fd9870ceb09a8605d87847e2fdb240d7_56.png)

![](img/fd9870ceb09a8605d87847e2fdb240d7_58.png)

---

![](img/fd9870ceb09a8605d87847e2fdb240d7_60.png)

![](img/fd9870ceb09a8605d87847e2fdb240d7_62.png)

![](img/fd9870ceb09a8605d87847e2fdb240d7_64.png)

![](img/fd9870ceb09a8605d87847e2fdb240d7_66.png)

![](img/fd9870ceb09a8605d87847e2fdb240d7_68.png)

![](img/fd9870ceb09a8605d87847e2fdb240d7_70.png)

## 实际应用：过滤系统信息

AWK的强大之处在于其灵活的数据提取能力，可以完成`grep`无法轻易实现的复杂过滤。

![](img/fd9870ceb09a8605d87847e2fdb240d7_72.png)

以下是使用AWK过滤系统内存信息的示例：
```bash
free -h | awk '/Mem:/ {print "剩余内存:", $4}'
```
此命令先使用`free -h`查看内存，然后通过管道`|`将结果传给AWK。AWK匹配包含“Mem:”的行，并打印出该行的第四列，即剩余内存量。这种方式比单纯使用`grep`更精确、输出更整洁。

![](img/fd9870ceb09a8605d87847e2fdb240d7_74.png)

![](img/fd9870ceb09a8605d87847e2fdb240d7_76.png)

同样，我们可以过滤网卡的流量信息：
```bash
ifconfig ens33 | awk '/RX packets/ {print "入口流量:", $6, $7}'
```
此命令匹配包含“RX packets”的行，并打印第六和第七列，显示入口流量数据。如果输出中包含不需要的括号，可以尝试通过指定分隔符来去除，例如使用`-F "[()]"`，但这需要根据具体输出格式进行调整。

![](img/fd9870ceb09a8605d87847e2fdb240d7_78.png)

---

## 结合脚本与循环

AWK可以轻松嵌入Shell脚本，结合循环和条件判断，实现自动化监控。

以下是一个监控网卡流量的简单脚本示例：
```bash
#!/bin/bash
while true
do
    ip_addr=`ifconfig ens33 | awk '/inet / {print $2}'`
    rx_flow=`ifconfig ens33 | awk '/RX packets/ {print $6 $7}'`
    tx_flow=`ifconfig ens33 | awk '/TX packets/ {print $6 $7}'`
    echo "IP地址: $ip_addr"
    echo "入口流量: $rx_flow"
    echo "出口流量: $tx_flow"
    sleep 1
    clear
done
```
脚本说明：
*   `while true`：创建一个无限循环。
*   反引号 `` ` ` ``：捕获命令执行的结果并赋值给变量。
*   `sleep 1`：让循环每秒执行一次，避免消耗过多CPU资源。
*   `clear`：清屏，使输出更清晰。

对于磁盘监控，可以结合`df`命令和条件判断：
```bash
#!/bin/bash
while true
do
    # 获取根分区剩余空间（去掉‘G’单位以便进行数值比较）
    space_left=`df -h / | awk 'END{print $4}' | tr -d 'G'`
    # 判断剩余空间是否小于5GB
    if [ $space_left -lt 5 ]
    then
        echo "警告：根分区空间不足，剩余 ${space_left}GB！"
    fi
    sleep 60 # 每分钟检查一次
done
```
**注意**：从`df -h`获取的值带有“G”等单位，直接进行数值比较 (`-lt`) 会出错。可以使用`tr -d 'G'`命令删除字母“G”，或者使用字符串比较 (`=`)。

若希望脚本在开机时自动运行，可以将执行该脚本的命令添加到`/etc/rc.d/rc.local`文件中，并确保该文件具有可执行权限 (`chmod +x /etc/rc.d/rc.local`)。

![](img/fd9870ceb09a8605d87847e2fdb240d7_80.png)

---

![](img/fd9870ceb09a8605d87847e2fdb240d7_82.png)

![](img/fd9870ceb09a8605d87847e2fdb240d7_84.png)

本节课中我们一起学习了AWK的`BEGIN`和`END`模式、完整的命令结构，以及如何将其应用于过滤系统内存、网络流量等实际运维场景。我们还探讨了如何将AWK命令嵌入Shell脚本，结合循环和条件判断，构建简单的自动化监控工具。AWK功能强大，但初期重点在于理解其工作原理并看懂现有脚本，实践中可根据需求灵活调整。