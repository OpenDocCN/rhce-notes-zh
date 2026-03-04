# Linux基础教程：P39：RHCE第二部分内容作业讲解 📝

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_1.png)

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_3.png)

在本节课中，我们将详细讲解RHCE课程第二部分的作业。我们将逐一分析题目要求、常见错误和正确的实现方法，帮助你巩固用户管理、文件权限、文本处理等核心知识。

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_5.png)

---

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_7.png)

## 作业题目回顾与总体说明

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_9.png)

我们首先回顾上周的作业题目。本次作业涵盖了目录创建、用户与组管理、文件权限设置、文本处理及正则表达式等多个方面。

以下是作业中需要注意的普遍问题：
*   仔细审题，理解题目意图。
*   命令参数的使用要准确，避免混淆。
*   对于递归操作，要明确操作的目标层级。

---

## 第一题：创建目录 `/data`

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_11.png)

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_13.png)

题目要求创建一个名为 `/data` 的目录。这道题本身没有难点，使用 `mkdir` 命令即可。

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_15.png)

**正确命令示例：**
```bash
mkdir /data
```

**注意：** 创建后目录的默认权限为755，所有者为root。

---

## 第二题：创建三个用户

上一节我们创建了目录，本节中我们来看看用户创建。题目要求创建三个用户：`user1`、`user2`、`user3`，并各有特定要求。

以下是具体要求及实现方法：
*   **user1**：家目录位于 `/data` 下，描述信息为 “test user”。
    *   **关键点**：使用 `-d` 参数指定家目录时，目录路径应为 `/data/user1`，而不是 `/data`。如果指定为 `/data`，则 `/data` 目录会成为 `user1` 的家目录，由于其权限为755（所有者root），`user1` 将无法写入。
    *   **正确命令**：`useradd -d /data/user1 -c "test user" user1`
*   **user2**：指定UID为2000。
    *   **正确命令**：`useradd -u 2000 user2`
*   **user3**：Shell设置为 `/sbin/nologin`，即不可登录系统。
    *   **正确命令**：`useradd -s /sbin/nologin user3`

**常见错误**：为 `user1` 指定家目录时，错误地使用了 `-d /data`。

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_17.png)

---

## 第三题：创建组并设置目录权限

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_19.png)

题目要求创建一个GID为3000的组 `it`，并将 `user1`、`user2`、`user3` 加入该组。然后，要求 `it` 组内的所有成员都可以在 `/data/it` 目录下创建和删除文件。

要实现组内成员共享目录的写权限，需要以下步骤：
1.  创建组：`groupadd -g 3000 it`
2.  将用户加入组：`usermod -aG it user1 user2 user3`
3.  创建目录：`mkdir /data/it`
4.  修改目录所属组：`chgrp it /data/it`
5.  为组添加写权限：`chmod g+w /data/it`

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_21.png)

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_23.png)

**核心概念**：Linux中，要对目录有写权限（创建/删除文件），需要该目录的**所属组**对你所在的组授予 `w` 权限。

---

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_25.png)

## 第四题：测试用户登录

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_27.png)

题目要求测试 `user3` 无法登录。由于 `user3` 的Shell是 `/sbin/nologin`，尝试使用 `su - user3` 或SSH登录时会失败，这是预期结果。

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_29.png)

---

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_31.png)

## 第五题：将 `it` 组更名为 `cloud` 组

使用 `groupmod` 命令可以安全地修改组名，系统会自动更新所有相关配置。

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_33.png)

**正确命令：**
```bash
groupmod -n cloud it
```

**注意：** 不建议直接手动编辑 `/etc/group` 文件，可能造成不一致。

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_35.png)

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_37.png)

---

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_39.png)

## 第六题：修改用户家目录

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_41.png)

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_42.png)

题目要求新建组 `itusers`，并将 `user1` 的家目录移动到 `/data/itusers` 下。

以下是操作步骤：
1.  创建组：`groupadd itusers`
2.  修改用户家目录并迁移文件：这里不能只用 `usermod -d /data/itusers/user1 user1`，这只会修改配置文件中的路径，不会移动现有家目录中的文件。需要使用 `-m` 选项。
    *   **正确命令**：`usermod -m -d /data/itusers/user1 user1`
    *   `-m` 选项会将旧家目录的内容移动到新路径。

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_44.png)

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_46.png)

---

## 第七题：更改文件所有者与组

题目要求将 `/data/it` 目录的所有者改为 `user1`，所属组改为 `user2`。这需要使用 `chown` 命令。

