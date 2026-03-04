# RHCE 8.0 教程：03：存储管理 📂

![](img/748747e887c63e19b16afbbef017d2f9_1.png)

在本节课中，我们将要学习如何使用 Ansible 进行存储管理。主要内容包括：如何对硬盘进行分区、如何配置逻辑卷、如何格式化文件系统以及如何进行挂载。我们还会学习交换分区的配置方法。通过本课的学习，你将能够使用 Ansible Playbook 自动化完成这些常见的存储管理任务。

![](img/748747e887c63e19b16afbbef017d2f9_3.png)

![](img/748747e887c63e19b16afbbef017d2f9_5.png)

---

## 分区管理

上一节我们介绍了课程概述，本节中我们来看看如何使用 Ansible 的 `parted` 模块进行磁盘分区。

![](img/748747e887c63e19b16afbbef017d2f9_7.png)

`parted` 模块用于创建或删除磁盘分区。它包含几个关键参数：

*   **`device`**: 指定要操作的硬盘设备，例如 `/dev/sda`。
*   **`number`**: 指定分区的编号，例如 `1` 表示第一个分区。
*   **`part_end`**: 指定分区的结束位置，例如 `10GB` 表示创建一个大小为 10GB 的分区。
*   **`state`**: 指定分区的状态。`present` 表示创建分区，`absent` 表示删除分区。

![](img/748747e887c63e19b16afbbef017d2f9_9.png)

以下是创建一个 10GB 大小分区的示例 Playbook：

![](img/748747e887c63e19b16afbbef017d2f9_10.png)

![](img/748747e887c63e19b16afbbef017d2f9_12.png)

```yaml
---
- hosts: web02
  remote_user: root
  tasks:
    - name: Create a 10GB partition on /dev/sda
      parted:
        device: /dev/sda
        number: 1
        state: present
        part_end: 10GB
```

执行此 Playbook 后，可以使用 `cat /proc/partitions` 命令检查是否成功创建了 `/dev/sda1` 分区。

---

![](img/748747e887c63e19b16afbbef017d2f9_14.png)

![](img/748747e887c63e19b16afbbef017d2f9_16.png)

## 逻辑卷管理 (LVM)

上一节我们学习了如何创建分区，本节中我们来看看如何将这些分区组合成逻辑卷。

![](img/748747e887c63e19b16afbbef017d2f9_18.png)

逻辑卷管理分为两个主要步骤：创建物理卷组 (VG) 和创建逻辑卷 (LV)。

![](img/748747e887c63e19b16afbbef017d2f9_20.png)

### 创建卷组 (VG)

使用 `lvg` 模块创建卷组。需要指定卷组名称和要加入的物理卷（分区）。

以下是创建卷组 `vg1` 并将 `/dev/sda1` 和 `/dev/sda2` 加入其中的示例：

![](img/748747e887c63e19b16afbbef017d2f9_22.png)

```yaml
---
- hosts: web02
  remote_user: root
  tasks:
    - name: Create volume group vg1
      lvg:
        vg: vg1
        pvs: /dev/sda1,/dev/sda2
        state: present
```

![](img/748747e887c63e19b16afbbef017d2f9_24.png)

执行后，可以使用 `vgs` 命令查看创建的卷组。

### 创建逻辑卷 (LV)

使用 `lvol` 模块在卷组上创建逻辑卷。需要指定逻辑卷名称、所属卷组和大小。

![](img/748747e887c63e19b16afbbef017d2f9_26.png)

以下是创建大小为 2GB 的逻辑卷 `lv1` 的示例：

```yaml
---
- hosts: web02
  remote_user: root
  tasks:
    - name: Create a 2GB logical volume lv1
      lvol:
        vg: vg1
        lv: lv1
        size: 2g
        state: present
```

![](img/748747e887c63e19b16afbbef017d2f9_28.png)

**注意**：`size` 参数中的单位 `g` 必须为小写。

### 扩展逻辑卷

![](img/748747e887c63e19b16afbbef017d2f9_30.png)

如果逻辑卷空间不足，可以使用 `lvol` 模块的 `size` 参数配合 `resizefs` 选项进行在线扩展。

![](img/748747e887c63e19b16afbbef017d2f9_32.png)

以下是将逻辑卷 `lv1` 从 2GB 扩展到 10GB 的示例：

```yaml
---
- hosts: web02
  remote_user: root
  tasks:
    - name: Resize logical volume lv1 to 10GB
      lvol:
        vg: vg1
        lv: lv1
        size: 10g
        resizefs: yes
```

