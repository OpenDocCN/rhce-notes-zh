# Linux文件管理：4-3：文件查找、传输与打包压缩 🔍📦

![](img/6b86fde10b317cdcfb38437fa627b282_1.png)

在本节课中，我们将要学习如何在Linux文件系统中查找、传输以及打包压缩文件。首先，我们会重点介绍两种文件查找工具：`locate`和`find`，理解它们的工作原理和适用场景。

## 文件查找概述

![](img/6b86fde10b317cdcfb38437fa627b282_3.png)

在文件系统上查找符合条件的文件，主要有两种方式：`locate`和`find`。它们的核心区别在于，`locate`是非实时查找，基于数据库索引；而`find`是实时查找，直接遍历文件系统。

![](img/6b86fde10b317cdcfb38437fa627b282_4.png)

![](img/6b86fde10b317cdcfb38437fa627b282_6.png)

![](img/6b86fde10b317cdcfb38437fa627b282_8.png)

![](img/6b86fde10b317cdcfb38437fa627b282_9.png)

### 非实时查找：`locate`命令

`locate`命令通过查询系统上预先生成的文件索引数据库来查找文件。这个索引数据库的构建是由系统在空闲时自动进行的周期性任务，也可以通过执行`updatedb`命令手动更新。

![](img/6b86fde10b317cdcfb38437fa627b282_11.png)

![](img/6b86fde10b317cdcfb38437fa627b282_13.png)

由于构建数据库需要遍历整个根文件系统，因此非常消耗资源。使用`locate`命令前，通常需要先运行`updatedb`来确保数据库是最新的。

![](img/6b86fde10b317cdcfb38437fa627b282_15.png)

![](img/6b86fde10b317cdcfb38437fa627b282_17.png)

以下是`locate`命令的工作特点：
*   查找速度快。
*   进行模糊查找。
*   属于非实时查找。
*   搜索结果是文件的全路径。
*   只能搜索用户具备读取和执行权限的目录。

`locate`命令的基本格式如下：
```bash
locate [选项] 关键字
```
常用选项包括：
*   `-i`：忽略大小写。
*   `-n N`：只列举前N个匹配结果。

![](img/6b86fde10b317cdcfb38437fa627b282_19.png)

让我们看一个案例。首先更新数据库，然后查找包含“log”字符的文件：
```bash
sudo updatedb
locate log
```
该命令会打印出所有路径或文件名中包含“log”的条目。

![](img/6b86fde10b317cdcfb38437fa627b282_21.png)

![](img/6b86fde10b317cdcfb38437fa627b282_22.png)

![](img/6b86fde10b317cdcfb38437fa627b282_23.png)

![](img/6b86fde10b317cdcfb38437fa627b282_24.png)

