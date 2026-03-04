# RHCE8.0视频教程：P48：存储管理

![](img/789c9fcc2166955d5d172990eb07972c_1.png)

在本节课中，我们将要学习如何使用Ansible进行存储管理。主要内容包括：如何对硬盘进行分区、如何配置逻辑卷、如何格式化文件系统以及如何进行挂载。我们还将了解交换分区的配置方法。通过本课的学习，你将能够使用Ansible剧本自动化完成这些常见的存储管理任务。

![](img/789c9fcc2166955d5d172990eb07972c_3.png)

![](img/789c9fcc2166955d5d172990eb07972c_5.png)

## 分区管理

上一节我们介绍了课程概述，本节中我们来看看如何使用Ansible的`parted`模块进行硬盘分区。

![](img/789c9fcc2166955d5d172990eb07972c_7.png)

`parted`模块用于管理磁盘分区，它包含多个参数来控制分区的创建与删除。

以下是`parted`模块的核心参数：

*   **`device`**: 指定要操作的硬盘设备，例如 `/dev/sda`。
*   **`number`**: 指定分区的编号，例如第一个分区为 `1`。
*   **`part_end`**: 指定分区的结束位置，例如 `10GB`。这决定了分区的大小。
*   **`state`**: 指定分区的状态。`present` 表示创建分区，`absent` 表示删除分区。

![](img/789c9fcc2166955d5d172990eb07972c_9.png)

例如，我们想要在 `/dev/sda` 上创建一个大小为10GB的第一个分区。可以编写如下剧本：

![](img/789c9fcc2166955d5d172990eb07972c_10.png)

![](img/789c9fcc2166955d5d172990eb07972c_12.png)

```yaml
---
- hosts: web202
  remote_user: root
  tasks:
    - name: Create a 10GB partition on /dev/sda
      parted:
        device: /dev/sda
        number: 1
        state: present
        part_end: 10GB
```

执行此剧本后，可以使用 `fdisk -l /dev/sda` 命令查看，确认已经创建了一个约10GB大小的 `/dev/sda1` 分区。

若要创建第二个分区，只需在剧本中增加一个类似的任务，并修改 `number` 和 `part_end` 参数即可。

![](img/789c9fcc2166955d5d172990eb07972c_14.png)

![](img/789c9fcc2166955d5d172990eb07972c_16.png)

## 逻辑卷管理 (LVM) - 创建卷组

分区创建完成后，接下来我们学习如何配置逻辑卷。首先，需要将物理分区加入卷组。

![](img/789c9fcc2166955d5d172990eb07972c_18.png)

Ansible提供了 `lvg` 模块来管理卷组。

![](img/789c9fcc2166955d5d172990eb07972c_20.png)

以下是 `lvg` 模块的核心参数：

*   **`vg`**: 卷组的名称。
*   **`pvs`**: 要加入到卷组中的物理卷设备列表。多个设备用逗号分隔。
*   **`state`**: 卷组的状态。`present` 表示创建，`absent` 表示删除。

![](img/789c9fcc2166955d5d172990eb07972c_22.png)

假设我们已经有了 `/dev/sda1` 和 `/dev/sda2` 两个分区，现在要将它们加入一个名为 `vg1` 的卷组。

![](img/789c9fcc2166955d5d172990eb07972c_24.png)

```yaml
---
- hosts: web202
  remote_user: root
  tasks:
    - name: Create volume group vg1
      lvg:
        vg: vg1
        pvs: /dev/sda1,/dev/sda2
        state: present
```

执行后，可以使用 `vgs` 命令查看，确认卷组 `vg1` 已创建，其大小约为两个分区之和。

如果后续需要向卷组中添加新的物理卷（例如 `/dev/sda3`），只需在 `pvs` 参数列表中追加即可。

## 逻辑卷管理 (LVM) - 创建与调整逻辑卷

![](img/789c9fcc2166955d5d172990eb07972c_26.png)

卷组创建好后，我们就可以在其中创建逻辑卷了。这需要使用 `lvol` 模块。

以下是 `lvol` 模块的核心参数：

![](img/789c9fcc2166955d5d172990eb07972c_28.png)

*   **`lv`**: 要创建的逻辑卷名称。
*   **`vg`**: 逻辑卷所属的卷组名称。
*   **`size`**: 逻辑卷的大小。**注意**：单位 `g` 需要小写（例如 `2g`）。
*   **`state`**: 逻辑卷的状态。`present` 表示创建，`absent` 表示删除。

例如，在 `vg1` 卷组中创建一个名为 `lv1`、大小为2GB的逻辑卷。

![](img/789c9fcc2166955d5d172990eb07972c_30.png)

![](img/789c9fcc2166955d5d172990eb07972c_32.png)

```yaml
---
- hosts: web202
  remote_user: root
  tasks:
    - name: Create logical volume lv1
      lvol:
        lv: lv1
        vg: vg1
        size: 2g
        state: present
```

创建后，可以使用 `lvs` 命令查看逻辑卷 `lv1`。

如果之后觉得2GB空间不足，需要扩展到5GB，可以使用 `lvol` 模块的 `size` 参数直接指定新的大小。Ansible会自动调整。

```yaml
    - name: Resize logical volume lv1 to 5GB
      lvol:
        lv: lv1
        vg: vg1
        size: 5g
```

![](img/789c9fcc2166955d5d172990eb07972c_34.png)

## 格式化文件系统

