# Linux系统管理：第6章：硬盘设备管理

![](img/cca78d39ee2819ce4e68467ab194d0dd_1.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_3.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_5.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_7.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_9.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_11.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_13.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_15.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_17.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_19.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_21.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_23.png)

在本节课中，我们将要学习如何管理Linux系统中的硬盘设备，包括添加新硬盘、创建交换分区、设置磁盘配额、使用VDO技术以及理解软硬链接的原理。这些是系统管理员日常工作中必须掌握的核心技能。

![](img/cca78d39ee2819ce4e68467ab194d0dd_25.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_27.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_29.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_31.png)

## 6.6：交换分区管理

![](img/cca78d39ee2819ce4e68467ab194d0dd_33.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_35.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_37.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_39.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_41.png)

上一节我们介绍了硬盘分区的基础知识，本节中我们来看看如何创建和管理交换分区。

![](img/cca78d39ee2819ce4e68467ab194d0dd_43.png)

交换分区（SWAP）的作用是当物理内存不足时，将一部分硬盘空间临时充当内存使用。这样可以将内存中不常用的数据暂存到硬盘，让宝贵的内存资源得到更充分的利用。Windows系统中也有类似的功能，通常表现为一个名为`pagefile.sys`的隐藏文件。

![](img/cca78d39ee2819ce4e68467ab194d0dd_45.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_47.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_49.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_51.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_53.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_55.png)

为交换分区扩容一般分为两步：
1.  查看当前内存使用情况，确认是否需要扩容。
2.  添加新硬盘或使用现有空间，对交换分区进行扩展操作。

![](img/cca78d39ee2819ce4e68467ab194d0dd_57.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_59.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_61.png)

首先，我们使用`free -h`命令查看内存使用情况。输出结果中，“Mem”行代表物理内存，“Swap”行代表交换分区。如果物理内存使用率持续很高（例如超过80%），而交换分区使用量也开始增长，就应考虑扩容。

![](img/cca78d39ee2819ce4e68467ab194d0dd_63.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_65.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_67.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_69.png)

接下来，我们通过添加新硬盘来扩展交换分区。根据之前学习的规则，新添加的第二块硬盘设备文件名通常为`/dev/sdb`。我们使用`fdisk /dev/sdb`命令进入交互式分区工具。

![](img/cca78d39ee2819ce4e68467ab194d0dd_71.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_73.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_75.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_77.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_79.png)

以下是创建扩展分区和逻辑分区的步骤：
*   输入 `n` 新建分区。
*   选择分区类型为扩展分区（`e`）。
*   分区编号可以指定（例如`4`），设备名将变为`/dev/sdb4`。
*   指定分区大小，例如 `+10G`。
*   输入 `n` 新建逻辑分区。
*   指定逻辑分区大小，例如 `+2G`，设备名将自动分配为`/dev/sdb5`。
*   输入 `t` 修改分区类型，选择分区`5`，将其类型代码设置为`82`（Linux交换分区）。
*   输入 `p` 预览分区表，确认无误后输入 `w` 保存并退出。

![](img/cca78d39ee2819ce4e68467ab194d0dd_81.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_83.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_85.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_87.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_89.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_91.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_93.png)

**注意**：扩展分区（如`/dev/sdb4`）本身不能直接挂载使用，它只是一个指向新空间的“指针”。我们实际使用的是其内部的逻辑分区（如`/dev/sdb5`）。主分区同样可以作为交换分区，本例使用扩展分区是为了演示更多分区类型。

![](img/cca78d39ee2819ce4e68467ab194d0dd_95.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_97.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_99.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_101.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_103.png)

分区创建后，使用`ls /dev/sdb*`查看设备文件是否生成。如果未出现，可尝试运行`partprobe`命令手动同步分区表信息到内核。

![](img/cca78d39ee2819ce4e68467ab194d0dd_105.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_107.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_109.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_111.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_113.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_115.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_117.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_119.png)

交换分区有专用的格式化命令：
```bash
mkswap /dev/sdb5
```
格式化完成后，使用`swapon /dev/sdb5`命令启用该交换分区。再次运行`free -h`，可以看到交换分区总容量已增加。

![](img/cca78d39ee2819ce4e68467ab194d0dd_121.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_123.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_125.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_127.png)

但以上操作在系统重启后会失效。为了让配置永久生效，需要编辑`/etc/fstab`文件，添加如下一行：
```bash
/dev/sdb5 swap swap defaults 0 0
```
保存文件后，执行`reboot`重启系统，或执行`mount -a`重新挂载所有文件系统。重启后使用`free -h`验证交换分区是否已成功自动挂载。

![](img/cca78d39ee2819ce4e68467ab194d0dd_129.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_131.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_133.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_135.png)

关于交换分区大小的经验值，传统建议是物理内存的1.5到2倍。但对于现代大内存服务器（如128GB），需根据实际业务负载调整。如果业务内存消耗不大，设置较小的交换分区即可；如果是内存消耗大的数据库服务，则可能需要设置较大的交换分区。

