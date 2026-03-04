# Linux运维教程：第4章：Vim编辑器与Shell命令脚本（下）📝

在本节课中，我们将要学习Shell脚本编程中的高级部分，包括条件测试语句、循环语句以及计划任务服务。通过学习，你将能够编写更智能、更自动化的脚本来满足复杂的运维工作需求。

![](img/95b3569340e4532ccf651bdc554a18d0_1.png)

---

## 条件测试语句 🔍

![](img/95b3569340e4532ccf651bdc554a18d0_3.png)

![](img/95b3569340e4532ccf651bdc554a18d0_5.png)

上一节我们介绍了如何编写一个简单的顺序执行的脚本。本节中我们来看看如何让脚本根据不同的条件执行不同的命令，这需要使用条件测试语句。

条件测试语句允许脚本根据命令执行的成功与否（或变量值的比较结果）来决定下一步的操作。它主要分为三种结构：单分支、双分支和多分支。

![](img/95b3569340e4532ccf651bdc554a18d0_7.png)

![](img/95b3569340e4532ccf651bdc554a18d0_9.png)

### 单分支if语句

![](img/95b3569340e4532ccf651bdc554a18d0_11.png)

![](img/95b3569340e4532ccf651bdc554a18d0_13.png)

单分支结构仅在条件成立时执行预设的命令序列。

**基本格式如下：**
```bash
if [ 条件测试表达式 ]
then
    命令序列
fi
```

以下是单分支if语句的一个应用示例。该脚本判断一个目录是否存在，若不存在则创建它。

```bash
#!/bin/bash
# 脚本声明
DIR="/home/wwwroot"
# 条件测试：判断目录是否存在
if [ ! -d $DIR ]
then
    mkdir -p $DIR
fi
```
*   `[ ! -d $DIR ]`：`-d`用于测试`$DIR`变量代表的路径是否为一个目录。`!`是逻辑“非”操作符，因此整个表达式意为“如果目录不存在”。
*   若条件成立（即目录不存在），则执行`mkdir -p $DIR`命令创建目录。
*   `fi`是`if`语句的结束标记。

![](img/95b3569340e4532ccf651bdc554a18d0_15.png)

### 双分支if语句

![](img/95b3569340e4532ccf651bdc554a18d0_17.png)

双分支结构在条件成立或不成立时，分别执行不同的命令序列。

![](img/95b3569340e4532ccf651bdc554a18d0_19.png)

**基本格式如下：**
```bash
if [ 条件测试表达式 ]
then
    命令序列1
else
    命令序列2
fi
```

以下是一个双分支if语句的示例。该脚本通过ping命令测试主机的网络连通性，并根据结果输出不同信息。

```bash
#!/bin/bash
# 使用ping命令测试主机连通性，并将输出信息重定向到“黑洞文件”/dev/null
ping -c 3 -i 0.2 -W 3 $1 &> /dev/null
# 通过$?变量获取上一条命令的返回值，0通常表示成功
if [ $? -eq 0 ]
then
    echo "$1 is online."
else
    echo "$1 is offline."
fi
```
*   `ping -c 3 -i 0.2 -W 3 $1 &> /dev/null`：`-c`指定次数，`-i`指定间隔，`-W`指定超时时间。`&> /dev/null`将命令的标准输出和错误输出都重定向到`/dev/null`（一个特殊的“空设备”文件），使屏幕保持整洁。
*   `$?`是一个特殊变量，用于获取上一个命令的退出状态。在Linux中，退出状态为0通常表示命令执行成功，非0值表示失败。
*   脚本执行时，需要在后面跟一个IP地址作为参数（`$1`）。

![](img/95b3569340e4532ccf651bdc554a18d0_21.png)

### 多分支if语句

![](img/95b3569340e4532ccf651bdc554a18d0_23.png)

多分支结构允许进行多次条件判断，适用于更复杂的场景。

**基本格式如下：**
```bash
if [ 条件测试表达式1 ]
then
    命令序列1
elif [ 条件测试表达式2 ]
then
    命令序列2
else
    命令序列3
fi
```

![](img/95b3569340e4532ccf651bdc554a18d0_25.png)

![](img/95b3569340e4532ccf651bdc554a18d0_27.png)

以下是一个多分支if语句的示例。该脚本读取用户输入的成绩分数，并判断其所属的等级。

![](img/95b3569340e4532ccf651bdc554a18d0_29.png)

![](img/95b3569340e4532ccf651bdc554a18d0_31.png)

```bash
#!/bin/bash
# 使用read命令读取用户输入，-p参数用于显示提示信息
read -p "Enter your score (0-300): " SCORE
if [ $SCORE -ge 210 ] && [ $SCORE -le 300 ]
then
    echo "Your score is pass."
elif [ $SCORE -ge 0 ] && [ $SCORE -lt 210 ]
then
    echo "Your score is failure."
else
    echo "Input error."
fi
```
*   `read -p "提示信息" 变量名`：`-p`选项允许在读取输入前显示提示信息。
*   `[ $SCORE -ge 210 ] && [ $SCORE -le 300 ]`：`-ge`表示大于等于，`-le`表示小于等于。`&&`是逻辑“与”操作符，表示两个条件必须同时成立。
*   `elif`是`else if`的缩写，用于添加额外的条件分支。

