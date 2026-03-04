# Linux Shell脚本编程：P27：SED与AWK文本处理工具详解 🚀

![](img/a593fe9fd79bd878b2ad162c5f530eb0_1.png)

在本节课中，我们将深入学习Shell脚本编程中两个极其强大的文本处理工具：**SED**（流编辑器）和**AWK**。它们能高效地处理、转换和分析文本数据，是自动化脚本编写的利器。我们将从基本概念入手，通过实例讲解其核心用法。

---

## SED：流编辑器 🔄

上一节我们介绍了Shell脚本中的循环与判断。本节中，我们来看看如何利用SED对文本进行非交互式的流式编辑。

SED被称为“流编辑器”，它源自古老的`ed`编辑器，但功能更强大，特别适合在脚本中自动修改文件内容。其基本工作模式是：读取输入、按指令处理、输出结果。**默认情况下，SED只将处理结果显示在屏幕，而不会修改源文件**。

![](img/a593fe9fd79bd878b2ad162c5f530eb0_3.png)

![](img/a593fe9fd79bd878b2ad162c5f530eb0_5.png)

### SED基本命令结构

SED命令通常由选项、地址定界（可选）、命令和参数组成。核心命令包括`s`（替换）、`d`（删除）、`p`（打印）。

**基本语法公式**：
```bash
sed [选项] ‘[地址]命令/[参数]’ 输入文件
```

![](img/a593fe9fd79bd878b2ad162c5f530eb0_7.png)

![](img/a593fe9fd79bd878b2ad162c5f530eb0_8.png)

![](img/a593fe9fd79bd878b2ad162c5f530eb0_10.png)

以下是SED的几个核心操作示例：

*   **替换文本**：将文件中每行**第一个**匹配的“root”替换为“shark”。
    ```bash
    sed -e 's/root/shark/' /etc/passwd
    ```
    若要替换**一行中所有**匹配项，需在命令末尾加上`g`：
    ```bash
    sed -e 's/root/shark/g' /etc/passwd
    ```

![](img/a593fe9fd79bd878b2ad162c5f530eb0_12.png)

*   **删除行**：删除包含“bash”关键词的所有行。
    ```bash
    sed -e '/bash/d' /etc/passwd
    ```

![](img/a593fe9fd79bd878b2ad162c5f530eb0_14.png)

![](img/a593fe9fd79bd878b2ad162c5f530eb0_15.png)

![](img/a593fe9fd79bd878b2ad162c5f530eb0_17.png)

*   **打印特定行**：结合`-n`选项（禁止默认输出），只打印包含“shark”单词的行。这里使用了正则表达式`\<`和`\>`来匹配单词边界。
    ```bash
    sed -n -e '/\<shark\>/p' /etc/passwd
    ```

![](img/a593fe9fd79bd878b2ad162c5f530eb0_19.png)

![](img/a593fe9fd79bd878b2ad162c5f530eb0_21.png)

![](img/a593fe9fd79bd878b2ad162c5f530eb0_22.png)

![](img/a593fe9fd79bd878b2ad162c5f530eb0_24.png)

### 指定操作范围与多命令执行

![](img/a593fe9fd79bd878b2ad162c5f530eb0_26.png)

![](img/a593fe9fd79bd878b2ad162c5f530eb0_27.png)

![](img/a593fe9fd79bd878b2ad162c5f530eb0_29.png)

![](img/a593fe9fd79bd878b2ad162c5f530eb0_30.png)

SED允许我们精确指定要操作的行，并可以组合多个命令。

以下是几种指定行范围和执行多命令的方法：

*   **按行号操作**：操作第1到第10行。
    ```bash
    sed -e ‘1,10s/old/new/‘ file.txt
    ```
*   **按步长操作**：操作第1、3、5…行（步长为2）。
    ```bash
    sed -e ‘1~2s/old/new/‘ file.txt
    ```
