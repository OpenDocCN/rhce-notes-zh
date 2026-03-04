# Redhat红帽 RHCE8.0认证体系课程：P26：Shell基础02 🐚

在本节课中，我们将要学习两个强大的文本处理工具：`awk` 和 `sed`。我们将通过对比和实例，了解它们如何解决 `cut` 命令的局限性，并实现更灵活、标准化的文本截取、编辑与输出。

![](img/c68ea73b6bd3253c830c91ad2b5c29d9_1.png)

![](img/c68ea73b6bd3253c830c91ad2b5c29d9_3.png)

## 使用 `awk` 进行标准化文本处理 🔧

![](img/c68ea73b6bd3253c830c91ad2b5c29d9_5.png)

![](img/c68ea73b6bd3253c830c91ad2b5c29d9_7.png)

![](img/c68ea73b6bd3253c830c91ad2b5c29d9_9.png)

![](img/c68ea73b6bd3253c830c91ad2b5c29d9_10.png)

![](img/c68ea73b6bd3253c830c91ad2b5c29d9_12.png)

上一节我们介绍了 `cut` 命令的局限性，本节中我们来看看 `awk` 如何解决这些问题。`awk` 能够以指定的分隔符截取文本，并进行标准化输出。

![](img/c68ea73b6bd3253c830c91ad2b5c29d9_13.png)

### `awk` 的基本语法

![](img/c68ea73b6bd3253c830c91ad2b5c29d9_15.png)

![](img/c68ea73b6bd3253c830c91ad2b5c29d9_17.png)

![](img/c68ea73b6bd3253c830c91ad2b5c29d9_19.png)

`awk` 的基本命令格式如下：
```bash
awk -F '分隔符' '动作指令' 文件名
```
其中，`-F` 用于指定分隔符，动作指令需要用单引号括起来。

![](img/c68ea73b6bd3253c830c91ad2b5c29d9_21.png)

### `awk` 与 `cut` 的对比示例

![](img/c68ea73b6bd3253c830c91ad2b5c29d9_23.png)

![](img/c68ea73b6bd3253c830c91ad2b5c29d9_25.png)

![](img/c68ea73b6bd3253c830c91ad2b5c29d9_27.png)

为了更好地理解 `awk` 的优势，我们创建一个测试文件 `test2.txt`，其内容如下：
```
1 2 3 4 5 6 7 8 9 0
1 2 3   5 6     8
```
可以看到，第二行的空格分隔是不规则的。

![](img/c68ea73b6bd3253c830c91ad2b5c29d9_29.png)

![](img/c68ea73b6bd3253c830c91ad2b5c29d9_30.png)

![](img/c68ea73b6bd3253c830c91ad2b5c29d9_32.png)

![](img/c68ea73b6bd3253c830c91ad2b5c29d9_34.png)

以下是使用 `awk` 和 `cut` 分别截取第1、5、7列的命令与结果对比：

![](img/c68ea73b6bd3253c830c91ad2b5c29d9_36.png)

![](img/c68ea73b6bd3253c830c91ad2b5c29d9_37.png)

![](img/c68ea73b6bd3253c830c91ad2b5c29d9_39.png)

![](img/c68ea73b6bd3253c830c91ad2b5c29d9_40.png)

![](img/c68ea73b6bd3253c830c91ad2b5c29d9_41.png)

*   **使用 `awk` 命令：**
    ```bash
    awk -F ' ' '{print $1, $5, $7}' test2.txt
    ```
    **输出结果：**
    ```
    1 5 7
    1 6 8
    ```
    `awk` 将多个连续空格视为一个分隔符，并去除了多余的空格，实现了标准化输出。

![](img/c68ea73b6bd3253c830c91ad2b5c29d9_43.png)

![](img/c68ea73b6bd3253c830c91ad2b5c29d9_45.png)

![](img/c68ea73b6bd3253c830c91ad2b5c29d9_47.png)

*   **使用 `cut` 命令：**
    ```bash
    cut -d ' ' -f 1,5,7 test2.txt
    ```
    **输出结果：**
    ```
    1
    1
    ```
    `cut` 严格按单个空格分隔，无法正确处理不规则的空格，导致输出不符合预期。

![](img/c68ea73b6bd3253c830c91ad2b5c29d9_48.png)

![](img/c68ea73b6bd3253c830c91ad2b5c29d9_50.png)

![](img/c68ea73b6bd3253c830c91ad2b5c29d9_52.png)

通过对比可以明显看出，`awk` 在处理不规则分隔的文本时更具优势。

![](img/c68ea73b6bd3253c830c91ad2b5c29d9_54.png)

![](img/c68ea73b6bd3253c830c91ad2b5c29d9_56.png)

### `awk` 的关键字匹配与格式化输出

![](img/c68ea73b6bd3253c830c91ad2b5c29d9_58.png)