![](img/cca78d39ee2819ce4e68467ab194d0dd_137.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_139.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_141.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_143.png)

## 6.7：磁盘容量配额

![](img/cca78d39ee2819ce4e68467ab194d0dd_145.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_147.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_149.png)

上一节我们成功扩展了交换分区，本节中我们来看看如何通过磁盘配额限制用户对硬盘资源的使用。

![](img/cca78d39ee2819ce4e68467ab194d0dd_151.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_153.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_155.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_157.png)

磁盘配额技术可以限制用户或用户组在特定文件系统上使用的磁盘空间大小和文件数量，防止个别用户过度占用资源导致服务器空间耗尽、服务异常。

![](img/cca78d39ee2819ce4e68467ab194d0dd_159.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_161.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_163.png)

**注意**：Linux系统的管理员（root用户）不受磁盘配额限制。

![](img/cca78d39ee2819ce4e68467ab194d0dd_165.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_167.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_169.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_171.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_173.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_175.png)

首先，我们需要在目标文件系统上启用配额功能。以`/boot`分区为例（实际生产环境请根据需求选择分区），编辑`/etc/fstab`文件，找到`/boot`分区所在行，在挂载参数（`defaults`）后添加`uquota`。对于XFS文件系统，正确的参数是`uquota`（用户配额），虽然有些编辑器可能会将`usrquota`高亮显示，但那是旧版参数。

![](img/cca78d39ee2819ce4e68467ab194d0dd_177.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_179.png)

参数生效有两种方式：
1.  使用 `mount -o remount /boot` 命令重新挂载。
2.  重启系统（更稳妥，确保完全同步）。

![](img/cca78d39ee2819ce4e68467ab194d0dd_181.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_183.png)

重启后，使用 `mount | grep boot` 命令过滤查看`/boot`分区的挂载参数，确认`uquota`已生效。

![](img/cca78d39ee2819ce4e68467ab194d0dd_185.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_187.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_189.png)

磁盘配额主要可以设置两类限制：
1.  **容量限制**：限制用户占用的磁盘空间大小（数据块数量）。
    *   `bsoft`：软限制。达到此限制会发出警告（记录到系统日志），但允许用户暂时超额。
    *   `bhard`：硬限制。达到此限制后，用户将无法创建新文件或扩大现有文件。
2.  **文件数限制**：限制用户拥有的文件（inode）数量。
    *   `isoft`：软限制（文件数）。
    *   `ihard`：硬限制（文件数）。

![](img/cca78d39ee2819ce4e68467ab194d0dd_191.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_193.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_195.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_197.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_199.png)

**注意**：硬限制值必须大于软限制值。

![](img/cca78d39ee2819ce4e68467ab194d0dd_201.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_203.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_205.png)

以下是配置磁盘配额的具体步骤：
1.  **准备实验环境**：由于要测试普通用户，需确保目标目录（如`/boot`）对普通用户有写入权限。执行 `chmod -Rf 777 /boot`。
2.  **配置用户配额**：使用`xfs_quota`命令的专家模式（`-x`）和非交互式（`-c`）进行配置。以下命令针对用户`linuxpro`在`/boot`目录上设置配额：
    ```bash
    xfs_quota -x -c 'limit bsoft=3m bhard=6m isoft=3 ihard=6 linuxpro' /boot
    ```
    该命令意为：限制用户`linuxpro`在`/boot`目录下，磁盘空间软限制3MB、硬限制6MB；文件数量软限制3个、硬限制6个。
3.  **验证配额效果**：切换到`linuxpro`用户，进入`/boot`目录。
    *   **测试文件数限制**：尝试创建第7个文件时，系统会报错“磁盘配额超出”。
    *   **测试容量限制**：使用`dd`命令创建文件。例如，创建一个4MB的文件：`dd if=/dev/zero of=haha bs=4M count=1`。再尝试创建一个总计超过6MB的文件时，会触发硬限制报错。

![](img/cca78d39ee2819ce4e68467ab194d0dd_207.png)

如果需要修改用户的配额限制（例如将容量硬限制从6MB改为60MB），需切换回root用户，使用`edquota -u linuxpro`命令编辑该用户的配额。编辑界面中，需要修改的是`blocks`和`inodes`后面的`hard`和`soft`值（代表硬限制和软限制），前面的`used`值是当前使用量，仅供参考。修改保存后立即生效。

![](img/cca78d39ee2819ce4e68467ab194d0dd_209.png)

## 6.8：VDO虚拟数据优化

![](img/cca78d39ee2819ce4e68467ab194d0dd_211.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_213.png)

上一节我们学习了如何限制磁盘使用，本节中我们来看看一种能“节省”磁盘空间的技术——VDO。

![](img/cca78d39ee2819ce4e68467ab194d0dd_215.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_217.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_219.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_221.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_223.png)

VDO（Virtual Data Optimize，虚拟数据优化）是RHEL 8中引入的新技术，它通过压缩和去重两大功能，显著提升硬盘的空间利用率。

![](img/cca78d39ee2819ce4e68467ab194d0dd_225.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_227.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_229.png)

