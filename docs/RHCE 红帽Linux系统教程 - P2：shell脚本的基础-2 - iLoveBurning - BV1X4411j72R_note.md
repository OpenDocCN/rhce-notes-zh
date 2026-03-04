# RHCE 红帽Linux系统教程：P2：Shell脚本基础-2 🐧

![](img/16d69ce2b1f4a69ee6eb5b99fdec1ae6_1.png)

![](img/16d69ce2b1f4a69ee6eb5b99fdec1ae6_2.png)

在本节课中，我们将要学习Shell脚本中变量的进阶知识，包括如何设置全局变量、理解位置变量和预定义变量的用法，以及如何进行简单的文件测试。这些概念是编写功能强大且灵活的Shell脚本的基础。

![](img/16d69ce2b1f4a69ee6eb5b99fdec1ae6_4.png)

![](img/16d69ce2b1f4a69ee6eb5b99fdec1ae6_5.png)

## 全局变量的设置

![](img/16d69ce2b1f4a69ee6eb5b99fdec1ae6_7.png)

![](img/16d69ce2b1f4a69ee6eb5b99fdec1ae6_8.png)

上一节我们介绍了局部变量的赋值，但局部变量只在当前Shell环境中生效。本节中我们来看看如何让变量在所有Shell环境中都生效。

![](img/16d69ce2b1f4a69ee6eb5b99fdec1ae6_10.png)

当你在当前Shell中设置一个变量后，切换到另一个Shell（子Shell）时，该变量将无法使用。这就像你精通Linux，但换了一台新服务器就不会操作了，这是不行的。

![](img/16d69ce2b1f4a69ee6eb5b99fdec1ae6_12.png)

![](img/16d69ce2b1f4a69ee6eb5b99fdec1ae6_13.png)

![](img/16d69ce2b1f4a69ee6eb5b99fdec1ae6_15.png)

![](img/16d69ce2b1f4a69ee6eb5b99fdec1ae6_16.png)

![](img/16d69ce2b1f4a69ee6eb5b99fdec1ae6_17.png)

为了解决这个问题，我们需要将局部变量设置为全局变量。以下是相关的命令：
*   `set`：用于设置和查看当前Shell的变量。
*   `env`：用于查看全局环境变量。

![](img/16d69ce2b1f4a69ee6eb5b99fdec1ae6_19.png)

![](img/16d69ce2b1f4a69ee6eb5b99fdec1ae6_21.png)

若要使一个变量在所有Shell解释器中生效，需要使用 `export` 命令将其设置为全局变量。

![](img/16d69ce2b1f4a69ee6eb5b99fdec1ae6_23.png)

**操作示例：**
首先，退出当前的子Shell环境。
```bash
exit
```
然后，使用 `export` 命令将变量设为全局。
```bash
export linux LINUX
```
设置完成后，进入一个新的子Shell进行验证，变量依然存在。
```bash
echo $linux
```

![](img/16d69ce2b1f4a69ee6eb5b99fdec1ae6_25.png)

此外，也可以直接为全局变量赋值。
```bash
export FQDN=www.xue.cn.com
```
之后在任何Shell环境中，都可以通过 `echo $FQDN` 来访问这个变量。请注意，Linux命令严格区分大小写。

![](img/16d69ce2b1f4a69ee6eb5b99fdec1ae6_27.png)

![](img/16d69ce2b1f4a69ee6eb5b99fdec1ae6_29.png)

## 位置变量

![](img/16d69ce2b1f4a69ee6eb5b99fdec1ae6_31.png)

![](img/16d69ce2b1f4a69ee6eb5b99fdec1ae6_33.png)

接下来，我们了解一下位置变量。位置变量是根据参数在命令行中的位置来引用的，例如 `$0`, `$1`, `$2` 等。

![](img/16d69ce2b1f4a69ee6eb5b99fdec1ae6_34.png)

![](img/16d69ce2b1f4a69ee6eb5b99fdec1ae6_35.png)

![](img/16d69ce2b1f4a69ee6eb5b99fdec1ae6_37.png)

`$0` 代表命令或脚本本身的名字，`$1` 代表第一个参数，`$2` 代表第二个参数，依此类推。

![](img/16d69ce2b1f4a69ee6eb5b99fdec1ae6_39.png)

为了更直观地理解，我们编写一个简单的脚本来演示。

![](img/16d69ce2b1f4a69ee6eb5b99fdec1ae6_41.png)

![](img/16d69ce2b1f4a69ee6eb5b99fdec1ae6_43.png)

**操作示例：**
创建一个用于求和的脚本 `weizhi.sh`。
```bash
vim weizhi.sh
```
在脚本中输入以下内容：
```bash
#!/bin/bash
sum=$(($1 + $2))
echo "$1 + $2 = $sum"
```
保存并退出后，执行该脚本并传入两个参数。
```bash
bash weizhi.sh 1 2
```
执行结果会显示 “1 + 2 = 3”。在脚本中，`$1` 被替换为第一个参数 `1`，`$2` 被替换为第二个参数 `2`。

![](img/16d69ce2b1f4a69ee6eb5b99fdec1ae6_45.png)

## 预定义变量

![](img/16d69ce2b1f4a69ee6eb5b99fdec1ae6_47.png)