![](img/c68ea73b6bd3253c830c91ad2b5c29d9_60.png)

`awk` 还可以根据关键字匹配特定行，并进行格式化输出。

![](img/c68ea73b6bd3253c830c91ad2b5c29d9_62.png)

![](img/c68ea73b6bd3253c830c91ad2b5c29d9_64.png)

创建一个测试文件 `test3.txt`，内容如下：
```
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
```

![](img/c68ea73b6bd3253c830c91ad2b5c29d9_66.png)

![](img/c68ea73b6bd3253c830c91ad2b5c29d9_68.png)

以下命令用于查找包含“root”关键字的行，并输出其第1、2、3列，列之间用制表符分隔：
```bash
awk -F ':' '/root/ {print $1 "\t" $2 "\t" $3}' test3.txt
```
**输出结果：**
```
root    x       0
```
这里，`/root/` 用于匹配包含“root”的行，`print` 语句中的 `"\t"` 代表制表符。

![](img/c68ea73b6bd3253c830c91ad2b5c29d9_70.png)

![](img/c68ea73b6bd3253c830c91ad2b5c29d9_71.png)

![](img/c68ea73b6bd3253c830c91ad2b5c29d9_73.png)

![](img/c68ea73b6bd3253c830c91ad2b5c29d9_75.png)

### `awk` 的特殊变量：`NF` 与 `NR`

![](img/c68ea73b6bd3253c830c91ad2b5c29d9_77.png)

![](img/c68ea73b6bd3253c830c91ad2b5c29d9_79.png)

`awk` 内置了两个常用变量：
*   **`NF`**： 代表当前行的字段总数（列数）。
*   **`NR`**： 代表当前处理的行号。

![](img/c68ea73b6bd3253c830c91ad2b5c29d9_81.png)

![](img/c68ea73b6bd3253c830c91ad2b5c29d9_82.png)

![](img/c68ea73b6bd3253c830c91ad2b5c29d9_83.png)

以下是它们的应用示例：

![](img/c68ea73b6bd3253c830c91ad2b5c29d9_85.png)

*   **打印每行的最后一列（`$NF`）：**
    ```bash
    pwd | awk -F '/' '{print $NF}'
    ```
    此命令会输出当前工作目录的最后一层名称。

*   **打印每行的行号与内容（`NR`）：**
    ```bash
    cat test3.txt | awk '{print NR, $0}'
    ```
    此命令会在每一行前加上行号。

需要注意的是，`$NR` 表示的是“第NR行的第NR列”，这通常用于获取对角线上的值。

![](img/c68ea73b6bd3253c830c91ad2b5c29d9_87.png)

### `awk` 实战练习：提取网卡信息 📝

以下是一个结合了之前知识的脚本练习，用于提取指定网卡设备的名称、MAC地址和IP地址。

![](img/c68ea73b6bd3253c830c91ad2b5c29d9_89.png)

![](img/c68ea73b6bd3253c830c91ad2b5c29d9_91.png)

**脚本目标：** 提示用户输入网卡名，然后输出该网卡的名称、MAC地址和IP地址。

![](img/c68ea73b6bd3253c830c91ad2b5c29d9_93.png)

![](img/c68ea73b6bd3253c830c91ad2b5c29d9_94.png)

**参考脚本 (`get_net_info.sh`)：**
```bash
#!/bin/bash
# 提示用户输入网卡设备名
read -p "Please import the network device name: " DEV_NAME

![](img/c68ea73b6bd3253c830c91ad2b5c29d9_95.png)

![](img/c68ea73b6bd3253c830c91ad2b5c29d9_97.png)

![](img/c68ea73b6bd3253c830c91ad2b5c29d9_98.png)

![](img/c68ea73b6bd3253c830c91ad2b5c29d9_100.png)

# 获取MAC地址 (通常位于ip addr show输出的第二行)
DEV_MAC=$(ip addr show $DEV_NAME | awk 'NR==2 {print $2}')

![](img/c68ea73b6bd3253c830c91ad2b5c29d9_102.png)

# 获取IP地址 (匹配以‘inet’开头的行)
DEV_IP=$(ip addr show $DEV_NAME | awk '/inet / {print $2}' | awk -F '/' '{print $1}')

![](img/c68ea73b6bd3253c830c91ad2b5c29d9_104.png)

![](img/c68ea73b6bd3253c830c91ad2b5c29d9_106.png)

# 格式化输出信息
echo "The network device information is listed below:"
echo "Device Name: $DEV_NAME"
echo "MAC Address: $DEV_MAC"
echo "IP Address:  $DEV_IP"
```

**脚本执行示例：**
```bash
sh get_net_info.sh
Please import the network device name: ens33
The network device information is listed below:
Device Name: ens33
MAC Address: 00:0c:29:xx:xx:xx
IP Address:  192.168.1.100
```

