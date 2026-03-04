# Linux权限管理：P61：设置SGID权限与协作目录

![](img/76ed37d59b3b7ce3b1b3abfaa8368e45_0.png)

![](img/76ed37d59b3b7ce3b1b3abfaa8368e45_2.png)

![](img/76ed37d59b3b7ce3b1b3abfaa8368e45_4.png)

在本节课中，我们将学习Linux中一个重要的权限概念——**SGID（Set Group ID）**。我们将通过模拟一个团队协作的场景，了解如何设置一个共享目录，使得团队成员在其中创建的文件自动归属于同一个组，从而实现便捷的文件共享与协作。

---

## 概述：Windows与Linux的权限对比

上一节我们介绍了Linux的基础权限模型。本节中，我们来看看如何利用SGID权限来管理团队协作目录。在开始之前，我们先简单对比一下Windows和Linux的权限管理方式。

![](img/76ed37d59b3b7ce3b1b3abfaa8368e45_6.png)

在Windows系统中，权限管理通常通过图形化界面完成，例如在文件属性中设置“安全”选项卡。其权限结构涉及用户、全局组、本地组以及复杂的“允许/拒绝”规则，对于初学者来说，细节设置可能相当复杂。

![](img/76ed37d59b3b7ce3b1b3abfaa8368e45_8.png)

相比之下，Linux传统的权限模型（用户、组、其他）看起来较为简单，但在管理团队协作时，需要一些特定的技巧来弥补其灵活性上的不足。SGID权限就是解决此类问题的关键工具之一。

![](img/76ed37d59b3b7ce3b1b3abfaa8368e45_10.png)

![](img/76ed37d59b3b7ce3b1b3abfaa8368e45_12.png)

---

## 场景模拟：创建团队与共享目录

![](img/76ed37d59b3b7ce3b1b3abfaa8368e45_14.png)

![](img/76ed37d59b3b7ce3b1b3abfaa8368e45_16.png)

以下是创建一个供团队成员（tom, marry, rose）协作的共享目录的完整步骤。

![](img/76ed37d59b3b7ce3b1b3abfaa8368e45_18.png)

![](img/76ed37d59b3b7ce3b1b3abfaa8368e45_19.png)

### 第一步：创建用户与工作组

![](img/76ed37d59b3b7ce3b1b3abfaa8368e45_21.png)

![](img/76ed37d59b3b7ce3b1b3abfaa8368e45_23.png)

![](img/76ed37d59b3b7ce3b1b3abfaa8368e45_25.png)

首先，我们需要创建用户账户，并将他们加入同一个工作组。

1.  **创建用户**：使用 `useradd` 命令创建用户 tom, marry, rose。
    ```bash
    useradd tom
    useradd marry
    useradd rose
    ```
    创建用户时，系统会默认生成一个与用户名同名的私有组。

![](img/76ed37d59b3b7ce3b1b3abfaa8368e45_27.png)

![](img/76ed37d59b3b7ce3b1b3abfaa8368e45_29.png)

![](img/76ed37d59b3b7ce3b1b3abfaa8368e45_31.png)

2.  **创建工作组**：使用 `groupadd` 命令创建一个名为 `worker` 的工作组。
    ```bash
    groupadd worker
    ```

![](img/76ed37d59b3b7ce3b1b3abfaa8368e45_33.png)

![](img/76ed37d59b3b7ce3b1b3abfaa8368e45_35.png)

3.  **将用户加入工作组**：使用 `gpasswd` 命令将用户添加到 `worker` 组中。
    ```bash
    gpasswd -M tom,marry,rose worker
    ```
    注意：多个用户名之间使用英文逗号分隔，不能有空格。

![](img/76ed37d59b3b7ce3b1b3abfaa8368e45_37.png)

![](img/76ed37d59b3b7ce3b1b3abfaa8368e45_38.png)

### 第二步：创建并配置共享目录

接下来，我们创建共享目录，并为其设置合适的权限。

1.  **创建目录**：在 `/var` 目录下创建一个名为 `work_files` 的文件夹。
    ```bash
    mkdir /var/work_files
    ```

2.  **更改目录的所属组**：使用 `chgrp` 命令将目录的所属组改为 `worker`。
    ```bash
    chgrp worker /var/work_files
    ```
    如果需要递归更改目录内所有现有文件的所属组，可以加上 `-R` 参数。

3.  **为组添加写入权限**：使用 `chmod` 命令为 `worker` 组添加对目录的写入（w）权限。
    ```bash
    chmod g+w /var/work_files
    ```

### 第三步：应用特殊权限（Sticky Bit 和 SGID）

![](img/76ed37d59b3b7ce3b1b3abfaa8368e45_40.png)

为了防止用户随意删除他人的文件，并实现文件自动继承目录的所属组，我们需要设置两个特殊权限。

![](img/76ed37d59b3b7ce3b1b3abfaa8368e45_42.png)

1.  **设置粘滞位（Sticky Bit）**：防止用户删除或重命名他人的文件。即使对目录有写权限，用户也只能删除自己创建的文件。
    ```bash
    chmod o+t /var/work_files
    ```

![](img/76ed37d59b3b7ce3b1b3abfaa8368e45_44.png)

![](img/76ed37d59b3b7ce3b1b3abfaa8368e45_46.png)