---

![](img/748747e887c63e19b16afbbef017d2f9_34.png)

## 格式化文件系统

上一节我们创建了逻辑卷，本节中我们来看看如何对其进行格式化，以便存储数据。

使用 `filesystem` 模块对分区或逻辑卷进行格式化。主要参数是设备路径和文件系统类型。

![](img/748747e887c63e19b16afbbef017d2f9_36.png)

以下是将逻辑卷 `/dev/vg1/lv1` 格式化为 `xfs` 文件系统的示例：

```yaml
---
- hosts: web02
  remote_user: root
  tasks:
    - name: Format /dev/vg1/lv1 as xfs
      filesystem:
        dev: /dev/vg1/lv1
        fstype: xfs
```

![](img/748747e887c63e19b16afbbef017d2f9_38.png)

![](img/748747e887c63e19b16afbbef017d2f9_40.png)

![](img/748747e887c63e19b16afbbef017d2f9_42.png)

执行后，可以使用 `blkid` 命令查看设备的文件系统类型。

---

## 挂载文件系统

![](img/748747e887c63e19b16afbbef017d2f9_44.png)

格式化完成后，需要将文件系统挂载到目录才能使用。本节我们学习如何使用 `mount` 模块。

`mount` 模块用于挂载或卸载文件系统。关键参数包括源设备 (`src`)、挂载点 (`path`) 和状态 (`state`)。

![](img/748747e887c63e19b16afbbef017d2f9_46.png)

*   `state: present` 仅在 `/etc/fstab` 中添加挂载条目，不立即挂载。
*   `state: mounted` 在 `/etc/fstab` 中添加条目并立即挂载。
*   `state: absent` 从 `/etc/fstab` 中移除条目并卸载。

以下是使用设备 UUID 将 `/dev/vg1/lv1` 永久挂载到 `/lv1test` 目录的示例：

![](img/748747e887c63e19b16afbbef017d2f9_48.png)

```yaml
---
- hosts: web02
  remote_user: root
  tasks:
    - name: Mount /dev/vg1/lv1 to /lv1test persistently
      mount:
        path: /lv1test
        src: UUID=你的设备UUID
        fstype: xfs
        state: mounted
```

![](img/748747e887c63e19b16afbbef017d2f9_50.png)

执行后，可以使用 `cat /etc/fstab` 和 `df -h` 命令验证挂载是否成功。

![](img/748747e887c63e19b16afbbef017d2f9_52.png)

![](img/748747e887c63e19b16afbbef017d2f9_54.png)

---

## 交换分区管理

![](img/748747e887c63e19b16afbbef017d2f9_56.png)

![](img/748747e887c63e19b16afbbef017d2f9_58.png)

最后，我们来看一下如何管理交换分区。在 RHEL 8 中，Ansible 没有专门的交换分区模块，但可以使用 `command` 模块执行命令来实现。

![](img/748747e887c63e19b16afbbef017d2f9_60.png)

交换分区的配置通常分为两步：格式化为交换分区和激活。

![](img/748747e887c63e19b16afbbef017d2f9_62.png)

![](img/748747e887c63e19b16afbbef017d2f9_64.png)

![](img/748747e887c63e19b16afbbef017d2f9_66.png)

以下是将 `/dev/sda4` 分区配置为交换分区的示例 Playbook：

![](img/748747e887c63e19b16afbbef017d2f9_68.png)

```yaml
---
- hosts: web02
  remote_user: root
  tasks:
    - name: Format /dev/sda4 as swap
      command: mkswap /dev/sda4

    - name: Activate swap partition temporarily
      command: swapon /dev/sda4

    - name: Add swap to fstab for permanent activation
      mount:
        path: swap
        src: /dev/sda4
        fstype: swap
        state: present
```

---

## 总结

本节课中我们一起学习了使用 Ansible 进行自动化存储管理。我们掌握了如何：
1.  使用 `parted` 模块对磁盘进行分区。
2.  使用 `lvg` 和 `lvol` 模块创建和管理逻辑卷。
3.  使用 `filesystem` 模块格式化分区或逻辑卷。
4.  使用 `mount` 模块挂载和卸载文件系统。
5.  使用 `command` 和 `mount` 模块配置交换分区。

![](img/748747e887c63e19b16afbbef017d2f9_70.png)

通过这些模块，你可以编写 Playbook 来批量、一致地管理多台服务器的存储配置，极大地提高了运维效率。