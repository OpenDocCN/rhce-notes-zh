# Linux运维教程：P50：Shell四剑客之awk数据库过滤 🔍

![](img/d986e135b29cc5fe273ef31836f42fe5_0.png)

![](img/d986e135b29cc5fe273ef31836f42fe5_2.png)

在本节课中，我们将深入学习AWK工具的高级用法，特别是如何利用其`BEGIN`和`END`模式、结合正则表达式进行精确的数据过滤，以及如何在实际运维场景（如监控内存、网卡流量、磁盘空间）中应用AWK。我们将通过具体示例，让初学者也能掌握这些强大的文本处理技巧。

![](img/d986e135b29cc5fe273ef31836f42fe5_4.png)

---

![](img/d986e135b29cc5fe273ef31836f42fe5_6.png)

## 使用BEGIN和END模式

上一节我们介绍了AWK的基本过滤功能。本节中，我们来看看如何利用`BEGIN`和`END`模式在数据处理前后执行特定操作。

`BEGIN`模式下的命令会在读取任何输入行之前执行一次，通常用于初始化或打印表头。`END`模式下的命令则在所有输入行处理完毕后执行一次，常用于输出汇总信息。

以下是创建一个带有表头的用户信息输出的示例：

```bash
awk 'BEGIN{print "用户名\t\t解释器"} /^main/{print $1, $7}' /etc/passwd
```

![](img/d986e135b29cc5fe273ef31836f42fe5_8.png)

在这个命令中：
*   `BEGIN{print "用户名\t\t解释器"}` 会先打印出“用户名”和“解释器”作为标题，并用制表符`\t`对齐。
*   `/^main/{print $1, $7}` 会查找以“main”开头的行，并打印其第1列（用户名）和第7列（解释器）。

**注意**：AWK命令中，`print`语句与要打印的内容之间对空格要求不严格，但为了可读性，建议加上空格。非AWK内置的字符串（如中文字符、制表符`\t`）必须用引号括起来，否则AWK无法识别。

一个包含`BEGIN`、主体处理和`END`的完整示例如下：

![](img/d986e135b29cc5fe273ef31836f42fe5_10.png)

![](img/d986e135b29cc5fe273ef31836f42fe5_12.png)

```bash
awk 'BEGIN{print "开始处理..."} /^main/{print $1,$7} END{print “处理完毕，总行数：”, NR}' /etc/passwd
```

![](img/d986e135b29cc5fe273ef31836f42fe5_14.png)

AWK本身是一门功能完整的编程语言，支持变量、数组、`if`条件判断和循环。但在日常运维中，我们主要利用其进行高效的数据提取和格式化，复杂的逻辑判断通常交给Shell脚本或其他编程语言完成。

---

![](img/d986e135b29cc5fe273ef31836f42fe5_16.png)

## 结合管道进行数据过滤

![](img/d986e135b29cc5fe273ef31836f42fe5_18.png)

![](img/d986e135b29cc5fe273ef31836f42fe5_20.png)

![](img/d986e135b29cc5fe273ef31836f42fe5_22.png)

AWK的强大之处在于它能轻松处理`grep`等工具难以直接格式化的输出。我们通过几个实际场景来学习。

![](img/d986e135b29cc5fe273ef31836f42fe5_24.png)

### 监控系统内存

![](img/d986e135b29cc5fe273ef31836f42fe5_26.png)

![](img/d986e135b29cc5fe273ef31836f42fe5_28.png)

![](img/d986e135b29cc5fe273ef31836f42fe5_30.png)

如果我们只想查看系统剩余内存，使用`grep`会包含不需要的行。使用AWK可以精确提取。

![](img/d986e135b29cc5fe273ef31836f42fe5_32.png)

![](img/d986e135b29cc5fe273ef31836f42fe5_34.png)

![](img/d986e135b29cc5fe273ef31836f42fe5_36.png)

以下是使用AWK过滤并格式化输出剩余内存的示例：

![](img/d986e135b29cc5fe273ef31836f42fe5_38.png)

![](img/d986e135b29cc5fe273ef31836f42fe5_40.png)

![](img/d986e135b29cc5fe273ef31836f42fe5_42.png)

![](img/d986e135b29cc5fe273ef31836f42fe5_44.png)

