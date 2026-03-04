# Linux运维全套培训课程：P31：红帽RHCSA-30.进程前后台调度、杀死进程、用户登录分析 🖥️

![](img/9b309b95edb522f836efb7fcbd56cddd_1.png)

![](img/9b309b95edb522f836efb7fcbd56cddd_3.png)

在本节课中，我们将要学习如何监控和管理Linux系统中的进程，包括使用`top`命令分析系统状态、进行进程的前后台调度、使用不同命令杀死进程，以及分析用户的登录情况。这些技能对于日常的系统维护和故障排查至关重要。

![](img/9b309b95edb522f836efb7fcbd56cddd_5.png)

## 使用`top`命令监控系统状态

上一节我们介绍了基础的进程概念，本节中我们来看看如何使用`top`命令动态地、全面地监控系统状态。`top`命令提供了一个实时更新的系统概览。

运行`top`命令后，界面主要分为几个部分。第一行显示系统摘要信息。

*   **当前系统时间**：例如 `15:30:45`。
*   **系统运行时间**：例如 `up 10 days, 2:30`。
*   **当前登录用户数**：例如 `2 users`。
*   **系统平均负载**：显示过去1分钟、5分钟、15分钟的平均负载，例如 `load average: 0.05, 0.10, 0.15`。

![](img/9b309b95edb522f836efb7fcbd56cddd_7.png)

![](img/9b309b95edb522f836efb7fcbd56cddd_9.png)

第二行显示进程的总体统计信息。

![](img/9b309b95edb522f836efb7fcbd56cddd_11.png)

![](img/9b309b95edb522f836efb7fcbd56cddd_13.png)

![](img/9b309b95edb522f836efb7fcbd56cddd_15.png)

![](img/9b309b95edb522f836efb7fcbd56cddd_17.png)

*   **总进程数**：`total`
*   **运行中的进程数**：`running`
*   **休眠的进程数**：`sleeping`
*   **停止的进程数**：`stopped`
*   **僵尸进程数**：`zombie`

![](img/9b309b95edb522f836efb7fcbd56cddd_19.png)

![](img/9b309b95edb522f836efb7fcbd56cddd_21.png)

僵尸进程是指已经终止但其父进程尚未对其进行善后处理的进程。如果发现僵尸进程数量不为0，可以使用`ps aux | grep defunct`找到其PID，然后用`kill`命令终止其父进程来清理。

![](img/9b309b95edb522f836efb7fcbd56cddd_23.png)

![](img/9b309b95edb522f836efb7fcbd56cddd_25.png)

第三行显示CPU的使用情况百分比。

![](img/9b309b95edb522f836efb7fcbd56cddd_27.png)

![](img/9b309b95edb522f836efb7fcbd56cddd_29.png)

*   **用户空间占用CPU百分比**：`us`
*   **内核空间占用CPU百分比**：`sy`
*   **改变过优先级的进程占用CPU百分比**：`ni`
*   **空闲CPU百分比**：`id` **（需要重点关注）**
*   **等待I/O的CPU时间百分比**：`wa`
*   **硬中断占用CPU百分比**：`hi`
*   **软中断占用CPU百分比**：`si`
*   **虚拟机占用CPU百分比**：`st`

**重点关注`id`（空闲百分比）**。如果这个值持续很低（例如低于10%），说明CPU非常繁忙，需要进一步排查是哪个进程消耗了大量资源。

第四行和第五行分别显示物理内存（Mem）和交换分区（Swap）的使用情况，信息以KB为单位显示。但查看内存更直观的方式是使用`free -h`命令。

在`top`的交互界面中，可以输入以下常用命令进行排序：
*   按 **`P`** （大写）：根据CPU使用率排序。
*   按 **`M`** （大写）：根据内存使用率排序。
*   按 **`q`** ：退出`top`命令。

![](img/9b309b95edb522f836efb7fcbd56cddd_31.png)

![](img/9b309b95edb522f836efb7fcbd56cddd_33.png)

通过`top`命令，我们可以快速了解系统的健康状态，定位高负载的进程。

## 检索特定进程

![](img/9b309b95edb522f836efb7fcbd56cddd_35.png)

除了动态监控，有时我们需要快速查找特定的进程。`ps`命令结合管道和`grep`是一种方法，而`pgrep`命令则更为简洁。

![](img/9b309b95edb522f836efb7fcbd56cddd_37.png)

以下是`pgrep`命令的常用选项：
*   `-l`：同时输出进程名和PID。
    ```bash
    pgrep -l sshd
    ```
*   `-u`：检索指定用户的进程。
    ```bash
    pgrep -u test -l
    ```
*   `-t`：检索指定终端（tty）的进程。
    ```bash
    pgrep -t pts/0 -l
    ```
*   `-x`：进行精确匹配，要求进程名完全一致。
    ```bash
    pgrep -x sleep
    ```

`pgrep`命令输出信息简洁，适合快速查找。如果需要查看更详细的进程信息，建议使用`ps aux | grep`的组合。

![](img/9b309b95edb522f836efb7fcbd56cddd_39.png)

![](img/9b309b95edb522f836efb7fcbd56cddd_41.png)

## 进程的前后台调度

![](img/9b309b95edb522f836efb7fcbd56cddd_43.png)

![](img/9b309b95edb522f836efb7fcbd56cddd_45.png)

![](img/9b309b95edb522f836efb7fcbd56cddd_47.png)

在Linux中，进程可以在前台运行（占用当前终端），也可以在后台运行（不占用终端）。这对于运行耗时任务非常有用。

