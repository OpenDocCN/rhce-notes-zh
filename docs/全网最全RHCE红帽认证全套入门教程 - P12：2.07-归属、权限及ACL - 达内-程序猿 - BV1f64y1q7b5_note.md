# RHCE红帽认证全套入门教程：P12：2.07-归属、权限及ACL 📂

![](img/9374cde690387b270fc9a4f0fb8436fb_1.png)

![](img/9374cde690387b270fc9a4f0fb8436fb_3.png)

![](img/9374cde690387b270fc9a4f0fb8436fb_4.png)

在本节课中，我们将要学习Linux系统中关于文件与目录的归属、基本权限以及访问控制列表（ACL）的核心概念与操作。理解这些内容是管理Linux系统安全与资源访问的基础。

![](img/9374cde690387b270fc9a4f0fb8436fb_6.png)

## 文档的归属与权限概述 🔍

上一节我们介绍了用户账号和组账号的管理。本节中，我们来看看用户登录系统后，如何控制其对文件和目录的访问。这主要涉及两个核心概念：**归属**和**权限**。

![](img/9374cde690387b270fc9a4f0fb8436fb_8.png)

*   **归属**：指一个文件或目录属于哪个用户和哪个组，就像物品的所有权。
*   **权限**：定义了不同类别的用户（所有者、所属组、其他人）对该文件或目录能进行哪些操作（读、写、执行）。

## 查看归属与权限 👀

![](img/9374cde690387b270fc9a4f0fb8436fb_10.png)

我们可以使用 `ls -l` 命令查看文件或目录的详细信息，其中包含了归属和权限信息。

```
ls -l /etc/passwd
```

![](img/9374cde690387b270fc9a4f0fb8436fb_12.png)

输出示例：
```
-rw-r--r--. 1 root root 2056 Jan 1 10:00 /etc/passwd
```

![](img/9374cde690387b270fc9a4f0fb8436fb_14.png)

以下是输出中各字段的含义：
*   **`-rw-r--r--`**：第一部分 `-` 表示文件类型（`-`为普通文件，`d`为目录）。随后的9个字符是权限标记。
*   **`root`**：第一个 `root` 表示文件**所有者**（属主）。
*   **`root`**：第二个 `root` 表示文件**所属组**（属组）。

![](img/9374cde690387b270fc9a4f0fb8436fb_16.png)

### 权限标记详解

![](img/9374cde690387b270fc9a4f0fb8436fb_18.png)

![](img/9374cde690387b270fc9a4f0fb8436fb_20.png)

这9个权限字符分为三组，每组三个字符，分别对应三类用户：
1.  **前三位**：文件**所有者**的权限。
2.  **中三位**：文件**所属组**成员的权限。
3.  **后三位**：**其他用户**的权限。

![](img/9374cde690387b270fc9a4f0fb8436fb_22.png)

![](img/9374cde690387b270fc9a4f0fb8436fb_24.png)

每组中的三个字符依次代表：
*   **`r` (Read)**：读取权限。
*   **`w` (Write)**：写入/修改权限。
*   **`x` (eXecute)**：执行权限。
*   **`-`**：表示没有对应权限。

![](img/9374cde690387b270fc9a4f0fb8436fb_26.png)

![](img/9374cde690387b270fc9a4f0fb8436fb_28.png)

以 `/etc/passwd` 的权限 `rw-r--r--` 为例：
*   所有者 `root`：有读(`r`)、写(`w`)权限，无执行(`-`)权限。
*   所属组 `root`：只有读(`r`)权限。
*   其他用户：只有读(`r`)权限。

![](img/9374cde690387b270fc9a4f0fb8436fb_30.png)

![](img/9374cde690387b270fc9a4f0fb8436fb_32.png)

### 权限对文件和目录的不同含义

同样的 `r`、`w`、`x` 权限，对文件和目录的作用不同：

| 权限 | 对文件的影响 | 对目录的影响 |
| :--- | :--- | :--- |
| **`r` (读)** | 可查看文件内容（如 `cat`, `less`） | 可列出目录内容（如 `ls`） |
| **`w` (写)** | 可修改文件内容（如 `vim`, `echo >>`） | 可在目录内创建、删除、重命名文件或子目录 |
| **`x` (执行)** | 可将文件作为程序或脚本运行 | 可进入该目录（如 `cd`） |

