# 红帽RHCE8认证课程：03-2：命令行管理文件-练习指导 🗂️

![](img/5f044ce4a04e6ba4d93ea1e4a68c0320_1.png)

在本节课中，我们将通过一个综合练习来巩固命令行管理文件的知识。我们将学习如何创建、组织、移动、复制和删除文件与目录，并完成一个完整的实验环境准备与清理流程。

---

![](img/5f044ce4a04e6ba4d93ea1e4a68c0320_3.png)

![](img/5f044ce4a04e6ba4d93ea1e4a68c0320_5.png)

![](img/5f044ce4a04e6ba4d93ea1e4a68c0320_7.png)

## 概述

![](img/5f044ce4a04e6ba4d93ea1e4a68c0320_9.png)

本节练习旨在帮助初学者熟悉Linux命令行下的基本文件操作。我们将以`student`用户身份登录到实验环境，按照教材指导完成一系列任务，包括创建目录结构、生成文件、移动与复制文件，以及最后的清理工作。

![](img/5f044ce4a04e6ba4d93ea1e4a68c0320_11.png)

![](img/5f044ce4a04e6ba4d93ea1e4a68c0320_13.png)

---

![](img/5f044ce4a04e6ba4d93ea1e4a68c0320_15.png)

![](img/5f044ce4a04e6ba4d93ea1e4a68c0320_16.png)

## 实验环境准备

![](img/5f044ce4a04e6ba4d93ea1e4a68c0320_18.png)

在开始练习之前，需要确保实验环境已就绪。以下是准备步骤：

![](img/5f044ce4a04e6ba4d93ea1e4a68c0320_19.png)

1.  以`student`用户身份登录到`workstation`虚拟机。
2.  打开终端，执行以下命令来初始化实验环境：
    ```bash
    lab files-manager start
    ```
    此命令会运行一个脚本，确保可以从网络访问服务器`servera`。

![](img/5f044ce4a04e6ba4d93ea1e4a68c0320_21.png)

**提示**：你也可以选择从其他终端使用SSH直接登录到`workstation`，效果相同。
```bash
ssh student@workstation
```

![](img/5f044ce4a04e6ba4d93ea1e4a68c0320_23.png)

---

![](img/5f044ce4a04e6ba4d93ea1e4a68c0320_25.png)

## 练习步骤详解

![](img/5f044ce4a04e6ba4d93ea1e4a68c0320_27.png)

上一节我们介绍了实验环境的准备，本节中我们来看看具体的操作步骤。

![](img/5f044ce4a04e6ba4d93ea1e4a68c0320_29.png)

### 1. 登录目标服务器

![](img/5f044ce4a04e6ba4d93ea1e4a68c0320_31.png)

首先，我们需要登录到练习的目标服务器`servera`。

使用`ssh`命令以`student`用户身份登录：
```bash
ssh student@servera
```
系统可能配置了密钥认证，若需要密码，请输入`student`用户的密码。

### 2. 创建分类目录

登录后，你位于`student`用户的主目录（`~`）。我们需要创建三个子目录，用于分类存放文件。

![](img/5f044ce4a04e6ba4d93ea1e4a68c0320_33.png)

![](img/5f044ce4a04e6ba4d93ea1e4a68c0320_34.png)

以下是需要执行的命令：
```bash
mkdir music pictures videos
```
命令执行后，使用`ls`命令可以查看创建的`music`、`pictures`、`videos`目录。

![](img/5f044ce4a04e6ba4d93ea1e4a68c0320_36.png)

### 3. 创建空白文件

![](img/5f044ce4a04e6ba4d93ea1e4a68c0320_38.png)

接下来，在主目录中创建本实验所需的一套空白文件。我们需要创建6个音乐文件、6个图片文件和6个视频文件。

![](img/5f044ce4a04e6ba4d93ea1e4a68c0320_40.png)

以下是创建文件的命令示例：
```bash
touch song1.mp3 song2.mp3 song3.mp3 song4.mp3 song5.mp3 song6.mp3
```
```bash
touch snap1.jpg snap2.jpg snap3.jpg snap4.jpg snap5.jpg snap6.jpg
```
```bash
touch film1.avi film2.avi film3.avi film4.avi film5.avi film6.avi
```
你可以使用`ls -1`命令来简洁地查看所有创建的文件。

![](img/5f044ce4a04e6ba4d93ea1e4a68c0320_42.png)

### 4. 将文件移动到对应目录