*   **执行多个命令**：可以使用多个`-e`选项，或在命令间用分号`；`分隔。
    ```bash
    sed -e ‘s/root/shark/; s/bin/nologin/‘ /etc/passwd
    ```
*   **使用SED脚本文件**：将复杂的SED命令写入文件（例如`script.sed`），然后通过`-f`选项调用。
    ```bash
    sed -f script.sed /etc/passwd
    ```
    脚本文件内容示例：
    ```
    s/root/shark/
    s/bin/nologin/
    ```

### SED在脚本中的实际应用

![](img/a593fe9fd79bd878b2ad162c5f530eb0_32.png)

SED的核心价值在于脚本自动化。例如，编写一个定时任务脚本，在凌晨自动清理配置文件中的注释行和空行：

![](img/a593fe9fd79bd878b2ad162c5f530eb0_34.png)

![](img/a593fe9fd79bd878b2ad162c5f530eb0_35.png)

![](img/a593fe9fd79bd878b2ad162c5f530eb0_36.png)

![](img/a593fe9fd79bd878b2ad162c5f530eb0_38.png)

![](img/a593fe9fd79bd878b2ad162c5f530eb0_40.png)

![](img/a593fe9fd79bd878b2ad162c5f530eb0_41.png)

```bash
#!/bin/bash
# 清理 squid.conf 的注释和空行
sed -e ‘/^#/d‘ -e ‘/^$/d‘ /etc/squid/squid.conf > /tmp/squid.conf.tmp
mv /tmp/squid.conf.tmp /etc/squid/squid.conf
```

![](img/a593fe9fd79bd878b2ad162c5f530eb0_43.png)

![](img/a593fe9fd79bd878b2ad162c5f530eb0_44.png)

![](img/a593fe9fd79bd878b2ad162c5f530eb0_45.png)

![](img/a593fe9fd79bd878b2ad162c5f530eb0_47.png)

![](img/a593fe9fd79bd878b2ad162c5f530eb0_48.png)

**过渡**：SED擅长基于行的流式编辑。然而，当我们需要基于列（字段）来处理结构化文本（如日志、`/etc/passwd`）时，另一个工具——AWK则更为强大。

![](img/a593fe9fd79bd878b2ad162c5f530eb0_50.png)

![](img/a593fe9fd79bd878b2ad162c5f530eb0_52.png)

---

![](img/a593fe9fd79bd878b2ad162c5f530eb0_54.png)

## AWK：模式扫描与处理语言 📊

AWK不仅具备SED的文本处理能力，更擅长处理按字段（列）组织的文本数据。它本身几乎是一门编程语言，支持变量、数组、函数以及流程控制。

### AWK基本工作原理

AWK将输入文本视为由记录（默认按行）和字段（默认按空格或制表符分隔）组成的数据表。其程序结构通常分为三部分：`BEGIN`（处理前执行）、主循环体（对每一行执行）、`END`（处理后执行）。

**基本语法公式**：
```bash
awk ‘BEGIN {预处理} 模式 {动作} END {后处理}‘ 输入文件
```

### AWK核心概念与操作

以下是AWK的几个基本但强大的用法：

*   **打印特定字段**：打印`/etc/passwd`文件的第一列（用户名）和第三列（UID）。这里用`-F`指定字段分隔符为冒号`:`。
    ```bash
    awk -F:‘{print $1, $3}‘ /etc/passwd
    ```
    *   `$1`代表第一个字段，`$2`代表第二个，依此类推。`$0`代表整行。

*   **条件过滤与统计**：统计使用`/bin/bash`作为登录shell的用户数量。
    ```bash
    awk -F:‘BEGIN {count=0} $7 == “/bin/bash“ {count++} END {print count}‘ /etc/passwd
    ```
    *   `BEGIN`块初始化计数器`count`。
    *   主循环体中，`$7 == “/bin/bash“`是一个条件，仅对第七字段符合条件的行执行`{count++}`（计数器加一）。
    *   `END`块打印最终计数。

