# RHCE8.0认证体系课程：第十章：管理启动过程 🚀

![](img/b431453d9cd9b0d003cde009becd4a61_0.png)

在本章中，我们将学习如何控制和管理Linux系统的启动过程。这包括选择不同的启动目标、重置root密码以及修复常见的启动故障。掌握这些技能对于系统管理员至关重要，尤其是在面对系统无法正常启动或忘记密码的情况时。

![](img/b431453d9cd9b0d003cde009becd4a61_2.png)

---

![](img/b431453d9cd9b0d003cde009becd4a61_4.png)

![](img/b431453d9cd9b0d003cde009becd4a61_6.png)

![](img/b431453d9cd9b0d003cde009becd4a61_8.png)

![](img/b431453d9cd9b0d003cde009becd4a61_9.png)

![](img/b431453d9cd9b0d003cde009becd4a61_11.png)

## 选择启动目标 🎯

![](img/b431453d9cd9b0d003cde009becd4a61_13.png)

![](img/b431453d9cd9b0d003cde009becd4a61_15.png)

上一节我们介绍了本章的学习目标，本节中我们来看看如何选择和切换系统的启动目标。在红帽系统中，主要有四种启动目标：图形界面、文本界面、救援模式和紧急模式。

![](img/b431453d9cd9b0d003cde009becd4a61_17.png)

以下是查看和设置启动目标的常见操作：

![](img/b431453d9cd9b0d003cde009becd4a61_19.png)

![](img/b431453d9cd9b0d003cde009becd4a61_21.png)

*   **查看当前启动目标**：使用命令 `systemctl get-default`。
*   **临时切换启动目标**：使用命令 `systemctl isolate [目标名称].target`。例如，从图形界面切换到文本界面：`systemctl isolate multi-user.target`。
*   **设置默认启动目标**：使用命令 `systemctl set-default [目标名称].target`。

![](img/b431453d9cd9b0d003cde009becd4a61_23.png)

![](img/b431453d9cd9b0d003cde009becd4a61_25.png)

![](img/b431453d9cd9b0d003cde009becd4a61_27.png)

**注意**：紧急模式（emergency.target）通常只在系统出现严重故障时使用，日常操作中不要随意切换到此模式。

![](img/b431453d9cd9b0d003cde009becd4a61_29.png)

![](img/b431453d9cd9b0d003cde009becd4a61_30.png)

![](img/b431453d9cd9b0d003cde009becd4a61_32.png)

如果不慎切换到紧急模式，系统会进入一个只读的根文件系统环境。此时，需要重新挂载根目录为读写模式才能进行修改操作。

![](img/b431453d9cd9b0d003cde009becd4a61_34.png)

![](img/b431453d9cd9b0d003cde009becd4a61_36.png)

![](img/b431453d9cd9b0d003cde009becd4a61_38.png)

![](img/b431453d9cd9b0d003cde009becd4a61_40.png)

```bash
# 在紧急模式下，重新挂载根目录为读写模式
mount -o remount,rw /
# 然后可以切换默认启动目标或进行其他修复
systemctl set-default multi-user.target
# 最后重启系统
reboot
```

![](img/b431453d9cd9b0d003cde009becd4a61_42.png)

![](img/b431453d9cd9b0d003cde009becd4a61_44.png)

![](img/b431453d9cd9b0d003cde009becd4a61_46.png)

---

![](img/b431453d9cd9b0d003cde009becd4a61_47.png)

![](img/b431453d9cd9b0d003cde009becd4a61_49.png)

![](img/b431453d9cd9b0d003cde009becd4a61_51.png)

## 重置Root密码 🔑

![](img/b431453d9cd9b0d003cde009becd4a61_53.png)

在系统管理或考试中，可能会遇到需要重置root密码的情况。如果无法登录系统，可以通过修改内核启动参数进入单用户模式来完成密码重置。

![](img/b431453d9cd9b0d003cde009becd4a61_55.png)

![](img/b431453d9cd9b0d003cde009becd4a61_57.png)

以下是重置root密码的步骤：

1.  在系统启动的GRUB菜单界面，按 `e` 键进入内核参数编辑模式。
2.  找到以 `linux` 开头的一行，将光标移动至该行末尾。
3.  删除 `ro` 参数，将其改为 `rw`，并在后面添加 `rd.break` 参数。修改后的行类似：`linux ... rw rd.break`。
4.  按 `Ctrl+X` 使用修改后的参数启动系统。此时会进入一个临时的紧急救援Shell（initramfs）。
5.  执行以下命令切换到真实的根文件系统并修改密码：
    ```bash
    # 重新挂载根目录并切换
    mount -o remount,rw /sysroot
    chroot /sysroot
    # 修改root密码
    passwd root
    # 非常重要：为重打SELinux标签创建标记文件
    touch /.autorelabel
    # 退出chroot环境并重启
    exit
    exit
    ```
6.  系统将自动重启，并在启动过程中重打SELinux标签，之后即可使用新密码登录。

**关键点**：步骤中的 `touch /.autorelabel` 命令至关重要。因为在单用户模式下SELinux是关闭的，如果不创建此标记文件，系统重启后可能因上下文标签不一致而无法正常启动。

---

![](img/b431453d9cd9b0d003cde009becd4a61_59.png)

![](img/b431453d9cd9b0d003cde009becd4a61_60.png)

## 修复启动故障 🛠️

系统启动失败通常与文件系统配置错误有关，例如 `/etc/fstab` 文件中的错误条目会导致系统在启动时挂载失败，从而可能进入紧急模式。

当遇到此类问题时，可以按照以下思路进行排查和修复：

![](img/b431453d9cd9b0d003cde009becd4a61_62.png)

![](img/b431453d9cd9b0d003cde009becd4a61_64.png)

![](img/b431453d9cd9b0d003cde009becd4a61_66.png)

![](img/b431453d9cd9b0d003cde009becd4a61_68.png)

1.  在紧急模式下，将根文件系统重新挂载为读写模式：`mount -o remount,rw /`。
2.  检查并编辑 `/etc/fstab` 文件，注释掉或修正有问题的挂载行。可以使用 `mount -a` 命令测试配置是否正确。
3.  利用系统日志工具（如 `journalctl -xb`）查看从启动开始的详细日志，定位具体的错误信息。
4.  修复问题后，重启系统。

![](img/b431453d9cd9b0d003cde009becd4a61_69.png)

![](img/b431453d9cd9b0d003cde009becd4a61_71.png)

**排错技巧**：如果无法快速定位问题，一个有效的方法是先注释掉 `/etc/fstab` 中可疑的配置行，让系统能够正常启动到命令行，然后再逐步分析和解决具体问题。

![](img/b431453d9cd9b0d003cde009becd4a61_73.png)

---

## 总结 📝

本节课中我们一起学习了Linux系统启动过程的管理。我们掌握了如何查看和切换不同的启动目标，详细演练了在忘记root密码时如何通过单用户模式进行重置，并了解了当 `/etc/fstab` 配置错误导致启动失败时的基本修复方法。这些是RHCE认证考试中非常重要的实操技能，也是日常系统维护工作中必备的能力。