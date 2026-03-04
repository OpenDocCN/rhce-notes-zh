# Linux系统管理：P7：系统进程管理

![](img/dcf11c3b2ea53ce023e0ae5aba003f9e_1.png)

在本节课中，我们将要学习Linux系统中的进程管理。进程是系统运行的核心，理解进程的概念、生命周期、状态以及如何查看和管理进程，是进行系统运维和故障排查的基础技能。我们将从进程的基本概念入手，逐步学习如何使用相关命令来监控和控制进程。

## 进程的基本概念

上一节我们介绍了课程概述，本节中我们来看看什么是进程。

程序是存储在磁盘上的静态二进制文件。当程序被加载到内存中并开始执行时，就形成了一个进程。进程是程序的一个运行实例，它拥有独立的内存空间、安全属性和执行状态。一个进程可以包含一个或多个线程。

进程与程序的关系可以这样理解：程序是菜谱，而进程是按照菜谱烹饪的过程。进程是动态的，会消耗CPU和内存资源。

## 进程的生命周期与状态

理解了进程是什么之后，我们来看看进程从创建到结束的完整生命周期，以及在这个过程中可能处于的各种状态。

![](img/dcf11c3b2ea53ce023e0ae5aba003f9e_3.png)

进程的生命周期始于其被创建（例如，通过`fork`系统调用），结束于其退出。在这个过程中，进程会在几种状态间转换。父进程可以创建子进程，子进程执行完毕后会进入“僵尸”状态，等待父进程读取其退出状态，之后才会完全释放资源。

![](img/dcf11c3b2ea53ce023e0ae5aba003f9e_5.png)

![](img/dcf11c3b2ea53ce023e0ae5aba003f9e_7.png)

![](img/dcf11c3b2ea53ce023e0ae5aba003f9e_9.png)

![](img/dcf11c3b2ea53ce023e0ae5aba003f9e_11.png)

![](img/dcf11c3b2ea53ce023e0ae5aba003f9e_13.png)

![](img/dcf11c3b2ea53ce023e0ae5aba003f9e_15.png)

以下是进程的几种主要状态：
*   **R (Running)**： 进程正在运行或在运行队列中等待。
*   **S (Sleeping)**： 进程处于可中断的睡眠状态，通常在等待某个事件（如I/O操作）完成。
*   **D (Uninterruptible Sleep)**： 进程处于不可中断的睡眠状态，通常在等待硬件I/O，不能被信号唤醒。
*   **T (Stopped)**： 进程已被停止（例如，通过 `Ctrl+Z` 发送了SIGTSTP信号）。
*   **Z (Zombie)**： 僵尸进程。进程已终止，但其父进程尚未获取其终止状态，导致资源未完全释放。

![](img/dcf11c3b2ea53ce023e0ae5aba003f9e_17.png)

![](img/dcf11c3b2ea53ce023e0ae5aba003f9e_19.png)

![](img/dcf11c3b2ea53ce023e0ae5aba003f9e_21.png)

## 查看进程信息：`ps` 命令

了解了进程的状态，我们需要工具来查看它们。`ps` 命令是查看当前进程状态的静态快照的基本工具。

![](img/dcf11c3b2ea53ce023e0ae5aba003f9e_23.png)

![](img/dcf11c3b2ea53ce023e0ae5aba003f9e_25.png)

![](img/dcf11c3b2ea53ce023e0ae5aba003f9e_27.png)

`ps` 命令的选项风格有两种：BSD风格（不加 `-`）和UNIX风格（加 `-`）。常用的组合是 `ps aux` 或 `ps -ef`。

![](img/dcf11c3b2ea53ce023e0ae5aba003f9e_29.png)

![](img/dcf11c3b2ea53ce023e0ae5aba003f9e_31.png)

*   `ps aux`： 以BSD格式列出所有用户的详细进程信息。
    *   **命令示例**：`ps aux | less`
