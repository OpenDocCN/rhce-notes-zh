# Linux运维培训教程：P38：编写与执行Shell脚本

![](img/d7fe309e123045d184b4dcefb23ee0f6_1.png)

![](img/d7fe309e123045d184b4dcefb23ee0f6_3.png)

在本节课中，我们将学习如何编写一个实用的Shell脚本，并了解脚本的不同执行方式。我们将通过一个“搭建本地YUM仓库”的实例，来掌握脚本编写的基本思路、优化技巧以及如何让脚本执行过程更友好。

![](img/d7fe309e123045d184b4dcefb23ee0f6_5.png)

## 脚本编写基础与思路

![](img/d7fe309e123045d184b4dcefb23ee0f6_7.png)

![](img/d7fe309e123045d184b4dcefb23ee0f6_9.png)

上一节我们介绍了Shell脚本的基本概念。本节中我们来看看如何将一系列手动操作整合成一个自动化的脚本。

编写脚本的核心思路是：将需要重复执行的命令行操作，按顺序写入一个文本文件，并赋予其执行权限。

## 实例：编写本地YUM仓库搭建脚本

我们的目标是创建一个脚本，自动完成挂载光盘、清理原有配置、创建本地YUM仓库文件并验证结果。

![](img/d7fe309e123045d184b4dcefb23ee0f6_11.png)

首先，我们创建一个脚本文件，例如 `local_yum.sh`：
```bash
#!/bin/bash
```
脚本的第一行 `#!/bin/bash` 指定了解释器，告诉系统使用哪个Shell来执行此脚本。

![](img/d7fe309e123045d184b4dcefb23ee0f6_13.png)

![](img/d7fe309e123045d184b4dcefb23ee0f6_15.png)

![](img/d7fe309e123045d184b4dcefb23ee0f6_17.png)

### 脚本内容分解

![](img/d7fe309e123045d184b4dcefb23ee0f6_19.png)

![](img/d7fe309e123045d184b4dcefb23ee0f6_21.png)

![](img/d7fe309e123045d184b4dcefb23ee0f6_23.png)

![](img/d7fe309e123045d184b4dcefb23ee0f6_25.png)

以下是脚本实现的核心步骤：

1.  **挂载光盘**：使用 `mount` 命令将光盘镜像挂载到指定目录。
    ```bash
    mount /dev/cdrom /mnt/centos
    ```

2.  **清理原有仓库配置**：删除 `/etc/yum.repos.d/` 目录下所有文件，为创建新配置做准备。
    ```bash
    rm -rf /etc/yum.repos.d/*
    ```

3.  **创建本地仓库配置文件**：这是脚本的关键。我们不使用交互式的 `vim` 命令，而是用 `cat` 命令配合重定向输入（Here Document）来非交互式地写入文件内容。
    ```bash
    cat > /etc/yum.repos.d/local.repo << EOF
    [local]
    name=local
    baseurl=file:///mnt/centos
    enabled=1
    gpgcheck=0
    EOF
    ```
    **代码解释**：
    *   `cat > /etc/yum.repos.d/local.repo`：将输出重定向到指定文件（覆盖写入）。
    *   `<< EOF` ... `EOF`：这是一个“Here Document”结构。`EOF` 是自定义的结束标记符（常用EOF或END）。两个 `EOF` 之间的所有内容都会被作为标准输入传递给前面的 `cat` 命令，最终写入目标文件。

4.  **验证仓库**：使用 `yum repolist` 命令列出仓库信息，验证是否配置成功。
    ```bash
    yum repolist
    ```

![](img/d7fe309e123045d184b4dcefb23ee0f6_27.png)

### 提升脚本可读性与体验

![](img/d7fe309e123045d184b4dcefb23ee0f6_29.png)

![](img/d7fe309e123045d184b4dcefb23ee0f6_31.png)

![](img/d7fe309e123045d184b4dcefb23ee0f6_33.png)

直接执行上述命令的脚本会非常快，用户可能看不清执行过程。我们可以通过以下方法优化：

