# Linux系统管理：2.04：使用tuned进行系统调优 🔧

![](img/e3f50f584dd39c2a863a0e0b7d81aefe_1.png)

![](img/e3f50f584dd39c2a863a0e0b7d81aefe_3.png)

![](img/e3f50f584dd39c2a863a0e0b7d81aefe_5.png)

在本节课中，我们将学习如何使用红帽系统自带的 `tuned` 工具来轻松配置系统调优。系统调优听起来复杂，但通过 `tuned`，我们只需选择红帽预设的优化方案即可，非常适合初学者和RHCSA考生掌握。

## 概述：什么是系统调优？

![](img/e3f50f584dd39c2a863a0e0b7d81aefe_7.png)

![](img/e3f50f584dd39c2a863a0e0b7d81aefe_9.png)

![](img/e3f50f584dd39c2a863a0e0b7d81aefe_11.png)

系统调优（System Tuning）指的是调整操作系统内核参数，以优化其在特定应用场景下的性能。传统方法需要手动修改 `/proc` 或 `/etc/sysctl.conf` 中的大量参数，过程繁琐且容易出错。

![](img/e3f50f584dd39c2a863a0e0b7d81aefe_13.png)

红帽的 `tuned` 服务则提供了大量预设的优化方案（称为“配置集”或“profile”），我们只需根据系统用途（如虚拟机、数据库服务器、桌面环境）选择合适的方案，`tuned` 便会自动应用对应的优化设置。

## tuned服务与安装

`tuned` 是一个系统服务，在红帽7和红帽8系统中通常默认安装并启用。我们可以通过以下命令确认：

```bash
systemctl status tuned
```

![](img/e3f50f584dd39c2a863a0e0b7d81aefe_15.png)

如果服务未运行，可使用以下命令启用并启动它：

![](img/e3f50f584dd39c2a863a0e0b7d81aefe_17.png)

![](img/e3f50f584dd39c2a863a0e0b7d81aefe_19.png)

```bash
systemctl enable --now tuned
```

![](img/e3f50f584dd39c2a863a0e0b7d81aefe_21.png)

![](img/e3f50f584dd39c2a863a0e0b7d81aefe_23.png)

## 核心管理工具：tuned-adm

![](img/e3f50f584dd39c2a863a0e0b7d81aefe_25.png)

管理 `tuned` 的核心命令是 `tuned-adm`。直接运行此命令可以查看其用法。

![](img/e3f50f584dd39c2a863a0e0b7d81aefe_27.png)

以下是 `tuned-adm` 最常用的几个指令：

![](img/e3f50f584dd39c2a863a0e0b7d81aefe_29.png)

*   **`list`**：列出所有可用的优化方案。
*   **`active`**：查看当前正在使用的优化方案。
*   **`recommend`**：显示系统推荐的优化方案。
*   **`profile`**：切换到指定的优化方案。

![](img/e3f50f584dd39c2a863a0e0b7d81aefe_31.png)

![](img/e3f50f584dd39c2a863a0e0b7d81aefe_33.png)

![](img/e3f50f584dd39c2a863a0e0b7d81aefe_35.png)

## 实践操作步骤

![](img/e3f50f584dd39c2a863a0e0b7d81aefe_37.png)

上一节我们介绍了核心命令，本节中我们来看看如何具体操作。

![](img/e3f50f584dd39c2a863a0e0b7d81aefe_39.png)

![](img/e3f50f584dd39c2a863a0e0b7d81aefe_41.png)

首先，查看系统推荐使用哪种优化方案：

![](img/e3f50f584dd39c2a863a0e0b7d81aefe_43.png)

```bash
tuned-adm recommend
```
命令输出可能类似 `virtual-guest`，这表示系统推荐使用为虚拟机客户机优化的方案。

![](img/e3f50f584dd39c2a863a0e0b7d81aefe_45.png)

接着，我们可以列出所有可用的方案，看看有哪些选择：

![](img/e3f50f584dd39c2a863a0e0b7d81aefe_47.png)

```bash
tuned-adm list
```
输出会显示一个列表，例如 `balanced`（平衡模式）、`throughput-performance`（吞吐性能模式）、`virtual-guest`（虚拟机客户机模式）等，每个都有简要说明。

![](img/e3f50f584dd39c2a863a0e0b7d81aefe_49.png)

然后，查看当前正在使用的方案：

![](img/e3f50f584dd39c2a863a0e0b7d81aefe_51.png)

```bash
tuned-adm active
```
如果当前方案与推荐方案不一致，我们需要进行切换。使用 `profile` 指令切换到推荐方案（例如 `virtual-guest`）：

![](img/e3f50f584dd39c2a863a0e0b7d81aefe_53.png)

```bash
tuned-adm profile virtual-guest
```
最后，再次确认当前活动方案已更改：

```bash
tuned-adm active
```

![](img/e3f50f584dd39c2a863a0e0b7d81aefe_55.png)

![](img/e3f50f584dd39c2a863a0e0b7d81aefe_56.png)

## 考试与日常应用提示

![](img/e3f50f584dd39c2a863a0e0b7d81aefe_57.png)

![](img/e3f50f584dd39c2a863a0e0b7d81aefe_58.png)

在RHCSA考试中，系统调优题目通常非常简单。很可能系统已经安装了 `tuned` 并运行了推荐方案。因此，解题步骤往往是：
1.  运行 `tuned-adm recommend` 获取推荐方案。
2.  运行 `tuned-adm active` 查看当前方案。
3.  如果两者不同，则使用 `tuned-adm profile [方案名]` 进行切换。

在日常工作中，`tuned` 极大简化了调优工作。如果你想深入了解某个优化方案的具体参数，可以查看 `/etc/tuned/` 和 `/usr/lib/tuned/` 目录下对应方案名的配置文件。

![](img/e3f50f584dd39c2a863a0e0b7d81aefe_60.png)

## 总结

![](img/e3f50f584dd39c2a863a0e0b7d81aefe_62.png)

![](img/e3f50f584dd39c2a863a0e0b7d81aefe_64.png)

本节课中我们一起学习了如何使用 `tuned` 工具进行系统调优。我们了解到 `tuned` 通过提供预设配置集简化了调优过程，掌握了使用 `tuned-adm` 命令查看、推荐和切换优化方案的方法。记住，对于RHCSA考试，这道题的关键在于验证并确保系统使用了推荐的优化方案。