![](img/9b309b95edb522f836efb7fcbd56cddd_48.png)

*   **将进程放入后台运行**：在命令末尾添加 `&` 符号。
    ```bash
    sleep 300 &
    ```
    命令会立即返回，并显示一个后台作业编号（如`[1] 12345`），其中`1`是作业号，`12345`是进程PID。

*   **查看后台进程列表**：使用 `jobs -l` 命令。

![](img/9b309b95edb522f836efb7fcbd56cddd_50.png)

![](img/9b309b95edb522f836efb7fcbd56cddd_52.png)

*   **将后台进程调至前台**：使用 `fg %作业号` 命令。
    ```bash
    fg %1
    ```

*   **暂停前台进程并放入后台**：
    1.  按 `Ctrl + Z` 暂停当前前台进程。
    2.  使用 `bg %作业号` 命令让其在后台继续运行。
        ```bash
        bg %1
        ```

![](img/9b309b95edb522f836efb7fcbd56cddd_54.png)

![](img/9b309b95edb522f836efb7fcbd56cddd_56.png)

![](img/9b309b95edb522f836efb7fcbd56cddd_58.png)

*   **终止前台进程**：按 `Ctrl + C`。

后台调度功能允许我们在一个终端会话中灵活管理多个任务。

## 杀死进程

当需要手动结束一个进程时，可以使用`kill`系列命令。杀死进程本质上是向进程发送一个信号。

最常用的信号有：
*   `15` (`SIGTERM`)：**正常终止**。这是`kill`命令的默认信号，允许进程进行清理工作后再结束。
*   `9` (`SIGKILL`)：**强制终止**。立即结束进程，不进行任何清理。用于无法正常结束的进程（如僵尸进程）。

以下是杀死进程的几种方式：

![](img/9b309b95edb522f836efb7fcbd56cddd_60.png)

![](img/9b309b95edb522f836efb7fcbd56cddd_62.png)

1.  **通过PID杀死进程**：使用 `kill` 命令。
    ```bash
    kill 12345        # 发送SIGTERM信号
    kill -9 12345     # 发送SIGKILL信号强制杀死
    ```

![](img/9b309b95edb522f836efb7fcbd56cddd_64.png)

![](img/9b309b95edb522f836efb7fcbd56cddd_66.png)

2.  **通过进程名杀死进程**：使用 `pkill` 命令。它会杀死所有匹配进程名的进程。
    ```bash
    pkill sleep       # 杀死所有名为sleep的进程
    pkill -9 -u test  # 强制杀死test用户的所有进程（可用于踢出用户）
    ```

![](img/9b309b95edb522f836efb7fcbd56cddd_68.png)

3.  **通过终端杀死进程**：使用 `killall` 命令（注意：并非所有系统默认安装，也可用`pkill -t`替代）。
    ```bash
    killall -9 -t pts/1  # 强制杀死在终端pts/1上运行的所有进程
    ```

在实际运维中，应优先尝试使用默认信号（`kill PID`）优雅地结束进程，仅在进程无响应时使用`kill -9`进行强制杀死。

![](img/9b309b95edb522f836efb7fcbd56cddd_70.png)

![](img/9b309b95edb522f836efb7fcbd56cddd_72.png)

![](img/9b309b95edb522f836efb7fcbd56cddd_74.png)

## 用户登录分析

![](img/9b309b95edb522f836efb7fcbd56cddd_76.png)

![](img/9b309b95edb522f836efb7fcbd56cddd_78.png)

![](img/9b309b95edb522f836efb7fcbd56cddd_80.png)

了解谁在访问系统以及访问历史是系统安全的重要部分。Linux提供了多条命令来查看用户登录信息。

*   **查看当前已登录用户**：
    *   `users`：仅显示已登录的用户名列表，非常简洁。
    *   `who`：显示更详细的信息，包括用户名、终端、登录时间和来源IP地址。
        ```bash
        who
        ```
    *   `w`：**功能最强大**。它不仅显示`who`的信息，还同时显示了系统当前时间、运行时长、平均负载，以及每个用户正在执行的命令和占用资源情况。这相当于`top`命令第一行的动态展示加上详细的用户会话信息。

*   **查看历史登录记录**：
    *   `last`：查看**成功登录**的历史记录，包括用户名、终端、登录IP、登录和退出时间。
        ```bash
        last
        ```
    *   `lastb`：查看**登录失败**的历史记录。这对于发现恶意登录尝试非常有用。通常需要root权限执行。
        ```bash
        sudo lastb
        ```

这些命令的日志通常来源于`/var/log/wtmp`（成功登录）和`/var/log/btmp`（失败登录）文件。

---

![](img/9b309b95edb522f836efb7fcbd56cddd_82.png)

![](img/9b309b95edb522f836efb7fcbd56cddd_84.png)

![](img/9b309b95edb522f836efb7fcbd56cddd_86.png)

![](img/9b309b95edb522f836efb7fcbd56cddd_88.png)

本节课中我们一起学习了Linux进程管理和用户监控的核心操作。我们掌握了使用`top`命令实时分析系统负载和进程状态，学会了使用`pgrep`检索进程，以及通过`&`、`jobs`、`fg`、`bg`进行进程的前后台调度。我们还深入了解了使用`kill`、`pkill`等命令以不同方式终止进程，并学会了使用`who`、`w`、`last`、`lastb`等命令来分析用户的登录情况。这些工具是每一位Linux运维人员日常工作中不可或缺的利器，能有效帮助维护系统稳定性和安全性。