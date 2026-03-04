# Linux运维教程：P41：判断、比较与运算

![](img/64a054e1f5bcdfbecdc34e69427c1504_1.png)

![](img/64a054e1f5bcdfbecdc34e69427c1504_3.png)

![](img/64a054e1f5bcdfbecdc34e69427c1504_5.png)

在本节课中，我们将学习Shell脚本中的核心判断与运算操作。内容包括如何判断文件状态、进行整数与字符串的比较、执行数值运算以及使用条件判断符号。这些是编写自动化脚本的基础。

## 文件状态判断 🔍

![](img/64a054e1f5bcdfbecdc34e69427c1504_7.png)

上一节我们介绍了基础命令，本节中我们来看看如何判断文件的状态。Shell提供了一系列单字母选项，用于判断文件或目录的各种属性。其基本格式为 `[ -选项 文件路径 ]`。

![](img/64a054e1f5bcdfbecdc34e69427c1504_9.png)

![](img/64a054e1f5bcdfbecdc34e69427c1504_11.png)

![](img/64a054e1f5bcdfbecdc34e69427c1504_13.png)

![](img/64a054e1f5bcdfbecdc34e69427c1504_15.png)

以下是常用的文件状态判断选项：

*   **`-e`**：判断文件或目录是否存在。例如：`[ -e /etc/passwd ]`
*   **`-d`**：判断路径是否为目录。例如：`[ -d /etc ]`
*   **`-f`**：判断路径是否为普通文件。例如：`[ -f /etc/passwd ]`
*   **`-r`**：判断当前用户对文件是否具有读权限。
*   **`-w`**：判断当前用户对文件是否具有写权限。
*   **`-x`**：判断当前用户对文件是否具有执行权限。

这些判断命令执行后不会直接输出结果，需要通过 `$?` 来获取上一条命令的返回值。返回值为 `0` 表示判断为真（条件成立），非 `0` 表示判断为假（条件不成立）。

```bash
[ -e /etc/passwd ]
echo $? # 输出 0，表示文件存在
```

## 整数比较 🔢

![](img/64a054e1f5bcdfbecdc34e69427c1504_17.png)

![](img/64a054e1f5bcdfbecdc34e69427c1504_19.png)

![](img/64a054e1f5bcdfbecdc34e69427c1504_21.png)

了解了文件判断，我们进入数值比较领域。Shell中对整数进行比较需要使用特定的运算符，不能直接使用数学符号（如 `=`， `>`）。

以下是整数比较的运算符：

![](img/64a054e1f5bcdfbecdc34e69427c1504_23.png)

![](img/64a054e1f5bcdfbecdc34e69427c1504_25.png)

*   **`-eq`**：等于（Equal）。例如：`[ 1 -eq 1 ]`
*   **`-ne`**：不等于（Not Equal）。例如：`[ 2 -ne 1 ]`
*   **`-gt`**：大于（Greater Than）。例如：`[ 2 -gt 1 ]`
*   **`-ge`**：大于等于（Greater or Equal）。例如：`[ 1 -ge 1 ]`
*   **`-lt`**：小于（Less Than）。例如：`[ 1 -lt 2 ]`
*   **`-le`**：小于等于（Less or Equal）。例如：`[ 1 -le 1 ]`

![](img/64a054e1f5bcdfbecdc34e69427c1504_27.png)

![](img/64a054e1f5bcdfbecdc34e69427c1504_29.png)

**注意**：比较的每一部分（括号、数字、运算符）之间都必须有空格。

![](img/64a054e1f5bcdfbecdc34e69427c1504_31.png)

![](img/64a054e1f5bcdfbecdc34e69427c1504_33.png)

```bash
[ 10 -gt 5 ]
echo $? # 输出 0，表示 10 > 5 为真
```

## 字符串对比 📝

![](img/64a054e1f5bcdfbecdc34e69427c1504_35.png)

![](img/64a054e1f5bcdfbecdc34e69427c1504_37.png)

与整数比较不同，字符串对比使用不同的运算符。字符串对比可以用于比较变量值或直接比较文本。

![](img/64a054e1f5bcdfbecdc34e69427c1504_39.png)

![](img/64a054e1f5bcdfbecdc34e69427c1504_41.png)

![](img/64a054e1f5bcdfbecdc34e69427c1504_43.png)

![](img/64a054e1f5bcdfbecdc34e69427c1504_45.png)

以下是字符串对比的运算符：

*   **`=` 或 `==`**：判断两个字符串是否相等。例如：`[ “$USER” = “root” ]`
*   **`!=`**：判断两个字符串是否不相等。例如：`[ “$USER” != “root” ]`
*   **`-z`**：判断字符串长度是否为零（是否为空）。例如：`[ -z “$var” ]`
*   **`-n`**：判断字符串长度是否非零（是否非空）。例如：`[ -n “$var” ]`

![](img/64a054e1f5bcdfbecdc34e69427c1504_47.png)

![](img/64a054e1f5bcdfbecdc34e69427c1504_49.png)