![](img/9374cde690387b270fc9a4f0fb8436fb_34.png)

![](img/9374cde690387b270fc9a4f0fb8436fb_36.png)

![](img/9374cde690387b270fc9a4f0fb8436fb_37.png)

**重要提示**：对于目录，如果用户没有 `x`（执行）权限，则无法进入该目录，即使拥有 `r`（读）权限也无法使用 `ls` 查看其内容。

## 修改归属关系 🛠️

要更改文件或目录的所有者或所属组，使用 `chown` 命令。

![](img/9374cde690387b270fc9a4f0fb8436fb_39.png)

![](img/9374cde690387b270fc9a4f0fb8436fb_40.png)

![](img/9374cde690387b270fc9a4f0fb8436fb_42.png)

**基本语法**：
```
chown [选项] 用户名[:组名] 文件或目录
```

以下是常用操作示例：

*   将 `/home/share` 目录的所有者改为用户 `zhangsan`：
    ```
    chown zhangsan /home/share
    ```

*   将 `/home/share` 目录的所属组改为组 `developers`：
    ```
    chown :developers /home/share
    ```

![](img/9374cde690387b270fc9a4f0fb8436fb_44.png)

*   同时将 `/home/share` 目录的所有者改为 `zhangsan`，所属组改为 `developers`：
    ```
    chown zhangsan:developers /home/share
    ```

*   递归修改目录及其内部所有内容的归属（使用 `-R` 选项）：
    ```
    chown -R zhangsan:developers /home/project
    ```

## 修改基本权限 🛠️

![](img/9374cde690387b270fc9a4f0fb8436fb_46.png)

![](img/9374cde690387b270fc9a4f0fb8436fb_48.png)

要直接修改文件或目录的权限标记，使用 `chmod` 命令。

**基本语法**：
```
chmod [选项] 权限模式 文件或目录
```

权限模式可以使用字母或数字表示。

### 字母表示法

使用 `u`（所有者）、`g`（所属组）、`o`（其他用户）、`a`（所有用户）来指定对象，用 `+`（添加）、`-`（移除）、`=`（精确设置）来操作权限。

*   为文件所有者添加执行权限：
    ```
    chmod u+x script.sh
    ```

*   移除所属组和其他用户的写权限：
    ```
    chmod go-w document.txt
    ```

*   精确设置其他用户的权限为只读：
    ```
    chmod o=r public_file.txt
    ```

*   同时为所属组和其他用户添加读和执行权限：
    ```
    chmod g+rx,o+rx shared_dir/
    ```

![](img/9374cde690387b270fc9a4f0fb8436fb_50.png)

![](img/9374cde690387b270fc9a4f0fb8436fb_52.png)

*   递归设置目录及其内容的权限：
    ```
    chmod -R 750 /home/secure_data/
    ```

### 数字表示法（八进制）

每个权限组可以用一个数字表示：`r=4`，`w=2`，`x=1`。将所需权限的数字相加即可。
*   权限 `rwx` = 4+2+1 = **7**
*   权限 `rw-` = 4+2+0 = **6**
*   权限 `r-x` = 4+0+1 = **5**
*   权限 `r--` = 4+0+0 = **4**

三个数字依次代表**所有者**、**所属组**、**其他用户**的权限。

*   设置权限为 `rwxr-xr--` (所有者可读/写/执行，组可读/执行，其他人只读)：
    ```
    chmod 754 file
    ```

*   设置权限为 `rw-r-----` (所有者可读/写，组可读，其他人无权限)：
    ```
    chmod 640 file
    ```

![](img/9374cde690387b270fc9a4f0fb8436fb_54.png)

![](img/9374cde690387b270fc9a4f0fb8436fb_56.png)

![](img/9374cde690387b270fc9a4f0fb8436fb_57.png)

![](img/9374cde690387b270fc9a4f0fb8436fb_59.png)

## 访问控制列表（ACL）🔐