![](img/5f044ce4a04e6ba4d93ea1e4a68c0320_44.png)

现在，我们需要将创建的文件移动到第2步创建的对应目录中，实现初步分类。

![](img/5f044ce4a04e6ba4d93ea1e4a68c0320_46.png)

![](img/5f044ce4a04e6ba4d93ea1e4a68c0320_48.png)

以下是移动文件的命令：
*   **移动歌曲文件到`music`目录**：
    ```bash
    mv song1.mp3 song2.mp3 song3.mp3 song4.mp3 song5.mp3 song6.mp3 music/
    ```
*   **移动快照文件到`pictures`目录**：
    ```bash
    mv snap1.jpg snap2.jpg snap3.jpg snap4.jpg snap5.jpg snap6.jpg pictures/
    ```
*   **移动影片文件到`videos`目录**：
    ```bash
    mv film1.avi film2.avi film3.avi film4.avi film5.avi film6.avi videos/
    ```
操作完成后，可以使用`tree`命令查看当前目录结构，确认文件已正确归类。

![](img/5f044ce4a04e6ba4d93ea1e4a68c0320_50.png)

### 5. 按项目整理文件

本步骤要求创建新的项目目录，并将特定文件复制进去。这模拟了更复杂的文件管理场景。

![](img/5f044ce4a04e6ba4d93ea1e4a68c0320_52.png)

首先，创建三个项目目录：
```bash
mkdir friends family work
```

然后，根据要求将文件复制到不同项目目录。以下是操作示例：

*   **复制文件到`friends`目录**：
    我们需要将包含数字1和2的文件复制到`friends`目录。这涉及从多个源目录复制文件。
    ```bash
    cd friends
    cp ../music/song1.mp3 ../music/song2.mp3 ~/videos/film1.avi ~/videos/film2.avi ~/pictures/snap1.jpg ~/pictures/snap2.jpg .
    ```
    命令说明：`cd friends`进入目标目录；`cp`命令后接源文件路径，最后的`.`代表当前目录（即`friends`）作为目标。

![](img/5f044ce4a04e6ba4d93ea1e4a68c0320_54.png)

*   **复制文件到`family`和`work`目录**：
    方法类似。将包含数字3和4的文件复制到`family`目录，将包含数字5和6的文件复制到`work`目录。请参照上述格式自行完成。

![](img/5f044ce4a04e6ba4d93ea1e4a68c0320_56.png)

![](img/5f044ce4a04e6ba4d93ea1e4a68c0320_57.png)

### 6. 清理目录

![](img/5f044ce4a04e6ba4d93ea1e4a68c0320_58.png)

![](img/5f044ce4a04e6ba4d93ea1e4a68c0320_60.png)

项目整理完成后，需要清理空的或不再需要的目录。

请注意，`rmdir`命令只能删除**空目录**。如果目录非空，需要使用`rm -rf`命令强制递归删除。

![](img/5f044ce4a04e6ba4d93ea1e4a68c0320_62.png)

![](img/5f044ce4a04e6ba4d93ea1e4a68c0320_64.png)

例如，删除空的`family`目录：
```bash
cd ~
rmdir family
```
删除非空的`friends`目录（包含我们复制的文件）：
```bash
rm -rf friends
```
请根据实际情况，清理`work`及其他练习中创建的目录。

![](img/5f044ce4a04e6ba4d93ea1e4a68c0320_66.png)

### 7. 完成实验

![](img/5f044ce4a04e6ba4d93ea1e4a68c0320_68.png)

所有练习完成后，需要退出`servera`，回到`workstation`执行清理脚本。

![](img/5f044ce4a04e6ba4d93ea1e4a68c0320_70.png)

在`workstation`的终端中执行：
```bash
lab files-manager finish
```
此脚本将删除本练习中创建的所有文件。你可以再次登录`servera`确认文件是否已被清理。

---

![](img/5f044ce4a04e6ba4d93ea1e4a68c0320_72.png)

## 总结

![](img/5f044ce4a04e6ba4d93ea1e4a68c0320_74.png)

本节课中我们一起完成了一个完整的命令行文件管理练习。我们实践了如何登录远程服务器、使用`mkdir`创建目录、使用`touch`创建文件、使用`mv`移动文件、使用`cp`复制文件，以及使用`rmdir`和`rm`删除目录与文件。通过这个循序渐进的练习，你应该对Linux基础文件操作有了更直观和牢固的理解。后续练习请参照此方法独立完成。