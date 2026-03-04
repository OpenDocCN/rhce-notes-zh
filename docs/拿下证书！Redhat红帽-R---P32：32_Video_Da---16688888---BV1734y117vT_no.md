# Redhat红帽 RHCE8.0认证体系课程：P32：Day06_Day05课程回顾

![](img/e289abdfc07aabbee392506571781f90_0.png)

![](img/e289abdfc07aabbee392506571781f90_2.png)

![](img/e289abdfc07aabbee392506571781f90_3.png)

## 概述
在本节课中，我们将回顾Day05课程中的三道脚本练习题，并学习如何使用不同的方法来实现相同的目标。我们将重点分析`seq`命令、`rpm`查询以及备份脚本的编写，帮助初学者理解Shell脚本的基本逻辑和多种实现方式。

![](img/e289abdfc07aabbee392506571781f90_5.png)

---

![](img/e289abdfc07aabbee392506571781f90_7.png)

![](img/e289abdfc07aabbee392506571781f90_8.png)

## 第一题：使用脚本输出等差数列

![](img/e289abdfc07aabbee392506571781f90_10.png)

上一节我们介绍了Shell脚本的基础结构，本节中我们来看看如何输出一个等差数列。

题目要求是输出从2开始、步长为4、直到50的等差数列。最简便的方法是使用`seq`命令。

**核心命令公式如下：**
```bash
seq 起始值 步长 结束值
```

以下是实现该功能的一种方法：
```bash
#!/bin/bash
seq 2 4 50
```

![](img/e289abdfc07aabbee392506571781f90_12.png)

![](img/e289abdfc07aabbee392506571781f90_13.png)

运行此脚本将直接输出所需的数列。当然，使用`for`循环或`while`循环也能达到相同效果，只要能实现目标，方法可以多样。

![](img/e289abdfc07aabbee392506571781f90_14.png)

![](img/e289abdfc07aabbee392506571781f90_16.png)

---

![](img/e289abdfc07aabbee392506571781f90_17.png)

![](img/e289abdfc07aabbee392506571781f90_19.png)

![](img/e289abdfc07aabbee392506571781f90_20.png)

## 第二题：列出与内核相关软件包的安装日期

![](img/e289abdfc07aabbee392506571781f90_22.png)

![](img/e289abdfc07aabbee392506571781f90_24.png)

![](img/e289abdfc07aabbee392506571781f90_26.png)

在掌握了基础输出后，我们来看看如何获取系统信息。本题要求列出所有包含“kernel”关键字的软件包及其安装日期。

![](img/e289abdfc07aabbee392506571781f90_28.png)

核心思路是：先用`rpm -qa`命令查询所有包，用`grep`过滤出内核包，然后循环获取每个包的安装日期。

![](img/e289abdfc07aabbee392506571781f90_30.png)

**关键命令和代码块如下：**
```bash
#!/bin/bash
for package in $(rpm -qa | grep -i kernel)
do
    install_time=$(rpm -qi $package | grep "Install Date" | awk -F': ' '{print $2}')
    echo "$package was installed on $install_time"
done
```

这段脚本中：
1.  `rpm -qa | grep -i kernel` 获取所有内核相关包名。
2.  `rpm -qi $package` 查询指定包的详细信息。
3.  `awk -F‘: ’ ‘{print $2}’` 以“冒号+空格”为分隔符，提取出安装日期。

![](img/e289abdfc07aabbee392506571781f90_32.png)

注意，`rpm -qi`输出的“Install Date”字段后有一个空格，因此`awk`的分隔符需要设置为`: `才能正确提取。

![](img/e289abdfc07aabbee392506571781f90_33.png)

![](img/e289abdfc07aabbee392506571781f90_35.png)

![](img/e289abdfc07aabbee392506571781f90_37.png)

---

![](img/e289abdfc07aabbee392506571781f90_39.png)

## 第三题：编写自动化备份脚本

![](img/e289abdfc07aabbee392506571781f90_40.png)

最后，我们将综合运用变量和命令，完成一个实用的自动化备份脚本。

题目要求：将`/etc/sysconfig`目录备份到`/usr/local/backup`，备份文件以当前日期命名，并删除7天前的旧备份。

**以下是完整的脚本示例：**
```bash
#!/bin/bash
# 定义当前日期变量
current_date=$(date +%Y%m%d)
# 定义工作目录和备份源目录
backup_dir="/usr/local/backup"
source_dir="/etc/sysconfig"
# 执行备份压缩命令
tar -cjf "${backup_dir}/sysconfig_backup_${current_date}.tar.bz2" "$source_dir"
# 删除7天前的备份文件
find "$backup_dir" -name "sysconfig_backup_*" -mtime +7 -exec rm -f {} \;
```

脚本解析：
1.  `date +%Y%m%d` 生成`YYYYMMDD`格式的日期字符串。
2.  `tar -cjf` 使用bzip2格式创建压缩备份包。
3.  `find ... -mtime +7 -exec rm -f {} \;` 查找修改时间在7天以前（`-mtime +7`）的备份文件并删除。

![](img/e289abdfc07aabbee392506571781f90_42.png)

这个脚本的结构清晰，定义了变量，并依次执行备份和清理操作，是一个可用于生产环境的自动化运维脚本模板。

![](img/e289abdfc07aabbee392506571781f90_44.png)

---

## 总结
本节课中我们一起复习了三道Shell脚本练习题。
1.  第一题展示了使用`seq`命令高效生成数列。
2.  第二题演示了如何通过命令组合（`rpm`, `grep`, `awk`）来提取和处理系统信息。
3.  第三题则综合应用变量、日期命令和归档命令，完成了一个实用的自动化备份脚本。

![](img/e289abdfc07aabbee392506571781f90_46.png)

脚本的写法多种多样，核心在于清晰分析需求并选择合适命令。建议大家多加练习，这些技能在实际运维工作中能有效提升效率。