**正确命令：**
```bash
chown user1:user2 /data/it
```

---

## 第八题：为 `cloud` 组设置密码

题目要求为 `cloud` 组设置一个“临时登录口令”。这里需要注意，**组密码**和**用户密码**的设置命令不同。

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_48.png)

*   设置**用户**密码：`passwd [用户名]`
*   设置**组**密码：`gpasswd [组名]`

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_50.png)

**正确命令：**
```bash
gpasswd cloud
```
执行后会提示输入组密码。

**常见错误**：使用 `usermod -p` 或 `useradd -p` 来设置，这个 `-p` 参数后跟的是加密后的密码字符串，并非交互式设置登录密码。

---

## 第九题：创建目录并指定权限

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_52.png)

题目要求在 `/tmp` 下创建 `test1/test2/test3` 目录，并在创建时直接指定 `test3` 的权限为764。

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_54.png)

使用 `mkdir` 的 `-p` 参数创建多级目录，使用 `-m` 参数指定最终目录的权限。

**正确命令：**
```bash
mkdir -p -m 764 /tmp/test1/test2/test3
```

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_56.png)

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_57.png)

---

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_59.png)

## 第十题：递归修改目录所有者

题目要求创建目录 `/storage/technology`，并递归地将其所有者改为 `user1`，所属组改为 `user2`。

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_61.png)

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_63.png)

这里有一个关键点：`chown` 命令的目标路径。
*   `chown -R user1:user2 /storage/technology`：修改的是 `technology` 目录及其**内部**所有内容的权限。
*   `chown -R user1:user2 /storage`：修改的是 `storage` 目录及其**内部**（包括 `technology`）所有内容的权限。

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_65.png)

根据题意，应使用第一种方式。即使 `technology` 目录下为空，递归操作也是有效的。

**正确命令：**
```bash
mkdir -p /storage/technology
chown -R user1:user2 /storage/technology
```

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_67.png)

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_69.png)

---

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_70.png)

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_72.png)

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_74.png)

## 第十一题：设置目录的SGID权限

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_76.png)

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_77.png)

题目要求创建组 `group1`（GID 2100），创建目录 `/tmp/share`，并为其设置SGID权限，所属组为 `group1`。

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_78.png)

SGID权限对目录的作用是：在该目录下创建的新文件或子目录，其所属组会自动继承该目录的所属组（`group1`）。

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_80.png)

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_81.png)

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_83.png)

**操作步骤：**
1.  `groupadd -g 2100 group1`
2.  `mkdir /tmp/share`
3.  `chgrp group1 /tmp/share`
4.  `chmod g+s /tmp/share` （设置SGID位）

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_85.png)

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_87.png)

---

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_89.png)

## 第十二题：递归设置家目录权限

题目要求将 `user1` 的家目录（假设为 `/home/user1`）及其内部所有文件的权限递归地设置为600（即仅所有者可读可写）。

**正确命令：**
```bash
chmod -R 600 /home/user1
```
**注意**：此操作需谨慎，因为可能会使目录本身失去执行(`x`)权限，导致无法进入。对于目录，通常需要 `rwx` 权限中的 `x`。更安全的做法是针对文件和目录分别设置，但根据题目要求，此命令可满足。

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_91.png)

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_93.png)

---

## 第十三题：复制目录权限

题目要求在 `/tmp` 下创建 `cap_demo` 目录，并使其权限与 `/home/user1` 目录权限相同。

`chmod` 命令的 `--reference` 参数可以参照某个文件的权限来设置目标文件。

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_95.png)

**正确命令：**
```bash
mkdir /tmp/cap_demo
chmod --reference=/home/user1 /tmp/cap_demo
```

**常见错误**：使用 `cp -r` 复制了整个目录，这不符合“仅复制权限”的要求。

---

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_97.png)

## 第十四题：文本排序与重定向

题目要求将 `/etc/passwd` 文件按UID（第3列）进行**降序**排序，并将结果保存到 `/root/passwd.bak`。

这需要用到 `sort` 命令：
*   `-t:`：指定冒号 `:` 为字段分隔符。
*   `-k3`：指定按第3个字段排序。
*   `-n`：按数值大小进行排序（而非字符串顺序，避免出现 `2` 排在 `100` 后面的情况）。
*   `-r`：降序排列。
*   `>`：将结果重定向到文件。

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_99.png)

