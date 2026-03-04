# Linux入门：P7：用户组和权限

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_1.png)

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_2.png)

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_3.png)

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_5.png)

在本节课中，我们将学习Linux系统中用户、组以及文件权限的核心概念。理解这些是掌握Linux系统安全和管理的基础。我们将从用户和组的定义开始，逐步深入到文件权限的查看与修改。

## 用户与组的基本概念

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_7.png)

Linux是一个多用户操作系统。每个用户都有一个唯一的用户名和用户ID（UID）。系统通过UID来识别用户，而非用户名。同样，每个用户都属于至少一个组，组也有唯一的组ID（GID）。

用户信息存储在 `/etc/passwd` 文件中，其格式如下：
```
用户名:密码占位符:UID:GID:描述信息:家目录:登录Shell
```
例如，`root:x:0:0:root:/root:/bin/bash`。

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_9.png)

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_11.png)

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_13.png)

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_15.png)

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_17.png)

组信息存储在 `/etc/group` 文件中，其格式如下：
```
组名:组密码占位符:GID:组成员列表
```
例如，`root:x:0:`。

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_19.png)

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_20.png)

## 管理用户和组

上一节我们介绍了用户和组的基本概念，本节中我们来看看如何通过命令行管理它们。

### 创建用户和组

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_22.png)

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_23.png)

使用 `useradd` 命令创建用户，使用 `groupadd` 命令创建组。

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_25.png)

以下是创建用户和组的常用命令示例：
```bash
# 创建用户user1
useradd user1

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_27.png)

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_29.png)

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_31.png)

# 创建用户user2，并指定UID为2000
useradd -u 2000 user2

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_33.png)

# 创建组group1
groupadd group1

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_35.png)

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_37.png)

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_39.png)

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_41.png)

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_43.png)

# 创建组group2，并指定GID为3000
groupadd -g 3000 group2
```

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_45.png)

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_47.png)

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_49.png)

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_51.png)

### 修改用户和组属性

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_53.png)

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_55.png)

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_57.png)

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_59.png)

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_61.png)

使用 `usermod` 命令修改用户属性，使用 `groupmod` 命令修改组属性。

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_63.png)

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_65.png)

以下是修改用户和组属性的常用命令示例：
```bash
# 修改用户user1的登录Shell
usermod -s /bin/bash user1

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_67.png)

# 将用户user1添加到附加组group1中
usermod -aG group1 user1

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_69.png)

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_70.png)

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_72.png)

# 修改组group1的GID
groupmod -g 3001 group1
```

### 删除用户和组

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_74.png)

使用 `userdel` 命令删除用户，使用 `groupdel` 命令删除组。删除用户时，通常需要加上 `-r` 选项来同时删除其家目录和邮件文件。

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_76.png)

以下是删除用户和组的命令示例：
```bash
# 删除用户user1及其家目录
userdel -r user1

# 删除组group1（确保该组不是任何用户的主组）
groupdel group1
```

## 文件权限详解

理解了用户和组的管理后，我们来看看Linux如何通过权限来控制对文件和目录的访问。

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_78.png)

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_80.png)

### 查看文件权限

使用 `ls -l` 命令可以查看文件的详细信息，包括权限。输出结果类似：
```
-rwxr-xr--. 1 user1 group1 1234 Jan 1 10:00 filename
```
前10个字符表示文件类型和权限。第一个字符表示文件类型（`-` 表示普通文件，`d` 表示目录）。后9个字符每3个一组，分别代表**文件所有者（owner）**、**所属组（group）** 和**其他用户（others）** 的权限。

权限字符含义：
*   `r`：读权限
*   `w`：写权限
*   `x`：执行权限
*   `-`：无对应权限

### 权限对文件和目录的不同含义

权限对于文件和目录的意义有所不同。

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_82.png)

以下是文件和目录的权限含义对比：

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_84.png)

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_86.png)

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_88.png)

