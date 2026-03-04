# RHCE8.0视频教程：P36：Ansible常用模块介绍（上）📚

![](img/ef10c3f3bdd7ec8689ce16a8277fbf36_1.png)

![](img/ef10c3f3bdd7ec8689ce16a8277fbf36_3.png)

![](img/ef10c3f3bdd7ec8689ce16a8277fbf36_5.png)

![](img/ef10c3f3bdd7ec8689ce16a8277fbf36_7.png)

在本节课中，我们将要学习Ansible中一些最常用的模块。上节课我们介绍了Ansible的基本环境配置和主机管理，本节中我们来看看如何利用不同的模块来执行具体的任务。

![](img/ef10c3f3bdd7ec8689ce16a8277fbf36_9.png)

## 环境准备与SSH免密登录

![](img/ef10c3f3bdd7ec8689ce16a8277fbf36_11.png)

![](img/ef10c3f3bdd7ec8689ce16a8277fbf36_13.png)

![](img/ef10c3f3bdd7ec8689ce16a8277fbf36_14.png)

上一节我们介绍了Ansible的基本环境配置，本节中我们来看看如何优化控制节点与被控节点之间的连接方式。

为了后续实验的便利，我们需要配置控制节点到被控节点的SSH免密登录。这样在执行Ansible命令时，就无需每次都输入密码。

![](img/ef10c3f3bdd7ec8689ce16a8277fbf36_16.png)

![](img/ef10c3f3bdd7ec8689ce16a8277fbf36_18.png)

以下是配置SSH免密登录的两个步骤：

1.  在控制节点上生成SSH密钥对。
2.  将公钥发送到被控节点。

具体操作命令如下：
```bash
# 在控制节点生成密钥对（直接回车使用默认选项）
ssh-keygen

![](img/ef10c3f3bdd7ec8689ce16a8277fbf36_20.png)

![](img/ef10c3f3bdd7ec8689ce16a8277fbf36_22.png)

# 将公钥发送到被控节点（例如IP为192.168.201.201）
ssh-copy-id -i ~/.ssh/id_rsa.pub root@192.168.201.201
```
配置完成后，可以使用`ansible`命令测试是否成功，例如使用`ping`模块：
```bash
ansible web1 -m ping
```
如果不需要输入密码且返回成功信息，则说明免密登录配置成功。

![](img/ef10c3f3bdd7ec8689ce16a8277fbf36_24.png)

![](img/ef10c3f3bdd7ec8689ce16a8277fbf36_26.png)

## Ansible执行状态的颜色含义

在Ansible执行任务时，输出信息会以不同颜色显示，这代表了不同的执行状态。这些颜色的定义可以在Ansible的配置文件中查看。

![](img/ef10c3f3bdd7ec8689ce16a8277fbf36_28.png)

![](img/ef10c3f3bdd7ec8689ce16a8277fbf36_30.png)

![](img/ef10c3f3bdd7ec8689ce16a8277fbf36_32.png)

以下是三种主要颜色的含义：

![](img/ef10c3f3bdd7ec8689ce16a8277fbf36_34.png)

*   **绿色**：表示任务执行成功，并且远端主机**没有被修改**。
*   **黄色**：表示任务执行成功，并且远端主机**有被修改**（例如创建或删除了文件）。
*   **红色**：表示任务**执行失败**。

例如，使用`command`模块删除一个文件，由于修改了远端主机，输出信息会显示为黄色。

## 常用模块介绍

Ansible拥有上千个模块，我们首先学习几个最基础、最常用的模块。理解这些模块的用法是后续编写Playbook的基础。

### Command模块

`command`模块用于在远程主机上执行命令。它是Ansible的默认模块，当不指定模块时，Ansible就会使用`command`。

以下是`command`模块的一些常用参数：

*   `chdir`：在执行命令前，先切换到指定的目录。
*   `creates`：指定一个文件名，如果该文件**存在**，则**不执行**后续命令。
*   `removes`：指定一个文件名，如果该文件**存在**，则**执行**后续命令。

让我们通过几个例子来理解它的用法。

**示例1：切换目录并查看文件**
```bash
# 先切换到/tmp目录，再查看aa.txt文件
ansible web1 -m command -a “chdir=/tmp cat aa.txt”
```

![](img/ef10c3f3bdd7ec8689ce16a8277fbf36_36.png)

![](img/ef10c3f3bdd7ec8689ce16a8277fbf36_38.png)

**示例2：使用`creates`参数**
```bash
# 如果/tmp/aa.txt文件存在，则不执行`cat`命令
ansible web1 -m command -a “creates=/tmp/aa.txt cat /tmp/aa.txt”
```

**示例3：使用`removes`参数**
```bash
# 如果/tmp/aa.txt文件存在，则执行`cat`命令
ansible web1 -m command -a “removes=/tmp/aa.txt cat /tmp/aa.txt”
```

**`command`模块的局限性：**
`command`模块不支持管道符 `|`、重定向符 `>` `<`、变量取值 `$VAR` 等Shell特性。例如，以下命令可能无法按预期工作：
```bash
# 尝试获取远程主机的主机名（可能失败）
ansible web1 -m command -a “echo $HOSTNAME”
```
此时，输出可能不是远程主机的`HOSTNAME`变量值。

### Shell模块

`shell`模块与`command`模块功能相似，但它是`command`模块的增强版。`shell`模块通过`/bin/sh`执行命令，因此支持管道、重定向、变量等所有Shell特性。

**示例1：正确获取远程主机名**
```bash
# 使用shell模块可以正确获取远程主机的环境变量
ansible web1 -m shell -a ‘echo $HOSTNAME’
```

![](img/ef10c3f3bdd7ec8689ce16a8277fbf36_40.png)

![](img/ef10c3f3bdd7ec8689ce16a8277fbf36_42.png)

**示例2：修改用户密码**
使用`command`模块修改密码可能失败，而`shell`模块可以成功。
```bash
# 为用户user0510设置密码
ansible web1 -m shell -a “echo ‘redhat123’ | passwd --stdin user0510”
```

![](img/ef10c3f3bdd7ec8689ce16a8277fbf36_44.png)

本节课中我们一起学习了Ansible的SSH免密登录配置、执行状态的颜色含义，并详细介绍了`command`和`shell`这两个基础命令执行模块。理解这些模块的用法和区别，是高效使用Ansible进行自动化管理的第一步。下节课我们将继续学习其他常用的Ansible模块。