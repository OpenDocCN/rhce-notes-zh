# 备考红帽认证必修课：P12：2.07-归属、权限及ACL 📚

![](img/62c7cf1fc04f8d7cbf198e31f224a2bd_1.png)

![](img/62c7cf1fc04f8d7cbf198e31f224a2bd_3.png)

![](img/62c7cf1fc04f8d7cbf198e31f224a2bd_4.png)

![](img/62c7cf1fc04f8d7cbf198e31f224a2bd_6.png)

在本节课中，我们将要学习Linux系统中关于文件与目录的**归属**、**基本权限**以及**访问控制列表（ACL）**。这些是管理用户访问资源、保障系统安全的核心知识，也是RHCE/RHCSA认证考试的重点内容。

上一节我们介绍了用户账号和组账号的管理，本节中我们来看看用户登录系统后，如何控制他们对文件和目录的访问权限。

![](img/62c7cf1fc04f8d7cbf198e31f224a2bd_8.png)

## 归属关系 👤

归属关系定义了文件或目录的“所有权”。每个文件或目录都有两个归属标记：
*   **属主**：文件属于哪个用户。
*   **属组**：文件属于哪个组。

![](img/62c7cf1fc04f8d7cbf198e31f224a2bd_10.png)

这类似于公司里，一台笔记本电脑属于某个员工（属主），同时也属于该员工所在的部门（属组）。

### 如何查看归属

![](img/62c7cf1fc04f8d7cbf198e31f224a2bd_12.png)

![](img/62c7cf1fc04f8d7cbf198e31f224a2bd_14.png)

使用 `ls -l` 命令可以查看文件的详细信息，其中就包含了归属信息。

![](img/62c7cf1fc04f8d7cbf198e31f224a2bd_16.png)

![](img/62c7cf1fc04f8d7cbf198e31f224a2bd_18.png)

```bash
ls -l /etc/passwd
```

![](img/62c7cf1fc04f8d7cbf198e31f224a2bd_20.png)

![](img/62c7cf1fc04f8d7cbf198e31f224a2bd_22.png)

输出示例：
```
-rw-r--r--. 1 root root 1234 Jan 1 12:34 /etc/passwd
```
*   第三个字段 `root` 表示属主是 `root` 用户。
*   第四个字段 `root` 表示属组是 `root` 组。

![](img/62c7cf1fc04f8d7cbf198e31f224a2bd_24.png)

![](img/62c7cf1fc04f8d7cbf198e31f224a2bd_26.png)

![](img/62c7cf1fc04f8d7cbf198e31f224a2bd_28.png)

### 归属的类别

![](img/62c7cf1fc04f8d7cbf198e31f224a2bd_30.png)

![](img/62c7cf1fc04f8d7cbf198e31f224a2bd_32.png)

根据用户与文件的关系，系统将访问者分为三类：
1.  **属主**：文件的所有者。
2.  **属组**：与文件属主同组的用户。
3.  **其他人**：既不是属主，也不在属组内的任何其他用户。

这个分类是后续设置权限的基础。

![](img/62c7cf1fc04f8d7cbf198e31f224a2bd_34.png)

![](img/62c7cf1fc04f8d7cbf198e31f224a2bd_36.png)

## 基本权限 🔐

![](img/62c7cf1fc04f8d7cbf198e31f224a2bd_37.png)

权限决定了用户能对文件或目录执行何种操作。基本权限有三种：
*   **r**：读取权限。
*   **w**：写入权限。
*   **x**：执行权限。

![](img/62c7cf1fc04f8d7cbf198e31f224a2bd_39.png)

### 权限的查看与结构

![](img/62c7cf1fc04f8d7cbf198e31f224a2bd_40.png)

![](img/62c7cf1fc04f8d7cbf198e31f224a2bd_42.png)

同样使用 `ls -l` 命令，开头的10个字符中的后9位代表权限。

```
-rw-r--r--.
^ ^^^^^^^^^
|    |
|    +-- 权限位 (9个字符)
+-- 文件类型 (- 表示普通文件，d 表示目录)
```

这9个权限位分为三组，每组三个字符，分别对应三类用户：
*   **前3位**：属主的权限。
*   **中3位**：属组的权限。
*   **后3位**：其他人的权限。

![](img/62c7cf1fc04f8d7cbf198e31f224a2bd_44.png)

