# 红帽认证系统工程师RHCE8：P5：第三天 进程管理 📚

在本节课中，我们将要学习Linux系统中的进程管理，包括作业控制、信号发送以及系统服务管理。这些知识对于系统管理员来说至关重要，能够帮助我们高效地管理系统资源和运行中的程序。

## 上节课知识点更正与补充 🔧

上一节我们介绍了文件权限和目录操作，本节中我们来看看一些需要特别注意的细节。

![](img/333d6fe23439cd2afc770e3a03a8b24b_1.png)

首先，更正一个上节课讲错的知识点。`wc`命令的选项含义如下：
*   `-l`：统计行数。
*   `-w`：统计单词数。
*   `-c`：统计字节数。
*   `-m`：统计字符数。

![](img/333d6fe23439cd2afc770e3a03a8b24b_3.png)

![](img/333d6fe23439cd2afc770e3a03a8b24b_5.png)

我之前错误地将 `-c` 说成是统计字符数，实际上 `-m` 才是。请大家留意。

![](img/333d6fe23439cd2afc770e3a03a8b24b_7.png)

![](img/333d6fe23439cd2afc770e3a03a8b24b_9.png)

其次，关于文件删除权限的问题。要成功删除一个文件，需要同时满足两个条件：
1.  对文件本身拥有写权限（`w`）。
2.  对文件所在的**上级目录**拥有写权限（`w`）。

![](img/333d6fe23439cd2afc770e3a03a8b24b_11.png)

如果上级目录没有写权限，即使文件本身权限是 `777`，你也无法删除该文件，只能修改文件内容。这一点非常重要。

![](img/333d6fe23439cd2afc770e3a03a8b24b_13.png)

最后，修改权限和所有者的命令中，`-R` 选项表示递归操作，会应用到目录下的所有子文件和子目录。请注意，只有管理员（root）才能使用 `chown` 命令更改文件的所有者。

![](img/333d6fe23439cd2afc770e3a03a8b24b_15.png)

![](img/333d6fe23439cd2afc770e3a03a8b24b_17.png)

![](img/333d6fe23439cd2afc770e3a03a8b24b_19.png)

## 作业控制 🎮

![](img/333d6fe23439cd2afc770e3a03a8b24b_21.png)

![](img/333d6fe23439cd2afc770e3a03a8b24b_23.png)

上一节我们介绍了进程的基本概念，本节中我们来看看如何控制作业（Job）。作业可以理解为一组通过管道关联的命令集合，单个命令也可以视为一个作业。

![](img/333d6fe23439cd2afc770e3a03a8b24b_25.png)

![](img/333d6fe23439cd2afc770e3a03a8b24b_27.png)

作业有两种运行方式：
*   **前台运行**：占用当前终端窗口，在命令执行完毕前，用户无法在同一终端执行其他命令。
*   **后台运行**：不占用当前终端窗口，命令在后台执行，用户可以继续在终端进行其他操作。

![](img/333d6fe23439cd2afc770e3a03a8b24b_29.png)

![](img/333d6fe23439cd2afc770e3a03a8b24b_31.png)

将一个命令放到后台运行，只需在命令末尾加上 `&` 符号。例如：
```bash
sleep 1000 &
```

![](img/333d6fe23439cd2afc770e3a03a8b24b_33.png)

![](img/333d6fe23439cd2afc770e3a03a8b24b_35.png)

### 作业管理命令

![](img/333d6fe23439cd2afc770e3a03a8b24b_37.png)

![](img/333d6fe23439cd2afc770e3a03a8b24b_39.png)

![](img/333d6fe23439cd2afc770e3a03a8b24b_40.png)

以下是管理作业的几个核心命令：

*   `jobs`：列出当前会话中的作业列表，显示作业号（`[n]`）和状态。
*   `fg %n`：将作业号为 `n` 的后台作业调到前台运行。`fg` 是 `frontground` 的缩写。
*   `bg %n`：将作业号为 `n` 的已停止（Stopped）作业在后台恢复运行。`bg` 是 `background` 的缩写。
*   `Ctrl + Z`：快捷键，将当前正在前台运行的作业**停止（Stop）**，并放入后台。注意，这不是终止进程，进程依然存在，只是暂停了。

![](img/333d6fe23439cd2afc770e3a03a8b24b_42.png)

**重要提示**：`fg` 和 `bg` 命令后面跟的是**作业号**（如 `%1`），而不是进程号（PID）。

![](img/333d6fe23439cd2afc770e3a03a8b24b_44.png)

![](img/333d6fe23439cd2afc770e3a03a8b24b_46.png)

### 作业状态演示

![](img/333d6fe23439cd2afc770e3a03a8b24b_48.png)

![](img/333d6fe23439cd2afc770e3a03a8b24b_50.png)

![](img/333d6fe23439cd2afc770e3a03a8b24b_52.png)

![](img/333d6fe23439cd2afc770e3a03a8b24b_54.png)

