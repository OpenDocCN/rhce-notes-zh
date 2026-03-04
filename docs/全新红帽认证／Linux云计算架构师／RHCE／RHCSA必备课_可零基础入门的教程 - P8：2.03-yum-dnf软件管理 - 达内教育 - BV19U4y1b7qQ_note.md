# Linux软件包管理：2.03：yum/dnf软件管理 📦

![](img/03e8da6055119a90e162de91c387ecb4_0.png)

![](img/03e8da6055119a90e162de91c387ecb4_2.png)

![](img/03e8da6055119a90e162de91c387ecb4_4.png)

![](img/03e8da6055119a90e162de91c387ecb4_5.png)

![](img/03e8da6055119a90e162de91c387ecb4_7.png)

在本节课中，我们将要学习如何使用 `yum` 和 `dnf` 工具来管理软件包。这包括查询、安装、卸载软件包等核心操作，是Linux系统管理的基础技能。

![](img/03e8da6055119a90e162de91c387ecb4_9.png)

---

![](img/03e8da6055119a90e162de91c387ecb4_11.png)

## 软件包管理工具简介

![](img/03e8da6055119a90e162de91c387ecb4_13.png)

上一节我们介绍了如何配置yum软件仓库。本节中我们来看看如何使用这些仓库来管理软件包。

![](img/03e8da6055119a90e162de91c387ecb4_15.png)

在RHEL 8及更高版本中，`yum` 命令已被 `dnf` 取代，但两者用法基本一致。为了保持与大多数用户的习惯一致，本教程主要使用 `yum` 进行演示，但您也可以使用 `dnf` 命令，效果相同。

![](img/03e8da6055119a90e162de91c387ecb4_17.png)

![](img/03e8da6055119a90e162de91c387ecb4_19.png)

以下是 `yum` 命令的一些常用操作：

*   **`yum clean all`**：清除本地缓存。
*   **`yum repolist`**：列出所有已配置的软件仓库。
*   **`yum install`**：安装指定的软件包。
*   **`yum list`**：列出软件包的安装情况。
*   **`yum info`**：查询软件包的详细信息。
*   **`yum provides`**：查询哪个软件包提供了某个文件或命令。
*   **`yum search`**：根据关键词搜索软件包。
*   **`yum remove`**：卸载指定的软件包。
*   **`yum update`**：升级软件包。
*   **`yum reinstall`**：重新安装指定的软件包。

![](img/03e8da6055119a90e162de91c387ecb4_21.png)

---

![](img/03e8da6055119a90e162de91c387ecb4_23.png)

## 安装软件包

安装软件包是管理软件的核心操作。使用 `yum install` 命令可以轻松完成。

![](img/03e8da6055119a90e162de91c387ecb4_25.png)

![](img/03e8da6055119a90e162de91c387ecb4_27.png)

以下是安装软件包时的几种常见用法：

*   **安装单个软件包**：`yum install 软件包名`
*   **安装多个软件包**：`yum install 软件包名1 软件包名2`
*   **使用通配符安装**：`yum install “bash-compl*”` （当记不清完整包名时）
*   **自动确认安装**：`yum -y install 软件包名` （`-y` 选项会自动对所有确认提示回答“是”）

![](img/03e8da6055119a90e162de91c387ecb4_29.png)

![](img/03e8da6055119a90e162de91c387ecb4_31.png)

例如，安装 `nmap` 扫描工具：
```bash
yum -y install nmap
```

![](img/03e8da6055119a90e162de91c387ecb4_33.png)

![](img/03e8da6055119a90e162de91c387ecb4_34.png)

---

![](img/03e8da6055119a90e162de91c387ecb4_36.png)

## 查询软件包

![](img/03e8da6055119a90e162de91c387ecb4_38.png)

![](img/03e8da6055119a90e162de91c387ecb4_39.png)

在安装或管理软件前，通常需要先查询软件包的信息。

![](img/03e8da6055119a90e162de91c387ecb4_41.png)

![](img/03e8da6055119a90e162de91c387ecb4_43.png)

### 列出软件包状态

![](img/03e8da6055119a90e162de91c387ecb4_45.png)

![](img/03e8da6055119a90e162de91c387ecb4_47.png)