**正确命令：**
```bash
sort -t: -k3 -nr /etc/passwd > /root/passwd.bak
```

---

## 第十五题：命令别名

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_101.png)

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_103.png)

题目要求定义一个名为 `copy` 的别名，当任何用户执行 `copy` 时，实际执行的是 `cp -i`，并且如果目标文件存在，则备份为“原文件名.日期.bak”的格式。

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_105.png)

这里的关键是生成动态日期。可以使用 `date +%Y%m%d` 命令。

**正确命令（在 `~/.bashrc` 中定义，然后 `source ~/.bashrc`）：**
```bash
alias copy='cp -i --backup=numbered'
```
或者，实现题目要求的特定格式（注意使用反引号或 `$()` 执行命令）：
```bash
alias copy='cp -i --backup --suffix=._$(date +%Y%m%d).bak'
```
**注意**：`--backup` 行为可能因系统而异，上述第二种方式更贴近题意描述的逻辑。

---

## 第十六题：查找文件内容

题目要求查找 `/etc` 目录下所有文件内容中包含字符串 `pass` 的文件，并显示该字符串所在的行号。

`grep` 命令的 `-r` 选项可以递归搜索目录，`-n` 选项显示行号。

**正确命令：**
```bash
grep -rn "pass" /etc
```

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_107.png)

---

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_109.png)

## 第十七题：正则表达式练习（部分讲解）

这部分题目旨在练习 `grep` 和 `sed` 中正则表达式的使用。我们挑选几道典型题目讲解。

**题目1：显示 `/etc/passwd` 中以 `bash` 结尾的行。**
*   `bash$` 表示以 `bash` 结尾。
*   **命令**：`grep "bash$" /etc/passwd`

**题目2：找出 `/etc/passwd` 中UID为3位或4位的行。**
*   `[0-9]` 匹配一个数字。`{3,4}` 表示前一个字符出现3到4次。需要使用扩展正则（`-E` 或 `egrep`）。
*   **命令**：`grep -E "[0-9]{3,4}" /etc/passwd` (注意，这会匹配所有3-4位数字序列，UID通常在特定列，更精确的写法是 `grep -E "^[^:]*:[^:]*:[0-9]{3,4}:"`)

**题目3：在 `sed` 中，将 `test.txt` 文件第5到10行中的所有数字删除。**
*   `[0-9]` 匹配数字，`+` 表示一个或多个（需扩展正则 `-r`）。`s/模式/替换内容/g` 进行全局替换。
*   **命令**：`sed -r '5,10 s/[0-9]+//g' test.txt`

**题目4：统计 `/etc/passwd` 中每个单词的出现频率。**
这是一个综合题目，思路如下：
1.  将文件中所有非字母字符（如 `:`、`/`）替换为换行符，使每个单词独占一行。
2.  删除空行和纯数字的行。
3.  使用 `sort` 排序，使相同单词相邻。
4.  使用 `uniq -c` 统计相邻重复行的次数。
5.  使用 `awk` 调整输出格式为“单词 次数”。

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_111.png)

**简化示例命令：**
```bash
tr -cs '[:alpha:]' '\n' < /etc/passwd | grep -v '^$' | sort | uniq -c | sort -nr
```
这条命令可以列出 `/etc/passwd` 中所有单词（由字母组成）的频率，并按频率降序排列。`tr -cs '[:alpha:]' '\n'` 表示将所有非字母字符压缩并替换为换行符。

---

## 本节课总结

本节课我们一起详细讲解了RHCE第二部分作业。我们重点回顾了：
1.  **用户与组管理**：`useradd`、`usermod`、`groupadd`、`groupmod`、`gpasswd` 命令的精确使用，特别是家目录指定、用户迁移、组密码设置等易错点。
2.  **文件权限管理**：`chown`、`chgrp`、`chmod` 命令，以及SGID特殊权限、递归操作 (`-R`) 的目标路径理解。
3.  **文本处理**：`sort` 排序（特别是 `-n`、`-r`、`-t`、`-k` 参数），`grep` 递归搜索 (`-r`)，命令别名 (`alias`) 的定义。
4.  **正则表达式应用**：在 `grep` 和 `sed` 中使用基础正则和扩展正则进行文本匹配、替换和提取，这是Linux文本处理的强大工具。

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_113.png)

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_114.png)

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_115.png)

通过本次作业的纠错与讲解，希望你能更扎实地掌握这些核心技能，为后续学习打下坚实基础。务必动手实践，理解每个参数的含义。