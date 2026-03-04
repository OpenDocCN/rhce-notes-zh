# Linux运维培训教程：P9：软链接与硬链接、获取命令帮助、系统运行级别、关机与重启 🔗

![](img/8ce9487c32dcd0dbd531ec35e276b116_1.png)

![](img/8ce9487c32dcd0dbd531ec35e276b116_3.png)

![](img/8ce9487c32dcd0dbd531ec35e276b116_5.png)

![](img/8ce9487c32dcd0dbd531ec35e276b116_7.png)

![](img/8ce9487c32dcd0dbd531ec35e276b116_9.png)

![](img/8ce9487c32dcd0dbd531ec35e276b116_11.png)

在本节课中，我们将要学习Linux系统中的几个重要概念：软链接与硬链接的区别与创建、如何获取命令的帮助信息、系统的不同运行级别以及如何进行关机与重启操作。这些知识是理解Linux文件系统和日常系统管理的基础。

![](img/8ce9487c32dcd0dbd531ec35e276b116_13.png)

![](img/8ce9487c32dcd0dbd531ec35e276b116_15.png)

![](img/8ce9487c32dcd0dbd531ec35e276b116_17.png)

![](img/8ce9487c32dcd0dbd531ec35e276b116_19.png)

![](img/8ce9487c32dcd0dbd531ec35e276b116_21.png)

## 软链接与硬链接

![](img/8ce9487c32dcd0dbd531ec35e276b116_23.png)

![](img/8ce9487c32dcd0dbd531ec35e276b116_25.png)

![](img/8ce9487c32dcd0dbd531ec35e276b116_27.png)

上一节我们介绍了文件和目录的基本操作，本节中我们来看看Linux中特殊的文件类型——链接文件。链接文件类似于Windows系统中的快捷方式，但Linux中分为软链接和硬链接两种，它们有各自的特点。

![](img/8ce9487c32dcd0dbd531ec35e276b116_29.png)

![](img/8ce9487c32dcd0dbd531ec35e276b116_31.png)

![](img/8ce9487c32dcd0dbd531ec35e276b116_33.png)

### 软链接

![](img/8ce9487c32dcd0dbd531ec35e276b116_35.png)

![](img/8ce9487c32dcd0dbd531ec35e276b116_37.png)

![](img/8ce9487c32dcd0dbd531ec35e276b116_39.png)

软链接（Symbolic Link）类似于Windows的快捷方式。以下是软链接的主要特点：

*   **可以跨分区**：源文件和链接文件可以位于不同的磁盘分区。
*   **可以对目录创建链接**：可以为目录创建软链接。
*   **依赖源文件**：如果删除源文件，软链接将失效（变为“坏链接”）。

![](img/8ce9487c32dcd0dbd531ec35e276b116_41.png)

创建软链接的命令格式是：
```bash
ln -s <源文件绝对路径> <目标链接路径>
```
**注意**：创建链接时，建议使用源文件的绝对路径，否则可能导致链接失效。

![](img/8ce9487c32dcd0dbd531ec35e276b116_43.png)

例如，在`/opt`目录创建一个文件，并为其在`/media`目录创建一个软链接：
```bash
touch /opt/hello_soft
ln -s /opt/hello_soft /media/
```
此时，对`/opt/hello_soft`文件的任何修改，都会同步反映在`/media/hello_soft`这个链接文件中。反之亦然。如果删除`/opt/hello_soft`源文件，`/media/hello_soft`链接将闪烁，表示不可用。

### 硬链接

硬链接（Hard Link）与软链接有显著区别。以下是硬链接的主要特点：

![](img/8ce9487c32dcd0dbd531ec35e276b116_45.png)

*   **不能跨分区**：源文件和硬链接必须在同一个文件系统（分区）内。
*   **不能对目录创建**：只能对普通文件创建硬链接。
*   **独立于源文件**：删除源文件后，硬链接文件仍然可用，因为硬链接和源文件共享相同的`inode`（索引节点），本质上是同一个文件的多个名称。