![](img/d7fe309e123045d184b4dcefb23ee0f6_35.png)

*   **添加注释与提示信息**：使用 `echo` 命令输出提示，告诉用户脚本正在做什么。
    ```bash
    echo “正在搭建本地软件仓库...”
    sleep 2 # 暂停2秒，让用户看清提示
    ```
*   **过滤命令输出**：有些命令（如 `yum repolist`）输出信息较多，我们可以用管道 (`|`) 和 `grep` 命令只显示关键信息。
    ```bash
    yum repolist | grep repolist
    ```
    或者使用 `awk` 提取更精确的数据（后续课程会详解）：
    ```bash
    yum repolist | awk ‘/repolist/{print $2}’
    ```

![](img/d7fe309e123045d184b4dcefb23ee0f6_37.png)

![](img/d7fe309e123045d184b4dcefb23ee0f6_39.png)

![](img/d7fe309e123045d184b4dcefb23ee0f6_41.png)

![](img/d7fe309e123045d184b4dcefb23ee0f6_42.png)

## 脚本的执行方式

![](img/d7fe309e123045d184b4dcefb23ee0f6_44.png)

![](img/d7fe309e123045d184b4dcefb23ee0f6_46.png)

脚本编写完成后，有多种方式可以执行它：

1.  **使用 `bash` 或 `sh` 解释器直接执行**：
    ```bash
    bash local_yum.sh
    sh local_yum.sh
    ```
    这种方式无需给脚本文件赋予执行权限。

2.  **赋予执行权限后直接运行**（推荐）：
    ```bash
    chmod +x local_yum.sh # 赋予执行权限
    ./local_yum.sh # 在当前目录下执行
    ```
    注意，执行当前目录下的脚本必须在前面加上 `./`。

![](img/d7fe309e123045d184b4dcefb23ee0f6_48.png)

3.  **通过绝对路径或放入 `PATH` 执行**：
    ```bash
    /root/local_yum.sh # 使用绝对路径
    ```
    如果将脚本移动到 `/usr/local/bin` 等已存在于 `PATH` 环境变量的目录中，则可以直接输入脚本名执行。

![](img/d7fe309e123045d184b4dcefb23ee0f6_50.png)

## 脚本的优化与进阶思路

![](img/d7fe309e123045d184b4dcefb23ee0f6_52.png)

![](img/d7fe309e123045d184b4dcefb23ee0f6_54.png)

一个优秀的脚本不仅仅是能运行，还应考虑健壮性和用户体验。

![](img/d7fe309e123045d184b4dcefb23ee0f6_56.png)

*   **错误处理**：检查目录是否存在、挂载是否成功等。例如，在删除目录前可先检查：
    ```bash
    [ -d “/etc/yum.repos.d” ] && rm -rf /etc/yum.repos.d/*
    ```
*   **使用变量**：使脚本更灵活。例如，将挂载点定义为变量：
    ```bash
    MOUNT_DIR=“/mnt/centos”
    mount /dev/cdrom $MOUNT_DIR
    ```
*   **增加“进度条”或动态效果**（增强体验感）：通过循环打印字符模拟进度。这是一个固定格式的“装潢”代码段，可直接复用：
    ```bash
    echo -n “正在处理：[”
    for i in {1..20}; do echo -n “#”; sleep 0.1; done
    echo “] 完成！”
    ```

![](img/d7fe309e123045d184b4dcefb23ee0f6_58.png)

## 总结

![](img/d7fe309e123045d184b4dcefb23ee0f6_60.png)

本节课中我们一起学习了Shell脚本的编写与执行。我们通过一个搭建本地YUM仓库的实例，掌握了脚本的基本结构、如何使用 `cat` 命令进行非交互式文件写入、如何通过 `echo` 和 `sleep` 提升脚本交互体验，以及脚本的几种执行方式。记住，编写脚本的核心目的是将重复、繁琐的操作自动化，初期可以从模仿和整合现有命令开始，逐步加入错误判断、变量使用等高级功能，最终写出健壮、易用的自动化脚本。