每组中的三个字符按顺序表示 `r`、`w`、x`。如果拥有该权限则显示相应字母，否则显示 `-`。

**示例分析** `/etc/passwd` 的权限 `rw-r--r--`：
*   属主 `root`：有 `rw-`，即可读、可写。
*   属组 `root`：有 `r--`，即可读。
*   其他人：有 `r--`，即可读。

![](img/62c7cf1fc04f8d7cbf198e31f224a2bd_46.png)

![](img/62c7cf1fc04f8d7cbf198e31f224a2bd_48.png)

### 权限对文件和目录的不同含义

权限字符相同，但对文件和目录的作用不同：

| 权限 | 对文件的意义 | 对目录的意义 |
| :--- | :--- | :--- |
| **r** | 读取文件内容 (cat, vim) | 读取目录内容列表 (ls) |
| **w** | 修改文件内容 (vim, echo) | 在目录内创建、删除、重命名文件或子目录 |
| **x** | 执行该文件 (运行程序/脚本) | 进入该目录 (cd) |

**重要提示**：对于目录，如果用户没有 `x` 权限，则无法进入，即使有 `r` 权限也无法查看其内容列表。

## 修改归属与权限 🛠️

了解了如何查看后，我们学习如何修改。

### 修改归属 (chown)

![](img/62c7cf1fc04f8d7cbf198e31f224a2bd_50.png)

![](img/62c7cf1fc04f8d7cbf198e31f224a2bd_52.png)

使用 `chown` 命令可以改变文件或目录的属主和属组。

**基本语法**：
```bash
chown [选项] 用户名[:组名] 文件或目录
```

**常用示例**：
以下是修改归属关系的几种常见操作：
*   将 `/home/share` 的属主改为 `zhangsan`：`chown zhangsan /home/share`
*   将 `/home/share` 的属组改为 `developers`：`chown :developers /home/share`
*   同时修改属主和属组：`chown zhangsan:developers /home/share`
*   递归修改目录及其内部所有内容的归属：`chown -R zhangsan:developers /home/share`

### 修改权限 (chmod)

使用 `chmod` 命令可以修改文件或目录的权限标记。

![](img/62c7cf1fc04f8d7cbf198e31f224a2bd_54.png)

![](img/62c7cf1fc04f8d7cbf198e31f224a2bd_56.png)

![](img/62c7cf1fc04f8d7cbf198e31f224a2bd_57.png)

![](img/62c7cf1fc04f8d7cbf198e31f224a2bd_59.png)

**基本语法（符号模式）**：
```bash
chmod [选项] 角色操作符权限 文件或目录
```
*   **角色**：`u` (属主), `g` (属组), `o` (其他人), `a` (所有人，可省略)。
*   **操作符**：`+` (增加), `-` (移除), `=` (直接设定)。
*   **权限**：`r`, `w`, `x` 或其组合。

![](img/62c7cf1fc04f8d7cbf198e31f224a2bd_61.png)

**常用示例**：
以下是使用符号模式修改权限的几种操作：
*   给属主增加执行权限：`chmod u+x file`
*   移除属组的写权限：`chmod g-w file`
*   设置其他人只有读权限：`chmod o=r file`
*   同时为属主和属组设置读写执行权限：`chmod ug=rwx file`
*   递归修改目录及其内部所有内容的权限：`chmod -R o+rX /home/share`

![](img/62c7cf1fc04f8d7cbf198e31f224a2bd_63.png)

## 访问控制列表 (ACL) 📋

![](img/62c7cf1fc04f8d7cbf198e31f224a2bd_65.png)

基本权限模型（属主、属组、其他人）有时不够灵活。例如，一个属于 `root` 的文件，需要让用户 `zhangsan` 可读写，而用户 `lisi` 完全不可访问。此时，`zhangsan` 和 `lisi` 都属于“其他人”，无法区分对待。

![](img/62c7cf1fc04f8d7cbf198e31f224a2bd_67.png)

![](img/62c7cf1fc04f8d7cbf198e31f224a2bd_69.png)

ACL 提供了更精细的权限控制，允许为**特定的用户**或**组**设置独立的权限。

### 管理 ACL

以下是管理ACL的核心命令：
*   **查看ACL**：`getfacl 文件或目录`
*   **设置/修改ACL**：`setfacl -m 条目 文件或目录`
*   **删除特定ACL条目**：`setfacl -x 条目 文件或目录`
*   **清空所有ACL条目**：`setfacl -b 文件或目录`

![](img/62c7cf1fc04f8d7cbf198e31f224a2bd_71.png)

**ACL条目格式**：
*   `user:用户名:权限` (如：`user:zhangsan:rw-`)
*   `group:组名:权限` (如：`group:developers:r-x`)

**示例**：解决上述问题，为 `/data/report` 文件设置ACL。
```bash
# 让 zhangsan 可读可写
setfacl -m user:zhangsan:rw- /data/report

![](img/62c7cf1fc04f8d7cbf198e31f224a2bd_73.png)

# 让 lisi 没有任何权限
setfacl -m user:lisi:--- /data/report

# 查看设置结果
getfacl /data/report
```

![](img/62c7cf1fc04f8d7cbf198e31f224a2bd_75.png)

![](img/62c7cf1fc04f8d7cbf198e31f224a2bd_77.png)

## 总结 📝

![](img/62c7cf1fc04f8d7cbf198e31f224a2bd_79.png)

![](img/62c7cf1fc04f8d7cbf198e31f224a2bd_81.png)

本节课中我们一起学习了Linux系统权限管理的核心知识：
1.  **归属关系**：通过 `ls -l` 查看，使用 `chown` 修改，定义了文件的属主和属组。
2.  **基本权限**：分为 `r`、`w`、x`，对文件和目录意义不同。通过 `ls -l` 查看，使用 `chmod` 修改，为属主、属组、其他人三类用户分配权限。
3.  **访问控制列表**：当基本权限无法满足精细控制需求时，使用 `setfacl` 和 `getfacl` 命令为特定用户或组设置独立权限。

![](img/62c7cf1fc04f8d7cbf198e31f224a2bd_83.png)

![](img/62c7cf1fc04f8d7cbf198e31f224a2bd_85.png)

![](img/62c7cf1fc04f8d7cbf198e31f224a2bd_87.png)

理解并熟练运用这些命令，是进行系统安全管理、完成相关认证考试题目的基础。请务必在实验环境中多加练习，加深理解。