# 红帽认证零基础入门教程：P9：2.03-yum-dnf软件管理 🛠️

![](img/4ae81c0c8425de2da5c7b54ef99e8b47_0.png)

![](img/4ae81c0c8425de2da5c7b54ef99e8b47_2.png)

![](img/4ae81c0c8425de2da5c7b54ef99e8b47_4.png)

![](img/4ae81c0c8425de2da5c7b54ef99e8b47_5.png)

在本节课中，我们将要学习如何使用 `yum` 和 `dnf` 工具来管理软件包。上一节我们介绍了如何配置软件仓库，本节中我们来看看如何利用这些仓库进行软件的查询、安装、卸载等操作。

![](img/4ae81c0c8425de2da5c7b54ef99e8b47_7.png)

![](img/4ae81c0c8425de2da5c7b54ef99e8b47_9.png)

## 概述

`yum` 是 Red Hat 系列 Linux 系统中用于管理 RPM 软件包的核心工具。在 Red Hat 8 及更新的系统中，它被一个名为 `dnf` 的新工具所取代，但两者在基本用法上非常相似。为了保持与大多数学习环境的兼容性，本教程将以 `yum` 命令为例进行讲解，但请记住，您完全可以使用 `dnf` 命令来替代，其效果是相同的。

![](img/4ae81c0c8425de2da5c7b54ef99e8b47_11.png)

## yum/dnf 常用命令详解

![](img/4ae81c0c8425de2da5c7b54ef99e8b47_13.png)

以下是 `yum` 工具的一些核心操作命令。

![](img/4ae81c0c8425de2da5c7b54ef99e8b47_15.png)

### 查询软件包信息

![](img/4ae81c0c8425de2da5c7b54ef99e8b47_17.png)

我们可以使用 `yum list` 命令来查看软件包的安装情况。

**命令格式：**
```bash
yum list [软件包名]
```

![](img/4ae81c0c8425de2da5c7b54ef99e8b47_19.png)

*   如果不跟任何软件包名，此命令会列出所有仓库中可用的软件包以及系统中已安装的软件包。
*   如果指定了软件包名，则只显示该软件包的信息。
*   在输出结果中，已安装的软件包前会标有 `@` 符号或列在 “Installed Packages” 部分；未安装但可用的软件包则列在 “Available Packages” 部分。
*   如果输入的软件包名在仓库中不存在，命令会报错提示 “No matching packages to list”。

![](img/4ae81c0c8425de2da5c7b54ef99e8b47_21.png)

---

除了查看是否安装，我们还可以使用 `yum info` 命令来获取某个软件包的详细描述信息。

![](img/4ae81c0c8425de2da5c7b54ef99e8b47_23.png)

**命令格式：**
```bash
yum info [软件包名]
```

![](img/4ae81c0c8425de2da5c7b54ef99e8b47_25.png)

此命令会显示软件包的名称、版本、来源以及详细的摘要（Summary）和描述（Description），帮助您了解该软件包的具体用途。

---

![](img/4ae81c0c8425de2da5c7b54ef99e8b47_27.png)

![](img/4ae81c0c8425de2da5c7b54ef99e8b47_29.png)

有时，我们可能知道需要某个命令或文件，但不知道它由哪个软件包提供。这时可以使用 `yum provides` 命令进行查询。

![](img/4ae81c0c8425de2da5c7b54ef99e8b47_31.png)

![](img/4ae81c0c8425de2da5c7b54ef99e8b47_32.png)

**命令格式：**
```bash
yum provides [命令或文件路径]
```

例如，查询 `ifconfig` 命令由哪个包提供：
```bash
yum provides ifconfig
```
在较新的系统（如 Red Hat 8）中，您甚至可以直接使用命令名进行查询，系统会自动尝试匹配。

![](img/4ae81c0c8425de2da5c7b54ef99e8b47_34.png)

![](img/4ae81c0c8425de2da5c7b54ef99e8b47_36.png)

![](img/4ae81c0c8425de2da5c7b54ef99e8b47_37.png)

---

![](img/4ae81c0c8425de2da5c7b54ef99e8b47_39.png)

我们还可以根据关键词来搜索相关的软件包，这适用于不确定完整包名的情况。

![](img/4ae81c0c8425de2da5c7b54ef99e8b47_41.png)

**命令格式：**
```bash
yum search [关键词]
```
此命令会在软件包名称和描述信息中搜索包含该关键词的软件包。

![](img/4ae81c0c8425de2da5c7b54ef99e8b47_43.png)

![](img/4ae81c0c8425de2da5c7b54ef99e8b47_45.png)

