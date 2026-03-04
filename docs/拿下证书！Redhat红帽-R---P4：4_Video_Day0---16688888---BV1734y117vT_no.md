# 拿下证书！Redhat红帽 RHCE8.0认证体系课程：P4：访问命令行

![](img/c4b2debe0afc252f20b09c74fb809724_0.png)

## 概述
在本节课中，我们将学习如何访问Linux命令行，包括本地与远程登录方式、终端类型切换、命令执行的基本技巧以及一些实用的快捷键。这些是后续深入学习系统管理的基础。

![](img/c4b2debe0afc252f20b09c74fb809724_2.png)

![](img/c4b2debe0afc252f20b09c74fb809724_4.png)

---

![](img/c4b2debe0afc252f20b09c74fb809724_6.png)

![](img/c4b2debe0afc252f20b09c74fb809724_7.png)

![](img/c4b2debe0afc252f20b09c74fb809724_8.png)

## 系统登录方式

![](img/c4b2debe0afc252f20b09c74fb809724_10.png)

![](img/c4b2debe0afc252f20b09c74fb809724_12.png)

上一节我们介绍了课程概述，本节中我们来看看如何访问Linux系统。

![](img/c4b2debe0afc252f20b09c74fb809724_14.png)

![](img/c4b2debe0afc252f20b09c74fb809724_15.png)

对于个人电脑，通常直接连接键盘、鼠标和显示器即可操作。对于服务器，除了直接连接外，通常还配备远程管理接口。无论是直接连接还是远程连接，都属于控制台访问模式。

![](img/c4b2debe0afc252f20b09c74fb809724_17.png)

![](img/c4b2debe0afc252f20b09c74fb809724_18.png)

在Linux系统中，本地登录意味着直接连接键盘和显示器。图形化界面提供了直观的桌面环境，而文本界面则完全通过命令行进行操作。



![](img/c4b2debe0afc252f20b09c74fb809724_20.png)

![](img/c4b2debe0afc252f20b09c74fb809724_22.png)

![](img/c4b2debe0afc252f20b09c74fb809724_23.png)

## 终端类型：TTY与PTS

![](img/c4b2debe0afc252f20b09c74fb809724_25.png)

接下来，我们区分两种主要的终端类型。

![](img/c4b2debe0afc252f20b09c74fb809724_27.png)

![](img/c4b2debe0afc252f20b09c74fb809724_29.png)

**TTY** 是指直接连接到系统的物理或虚拟文本控制台。例如，在服务器前直接使用键盘显示器登录，或在图形界面下切换到纯文本界面，看到的终端标识就是TTY。

![](img/c4b2debe0afc252f20b09c74fb809724_31.png)

**PTS** 代表伪终端，通常是通过网络连接（如SSH）创建的虚拟终端。在图形界面中打开的终端窗口也属于PTS。

以下是查看当前终端类型的方法：
```bash
# 执行ps命令查看终端信息
ps
```
命令输出中，`tty2` 表示第二个文本终端，而 `pts/0` 则表示第一个伪终端。



![](img/c4b2debe0afc252f20b09c74fb809724_33.png)

## 终端界面切换

了解了终端类型后，我们来看看如何在它们之间切换。

![](img/c4b2debe0afc252f20b09c74fb809724_35.png)

在纯文本安装的Linux系统上，可以使用 `Alt + F1` 到 `Alt + F6` 在多个文本终端（tty1-tty6）之间切换。

![](img/c4b2debe0afc252f20b09c74fb809724_37.png)

![](img/c4b2debe0afc252f20b09c74fb809724_39.png)

在安装了图形界面的系统上，图形界面通常占据 `tty1`。此时，需要使用 `Ctrl + Alt + F2` 到 `Ctrl + Alt + F6` 切换到文本终端，使用 `Ctrl + Alt + F1` 切换回图形界面。

![](img/c4b2debe0afc252f20b09c74fb809724_40.png)

![](img/c4b2debe0afc252f20b09c74fb809724_42.png)

![](img/c4b2debe0afc252f20b09c74fb809724_44.png)

> **注意**：部分笔记本电脑的F1-F12键有特殊功能（如调节音量），可能需要配合 `Fn` 键使用。



![](img/c4b2debe0afc252f20b09c74fb809724_46.png)

## 设置默认启动目标

![](img/c4b2debe0afc252f20b09c74fb809724_48.png)

![](img/c4b2debe0afc252f20b09c74fb809724_49.png)

除了临时切换，我们还可以设置系统默认启动到图形界面或文本界面。

![](img/c4b2debe0afc252f20b09c74fb809724_51.png)

![](img/c4b2debe0afc252f20b09c74fb809724_52.png)

在RHEL 8中，系统启动目标由 `systemd` 管理。我们可以查看和修改默认目标。

![](img/c4b2debe0afc252f20b09c74fb809724_54.png)

![](img/c4b2debe0afc252f20b09c74fb809724_56.png)

以下是相关操作命令：
```bash
# 查看当前默认启动目标
systemctl get-default

# 将默认启动目标设置为多用户文本模式（相当于旧版的init 3）
sudo systemctl set-default multi-user.target

![](img/c4b2debe0afc252f20b09c74fb809724_58.png)

# 将默认启动目标设置为图形模式（相当于旧版的init 5）
sudo systemctl set-default graphical.target
```
执行修改后，需要重启系统才能生效。



