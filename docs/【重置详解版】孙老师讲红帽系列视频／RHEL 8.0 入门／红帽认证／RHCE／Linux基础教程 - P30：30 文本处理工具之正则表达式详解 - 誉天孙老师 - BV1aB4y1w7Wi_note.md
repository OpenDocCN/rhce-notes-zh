# Linux基础教程：P30：文本处理工具之正则表达式详解 🔍

在本节课中，我们将要学习正则表达式。正则表达式是一种强大的文本匹配工具，主要用于在文本内容中查找、匹配特定的字符串模式。掌握正则表达式对于高效处理文本数据至关重要。

上一节我们介绍了文本处理的基本概念，本节中我们来看看如何使用正则表达式进行精确的字符串匹配。

## 正则表达式概述

正则表达式主要用来匹配字符串。基本正则表达式称为标准正则表达式，通常用 `grep` 命令配合使用。此外，还有扩展正则表达式，它提供了更多元字符和功能。

`grep` 命令本身支持基本正则表达式。如果想使用扩展正则表达式的功能，可以使用 `grep -E` 或 `egrep` 命令。`egrep` 命令与 `grep -E` 功能相同。

![](img/b5dafa140dffcad930901ec491876ff0_1.png)

以下是正则表达式中常用的元字符及其含义：

*   `^`：匹配行的开头。
*   `$`：匹配行的结尾。
*   `\<`：匹配单词的开头。
*   `\>`：匹配单词的结尾。
*   `.`：匹配任意一个字符。
*   `[]`：匹配括号内的任意一个字符。
*   `[^]`：匹配除了括号内字符以外的任意一个字符。
*   `[a-z]`：匹配指定范围内的一个字符（如小写字母a到z）。
*   `\`：转义字符，使后面的字符失去特殊含义。
*   `*`：匹配前一个字符0次或多次。

## 基本正则表达式详解

### 匹配行首与行尾

`^` 符号用于匹配以特定字符串开头的行。例如，`^root` 匹配以 “root” 开头的行。

![](img/b5dafa140dffcad930901ec491876ff0_3.png)

`$` 符号用于匹配以特定字符串结尾的行。例如，`bash$` 匹配以 “bash” 结尾的行。

![](img/b5dafa140dffcad930901ec491876ff0_5.png)

`^$` 组合用于匹配空行。

**示例代码：**
```bash
# 匹配以root开头的行
grep '^root' /etc/passwd

# 匹配以bash结尾的行
grep 'bash$' /etc/passwd

![](img/b5dafa140dffcad930901ec491876ff0_7.png)

# 匹配空行
grep '^$' filename

# 过滤掉空行
grep -v '^$' filename
```

### 匹配单词边界

`\<` 和 `\>` 用于精确匹配单词，而不是字符串的一部分。`\<root` 匹配以 “root” 开头的单词，`root\>` 匹配以 “root” 结尾的单词。

![](img/b5dafa140dffcad930901ec491876ff0_9.png)

**示例代码：**
```bash
# 匹配以root开头的单词
grep '\<root' /etc/passwd

![](img/b5dafa140dffcad930901ec491876ff0_11.png)

# 匹配以root结尾的单词
grep 'root\>' /etc/passwd
```

![](img/b5dafa140dffcad930901ec491876ff0_13.png)

### 匹配任意字符与字符集

`.` 符号可以匹配任意一个字符。

`[]` 用于定义一个字符集合，匹配集合中的任意一个字符。`[abc]` 匹配字符 a、b 或 c。

![](img/b5dafa140dffcad930901ec491876ff0_15.png)

![](img/b5dafa140dffcad930901ec491876ff0_17.png)

`[^]` 用于匹配不在集合中的任意一个字符。`[^abc]` 匹配除了 a、b、c 以外的任意一个字符。

`[a-z]` 匹配从小写 a 到 z 的任意一个字符。`[A-Z]` 匹配大写字母，`[0-9]` 匹配数字。

**示例代码：**
```bash
# 匹配包含r后接任意字符再接t的行，如 rat, rot, rut
grep 'r.t' /etc/passwd

![](img/b5dafa140dffcad930901ec491876ff0_19.png)

# 匹配以a、b或c开头的行
grep '^[abc]' /etc/passwd

![](img/b5dafa140dffcad930901ec491876ff0_21.png)

