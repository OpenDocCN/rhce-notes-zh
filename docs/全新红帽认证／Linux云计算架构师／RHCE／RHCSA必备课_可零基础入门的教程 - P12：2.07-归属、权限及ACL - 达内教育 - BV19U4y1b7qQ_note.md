# Linux权限管理：2.07：归属、权限及ACL 📂

![](img/fa0053f7c624cd08fb35ef079a37d5ec_1.png)

![](img/fa0053f7c624cd08fb35ef079a37d5ec_3.png)

![](img/fa0053f7c624cd08fb35ef079a37d5ec_4.png)

![](img/fa0053f7c624cd08fb35ef079a37d5ec_6.png)

![](img/fa0053f7c624cd08fb35ef079a37d5ec_8.png)

在本节课中，我们将要学习Linux系统中关于文件和目录的归属、基本权限以及访问控制列表（ACL）的核心概念与操作方法。理解这些内容是管理多用户环境和保障系统安全的基础。

上一节我们介绍了用户账号和组账号的管理，本节中我们来看看用户创建后，如何控制他们对文件和目录的访问。

![](img/fa0053f7c624cd08fb35ef079a37d5ec_10.png)

## 文档的归属与权限

![](img/fa0053f7c624cd08fb35ef079a37d5ec_12.png)

在Linux系统中，每个文件或目录都有两个关键属性：**归属**和**权限**。归属定义了文件的所有者，权限则定义了不同用户能对文件执行哪些操作。

![](img/fa0053f7c624cd08fb35ef079a37d5ec_14.png)

### 理解归属关系

![](img/fa0053f7c624cd08fb35ef079a37d5ec_16.png)

![](img/fa0053f7c624cd08fb35ef079a37d5ec_18.png)

归属关系标记了一个文件属于哪个用户以及哪个组。这类似于公司里，一台笔记本电脑会分配给特定员工（用户）和其所在部门（组）。

![](img/fa0053f7c624cd08fb35ef079a37d5ec_20.png)

![](img/fa0053f7c624cd08fb35ef079a37d5ec_22.png)

我们可以使用 `ls -l` 命令查看文件的归属信息。命令输出中，位于权限标记后的两个名称分别代表**属主**（所有者用户）和**属组**（所有者组）。

![](img/fa0053f7c624cd08fb35ef079a37d5ec_24.png)

![](img/fa0053f7c624cd08fb35ef079a37d5ec_26.png)

![](img/fa0053f7c624cd08fb35ef079a37d5ec_28.png)

![](img/fa0053f7c624cd08fb35ef079a37d5ec_30.png)

```bash
ls -l /etc/passwd
```

![](img/fa0053f7c624cd08fb35ef079a37d5ec_32.png)

![](img/fa0053f7c624cd08fb35ef079a37d5ec_34.png)

根据用户与文件的归属关系，系统将访问文件的用户分为三类：
*   **属主**：文件的所有者用户。
*   **属组**：与文件属主同组的其他用户。
*   **其他人**：既不是属主也不在属组内的任何其他用户。

### 理解基本权限

![](img/fa0053f7c624cd08fb35ef079a37d5ec_36.png)

![](img/fa0053f7c624cd08fb35ef079a37d5ec_38.png)

![](img/fa0053f7c624cd08fb35ef079a37d5ec_39.png)

基本权限通过三个字符表示：`r`（读取）、`w`（写入）、`x`（执行）。使用 `ls -l` 命令时，第一个字符后的9个字符即为权限标记。

这9个字符分为三组，按顺序分别对应**属主**、**属组**和**其他人**的权限。每组中的三个字符按 `rwx` 顺序排列，拥有相应权限则显示对应字母，否则显示 `-`。

![](img/fa0053f7c624cd08fb35ef079a37d5ec_41.png)

![](img/fa0053f7c624cd08fb35ef079a37d5ec_42.png)

![](img/fa0053f7c624cd08fb35ef079a37d5ec_44.png)

权限对于文件和目录的含义有所不同：
*   **对文件**：
    *   `r`：可以读取文件内容（如使用 `cat`, `vim`）。
    *   `w`：可以修改文件内容。
    *   `x`：可以将文件作为程序来执行。
*   **对目录**：
    *   `r`：可以列出目录内的内容（如使用 `ls`）。
    *   `w`：可以在目录内创建、删除、重命名文件或子目录。
    *   `x`：可以进入该目录（如使用 `cd`）。**注意**：如果没有 `x` 权限，即使有 `r` 权限也无法查看目录内容。

## 修改归属与权限

了解了如何查看归属和权限后，我们来看看如何修改它们。

![](img/fa0053f7c624cd08fb35ef079a37d5ec_46.png)

### 修改归属：`chown` 命令

![](img/fa0053f7c624cd08fb35ef079a37d5ec_48.png)

`chown` 命令用于改变文件或目录的属主和属组。

![](img/fa0053f7c624cd08fb35ef079a37d5ec_50.png)

以下是 `chown` 命令的一些常见用法：
*   修改属主：`chown 用户名 文件或目录`
*   修改属组：`chown :组名 文件或目录`
*   同时修改属主和属组：`chown 用户名:组名 文件或目录`
*   递归修改目录内所有内容的归属：`chown -R 用户名:组名 目录`