![](img/64a054e1f5bcdfbecdc34e69427c1504_51.png)

![](img/64a054e1f5bcdfbecdc34e69427c1504_53.png)

```bash
name=“Alice”
[ “$name” = “Alice” ]
echo $? # 输出 0，表示字符串相等
```

## 常用数值运算方式 ➕➖✖️➗

![](img/64a054e1f5bcdfbecdc34e69427c1504_55.png)

![](img/64a054e1f5bcdfbecdc34e69427c1504_57.png)

在脚本中，我们经常需要进行数学运算。Shell提供了多种数值运算的方法。

![](img/64a054e1f5bcdfbecdc34e69427c1504_59.png)

以下是几种常用的数值运算方式：

![](img/64a054e1f5bcdfbecdc34e69427c1504_61.png)

![](img/64a054e1f5bcdfbecdc34e69427c1504_63.png)

1.  **`$[ ]` 表达式**：这是一种简洁的运算方式。
    ```bash
    echo $[ 5 + 3 ] # 输出 8
    echo $[ 10 / 3 ] # 输出 3 (整数除法)
    echo $[ 10 % 3 ] # 输出 1 (取余)
    ```

![](img/64a054e1f5bcdfbecdc34e69427c1504_65.png)

2.  **`$(( ))` 表达式**：这是C语言风格的运算方式，功能与 `$[ ]` 类似。
    ```bash
    echo $(( 5 * 2 )) # 输出 10
    ```

3.  **`expr` 命令**：一个外部命令，用于表达式求值。运算符两侧需要空格，乘号(`*`)等需要转义。
    ```bash
    expr 1 + 1 # 输出 2
    expr 5 \* 2 # 输出 10 (乘号需要转义)
    result=`expr 10 % 3` # 使用反引号获取结果，result=1
    ```

![](img/64a054e1f5bcdfbecdc34e69427c1504_67.png)

![](img/64a054e1f5bcdfbecdc34e69427c1504_69.png)

![](img/64a054e1f5bcdfbecdc34e69427c1504_71.png)

![](img/64a054e1f5bcdfbecdc34e69427c1504_73.png)

4.  **`let` 命令**：用于执行算术运算，通常将结果赋值给变量。它支持一些简写形式（如自增、自减）。
    ```bash
    let a=5+3
    echo $a # 输出 8

    let a++ # 等同于 let a=a+1
    echo $a # 输出 9

    let “a *= 2” # 等同于 let a=a*2
    echo $a # 输出 18
    ```

![](img/64a054e1f5bcdfbecdc34e69427c1504_75.png)

## 条件判断符号（命令连接符） ⛓️

最后，我们学习如何将多个命令根据逻辑关系连接起来，实现简单的条件判断。这在命令行和脚本中都非常实用。

![](img/64a054e1f5bcdfbecdc34e69427c1504_77.png)

以下是常用的命令连接符：

![](img/64a054e1f5bcdfbecdc34e69427c1504_79.png)

*   **`&&`（逻辑与）**：只有**前一个命令执行成功**（返回值为0），后一个命令才会执行。
    ```bash
    # 如果 /etc/passwd 文件存在，则查看其内容
    [ -f /etc/passwd ] && cat /etc/passwd
    # 如果软件包已安装，则启动其服务
    rpm -q vsftpd && systemctl start vsftpd
    ```

*   **`||`（逻辑或）**：只有**前一个命令执行失败**（返回值非0），后一个命令才会执行。
    ```bash
    # 如果软件包未安装，则安装它
    rpm -q vsftpd || yum install -y vsftpd
    ```

![](img/64a054e1f5bcdfbecdc34e69427c1504_81.png)

![](img/64a054e1f5bcdfbecdc34e69427c1504_83.png)

*   **`;`（分号）**：顺序执行多个命令，**无论前一个命令成功与否**，后面的命令都会执行。命令之间没有逻辑依赖关系。
    ```bash
    cd /opt; ls -l; date # 依次执行切换目录、列出文件、显示日期
    ```

![](img/64a054e1f5bcdfbecdc34e69427c1504_85.png)

**综合示例**：一个安装并启动服务的逻辑。
```bash
# 查询vsftpd是否安装，如果没安装则安装，安装成功后启动服务。
rpm -q vsftpd || yum install -y vsftpd && systemctl start vsftpd
```
**逻辑解释**：`||` 优先级高于 `&&`。所以先判断 `rpm -q` 是否失败，失败则执行 `yum install`。`yum install` 成功后，再执行 `&&` 后面的 `systemctl start`。

![](img/64a054e1f5bcdfbecdc34e69427c1504_87.png)

---

![](img/64a054e1f5bcdfbecdc34e69427c1504_89.png)

![](img/64a054e1f5bcdfbecdc34e69427c1504_91.png)

本节课中我们一起学习了Shell中判断文件状态、进行整数与字符串比较、执行数值运算以及使用条件判断符号连接命令。这些是构建复杂逻辑和自动化脚本的基石，请务必理解其用法和区别。下一节，我们将学习更强大的 `if` 条件判断语句。