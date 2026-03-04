# Redhat红帽 RHCE8.0认证体系课程：P27：Day06_Day05课程回顾与练习讲解 📚

在本节课中，我们将回顾并详细讲解Day05课程中布置的三道Shell脚本练习题。我们将逐一分析题目要求，展示多种可行的解决方案，并解释其中的核心命令和逻辑。通过本次学习，你将巩固Shell脚本编写、循环控制、命令替换以及文件操作等关键技能。

![](img/971690251da554ab2afa0e24eed7a534_1.png)

---

![](img/971690251da554ab2afa0e24eed7a534_3.png)

![](img/971690251da554ab2afa0e24eed7a534_4.png)

![](img/971690251da554ab2afa0e24eed7a534_6.png)

## 练习一：打印等差数列 🔢

上一节我们介绍了Shell脚本的基础结构，本节中我们来看看如何完成第一道练习题：使用Shell脚本打印出2到50之间步长为4的等差数列（即2, 6, 10, ..., 50）。

以下是几种常见的实现方法：

*   **方法一：使用 `seq` 命令**
    这是最简便直接的方法。`seq` 命令可以生成一个数字序列。
    ```bash
    # 使用 seq 命令，起始值为2，步长为4，结束值为50
    seq 2 4 50
    ```
    或者将其写入脚本：
    ```bash
    #!/bin/bash
    seq 2 4 50
    ```

![](img/971690251da554ab2afa0e24eed7a534_8.png)

![](img/971690251da554ab2afa0e24eed7a534_10.png)

*   **方法二：使用 `for` 循环**
    利用 `for` 循环和算术扩展来生成序列。
    ```bash
    #!/bin/bash
    for ((i=2; i<=50; i+=4)); do
        echo $i
    done
    ```

![](img/971690251da554ab2afa0e24eed7a534_11.png)

![](img/971690251da554ab2afa0e24eed7a534_12.png)

*   **方法三：使用 `while` 循环**
    使用 `while` 循环配合条件判断来实现。
    ```bash
    #!/bin/bash
    i=2
    while [ $i -le 50 ]; do
        echo $i
        i=$((i+4))
    done
    ```

![](img/971690251da554ab2afa0e24eed7a534_14.png)

![](img/971690251da554ab2afa0e24eed7a534_16.png)

![](img/971690251da554ab2afa0e24eed7a534_18.png)

**核心要点**：同一个脚本任务可以有多种写法，只要能达到预期结果即可。`seq` 命令在处理这类等差数列问题时通常最高效。

![](img/971690251da554ab2afa0e24eed7a534_20.png)

![](img/971690251da554ab2afa0e24eed7a534_22.png)

![](img/971690251da554ab2afa0e24eed7a534_24.png)

---

![](img/971690251da554ab2afa0e24eed7a534_26.png)

![](img/971690251da554ab2afa0e24eed7a534_28.png)

## 练习二：查询软件包安装日期 📅

![](img/971690251da554ab2afa0e24eed7a534_30.png)

![](img/971690251da554ab2afa0e24eed7a534_32.png)

在掌握了基础输出后，我们进一步学习如何处理命令的输出结果。第二道题要求：编写一个Shell脚本，列出所有包含“kernel”关键字的RPM软件包及其安装日期，输出格式参考“软件包名 was installed on 安装日期”。

![](img/971690251da554ab2afa0e24eed7a534_34.png)

以下是解决此问题的关键步骤和一种实现方法：

*   **思路分析**：
    1.  使用 `rpm -qa | grep kernel` 命令找出所有相关的软件包名。
    2.  通过循环遍历每一个软件包名。
    3.  针对每个包，使用 `rpm -qi` 查询其详细信息，并从中提取“Install Date”这一行。
    4.  使用 `awk` 或 `cut` 等工具对提取出的字符串进行格式化处理。

![](img/971690251da554ab2afa0e24eed7a534_36.png)