*   `ps -ef`： 以标准格式列出完整格式的进程信息，常与 `grep` 结合使用来查找特定进程。
    *   **命令示例**：`ps -ef | grep httpd`

![](img/dcf11c3b2ea53ce023e0ae5aba003f9e_33.png)

![](img/dcf11c3b2ea53ce023e0ae5aba003f9e_35.png)

`ps aux` 输出的关键列包括：
*   **USER**: 进程所有者
*   **PID**: 进程ID
*   **%CPU**: CPU占用率
*   **%MEM**: 内存占用率
*   **STAT**: 进程状态（即上文提到的R/S/D/T/Z等）
*   **COMMAND**: 启动进程的命令

![](img/dcf11c3b2ea53ce023e0ae5aba003f9e_37.png)

![](img/dcf11c3b2ea53ce023e0ae5aba003f9e_39.png)

![](img/dcf11c3b2ea53ce023e0ae5aba003f9e_41.png)

![](img/dcf11c3b2ea53ce023e0ae5aba003f9e_43.png)

可以使用 `--sort` 参数对输出进行排序，例如 `ps aux --sort=-%cpu` 可以按CPU使用率降序排列。

![](img/dcf11c3b2ea53ce023e0ae5aba003f9e_45.png)

![](img/dcf11c3b2ea53ce023e0ae5aba003f9e_47.png)

![](img/dcf11c3b2ea53ce023e0ae5aba003f9e_49.png)

![](img/dcf11c3b2ea53ce023e0ae5aba003f9e_51.png)

![](img/dcf11c3b2ea53ce023e0ae5aba003f9e_53.png)

## 动态监控进程：`top` 命令

![](img/dcf11c3b2ea53ce023e0ae5aba003f9e_55.png)

![](img/dcf11c3b2ea53ce023e0ae5aba003f9e_57.png)

![](img/dcf11c3b2ea53ce023e0ae5aba003f9e_59.png)

`ps` 命令提供的是瞬间状态，而 `top` 命令可以动态、实时地监控系统的进程活动和资源使用情况。

![](img/dcf11c3b2ea53ce023e0ae5aba003f9e_61.png)

![](img/dcf11c3b2ea53ce023e0ae5aba003f9e_62.png)

![](img/dcf11c3b2ea53ce023e0ae5aba003f9e_64.png)

运行 `top` 命令后，屏幕上半部分显示系统概览，包括负载、任务数、CPU和内存使用情况；下半部分则是进程列表。

![](img/dcf11c3b2ea53ce023e0ae5aba003f9e_66.png)

![](img/dcf11c3b2ea53ce023e0ae5aba003f9e_68.png)

![](img/dcf11c3b2ea53ce023e0ae5aba003f9e_70.png)

**命令示例**：
*   `top`： 默认启动，每3秒刷新一次。
*   `top -d 1`： 设置刷新间隔为1秒。
*   `top -p PID1,PID2`： 仅监控指定PID的进程。
*   `top -u username`： 仅监控指定用户的进程。

![](img/dcf11c3b2ea53ce023e0ae5aba003f9e_72.png)

在 `top` 界面中，可以按 `P`（按CPU排序）、`M`（按内存排序）等键进行交互操作。按 `q` 键退出。

## 控制进程：`kill` 与 `killall` 命令

当我们发现异常进程或需要结束某个任务时，就需要使用进程控制命令。`kill` 命令用于向进程发送特定的信号。

![](img/dcf11c3b2ea53ce023e0ae5aba003f9e_74.png)

![](img/dcf11c3b2ea53ce023e0ae5aba003f9e_75.png)

默认情况下，`kill PID` 发送的是 `SIGTERM(15)` 信号，请求进程正常终止。如果进程不响应，可以使用 `SIGKILL(9)` 信号强制终止。

![](img/dcf11c3b2ea53ce023e0ae5aba003f9e_77.png)

