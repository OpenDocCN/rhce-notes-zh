# Linux用户管理：P16：用户密码设置与密码文件详解 🔑

![](img/ce1b8c86b5b39fb5d85c543433c7ecaa_0.png)

![](img/ce1b8c86b5b39fb5d85c543433c7ecaa_2.png)

![](img/ce1b8c86b5b39fb5d85c543433c7ecaa_4.png)

![](img/ce1b8c86b5b39fb5d85c543433c7ecaa_6.png)

在本节课中，我们将要学习如何为用户设置密码，并深入解析存储密码信息的 `/etc/shadow` 文件。这是管理用户账户安全性的核心知识。

![](img/ce1b8c86b5b39fb5d85c543433c7ecaa_8.png)

## 回顾与过渡

上一节我们介绍了用户的基本管理，包括使用 `useradd` 命令创建用户以及 `/etc/passwd` 文件的结构。本节中，我们来看看如何为用户设置密码，并详细解读与之密切相关的 `/etc/shadow` 密码文件。

![](img/ce1b8c86b5b39fb5d85c543433c7ecaa_10.png)

![](img/ce1b8c86b5b39fb5d85c543433c7ecaa_12.png)

## 设置用户密码

![](img/ce1b8c86b5b39fb5d85c543433c7ecaa_14.png)

`passwd` 命令用于设置或修改用户的登录密码。

![](img/ce1b8c86b5b39fb5d85c543433c7ecaa_16.png)

![](img/ce1b8c86b5b39fb5d85c543433c7ecaa_18.png)

![](img/ce1b8c86b5b39fb5d85c543433c7ecaa_20.png)

![](img/ce1b8c86b5b39fb5d85c543433c7ecaa_22.png)

### 命令基本格式

命令格式非常简单：`passwd [用户名]`。

*   **修改当前用户自身密码**：直接输入 `passwd` 并回车，系统会提示你输入新密码。
    ```bash
    passwd
    ```
*   **修改指定用户密码（仅root用户可用）**：在命令后跟上用户名。
    ```bash
    passwd user1
    ```

### 密码规范

系统对密码有严格的安全规范，普通用户修改密码时必须遵守：
1.  **长度**：不能少于8个字符。
2.  **复杂度**：需包含数字、字母（区分大小写）和特殊字符。
3.  **历史**：新密码不能与旧密码过于相似。

**注意**：`root` 用户为任何用户（包括自己）设置密码时，无需知道原密码，且不受上述复杂度限制。

![](img/ce1b8c86b5b39fb5d85c543433c7ecaa_24.png)

![](img/ce1b8c86b5b39fb5d85c543433c7ecaa_26.png)

![](img/ce1b8c86b5b39fb5d85c543433c7ecaa_28.png)

![](img/ce1b8c86b5b39fb5d85c543433c7ecaa_30.png)

### 密码管理常用选项

![](img/ce1b8c86b5b39fb5d85c543433c7ecaa_32.png)

![](img/ce1b8c86b5b39fb5d85c543433c7ecaa_34.png)

![](img/ce1b8c86b5b39fb5d85c543433c7ecaa_36.png)

![](img/ce1b8c86b5b39fb5d85c543433c7ecaa_38.png)

`passwd` 命令还有一些实用的选项：

![](img/ce1b8c86b5b39fb5d85c543433c7ecaa_40.png)

![](img/ce1b8c86b5b39fb5d85c543433c7ecaa_42.png)

*   **`-S`**：查看用户的密码状态信息。
    ```bash
    passwd -S user1
    ```
*   **`-l`**：锁定用户密码，使其无法登录。
    ```bash
    passwd -l user1
    ```
*   **`-u`**：解锁用户密码。
    ```bash
    passwd -u user1
    ```
*   **`-d`**：清除用户密码。
    ```bash
    passwd -d user1
    ```
*   **`--stdin`**：非交互式设置密码（常用于脚本中）。
    ```bash
    echo "MyPass123!" | passwd --stdin user1
    ```

![](img/ce1b8c86b5b39fb5d85c543433c7ecaa_44.png)

![](img/ce1b8c86b5b39fb5d85c543433c7ecaa_46.png)

## 深入 `/etc/shadow` 密码文件