![](img/c68ea73b6bd3253c830c91ad2b5c29d9_108.png)

这个脚本展示了 `awk` 在实际系统巡检任务中的应用价值，可以方便地自动化信息收集工作。

![](img/c68ea73b6bd3253c830c91ad2b5c29d9_110.png)

![](img/c68ea73b6bd3253c830c91ad2b5c29d9_112.png)

## 使用 `sed` 进行流式文本编辑 ✏️

![](img/c68ea73b6bd3253c830c91ad2b5c29d9_114.png)

![](img/c68ea73b6bd3253c830c91ad2b5c29d9_116.png)

接下来，我们学习 `sed` 命令。`sed` 是一个流编辑器，主要用于自动编辑一个或多个文件，简化对文件的反复操作。

### `sed` 的基本语法与常用选项

`sed` 的基本命令格式如下：
```bash
sed [选项] ‘动作指令’ 文件名
```

常用选项包括：
*   `-n`： 仅显示处理后的结果，抑制默认输出。
*   `-e`： 允许在同一行里执行多条 `sed` 命令。
*   `-i`： **直接修改文件内容**（使用需谨慎）。
*   `-f`： 从脚本文件中读取 `sed` 命令。

### `sed` 的常见操作情景

以下是 `sed` 命令的几个典型应用场景，我们以一个名为 `testfile.txt` 的文件为例。

**情景一：增加行**
在第四行后插入一行 “new line”：
```bash
sed ‘4a new line’ testfile.txt
```
`4a` 表示在第四行后**追加**。

**情景二：删除行**
删除第2到第5行：
```bash
nl testfile.txt | sed ‘2,5d’
```
`2,5d` 表示删除第2至第5行。`nl` 命令用于显示行号。

**情景三：替换行**
将第2到第5行内容替换为 “number two to five”：
```bash
sed ‘2,5c number two to five’ testfile.txt
```
`2,5c` 表示将第2至第5行**替换**为新内容。

**情景四：搜索并显示**
仅显示包含“root”关键字的行：
```bash
sed -n ‘/root/p’ /etc/passwd
```
`/root/p` 表示打印匹配 “root” 的行，`-n` 选项确保只输出这些行。

**情景五：搜索并替换（字符串替换）**
将包含“root”的行中的 “bash” 替换为 “blueshell”：
```bash
sed -n ‘/root/{s/bash/blueshell/;p}’ /etc/passwd
```
`s/旧字符串/新字符串/` 是替换命令，多个命令可以用分号 `;` 分隔。

**情景六：直接修改文件**
这是 `sed` 非常强大且危险的功能，使用 `-i` 选项。
1.  将文件 `url.txt` 中每行末尾的 `.` 替换为 `!`：
    ```bash
    sed -i ‘s/\.$/!/’ url.txt
    ```
    `\.` 对点进行转义，`$` 表示行尾。
2.  在文件最后一行添加 “This is a test”：
    ```bash
    sed -i ‘$a This is a test’ url.txt
    ```
    `$` 代表最后一行，`a` 是追加。

### `sed` 实战示例：提取IP地址

我们可以用 `sed` 分步从 `ifconfig` 命令输出中提取IP地址：
```bash
ifconfig ens33 | sed -n ‘/inet /p’ | sed ‘s/^.*inet //’ | sed ‘s/ netmask.*$//’
```
**命令分解：**
1.  `sed -n ‘/inet /p’`： 仅输出包含 “inet ” 的行。
2.  `sed ‘s/^.*inet //’`： 将行首到 “inet ” 的所有字符替换为空（删除）。
3.  `sed ‘s/ netmask.*$//’`： 将 “netmask” 及其之后到行尾的所有字符替换为空（删除）。

![](img/c68ea73b6bd3253c830c91ad2b5c29d9_118.png)

最终得到纯净的IPv4地址。这展示了 `sed` 通过管道组合，进行复杂文本处理的强大能力。

![](img/c68ea73b6bd3253c830c91ad2b5c29d9_120.png)

## 课程总结 📚

![](img/c68ea73b6bd3253c830c91ad2b5c29d9_122.png)

本节课中我们一起学习了两个核心的Shell文本处理工具：
1.  **`awk`**： 擅长基于列的处理和格式化输出。它通过内置变量（如 `NF`, `NR`）和编程逻辑，能灵活地过滤、计算和格式化文本数据，是生成标准化报告的有力工具。
2.  **`sed`**： 擅长基于行的编辑和转换。它通过寻址（行号、模式）和命令（增、删、改、查），能够以非交互式、流式的方式高效编辑文本，特别适合编写批量转换脚本。

![](img/c68ea73b6bd3253c830c91ad2b5c29d9_124.png)

掌握 `awk` 和 `sed` 能够极大提升在Linux命令行下处理日志、配置文件和各类文本数据的效率，是系统管理和自动化脚本编写中不可或缺的技能。