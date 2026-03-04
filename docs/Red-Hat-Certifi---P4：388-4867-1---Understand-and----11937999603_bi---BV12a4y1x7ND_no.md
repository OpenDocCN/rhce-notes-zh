# RHCE认证课程：2.1：理解和使用基本工具（第一部分）📚

在本节课中，我们将学习作为红帽认证系统管理员（RHCSA）和工程师（RHCE）所需掌握的基本工具。理解并熟练使用操作系统的基本工具是有效管理系统的基础。本节内容将回顾这些工具，并在Red Hat Enterprise Linux 8上演示其使用方法。请注意，RHCE认证要求考生首先具备RHCSA的技能。

![](img/266d687d518fa6cbe834e2f36a47cc2e_1.png)

---

## 远程登录服务器 🔐

要管理一个系统，首先需要能够登录它。对于远程服务器，最常用的登录工具是SSH。

![](img/266d687d518fa6cbe834e2f36a47cc2e_3.png)

![](img/266d687d518fa6cbe834e2f36a47cc2e_4.png)

以下是使用SSH登录远程服务器的基本命令格式：
```bash
ssh username@host_ip_address
```
例如，要登录到IP地址为`192.168.1.100`的服务器上的`cloud_user`账户，命令如下：
```bash
ssh cloud_user@192.168.1.100
```
执行命令后，系统会提示输入密码。输入正确密码后，即可登录到远程主机。更多高级选项可以通过`man ssh`命令查看手册。

![](img/266d687d518fa6cbe834e2f36a47cc2e_6.png)

![](img/266d687d518fa6cbe834e2f36a47cc2e_8.png)

现在我们已经成功登录到系统，接下来看看如何管理文件和目录。

![](img/266d687d518fa6cbe834e2f36a47cc2e_10.png)

![](img/266d687d518fa6cbe834e2f36a47cc2e_12.png)

---

![](img/266d687d518fa6cbe834e2f36a47cc2e_14.png)

![](img/266d687d518fa6cbe834e2f36a47cc2e_16.png)

![](img/266d687d518fa6cbe834e2f36a47cc2e_18.png)

## 创建文件和目录 📁

![](img/266d687d518fa6cbe834e2f36a47cc2e_20.png)

![](img/266d687d518fa6cbe834e2f36a47cc2e_22.png)

登录系统后，我们通常需要在文件系统中创建、查看和删除文件与目录。这是系统管理中最常见的任务之一。

![](img/266d687d518fa6cbe834e2f36a47cc2e_24.png)

![](img/266d687d518fa6cbe834e2f36a47cc2e_26.png)

![](img/266d687d518fa6cbe834e2f36a47cc2e_28.png)

以下是相关的核心命令：

![](img/266d687d518fa6cbe834e2f36a47cc2e_30.png)

![](img/266d687d518fa6cbe834e2f36a47cc2e_32.png)

![](img/266d687d518fa6cbe834e2f36a47cc2e_34.png)

*   **创建目录**：使用`mkdir`命令。
    ```bash
    mkdir test_directory
    ```
*   **创建文件**：有两种常用方法。
    1.  使用`touch`命令创建一个空文件。
        ```bash
        touch test_file.txt
        ```
    2.  使用文本编辑器（如`vi`）创建并编辑文件。
        ```bash
        vi new_file.txt
        ```
*   **删除文件**：使用`rm`命令。
    ```bash
    rm test_file.txt
    ```
*   **删除目录**：使用`rm -r`命令进行递归删除（用于删除非空目录）。
    ```bash
    rm -r test_directory
    ```

![](img/266d687d518fa6cbe834e2f36a47cc2e_36.png)

![](img/266d687d518fa6cbe834e2f36a47cc2e_37.png)

![](img/266d687d518fa6cbe834e2f36a47cc2e_38.png)

![](img/266d687d518fa6cbe834e2f36a47cc2e_40.png)

掌握了文件管理的基础后，我们将学习一个更强大的概念：输入输出重定向。

![](img/266d687d518fa6cbe834e2f36a47cc2e_42.png)

---

## 输入输出重定向 🔄

