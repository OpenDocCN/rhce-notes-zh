# RHCSA认证精讲教程：P8：2.03-yum-dnf软件管理

![](img/270e2bfd57d6a5c61e18312eca7e42dc_0.png)

![](img/270e2bfd57d6a5c61e18312eca7e42dc_2.png)

![](img/270e2bfd57d6a5c61e18312eca7e42dc_4.png)

![](img/270e2bfd57d6a5c61e18312eca7e42dc_5.png)

![](img/270e2bfd57d6a5c61e18312eca7e42dc_7.png)

![](img/270e2bfd57d6a5c61e18312eca7e42dc_9.png)

在本节课中，我们将要学习如何使用 `yum` 和 `dnf` 工具来管理软件包。这是RHCSA考试中的核心技能之一，也是日常Linux系统管理的基础工作。

![](img/270e2bfd57d6a5c61e18312eca7e42dc_11.png)

![](img/270e2bfd57d6a5c61e18312eca7e42dc_13.png)

上一节我们介绍了如何配置 `yum` 软件仓库，本节中我们来看看如何使用这些仓库来查询、安装和卸载软件包。

![](img/270e2bfd57d6a5c61e18312eca7e42dc_15.png)

## 工具选择：yum 与 dnf

![](img/270e2bfd57d6a5c61e18312eca7e42dc_17.png)

![](img/270e2bfd57d6a5c61e18312eca7e42dc_19.png)

在红帽8系统中，`yum` 工具被一个名为 `dnf` 的新工具所取代。两者的用法基本相同。为了与大多数同学的习惯保持一致，本教程主要使用 `yum` 命令进行讲解，但所有操作均可用 `dnf` 命令替代。例如，查看仓库的命令既可以是 `yum repolist`，也可以是 `dnf repolist`。

![](img/270e2bfd57d6a5c61e18312eca7e42dc_21.png)

## 核心命令详解

![](img/270e2bfd57d6a5c61e18312eca7e42dc_23.png)

![](img/270e2bfd57d6a5c61e18312eca7e42dc_25.png)

以下是 `yum` 工具最常用的一些命令及其功能。

![](img/270e2bfd57d6a5c61e18312eca7e42dc_27.png)

### 查询软件包

![](img/270e2bfd57d6a5c61e18312eca7e42dc_29.png)

![](img/270e2bfd57d6a5c61e18312eca7e42dc_31.png)

![](img/270e2bfd57d6a5c61e18312eca7e42dc_32.png)

查询是软件包管理的第一步，可以帮助我们了解软件是否已安装、其版本信息以及来源。

![](img/270e2bfd57d6a5c61e18312eca7e42dc_34.png)

![](img/270e2bfd57d6a5c61e18312eca7e42dc_36.png)

![](img/270e2bfd57d6a5c61e18312eca7e42dc_37.png)

*   **`yum list`**：此命令用于列出软件包的安装情况。
    *   命令格式：`yum list [软件包名]`
    *   如果不跟任何软件包名，将列出所有已安装和可用的软件包。
    *   如果指定软件包名，将显示该包的详细信息。已安装的包前会标记 `@` 符号，并归类在 `Installed Packages` 下；未安装但仓库中可用的包则归类在 `Available Packages` 下。
    *   如果仓库中不存在指定软件包，命令会报错提示 `No matching packages to list`。

![](img/270e2bfd57d6a5c61e18312eca7e42dc_39.png)

![](img/270e2bfd57d6a5c61e18312eca7e42dc_41.png)

*   **`yum info`**：此命令用于查询指定软件包的详细描述信息。
    *   命令格式：`yum info <软件包名>`
    *   执行后会显示软件包的名称、版本、来源、摘要和详细描述。这在安装前了解软件功能时非常有用。

![](img/270e2bfd57d6a5c61e18312eca7e42dc_43.png)

![](img/270e2bfd57d6a5c61e18312eca7e42dc_45.png)

![](img/270e2bfd57d6a5c61e18312eca7e42dc_47.png)

![](img/270e2bfd57d6a5c61e18312eca7e42dc_48.png)

*   **`yum provides`**：此命令用于查询某个特定文件或命令是由哪个软件包提供的。
    *   命令格式：`yum provides <文件路径或命令名>`
    *   例如，当系统缺少 `ifconfig` 命令时，可以使用 `yum provides ifconfig` 来查找提供该命令的软件包（通常是 `net-tools`）。在红帽8中，也可以直接使用命令名进行查询。