逻辑卷或普通分区创建后，需要格式化才能存储数据。这里使用 `filesystem` 模块。

![](img/789c9fcc2166955d5d172990eb07972c_36.png)

以下是 `filesystem` 模块的核心参数：

*   **`dev`**: 指定要格式化的块设备，例如 `/dev/vg1/lv1`。
*   **`fstype`**: 指定文件系统类型，例如 `xfs`、`ext4`。
*   **`resizefs`**: (可选) 如果设备大小已扩展，此参数设为 `yes` 可以调整文件系统大小以填充空间。

![](img/789c9fcc2166955d5d172990eb07972c_38.png)

现在，我们将逻辑卷 `/dev/vg1/lv1` 格式化为 `xfs` 文件系统。

![](img/789c9fcc2166955d5d172990eb07972c_40.png)

![](img/789c9fcc2166955d5d172990eb07972c_42.png)

```yaml
---
- hosts: web202
  remote_user: root
  tasks:
    - name: Format logical volume lv1 to xfs
      filesystem:
        dev: /dev/vg1/lv1
        fstype: xfs
```

格式化完成后，可以使用 `blkid /dev/vg1/lv1` 命令查看其文件系统类型和UUID。

![](img/789c9fcc2166955d5d172990eb07972c_44.png)

## 挂载文件系统

文件系统格式化后，需要挂载到目录才能使用。挂载使用 `mount` 模块。

![](img/789c9fcc2166955d5d172990eb07972c_46.png)

以下是 `mount` 模块的核心参数：

*   **`path`**: 挂载点目录的路径。
*   **`src`**: 要挂载的设备，可以是设备路径（如 `/dev/vg1/lv1`）或更稳定的UUID。
*   **`fstype`**: 文件系统类型。
*   **`state`**: 挂载状态。
    *   `present`：仅在 `/etc/fstab` 中添加挂载条目，但**不立即挂载**。
    *   `mounted`：在 `/etc/fstab` 中添加条目，并**立即执行挂载**。
    *   `absent`：从 `/etc/fstab` 中移除条目，并卸载。

![](img/789c9fcc2166955d5d172990eb07972c_48.png)

例如，我们将 `/dev/vg1/lv1` 挂载到 `/mnt/lv1test` 目录，并希望立即生效。

![](img/789c9fcc2166955d5d172990eb07972c_50.png)

```yaml
---
- hosts: web202
  remote_user: root
  tasks:
    - name: Mount lv1 to /mnt/lv1test and update fstab
      mount:
        path: /mnt/lv1test
        src: /dev/vg1/lv1
        fstype: xfs
        state: mounted
```

![](img/789c9fcc2166955d5d172990eb07972c_52.png)

![](img/789c9fcc2166955d5d172990eb07972c_54.png)

执行后，使用 `df -h` 命令可以看到 `/dev/mapper/vg1-lv1` 已挂载到 `/mnt/lv1test`，同时 `/etc/fstab` 文件中也写入了相应的配置，保证开机自动挂载。

如果将 `state` 改为 `absent` 并执行，则会卸载文件系统并从 `/etc/fstab` 中删除对应条目。

![](img/789c9fcc2166955d5d172990eb07972c_56.png)

![](img/789c9fcc2166955d5d172990eb07972c_58.png)

## 交换分区管理

![](img/789c9fcc2166955d5d172990eb07972c_60.png)

最后，我们简要了解交换分区的配置。在RHEL 8中，Ansible没有专门的模块管理交换分区，但可以通过 `command` 模块调用系统命令来实现。

![](img/789c9fcc2166955d5d172990eb07972c_62.png)

![](img/789c9fcc2166955d5d172990eb07972c_64.png)

假设已有一个分区 `/dev/sda4`，我们希望将其设置为交换分区。

![](img/789c9fcc2166955d5d172990eb07972c_66.png)

![](img/789c9fcc2166955d5d172990eb07972c_68.png)

步骤如下：
1.  **格式化分区为交换空间**：使用 `mkswap` 命令。
2.  **激活交换分区**：使用 `swapon` 命令进行临时激活。
3.  **永久配置**：通过 `mount` 模块在 `/etc/fstab` 中添加条目，但 `state` 设为 `present`（因为 `mounted` 状态不适用于 `swap`）。

```yaml
---
- hosts: web202
  remote_user: root
  tasks:
    - name: Format /dev/sda4 as swap
      command: mkswap /dev/sda4

    - name: Activate swap partition temporarily
      command: swapon /dev/sda4

    - name: Add swap entry to fstab for permanent mount
      mount:
        path: swap
        src: /dev/sda4
        fstype: swap
        state: present
```

**注意**：`mount` 模块的 `mounted` 状态无法直接激活交换分区，永久生效需要依赖系统启动时读取 `/etc/fstab`。

## 总结

![](img/789c9fcc2166955d5d172990eb07972c_70.png)

本节课中我们一起学习了使用Ansible进行自动化存储管理。我们从硬盘分区开始，逐步学习了创建卷组和逻辑卷、格式化文件系统以及挂载文件系统的完整流程。每个步骤都对应着Ansible的一个特定模块（`parted`, `lvg`, `lvol`, `filesystem`, `mount`），我们也了解了如何通过 `command` 模块处理像交换分区这样没有专用模块的任务。掌握这些内容，你将能够编写高效的Ansible剧本来统一管理多台服务器的存储配置。