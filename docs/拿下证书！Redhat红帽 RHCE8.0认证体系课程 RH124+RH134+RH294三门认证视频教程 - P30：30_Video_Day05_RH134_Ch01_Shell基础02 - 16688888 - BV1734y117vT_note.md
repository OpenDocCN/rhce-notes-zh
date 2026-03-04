# Redhat红帽 RHCE8.0认证体系课程：RH134：Shell基础02

![](img/0abadbe74fda3692f34ec57c97f765d2_0.png)

![](img/0abadbe74fda3692f34ec57c97f765d2_2.png)

![](img/0abadbe74fda3692f34ec57c97f765d2_4.png)

## 概述
在本节课中，我们将要学习两个强大的文本处理工具：`awk` 和 `sed`。我们将了解它们的基本语法、常用功能以及如何在实际场景中应用它们来处理和分析文本数据。

![](img/0abadbe74fda3692f34ec57c97f765d2_6.png)

![](img/0abadbe74fda3692f34ec57c97f765d2_8.png)

![](img/0abadbe74fda3692f34ec57c97f765d2_10.png)

![](img/0abadbe74fda3692f34ec57c97f765d2_12.png)

---

![](img/0abadbe74fda3692f34ec57c97f765d2_14.png)

![](img/0abadbe74fda3692f34ec57c97f765d2_16.png)

## AWK 命令详解

![](img/0abadbe74fda3692f34ec57c97f765d2_18.png)

![](img/0abadbe74fda3692f34ec57c97f765d2_20.png)

![](img/0abadbe74fda3692f34ec57c97f765d2_22.png)

![](img/0abadbe74fda3692f34ec57c97f765d2_23.png)

![](img/0abadbe74fda3692f34ec57c97f765d2_25.png)

![](img/0abadbe74fda3692f34ec57c97f765d2_26.png)

上一节我们介绍了 `cut` 命令的局限性，本节中我们来看看 `awk` 命令如何解决这些问题。`awk` 能够根据指定的分隔符截取文本，并进行标准化输出。

![](img/0abadbe74fda3692f34ec57c97f765d2_28.png)

![](img/0abadbe74fda3692f34ec57c97f765d2_30.png)

![](img/0abadbe74fda3692f34ec57c97f765d2_32.png)

![](img/0abadbe74fda3692f34ec57c97f765d2_34.png)

![](img/0abadbe74fda3692f34ec57c97f765d2_36.png)

### AWK 基本语法
`awk` 命令的基本语法如下：
```bash
awk -F '分隔符' '动作指令' 文件名
```
其中，`-F` 用于指定分隔符，动作指令需要用单引号包裹。

![](img/0abadbe74fda3692f34ec57c97f765d2_38.png)

![](img/0abadbe74fda3692f34ec57c97f765d2_40.png)

![](img/0abadbe74fda3692f34ec57c97f765d2_42.png)

### 标准化输出示例
假设我们有一个文本文件 `test2.txt`，内容如下：
```
1 2 3 4 5 6 7 8 9 0
1 2 3 4 5 6 7 8
```
这个文本的格式并不规则。我们可以使用 `awk` 来截取并标准化输出第1、5、7列：
```bash
awk -F ' ' '{print $1, $5, $7}' test2.txt
```
`awk` 会将连续的多个分隔符视为一个，并去除它们，从而实现标准化输出。

![](img/0abadbe74fda3692f34ec57c97f765d2_44.png)

![](img/0abadbe74fda3692f34ec57c97f765d2_46.png)

![](img/0abadbe74fda3692f34ec57c97f765d2_48.png)

### 与 CUT 命令的对比
以下是使用 `cut` 命令处理相同文本的结果：
```bash
cut -d ' ' -f 1,5,7 test2.txt
```
对比两者输出，可以明显看出 `awk` 在处理不规则分隔符时的优势。

![](img/0abadbe74fda3692f34ec57c97f765d2_50.png)

![](img/0abadbe74fda3692f34ec57c97f765d2_52.png)

### 按关键字搜索和输出
`awk` 还可以根据关键字搜索特定行并输出。例如，在文件 `test3.txt` 中搜索包含 “root” 的行，并输出其第1、2、3列，列之间用制表符分隔：
```bash
awk -F ' ' '/root/ {print $1 "\t" $2 "\t" $3}' test3.txt
```
这里，`/root/` 表示搜索关键字 “root”，`\t` 代表制表符。

![](img/0abadbe74fda3692f34ec57c97f765d2_54.png)

![](img/0abadbe74fda3692f34ec57c97f765d2_56.png)

![](img/0abadbe74fda3692f34ec57c97f765d2_58.png)

### 特殊变量 NF 和 NR
`awk` 有两个常用的内置变量：
*   **`NF`**：表示当前行的列数。`$NF` 代表最后一列。
*   **`NR`**：表示当前处理的行号。

以下是使用示例：
*   打印 `/etc/passwd` 文件的最后一列：
    ```bash
    awk -F ':' '{print $NF}' /etc/passwd
    ```
*   打印行号及其对应列（形成对角线输出）：
    ```bash
    awk '{print $NR}' test.txt
    ```
    注意，`$NR` 表示第 `NR` 行的第 `NR` 列。

