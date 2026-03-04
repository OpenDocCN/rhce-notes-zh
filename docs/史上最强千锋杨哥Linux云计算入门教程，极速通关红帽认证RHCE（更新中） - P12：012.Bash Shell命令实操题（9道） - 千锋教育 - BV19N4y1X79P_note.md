Linux入门与RHCE认证：012：Bash Shell命令实操题（9道）

在本节课中，我们将通过九道实操题目来巩固Bash Shell的基础命令。这些练习涵盖了文件操作、文本处理、权限管理等核心技能，是掌握Linux日常操作和备战RHCE认证的关键步骤。

---

上一节我们学习了Shell的基本概念，本节将通过具体的题目来实践这些命令。

以下是第一道题目：创建一个目录结构。

1.  在`/home`目录下创建一个名为`test`的目录。
2.  在`test`目录下创建三个子目录：`dir1`、`dir2`、`dir3`。
3.  在`dir1`目录下创建一个名为`file1.txt`的空文件。

**参考命令：**
```bash
mkdir -p /home/test/{dir1,dir2,dir3}
touch /home/test/dir1/file1.txt
```

---

接下来，我们练习文件内容的操作。

以下是第二道题目：写入并查看文件内容。

1.  向`/home/test/dir1/file1.txt`文件中写入一行文本：“Hello, Linux World!”。
2.  查看该文件的内容以确认写入成功。

**参考命令：**
```bash
echo "Hello, Linux World!" > /home/test/dir1/file1.txt
cat /home/test/dir1/file1.txt
```

---

文件复制和移动是常见的操作。

以下是第三道题目：复制与移动文件。

1.  将`file1.txt`复制到`dir2`目录下。
2.  将`dir2`目录下的`file1.txt`移动到`dir3`目录下，并重命名为`file2.txt`。

**参考命令：**
```bash
cp /home/test/dir1/file1.txt /home/test/dir2/
mv /home/test/dir2/file1.txt /home/test/dir3/file2.txt
```

---

查找文件是系统管理中的必备技能。

以下是第四道题目：查找特定文件。

1.  在`/home/test`目录及其子目录中，查找所有以`.txt`结尾的文件。

**参考命令：**
```bash
find /home/test -name "*.txt"
```

---

![](img/e27147e968280c24a0e55e1e344f4725_1.png)

文本处理命令`grep`非常强大。

以下是第五道题目：在文件中搜索文本。

1.  在`/home/test/dir3/file2.txt`文件中，搜索包含“Linux”的行。

**参考命令：**
```bash
grep "Linux" /home/test/dir3/file2.txt
```

---

文件权限管理是Linux安全的基础。

以下是第六道题目：修改文件权限。

1.  将`/home/test/dir3/file2.txt`的文件权限设置为：所有者可读可写可执行（7），所属组可读可执行（5），其他人无权限（0）。权限数字模式公式为：**权限 = (所有者 * 8²) + (所属组 * 8¹) + (其他人 * 8⁰)**，其中读=4，写=2，执行=1。
2.  使用长格式查看文件详情以验证权限。

**参考命令：**
```bash
chmod 750 /home/test/dir3/file2.txt
ls -l /home/test/dir3/file2.txt
```

---

进程管理对于监控系统状态至关重要。

以下是第七道题目：查看与管理进程。

1.  查看当前系统中所有的进程信息。
2.  查找并仅显示包含“bash”字符串的进程行。

**参考命令：**
```bash
ps aux
ps aux | grep bash
```

![](img/e27147e968280c24a0e55e1e344f4725_3.png)

---

管道符`|`能将一个命令的输出作为另一个命令的输入。

以下是第八道题目：使用管道组合命令。

1.  统计`/home/test`目录下文件和目录的总数。

**参考命令：**
```bash
ls -l /home/test | wc -l
```
**注意：** `ls -l`输出的第一行“总计”也会被计数，实际文件数应为结果减一。

---

环境变量存储了重要的系统信息。

以下是第九道题目：显示环境变量。

1.  显示当前用户的`PATH`环境变量的值。

**参考命令：**
```bash
echo $PATH
```

---

本节课中，我们一起通过九道实操题练习了Bash Shell的核心命令，包括目录文件操作、文本处理、权限修改、进程查看以及管道和环境变量的使用。反复练习这些题目，能帮助你扎实掌握Linux命令行基础，为后续学习和RHCE认证打下坚实基础。