![](img/8ce9487c32dcd0dbd531ec35e276b116_47.png)

创建硬链接的命令格式是（不加 `-s` 选项）：
```bash
ln <源文件路径> <目标链接路径>
```

例如，为`/opt`目录下的文件创建硬链接到`/media`目录：
```bash
touch /opt/hello_hard
ln /opt/hello_hard /media/
```
此时，`/media/hello_hard`看起来就像一个普通文件。即使删除`/opt/hello_hard`，`/media/hello_hard`中的内容依然存在且可访问。

![](img/8ce9487c32dcd0dbd531ec35e276b116_49.png)

![](img/8ce9487c32dcd0dbd531ec35e276b116_51.png)

![](img/8ce9487c32dcd0dbd531ec35e276b116_52.png)

**核心理解**：可以将硬链接理解为给文件起了另一个“别名”，它们指向磁盘上同一块数据区域。而软链接则是一个独立的文件，其内容仅仅是存储了指向源文件的路径信息。

## 获取命令帮助

在Linux中，当你忘记某个命令的用法或想了解其更多选项时，可以使用系统自带的帮助功能。

以下是几种获取命令帮助的方法：

![](img/8ce9487c32dcd0dbd531ec35e276b116_54.png)

*   **`--help` 选项**：大多数命令都支持此选项，能快速列出命令的基本用法和选项说明。这是最常用、最直接的方式。
    ```bash
    ls --help
    ```

*   **`man` 命令**：提供更详细、更正式的命令手册（manual pages），包含描述、选项、示例等。使用上下箭头或`Page Up`/`Page Down`键浏览，按`q`键退出。
    ```bash
    man ls
    ```

*   **`info` 命令**：提供另一种格式的帮助文档，通常比`man`更结构化，但内容可能更古老，现在较少使用。
    ```bash
    info ls
    ```

![](img/8ce9487c32dcd0dbd531ec35e276b116_56.png)

*   **`help` 命令**：主要用于查看Shell内置命令（如`cd`, `echo`）的帮助。对于外部命令（如`ls`, `cp`）通常无效。
    ```bash
    help cd
    ```

![](img/8ce9487c32dcd0dbd531ec35e276b116_58.png)

![](img/8ce9487c32dcd0dbd531ec35e276b116_60.png)

**建议**：对于初学者，如果英文帮助文档阅读困难，可以优先使用`--help`选项获取核心信息，或直接通过互联网搜索引擎（如百度）查找中文教程和示例，这通常更高效易懂。

![](img/8ce9487c32dcd0dbd531ec35e276b116_62.png)

## 系统运行级别

![](img/8ce9487c32dcd0dbd531ec35e276b116_64.png)

Linux系统有不同的运行级别（Runlevel），它定义了系统启动后运行哪些服务。掌握运行级别有助于理解系统状态和进行维护。

![](img/8ce9487c32dcd0dbd531ec35e276b116_66.png)

![](img/8ce9487c32dcd0dbd531ec35e276b116_68.png)

![](img/8ce9487c32dcd0dbd531ec35e276b116_70.png)

系统共有7个运行级别（0-6），每个级别有特定用途：

![](img/8ce9487c32dcd0dbd531ec35e276b116_72.png)

![](img/8ce9487c32dcd0dbd531ec35e276b116_74.png)

*   **0：关机**。系统停机状态。
*   **1：单用户模式**。仅`root`用户可登录，所有网络服务关闭，用于系统维护（如忘记root密码）。
*   **2：多用户模式（无NFS）**。允许多用户登录，但不支持网络文件共享（NFS）服务。
*   **3：完全多用户模式（文本模式）**。标准运行级别，系统以命令行界面启动，所有网络服务正常。
*   **4：未使用**。保留，通常与级别3相同。
*   **5：图形化模式**。标准运行级别，系统启动到图形用户界面（GUI）。
*   **6：重启**。系统重新启动。

![](img/8ce9487c32dcd0dbd531ec35e276b116_76.png)