## 临时切换运行目标

![](img/c4b2debe0afc252f20b09c74fb809724_60.png)

如果只想临时切换界面，而不改变默认设置，可以使用 `isolate` 命令。

![](img/c4b2debe0afc252f20b09c74fb809724_62.png)

以下是临时切换的方法：
```bash
# 从当前界面临时切换到文本模式
sudo systemctl isolate multi-user.target

![](img/c4b2debe0afc252f20b09c74fb809724_64.png)

# 从当前界面临时切换到图形模式
sudo systemctl isolate graphical.target
```
这种方法在重启后会恢复为默认设置。



## 远程登录系统

在实际工作中，我们更常通过远程方式管理Linux服务器。

从Windows系统连接Linux，通常需要借助第三方SSH客户端工具，如Xshell、PuTTY等。从macOS或Linux系统连接，则可以直接使用终端下的 `ssh` 命令。

远程登录的命令格式如下：
```bash
ssh 用户名@服务器IP地址
```
例如：`ssh root@192.168.1.100`。执行后会提示输入对应用户的密码。

登录后，可以查看当前的SSH连接信息：
```bash
# 查看22端口的连接情况
ss -t | grep :22
```



## 注销登录会话

完成操作后，需要正确注销远程会话。

退出SSH登录会话有两种常用方法：
1.  输入 `exit` 命令。
2.  使用快捷键 `Ctrl + D`。

![](img/c4b2debe0afc252f20b09c74fb809724_66.png)

两种方式效果相同，都会终止当前的SSH连接。



![](img/c4b2debe0afc252f20b09c74fb809724_68.png)

![](img/c4b2debe0afc252f20b09c74fb809724_70.png)

## 命令执行与组合

![](img/c4b2debe0afc252f20b09c74fb809724_72.png)

现在，我们开始学习在命令行中执行命令的技巧。

![](img/c4b2debe0afc252f20b09c74fb809724_74.png)

执行单个命令后按回车键即可。若要顺序执行多个命令，可以使用分号 `;` 分隔。
```bash
# 顺序执行两条命令，无论前一条是否成功
cd /tmp; ls
```

除了顺序执行，还有两种逻辑组合命令的方式：
*   **逻辑与 `&&`**：只有前一条命令执行**成功**，才会执行后一条命令。
    ```bash
    mkdir test && cd test  # 只有创建目录成功，才会进入该目录
    ```
*   **逻辑或 `||`**：只有前一条命令执行**失败**，才会执行后一条命令。
    ```bash
    cd /nonexistent || echo “目录不存在”  # 切换目录失败，则输出提示
    ```

![](img/c4b2debe0afc252f20b09c74fb809724_76.png)

如果命令过长，可以使用反斜杠 `\` 将一条命令分成多行书写，以提高可读性。
```bash
# 将长命令分成多行
tail -n 1 \
/usr/share/doc/kernel-doc-*/README \
/usr/share/doc/gnu-*/README
```



![](img/c4b2debe0afc252f20b09c74fb809724_78.png)

## 命令补全与快捷键

高效使用命令行的关键是掌握补全和快捷键。

![](img/c4b2debe0afc252f20b09c74fb809724_80.png)

**Tab键补全** 是Linux命令行中最实用的功能之一，它可以补全命令、选项和文件路径。
*   输入命令开头部分，按一次 `Tab`，如果唯一则自动补全。
*   如果不唯一，按两次 `Tab`，会列出所有可能的选项。

以下是一些常用的命令行编辑快捷键：
*   `Ctrl + A`：光标跳到行首。
*   `Ctrl + E`：光标跳到行尾。
*   `Ctrl + U`：删除光标之前的所有内容。
*   `Ctrl + K`：删除光标之后的所有内容。
*   `Ctrl + ←/→`：以单词为单位向前/向后移动光标。



## 历史命令查找与调用

![](img/c4b2debe0afc252f20b09c74fb809724_82.png)

![](img/c4b2debe0afc252f20b09c74fb809724_84.png)

Linux会记录用户执行过的命令历史，默认保存1000条。

![](img/c4b2debe0afc252f20b09c74fb809724_86.png)

使用 `history` 命令可以查看历史命令列表。通过 `!序号` 可以快速重新执行该序号对应的历史命令。
```bash
# 查看历史命令
history

![](img/c4b2debe0afc252f20b09c74fb809724_88.png)

# 执行历史记录中第101条命令
!101
```

还可以搜索历史命令：
*   使用 `Ctrl + R` 进入反向搜索模式，输入关键词即可搜索历史命令。
*   在搜索模式下，继续按 `Ctrl + R` 可以向前查找更早的匹配项。
*   找到命令后，按回车即可执行，或按方向键进行编辑。



![](img/c4b2debe0afc252f20b09c74fb809724_90.png)

![](img/c4b2debe0afc252f20b09c74fb809724_92.png)

## 总结
本节课中我们一起学习了访问Linux命令行的核心知识。我们了解了本地与远程登录的方式，区分了TTY和PTS终端，并掌握了图形与文本界面的切换方法。我们还学习了命令的基本执行、逻辑组合、分行书写，以及Tab补全、历史命令调用和一系列提高效率的快捷键。这些是成为一名合格系统管理员的必备基础技能。