*   **脚本示例**：
    ```bash
    #!/bin/bash
    # 遍历所有包含‘kernel’的软件包
    for package in $(rpm -qa | grep -i kernel); do
        # 查询该包的详细信息，并过滤出‘Install Date’行，然后使用awk提取日期部分
        # ‘-F:’ 表示以冒号分隔，`{print $2}` 表示打印第二个字段（即冒号后的内容）
        install_date=$(rpm -qi $package | grep "Install Date" | awk -F: '{print $2}')
        # 输出结果
        echo "$package was installed on$install_date"
    done
    ```
    **注意**：`Install Date:` 后面有一个空格，因此使用 `awk -F: ‘{print $2}’` 可以正确提取出日期。也可以使用 `awk -F‘: ’ ‘{print $2}’`（分隔符设为“冒号+空格”）来更精确地匹配。

![](img/971690251da554ab2afa0e24eed7a534_37.png)

![](img/971690251da554ab2afa0e24eed7a534_38.png)

**核心要点**：本题练习了**命令替换** `$()`、**循环遍历命令输出结果**以及使用文本处理工具（如`awk`）解析格式化数据。

---

![](img/971690251da554ab2afa0e24eed7a534_40.png)

## 练习三：自动化备份与清理 🗃️

最后，我们将综合运用变量、日期命令和文件操作，完成一个实用的自动化备份脚本。第三道题要求：编写脚本，将`/etc/sysconfig`目录备份到`/usr/local/backup`目录下，备份文件名为`sysconfig_back_当前日期.tar.bz2`，并删除该目录下7天前的备份文件。

以下是实现此需求的详细步骤和脚本：

![](img/971690251da554ab2afa0e24eed7a534_42.png)

*   **思路分析**：
    1.  **定义变量**：定义备份日期、工作目录、源目录和目标目录路径，使脚本更清晰、易修改。
    2.  **创建备份**：使用 `tar` 命令进行压缩打包（`cjf` 参数表示创建bzip2压缩的tar包）。
    3.  **清理旧备份**：使用 `find` 命令定位并删除7天前（`-mtime +7`）的备份文件。

![](img/971690251da554ab2afa0e24eed7a534_44.png)

*   **脚本示例**：
    ```bash
    #!/bin/bash
    # 1. 定义变量
    backup_date=$(date +%Y%m%d)                 # 获取当前日期，格式为20220101
    work_dir="/usr/local/backup"                # 备份文件存放目录
    source_dir="/etc/sysconfig"                 # 需要备份的源目录
    destination="${work_dir}/sysconfig_back_${backup_date}.tar.bz2" # 备份文件完整路径

    # 2. 执行备份
    tar cjf "$destination" "$source_dir"        # 创建压缩备份包

    # 3. 清理7天前的旧备份
    # 在work_dir目录下，查找名称匹配‘sysconfig_back_*.tar.bz2’且修改时间在7天前的文件，并删除
    find "$work_dir" -name "sysconfig_back_*.tar.bz2" -mtime +7 -exec rm -f {} \;
    ```

![](img/971690251da554ab2afa0e24eed7a534_46.png)

![](img/971690251da554ab2afa0e24eed7a534_48.png)

![](img/971690251da554ab2afa0e24eed7a534_50.png)

**核心命令解释**：
*   `date +%Y%m%d`：**格式化输出当前日期**。
*   `tar cjf <目标文件> <源目录>`：**创建bzip2压缩的tar归档文件**。
*   `find <路径> -name <模式> -mtime +N -exec rm -f {} \;`：**查找并删除N天前修改过的文件**。

![](img/971690251da554ab2afa0e24eed7a534_52.png)

**核心要点**：这是一个非常实用的自动化运维脚本原型，涵盖了**变量定义**、**日期处理**、**文件打包压缩**和**定时清理**等关键操作。在实际生产中，可以在此基础之上添加日志记录、错误处理、邮件通知等功能。

---

![](img/971690251da554ab2afa0e24eed7a534_54.png)

![](img/971690251da554ab2afa0e24eed7a534_56.png)

![](img/971690251da554ab2afa0e24eed7a534_57.png)

本节课中我们一起学习了三道Shell脚本练习题，分别涵盖了序列生成、信息查询提取和自动化备份任务。关键点在于理解需求、灵活运用Shell命令（如`seq`， `rpm`， `date`， `tar`， `find`）及其组合，并掌握变量使用、循环控制和命令输出处理等核心编程概念。多练习、多思考不同的实现方式，是提升Shell脚本编写能力的最佳途径。