![](img/270e2bfd57d6a5c61e18312eca7e42dc_50.png)

![](img/270e2bfd57d6a5c61e18312eca7e42dc_51.png)

*   **`yum search`**：此命令用于根据关键词在软件包名称和描述中搜索相关的软件包。
    *   命令格式：`yum search <关键词>`
    *   当你不确定软件包完整名称时，可以使用此命令进行模糊查找。

![](img/270e2bfd57d6a5c61e18312eca7e42dc_53.png)

![](img/270e2bfd57d6a5c61e18312eca7e42dc_55.png)

![](img/270e2bfd57d6a5c61e18312eca7e42dc_57.png)

![](img/270e2bfd57d6a5c61e18312eca7e42dc_59.png)

### 安装与卸载软件包

![](img/270e2bfd57d6a5c61e18312eca7e42dc_61.png)

安装和卸载是软件包管理最核心的操作。

![](img/270e2bfd57d6a5c61e18312eca7e42dc_63.png)

![](img/270e2bfd57d6a5c61e18312eca7e42dc_65.png)

*   **`yum install`**：此命令用于安装指定的软件包。
    *   命令格式：`yum install <软件包名1> [软件包名2] ...`
    *   可以同时安装多个软件包，用空格分隔。
    *   如果记不清完整包名，可以使用通配符 `*`，例如 `yum install “bash*comp*”`。
    *   安装过程中，系统会提示确认下载和安装，输入 `y` 继续，输入 `n` 取消。
    *   为了自动化安装，可以添加 `-y` 选项，让命令自动对所有确认提示回答 “yes”。命令格式为：`yum -y install <软件包名>`。

![](img/270e2bfd57d6a5c61e18312eca7e42dc_67.png)

![](img/270e2bfd57d6a5c61e18312eca7e42dc_69.png)

![](img/270e2bfd57d6a5c61e18312eca7e42dc_71.png)

![](img/270e2bfd57d6a5c61e18312eca7e42dc_72.png)

*   **`yum remove`**：此命令用于卸载指定的软件包。
    *   命令格式：`yum remove <软件包名>`
    *   同样可以使用 `-y` 选项来自动确认。**注意**：卸载一个软件包时，依赖于它的其他软件包也可能被一并卸载。

![](img/270e2bfd57d6a5c61e18312eca7e42dc_74.png)

### 其他实用命令

![](img/270e2bfd57d6a5c61e18312eca7e42dc_76.png)

![](img/270e2bfd57d6a5c61e18312eca7e42dc_77.png)

![](img/270e2bfd57d6a5c61e18312eca7e42dc_78.png)

![](img/270e2bfd57d6a5c61e18312eca7e42dc_79.png)

除了基本的增删查，还有一些维护性命令。

![](img/270e2bfd57d6a5c61e18312eca7e42dc_81.png)

*   **`yum update`**：此命令用于将已安装的软件包升级到仓库中的最新版本。
*   **`yum reinstall`**：此命令用于重新安装指定的软件包。
    *   这适用于软件包文件被意外删除或损坏的情况。**注意**：如果文件是被管理员修改而非删除，重装可能不会覆盖已修改的文件，需要先手动删除损坏的文件再执行重装。
*   **`yum clean`**：此命令用于清除 `yum` 的缓存数据（如下载的仓库元数据）。
*   **`yum repolist`**：此命令用于列出所有已配置并启用的软件仓库。

## 关于软件包依赖

`yum` 和 `dnf` 的一个关键优势是能自动处理**软件包依赖关系**。
*   **安装时**：如果你安装软件包A，而A的正常运行需要软件包B和C，那么 `yum` 会自动将B和C一并安装。
*   **卸载时**：如果你卸载软件包B，而软件包D依赖于B，那么 `yum` 可能会提示或直接将D也卸载（取决于依赖关系的强弱）。
*   **重装时**：`yum reinstall` 通常只影响目标软件包本身，不会处理其依赖包，因为依赖关系仍然存在。

![](img/270e2bfd57d6a5c61e18312eca7e42dc_83.png)

![](img/270e2bfd57d6a5c61e18312eca7e42dc_84.png)

本节课中我们一起学习了 `yum/dnf` 软件包管理工具的核心用法。我们掌握了如何查询软件包信息、安装、卸载、升级和重装软件包，并理解了工具自动处理依赖关系的机制。熟练运用这些命令，是高效管理红帽Linux系统软件环境的基础。