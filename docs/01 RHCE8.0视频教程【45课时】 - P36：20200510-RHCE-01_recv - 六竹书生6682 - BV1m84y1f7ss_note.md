# RHCE 8.0 课程：P36：常用模块介绍（上）📚

![](img/87096860a07ac0ebf43f3e276e84aa78_1.png)

![](img/87096860a07ac0ebf43f3e276e84aa78_3.png)

![](img/87096860a07ac0ebf43f3e276e84aa78_5.png)

![](img/87096860a07ac0ebf43f3e276e84aa78_7.png)

在本节课中，我们将要学习 Ansible 的常用模块。上节课我们学习了 Ansible 环境的配置，并进行了基础的主机管理操作。本节中，我们来看看 Ansible 中一些核心模块的使用方法，它们是编写自动化任务的基础。

![](img/87096860a07ac0ebf43f3e276e84aa78_9.png)

## 环境准备与回顾

![](img/87096860a07ac0ebf43f3e276e84aa78_11.png)

![](img/87096860a07ac0ebf43f3e276e84aa78_13.png)

![](img/87096860a07ac0ebf43f3e276e84aa78_14.png)

上一节我们介绍了 Ansible 的基本配置，并克隆了三台虚拟机。为了简化后续的测试，本节课我们将保留一台虚拟机（例如 `web1`，IP 为 `192.168.1.201`）作为被控节点，由控制节点 `control` 进行管理。

首先，我们需要配置控制节点对 `web1` 的 SSH 免密登录，以避免每次执行任务时都需要输入密码。

![](img/87096860a07ac0ebf43f3e276e84aa78_16.png)

![](img/87096860a07ac0ebf43f3e276e84aa78_18.png)

以下是配置 SSH 免密登录的步骤：

1.  在控制节点 `control` 上生成 SSH 密钥对。
    ```bash
    ssh-keygen
    ```
2.  将公钥发送到被控节点 `web1`。
    ```bash
    ssh-copy-id root@192.168.1.201
    ```
    首次发送时需要输入 `web1` 的 root 用户密码。

配置完成后，我们可以测试免密登录是否成功。使用 `ping` 模块测试连通性，现在无需再输入密码。
```bash
ansible web1 -m ping
```

![](img/87096860a07ac0ebf43f3e276e84aa78_20.png)

![](img/87096860a07ac0ebf43f3e276e84aa78_22.png)

## Ansible 执行状态颜色

![](img/87096860a07ac0ebf43f3e276e84aa78_24.png)

![](img/87096860a07ac0ebf43f3e276e84aa78_26.png)

在执行 Ansible 命令时，输出结果会以不同颜色显示，这代表了不同的执行状态。理解这些颜色有助于快速判断任务执行情况。

以下是三种主要颜色的含义：

![](img/87096860a07ac0ebf43f3e276e84aa78_28.png)

![](img/87096860a07ac0ebf43f3e276e84aa78_30.png)

![](img/87096860a07ac0ebf43f3e276e84aa78_32.png)

*   **绿色**：任务执行**成功**，且远端主机**未被修改**。
*   **黄色**：任务执行**成功**，且远端主机**已被修改**。
*   **红色**：任务执行**失败**。

![](img/87096860a07ac0ebf43f3e276e84aa78_34.png)

例如，使用 `command` 模块删除一个文件，由于修改了远端主机，输出会显示为黄色。

## 常用模块介绍

Ansible 拥有上千个模块，我们首先学习几个最常用的核心模块。理解模块的参数和用法是编写 Playbook 的基础。

### command 模块

`command` 模块用于在远程主机上执行命令。它是 Ansible 的默认模块，即不指定 `-m` 参数时使用的模块。

以下是 `command` 模块的一些常用参数：

*   `chdir`：在执行命令前，先切换到指定目录。
*   `creates`：指定一个文件路径。如果该文件**存在**，则**不执行**后续命令。
*   `removes`：指定一个文件路径。如果该文件**存在**，则**执行**后续命令。

让我们通过几个例子来理解这些参数。

**示例 1：切换目录并查看文件**
假设远端 `/tmp` 目录下存在文件 `aa.test`。
```bash
ansible web1 -m command -a “chdir=/tmp cat aa.test”
```
此命令会先切换到 `/tmp` 目录，然后查看 `aa.test` 文件的内容。

**示例 2：使用 `creates` 参数**
```bash
ansible web1 -m command -a “creates=/tmp/aa.test cat /tmp/aa.test”
```
因为 `/tmp/aa.test` 文件存在，所以 `cat` 命令**不会执行**。

![](img/87096860a07ac0ebf43f3e276e84aa78_36.png)

![](img/87096860a07ac0ebf43f3e276e84aa78_38.png)

**示例 3：使用 `removes` 参数**
```bash
ansible web1 -m command -a “removes=/tmp/aa.test cat /tmp/aa.test”
```
因为 `/tmp/aa.test` 文件存在，所以 `cat` 命令**会执行**。

**`command` 模块的局限性**
`command` 模块不支持管道符 `|`、重定向符 `>`、`<` 以及变量引用（如 `$HOME`）等 Shell 特性。例如，尝试使用 `echo $HOSTNAME` 会失败，因为它无法正确处理 `$` 符号。

### shell 模块

`shell` 模块与 `command` 模块功能相似，但它是 `command` 模块的增强版。它支持管道、重定向、变量引用等所有 Shell 特性。

**示例 1：显示主机名**
使用 `shell` 模块可以正确获取远端主机的主机名。
```bash
ansible web1 -m shell -a ‘echo $HOSTNAME’
```

**示例 2：修改用户密码**
使用 `shell` 模块可以成功修改用户密码。
```bash
ansible web1 -m shell -a “echo ‘redhat123’ | passwd --stdin user0510”
```
修改后，可以检查 `/etc/shadow` 文件确认密码已更新。

![](img/87096860a07ac0ebf43f3e276e84aa78_40.png)

![](img/87096860a07ac0ebf43f3e276e84aa78_42.png)

---

![](img/87096860a07ac0ebf43f3e276e84aa78_44.png)

本节课中我们一起学习了 Ansible 的环境准备、执行状态颜色的含义，并深入探讨了 `command` 和 `shell` 这两个基础但强大的模块。`command` 模块简单直接，而 `shell` 模块功能更全面，支持复杂的 Shell 命令。理解它们的区别和适用场景，是构建自动化任务的第一步。下节课我们将继续学习其他常用模块。