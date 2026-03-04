# Linux运维教程：P38：编写脚本、脚本执行方式 🖥️

在本节课中，我们将学习如何编写一个实用的Shell脚本，并了解如何优化脚本以提升其可读性和用户体验。我们将通过一个搭建本地Yum仓库的实例，来演示脚本的编写、执行以及优化技巧。

---

![](img/02063266cecf66e0cedcb38a4519a9e4_1.png)

![](img/02063266cecf66e0cedcb38a4519a9e4_3.png)

## 脚本编写基础

![](img/02063266cecf66e0cedcb38a4519a9e4_5.png)

上一节我们介绍了脚本的基本概念，本节中我们来看看如何编写一个具体的脚本。

![](img/02063266cecf66e0cedcb38a4519a9e4_7.png)

### 脚本执行速度控制

![](img/02063266cecf66e0cedcb38a4519a9e4_9.png)

某些命令执行速度非常快，输出信息会瞬间显示完毕，这可能导致用户看不清执行过程。为了让用户能看清每一步操作，可以在脚本中使用 `sleep` 命令。

`sleep` 命令的功能是让系统休眠指定的秒数，然后再执行后续命令。这并非让命令执行变慢，而是在命令执行后等待一段时间。

**示例代码：**
```bash
sleep 3
```

### 脚本内容组织

编写脚本时，可以加入注释和提示信息，使脚本逻辑更清晰。注释不区分英文和中文。

**示例代码：**
```bash
# 这是一个搭建本地仓库的脚本
echo “正在搭建本地软件仓库...”
```

---

## 脚本实例：搭建本地Yum仓库

![](img/02063266cecf66e0cedcb38a4519a9e4_11.png)

以下是搭建本地Yum仓库脚本的核心步骤。

![](img/02063266cecf66e0cedcb38a4519a9e4_13.png)

![](img/02063266cecf66e0cedcb38a4519a9e4_15.png)

### 1. 挂载光盘镜像

![](img/02063266cecf66e0cedcb38a4519a9e4_17.png)

![](img/02063266cecf66e0cedcb38a4519a9e4_19.png)

首先，需要将光盘镜像挂载到指定目录。如果已经挂载，则无需重复操作。

![](img/02063266cecf66e0cedcb38a4519a9e4_21.png)

![](img/02063266cecf66e0cedcb38a4519a9e4_23.png)

**示例代码：**
```bash
mount /dev/cdrom /mnt/centos
```

![](img/02063266cecf66e0cedcb38a4519a9e4_25.png)

### 2. 清理现有仓库配置

为了避免旧配置的干扰，可以清空Yum仓库的配置文件目录。

**示例代码：**
```bash
rm -rf /etc/yum.repos.d/*
```

### 3. 创建本地仓库配置文件

接下来，需要创建本地仓库的配置文件。我们可以使用 `cat` 命令结合重定向输入（EOF）来非交互式地写入文件内容，这比使用 `vim` 命令更适合脚本。

**示例代码：**
```bash
cat > /etc/yum.repos.d/local.repo << EOF
[local]
name=local
baseurl=file:///mnt/centos
enabled=1
gpgcheck=0
EOF
```

**代码解释：**
*   `cat > 文件名`：创建或覆盖文件。
*   `<< EOF`：开始重定向输入，直到遇到下一个 `EOF`。
*   中间的内容会被写入指定文件。
*   最后的 `EOF`：结束输入。

### 4. 验证仓库

最后，使用 `yum` 命令列出仓库中的软件包，以验证配置是否成功。

![](img/02063266cecf66e0cedcb38a4519a9e4_27.png)

![](img/02063266cecf66e0cedcb38a4519a9e4_29.png)

**示例代码：**
```bash
yum repolist
```

![](img/02063266cecf66e0cedcb38a4519a9e4_31.png)

---

![](img/02063266cecf66e0cedcb38a4519a9e4_33.png)

## 脚本优化与用户体验

![](img/02063266cecf66e0cedcb38a4519a9e4_35.png)

一个优秀的脚本不仅功能正确，还应具有良好的可读性和用户体验。

![](img/02063266cecf66e0cedcb38a4519a9e4_37.png)

### 1. 添加执行状态提示

![](img/02063266cecf66e0cedcb38a4519a9e4_39.png)

![](img/02063266cecf66e0cedcb38a4519a9e4_41.png)

![](img/02063266cecf66e0cedcb38a4519a9e4_42.png)

在关键步骤前后添加 `echo` 提示信息，并使用 `sleep` 命令暂停，让用户清楚脚本正在执行什么操作。

**示例代码：**
```bash
echo “脚本正在搭建本地软件仓库...”
sleep 2
# ... 执行挂载、配置等操作 ...
echo “正在验证软件包数量...”
sleep 1
yum repolist
```

![](img/02063266cecf66e0cedcb38a4519a9e4_44.png)

### 2. 简化命令输出

![](img/02063266cecf66e0cedcb38a4519a9e4_46.png)

某些命令（如 `yum repolist`）的默认输出包含冗余信息。我们可以使用管道（`|`）和文本处理命令（如 `grep`、`tail`、`awk`）来过滤，只显示核心信息。

**示例代码（使用grep）：**
```bash
yum repolist | grep repolist
```
**示例代码（使用tail）：**
```bash
yum repolist | tail -1
```
**示例代码（使用awk，更精确）：**
```bash
yum repolist | awk ‘/repolist:/{print $2}’
```

### 3. 使用变量存储结果

将命令执行结果存入变量，可以使脚本更灵活，输出信息更友好。

**示例代码：**
```bash
package_count=$(yum repolist | awk ‘/repolist:/{print $2}’)
echo “软件包总数量为：$package_count”
```

![](img/02063266cecf66e0cedcb38a4519a9e4_48.png)

![](img/02063266cecf66e0cedcb38a4519a9e4_50.png)

### 4. 增加“进度条”效果（可选）

为了提升视觉效果，可以添加一个简单的“进度条”动画。这通常是一个循环打印字符的过程，与实际任务进度无关，主要用于提升体验感。

**示例代码（一个简单的点状进度提示）：**
```bash
echo -n “正在处理”
for i in {1..5}; do
    echo -n “.”
    sleep 0.5
done
echo “ 完成！”
```

![](img/02063266cecf66e0cedcb38a4519a9e4_52.png)

---

![](img/02063266cecf66e0cedcb38a4519a9e4_54.png)

## 脚本执行方式

![](img/02063266cecf66e0cedcb38a4519a9e4_56.png)

编写完成的脚本需要赋予执行权限才能运行。

![](img/02063266cecf66e0cedcb38a4519a9e4_58.png)

**示例代码：**
```bash
chmod +x local_yum.sh  # 赋予脚本执行权限
./local_yum.sh         # 执行脚本
```

---

## 总结

![](img/02063266cecf66e0cedcb38a4519a9e4_60.png)

本节课中我们一起学习了如何编写一个完整的Shell脚本。我们从控制脚本执行速度开始，逐步完成了挂载镜像、清理配置、创建仓库文件以及验证结果等步骤。更重要的是，我们探讨了如何通过添加提示信息、简化输出、使用变量和增加视觉效果来优化脚本，使其不仅功能完备，而且清晰易懂、用户体验良好。掌握这些技巧，你将能编写出更专业、更实用的自动化脚本。