![](img/b5dafa140dffcad930901ec491876ff0_23.png)

# 匹配不以a、b或c开头的行
grep '^[^abc]' /etc/passwd

![](img/b5dafa140dffcad930901ec491876ff0_25.png)

# 匹配包含数字的行
grep '[0-9]' /etc/passwd
```

![](img/b5dafa140dffcad930901ec491876ff0_27.png)

### 匹配重复字符

![](img/b5dafa140dffcad930901ec491876ff0_29.png)

`*` 符号匹配前一个字符0次或多次。例如，`ro*t` 可以匹配 rt、rot、root、rooot 等。

![](img/b5dafa140dffcad930901ec491876ff0_31.png)

**示例代码：**
```bash
# 匹配r后面跟0个或多个o，再接t的字符串
grep 'ro*t' /etc/passwd
```

![](img/b5dafa140dffcad930901ec491876ff0_33.png)

![](img/b5dafa140dffcad930901ec491876ff0_35.png)

## 扩展正则表达式

扩展正则表达式在基本正则表达式的基础上，新增了一些元字符，功能更强大。使用时需使用 `grep -E` 或 `egrep` 命令。

![](img/b5dafa140dffcad930901ec491876ff0_37.png)

![](img/b5dafa140dffcad930901ec491876ff0_39.png)

以下是扩展正则表达式新增的元字符：

![](img/b5dafa140dffcad930901ec491876ff0_41.png)

*   `|`：匹配竖杠左边或右边的项，表示“或”的关系。
*   `+`：匹配前一个字符1次或多次。
*   `?`：匹配前一个字符0次或1次。
*   `{m,n}`：匹配前一个字符至少m次，至多n次。
*   `()`：将括号内的模式作为一个整体（分组）。

### 使用“或”关系匹配

![](img/b5dafa140dffcad930901ec491876ff0_43.png)

`|` 符号用于匹配多个模式中的任意一个。

![](img/b5dafa140dffcad930901ec491876ff0_45.png)

![](img/b5dafa140dffcad930901ec491876ff0_46.png)

**示例代码：**
```bash
# 匹配包含root或user的行
egrep 'root|user' /etc/passwd
# 或使用 grep -E
grep -E 'root|user' /etc/passwd
```

![](img/b5dafa140dffcad930901ec491876ff0_48.png)

### 指定匹配次数

![](img/b5dafa140dffcad930901ec491876ff0_50.png)

`+` 匹配前一个字符1次或多次。`?` 匹配前一个字符0次或1次。`{m,n}` 可以更精确地指定匹配次数。

![](img/b5dafa140dffcad930901ec491876ff0_52.png)

![](img/b5dafa140dffcad930901ec491876ff0_53.png)

**示例代码：**
```bash
# 匹配r后面跟1个或多个o，再接t的字符串（如rot, root，但不匹配rt）
egrep 'ro+t' /etc/passwd

# 匹配r后面跟0个或1个o，再接t的字符串（如rt, rot）
egrep 'ro?t' /etc/passwd

# 匹配r后面跟2个到3个o，再接t的字符串（如root, rooot）
egrep 'ro{2,3}t' /etc/passwd

![](img/b5dafa140dffcad930901ec491876ff0_55.png)

# 匹配r后面跟恰好2个o的字符串（root）
egrep 'ro{2}t' /etc/passwd
```

## 总结

本节课中我们一起学习了正则表达式的核心概念和应用。我们首先了解了正则表达式的作用是进行文本模式匹配。然后，详细讲解了基本正则表达式的元字符，如 `^`、`$`、`.`、`[]`、`*` 等，并学习了如何匹配行首行尾、单词边界、任意字符、字符集以及重复模式。

接着，我们介绍了功能更强大的扩展正则表达式，学习了如何使用 `|` 进行“或”匹配，以及 `+`、`?`、`{m,n}` 来更灵活地控制字符的匹配次数。记住，使用扩展正则表达式时，需要加上 `-E` 选项或直接使用 `egrep` 命令。

![](img/b5dafa140dffcad930901ec491876ff0_57.png)

正则表达式是文本处理的利器，不仅在 `grep` 命令中，在许多其他编程语言和工具中也有广泛应用。关键在于多练习，根据具体的匹配需求来组合使用这些元字符。