![](img/dcf11c3b2ea53ce023e0ae5aba003f9e_79.png)

**常用信号**：
*   **1 (SIGHUP)**： 挂起信号，通常用于让进程重新读取配置文件。
    *   **命令示例**：`kill -1 PID` 等价于 `systemctl reload service_name`
*   **9 (SIGKILL)**： 强制终止信号，立即结束进程，不可被捕获或忽略。
    *   **命令示例**：`kill -9 PID`
*   **15 (SIGTERM)**： 终止信号，请求进程正常退出（默认信号）。
    *   **命令示例**：`kill PID` 或 `kill -15 PID`
*   **18 (SIGCONT)**： 继续执行已停止的进程。
*   **19 (SIGSTOP)**： 停止（暂停）进程。

![](img/dcf11c3b2ea53ce023e0ae5aba003f9e_81.png)

![](img/dcf11c3b2ea53ce023e0ae5aba003f9e_82.png)

`killall` 命令通过进程名来发送信号，例如 `killall -9 httpd` 会终止所有名为 `httpd` 的进程。

![](img/dcf11c3b2ea53ce023e0ae5aba003f9e_84.png)

**注意**：`SIGKILL(9)` 信号应谨慎使用，因为它可能导致数据丢失或状态不一致。

![](img/dcf11c3b2ea53ce023e0ae5aba003f9e_86.png)

## 进程优先级：`nice` 与 `renice`

![](img/dcf11c3b2ea53ce023e0ae5aba003f9e_88.png)

![](img/dcf11c3b2ea53ce023e0ae5aba003f9e_90.png)

在系统资源紧张时，可能需要调整进程的优先级。Linux进程的优先级通过 `nice` 值来体现，范围从 `-20`（最高优先级）到 `19`（最低优先级）。普通用户只能降低优先级（增大nice值），只有root用户可以提高优先级。

*   `nice`： 以指定的优先级启动一个进程。
    *   **命令示例**：`nice -n 5 command` 以优先级5启动命令。
*   `renice`： 调整一个已运行进程的优先级。
    *   **命令示例**：`renice -n 10 -p PID` 将指定PID进程的优先级改为10。

在 `top` 命令的输出中，`NI` 列显示的就是进程的 `nice` 值，`PR` 列（优先级）会随之变化。

![](img/dcf11c3b2ea53ce023e0ae5aba003f9e_92.png)

![](img/dcf11c3b2ea53ce023e0ae5aba003f9e_94.png)

## 总结与作业

![](img/dcf11c3b2ea53ce023e0ae5aba003f9e_96.png)

![](img/dcf11c3b2ea53ce023e0ae5aba003f9e_98.png)

![](img/dcf11c3b2ea53ce023e0ae5aba003f9e_100.png)

![](img/dcf11c3b2ea53ce023e0ae5aba003f9e_101.png)

本节课中我们一起学习了Linux系统进程管理的核心知识。我们从进程的基本概念和生命周期讲起，学习了如何用 `ps` 和 `top` 命令查看进程信息，如何使用 `kill` 系列命令控制进程，以及如何通过 `nice` 值调整进程的优先级。

![](img/dcf11c3b2ea53ce023e0ae5aba003f9e_103.png)

**课后作业**：
1.  **总结归纳**：请整理并记忆进程的几种主要状态（R, S, D, T, Z）以及 `kill` 命令的常用信号（1, 9, 15, 18, 19）及其含义。
2.  **动手实践**：在您的Linux系统上，完成以下操作练习：
    *   使用 `ps aux`、`ps -ef` 查看进程，并用 `--sort` 参数进行排序。
    *   使用 `top` 命令动态监控进程，尝试交互排序。
    *   启动一个后台进程（如 `sleep 600 &`），然后使用 `kill` 命令通过不同信号终止它。
    *   尝试使用 `nice` 和 `renice` 命令调整进程的优先级。