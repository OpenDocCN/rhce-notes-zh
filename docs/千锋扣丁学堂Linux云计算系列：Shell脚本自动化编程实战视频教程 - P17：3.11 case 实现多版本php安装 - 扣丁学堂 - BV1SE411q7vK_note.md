# Shell脚本自动化编程实战：P17：3.11 case实现多版本PHP安装 🛠️

![](img/8f254694a3a3eaa8a59c98da642bfe19_0.png)

![](img/8f254694a3a3eaa8a59c98da642bfe19_2.png)

![](img/8f254694a3a3eaa8a59c98da642bfe19_4.png)

## 概述
在本节课中，我们将学习如何使用`case`语句来编写一个交互式脚本，实现多版本PHP的选择性安装。我们将通过一个具体的例子，演示如何构建一个结构清晰、易于维护的脚本，并理解脚本模块化的重要性。

---

## 脚本结构设计思路
上一节我们介绍了`case`语句的基本用法，本节中我们来看看如何将其应用于一个实际的场景。我们将编写一个脚本，允许用户从多个PHP版本中选择一个进行安装。

![](img/8f254694a3a3eaa8a59c98da642bfe19_6.png)

一个结构良好的脚本通常遵循“三部曲”原则：
1.  显示功能菜单。
2.  读取用户的选择。
3.  根据用户的选择执行相应的操作。

以下是脚本的基本框架：
```bash
#!/bin/bash
# 1. 显示菜单
echo "菜单内容..."
# 2. 读取用户输入
read -p "请选择 (1-3): " choice
# 3. 根据选择执行操作
case $choice in
    1)
        # 执行操作1
        ;;
    2)
        # 执行操作2
        ;;
    *)
        # 默认操作
        ;;
esac
```

![](img/8f254694a3a3eaa8a59c98da642bfe19_8.png)

![](img/8f254694a3a3eaa8a59c98da642bfe19_10.png)

---

![](img/8f254694a3a3eaa8a59c98da642bfe19_12.png)

## 多版本PHP安装脚本示例
我们以安装PHP为例。在实际生产环境中，不同的项目可能需要不同版本的PHP。使用源码编译安装可以灵活地将不同版本安装到不同目录，互不干扰。

![](img/8f254694a3a3eaa8a59c98da642bfe19_14.png)

![](img/8f254694a3a3eaa8a59c98da642bfe19_16.png)

以下是脚本的核心代码示例，它展示了如何利用`case`语句和函数来组织代码：

```bash
#!/bin/bash

![](img/8f254694a3a3eaa8a59c98da642bfe19_18.png)

![](img/8f254694a3a3eaa8a59c98da642bfe19_19.png)

![](img/8f254694a3a3eaa8a59c98da642bfe19_21.png)

![](img/8f254694a3a3eaa8a59c98da642bfe19_23.png)

# 定义安装不同版本PHP的函数（此处为演示，仅打印信息）
install_php56() {
    echo "正在安装 PHP 5.6 ..."
    # 此处可替换为实际的安装命令，例如源码编译步骤
}

install_php70() {
    echo "正在安装 PHP 7.0 ..."
}

install_php71() {
    echo "正在安装 PHP 7.1 ..."
}

# 主菜单函数
show_menu() {
    clear
    echo -e "\t 1. 安装 PHP 5.6"
    echo -e "\t 2. 安装 PHP 7.0"
    echo -e "\t 3. 安装 PHP 7.1"
    echo -e "\t q. 退出"
}

# 主程序循环
while true; do
    show_menu
    read -p "请选择要安装的PHP版本 (1-3 或 q): " version_choice

    case $version_choice in
        1)
            install_php56
            read -p "按任意键继续..."
            ;;
        2)
            install_php70
            read -p "按任意键继续..."
            ;;
        3)
            install_php71
            read -p "按任意键继续..."
            ;;
        q|Q)
            echo "退出安装程序。"
            exit 0
            ;;
        *)
            echo "输入错误，请重新选择。"
            read -p "按任意键继续..."
            ;;
    esac
done
```

---

## 脚本的模块化与优化
当每个安装函数的逻辑非常复杂时，将所有代码写在一个脚本中会显得臃肿且难以维护。更好的做法是进行模块化设计。

![](img/8f254694a3a3eaa8a59c98da642bfe19_25.png)

我们可以将每个版本的安装逻辑封装到独立的脚本文件中。主脚本只需“加载”这些函数，然后调用即可。

![](img/8f254694a3a3eaa8a59c98da642bfe19_27.png)

![](img/8f254694a3a3eaa8a59c98da642bfe19_28.png)

假设我们有一个专门安装PHP 5.6的脚本 `install_php56.sh`：
```bash
#!/bin/bash
# install_php56.sh - 仅定义函数，不直接执行
function install_php56() {
    echo "开始执行复杂的PHP 5.6源码编译安装过程..."
    # ./configure --prefix=/app/php56 ...
    # make && make install
}
```

![](img/8f254694a3a3eaa8a59c98da642bfe19_30.png)

![](img/8f254694a3a3eaa8a59c98da642bfe19_31.png)

![](img/8f254694a3a3eaa8a59c98da642bfe19_33.png)

![](img/8f254694a3a3eaa8a59c98da642bfe19_35.png)

在主安装脚本中，我们可以这样加载和使用它：
```bash
#!/bin/bash
# 主脚本 main_installer.sh

# 加载包含安装函数的脚本
source ./install_php56.sh

# 显示菜单，读取用户输入...
case $choice in
    1)
        # 直接调用已加载的函数
        install_php56
        ;;
    # ... 其他选项
esac
```
这种方法使得主脚本逻辑清晰，功能模块易于单独开发和调试。

---

## 实战练习：编写综合管理菜单
为了巩固`case`语句的用法，请尝试完成以下练习：

![](img/8f254694a3a3eaa8a59c98da642bfe19_37.png)

**目标**：编写一个综合软件管理脚本，具有二级菜单。
1.  **主菜单**提供安装不同软件（Apache, PHP, MySQL）的选项。
2.  选择某一软件（如PHP）后，进入**子菜单**，提供该软件不同版本的选择。
3.  在子菜单中可以选择具体版本安装，或返回主菜单。

![](img/8f254694a3a3eaa8a59c98da642bfe19_39.png)

**结构示意**：
```
主菜单：
1. 安装 Apache
2. 安装 PHP
3. 安装 MySQL
q. 退出

请输入选择：2

PHP安装子菜单：
1. 安装 PHP 5.6
2. 安装 PHP 7.0
3. 安装 PHP 7.1
0. 返回主菜单

![](img/8f254694a3a3eaa8a59c98da642bfe19_41.png)

![](img/8f254694a3a3eaa8a59c98da642bfe19_43.png)

请输入选择：1
（开始安装PHP 5.6...）
```

**要求**：
*   使用`case`语句处理用户输入。
*   使用函数来封装菜单显示和安装操作（安装操作可简化为打印一行提示信息）。
*   体会多级菜单和模块化脚本的设计思想。

---

![](img/8f254694a3a3eaa8a59c98da642bfe19_45.png)

## 总结
本节课我们一起学习了如何利用`case`语句构建一个实用的多版本软件安装脚本。我们掌握了脚本编写的“三部曲”原则，并深入探讨了通过函数和模块化（`source`加载独立脚本）来管理复杂逻辑的方法。这些技巧对于编写结构清晰、易于维护的自动化脚本至关重要。