我们也可以结合正则表达式进行更精确的查找。例如，查找以“.conf”结尾的文件，并只显示前10个结果：
```bash
locate -n 10 ‘\.conf$’
```
这里的`\`用于转义点号`.`，`$`表示行尾。

更进一步，我们可以通过管道`|`将`locate`的结果传递给其他命令（如`grep`）进行二次筛选。例如，查找特定路径下的log文件：
```bash
locate log | grep /var/log
```

### 实时查找：`find`命令

![](img/6b86fde10b317cdcfb38437fa627b282_26.png)

![](img/6b86fde10b317cdcfb38437fa627b282_27.png)

![](img/6b86fde10b317cdcfb38437fa627b282_28.png)

`find`是我们更常使用的工具，它进行实时查找，无需依赖数据库，但查找速度相对较慢。它的查找条件非常丰富，功能强大。

![](img/6b86fde10b317cdcfb38437fa627b282_29.png)

![](img/6b86fde10b317cdcfb38437fa627b282_31.png)

`find`命令的工作特点如下：
*   查找速度慢。
*   进行精确查找。
*   属于实时查找。
*   查找条件丰富。
*   只搜索用户具备读取和执行权限的目录和文件。

![](img/6b86fde10b317cdcfb38437fa627b282_33.png)

`find`命令的基本格式为：
```bash
find [查找路径] [查找条件] [处理动作]
```
*   **查找路径**：指定开始查找的目录，默认为当前目录`.`。
*   **查找条件**：指定查找标准，如文件名、大小、类型等，默认为该路径下所有文件。
*   **处理动作**：对查找到的文件执行的操作，默认为打印到屏幕`-print`。

![](img/6b86fde10b317cdcfb38437fa627b282_35.png)

![](img/6b86fde10b317cdcfb38437fa627b282_36.png)

#### 根据搜索层级查找

![](img/6b86fde10b317cdcfb38437fa627b282_38.png)

![](img/6b86fde10b317cdcfb38437fa627b282_40.png)

我们可以控制`find`命令搜索目录的深度。
*   `-maxdepth LEVEL`：设置最大搜索深度（LEVEL为数字）。
*   `-mindepth LEVEL`：设置最小搜索深度。

![](img/6b86fde10b317cdcfb38437fa627b282_42.png)

例如，在`/data/test`目录下，查找深度最大为2级的文件和目录：
```bash
find /data/test -maxdepth 2
```
`-depth`选项会让`find`先处理目录内的文件，再处理目录本身。

![](img/6b86fde10b317cdcfb38437fa627b282_44.png)

![](img/6b86fde10b317cdcfb38437fa627b282_46.png)

![](img/6b86fde10b317cdcfb38437fa627b282_47.png)

#### 根据文件名查找

![](img/6b86fde10b317cdcfb38437fa627b282_49.png)

![](img/6b86fde10b317cdcfb38437fa627b282_51.png)

![](img/6b86fde10b317cdcfb38437fa627b282_53.png)

使用`-name`选项可以根据文件名进行查找。例如，在`/data/test`下查找名为`f3.txt`的文件：
```bash
find /data/test -name “f3.txt”
```
使用`-iname`选项可以不区分大小写：
```bash
find /data/test -iname “F3.TXT”
```

![](img/6b86fde10b317cdcfb38437fa627b282_54.png)

#### 根据文件属主/属组查找

![](img/6b86fde10b317cdcfb38437fa627b282_56.png)

![](img/6b86fde10b317cdcfb38437fa627b282_57.png)

可以根据文件的所有者或所属用户组来查找文件。
*   `-user USERNAME`：查找属主为指定用户的文件。
*   `-group GROUPNAME`：查找属组为指定组的文件。
*   `-nouser`：查找没有属主的文件。
*   `-nogroup`：查找没有属组的文件。

![](img/6b86fde10b317cdcfb38437fa627b282_59.png)

![](img/6b86fde10b317cdcfb38437fa627b282_60.png)

![](img/6b86fde10b317cdcfb38437fa627b282_62.png)

![](img/6b86fde10b317cdcfb38437fa627b282_63.png)

例如，查找属主为`user1`的文件：
```bash
find / -user user1
```
查找没有属主的文件：
```bash
find / -nouser
```

![](img/6b86fde10b317cdcfb38437fa627b282_65.png)

![](img/6b86fde10b317cdcfb38437fa627b282_67.png)

#### 根据文件类型查找

使用`-type`选项可以根据文件类型查找。
*   `f`：普通文件
*   `d`：目录
*   `l`：符号链接文件
*   `b`：块设备文件
*   `c`：字符设备文件
*   `p`：管道文件

![](img/6b86fde10b317cdcfb38437fa627b282_69.png)

![](img/6b86fde10b317cdcfb38437fa627b282_71.png)

例如，查找`/data/test`目录下的所有普通文件：
```bash
find /data/test -type f
```

![](img/6b86fde10b317cdcfb38437fa627b282_73.png)

![](img/6b86fde10b317cdcfb38437fa627b282_74.png)

#### 查找空文件或目录

![](img/6b86fde10b317cdcfb38437fa627b282_76.png)

![](img/6b86fde10b317cdcfb38437fa627b282_77.png)

使用`-empty`选项可以查找空文件或空目录。
```bash
find /data/test -empty
```

![](img/6b86fde10b317cdcfb38437fa627b282_79.png)

![](img/6b86fde10b317cdcfb38437fa627b282_80.png)

![](img/6b86fde10b317cdcfb38437fa627b282_82.png)

![](img/6b86fde10b317cdcfb38437fa627b282_83.png)

#### 组合条件查找

![](img/6b86fde10b317cdcfb38437fa627b282_85.png)

![](img/6b86fde10b317cdcfb38437fa627b282_86.png)

![](img/6b86fde10b317cdcfb38437fa627b282_88.png)

`find`支持使用逻辑运算符组合多个条件。
*   `-a` 或 `-and`：与（默认关系，可省略）。
*   `-o` 或 `-or`：或。
*   `!` 或 `-not`：非。

![](img/6b86fde10b317cdcfb38437fa627b282_89.png)

例如，查找`/data/test`目录下，属主是`user1`**并且**类型是普通文件的项：
```bash
find /data/test -user user1 -type f
```
查找`/data/test`目录下，属主是`user1`**或者**类型是目录的项：
```bash
find /data/test -user user1 -o -type d
```
查找`/etc`目录下，排除某个子目录后进行查找：
```bash
find /etc -path “/etc/sane.d” -prune -o -name “*.conf” -print
```

#### 根据文件大小查找

![](img/6b86fde10b317cdcfb38437fa627b282_91.png)

![](img/6b86fde10b317cdcfb38437fa627b282_93.png)

![](img/6b86fde10b317cdcfb38437fa627b282_95.png)

![](img/6b86fde10b317cdcfb38437fa627b282_96.png)

使用`-size`选项可以根据文件大小查找。单位可以是：
*   `c`：字节
*   `k`：KB
*   `M`：MB
*   `G`：GB

![](img/6b86fde10b317cdcfb38437fa627b282_98.png)

![](img/6b86fde10b317cdcfb38437fa627b282_100.png)

![](img/6b86fde10b317cdcfb38437fa627b282_102.png)

例如，查找`/`目录下大于100MB的文件：
```bash
find / -size +100M
```
查找`/`目录下小于10KB的文件：
```bash
find / -size -10k
```

![](img/6b86fde10b317cdcfb38437fa627b282_104.png)

![](img/6b86fde10b317cdcfb38437fa627b282_106.png)

![](img/6b86fde10b317cdcfb38437fa627b282_108.png)

![](img/6b86fde10b317cdcfb38437fa627b282_109.png)

#### 根据时间戳查找

![](img/6b86fde10b317cdcfb38437fa627b282_111.png)

![](img/6b86fde10b317cdcfb38437fa627b282_113.png)

`find`可以根据文件的三种时间戳进行查找，时间单位可以是天（`-atime`, `-mtime`, `-ctime`）或分钟（`-amin`, `-mmin`, `-cmin`）。
*   **修改时间（mtime）**：文件内容最后一次被修改的时间。
*   **访问时间（atime）**：文件最后一次被读取的时间。
*   **状态变更时间（ctime）**：文件元数据（如权限、属主）最后一次改变的时间。

![](img/6b86fde10b317cdcfb38437fa627b282_115.png)

![](img/6b86fde10b317cdcfb38437fa627b282_117.png)

![](img/6b86fde10b317cdcfb38437fa627b282_119.png)

例如，查找`/etc`目录下最近50天内被修改过的文件：
```bash
find /etc -mtime -50
```
查找过去一小时内状态发生改变的文件：
```bash
find / -cmin -60
```

![](img/6b86fde10b317cdcfb38437fa627b282_121.png)

![](img/6b86fde10b317cdcfb38437fa627b282_122.png)

![](img/6b86fde10b317cdcfb38437fa627b282_123.png)

![](img/6b86fde10b317cdcfb38437fa627b282_124.png)

![](img/6b86fde10b317cdcfb38437fa627b282_126.png)

#### 根据权限查找

![](img/6b86fde10b317cdcfb38437fa627b282_128.png)

![](img/6b86fde10b317cdcfb38437fa627b282_130.png)

使用`-perm`选项可以根据文件权限查找。
*   `-perm MODE`：精确匹配权限`MODE`。
*   `-perm /MODE`：任意一类用户（u,g,o）的权限位只要能匹配`MODE`中的任何一位即可。
*   `-perm -MODE`：每一类用户（u,g,o）的权限位必须同时匹配`MODE`中指定的所有位。

例如，查找权限中任何人都有写权限的文件：
```bash
find . -perm -222
```

#### 处理动作

`find`查找到文件后，可以执行多种处理动作。
*   `-print`：默认动作，打印至屏幕。
*   `-ls`：以长格式`ls -l`的形式显示找到的文件详情。
*   `-delete`：**慎用**，删除找到的文件。
*   `-ok COMMAND {} \;`：对每个找到的文件执行`COMMAND`命令，但会交互式地要求用户确认。
*   `-exec COMMAND {} \;`：对每个找到的文件执行`COMMAND`命令，静默执行。

![](img/6b86fde10b317cdcfb38437fa627b282_132.png)

![](img/6b86fde10b317cdcfb38437fa627b282_133.png)

`{}`代表`find`找到的每个文件路径，`\;`是命令结束的标记。

![](img/6b86fde10b317cdcfb38437fa627b282_135.png)

![](img/6b86fde10b317cdcfb38437fa627b282_136.png)

**重要提示**：使用`-delete`或`-exec`时，务必确认查找条件准确无误。建议先使用`-print`或`-ls`预览结果，再执行危险操作。

例如，交互式地删除`/tmp`目录下所有`.log`文件：
```bash
find /tmp -name “*.log” -ok rm {} \;
```
静默地将`/data`目录下所有`.conf`文件备份（添加.bak后缀）：
```bash
find /data -name “*.conf” -exec cp {} {}.bak \;
```

![](img/6b86fde10b317cdcfb38437fa627b282_138.png)

![](img/6b86fde10b317cdcfb38437fa627b282_140.png)

## 总结

本节课我们一起学习了Linux中的文件查找、传输与打包压缩的基础知识。我们重点探讨了`locate`和`find`两个强大的文件查找命令：
*   `locate`基于数据库，速度快，适合快速模糊查找。
*   `find`实时遍历，条件丰富，功能强大，可对找到的文件执行各种操作。

![](img/6b86fde10b317cdcfb38437fa627b282_142.png)

![](img/6b86fde10b317cdcfb38437fa627b282_144.png)

![](img/6b86fde10b317cdcfb38437fa627b282_146.png)

理解并熟练运用`find`命令的各类选项（如按名称、类型、大小、时间、权限查找）及其逻辑组合，是高效管理Linux文件系统的关键。同时，务必谨慎使用`-delete`和`-exec`等处理动作，避免误操作。