### 安装与卸载软件包

![](img/4ae81c0c8425de2da5c7b54ef99e8b47_47.png)

![](img/4ae81c0c8425de2da5c7b54ef99e8b47_48.png)

安装软件包是 `yum` 最常用的功能之一，使用 `yum install` 命令。

![](img/4ae81c0c8425de2da5c7b54ef99e8b47_50.png)

![](img/4ae81c0c8425de2da5c7b54ef99e8b47_51.png)

**命令格式：**
```bash
yum install [软件包名1] [软件包名2] ...
```

*   可以同时安装多个软件包，用空格分隔即可。
*   如果记不清完整的包名，可以使用通配符 `*`，例如 `yum install “bash*comp*”`。
*   在安装过程中，系统会提示确认下载和安装，如果您希望自动确认所有提示，可以加上 `-y` 选项：`yum -y install [软件包名]`。
*   **重要特性：** `yum` 在安装一个软件包时，会自动解析并安装它所依赖的其他所有软件包。

![](img/4ae81c0c8425de2da5c7b54ef99e8b47_53.png)

![](img/4ae81c0c8425de2da5c7b54ef99e8b47_55.png)

---

![](img/4ae81c0c8425de2da5c7b54ef99e8b47_57.png)

![](img/4ae81c0c8425de2da5c7b54ef99e8b47_59.png)

与安装相对应，卸载软件包使用 `yum remove` 命令。

**命令格式：**
```bash
yum remove [软件包名]
```

*   同样支持 `-y` 选项来自动确认。
*   **重要特性：** 在卸载一个软件包时，如果其他已安装的软件包依赖于它，那么这些依赖包通常也会被一并卸载。

![](img/4ae81c0c8425de2da5c7b54ef99e8b47_61.png)

![](img/4ae81c0c8425de2da5c7b54ef99e8b47_63.png)

---

![](img/4ae81c0c8425de2da5c7b54ef99e8b47_65.png)

![](img/4ae81c0c8425de2da5c7b54ef99e8b47_67.png)

有时，我们需要重新安装一个软件包以恢复丢失或被错误修改的文件，这时可以使用 `yum reinstall` 命令。

![](img/4ae81c0c8425de2da5c7b54ef99e8b47_69.png)

**命令格式：**
```bash
yum reinstall [软件包名]
```

![](img/4ae81c0c8425de2da5c7b54ef99e8b47_71.png)

![](img/4ae81c0c8425de2da5c7b54ef99e8b47_72.png)

![](img/4ae81c0c8425de2da5c7b54ef99e8b47_74.png)

与先 `remove` 再 `install` 不同，`reinstall` 主要针对指定软件包本身进行操作，对它的依赖包影响较小。但请注意，如果被修改的文件未被删除，重装可能不会覆盖管理员已做的更改。

### 其他实用命令

![](img/4ae81c0c8425de2da5c7b54ef99e8b47_76.png)

![](img/4ae81c0c8425de2da5c7b54ef99e8b47_77.png)

我们可以使用 `yum update` 命令来将已安装的软件包升级到仓库中的最新版本。

![](img/4ae81c0c8425de2da5c7b54ef99e8b47_78.png)

![](img/4ae81c0c8425de2da5c7b54ef99e8b47_79.png)

**命令格式：**
```bash
yum update [软件包名]
```
如果不指定软件包名，则会检查并更新所有可更新的软件包。

---

![](img/4ae81c0c8425de2da5c7b54ef99e8b47_81.png)

`yum` 会将仓库的元数据信息缓存到本地。使用 `yum clean` 命令可以清除这些缓存。

**命令格式：**
```bash
yum clean all
```
在软件仓库配置发生变更后，执行此命令可以确保获取到最新的仓库信息。

---

正如开头所提到的，`yum repolist` 命令用于列出系统中所有已配置并启用的软件仓库。

**命令格式：**
```bash
yum repolist
```

## 总结

![](img/4ae81c0c8425de2da5c7b54ef99e8b47_83.png)

![](img/4ae81c0c8425de2da5c7b54ef99e8b47_84.png)

本节课中我们一起学习了 `yum`/`dnf` 软件包管理工具的核心操作。我们掌握了如何查询软件包信息（`list`, `info`, `provides`, `search`），以及如何进行软件包的安装（`install`）、卸载（`remove`）、重装（`reinstall`）和更新（`update`）。理解 `yum` 自动处理依赖关系的特性对于高效管理系统软件至关重要。这些技能是进行日常系统管理和应对红帽认证考试的基础，请务必熟练掌握。