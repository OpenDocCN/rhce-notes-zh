# Redhat红帽 RHCE8.0认证体系课程：P30：Shell基础04 🐚

![](img/f7fb363aa4c41e4148874df15fbb26e8_0.png)

![](img/f7fb363aa4c41e4148874df15fbb26e8_2.png)

![](img/f7fb363aa4c41e4148874df15fbb26e8_3.png)

在本节课中，我们将要学习Shell脚本中的特殊变量、参数处理、字符串操作以及命令的退出状态码。这些是编写健壮、灵活脚本的基础知识。

![](img/f7fb363aa4c41e4148874df15fbb26e8_5.png)

## 特殊变量与位置参数 🔢

![](img/f7fb363aa4c41e4148874df15fbb26e8_7.png)

![](img/f7fb363aa4c41e4148874df15fbb26e8_9.png)

![](img/f7fb363aa4c41e4148874df15fbb26e8_11.png)

上一节我们介绍了任务控制，本节中我们来看看Shell中的特殊变量。特殊变量中有一类叫做预定义变量，例如 `$0`, `$1`, `$2` 等，它们被称为位置参数。

以下是关于位置参数的核心要点：
*   `$0` 代表脚本自身的名称。
*   `$1`, `$2` ... `$9` 分别代表传递给脚本的第一个、第二个到第九个参数。
*   当参数位置超过9时，例如第十个参数，必须使用大括号 `{}` 将其括起来，即 `${10}`，以避免Shell将 `$10` 误解为 `$1` 后面跟着一个字符 `0`。

![](img/f7fb363aa4c41e4148874df15fbb26e8_13.png)

![](img/f7fb363aa4c41e4148874df15fbb26e8_15.png)

![](img/f7fb363aa4c41e4148874df15fbb26e8_16.png)

![](img/f7fb363aa4c41e4148874df15fbb26e8_18.png)

![](img/f7fb363aa4c41e4148874df15fbb26e8_20.png)

我们通过一个脚本示例来理解 `$0`：
```bash
#!/bin/bash
echo "The zeroth parameter is set to $0"
```
运行此脚本，`$0` 会输出脚本的完整路径。如果只想获取文件名（不包含路径），可以使用 `basename` 命令：
```bash
#!/bin/bash
np=$(basename $0)
echo "The zeroth parameter is set to $np"
```

![](img/f7fb363aa4c41e4148874df15fbb26e8_21.png)

![](img/f7fb363aa4c41e4148874df15fbb26e8_23.png)

## 处理带空格的参数 📦

![](img/f7fb363aa4c41e4148874df15fbb26e8_25.png)

![](img/f7fb363aa4c41e4148874df15fbb26e8_27.png)

当传递给脚本的参数包含空格时，需要特别注意。如果不加引号，Shell会将空格视为参数的分隔符。

![](img/f7fb363aa4c41e4148874df15fbb26e8_29.png)

![](img/f7fb363aa4c41e4148874df15fbb26e8_31.png)

![](img/f7fb363aa4c41e4148874df15fbb26e8_33.png)

例如，脚本 `test2.sh` 内容如下：
```bash
#!/bin/bash
echo "Hello $1, welcome to the class."
```
如果执行 `./test2.sh Roy Xia`，Shell会将 `Roy` 和 `Xia` 视为两个独立的参数，`$1` 的值是 `Roy`。为了将 `Roy Xia` 作为一个整体参数传递，需要使用引号：
```bash
./test2.sh "Roy Xia"
```
这样，`$1` 的值就是完整的 `Roy Xia`。

![](img/f7fb363aa4c41e4148874df15fbb26e8_35.png)

![](img/f7fb363aa4c41e4148874df15fbb26e8_37.png)

## `$*` 与 `$@` 的区别 🔄

![](img/f7fb363aa4c41e4148874df15fbb26e8_39.png)

![](img/f7fb363aa4c41e4148874df15fbb26e8_41.png)

`$*` 和 `$@` 都表示所有的位置参数，但它们在循环处理时有细微差别。