基本权限模型（所有者/组/其他人）有时不够灵活。例如，一个属于 `root` 的文件，需要让用户 `zhangsan` 可读写，而用户 `lisi` 完全不可访问。此时，基本权限无法区分同属“其他人”的 `zhangsan` 和 `lisi`。

![](img/9374cde690387b270fc9a4f0fb8436fb_61.png)

![](img/9374cde690387b270fc9a4f0fb8436fb_63.png)

**访问控制列表（ACL）** 提供了更精细的权限控制，可以为**单个用户**或**单个组**设置独立的权限。

### 管理ACL

![](img/9374cde690387b270fc9a4f0fb8436fb_65.png)

*   **查看ACL**：使用 `getfacl` 命令。
    ```
    getfacl /path/to/file
    ```

![](img/9374cde690387b270fc9a4f0fb8436fb_67.png)

*   **设置/修改ACL**：使用 `setfacl` 命令。
    **基本语法**：
    ```
    setfacl -m u:用户名:权限 文件或目录
    setfacl -m g:组名:权限 文件或目录
    ```
    *   `-m` 选项表示修改ACL规则。
    *   权限格式与 `chmod` 字母表示法类似，如 `rwx`、`r--`、`-`（无权限）。

![](img/9374cde690387b270fc9a4f0fb8436fb_69.png)

*   **删除ACL**：
    *   删除特定规则：`setfacl -x u:用户名 文件`
    *   清除所有ACL规则：`setfacl -b 文件`

*   **递归设置ACL**：使用 `-R` 选项。
    ```
    setfacl -R -m u:zhangsan:rwX /shared/directory/
    ```
    （注意：`X` 表示仅当对象是目录或已有执行权限时，才赋予执行权限，比 `x` 更安全。）

### ACL应用示例

![](img/9374cde690387b270fc9a4f0fb8436fb_71.png)

假设文件 `/data/report.txt` 属于 `root:root`，基本权限为 `644` (`rw-r--r--`)。现在需要：
1.  用户 `zhangsan` 拥有读写(`rw`)权限。
2.  用户 `lisi` 没有任何权限。

操作如下：
```
# 1. 查看当前ACL（初始状态通常只有基本权限条目）
getfacl /data/report.txt

# 2. 为zhangsan添加rw权限
setfacl -m u:zhangsan:rw /data/report.txt

![](img/9374cde690387b270fc9a4f0fb8436fb_73.png)

![](img/9374cde690387b270fc9a4f0fb8436fb_74.png)

# 3. 为lisi设置无权限（用‘-’表示）
setfacl -m u:lisi:- /data/report.txt

# 4. 再次查看ACL，确认规则已添加
getfacl /data/report.txt
```

![](img/9374cde690387b270fc9a4f0fb8436fb_76.png)

执行 `getfacl` 后，输出中会看到新增的 `user:zhangsan:rw-` 和 `user:lisi:---` 条目。

![](img/9374cde690387b270fc9a4f0fb8436fb_78.png)

![](img/9374cde690387b270fc9a4f0fb8436fb_80.png)

## 总结 📝

![](img/9374cde690387b270fc9a4f0fb8436fb_82.png)

![](img/9374cde690387b270fc9a4f0fb8436fb_84.png)

本节课中我们一起学习了Linux系统权限管理的核心知识：
1.  **归属**：通过 `ls -l` 查看文件/目录的所有者(`chown`)和所属组(`chown`)，理解这是权限控制的基础。
2.  **基本权限**：掌握了 `r`、`w`、`x` 权限对文件和目录的不同含义，并学会使用 `chmod` 命令通过字母法或数字法来修改权限。
3.  **ACL（访问控制列表）**：当基本权限模型无法满足精细控制需求时，使用 `getfacl` 和 `setfacl` 命令为特定用户或组设置独立的权限规则。

![](img/9374cde690387b270fc9a4f0fb8436fb_86.png)

![](img/9374cde690387b270fc9a4f0fb8436fb_88.png)

熟练掌握归属、基本权限和ACL，是有效管理Linux系统多用户环境、保障数据安全的关键技能。