![](img/266d687d518fa6cbe834e2f36a47cc2e_44.png)

在Linux中，命令的输入和输出可以被重定向，这是实现自动化和脚本编写的关键。主要有三种标准数据流：
*   **标准输出**：命令的正常输出，默认显示在屏幕上。
*   **标准错误**：命令的错误信息输出，默认也显示在屏幕上。
*   **标准输入**：命令接收的输入，默认来自键盘。

![](img/266d687d518fa6cbe834e2f36a47cc2e_46.png)

以下是重定向操作的核心用法：

*   **重定向标准输出到文件**：使用 `>` 符号。
    ```bash
    ls -al /var/log > log_list.txt
    ```
*   **重定向标准错误到文件**：使用 `2>` 符号。
    ```bash
    ls /non_existent_dir 2> error.log
    ```
*   **重定向标准输入**：使用 `<` 符号，将文件内容作为命令的输入。
    ```bash
    sort < unsorted_data.txt
    ```
*   **组合使用**：可以同时进行输入和输出重定向。
    ```bash
    sort < log_list.txt > sorted_log.txt
    ```
*   **管道**：使用 `|` 符号，将一个命令的输出直接作为另一个命令的输入。
    ```bash
    cat sorted_log.txt | grep “cron”
    ```

通过重定向和管道，我们可以将简单的命令组合起来完成复杂任务。接下来，我们将学习如何查看和分析文本文件的内容。

---

## 查看和分析文本文件 📄

![](img/266d687d518fa6cbe834e2f36a47cc2e_48.png)

![](img/266d687d518fa6cbe834e2f36a47cc2e_50.png)

系统管理员经常需要查看日志、配置文件等文本文件。有多种工具可以帮助我们高效地完成这项工作。

![](img/266d687d518fa6cbe834e2f36a47cc2e_52.png)

![](img/266d687d518fa6cbe834e2f36a47cc2e_53.png)

![](img/266d687d518fa6cbe834e2f36a47cc2e_54.png)

以下是查看和分析文本的常用方法：

![](img/266d687d518fa6cbe834e2f36a47cc2e_56.png)

![](img/266d687d518fa6cbe834e2f36a47cc2e_57.png)

*   **使用文本编辑器查看**：如 `vi`、`vim` 或 `nano`。它们允许查看和编辑文件。
    ```bash
    vi /etc/hosts
    ```
    在`vi`中，可以使用 `/` 后跟字符串进行搜索。
*   **使用`less`分页查看**：适合查看大文件，允许上下翻页。
    ```bash
    less /var/log/messages
    ```
*   **使用`cat`打印全部内容**：将整个文件内容输出到屏幕。
    ```bash
    cat config_file.conf
    ```
*   **使用`grep`搜索特定内容**：打印文件中匹配特定模式的行。
    ```bash
    grep “error” /var/log/secure
    ```
    `grep`支持强大的正则表达式。例如：
    *   搜索以`samba`结尾的行：`grep ‘samba$’ logfile`
    *   搜索以`drwx`开头的行：`grep ‘^drwx’ logfile`

---

## 总结 ✨

![](img/266d687d518fa6cbe834e2f36a47cc2e_59.png)

本节课我们一起学习了RHCE认证中“理解和使用基本工具”的第一部分核心内容。我们回顾了四个关键技能：
1.  使用**SSH**远程登录服务器。
2.  使用`mkdir`、`touch`、`rm`等命令**创建和管理文件与目录**。
3.  利用 `>`、`<`、`|` 等符号进行**输入输出重定向和管道操作**，这是实现命令行高效工作的基础。
4.  使用`vi`、`cat`、`less`、`grep`等工具**查看和分析文本文件**。

![](img/266d687d518fa6cbe834e2f36a47cc2e_61.png)

![](img/266d687d518fa6cbe834e2f36a47cc2e_62.png)

![](img/266d687d518fa6cbe834e2f36a47cc2e_63.png)

这些是Linux系统管理的基石。在下一部分，我们将继续学习其他基本工具，例如管理进程和归档文件。请确保你已熟练掌握本节的命令和概念。