![](img/f7fb363aa4c41e4148874df15fbb26e8_43.png)

![](img/f7fb363aa4c41e4148874df15fbb26e8_45.png)

![](img/f7fb363aa4c41e4148874df15fbb26e8_47.png)

以下是展示两者区别的示例脚本：
```bash
#!/bin/bash
# 脚本1: 使用 $*
for i in "$*"
do
    echo $i
done

![](img/f7fb363aa4c41e4148874df15fbb26e8_49.png)

# 脚本2: 使用 $@
for j in "$@"
do
    echo $j
done
```
执行脚本 `./script.sh 1 2 3 4`：
*   使用 `$*` 时，所有参数被视为一个**整体单词**，因此会**横向输出**：`1 2 3 4`。
*   使用 `$@` 时，每个参数被视为**独立的**，因此会**纵向输出**：
    ```
    1
    2
    3
    4
    ```

![](img/f7fb363aa4c41e4148874df15fbb26e8_51.png)

## 参数数量 `$#` 与花括号操作 `{}` 📐

![](img/f7fb363aa4c41e4148874df15fbb26e8_53.png)

![](img/f7fb363aa4c41e4148874df15fbb26e8_55.png)

`$#` 是一个特殊的预定义变量，它表示传递给脚本的命令行参数的数量。这常用于验证脚本是否收到了预期数量的参数。

![](img/f7fb363aa4c41e4148874df15fbb26e8_57.png)

花括号 `{}` 在变量操作中非常强大，主要用于字符串的截取和替换。

定义一个变量：
```bash
file="/tmp/test/file.tar.gz"
```
以下是使用花括号进行字符串操作的方法：

![](img/f7fb363aa4c41e4148874df15fbb26e8_59.png)

![](img/f7fb363aa4c41e4148874df15fbb26e8_61.png)

![](img/f7fb363aa4c41e4148874df15fbb26e8_62.png)

![](img/f7fb363aa4c41e4148874df15fbb26e8_64.png)

**1. 从左边开始删除（最小匹配 vs 最大匹配）**
*   `echo ${file#*/}`: 删除第一个 `/` 及其左边的部分，结果：`tmp/test/file.tar.gz`
*   `echo ${file##*/}`: 删除最后一个 `/` 及其左边的部分，结果：`file.tar.gz`
*   `echo ${file#*.}`: 删除第一个 `.` 及其左边的部分，结果：`tar.gz`
*   `echo ${file##*.}`: 删除最后一个 `.` 及其左边的部分，结果：`gz`

![](img/f7fb363aa4c41e4148874df15fbb26e8_66.png)

**2. 从右边开始删除（最小匹配 vs 最大匹配）**
*   `echo ${file%/*}`: 删除最后一个 `/` 及其右边的部分，结果：`/tmp/test`
*   `echo ${file%%/*}`: 删除第一个 `/` 及其右边的部分，结果：（空）
*   `echo ${file%.*}`: 删除最后一个 `.` 及其右边的部分，结果：`/tmp/test/file.tar`
*   `echo ${file%%.*}`: 删除第一个 `.` 及其右边的部分，结果：`/tmp/test/file`

![](img/f7fb363aa4c41e4148874df15fbb26e8_68.png)

**记忆口诀**：`#` 表示从左边删除（在键盘上 `#` 在 `$` 左边），`%` 表示从右边删除（在键盘上 `%` 在 `$` 右边）。单个符号是最小匹配，两个符号是最大匹配。

![](img/f7fb363aa4c41e4148874df15fbb26e8_70.png)

![](img/f7fb363aa4c41e4148874df15fbb26e8_72.png)

**3. 提取子串和变量替换**
*   提取子串：`${变量:起始位置:长度}`
    *   `echo ${file:0:5}`: 从第0个字符开始，提取5个字符，结果：`/tmp/`
    *   `echo ${file:5:5}`: 从第5个字符开始，提取5个字符，结果：`test/`
