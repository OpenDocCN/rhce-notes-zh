# Linux系统管理：P24：用户计划任务与救援模式

![](img/dd71331e74e19cdf0fa7bac77d0bedd7_1.png)

![](img/dd71331e74e19cdf0fa7bac77d0bedd7_3.png)

## 概述
在本节课中，我们将学习Linux系统中的两个重要主题：用户计划任务（cron）和系统救援模式。我们将了解如何配置定时任务，以及当系统无法正常启动或忘记root密码时，如何使用救援模式进行修复。

---

## 用户计划任务管理

![](img/dd71331e74e19cdf0fa7bac77d0bedd7_5.png)

![](img/dd71331e74e19cdf0fa7bac77d0bedd7_6.png)

上一节我们介绍了用户计划任务的基本概念，本节中我们来看看如何具体管理和配置它。

用户计划任务存储在特定文件中，用于记录定时执行的命令。对于root用户，其计划任务文件位于 `/var/spool/cron/root`。

我们可以使用 `crontab` 命令来管理这些任务。

以下是 `crontab` 命令的常用参数：
*   `-l`：列出当前用户的所有计划任务。
*   `-e`：编辑当前用户的计划任务文件。
*   `-r`：移除当前用户的所有计划任务（使用需谨慎）。
*   `-i`：在移除任务前进行确认提示。

### 配置计划任务示例
我们可以编辑计划任务来执行特定命令。例如，创建一个每分钟执行一次的用户：
```bash
* * * * * /usr/sbin/useradd testuser
```
此配置表示五个星号（代表分、时、日、月、周），意为每分钟执行一次后面的命令。

### 计划任务执行注意事项
计划任务运行时，其输出结果默认会通过邮件发送给相关用户。如果不希望接收邮件，可以将输出重定向。
*   丢弃所有输出（标准输出和错误输出）：
    ```bash
    * * * * * /path/to/command > /dev/null 2>&1
    ```
*   在cron命令中，百分号 `%` 有特殊含义（代表换行符）。如果命令中需要使用百分号，必须使用反斜杠 `\` 进行转义。
    *   错误示例（`%` 未被转义）：
        ```bash
        * * * * * /bin/echo "Date: %Y-%m-%d"
        ```
    *   正确示例（`%` 被转义）：
        ```bash
        * * * * * /bin/echo "Date: \%Y-\%m-\%d"
        ```

![](img/dd71331e74e19cdf0fa7bac77d0bedd7_8.png)

此外，请确保计划任务中调用的脚本或命令具有可执行权限，否则任务将执行失败。

![](img/dd71331e74e19cdf0fa7bac77d0bedd7_10.png)

![](img/dd71331e74e19cdf0fa7bac77d0bedd7_11.png)

---

![](img/dd71331e74e19cdf0fa7bac77d0bedd7_13.png)

## 系统救援模式操作

![](img/dd71331e74e19cdf0fa7bac77d0bedd7_15.png)

![](img/dd71331e74e19cdf0fa7bac77d0bedd7_17.png)

当系统因配置错误无法启动，或忘记root密码时，可以使用救援模式进行修复。下面我们通过重置root密码的实例来演示救援模式的操作流程。

![](img/dd71331e74e19cdf0fa7bac77d0bedd7_19.png)

![](img/dd71331e74e19cdf0fa7bac77d0bedd7_20.png)

![](img/dd71331e74e19cdf0fa7bac77d0bedd7_22.png)

1.  **启动虚拟机并进入内核选择界面**：启动系统，在GRUB引导界面出现时，按上下方向键停止倒计时。屏幕提示按 `e` 键进行编辑。

![](img/dd71331e74e19cdf0fa7bac77d0bedd7_24.png)

![](img/dd71331e74e19cdf0fa7bac77d0bedd7_26.png)

2.  **编辑内核启动参数**：按 `e` 键后，进入编辑界面。找到以 `linux` 开头的那一行，将光标移动至该行末尾。

![](img/dd71331e74e19cdf0fa7bac77d0bedd7_28.png)

![](img/dd71331e74e19cdf0fa7bac77d0bedd7_30.png)

3.  **修改启动参数**：删除该行中从 `ro` 到 `quiet` 之间的所有参数（`ro` 表示只读挂载）。然后，在删除后的位置添加 `rw init=/sysroot/bin/sh`。其中 `rw` 表示读写挂载，`init=/sysroot/bin/sh` 指定了救援模式的shell路径。

    修改后的行应类似于：
    ```
    linux /vmlinuz... rw init=/sysroot/bin/sh
    ```

4.  **进入救援模式**：按 `Ctrl + X` 组合键，系统将以修改后的参数启动，并进入救援模式的shell环境，提示符为 `sh-5.1#`。

5.  **切换根环境并修改密码**：
    *   首先切换根目录到原系统的根分区：`chroot /sysroot`
    *   现在可以像在正常系统中一样操作。使用 `passwd root` 命令修改root用户的密码，根据提示输入两次新密码。
    *   如果系统启用了SELinux，需要创建自动重新标记文件，以便在下次启动时恢复安全上下文：`touch /.autorelabel`

![](img/dd71331e74e19cdf0fa7bac77d0bedd7_32.png)

![](img/dd71331e74e19cdf0fa7bac77d0bedd7_34.png)

![](img/dd71331e74e19cdf0fa7bac77d0bedd7_35.png)

6.  **退出并重启**：
    *   输入 `exit` 退出chroot环境。
    *   输入 `reboot -f` 强制重启系统。
    *   系统重启后，即可使用新设置的root密码登录。

![](img/dd71331e74e19cdf0fa7bac77d0bedd7_37.png)

![](img/dd71331e74e19cdf0fa7bac77d0bedd7_39.png)

---

![](img/dd71331e74e19cdf0fa7bac77d0bedd7_41.png)

## 总结
本节课中我们一起学习了Linux用户计划任务的管理和系统救援模式的使用。我们掌握了使用 `crontab` 命令配置定时任务的方法，并了解了任务配置中的一些关键细节，如输出处理和特殊字符转义。同时，我们通过实战演练，学会了在系统无法正常登录时，如何通过GRUB引导进入救援模式，并成功重置root密码。这两个技能对于系统管理和故障排查至关重要。