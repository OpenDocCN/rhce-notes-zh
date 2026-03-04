# Unix&Linux快速入门超详细教程-7天通关RHCE：P9：02-3-4 Linux系统硬盘分区对应标识 🖥️💾

![](img/7e5d15101ddbc0e05b386a47bdfdbd9c_0.png)

![](img/7e5d15101ddbc0e05b386a47bdfdbd9c_2.png)

![](img/7e5d15101ddbc0e05b386a47bdfdbd9c_4.png)

## 概述
在本节课中，我们将学习Linux系统中硬盘分区的命名规则。理解硬盘接口类型与分区标识符的对应关系，是进行磁盘管理和系统安装的基础。

![](img/7e5d15101ddbc0e05b386a47bdfdbd9c_6.png)

![](img/7e5d15101ddbc0e05b386a47bdfdbd9c_8.png)

![](img/7e5d15101ddbc0e05b386a47bdfdbd9c_10.png)

上一节我们介绍了硬盘分区、MBR以及硬盘接口类型。本节中我们来看看Linux系统如何为不同的硬盘和分区分配标识符。

## Linux分区对应关系
Linux系统使用设备名称来标识硬盘和分区。设备文件位于 `/dev` 目录下，其命名规则与硬盘接口类型密切相关。

以下是硬盘接口类型与设备标识符的对应关系：
*   **IDE接口硬盘**：使用 `h` 来表示。
*   **SATA、SCSI、SAS接口硬盘，以及USB接口的U盘或移动硬盘**：统一使用 `s` 来表示。

![](img/7e5d15101ddbc0e05b386a47bdfdbd9c_12.png)

![](img/7e5d15101ddbc0e05b386a47bdfdbd9c_14.png)

![](img/7e5d15101ddbc0e05b386a47bdfdbd9c_16.png)

![](img/7e5d15101ddbc0e05b386a47bdfdbd9c_18.png)

## 硬盘与分区命名详解
了解基本对应关系后，我们来看具体的命名规则。设备名称的格式通常为 `/dev/[接口标识][盘序字母][分区号]`。

![](img/7e5d15101ddbc0e05b386a47bdfdbd9c_20.png)

![](img/7e5d15101ddbc0e05b386a47bdfdbd9c_21.png)

![](img/7e5d15101ddbc0e05b386a47bdfdbd9c_23.png)

### 硬盘命名
首先确定是哪一块硬盘。系统会按照检测顺序为硬盘分配字母序号。

![](img/7e5d15101ddbc0e05b386a47bdfdbd9c_25.png)

![](img/7e5d15101ddbc0e05b386a47bdfdbd9c_27.png)

![](img/7e5d15101ddbc0e05b386a47bdfdbd9c_28.png)

*   第一块IDE硬盘：`/dev/hda`
*   第二块IDE硬盘：`/dev/hdb`
*   第一块SATA硬盘：`/dev/sda`
*   第三块SATA硬盘：`/dev/sdc`

![](img/7e5d15101ddbc0e05b386a47bdfdbd9c_30.png)

![](img/7e5d15101ddbc0e05b386a47bdfdbd9c_32.png)

### 分区命名
在硬盘标识后加上数字，即为分区标识。主分区和扩展分区的编号为1-4，逻辑分区的编号从5开始。

![](img/7e5d15101ddbc0e05b386a47bdfdbd9c_34.png)

以下是几个命名示例：
*   **IDE第一块硬盘的第一个主分区**：`/dev/hda1`
*   **IDE第一块硬盘的第一个逻辑分区**：`/dev/hda5` (逻辑分区编号从5开始)
*   **SATA第四块硬盘的第二个主分区**：`/dev/sdd2` (第四块盘字母为d)

![](img/7e5d15101ddbc0e05b386a47bdfdbd9c_36.png)

![](img/7e5d15101ddbc0e05b386a47bdfdbd9c_38.png)

![](img/7e5d15101ddbc0e05b386a47bdfdbd9c_40.png)

![](img/7e5d15101ddbc0e05b386a47bdfdbd9c_41.png)

## 文件系统与格式化
分区完成后，必须经过格式化才能使用。格式化的核心目的是为分区赋予特定的**文件系统格式**。

![](img/7e5d15101ddbc0e05b386a47bdfdbd9c_43.png)

每种操作系统都有自己支持的文件系统格式：
*   **Windows常见格式**：FAT32, NTFS
*   **Linux常见格式**：ext3, ext4, XFS

![](img/7e5d15101ddbc0e05b386a47bdfdbd9c_45.png)

![](img/7e5d15101ddbc0e05b386a47bdfdbd9c_47.png)

文件系统负责在应用程序（如QQ、微信）和存储设备驱动之间建立桥梁，管理文件的存储和访问请求。因此，格式化并选择正确的文件系统格式是使用磁盘前的必要步骤。

![](img/7e5d15101ddbc0e05b386a47bdfdbd9c_49.png)

![](img/7e5d15101ddbc0e05b386a47bdfdbd9c_51.png)

![](img/7e5d15101ddbc0e05b386a47bdfdbd9c_53.png)

## 总结
本节课中我们一起学习了Linux系统下硬盘分区的标识规则。我们了解到：
1.  IDE接口硬盘用 `h` 标识，SATA/SCSI等接口硬盘用 `s` 标识。
2.  分区标识符遵循 `/dev/[接口标识][盘序][分区号]` 的格式，逻辑分区编号从5开始。
3.  格式化是为分区建立**文件系统**（如ext4, XFS）的必要过程，使操作系统能够管理分区上的数据。

![](img/7e5d15101ddbc0e05b386a47bdfdbd9c_55.png)

理解这些规则，将帮助你在后续的磁盘管理和系统安装中清晰地识别和操作不同的存储设备。