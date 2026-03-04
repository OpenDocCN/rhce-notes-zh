# RHCE红帽认证全套入门教程：P9：2.04-tuned系统调优 🚀

![](img/8516cee3720a1ec25691bd4c2a0f05db_1.png)

![](img/8516cee3720a1ec25691bd4c2a0f05db_3.png)

![](img/8516cee3720a1ec25691bd4c2a0f05db_5.png)

在本节课中，我们将要学习如何使用红帽系统自带的 `tuned` 工具进行系统调优。这是一个在RHCE考试中可能出现的考点，但操作非常简单，核心是学会使用预设的优化方案。

![](img/8516cee3720a1ec25691bd4c2a0f05db_7.png)

## 什么是系统调优？

![](img/8516cee3720a1ec25691bd4c2a0f05db_9.png)

![](img/8516cee3720a1ec25691bd4c2a0f05db_11.png)

上一节我们介绍了系统管理的基础知识，本节中我们来看看如何优化系统性能。系统调优的英文是 `tune`，在红帽系统中，对应的服务是 `tuned`。它的作用是调整Linux内核的各种参数，以提升系统在不同应用场景下的性能，例如作为数据库服务器、网络服务器或虚拟机时的运行效率。

![](img/8516cee3720a1ec25691bd4c2a0f05db_13.png)

传统的手动调优需要修改 `/proc` 或 `/etc/sysctl.conf` 等文件中的大量内核参数，过程复杂且容易出错。`tuned` 服务的优势在于，它为我们提供了大量预设的、针对不同场景优化好的配置方案，我们只需选择启用即可。

## tuned服务与配置集

`tuned` 服务在红帽系统（RHEL 7/8）中通常默认已安装并启用。它的核心管理命令是 `tuned-adm`。我们可以通过这个命令来查看、选择和验证优化方案，这些方案在 `tuned` 中被称为“配置集”（profile）。

![](img/8516cee3720a1ec25691bd4c2a0f05db_15.png)

以下是 `tuned-adm` 命令的几个关键用法：

![](img/8516cee3720a1ec25691bd4c2a0f05db_17.png)

![](img/8516cee3720a1ec25691bd4c2a0f05db_19.png)

*   **`tuned-adm list`**：列出所有可用的调优配置集。
*   **`tuned-adm active`**：显示当前正在使用的活动配置集。
*   **`tuned-adm recommend`**：显示系统推荐的配置集。这是考试中的关键命令。
*   **`tuned-adm profile <配置集名称>`**：将系统切换到指定的配置集。

![](img/8516cee3720a1ec25691bd4c2a0f05db_21.png)

## 实际操作演示

![](img/8516cee3720a1ec25691bd4c2a0f05db_23.png)

![](img/8516cee3720a1ec25691bd4c2a0f05db_25.png)

现在，让我们通过实际操作来掌握这些命令。首先，我们列出所有可用的配置集。

![](img/8516cee3720a1ec25691bd4c2a0f05db_27.png)

```bash
tuned-adm list
```

![](img/8516cee3720a1ec25691bd4c2a0f05db_29.png)

![](img/8516cee3720a1ec25691bd4c2a0f05db_31.png)

执行后会显示类似以下的列表，其中包含了诸如平衡模式、桌面模式、低延迟网络模式、虚拟机客户机模式等多种方案。

![](img/8516cee3720a1ec25691bd4c2a0f05db_33.png)

![](img/8516cee3720a1ec25691bd4c2a0f05db_35.png)

```
Available profiles:
- balanced                    - General non-specialized tuned profile
- desktop                     - Optimize for the desktop use-case
- latency-performance         - Optimize for deterministic performance
- network-latency             - Optimize for deterministic performance
- network-throughput          - Optimize for streaming network throughput.
- powersave                   - Optimize for low power consumption
- throughput-performance      - Broadly applicable tuning that provides excellent performance
- virtual-guest               - Optimize for running inside a virtual guest
- virtual-host                - Optimize for running KVM guests
Current active profile: virtual-guest
```

![](img/8516cee3720a1ec25691bd4c2a0f05db_37.png)

接下来，我们查看系统为我们推荐的配置集。对于运行在虚拟机中的系统，它很可能会推荐 `virtual-guest`。

![](img/8516cee3720a1ec25691bd4c2a0f05db_39.png)

![](img/8516cee3720a1ec25691bd4c2a0f05db_41.png)

```bash
tuned-adm recommend
```

![](img/8516cee3720a1ec25691bd4c2a0f05db_43.png)

![](img/8516cee3720a1ec25691bd4c2a0f05db_45.png)

输出可能为：
```
virtual-guest
```

然后，我们检查当前正在使用的配置集，确认是否与推荐的一致。

![](img/8516cee3720a1ec25691bd4c2a0f05db_47.png)

![](img/8516cee3720a1ec25691bd4c2a0f05db_49.png)

```bash
tuned-adm active
```

![](img/8516cee3720a1ec25691bd4c2a0f05db_51.png)

如果当前活动配置集与推荐的不一致，我们需要使用 `profile` 子命令进行切换。例如，切换到 `virtual-guest`：

```bash
tuned-adm profile virtual-guest
```

![](img/8516cee3720a1ec25691bd4c2a0f05db_53.png)

切换后，再次使用 `tuned-adm active` 命令确认更改已生效。

![](img/8516cee3720a1ec25691bd4c2a0f05db_55.png)

![](img/8516cee3720a1ec25691bd4c2a0f05db_57.png)

## 总结与注意事项

![](img/8516cee3720a1ec25691bd4c2a0f05db_59.png)

![](img/8516cee3720a1ec25691bd4c2a0f05db_61.png)

本节课中我们一起学习了如何使用 `tuned` 工具进行系统调优。我们了解到 `tuned` 通过提供预设配置集简化了调优过程。关键步骤是：使用 `tuned-adm recommend` 获取系统推荐方案，并使用 `tuned-adm profile` 启用它。

对于RHCE考试，这道题可能非常简单。你需要做的就是登录指定的虚拟机（如 `blue`），检查 `tuned` 服务是否运行，并使用上述命令验证或切换至推荐的配置集（很可能是 `virtual-guest`）。在很多情况下，系统默认已经配置正确，但验证这一步骤是必不可少的。

![](img/8516cee3720a1ec25691bd4c2a0f05db_63.png)

**核心命令总结：**
*   查看推荐配置：`tuned-adm recommend`
*   切换配置：`tuned-adm profile <配置集名称>`
*   验证当前配置：`tuned-adm active`

![](img/8516cee3720a1ec25691bd4c2a0f05db_65.png)

![](img/8516cee3720a1ec25691bd4c2a0f05db_67.png)

记住这些命令，你就能轻松应对考试中关于系统调优的题目。