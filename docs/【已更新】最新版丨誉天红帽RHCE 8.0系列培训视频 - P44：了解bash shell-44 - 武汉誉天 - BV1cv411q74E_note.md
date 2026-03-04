# Linux Shell基础：P44：PATH变量与别名

![](img/16db1b906b86f5dcb91242a58e8b1770_0.png)

![](img/16db1b906b86f5dcb91242a58e8b1770_2.png)

## 概述
在本节课中，我们将要学习Linux Shell中的两个核心概念：**PATH环境变量**和**命令别名**。我们将了解PATH变量如何决定系统查找命令的位置，以及如何创建和使用别名来简化命令操作。

---

## PATH环境变量详解

上一节我们介绍了环境变量和本地变量的区别，本节中我们来看看一个至关重要的环境变量：**PATH**。

PATH变量的作用是定义系统查找可执行命令的目录列表。当你在终端输入一个非绝对路径的命令（例如 `ls`）时，Shell会按照PATH变量中定义的目录顺序，依次在这些目录中查找该命令对应的可执行文件。

![](img/16db1b906b86f5dcb91242a58e8b1770_4.png)

![](img/16db1b906b86f5dcb91242a58e8b1770_5.png)

![](img/16db1b906b86f5dcb91242a58e8b1770_7.png)

### PATH变量的值
你可以使用 `echo $PATH` 命令查看当前PATH变量的值。其值通常是由冒号（`:`）分隔的一系列目录路径。

![](img/16db1b906b86f5dcb91242a58e8b1770_9.png)

![](img/16db1b906b86f5dcb91242a58e8b1770_11.png)

```bash
echo $PATH
# 输出示例：/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/bin
```

### 命令查找机制
`which` 命令可以用来查看一个命令的完整路径，它正是在PATH变量指定的目录中进行查找。

```bash
which ls
# 输出：/usr/bin/ls
```

如果创建一个新文件并赋予执行权限，但它的存放目录不在PATH中，直接输入文件名将无法执行。

```bash
touch mycmd.sh
chmod +x mycmd.sh
which mycmd.sh
# 输出为空，因为mycmd.sh不在PATH包含的目录中
```

### 修改PATH变量
PATH变量可以像其他变量一样被修改。但请注意，直接赋值会覆盖原有值。

**添加目录到PATH（推荐方法）**：
这种方法将新目录添加到现有PATH的末尾。
```bash
export PATH=$PATH:/your/new/directory
```

**临时修改PATH**：
直接在Shell中修改，这只在当前会话有效。
```bash
PATH=/usr/bin
# 此时，系统将只在/usr/bin目录下查找命令
```

**永久修改PATH**：
需要将 `export PATH=$PATH:/your/new/directory` 语句添加到用户的Shell配置文件中（如 `~/.bashrc` 或 `~/.bash_profile`），然后执行 `source ~/.bashrc` 使其生效。

### 重要注意事项
1.  **非递归查找**：PATH变量只查找指定目录本身，**不会递归查找其子目录**。例如，PATH包含 `/root`，但不会查找 `/root/bin` 下的命令。
2.  **系统依赖**：系统启动和运行依赖PATH找到关键命令（如 `mount`, `systemd`）。错误地修改（例如设为根目录 `/`）可能导致系统无法启动。
3.  **安全风险**：将当前目录（`.`）加入PATH，尤其是放在最前面，存在安全风险。恶意脚本可能与常用命令同名，导致你无意中执行了恶意程序。
4.  **变量类型**：PATH通常被定义为**环境变量**（使用 `export`），这样在子Shell中也能继承其值。

---

![](img/16db1b906b86f5dcb91242a58e8b1770_13.png)

## 命令别名（Alias）的使用

![](img/16db1b906b86f5dcb91242a58e8b1770_15.png)

![](img/16db1b906b86f5dcb91242a58e8b1770_17.png)

理解了PATH变量如何定位命令后，我们来看看如何通过创建“别名”来定制和简化命令。

![](img/16db1b906b86f5dcb91242a58e8b1770_18.png)

![](img/16db1b906b86f5dcb91242a58e8b1770_20.png)

别名允许你为常用的命令或命令组合创建一个简短的替代名称。

![](img/16db1b906b86f5dcb91242a58e8b1770_22.png)

![](img/16db1b906b86f5dcb91242a58e8b1770_23.png)

### 查看现有别名
使用 `alias` 命令可以列出当前Shell会话中所有已定义的别名。

![](img/16db1b906b86f5dcb91242a58e8b1770_25.png)

```bash
alias
```

![](img/16db1b906b86f5dcb91242a58e8b1770_27.png)

![](img/16db1b906b86f5dcb91242a58e8b1770_29.png)

### 定义新别名
使用 `alias` 关键字来定义别名。命令部分建议用**单引号**括起来，以避免特殊字符被Shell提前解释。

![](img/16db1b906b86f5dcb91242a58e8b1770_31.png)

![](img/16db1b906b86f5dcb91242a58e8b1770_33.png)

以下是定义别名的方法：
```bash
alias myls='ls -la'
alias cpetc='cp -r /etc /tmp/'
```

定义后，输入 `myls` 就等同于输入 `ls -la`。

### 取消别名
使用 `unalias` 命令可以取消已定义的别名。

```bash
unalias myls
```

### 别名的作用范围
在命令行直接定义的别名是**临时的**，仅对当前Shell会话有效。关闭终端或开启新会话后，别名就会失效。

![](img/16db1b906b86f5dcb91242a58e8b1770_35.png)

![](img/16db1b906b86f5dcb91242a58e8b1770_36.png)

**使别名永久生效**：
需要将别名定义语句（如 `alias myls='ls -la'`）添加到用户的Shell配置文件中（如 `~/.bashrc`），然后执行 `source ~/.bashrc`。

![](img/16db1b906b86f5dcb91242a58e8b1770_38.png)

### 别名与PATH的对比
*   **PATH**：告诉系统**去哪里找**命令文件。
*   **别名**：为已有的命令或命令串**创建一个新名字**，不改变命令的实际位置。

---

## 总结
本节课中我们一起学习了：
1.  **PATH环境变量**：它是系统查找可执行命令的路径列表。我们学习了如何查看、临时修改以及永久修改PATH变量，并理解了其非递归查找的特性及其对系统稳定性的重要性。
2.  **命令别名**：它允许我们为复杂的命令创建简短易记的替代名称。我们掌握了使用 `alias` 定义别名、使用 `unalias` 取消别名的方法，并知道了如何通过配置文件使别名永久生效。

![](img/16db1b906b86f5dcb91242a58e8b1770_40.png)

掌握PATH变量和别名是高效使用Linux Shell的基础，它们能极大地提升命令行操作的效率和个性化程度。