### 实践练习：编写巡检脚本
现在，我们来完成一个实践练习，编写一个脚本，用于获取指定网络接口的 MAC 地址和 IP 地址。

以下是脚本的参考实现：
```bash
#!/bin/bash
# 提示用户输入网络设备名称
read -p "Please input the network device name: " DEV_NAME

![](img/0abadbe74fda3692f34ec57c97f765d2_60.png)

![](img/0abadbe74fda3692f34ec57c97f765d2_61.png)

![](img/0abadbe74fda3692f34ec57c97f765d2_62.png)

![](img/0abadbe74fda3692f34ec57c97f765d2_64.png)

![](img/0abadbe74fda3692f34ec57c97f765d2_65.png)

# 获取 MAC 地址 (通常位于 ip addr show 输出的第二行)
DEV_MAC=$(ip addr show $DEV_NAME | awk -F ' ' 'NR==2 {print $2}')

![](img/0abadbe74fda3692f34ec57c97f765d2_67.png)

![](img/0abadbe74fda3692f34ec57c97f765d2_69.png)

# 获取 IP 地址 (搜索以 ‘inet’ 开头的行)
DEV_IP=$(ip addr show $DEV_NAME | awk -F ' ' '/inet / {print $2}')

# 格式化输出结果
echo "The network device information is listed below:"
echo "Device Name: $DEV_NAME"
echo "MAC Address: $DEV_MAC"
echo "IP Address: $DEV_IP"
```
这个脚本展示了 `awk` 在实际系统巡检中的应用价值。

![](img/0abadbe74fda3692f34ec57c97f765d2_71.png)

---

## SED 命令详解

接下来，我们学习 `sed` 命令。`sed` 是一个流编辑器，用于对文本文件进行基本的文本转换，非常适合自动化编辑任务。

### SED 基本语法
`sed` 命令的基本语法如下：
```bash
sed [选项] ‘动作指令’ 文件名
```
常用选项包括：
*   `-e`：直接在命令行模式上进行 `sed` 动作编辑。
*   `-f`：执行包含 `sed` 动作的文件。
*   `-n`：仅显示处理后的结果。
*   `-i`：直接修改文件内容（慎用）。

### 文本新增与删除
以下是 `sed` 进行行单位新增和删除的示例：
*   在文件第4行后添加一行 “new line” (仅屏幕显示)：
    ```bash
    sed -e ‘4a\new line’ textfile.txt
    ```
*   删除文件的第2到第5行：
    ```bash
    nl passwd | sed ‘2,5d’
    ```
*   删除第3行到末尾：
    ```bash
    nl passwd | sed ‘3,$d’
    ```
*   在第2行后插入多行内容：
    ```bash
    sed ‘2a\drink tea\
    drink beer’ textfile.txt
    ```

### 文本替换
`sed` 可以使用 `c` 命令进行整行替换，或使用 `s` 命令进行部分字符串替换。
*   将第2到第5行替换为 “number two to five”：
    ```bash
    sed ‘2,5c\number two to five’ textfile.txt
    ```
*   搜索包含 “root” 的行并打印：
    ```bash
    sed -n ‘/root/p’ /etc/passwd
    ```
*   搜索 “root” 并将其替换为 “admin”：
    ```bash
    sed ‘s/root/admin/g’ /etc/passwd
    ```

### 实战应用：提取 IP 地址
我们可以使用 `sed` 分步从 `ifconfig` 命令输出中提取 IP 地址：
```bash
ifconfig eth0 | sed -n ‘/inet /p‘ | sed ‘s/^.*inet //g‘ | sed ‘s/ netmask.*$//g‘
```
这个命令链通过三次 `sed` 处理，逐步去除非 IP 地址部分，最终只留下 IP 地址。

### 直接修改文件
使用 `-i` 选项，`sed` 可以直接修改源文件，这在进行批量文本替换时非常高效。
*   将文件 `sed.txt` 中所有行末尾的句点 `.` 替换为感叹号 `!`：
    ```bash
    sed -i ‘s/\.$/!/g’ sed.txt
    ```
*   在文件末尾添加一行 “This is a test”：
    ```bash
    sed -i ‘$a\This is a test’ sed.txt
    ```
**注意**：`-i` 选项会直接更改原文件，操作前请务必确认或备份。

---

![](img/0abadbe74fda3692f34ec57c97f765d2_73.png)

## 总结
本节课中我们一起学习了 `awk` 和 `sed` 这两个强大的文本处理工具。
*   **`awk`** 擅长基于分隔符对文本进行列处理、数据提取和格式化输出，其内置变量 `NF` 和 `NR` 提供了强大的行、列控制能力。
*   **`sed`** 则是一个流编辑器，专注于对文本进行新增、删除、替换等编辑操作，尤其适合编写脚本进行批量、自动化的文本处理。

![](img/0abadbe74fda3692f34ec57c97f765d2_75.png)

![](img/0abadbe74fda3692f34ec57c97f765d2_77.png)

掌握这两个工具，将极大提升你在 Linux 环境下处理日志、配置文件和进行数据清洗的效率。