# 备考红帽认证必修课：P9：2.04-tuned系统调优

![](img/68a7fc560227372806bb126fd241ec2f_1.png)

![](img/68a7fc560227372806bb126fd241ec2f_3.png)

![](img/68a7fc560227372806bb126fd241ec2f_5.png)

在本节课中，我们将要学习如何使用红帽系统自带的 `tuned` 工具进行系统调优。这是一个在RHCSA/RHCE考试中可能出现的考点，但其操作本身非常简单，核心在于理解并应用红帽预设的优化方案。

![](img/68a7fc560227372806bb126fd241ec2f_7.png)

![](img/68a7fc560227372806bb126fd241ec2f_9.png)

![](img/68a7fc560227372806bb126fd241ec2f_11.png)

## 概述

![](img/68a7fc560227372806bb126fd241ec2f_13.png)

系统调优（Tuning）旨在优化Linux系统的性能，使其在不同应用场景下运行得更高效。传统的手动调整内核参数方法较为复杂且容易出错。红帽系统（RHEL 7/8）提供了 `tuned` 服务，它预置了多种针对不同场景（如虚拟机、数据库、网络服务器等）的优化方案（称为“配置集”或“profile”）。我们只需选择合适的方案即可，无需手动修改复杂的配置文件。

## tuned服务与工具

![](img/68a7fc560227372806bb126fd241ec2f_15.png)

上一节我们介绍了系统调优的基本概念，本节中我们来看看具体的实现工具 `tuned`。

![](img/68a7fc560227372806bb126fd241ec2f_17.png)

![](img/68a7fc560227372806bb126fd241ec2f_19.png)

`tuned` 是一个系统服务，它通过一个名为 `tuned` 的软件包提供。在红帽系统的最小化安装中，此软件包通常已默认安装。

![](img/68a7fc560227372806bb126fd241ec2f_21.png)

![](img/68a7fc560227372806bb126fd241ec2f_23.png)

要使用 `tuned`，首先需要确保其服务已启用并运行。可以使用以下命令检查和设置：
```bash
systemctl enable tuned
systemctl start tuned
```
不过，在考试环境中，这两步通常无需操作，因为服务默认就是启用并启动的。

![](img/68a7fc560227372806bb126fd241ec2f_25.png)

![](img/68a7fc560227372806bb126fd241ec2f_27.png)

## 核心管理命令

![](img/68a7fc560227372806bb126fd241ec2f_29.png)

![](img/68a7fc560227372806bb126fd241ec2f_31.png)

`tuned` 功能的核心管理工具是 `tuned-adm` 命令。如果忘记其用法，直接运行该命令会显示帮助信息。

![](img/68a7fc560227372806bb126fd241ec2f_33.png)

![](img/68a7fc560227372806bb126fd241ec2f_35.png)

![](img/68a7fc560227372806bb126fd241ec2f_37.png)

以下是 `tuned-adm` 命令中我们需要掌握的几个关键子命令：

![](img/68a7fc560227372806bb126fd241ec2f_39.png)

![](img/68a7fc560227372806bb126fd241ec2f_41.png)

*   **`list`**: 列出系统中所有可用的优化方案（配置集）。
*   **`active`**: 显示当前正在使用的优化方案。
*   **`recommend`**: 显示系统推荐的优化方案。这是考试题目中“建议的调优配置集”的来源。
*   **`profile`**: 切换并应用指定的优化方案。用法为 `tuned-adm profile <方案名称>`。

![](img/68a7fc560227372806bb126fd241ec2f_43.png)

![](img/68a7fc560227372806bb126fd241ec2f_45.png)

其他命令如 `verify`（验证推荐方案与实际设置的差异）或 `off`（关闭调优）在考试中不常用。

![](img/68a7fc560227372806bb126fd241ec2f_47.png)

## 实际操作演示

![](img/68a7fc560227372806bb126fd241ec2f_49.png)

![](img/68a7fc560227372806bb126fd241ec2f_51.png)

以下是使用 `tuned-adm` 命令的具体操作步骤。

![](img/68a7fc560227372806bb126fd241ec2f_53.png)

首先，我们可以查看所有可用的优化方案：
```bash
tuned-adm list
```
执行后会列出如 `balanced`（平衡模式）、`throughput-performance`（吞吐性能模式）、`virtual-guest`（虚拟机客户机模式）等多种方案及其简要说明。

接着，查看系统为我们推荐的优化方案：
```bash
tuned-adm recommend
```
对于虚拟机环境，输出很可能是 `virtual-guest`。

![](img/68a7fc560227372806bb126fd241ec2f_55.png)

![](img/68a7fc560227372806bb126fd241ec2f_56.png)

![](img/68a7fc560227372806bb126fd241ec2f_57.png)

![](img/68a7fc560227372806bb126fd241ec2f_58.png)

然后，查看当前正在使用的方案：
```bash
tuned-adm active
```
如果 `active` 命令显示的结果与 `recommend` 命令推荐的结果不一致，我们就需要应用推荐的方案。例如，应用 `virtual-guest` 方案：
```bash
tuned-adm profile virtual-guest
```
应用后，再次使用 `tuned-adm active` 命令确认当前活动方案已切换成功。

## 总结

![](img/68a7fc560227372806bb126fd241ec2f_60.png)

![](img/68a7fc560227372806bb126fd241ec2f_62.png)

本节课中我们一起学习了如何使用红帽的 `tuned` 工具进行系统调优。我们了解到 `tuned` 服务提供了预设的优化方案，大大简化了调优过程。关键点在于使用 `tuned-adm` 命令的 `list`、`recommend`、`active` 和 `profile` 子命令来查看、选择和验证优化方案。在考试中，这道题目的核心操作通常就是运行 `tuned-adm recommend` 查看推荐方案，并确保当前活动方案（`tuned-adm active`）与之匹配，必要时使用 `tuned-adm profile` 进行切换。整个过程简洁明了，是考试中相对容易的题目。