| 权限 | 对文件的意义 | 对目录的意义 |
| :--- | :--- | :--- |
| **r (读)** | 可以查看文件内容 | 可以列出目录中的文件列表（仅文件名） |
| **w (写)** | 可以修改文件内容 | 可以在目录内**创建、删除、重命名**文件或子目录 |
| **x (执行)** | 可以执行该文件（如脚本、程序） | 可以进入（`cd`）该目录，并访问其下的元数据 |

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_90.png)

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_92.png)

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_93.png)

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_95.png)

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_97.png)

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_99.png)

**核心要点**：对目录拥有 `w` 权限，意味着可以删除该目录下的任何文件（无论该文件本身的权限如何），除非有其他特殊权限限制。

### 修改文件权限和归属

我们可以使用 `chmod` 命令修改文件权限，使用 `chown` 和 `chgrp` 命令修改文件的所有者和所属组。

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_101.png)

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_103.png)

#### 使用 `chmod` 修改权限

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_105.png)

`chmod` 命令有两种模式：符号模式和数字（八进制）模式。

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_107.png)

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_109.png)

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_111.png)

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_113.png)

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_114.png)

**符号模式**：使用 `u`（所有者）、`g`（所属组）、`o`（其他用户）、`a`（所有用户）和 `+`（添加）、`-`（移除）、`=`（设置）来操作权限。

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_116.png)

以下是使用符号模式修改权限的示例：
```bash
# 给文件所有者添加执行权限
chmod u+x filename

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_118.png)

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_120.png)

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_122.png)

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_124.png)

# 移除所属组和其他用户的写权限
chmod go-w filename

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_126.png)

# 设置其他用户的权限为只读
chmod o=r filename
```

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_128.png)

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_130.png)

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_132.png)

**数字模式**：用三位八进制数字分别代表所有者、所属组和其他用户的权限。其中 `r=4`，`w=2`，`x=1`，将需要的权限值相加即可。

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_134.png)

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_136.png)

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_138.png)

以下是使用数字模式修改权限的示例：
```bash
# 设置权限为 rwxr-xr-- (所有者：4+2+1=7， 所属组：4+0+1=5， 其他用户：4+0+0=4)
chmod 754 filename

# 设置权限为 rw-r--r--
chmod 644 filename
```

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_140.png)

#### 使用 `chown` 和 `chgrp` 修改归属

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_142.png)

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_144.png)

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_146.png)

`chown` 命令用于修改文件的所有者和/或所属组。`chgrp` 命令专门用于修改文件的所属组。

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_148.png)

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_150.png)

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_152.png)

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_154.png)

以下是修改文件归属的示例：
```bash
# 将文件所有者改为 user2
chown user2 filename

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_156.png)

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_158.png)

# 将文件所属组改为 group2
chgrp group2 filename

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_160.png)

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_161.png)

# 同时将文件所有者和所属组改为 user3 和 group3
chown user3:group3 filename

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_163.png)

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_165.png)

# 使用 chown 只修改所属组（冒号前留空）
chown :group4 filename

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_167.png)

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_169.png)

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_171.png)

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_173.png)

# 递归修改目录及其下所有文件的归属
chown -R user5:group5 /path/to/directory
```

**重要规则**：
1.  只有 **root** 用户可以任意修改文件的所有者。
2.  文件的**所有者**或 **root** 用户可以修改文件的所属组，但普通用户修改所属组时，自身必须是目标组的成员。

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_175.png)

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_177.png)

## 总结

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_179.png)

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_181.png)

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_183.png)

本节课中我们一起学习了Linux用户、组和文件权限的核心知识。我们了解了用户（UID）和组（GID）的概念及其配置文件（`/etc/passwd`， `/etc/group`），掌握了使用 `useradd`， `usermod`， `userdel`， `groupadd`， `groupmod`， `groupdel` 等命令管理用户和组。

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_185.png)

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_187.png)

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_189.png)

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_191.png)

![](img/8f50785ca0c6ebe4c41c8e0137be0e40_193.png)

更重要的是，我们深入探讨了文件权限机制，学会了如何解读 `ls -l` 输出的权限信息，理解了读（r）、写（w）、执行（x）权限对文件和目录的不同意义。最后，我们掌握了使用 `chmod`（符号/数字模式）、`chown` 和 `chgrp` 命令来修改文件权限和归属，这是进行系统安全配置和日常管理的基础操作。请务必通过实践练习来巩固这些概念。