*   **压缩**：VDO会对存储的数据进行实时压缩，不同类型的文件压缩率不同（如文本配置文件压缩率可达50%以上）。
*   **去重**：当保存多个相同或相似的文件时，VDO会极大地减少冗余数据的存储。这类似于网盘服务，当无数用户上传同一部电影时，服务端实际上只保存一份数据。

![](img/cca78d39ee2819ce4e68467ab194d0dd_231.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_233.png)

VDO卷有“物理大小”和“逻辑大小”的概念。物理大小是硬盘的实际容量，逻辑大小是呈现给用户的可使用容量。由于压缩和去重的存在，逻辑大小可以远大于物理大小，通常根据数据类型设置压缩比（如配置文件用10:1，数据库用3:1）。

以下是创建和使用VDO卷的步骤：
1.  **创建VDO卷**：使用`vdo create`命令。例如，将新硬盘`/dev/sdb`创建为名为`vdo0`的卷，物理大小20GB，逻辑大小200GB：
    ```bash
    vdo create --name=vdo0 --device=/dev/sdb --vdoLogicalSize=200G
    ```
    可以使用 `vdo status --name=vdo0` 查看卷状态。
2.  **格式化并挂载**：VDO卷的设备文件位于`/dev/mapper/`目录下。
    ```bash
    mkfs.xfs /dev/mapper/vdo0
    mkdir /vdo
    mount /dev/mapper/vdo0 /vdo
    ```
    使用 `df -h` 查看，会发现`/vdo`目录显示容量约为200GB（逻辑大小）。
3.  **永久挂载**：编辑`/etc/fstab`，添加：
    ```bash
    /dev/mapper/vdo0 /vdo xfs defaults,_netdev 0 0
    ```
    `_netdev`参数表示这是一个网络存储设备（虽然VDO不是，但某些情况下需要此参数避免启动问题）。
4.  **验证效果**：进入`/vdo`目录，复制一个大文件（如一个460MB的安装包）多次。使用 `vdo status --name=vdo0` 查看，观察“Data blocks used”（已用数据块）的增长远小于文件实际大小的倍数，而“Compression”和“Deduplication”的比率会显著提高。这证明VDO有效地节省了存储空间。

**注意**：VDO的去重不是简单的创建硬链接。即使删除了“原始文件”，通过VDO去重保存的“副本”依然可以独立访问，因为VDO保存的是经过压缩算法处理后的数据块。

## 6.9：软硬链接

在了解了VDO这种高级存储技术后，我们回过头来看一个更基础但非常重要的概念——文件链接。Linux中的链接分为软链接和硬链接。

**软链接（Symbolic Link）**：类似于Windows的“快捷方式”。它是一个特殊的文件，内容是指向目标文件路径的指针。
*   **创建命令**：`ln -s 源文件 链接文件`
*   **特点**：删除源文件，软链接将失效（“断链”）。可以跨分区和文件系统创建。链接文件本身有独立的inode。

**硬链接（Hard Link）**：直接指向目标文件的inode（索引节点）。可以将它理解为文件的另一个“别名”。
*   **创建命令**：`ln 源文件 链接文件`
*   **特点**：
    *   删除源文件，只要硬链接计数不为零，文件数据依然可通过其他硬链接访问。
    *   不能跨分区或文件系统创建。
    *   不能对目录创建硬链接（目录本身有特殊的`.`和`..`链接）。
    *   硬链接文件和源文件共享同一个inode，因此它们拥有相同的权限、所有者和内容。

**原理剖析**：每个文件由两部分组成：`inode`（存储文件元数据：权限、所有者、时间戳、数据块指针等）和`data block`（存储文件实际内容）。
*   创建**硬链接**时，系统新建一个目录项，指向**同一个inode**。该inode的链接计数（`ls -l`结果中第二列的数字）加1。只有当链接计数减为0时，inode及其指向的数据块才会被真正释放。
*   创建**软链接**时，系统会创建一个**新的inode和新的数据块**，数据块中存储的是目标文件的路径字符串。

**如何选择**：只要条件允许（不跨分区、不对目录），优先使用硬链接，因为它更高效且不会产生“悬空链接”。软链接则用于需要跨文件系统或为目录创建链接的场景。

**查看链接计数**：使用`ls -li`命令可以查看文件的inode编号和链接计数。为文件创建硬链接后，其链接计数会增加。

## 本章总结

本节课中我们一起学习了Linux系统下硬盘设备的高级管理。
*   我们掌握了为系统添加交换分区并使其永久生效的方法。
*   我们学习了如何使用磁盘配额技术，精确控制用户对存储资源的使用，避免资源滥用。
*   我们探讨了VDO虚拟数据优化技术，它通过压缩和去重，极大地提升了存储空间的利用率。
*   最后，我们深入理解了软链接和硬链接的原理与区别，这是理解Linux文件系统的基础。

![](img/cca78d39ee2819ce4e68467ab194d0dd_235.png)

![](img/cca78d39ee2819ce4e68467ab194d0dd_236.png)

这些技能将帮助您更有效、更安全地管理服务器存储资源。