![](img/8ce9487c32dcd0dbd531ec35e276b116_78.png)

![](img/8ce9487c32dcd0dbd531ec35e276b116_80.png)

**查看当前运行级别**：
```bash
runlevel
```
输出如 `N 3`，表示之前级别为N（N表示无），当前级别为3。

![](img/8ce9487c32dcd0dbd531ec35e276b116_82.png)

![](img/8ce9487c32dcd0dbd531ec35e276b116_84.png)

**切换运行级别**（需谨慎，切换至0或6会导致关机或重启）：
```bash
init <级别数字>
# 例如，切换到单用户模式
init 1
```

![](img/8ce9487c32dcd0dbd531ec35e276b116_86.png)

![](img/8ce9487c32dcd0dbd531ec35e276b116_88.png)

![](img/8ce9487c32dcd0dbd531ec35e276b116_90.png)

**设置默认运行级别**：
系统默认运行级别在文件`/etc/inittab`中定义（旧式系统），或使用`systemctl`命令设置（新式系统，如RHEL 9）。
查看默认级别：
```bash
systemctl get-default
```
设置默认级别（例如设为图形界面）：
```bash
systemctl set-default graphical.target
# 或设为文本模式
systemctl set-default multi-user.target
```
通常，服务器默认运行级别为3（文本模式），桌面系统默认运行级别为5（图形模式）。

![](img/8ce9487c32dcd0dbd531ec35e276b116_92.png)

![](img/8ce9487c32dcd0dbd531ec35e276b116_94.png)

![](img/8ce9487c32dcd0dbd531ec35e276b116_96.png)

![](img/8ce9487c32dcd0dbd531ec35e276b116_98.png)

## 关机与重启

![](img/8ce9487c32dcd0dbd531ec35e276b116_100.png)

在命令行下管理服务器时，需要掌握安全的关机与重启命令。

![](img/8ce9487c32dcd0dbd531ec35e276b116_102.png)

![](img/8ce9487c32dcd0dbd531ec35e276b116_104.png)

![](img/8ce9487c32dcd0dbd531ec35e276b116_106.png)

以下是常用的命令：

![](img/8ce9487c32dcd0dbd531ec35e276b116_108.png)

![](img/8ce9487c32dcd0dbd531ec35e276b116_110.png)

![](img/8ce9487c32dcd0dbd531ec35e276b116_112.png)

*   **`poweroff`**：立即关闭系统。**（推荐记忆此命令）**
*   **`shutdown -h now`**：立即关闭系统。`-h`后可以加时间，如`shutdown -h +10`表示10分钟后关机。
*   **`init 0`**：通过切换运行级别到0来关机。
*   **`halt`**：停止系统运行，通常也需要断电。

![](img/8ce9487c32dcd0dbd531ec35e276b116_114.png)

![](img/8ce9487c32dcd0dbd531ec35e276b116_116.png)

![](img/8ce9487c32dcd0dbd531ec35e276b116_118.png)

*   **`reboot`**：立即重启系统。**（推荐记忆此命令）**
*   **`shutdown -r now`**：立即重启系统。`-r`后同样可以加时间。
*   **`init 6`**：通过切换运行级别到6来重启。

![](img/8ce9487c32dcd0dbd531ec35e276b116_120.png)

**注意**：在执行关机或重启前，请确保已保存所有工作。在生产环境中，应提前通知用户。

![](img/8ce9487c32dcd0dbd531ec35e276b116_122.png)

---

![](img/8ce9487c32dcd0dbd531ec35e276b116_124.png)

![](img/8ce9487c32dcd0dbd531ec35e276b116_126.png)

本节课中我们一起学习了Linux中软链接与硬链接的创建与区别，掌握了通过`--help`和`man`获取命令帮助的方法，了解了系统运行级别的概念与作用，并学会了使用`poweroff`和`reboot`等命令进行关机与重启。这些是Linux系统管理中最基础且实用的技能，请务必通过实践加以巩固。