![](img/95b3569340e4532ccf651bdc554a18d0_33.png)

![](img/95b3569340e4532ccf651bdc554a18d0_35.png)

---

![](img/95b3569340e4532ccf651bdc554a18d0_37.png)

## 循环语句 🔄

在脚本中，有时需要重复执行某些操作，这时就需要用到循环语句。本节中我们来看看两种常用的循环：for循环和while循环。

### for循环语句

for循环语句用于遍历一个列表（如一系列字符串、文件中的行等）中的每个项目，并对每个项目执行相同的命令序列。

**基本格式如下：**
```bash
for 变量名 in 取值列表
do
    命令序列
done
```

以下是for循环语句的一个应用示例。该脚本读取一个用户名单文件，并批量创建系统用户。

```bash
#!/bin/bash
# 读取用户名单文件，逐行赋值给UNAME变量
for UNAME in $(cat /root/users.txt)
do
    # 创建用户，并将输出信息重定向
    useradd $UNAME &> /dev/null
    # 为用户设置密码（此处密码为硬编码，实际应用应更安全）
    echo "redhat" | passwd --stdin $UNAME &> /dev/null
    # 判断密码设置是否成功
    if [ $? -eq 0 ]
    then
        echo "$UNAME is created successfully."
    else
        echo "$UNAME is created unsuccessfully."
    fi
done
```
*   `$(cat /root/users.txt)`：命令替换。先执行`cat /root/users.txt`命令，然后将其输出结果（文件内容）作为列表供for循环遍历。
*   `echo "redhat" | passwd --stdin $UNAME`：通过管道`|`将密码字符串传递给`passwd`命令的`--stdin`选项，实现非交互式设置密码。

### while循环语句

while循环语句根据条件测试表达式的返回值来决定是否继续循环。只要条件为真（返回值为0），就持续执行循环体内的命令序列。

![](img/95b3569340e4532ccf651bdc554a18d0_39.png)

**基本格式如下：**
```bash
while [ 条件测试表达式 ]
do
    命令序列
done
```

以下是一个while循环结合条件判断的趣味示例。该脚本生成一个随机数，让用户猜测，直到猜中为止。

```bash
#!/bin/bash
# 生成一个0-999的随机数作为目标价格
PRICE=$(expr $RANDOM % 1000)
TIMES=0
echo "The target price is between 0 and 999. Guess it!"
# 条件永远为真，构成无限循环，依靠循环体内的break退出
while true
do
    read -p "Please enter your guess: " INT
    let TIMES++
    if [ $INT -eq $PRICE ]
    then
        echo "Congratulations! You guessed it in $TIMES times."
        exit 0
    elif [ $INT -gt $PRICE ]
    then
        echo "Too high!"
    else
        echo "Too low!"
    fi
done
```
*   `PRICE=$(expr $RANDOM % 1000)`：`$RANDOM`是一个生成随机整数的环境变量。`expr`命令进行数学运算，`%`是取余运算符，因此`$RANDOM % 1000`会得到一个0到999之间的随机数。
*   `while true`：设置一个始终成立的条件，构成无限循环。
*   `exit 0`：当猜中时，使用`exit`命令退出脚本，并返回状态码0。

![](img/95b3569340e4532ccf651bdc554a18d0_41.png)

### case条件语句

![](img/95b3569340e4532ccf651bdc554a18d0_43.png)

case条件语句主要用于匹配一个变量的值是否等于某个模式（可以是字符串或通配符），非常适合处理用户输入的选择或简单的字符匹配，结构比多重的if-elif更清晰。

![](img/95b3569340e4532ccf651bdc554a18d0_45.png)

**基本格式如下：**
```bash
case 变量值 in
模式1)
    命令序列1
    ;;
模式2)
    命令序列2
    ;;
*)
    默认命令序列
    ;;
esac
```

以下是case语句的一个示例。该脚本判断用户输入的是字母、数字还是其他字符。

```bash
#!/bin/bash
read -p "Please enter a character: " KEY
case "$KEY" in
[a-z]|[A-Z])
    echo "You entered a letter."
    ;;
[0-9])
    echo "You entered a number."
    ;;
*)
    echo "You entered a special character or something else."
    ;;
esac
```
*   每个模式分支以`)`结束，对应的命令序列以`;;`结束。
*   `[a-z]|[A-Z]`：使用`|`表示“或”，匹配任意小写或大写字母。
*   `*`：通配符，匹配任何值，通常作为默认情况放在最后。

---

## 计划任务服务 ⏰

脚本编写好后，我们通常希望它在特定的时间自动运行，而不是手动执行。Linux系统提供了计划任务服务来实现这一功能。本节中我们来看看两种计划任务：at命令和cron服务。

### at一次性计划任务

![](img/95b3569340e4532ccf651bdc554a18d0_47.png)

![](img/95b3569340e4532ccf651bdc554a18d0_49.png)

