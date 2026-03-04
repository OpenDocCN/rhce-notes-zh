# Linux免费公开课1：好用命令系列一

![](img/aae8b079d175d929da22647ee293d23d_0.png)

![](img/aae8b079d175d929da22647ee293d23d_2.png)

在本节课中，我们将学习Linux系统中一些实用但可能不常被提及的命令。这些命令分为三类：系统工作类命令、文本文件操作类命令和免交互命令。我们将通过具体的例子来理解每个命令的用途和用法，帮助初学者更好地掌握。

![](img/aae8b079d175d929da22647ee293d23d_4.png)

![](img/aae8b079d175d929da22647ee293d23d_5.png)

![](img/aae8b079d175d929da22647ee293d23d_7.png)

## 系统工作类命令

![](img/aae8b079d175d929da22647ee293d23d_9.png)

上一节我们介绍了课程的整体结构，本节中我们来看看第一类命令：系统工作类命令。这类命令主要用于系统的日常管理和维护。

![](img/aae8b079d175d929da22647ee293d23d_11.png)

### 1. echo命令

![](img/aae8b079d175d929da22647ee293d23d_13.png)

`echo` 命令用于在终端屏幕上输出内容。它看似简单，但在脚本编写和系统管理中非常有用。

![](img/aae8b079d175d929da22647ee293d23d_15.png)

![](img/aae8b079d175d929da22647ee293d23d_17.png)

**基本用法：**
```bash
echo "Hello, World!"
```
这条命令会在屏幕上显示 “Hello, World!”。

**常用参数：**
以下是 `echo` 命令的两个常用参数：
*   **-n**：输出内容后不换行。
    ```bash
    echo -n "Hello"
    echo "World"
    ```
    输出结果为 `HelloWorld`（在同一行）。
*   **-e**：启用反斜杠转义字符的解释。
    ```bash
    echo -e "Line1\nLine2"
    ```
    输出结果为两行：`Line1` 和 `Line2`。`\n` 被解释为换行符。

![](img/aae8b079d175d929da22647ee293d23d_19.png)

![](img/aae8b079d175d929da22647ee293d23d_21.png)

**特殊应用：输出带颜色的文本**
在脚本中，我们可以使用 `echo -e` 结合特定代码来输出彩色文本，使提示信息更醒目。
格式为：`echo -e "\e[背景色代码;文字色代码m要显示的文本\e[0m"`
例如，输出红色文字：
```bash
echo -e "\e[31m这是红色文字\e[0m"
```
常用颜色代码：31（红），32（绿），33（黄），34（蓝）等。

![](img/aae8b079d175d929da22647ee293d23d_23.png)

![](img/aae8b079d175d929da22647ee293d23d_24.png)

### 2. date命令

`date` 命令用于显示或设置系统日期和时间。它在日志归档、定时任务等场景中非常实用。

![](img/aae8b079d175d929da22647ee293d23d_26.png)

**基本用法：**
```bash
date
```
直接运行会显示系统的当前日期和时间。

![](img/aae8b079d175d929da22647ee293d23d_28.png)

**格式化输出：**
我们可以使用 `+` 和格式符来定制时间的显示方式。
```bash
date "+%Y-%m-%d %H:%M:%S"
```
输出格式类似 `2020-02-27 16:29:49`。
常用格式符：`%Y`（年），`%m`（月），`%d`（日），`%H`（时），`%M`（分），`%S`（秒）。

**实用技巧：创建带日期的文件**
结合命令替换，可以方便地创建以当前日期命名的文件。
```bash
touch `date "+%Y%m%d"`.log
```
这条命令会创建一个名为 `20200227.log` 的文件。

**计算日期：**
`date` 命令还可以进行简单的日期计算。
```bash
date -d “yesterday” “+%Y-%m-%d” # 显示昨天的日期
date -d “-1 day” “+%Y-%m-%d”    # 显示一天前的日期
```

**时间戳转换：**
Linux时间戳是从1970年1月1日开始的秒数。
```bash
date “+%s” # 显示当前时间的时间戳
date -d “@1582795200” “+%Y-%m-%d” # 将时间戳转换为可读日期
```

### 3. wget命令

![](img/aae8b079d175d929da22647ee293d23d_30.png)

![](img/aae8b079d175d929da22647ee293d23d_32.png)

![](img/aae8b079d175d929da22647ee293d23d_34.png)

`wget` 是一个非交互式的网络下载器，可以在终端中直接从网络下载文件。

![](img/aae8b079d175d929da22647ee293d23d_36.png)

![](img/aae8b079d175d929da22647ee293d23d_37.png)

**基本用法：**
```bash
wget http://example.com/file.zip
```
这条命令会将 `file.zip` 下载到当前目录。

### 4. lsof命令

![](img/aae8b079d175d929da22647ee293d23d_39.png)

![](img/aae8b079d175d929da22647ee293d23d_41.png)

![](img/aae8b079d175d929da22647ee293d23d_42.png)

`lsof` 命令用于列出当前系统打开的文件。在Linux中，一切皆文件，包括网络连接、设备等，因此这个命令功能强大。

![](img/aae8b079d175d929da22647ee293d23d_44.png)

![](img/aae8b079d175d929da22647ee293d23d_46.png)

**基本用法：**
```bash
lsof
```
直接运行会列出所有进程打开的所有文件，信息量很大。

**常用场景：**
*   **查看指定进程打开的文件：**
    ```bash
    lsof -c sshd
    ```
    这条命令列出所有 `sshd` 进程打开的文件。