2.  **设置SGID权限**：这是核心步骤。设置后，任何用户在此目录下创建的新文件或子目录，其所属组将自动继承该目录的所属组（即 `worker` 组），而不是创建者自己的主要组。
    ```bash
    chmod g+s /var/work_files
    ```
    现在，目录的完整权限设置完成。你可以使用 `ls -ld /var/work_files` 查看，权限位中会出现 `s`（SGID）和 `t`（粘滞位）的标志。

---

![](img/76ed37d59b3b7ce3b1b3abfaa8368e45_48.png)

![](img/76ed37d59b3b7ce3b1b3abfaa8368e45_49.png)

## 效果验证与原理分析

![](img/76ed37d59b3b7ce3b1b3abfaa8368e45_51.png)

![](img/76ed37d59b3b7ce3b1b3abfaa8368e45_53.png)

让我们通过实际操作来验证SGID权限的效果。

![](img/76ed37d59b3b7ce3b1b3abfaa8368e45_55.png)

![](img/76ed37d59b3b7ce3b1b3abfaa8368e45_56.png)

1.  **root用户创建文件**：以root身份在目录中创建一个文件。
    ```bash
    touch /var/work_files/root_file
    ls -l /var/work_files/root_file
    ```
    你会发现，`root_file` 的所属组是 `worker`，而不是 `root`。

![](img/76ed37d59b3b7ce3b1b3abfaa8368e45_57.png)

![](img/76ed37d59b3b7ce3b1b3abfaa8368e45_59.png)

2.  **普通用户创建文件**：切换到 `tom` 用户，并创建一个文件。
    ```bash
    su - tom
    cd /var/work_files
    touch tom_file
    ls -l tom_file
    ```
    观察 `tom_file` 的权限，其所属组同样是 `worker`。由于 `tom` 的默认 `umask` 是 `0002`，文件权限通常是 `-rw-rw-r--`（664），这意味着 `worker` 组的其他成员（marry, rose）也拥有对该文件的读写权限。

### 关于umask的补充说明

![](img/76ed37d59b3b7ce3b1b3abfaa8368e45_61.png)

![](img/76ed37d59b3b7ce3b1b3abfaa8368e45_62.png)

`umask` 值决定了新建文件的默认权限。如果用户希望自己创建的文件默认不允许同组用户写入，可以修改其 `umask`。

-   查看当前 `umask`：
    ```bash
    umask
    ```
-   临时修改 `umask`（例如改为 `0022`）：
    ```bash
    umask 0022
    ```
    之后创建的文件，组权限将只有读（r）权限。
-   永久修改 `umask`：需要将 `umask` 设置命令写入用户家目录下的Shell配置文件中，例如 `~/.bash_profile` 或 `~/.bashrc`。这样每次登录时都会生效。

![](img/76ed37d59b3b7ce3b1b3abfaa8368e45_64.png)

![](img/76ed37d59b3b7ce3b1b3abfaa8368e45_65.png)

![](img/76ed37d59b3b7ce3b1b3abfaa8368e45_66.png)

---

![](img/76ed37d59b3b7ce3b1b3abfaa8368e45_68.png)

## 总结与展望

![](img/76ed37d59b3b7ce3b1b3abfaa8368e45_70.png)

本节课中我们一起学习了如何利用 **SGID权限** 和 **粘滞位** 来创建一个高效的团队协作目录。

![](img/76ed37d59b3b7ce3b1b3abfaa8368e45_72.png)

![](img/76ed37d59b3b7ce3b1b3abfaa8368e45_74.png)

-   **SGID权限**：通过 `chmod g+s` 设置，确保在共享目录中创建的任何新文件都自动归属于目录的所属组，简化了组内权限管理。
-   **粘滞位**：通过 `chmod o+t` 设置，保护了用户的文件不被其他组员误删，增强了目录的安全性。
-   **umask**：了解如何通过调整 `umask` 值来控制新建文件的默认组权限。

![](img/76ed37d59b3b7ce3b1b3abfaa8368e45_76.png)

![](img/76ed37d59b3b7ce3b1b3abfaa8368e45_77.png)

这种“目录SGID + 粘滞位”的组合，是Linux系统中管理公共工作区的经典方法。然而，传统的“用户-组-其他”三元权限模型在处理更复杂的权限需求时（例如，为单个用户或特定组外的用户设置特殊权限）显得力不从心。

因此，在后续的课程中，我们将介绍更强大的权限管理扩展——**ACL（访问控制列表）**。ACL允许你为任意用户或组设置精细的文件访问权限，极大地增强了Linux系统权限管理的灵活性和粒度。

![](img/76ed37d59b3b7ce3b1b3abfaa8368e45_79.png)

![](img/76ed37d59b3b7ce3b1b3abfaa8368e45_80.png)

![](img/76ed37d59b3b7ce3b1b3abfaa8368e45_81.png)

---

![](img/76ed37d59b3b7ce3b1b3abfaa8368e45_83.png)

**本节课核心命令总结：**
- 创建组：`groupadd [组名]`
- 添加用户到组：`gpasswd -M [用户列表] [组名]`
- 修改所属组：`chgrp [组名] [文件/目录]`
- 设置权限：
    - 添加组写权限：`chmod g+w [文件/目录]`
    - 设置粘滞位：`chmod o+t [目录]`
    - 设置SGID：`chmod g+s [目录]`
- 查看目录详情：`ls -ld [目录]`