![](img/a593fe9fd79bd878b2ad162c5f530eb0_56.png)

*   **系统命令结合示例**：检查系统服务在运行级别3下的状态，并找出`httpd`服务是否启用。
    ```bash
    chkconfig --list | awk ‘$1 == “httpd“ {print $5}‘
    ```
    这条命令会输出`httpd`服务在运行级别3下的状态（`3:on`或`3:off`中的`on/off`部分）。

### AWK脚本文件

对于复杂的AWK程序，可以将其写入脚本文件，提高可读性和复用性。

![](img/a593fe9fd79bd878b2ad162c5f530eb0_58.png)

![](img/a593fe9fd79bd878b2ad162c5f530eb0_59.png)

创建一个AWK脚本文件`count.awk`：
```awk
BEGIN {
    FS=“:“ # 设置字段分隔符
    count=0
}
$7 == “/bin/bash“ {
    count++
}
END {
    print “Number of bash users:“, count
}
```
执行该脚本：
```bash
awk -f count.awk /etc/passwd
```

![](img/a593fe9fd79bd878b2ad162c5f530eb0_61.png)

![](img/a593fe9fd79bd878b2ad162c5f530eb0_62.png)

![](img/a593fe9fd79bd878b2ad162c5f530eb0_64.png)

![](img/a593fe9fd79bd878b2ad162c5f530eb0_65.png)

### 深入学习的资源

![](img/a593fe9fd79bd878b2ad162c5f530eb0_67.png)

![](img/a593fe9fd79bd878b2ad162c5f530eb0_69.png)

SED和AWK功能极其丰富。要深入学习，最好的老师是系统自带的手册：
*   `info sed` 或 `man sed`：包含大量SED示例和高级用法。
*   `info gawk` 或 `man awk`：GNU AWK的完整手册，涵盖了所有语法、内置变量和函数。

![](img/a593fe9fd79bd878b2ad162c5f530eb0_71.png)

**过渡**：掌握SED和AWK，意味着你拥有了在Shell脚本中随心所欲处理任何文本格式数据的能力。无论是清理日志、生成报告，还是动态修改配置，都将变得高效而优雅。

![](img/a593fe9fd79bd878b2ad162c5f530eb0_72.png)

![](img/a593fe9fd79bd878b2ad162c5f530eb0_74.png)

![](img/a593fe9fd79bd878b2ad162c5f530eb0_75.png)

![](img/a593fe9fd79bd878b2ad162c5f530eb0_76.png)

---

![](img/a593fe9fd79bd878b2ad162c5f530eb0_78.png)

![](img/a593fe9fd79bd878b2ad162c5f530eb0_79.png)

![](img/a593fe9fd79bd878b2ad162c5f530eb0_81.png)

## 总结 📝

![](img/a593fe9fd79bd878b2ad162c5f530eb0_83.png)

本节课中我们一起学习了Shell脚本编程中两个核心的文本处理工具：
1.  **SED（流编辑器）**：专注于**行**级的非交互式编辑，核心命令是`s`（替换）、`d`（删除）、`p`（打印）。它通过`-e`执行命令，`-f`调用脚本，是批量修改文本文件的理想选择。
2.  **AWK**：一种功能更全面的编程语言，擅长处理按**字段**（列）结构化的文本。它通过`-F`指定分隔符，使用`$1`、`$2`等引用字段，并支持`BEGIN`、主循环体、`END`三段式编程结构，非常适合数据提取、统计和报告生成。

![](img/a593fe9fd79bd878b2ad162c5f530eb0_85.png)

![](img/a593fe9fd79bd878b2ad162c5f530eb0_86.png)

将SED、AWK与之前学习的Shell变量、条件判断、循环结合，你就能编写出强大、灵活的自动化管理脚本，真正释放Linux命令行和脚本编程的威力。