*   **查看指定端口被谁占用：**
    ```bash
    lsof -i :80
    ```
*   **恢复误删的文件：**
    如果一个进程正在使用某个文件，即使该文件被删除，在进程结束前，其数据仍存在于内存中。`lsof` 可以帮助找到并恢复它。
    1.  找到被删除但仍在被进程使用的文件：
        ```bash
        lsof | grep deleted
        ```
    2.  从 `/proc/<PID>/fd/<FD>` 中拷贝出数据即可恢复文件内容。

---

![](img/aae8b079d175d929da22647ee293d23d_48.png)

## 文本文件操作类命令

![](img/aae8b079d175d929da22647ee293d23d_49.png)

![](img/aae8b079d175d929da22647ee293d23d_51.png)

上一节我们介绍了几个系统工作相关的命令，本节中我们来看看用于处理文本文件的命令。

![](img/aae8b079d175d929da22647ee293d23d_53.png)

![](img/aae8b079d175d929da22647ee293d23d_55.png)

### diff命令

![](img/aae8b079d175d929da22647ee293d23d_57.png)

`diff` 命令用于比较两个文件之间的差异，并以逐行的方式显示出来。

![](img/aae8b079d175d929da22647ee293d23d_59.png)

![](img/aae8b079d175d929da22647ee293d23d_61.png)

**基本用法：**
```bash
diff file1.txt file2.txt
```
如果两个文件完全相同，则没有任何输出。

**输出解读：**
`diff` 的输出可能像这样：`1c1`。这表示第一个文件的第1行和第二个文件的第1行有内容更改（change）。
*   `a` 表示添加（add）
*   `d` 表示删除（delete）
*   `c` 表示更改（change）

![](img/aae8b079d175d929da22647ee293d23d_63.png)

![](img/aae8b079d175d929da22647ee293d23d_65.png)

**更直观的比较方式：**
使用 `-y` 参数可以进行并排比较，结果更直观。
```bash
diff -y -W 50 file1.txt file2.txt
```
`-W 50` 指定并排显示的宽度为50个字符。输出中会用 `|` 标出有差异的行。

![](img/aae8b079d175d929da22647ee293d23d_67.png)

---

![](img/aae8b079d175d929da22647ee293d23d_69.png)

![](img/aae8b079d175d929da22647ee293d23d_70.png)

![](img/aae8b079d175d929da22647ee293d23d_72.png)

![](img/aae8b079d175d929da22647ee293d23d_73.png)

![](img/aae8b079d175d929da22647ee293d23d_74.png)

## 免交互命令

![](img/aae8b079d175d929da22647ee293d23d_76.png)

![](img/aae8b079d175d929da22647ee293d23d_78.png)

上一节我们学习了文件比较，本节中我们来看看如何实现自动化操作，避免手动交互。

![](img/aae8b079d175d929da22647ee293d23d_80.png)

![](img/aae8b079d175d929da22647ee293d23d_81.png)

![](img/aae8b079d175d929da22647ee293d23d_82.png)

### expect命令

![](img/aae8b079d175d929da22647ee293d23d_84.png)

![](img/aae8b079d175d929da22647ee293d23d_86.png)

![](img/aae8b079d175d929da22647ee293d23d_88.png)

`expect` 是一个用来实现自动交互式任务的工具。它可以模拟用户输入，例如自动填写密码，从而实现脚本的全自动化运行。

![](img/aae8b079d175d929da22647ee293d23d_90.png)

**基本概念：**
`expect` 基于Tcl语言，通常需要单独安装：`yum install expect -y`。

![](img/aae8b079d175d929da22647ee293d23d_92.png)

**简单示例：自动登录SSH**
以下脚本实现了自动登录到远程服务器并执行一条命令。
```bash
#!/usr/bin/expect
set timeout 30
spawn ssh root@192.168.31.132
expect “password:”
send “your_password\r”
expect “#”
send “touch /root/1.txt\r”
send “exit\r”
expect eof
```
脚本解释：
1.  `spawn`：启动一个新的进程（这里是ssh）。
2.  `expect`：等待进程输出特定的字符串（如“password:”）。
3.  `send`：向进程发送字符串（如密码和命令）。
4.  `\r` 代表回车键。
5.  `expect eof` 等待进程结束。

![](img/aae8b079d175d929da22647ee293d23d_94.png)

![](img/aae8b079d175d929da22647ee293d23d_96.png)

![](img/aae8b079d175d929da22647ee293d23d_98.png)

运行此脚本后，它会自动输入密码登录，创建文件，然后退出，全程无需人工干预。

![](img/aae8b079d175d929da22647ee293d23d_100.png)

![](img/aae8b079d175d929da22647ee293d23d_102.png)

---

## 总结

本节课我们一起学习了Linux中一系列实用命令：
1.  **系统工作类**：`echo`（输出及颜色控制）、`date`（时间操作与格式化）、`wget`（网络下载）、`lsof`（查看打开文件及恢复数据）。
2.  **文本文件类**：`diff`（比较文件差异）。
3.  **免交互类**：`expect`（实现自动化交互任务）。

![](img/aae8b079d175d929da22647ee293d23d_104.png)

![](img/aae8b079d175d929da22647ee293d23d_106.png)

![](img/aae8b079d175d929da22647ee293d23d_108.png)

这些命令虽然不一定每天都会用到，但在特定场景下能极大地提升工作效率或解决棘手问题。建议结合提供的例子进行练习，以加深理解。我们将在后续的系列中介绍更多好用的命令。