*   变量替换：`${变量/旧模式/新模式}`
    *   `echo ${file/s/S}`: 将第一个小写 `s` 替换为大写 `S`，结果：`/tmp/teSt/file.tar.gz`
    *   `echo ${file//s/S}`: 将所有小写 `s` 替换为大写 `S`，结果：`/tmp/teSt/file.tar.gz`

![](img/f7fb363aa4c41e4148874df15fbb26e8_74.png)

![](img/f7fb363aa4c41e4148874df15fbb26e8_76.png)

![](img/f7fb363aa4c41e4148874df15fbb26e8_78.png)

## 退出状态码 `$?` 与 `exit` 命令 🚪

![](img/f7fb363aa4c41e4148874df15fbb26e8_80.png)

![](img/f7fb363aa4c41e4148874df15fbb26e8_81.png)

在Linux中，每个命令执行后都会返回一个退出状态码（Return Code 或 Exit Code）。这个状态码是一个0-255之间的整数值，存储在特殊变量 `$?` 中。

![](img/f7fb363aa4c41e4148874df15fbb26e8_83.png)

![](img/f7fb363aa4c41e4148874df15fbb26e8_85.png)

![](img/f7fb363aa4c41e4148874df15fbb26e8_87.png)

![](img/f7fb363aa4c41e4148874df15fbb26e8_89.png)

![](img/f7fb363aa4c41e4148874df15fbb26e8_91.png)

以下是常见的退出状态码含义：
*   **0**: 命令成功执行。
*   **1**: 一般性未知错误（例如，参数不存在）。
*   **2**: 不适合的Shell命令（例如，目录不存在）。
*   **126**: 命令不可执行（例如，文件没有执行权限）。
*   **127**: 命令未找到。
*   **130**: 命令被 `Ctrl+C` (SIGINT) 中断。

![](img/f7fb363aa4c41e4148874df15fbb26e8_93.png)

![](img/f7fb363aa4c41e4148874df15fbb26e8_95.png)

我们可以通过 `$?` 立即检查上一条命令的执行结果：
```bash
ls /tmp
echo $? # 输出 0，表示成功
ls /nonexistent_dir
echo $? # 输出 2，表示目录不存在
```

![](img/f7fb363aa4c41e4148874df15fbb26e8_97.png)

![](img/f7fb363aa4c41e4148874df15fbb26e8_99.png)

在脚本中，可以使用 `exit` 命令主动退出并指定一个状态码。如果不指定，脚本将以最后一条命令的退出状态码作为自己的退出码。

![](img/f7fb363aa4c41e4148874df15fbb26e8_101.png)

![](img/f7fb363aa4c41e4148874df15fbb26e8_102.png)

![](img/f7fb363aa4c41e4148874df15fbb26e8_103.png)

![](img/f7fb363aa4c41e4148874df15fbb26e8_105.png)

示例脚本 `test3.sh`：
```bash
#!/bin/bash
for i in $(seq 1 5)
do
    echo $i
done
exit 1 # 主动指定退出状态码为1
```
执行该脚本后，即使循环成功执行，脚本的最终退出码也是我们指定的 `1`，而不是默认的 `0`。这允许我们在脚本中根据不同的错误条件返回不同的代码，便于外部程序调用和判断。

![](img/f7fb363aa4c41e4148874df15fbb26e8_106.png)

![](img/f7fb363aa4c41e4148874df15fbb26e8_108.png)

![](img/f7fb363aa4c41e4148874df15fbb26e8_110.png)

---

![](img/f7fb363aa4c41e4148874df15fbb26e8_111.png)

![](img/f7fb363aa4c41e4148874df15fbb26e8_113.png)

![](img/f7fb363aa4c41e4148874df15fbb26e8_115.png)

本节课中我们一起学习了Shell脚本编程的几个核心概念：特殊变量（`$0`, `$1`, `$#`, `$*`, `$@`）用于处理输入参数；花括号 `{}` 提供了强大的字符串操作能力；而退出状态码 `$?` 和 `exit` 命令则是控制脚本流程和与外部环境通信的重要工具。掌握这些基础知识是编写实用Shell脚本的关键。