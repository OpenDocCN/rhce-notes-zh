# RHCSA精讲教程：P9：2.04-tuned系统调优 🛠️

![](img/ad44b2fdefd49380af9ed822e23385e0_1.png)

![](img/ad44b2fdefd49380af9ed822e23385e0_3.png)

![](img/ad44b2fdefd49380af9ed822e23385e0_5.png)

在本节课中，我们将学习如何使用红帽系统自带的 `tuned` 工具进行系统调优。这是一个在RHCSA考试中常见的考点，但操作非常简单，核心是学会使用预设的优化方案。

![](img/ad44b2fdefd49380af9ed822e23385e0_7.png)

![](img/ad44b2fdefd49380af9ed822e23385e0_9.png)

## 概述

![](img/ad44b2fdefd49380af9ed822e23385e0_11.png)

系统调优（System Tuning）是指调整Linux内核参数，以优化系统在不同应用场景下的性能。传统的手动调整方式复杂且容易出错。红帽系统提供了 `tuned` 服务，它预置了多种针对不同场景（如虚拟机、数据库、桌面环境）的优化方案，我们只需选择合适的方案即可，大大简化了操作。

![](img/ad44b2fdefd49380af9ed822e23385e0_13.png)

## 理解tuned服务

上一节我们介绍了系统调优的基本概念，本节中我们来看看红帽提供的具体解决方案：`tuned` 服务。

`tuned` 是一个系统守护进程，它能够根据用户选择的“配置集”（Profile）自动调整系统设置。这些配置集是红帽预先定义好的一组优化参数，适用于如“虚拟客户机”、“高吞吐量”、“低延迟”等特定场景。

![](img/ad44b2fdefd49380af9ed822e23385e0_15.png)

![](img/ad44b2fdefd49380af9ed822e23385e0_17.png)

![](img/ad44b2fdefd49380af9ed822e23385e0_19.png)

在红帽7和8系统中，`tuned` 软件包通常是**默认安装并启用**的。这意味着在考试环境中，我们很可能不需要进行安装和启动服务的基础操作。

![](img/ad44b2fdefd49380af9ed822e23385e0_21.png)

## 核心管理命令

![](img/ad44b2fdefd49380af9ed822e23385e0_23.png)

![](img/ad44b2fdefd49380af9ed822e23385e0_25.png)

要使用 `tuned`，我们主要通过一个命令进行管理：`tuned-adm`。以下是该命令最常用、也是考试最可能用到的几个子命令。

![](img/ad44b2fdefd49380af9ed822e23385e0_27.png)

![](img/ad44b2fdefd49380af9ed822e23385e0_29.png)

以下是 `tuned-adm` 命令的核心用法列表：

![](img/ad44b2fdefd49380af9ed822e23385e0_31.png)

![](img/ad44b2fdefd49380af9ed822e23385e0_33.png)

*   **`tuned-adm list`**
    *   **作用**：列出所有可用的调优配置集（优化方案）。
    *   **示例**：`tuned-adm list`

![](img/ad44b2fdefd49380af9ed822e23385e0_35.png)

![](img/ad44b2fdefd49380af9ed822e23385e0_37.png)

*   **`tuned-adm active`**
    *   **作用**：查看当前系统**正在使用**的调优配置集。
    *   **示例**：`tuned-adm active`

![](img/ad44b2fdefd49380af9ed822e23385e0_39.png)

![](img/ad44b2fdefd49380af9ed822e23385e0_41.png)

*   **`tuned-adm recommend`**
    *   **作用**：让 `tuned` 分析当前系统硬件和运行环境，并**推荐**一个最合适的配置集。这是考试题目的关键。
    *   **示例**：`tuned-adm recommend`

![](img/ad44b2fdefd49380af9ed822e23385e0_43.png)

*   **`tuned-adm profile`** *[配置集名称]*
    *   **作用**：将系统切换至指定的调优配置集。
    *   **示例**：`tuned-adm profile virtual-guest`

![](img/ad44b2fdefd49380af9ed822e23385e0_45.png)

## 实战操作步骤

![](img/ad44b2fdefd49380af9ed822e23385e0_47.png)

![](img/ad44b2fdefd49380af9ed822e23385e0_49.png)

了解了核心命令后，我们来看看在考试或实际中如何应用。整个操作流程非常清晰。

![](img/ad44b2fdefd49380af9ed822e23385e0_51.png)

1.  **检查并获取推荐方案**
    首先，我们需要知道系统当前的状态以及 `tuned` 推荐我们使用什么方案。运行以下命令：
    ```bash
    tuned-adm recommend
    ```
    命令输出可能类似于 `virtual-guest`，这表示系统被识别为虚拟机，推荐使用“虚拟客户机”优化方案。

2.  **查看当前活动方案**
    接着，我们查看系统当前实际使用的是哪个方案：
    ```bash
    tuned-adm active
    ```

![](img/ad44b2fdefd49380af9ed822e23385e0_53.png)

3.  **应用推荐方案（如果需要）**
    比较 `recommend` 和 `active` 命令的输出。
    *   **如果两者一致**：说明系统已经处于最优配置，无需任何操作。
    *   **如果两者不一致**：则需要使用 `profile` 子命令将系统切换到推荐方案。例如，如果推荐的是 `virtual-guest`，则执行：
        ```bash
        tuned-adm profile virtual-guest
        ```
        执行后，可以再次运行 `tuned-adm active` 确认切换是否成功。

![](img/ad44b2fdefd49380af9ed822e23385e0_55.png)

![](img/ad44b2fdefd49380af9ed822e23385e0_56.png)

![](img/ad44b2fdefd49380af9ed822e23385e0_57.png)

![](img/ad44b2fdefd49380af9ed822e23385e0_58.png)

## 方案存储位置（扩展了解）

对于想深入了解的同学，可以知道这些预置的调优方案存储在 `/usr/lib/tuned/` 和 `/etc/tuned/` 目录下。每个配置集（如 `virtual-guest`）都是一个独立的文件夹，里面包含定义具体内核参数的配置文件 `tuned.conf`。例如，查看虚拟客户机配置的部分参数：
```bash
cat /usr/lib/tuned/virtual-guest/tuned.conf
```
考试中不需要记忆或修改这些文件，了解其存在即可。

## 总结

![](img/ad44b2fdefd49380af9ed822e23385e0_60.png)

![](img/ad44b2fdefd49380af9ed822e23385e0_62.png)

![](img/ad44b2fdefd49380af9ed822e23385e0_64.png)

本节课中我们一起学习了如何使用 `tuned` 工具进行系统调优。我们了解到：
1.  `tuned` 服务提供了预定义的优化方案，简化了手动调优的复杂性。
2.  管理 `tuned` 的核心命令是 `tuned-adm`，需要掌握 `list`、`active`、`recommend` 和 `profile` 这四个关键子命令。
3.  实际操作的黄金步骤是：先获取**推荐**方案，再查看**当前**方案，若不一致则进行**切换**。
4.  在RHCSA考试中，这道题很可能非常简单，甚至可能因为系统已配置正确而无需操作，但掌握其方法是必须的。