我们可以通过一个例子来理解这些命令。例如，启动一个会长时间运行的前台命令（如 `firefox`），它会占用终端。此时，按下 `Ctrl + Z` 可以将其停止并放入后台。使用 `jobs` 命令查看，会看到其状态为 `Stopped`。如果想让它继续运行但不占用终端，可以使用 `bg %1` 命令。如果想再次与它交互，则使用 `fg %1` 将其调回前台。

![](img/333d6fe23439cd2afc770e3a03a8b24b_56.png)

![](img/333d6fe23439cd2afc770e3a03a8b24b_58.png)

使用 `ps j` 命令（BSD格式，不加 `-`）可以查看进程的详细状态，其中 `T` 状态就代表 `Stopped`。这与使用 `Ctrl + C`（发送 `SIGINT` 信号）终止进程有本质区别。

## 进程信号管理 📶

![](img/333d6fe23439cd2afc770e3a03a8b24b_60.png)

![](img/333d6fe23439cd2afc770e3a03a8b24b_62.png)

![](img/333d6fe23439cd2afc770e3a03a8b24b_64.png)

![](img/333d6fe23439cd2afc770e3a03a8b24b_66.png)

![](img/333d6fe23439cd2afc770e3a03a8b24b_68.png)

上一节我们学会了如何控制作业的启停，本节中我们来看看如何通过信号（Signal）更精细地管理进程。信号是发送给进程的软件中断，管理员通过发送不同的信号来控制进程的行为。

![](img/333d6fe23439cd2afc770e3a03a8b24b_70.png)

使用 `kill -l` 命令可以列出系统支持的所有信号。我们主要掌握以下几个常用信号：

| 信号编号 | 信号名    | 默认行为 | 说明 |
| :--- | :--- | :--- | :--- |
| 1 | `SIGHUP` | 终止 | 挂起。通常用于让进程重新读取配置文件（`reload`）。 |
| 15 | `SIGTERM` | 终止 | 优雅地终止进程（`terminate`），允许进程进行清理工作。 |
| 9 | `SIGKILL` | 终止 | 强制立即杀死进程，进程无法捕获或忽略此信号。 |
| 18 | `SIGCONT` | 继续 | 让一个停止（Stopped）的进程继续运行。 |
| 19 | `SIGSTOP` | 停止 | 停止一个进程（等同于 `Ctrl + Z`），进程无法捕获或忽略此信号。 |
| 2 | `SIGINT` | 终止 | 中断进程（等同于 `Ctrl + C`）。 |

![](img/333d6fe23439cd2afc770e3a03a8b24b_72.png)

### 发送信号的命令

![](img/333d6fe23439cd2afc770e3a03a8b24b_74.png)

发送信号的主要命令是 `kill`。请注意，`kill` 不仅仅是“杀死”，它的本质是“发送信号”，只有发送 `9` 号信号 (`SIGKILL`) 才是强制杀死。

![](img/333d6fe23439cd2afc770e3a03a8b24b_76.png)

**基本语法**：
```bash
kill -信号编号 PID
```
或
```bash
kill -信号名 PID
```

![](img/333d6fe23439cd2afc770e3a03a8b24b_78.png)

**示例**：
1.  让 `vsftpd` 服务重新加载配置文件而不中断现有连接：
    ```bash
    kill -1 $(pidof vsftpd)
    # 或者
    kill -SIGHUP $(pidof vsftpd)
    ```
    这里 `$(命令)` 的作用是先执行括号内的命令，并将其输出结果作为参数。`pidof` 命令用于获取指定进程名的PID。

![](img/333d6fe23439cd2afc770e3a03a8b24b_80.png)

![](img/333d6fe23439cd2afc770e3a03a8b24b_82.png)

2.  停止和继续 `firefox` 进程：
    ```bash
    kill -19 $(pidof firefox) # 停止，等同于 Ctrl+Z
    kill -18 $(pidof firefox) # 继续
    ```

![](img/333d6fe23439cd2afc770e3a03a8b24b_84.png)

![](img/333d6fe23439cd2afc770e3a03a8b24b_86.png)

![](img/333d6fe23439cd2afc770e3a03a8b24b_88.png)

![](img/333d6fe23439cd2afc770e3a03a8b24b_90.png)

![](img/333d6fe23439cd2afc770e3a03a8b24b_91.png)

![](img/333d6fe23439cd2afc770e3a03a8b24b_93.png)

![](img/333d6fe23439cd2afc770e3a03a8b24b_95.png)

3.  终止进程：
    ```bash
    kill -15 $(pidof process_name) # 友好地终止
    kill -9 $(pidof process_name)  # 强制杀死
    ```
    `SIGTERM` (15) 是默认信号，所以 `kill PID` 等同于 `kill -15 PID`。

![](img/333d6fe23439cd2afc770e3a03a8b24b_97.png)

**重要区别**：`SIGTERM` (15) 是进程可以捕获的，允许其进行善后工作（如保存数据）。而 `SIGKILL` (9) 是致命的，进程会立即被清除，可能导致数据丢失或产生临时文件（如vim编辑时的 `.swp` 文件）。

![](img/333d6fe23439cd2afc770e3a03a8b24b_99.png)

![](img/333d6fe23439cd2afc770e3a03a8b24b_101.png)

