# Linux系统管理：P45：bash shell补充-45 📚

![](img/12452ab568bbd1e691ac6bbf63191ab2_1.png)

![](img/12452ab568bbd1e691ac6bbf63191ab2_3.png)

![](img/12452ab568bbd1e691ac6bbf63191ab2_5.png)

![](img/12452ab568bbd1e691ac6bbf63191ab2_7.png)

![](img/12452ab568bbd1e691ac6bbf63191ab2_9.png)

在本节课中，我们将回顾并深入解析上周的作业，重点讲解用户、组、目录权限管理以及文本处理命令中的关键概念和常见误区。通过本次学习，你将巩固对Linux基础命令的理解，并掌握解决实际问题的思路。

![](img/12452ab568bbd1e691ac6bbf63191ab2_11.png)

![](img/12452ab568bbd1e691ac6bbf63191ab2_13.png)

![](img/12452ab568bbd1e691ac6bbf63191ab2_15.png)

## 作业回顾与解析 🔍

上一节我们学习了用户和组管理的基础命令，本节中我们来看看作业中反映出的具体问题和解决方案。

![](img/12452ab568bbd1e691ac6bbf63191ab2_17.png)

![](img/12452ab568bbd1e691ac6bbf63191ab2_19.png)

以下是作业中部分题目的详细解析：

![](img/12452ab568bbd1e691ac6bbf63191ab2_21.png)

![](img/12452ab568bbd1e691ac6bbf63191ab2_23.png)

![](img/12452ab568bbd1e691ac6bbf63191ab2_25.png)

1.  **创建用户并指定家目录**：题目要求创建用户`user1`，其家目录位于`/data`下。常见错误是直接使用`useradd -D /data/user1`。这会导致`/data`目录（属主为`root`，权限755）成为`user1`的家目录，而`user1`对该目录没有写权限。正确做法是使用`useradd -d /data/user1 user1`，系统会自动创建`/data/user1`目录并设置正确的权限（700）和属主。

![](img/12452ab568bbd1e691ac6bbf63191ab2_27.png)

![](img/12452ab568bbd1e691ac6bbf63191ab2_29.png)

![](img/12452ab568bbd1e691ac6bbf63191ab2_31.png)

2.  **修改用户家目录并迁移文件**：将用户`user1`的家目录移动到`/data/itusers`。仅使用`usermod -d /data/itusers/user1 user1`只会修改`/etc/passwd`中的记录，但不会移动家目录下的原有文件。需要加上`-m`选项来迁移文件：`usermod -d /data/itusers/user1 -m user1`。

![](img/12452ab568bbd1e691ac6bbf63191ab2_33.png)

![](img/12452ab568bbd1e691ac6bbf63191ab2_35.png)

3.  **为组设置密码**：给`cloud`组设置临时登录口令。给**组**设置密码应使用`gpasswd`命令，例如`gpasswd cloud`。错误做法是使用`usermod -p`或`useradd -p`，这两个命令是为**用户**设置的是加密后的密码字符串，并非交互式登录密码。

![](img/12452ab568bbd1e691ac6bbf63191ab2_37.png)

![](img/12452ab568bbd1e691ac6bbf63191ab2_39.png)

![](img/12452ab568bbd1e691ac6bbf63191ab2_40.png)

![](img/12452ab568bbd1e691ac6bbf63191ab2_42.png)

![](img/12452ab568bbd1e691ac6bbf63191ab2_44.png)

4.  **递归修改目录属主和属组**：在`/tmp/storage/technology`目录创建后，递归修改其属主为`user1`，属组为`user2`。需要注意命令引用的目录层级。`chown -R user1:user2 /tmp/storage/technology`修改的是`technology`及其下所有内容的权限。而如果引用`/tmp/storage`，则`storage`目录的权限也会被修改。

5.  **复制目录权限**：在`/tmp`下创建`demo`目录，并复制`/home/user1`目录的权限给它。不应直接复制整个目录。`chmod`命令的`--reference`选项可以实现权限复制：`chmod --reference=/home/user1 /tmp/demo`。

![](img/12452ab568bbd1e691ac6bbf63191ab2_46.png)