### 修改权限：`chmod` 命令

`chmod` 命令用于修改文件或目录的权限标记。

修改权限时，需要指定为哪类用户（`u`-属主，`g`-属组，`o`-其他人）进行操作，以及操作方式（`+`-增加，`-`-移除，`=`-直接设置）。

以下是 `chmod` 命令的一些用法示例：
*   为属主增加执行权限：`chmod u+x 文件`
*   为属组移除写入权限：`chmod g-w 文件`
*   直接设置其他人的权限为只读：`chmod o=r 文件`
*   同时为属组和其他人增加读和执行权限：`chmod g+rx,o+rx 文件` 或 `chmod go+rx 文件`
*   递归修改目录内所有内容的权限：`chmod -R 权限 目录`

![](img/fa0053f7c624cd08fb35ef079a37d5ec_52.png)

![](img/fa0053f7c624cd08fb35ef079a37d5ec_54.png)

## 访问控制列表

基本权限模型（属主、属组、其他人）有时不够灵活。例如，一个属于 `root` 用户的文件，需要让用户 `zhangsan` 能读写，而用户 `lisi` 完全不能访问。此时，两者都属于“其他人”类别，无法区分设置权限。

这时就需要使用**访问控制列表**，它允许为特定的用户或组设置独立的权限。

### 管理ACL：`setfacl` 与 `getfacl` 命令

![](img/fa0053f7c624cd08fb35ef079a37d5ec_56.png)

![](img/fa0053f7c624cd08fb35ef079a37d5ec_58.png)

![](img/fa0053f7c624cd08fb35ef079a37d5ec_59.png)

![](img/fa0053f7c624cd08fb35ef079a37d5ec_61.png)

*   `setfacl`：设置ACL规则。
*   `getfacl`：查看ACL规则。

![](img/fa0053f7c624cd08fb35ef079a37d5ec_63.png)

以下是ACL管理的核心操作：
*   为用户设置独立权限：`setfacl -m u:用户名:权限 文件`
*   为组设置独立权限：`setfacl -m g:组名:权限 文件`
*   查看文件的ACL：`getfacl 文件`
*   清除文件的所有ACL规则：`setfacl -b 文件`

![](img/fa0053f7c624cd08fb35ef079a37d5ec_65.png)

**示例**：对文件 `important.txt` 设置ACL，让 `zhangsan` 可读写，`lisi` 无任何权限。
```bash
setfacl -m u:zhangsan:rw important.txt
setfacl -m u:lisi:--- important.txt
# 或简写为 setfacl -m u:lisi:- important.txt
getfacl important.txt # 查看结果
```

![](img/fa0053f7c624cd08fb35ef079a37d5ec_67.png)

![](img/fa0053f7c624cd08fb35ef079a37d5ec_69.png)

## 实战演练：配置文件权限

![](img/fa0053f7c624cd08fb35ef079a37d5ec_71.png)

让我们通过一个例子综合运用以上知识。任务要求如下：
1.  将 `/etc/fstab` 文件复制为 `/home/backup`。
2.  确保新文件属于 `root` 用户和 `root` 组。
3.  确保任何用户都没有执行权限。
4.  用户 `zhangsan` 对该文件拥有读写权限。
5.  用户 `lisi` 对该文件没有任何权限。
6.  其他所有用户保留读取权限。

**操作步骤**：
```bash
# 1. 复制文件
cp /etc/fstab /home/backup

![](img/fa0053f7c624cd08fb35ef079a37d5ec_73.png)

# 2. 确认归属（默认已是root:root，此步可省略）
ls -l /home/backup

# 3. 确认无执行权限（默认已无x，此步可省略）

![](img/fa0053f7c624cd08fb35ef079a37d5ec_75.png)

![](img/fa0053f7c624cd08fb35ef079a37d5ec_76.png)

# 4. 为zhangsan设置ACL，允许读写
setfacl -m u:zhangsan:rw /home/backup

![](img/fa0053f7c624cd08fb35ef079a37d5ec_78.png)

# 5. 为lisi设置ACL，禁止所有访问
setfacl -m u:lisi:- /home/backup

![](img/fa0053f7c624cd08fb35ef079a37d5ec_80.png)

![](img/fa0053f7c624cd08fb35ef079a37d5ec_82.png)

# 6. 确认其他人权限为只读（默认已是r--，此步可省略）
# 使用getfacl检查最终结果
getfacl /home/backup
```

![](img/fa0053f7c624cd08fb35ef079a37d5ec_84.png)

![](img/fa0053f7c624cd08fb35ef079a37d5ec_86.png)

![](img/fa0053f7c624cd08fb35ef079a37d5ec_88.png)

![](img/fa0053f7c624cd08fb35ef079a37d5ec_90.png)

本节课中我们一起学习了Linux系统的权限管理核心。我们首先理解了文件和目录的归属关系（属主、属组）以及基本权限模型（rwx）。然后，我们掌握了使用 `chown` 修改归属、使用 `chmod` 修改基本权限的方法。最后，为了满足更精细的权限控制需求，我们学习了访问控制列表（ACL）的概念，以及如何使用 `setfacl` 和 `getfacl` 命令为特定用户或组设置独立于基本权限模型的访问规则。这些技能是进行系统管理和安全配置的基石。