### 其他相关命令

![](img/333d6fe23439cd2afc770e3a03a8b24b_103.png)

![](img/333d6fe23439cd2afc770e3a03a8b24b_105.png)

![](img/333d6fe23439cd2afc770e3a03a8b24b_107.png)

![](img/333d6fe23439cd2afc770e3a03a8b24b_109.png)

![](img/333d6fe23439cd2afc770e3a03a8b24b_111.png)

![](img/333d6fe23439cd2afc770e3a03a8b24b_113.png)

![](img/333d6fe23439cd2afc770e3a03a8b24b_114.png)

![](img/333d6fe23439cd2afc770e3a03a8b24b_116.png)

![](img/333d6fe23439cd2afc770e3a03a8b24b_118.png)

*   `killall`：根据**进程名**发送信号，会作用于所有同名进程。
    ```bash
    killall -9 firefox # 杀死所有名为 firefox 的进程
    ```
*   `pkill`：根据更复杂的条件（如进程名、用户名等）发送信号。
    ```bash
    pkill -u student # 终止 student 用户的所有进程
    pkill -f “sleep 100” # 终止命令中包含 “sleep 100” 的进程
    ```

![](img/333d6fe23439cd2afc770e3a03a8b24b_120.png)

![](img/333d6fe23439cd2afc770e3a03a8b24b_122.png)

![](img/333d6fe23439cd2afc770e3a03a8b24b_124.png)

这些命令同样支持作业号，例如 `kill -9 %1` 可以强制杀死作业号为1的作业。

![](img/333d6fe23439cd2afc770e3a03a8b24b_126.png)

![](img/333d6fe23439cd2afc770e3a03a8b24b_128.png)

## 特殊变量与进程返回值 💡

![](img/333d6fe23439cd2afc770e3a03a8b24b_130.png)

![](img/333d6fe23439cd2afc770e3a03a8b24b_132.png)

![](img/333d6fe23439cd2afc770e3a03a8b24b_134.png)

![](img/333d6fe23439cd2afc770e3a03a8b24b_136.png)

![](img/333d6fe23439cd2afc770e3a03a8b24b_138.png)

![](img/333d6fe23439cd2afc770e3a03a8b24b_140.png)

在Linux中，有一个特殊的变量 `$?`，它保存了**上一个执行命令的退出状态码**。

![](img/333d6fe23439cd2afc770e3a03a8b24b_142.png)

![](img/333d6fe23439cd2afc770e3a03a8b24b_144.png)

*   **0**：表示命令成功执行（真）。
*   **非0**（1-255）：表示命令执行失败或出现错误（假）。

![](img/333d6fe23439cd2afc770e3a03a8b24b_146.png)

![](img/333d6fe23439cd2afc770e3a03a8b24b_148.png)

![](img/333d6fe23439cd2afc770e3a03a8b24b_150.png)

![](img/333d6fe23439cd2afc770e3a03a8b24b_152.png)

这个变量在编写Shell脚本时非常有用，可以用于判断上一条命令是否执行成功，从而决定后续的逻辑分支。

![](img/333d6fe23439cd2afc770e3a03a8b24b_154.png)

**示例**：
```bash
ls /etc/passwd
echo $? # 输出 0，因为文件存在

ls /nonexistent
echo $? # 输出非0值（如2），因为文件不存在
```

![](img/333d6fe23439cd2afc770e3a03a8b24b_156.png)

![](img/333d6fe23439cd2afc770e3a03a8b24b_158.png)

## 总结 🎯

本节课中我们一起学习了Linux进程管理的核心内容：

![](img/333d6fe23439cd2afc770e3a03a8b24b_160.png)

![](img/333d6fe23439cd2afc770e3a03a8b24b_162.png)

![](img/333d6fe23439cd2afc770e3a03a8b24b_164.png)

![](img/333d6fe23439cd2afc770e3a03a8b24b_166.png)

![](img/333d6fe23439cd2afc770e3a03a8b24b_168.png)

1.  **作业控制**：理解了前台与后台作业的区别，掌握了 `&`、`jobs`、`fg`、`bg` 和 `Ctrl+Z` 等工具来控制作业的运行状态。
2.  **进程信号**：学习了信号的概念，掌握了 `SIGHUP`(1)、`SIGTERM`(15)、`SIGKILL`(9)、`SIGCONT`(18)、`SIGSTOP`(19) 等关键信号的含义与用法。
3.  **管理命令**：熟练使用 `kill`、`killall`、`pkill` 命令向进程发送特定信号，并理解了 `$(command)` 和 `pidof` 命令在组合使用时的便利性。
4.  **返回值**：了解了 `$?` 变量的作用，知道如何通过退出状态码判断命令的执行结果。

![](img/333d6fe23439cd2afc770e3a03a8b24b_170.png)

![](img/333d6fe23439cd2afc770e3a03a8b24b_172.png)

![](img/333d6fe23439cd2afc770e3a03a8b24b_174.png)

这些技能是系统管理员进行日常维护、故障排查和自动化脚本编写的基础，请务必通过实践加以巩固。