```bash
free -h | awk '/Mem:/ {print “剩余内存：”, $4}'
```
这条命令先执行`free -h`查看内存，然后通过管道`|`将结果传给AWK。AWK找到包含“Mem:”的行，并打印出第4列（剩余内存），同时添加了“剩余内存：”的说明。

![](img/d986e135b29cc5fe273ef31836f42fe5_46.png)

![](img/d986e135b29cc5fe273ef31836f42fe5_48.png)

![](img/d986e135b29cc5fe273ef31836f42fe5_50.png)

### 监控网卡流量

![](img/d986e135b29cc5fe273ef31836f42fe5_52.png)

![](img/d986e135b29cc5fe273ef31836f42fe5_54.png)

![](img/d986e135b29cc5fe273ef31836f42fe5_56.png)

查看特定网卡（如ens32）的入口流量时，`ifconfig`的输出较为复杂。AWK可以精准定位所需数据。

以下是提取网卡ens32入口流量（RX bytes）的示例：

```bash
ifconfig ens32 | awk '/RX packets/ {print “入口流量：”, $6, $7}'
```
此命令匹配包含“RX packets”的行，并打印第6和第7列，即流量数值和单位。若想去除输出中的括号，可以指定更复杂的分隔符，例如：
```bash
ifconfig ens32 | awk -F ‘[ ()]+’ ‘/RX packets/ {print $4, $5}’
```
这里`-F ‘[ ()]+’`表示以空格、左括号或右括号中的一个或多个作为分隔符。

![](img/d986e135b29cc5fe273ef31836f42fe5_58.png)

### 监控磁盘空间

![](img/d986e135b29cc5fe273ef31836f42fe5_60.png)

![](img/d986e135b29cc5fe273ef31836f42fe5_62.png)

要监控根分区`/`的可用空间，并希望输出简洁，可以结合`df`和AWK。

以下是查看根分区可用空间的示例：

![](img/d986e135b29cc5fe273ef31836f42fe5_64.png)

```bash
df -h | awk ‘$NF==“/” {print “可用空间：”, $4}’
```
`$NF`代表最后一列，`$NF==“/”`匹配挂载点为根目录`/`的行，然后打印第4列（可用空间）。

更进一步，我们可以将上述命令嵌入一个简单的监控脚本，当可用空间低于阈值时发出警告：

```bash
#!/bin/bash
while true; do
    available=$(df -h | awk ‘$NF==“/” {print $4}’ | sed ‘s/G//‘) # 获取数值，移除“G”
    if [ $available -lt 5 ]; then # 假设阈值是5G
        echo “警告：根分区空间不足，剩余 ${available}G！”
    fi
    sleep 60 # 每分钟检查一次
done
```

**注意**：实际脚本中，从`df -h`获取的值带有“G”等单位，不能直接用于数值比较。可以使用`sed ‘s/G//‘`命令移除字母“G”，或者使用`awk`的`-F`选项以“G”为分隔符直接获取数字部分。

---

## 脚本开机自启

对于需要长期运行的后台监控脚本，我们可以将其设置为开机自动启动。

在Linux中，可以将启动命令添加到`/etc/rc.d/rc.local`文件中。请注意，必须确保`rc.local`文件本身具有可执行权限。

以下是设置开机自启的步骤：

![](img/d986e135b29cc5fe273ef31836f42fe5_66.png)

1.  赋予`rc.local`文件执行权限：
    ```bash
    chmod +x /etc/rc.d/rc.local
    ```
2.  编辑`/etc/rc.d/rc.local`文件，在文件末尾添加执行你的脚本的命令（请使用绝对路径）：
    ```bash
    /bin/bash /path/to/your/monitor_script.sh &
    ```
    （末尾的`&`符号表示在后台运行）

---

![](img/d986e135b29cc5fe273ef31836f42fe5_68.png)

本节课中我们一起学习了AWK的`BEGIN`和`END`模式，掌握了如何利用AWK进行比`grep`更精细的数据过滤和格式化，并将其应用于内存、网络流量和磁盘空间等实际监控场景。我们还了解了如何将写好的监控脚本设置为开机自动启动。AWK是Linux运维工程师手中不可或缺的利器，熟练使用将极大提升工作效率。