![](img/12452ab568bbd1e691ac6bbf63191ab2_48.png)

6.  **按数值列排序**：将`/etc/passwd`按UID（第3列）降序排序，结果保存。这里涉及多个关键选项：
    *   `-t:`：指定冒号为字段分隔符。
    *   `-k3`：指定按第3列排序。
    *   `-n`：将列内容视为**数值**进行排序，避免字符串排序时“100”排在“2”前面的问题。
    *   `-r`：降序排列。
    完整命令示例：`sort -t: -k3 -n -r /etc/passwd > /root/passwd.bak`

7.  **定义带动态时间的别名**：定义别名`copy`，执行时实际运行`cp`命令并自动备份，备份文件名包含当天日期。日期应使用动态生成的格式。定义别名时，使用反引号或`$()`来执行`date`命令获取当前日期。
    代码示例：`alias copy='cp -a --backup=numbered $1 $1.$(date +%Y%m%d)'`
    **注意**：此别名定义仅为示例，实际使用可能需要根据具体需求调整`cp`参数和备份文件名格式。

![](img/12452ab568bbd1e691ac6bbf63191ab2_50.png)

![](img/12452ab568bbd1e691ac6bbf63191ab2_52.png)

![](img/12452ab568bbd1e691ac6bbf63191ab2_54.png)

![](img/12452ab568bbd1e691ac6bbf63191ab2_55.png)

8.  **在目录中递归查找字符串**：查找`/etc`下所有文件内容中包含“pass”字符串的文件，并显示所在行号。这需要使用`grep`命令的递归搜索和行号显示功能。
    命令：`grep -r -n "pass" /etc`
    *   `-r` 或 `-R`：递归搜索目录。
    *   `-n`：显示匹配行在文件中的行号。

![](img/12452ab568bbd1e691ac6bbf63191ab2_57.png)

![](img/12452ab568bbd1e691ac6bbf63191ab2_59.png)

![](img/12452ab568bbd1e691ac6bbf63191ab2_61.png)

![](img/12452ab568bbd1e691ac6bbf63191ab2_63.png)

## 核心命令与概念总结 💡

![](img/12452ab568bbd1e691ac6bbf63191ab2_65.png)

![](img/12452ab568bbd1e691ac6bbf63191ab2_67.png)

![](img/12452ab568bbd1e691ac6bbf63191ab2_68.png)

![](img/12452ab568bbd1e691ac6bbf63191ab2_70.png)

![](img/12452ab568bbd1e691ac6bbf63191ab2_71.png)

![](img/12452ab568bbd1e691ac6bbf63191ab2_73.png)

本节课中我们一起学习了多个系统管理命令的进阶用法和细节：

![](img/12452ab568bbd1e691ac6bbf63191ab2_75.png)

![](img/12452ab568bbd1e691ac6bbf63191ab2_77.png)

![](img/12452ab568bbd1e691ac6bbf63191ab2_78.png)

![](img/12452ab568bbd1e691ac6bbf63191ab2_79.png)

![](img/12452ab568bbd1e691ac6bbf63191ab2_81.png)

![](img/12452ab568bbd1e691ac6bbf63191ab2_83.png)

![](img/12452ab568bbd1e691ac6bbf63191ab2_85.png)

*   **用户管理**：`useradd -d` 指定家目录，`usermod -d -m` 修改并迁移家目录。
*   **组管理**：`gpasswd` 为组设置密码，区别于为用户设置加密密码的 `usermod -p`。
*   **权限管理**：`chown -R` 递归修改属主/组，`chmod --reference` 复制权限模式。
*   **文本处理**：`sort -t -k -n -r` 按指定分隔符、列、数值方式和顺序排序。
*   **查找与别名**：`grep -r -n` 递归查找并显示行号；使用 `alias` 结合命令替换创建动态功能的别名。

![](img/12452ab568bbd1e691ac6bbf63191ab2_87.png)

![](img/12452ab568bbd1e691ac6bbf63191ab2_89.png)

![](img/12452ab568bbd1e691ac6bbf63191ab2_91.png)

通过本次作业的纠错与解析，希望大家能更深刻地理解命令参数的含义及其应用场景，在后续的学习和实践中能更加得心应手。