用户设置密码后，加密的密码信息并非存储在 `/etc/passwd` 中，而是存放在 `/etc/shadow` 文件里。该文件权限严格，只有 `root` 用户可以读取和修改。

### 文件结构解析

与 `/etc/passwd` 类似，`/etc/shadow` 每行代表一个用户的密码信息，以冒号 `:` 分隔为 **9** 个字段。

我们以一行示例 `user1:$6$...:19077:0:60:7:7::` 来解读每个字段的含义：

1.  **用户名**：用户的登录名。
2.  **加密密码**：使用加密算法（如SHA512）处理后的密码字符串。如果为 `!!` 或 `*`，则表示该用户未设置密码或密码被锁定。
3.  **上次修改密码日期**：从1970年1月1日到上次修改密码那天的天数（时间戳）。
4.  **密码最短有效期**：密码修改后，多少天内不能再次修改。`0` 表示随时可改。
5.  **密码最长有效期**：密码多少天后必须更改。`99999` 是默认的极大值。
6.  **密码过期前警告天数**：在密码到期前多少天开始向用户发出警告。
7.  **密码过期后宽限天数**：密码过期后，仍允许使用旧密码登录并强制要求修改密码的天数。
8.  **账户失效日期**：从1970年1月1日起，账户将在多少天后完全失效（无法登录）。
9.  **保留字段**：目前未使用。

![](img/ce1b8c86b5b39fb5d85c543433c7ecaa_48.png)

### 关键字段的实践应用

![](img/ce1b8c86b5b39fb5d85c543433c7ecaa_50.png)

以下是两个常见的密码策略管理操作：

![](img/ce1b8c86b5b39fb5d85c543433c7ecaa_52.png)

![](img/ce1b8c86b5b39fb5d85c543433c7ecaa_54.png)

*   **强制用户首次登录时必须修改密码**：使用 `chage` 命令将用户“上次修改密码日期”字段设置为 `0`。
    ```bash
    chage -d 0 user1
    ```
    此后，用户 `user1` 首次使用初始密码登录时，系统会强制其立即设置新密码。

![](img/ce1b8c86b5b39fb5d85c543433c7ecaa_56.png)

![](img/ce1b8c86b5b39fb5d85c543433c7ecaa_58.png)

*   **设置密码有效期策略**：例如，要求用户每60天必须更改一次密码，并在到期前7天发出警告。
    ```bash
    chage -M 60 -W 7 user1
    ```

## 切换用户身份

![](img/ce1b8c86b5b39fb5d85c543433c7ecaa_60.png)

![](img/ce1b8c86b5b39fb5d85c543433c7ecaa_62.png)

![](img/ce1b8c86b5b39fb5d85c543433c7ecaa_64.png)

![](img/ce1b8c86b5b39fb5d85c543433c7ecaa_66.png)

`su` 命令用于切换当前登录用户的身份。

![](img/ce1b8c86b5b39fb5d85c543433c7ecaa_68.png)

![](img/ce1b8c86b5b39fb5d85c543433c7ecaa_69.png)

*   **切换用户（不切换环境变量）**：
    ```bash
    su username
    ```
*   **完全切换用户（包括环境变量和家目录）**：在命令后加 `-`。
    ```bash
    su - username
    ```
*   **退出切换，返回原用户**：
    ```bash
    exit
    ```
**注意**：`root` 用户切换到任何其他用户都无需输入密码。

## 课程总结

![](img/ce1b8c86b5b39fb5d85c543433c7ecaa_71.png)

![](img/ce1b8c86b5b39fb5d85c543433c7ecaa_73.png)

本节课中我们一起学习了 Linux 用户管理的核心安全部分：
1.  使用 `passwd` 命令为用户设置和管理密码，并了解了密码安全规范。
2.  详细解读了存储密码核心信息的 `/etc/shadow` 文件，理解了其9个字段的具体含义。
3.  掌握了通过 `chage` 命令实施密码策略，如强制首次登录改密和设置密码有效期。
4.  学会了使用 `su` 命令在不同用户身份间进行切换。

![](img/ce1b8c86b5b39fb5d85c543433c7ecaa_75.png)

![](img/ce1b8c86b5b39fb5d85c543433c7ecaa_77.png)

理解密码文件的机制和密码策略管理，对于维护系统账户安全至关重要。