使用 `yum list` 命令可以查看软件包的安装状态。

![](img/03e8da6055119a90e162de91c387ecb4_49.png)

![](img/03e8da6055119a90e162de91c387ecb4_50.png)

以下是查询软件包状态的几种情况：

![](img/03e8da6055119a90e162de91c387ecb4_52.png)

![](img/03e8da6055119a90e162de91c387ecb4_53.png)

*   **查询特定包**：`yum list httpd`。如果已安装，输出行前会有 `@` 符号，并显示在 “Installed Packages” 下。
*   **查询未安装的包**：如果包未安装但仓库中有，会显示在 “Available Packages” 下，且前面没有 `@` 符号。
*   **查询不存在的包**：如果包名错误或仓库中没有，命令会报错 “No matching packages to list”。
*   **列出所有包**：`yum list` 会列出所有已安装和可用的软件包。

### 查看软件包详细信息

![](img/03e8da6055119a90e162de91c387ecb4_55.png)

![](img/03e8da6055119a90e162de91c387ecb4_57.png)

![](img/03e8da6055119a90e162de91c387ecb4_59.png)

如果想了解一个软件包的具体功能，可以使用 `yum info` 命令。

![](img/03e8da6055119a90e162de91c387ecb4_61.png)

例如，查看 `bash-completion` 包的描述：
```bash
yum info bash-completion
```
命令输出会包含 `Summary`（摘要）和 `Description`（详细描述）字段，说明了软件包的作用。

### 查询文件来源

![](img/03e8da6055119a90e162de91c387ecb4_63.png)

![](img/03e8da6055119a90e162de91c387ecb4_65.png)

有时我们想知道某个命令或文件是由哪个软件包提供的，可以使用 `yum provides` 命令。

![](img/03e8da6055119a90e162de91c387ecb4_67.png)

![](img/03e8da6055119a90e162de91c387ecb4_69.png)

例如，查询 `route` 命令的来源：
```bash
yum provides route
```
或者使用更精确的路径查询（这是标准用法）：
```bash
yum provides */route
```
在RHEL 8中，直接使用命令名通常也能查到结果。

![](img/03e8da6055119a90e162de91c387ecb4_71.png)

![](img/03e8da6055119a90e162de91c387ecb4_73.png)

### 搜索软件包

![](img/03e8da6055119a90e162de91c387ecb4_74.png)

![](img/03e8da6055119a90e162de91c387ecb4_76.png)

如果不确定软件包的确切名称，可以使用 `yum search` 命令根据关键词进行搜索。

![](img/03e8da6055119a90e162de91c387ecb4_78.png)

例如，搜索与 “editor” 相关的包：
```bash
yum search editor
```

![](img/03e8da6055119a90e162de91c387ecb4_79.png)

![](img/03e8da6055119a90e162de91c387ecb4_80.png)

![](img/03e8da6055119a90e162de91c387ecb4_81.png)

---

## 卸载与维护软件包

![](img/03e8da6055119a90e162de91c387ecb4_83.png)

了解如何卸载和修复软件包同样重要。

以下是卸载和维护软件包的相关操作：

*   **卸载软件包**：`yum remove 软件包名`。卸载时，依赖于此包的其他软件包也可能被一同卸载。
*   **升级软件包**：`yum update 软件包名`。当仓库中有新版本时，用于升级。
*   **重新安装软件包**：`yum reinstall 软件包名`。这相当于先卸载再安装，可用于恢复被误删或损坏的软件文件。注意，如果文件被修改但未删除，重装可能不会覆盖原有修改。

需要特别注意的是软件包的**依赖关系**。安装一个包时会自动安装其依赖包；卸载一个包时，也可能卸载那些依赖它的其他包。而 `reinstall` 操作通常不会影响依赖包。

---

![](img/03e8da6055119a90e162de91c387ecb4_85.png)

![](img/03e8da6055119a90e162de91c387ecb4_86.png)

本节课中我们一起学习了 `yum/dnf` 软件包管理工具的核心用法，包括安装、查询、卸载和升级软件包。理解这些操作和软件包之间的依赖关系，是高效管理Linux系统软件环境的基础。请务必通过实践练习来巩固这些命令。