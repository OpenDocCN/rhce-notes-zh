# 红帽认证系列工程师RHCE RH124-Chapter06-管理本地用户和组：06-2：管理本地用户和组-获取超级用户访问权限 🔑

在本节课中，我们将要学习如何在Linux系统中获取超级用户权限。主要内容包括使用`su`命令切换用户身份，以及使用`sudo`命令临时获取管理员权限来执行命令。理解这两种方法对于安全、高效地管理系统至关重要。

## 切换用户身份：`su`命令

上一节我们介绍了用户和组的基本概念，本节中我们来看看如何在不同用户身份之间切换。在Linux系统中，`su`命令用于切换用户身份。

![](img/644e19e7da16b6dc4e5f07c9bf86f4e5_1.png)

大多数操作系统都有一个超级用户，在Linux中是`root`，在Windows中是`administrator`。通常，我们需要使用`root`身份来执行普通用户无法执行的管理任务，例如使用`useradd`添加用户或使用`passwd`更改其他用户的密码。

`su`命令有两种主要用法，它们在环境变量的加载上有明显区别。

以下是两种用法的具体说明：

*   **`su [用户名]`**：仅切换用户身份，但**不加载**目标用户的shell环境（如环境变量）。这意味着切换后，工作目录和环境可能仍是原用户的。
*   **`su - [用户名]`**：切换用户身份并**加载**目标用户的登录shell环境。这等同于一次全新的登录，会切换到目标用户的家目录并应用其所有环境设置。

因此，我们通常推荐使用`su - [用户名]`来获得一个完整的目标用户会话环境。从`root`用户切换到普通用户时，通常不需要输入密码。

## 临时提权工具：`sudo`命令

直接让所有用户都知道`root`密码存在安全风险。从安全角度考虑，不推荐直接使用`root`身份登录和管理系统。更安全的方法是使用`sudo`命令。

![](img/644e19e7da16b6dc4e5f07c9bf86f4e5_3.png)

`sudo`允许被授权的普通用户临时以超级用户（或其他用户）的身份执行命令。这类似于Windows中的UAC（用户账户控制）弹窗授权机制。使用`sudo`执行的命令会被详细记录在系统日志中（如`/var/log/secure`），便于审计。

![](img/644e19e7da16b6dc4e5f07c9bf86f4e5_5.png)

![](img/644e19e7da16b6dc4e5f07c9bf86f4e5_6.png)

要让一个用户能够使用`sudo`，需要将其添加到`sudoers`配置列表中。被授权的用户在执行命令时，只需在命令前加上`sudo`即可。

![](img/644e19e7da16b6dc4e5f07c9bf86f4e5_8.png)

![](img/644e19e7da16b6dc4e5f07c9bf86f4e5_9.png)

![](img/644e19e7da16b6dc4e5f07c9bf86f4e5_10.png)

## 配置`sudo`权限

![](img/644e19e7da16b6dc4e5f07c9bf86f4e5_11.png)

![](img/644e19e7da16b6dc4e5f07c9bf86f4e5_12.png)

并非所有用户都能使用`sudo`。我们需要通过编辑`sudoers`配置文件来授权。该文件的默认路径是`/etc/sudoers`。

![](img/644e19e7da16b6dc4e5f07c9bf86f4e5_14.png)

**重要提示**：请始终使用`visudo`命令来编辑`/etc/sudoers`文件。`visudo`会在保存前检查语法，防止配置错误导致所有用户都无法使用`sudo`。

以下是`/etc/sudoers`文件中的基本配置语法：

![](img/644e19e7da16b6dc4e5f07c9bf86f4e5_16.png)

![](img/644e19e7da16b6dc4e5f07c9bf86f4e5_17.png)

```
[用户或组] [主机列表] = ([以谁的身份]) [命令列表]
```

![](img/644e19e7da16b6dc4e5f07c9bf86f4e5_18.png)

![](img/644e19e7da16b6dc4e5f07c9bf86f4e5_19.png)

例如，`root ALL=(ALL) ALL`表示`root`用户可以在所有主机上，以任何用户的身份执行任何命令。

更常见的做法是授权一个管理组（如`wheel`）的所有成员。在RHEL系统中，默认配置可能允许`wheel`组成员执行所有命令：

![](img/644e19e7da16b6dc4e5f07c9bf86f4e5_21.png)

![](img/644e19e7da16b6dc4e5f07c9bf86f4e5_22.png)

![](img/644e19e7da16b6dc4e5f07c9bf86f4e5_23.png)

```
%wheel ALL=(ALL) ALL
```

![](img/644e19e7da16b6dc4e5f07c9bf86f4e5_25.png)

![](img/644e19e7da16b6dc4e5f07c9bf86f4e5_26.png)

我们可以进行更精细的控制。例如，允许`wheel`组成员执行所有命令，但禁止他们修改`root`用户的密码：

![](img/644e19e7da16b6dc4e5f07c9bf86f4e5_27.png)

![](img/644e19e7da16b6dc4e5f07c9bf86f4e5_28.png)

```
%wheel ALL=(ALL) ALL, !/usr/bin/passwd root
```

![](img/644e19e7da16b6dc4e5f07c9bf86f4e5_30.png)

这里的感叹号`!`表示排除或禁止。

![](img/644e19e7da16b6dc4e5f07c9bf86f4e5_32.png)

对于更复杂的配置，可以在`/etc/sudoers.d/`目录下为特定用户或组创建独立的配置文件。这些文件会被主配置文件包含。例如，创建一个文件`/etc/sudoers.d/90-cloud-init-users`，并添加以下内容，可以使`devops`用户在执行`sudo`时无需输入密码：

![](img/644e19e7da16b6dc4e5f07c9bf86f4e5_34.png)

```
devops ALL=(ALL) NOPASSWD:ALL
```

![](img/644e19e7da16b6dc4e5f07c9bf86f4e5_36.png)

在生产环境中，应根据“最小权限原则”仔细配置`sudo`规则，只授予用户完成工作所必需的最小权限，而不是简单地赋予全部权限。

![](img/644e19e7da16b6dc4e5f07c9bf86f4e5_38.png)

## 总结

![](img/644e19e7da16b6dc4e5f07c9bf86f4e5_40.png)

![](img/644e19e7da16b6dc4e5f07c9bf86f4e5_41.png)

本节课中我们一起学习了在Linux中获取超级用户权限的两种核心方法。我们了解了使用`su - [用户名]`来完整切换用户环境，以及更安全、更推荐的使用`sudo`命令来临时提权。我们还学习了如何通过编辑`/etc/sudoers`文件或`/etc/sudoers.d/`目录下的文件，来精细控制哪些用户可以在哪些主机上执行哪些命令。合理使用`su`和`sudo`是Linux系统安全管理的重要基础。