`at`命令用于创建一次性计划任务，任务执行一次后便自动删除。它适用于处理临时性的、单次的工作。

以下是使用`at`命令的步骤：

1.  **设置任务**：使用`at <时间>`格式指定任务运行时间，然后输入要执行的命令，按`Ctrl+D`保存退出。
    ```bash
    # 示例：在19:30重启系统
    [root@linuxprobe ~]# at 19:30
    at> reboot
    at> <EOT> # 此处按Ctrl+D
    job 3 at Mon Apr 15 19:30:00 2024
    ```
2.  **查看任务**：使用`at -l`或`atq`查看等待执行的任务列表。
    ```bash
    [root@linuxprobe ~]# at -l
    3       Mon Apr 15 19:30:00 2024 a root
    ```
3.  **删除任务**：使用`atrm <任务编号>`删除指定的计划任务。
    ```bash
    [root@linuxprobe ~]# atrm 3
    [root@linuxprobe ~]# at -l
    # 列表为空
    ```

### cron周期性计划任务

![](img/95b3569340e4532ccf651bdc554a18d0_51.png)

`cron`服务用于创建周期性计划任务，可以按分钟、小时、日、月、星期等周期重复执行。这是系统自动化运维的核心工具之一。

![](img/95b3569340e4532ccf651bdc554a18d0_53.png)

![](img/95b3569340e4532ccf651bdc554a18d0_55.png)

管理cron计划任务有两种主要方式：
*   **使用`crontab`命令**：这是推荐的方式，因为它有语法检查功能。
    *   `crontab -e`：编辑当前用户的cron任务。
    *   `crontab -l`：列出当前用户的cron任务。
    *   `crontab -r`：删除当前用户的所有cron任务（慎用！）。
*   **编辑配置文件**：任务最终保存在`/var/spool/cron/`目录下以用户名命名的文件中，但直接编辑容易出错。

![](img/95b3569340e4532ccf651bdc554a18d0_57.png)

**cron任务格式如下：**
```
分 时 日 月 星期 命令
```
*   **字段说明**：每个字段代表一个时间单位。可以使用数字、`*`（通配符，表示任意值）、`-`（范围，如`1-5`）、`,`（列表，如`1,3,5`）和`/`（步长，如`*/2`表示每2个单位）。
*   **注意事项**：“日”和“星期”字段最好不要同时指定，因为逻辑关系可能产生混淆。命令最好使用**绝对路径**。

![](img/95b3569340e4532ccf651bdc554a18d0_59.png)

![](img/95b3569340e4532ccf651bdc554a18d0_61.png)

以下是几个cron任务配置示例：

![](img/95b3569340e4532ccf651bdc554a18d0_63.png)

![](img/95b3569340e4532ccf651bdc554a18d0_65.png)

![](img/95b3569340e4532ccf651bdc554a18d0_67.png)

![](img/95b3569340e4532ccf651bdc554a18d0_69.png)

```bash
# 示例1：每天凌晨2点30分执行备份脚本
30 2 * * * /usr/sbin/backup.sh

# 示例2：每周一和周五的下午4点发送报告
0 16 * * 1,5 /usr/bin/send_report.py

![](img/95b3569340e4532ccf651bdc554a18d0_71.png)

# 示例3：每10分钟检查一次系统负载
*/10 * * * * /usr/bin/check_load

![](img/95b3569340e4532ccf651bdc554a18d0_73.png)

# 示例4：每月1号凌晨1点清理日志
0 1 1 * * /bin/find /var/log -name "*.log" -mtime +30 -exec rm {} \;
```

![](img/95b3569340e4532ccf651bdc554a18d0_75.png)

**重要提示**：在配置cron任务前，请确保`crond`服务已启动并设置为开机自启。
```bash
systemctl start crond
systemctl enable crond
systemctl status crond
```

---

## 总结 📚

![](img/95b3569340e4532ccf651bdc554a18d0_77.png)

![](img/95b3569340e4532ccf651bdc554a18d0_79.png)

本节课中我们一起学习了Shell脚本编程的核心控制结构和Linux的计划任务服务。

![](img/95b3569340e4532ccf651bdc554a18d0_81.png)

![](img/95b3569340e4532ccf651bdc554a18d0_83.png)

![](img/95b3569340e4532ccf651bdc554a18d0_85.png)

*   **条件测试语句（if、case）**：让脚本具备了判断能力，可以根据不同的情况执行不同的逻辑分支。
*   **循环语句（for、while）**：让脚本能够重复执行某些操作，极大地提高了处理批量任务的效率。
*   **计划任务服务（at、cron）**：让脚本能够在指定的时间自动运行，是实现系统运维自动化的基石。

![](img/95b3569340e4532ccf651bdc554a18d0_87.png)

![](img/95b3569340e4532ccf651bdc554a18d0_89.png)

![](img/95b3569340e4532ccf651bdc554a18d0_91.png)

通过将条件判断、循环与计划任务相结合，你可以编写出非常强大和智能的自动化运维脚本，例如自动备份、日志轮转、服务监控和资源清理等。请务必多加练习，亲手编写和调试脚本，这是掌握Shell编程的最佳途径。