![](img/16d69ce2b1f4a69ee6eb5b99fdec1ae6_48.png)

![](img/16d69ce2b1f4a69ee6eb5b99fdec1ae6_49.png)

![](img/16d69ce2b1f4a69ee6eb5b99fdec1ae6_50.png)

除了位置变量，Shell还提供了一些有特殊含义的预定义变量。

![](img/16d69ce2b1f4a69ee6eb5b99fdec1ae6_51.png)

![](img/16d69ce2b1f4a69ee6eb5b99fdec1ae6_52.png)

![](img/16d69ce2b1f4a69ee6eb5b99fdec1ae6_53.png)

以下是几个常用的预定义变量：
*   `$#`：表示命令行中位置参数的个数。
*   `$*`：表示所有位置参数的具体内容。
*   `$?`：表示上一条命令执行后的返回状态。`0`表示成功，非`0`（通常是1-127）表示失败。
*   `$0`：表示当前正在执行的脚本或程序的名称。

![](img/16d69ce2b1f4a69ee6eb5b99fdec1ae6_55.png)

![](img/16d69ce2b1f4a69ee6eb5b99fdec1ae6_57.png)

我们通过一个备份脚本来理解它们的用法。

![](img/16d69ce2b1f4a69ee6eb5b99fdec1ae6_59.png)

**操作示例：**
创建一个备份脚本 `backup.sh`。
```bash
vim backup.sh
```
脚本内容如下：
```bash
#!/bin/bash
file=backup-$(date +%Y%m%d).tar.gz
tar -czf $file $*
echo “$0 共完成了 $# 个备份对象的打包。”
echo “打包的内容包括：$*”
```
为脚本添加执行权限并运行。
```bash
chmod +x backup.sh
./backup.sh /home /opt
```
执行后，`$0` 会输出脚本名，`$#` 会输出参数个数（例如2），`$*` 会输出所有参数内容（例如 `/home /opt`）。

![](img/16d69ce2b1f4a69ee6eb5b99fdec1ae6_61.png)

## 文件测试

![](img/16d69ce2b1f4a69ee6eb5b99fdec1ae6_63.png)

![](img/16d69ce2b1f4a69ee6eb5b99fdec1ae6_64.png)

在脚本中，经常需要判断文件或目录的状态，这就需要用到文件测试。

![](img/16d69ce2b1f4a69ee6eb5b99fdec1ae6_66.png)

![](img/16d69ce2b1f4a69ee6eb5b99fdec1ae6_68.png)

文件测试可以判断指定路径是文件还是目录，以及是否具有读、写、执行权限或是否存在。

![](img/16d69ce2b1f4a69ee6eb5b99fdec1ae6_69.png)

![](img/16d69ce2b1f4a69ee6eb5b99fdec1ae6_71.png)

![](img/16d69ce2b1f4a69ee6eb5b99fdec1ae6_73.png)

![](img/16d69ce2b1f4a69ee6eb5b99fdec1ae6_75.png)

常用的测试选项如下：
*   `-d`：测试是否为目录或目录是否存在。
*   `-e`：测试目录或文件是否存在。
*   `-f`：测试是否为文件或文件是否存在。
*   `-r`, `-w`, `-x`：分别测试当前用户是否拥有读、写、执行权限。

![](img/16d69ce2b1f4a69ee6eb5b99fdec1ae6_77.png)

![](img/16d69ce2b1f4a69ee6eb5b99fdec1ae6_79.png)

测试后，可以通过预定义变量 `$?` 来获取返回状态，以判断条件是否成立。

![](img/16d69ce2b1f4a69ee6eb5b99fdec1ae6_81.png)

![](img/16d69ce2b1f4a69ee6eb5b99fdec1ae6_83.png)

**操作示例：**
判断 `/media/cdrom` 目录是否存在。
使用方括号 `[]` 进行测试（注意括号两边必须有空格）。
```bash
[ -d /media/cdrom ]
echo $?
```
如果返回值为非`0`，表示目录不存在。使用 `test` 命令效果相同。
```bash
test -d /media/cdrom
echo $?
```
创建该目录后再次测试，返回值将变为`0`，表示目录已存在。
```bash
mkdir -p /media/cdrom
[ -d /media/cdrom ] && echo “目录存在”
```
这里的 `&&` 是逻辑“与”操作符，只有前面的命令执行成功（返回`0`），后面的 `echo` 命令才会执行。

![](img/16d69ce2b1f4a69ee6eb5b99fdec1ae6_84.png)

---

![](img/16d69ce2b1f4a69ee6eb5b99fdec1ae6_86.png)

![](img/16d69ce2b1f4a69ee6eb5b99fdec1ae6_87.png)

![](img/16d69ce2b1f4a69ee6eb5b99fdec1ae6_89.png)

![](img/16d69ce2b1f4a69ee6eb5b99fdec1ae6_91.png)

本节课中我们一起学习了Shell脚本中关于变量的核心知识。我们掌握了如何通过 `export` 设置全局变量，理解了位置变量（`$1`, `$2`...）和预定义变量（`$#`, `$*`, `$?`, `$0`）在脚本中的重要作用，并初步了解了如何使用文件测试选项（`-d`, `-e`, `-f`）和逻辑操作符 `&&` 来增强脚本的逻辑判断能力。这些是构建更复杂自动化脚本的基石。