# 红帽RHCE7培训课程：第13章：系统引导过程与故障排除 🚀

![](img/9db9da51e7347433671cebb0e1b06c9c_0.png)

在本章中，我们将学习Linux系统的引导过程。理解这个过程对于系统排错至关重要。当系统无法启动时，了解引导流程能帮助我们快速定位问题所在，并知道如何进行修复。

## 系统引导流程概述

上一节我们介绍了学习引导过程的重要性，本节中我们来看看系统启动的具体步骤。

系统启动时，首先从主机的BIOS开始。BIOS中可以设定从哪个设备启动，例如硬盘。如果从硬盘启动，BIOS会找到硬盘上的主引导记录。

MBR中安装的是GRUB引导程序。GRUB在启动时会寻找其配置文件。配置文件位于 `/boot/grub2/grub.cfg`。`/etc/grub2.cfg` 是其软链接。

如果配置文件被意外删除，在GRUB 2版本中，可以使用命令重新生成。

```
grub2-mkconfig
```

![](img/9db9da51e7347433671cebb0e1b06c9c_2.png)

![](img/9db9da51e7347433671cebb0e1b06c9c_4.png)

![](img/9db9da51e7347433671cebb0e1b06c9c_6.png)

用户可以在GRUB菜单出现时做出选择。如果不选择，系统会使用默认菜单倒计时启动。

![](img/9db9da51e7347433671cebb0e1b06c9c_8.png)

系统启动过程中会加载内核和内存文件系统，然后加载配置文件。之后，控制权会从内存传递回硬盘。

![](img/9db9da51e7347433671cebb0e1b06c9c_10.png)

![](img/9db9da51e7347433671cebb0e1b06c9c_12.png)

在内存文件系统阶段中断，可以进入单用户模式。这是通过在内核参数中添加 `rd.break` 实现的。

![](img/9db9da51e7347433671cebb0e1b06c9c_14.png)

![](img/9db9da51e7347433671cebb0e1b06c9c_15.png)

系统继续启动后，第一个运行的进程是 `systemd`。在RHEL 7之前，这个进程是 `init`。

系统会挂载根文件系统，然后根据 `/etc/fstab` 挂载其他文件系统，最后启动网络服务，最终出现登录界面。

## 关键配置文件与命令

上一节我们梳理了引导流程，本节中我们来看看其中涉及的关键文件和命令。

系统中有两个重要的目标文件：`default.target` 和 `graphical.target`。命令 `systemctl set-default multi-user.target` 设置默认进入字符界面，`systemctl set-default graphical.target` 设置默认进入图形界面。这些命令实际上创建了指向目标文件的软链接。

系统重启和关机命令如下：
*   `reboot` 或 `systemctl reboot`：重启系统。
*   `poweroff` 或 `systemctl poweroff`：关闭系统。
*   旧命令 `init 0`（关机）和 `init 6`（重启）在RHEL 7中依然可用，保持向下兼容。

系统中有两个主要目标：
*   `graphical.target`：图形界面。
*   `multi-user.target`：多用户字符界面。

![](img/9db9da51e7347433671cebb0e1b06c9c_17.png)

使用 `systemctl isolate` 命令可以临时切换目标模式。

![](img/9db9da51e7347433671cebb0e1b06c9c_19.png)

查看和设置默认启动模式的命令：
*   `systemctl get-default`：查看当前默认目标。
*   `systemctl set-default [目标名]`：设置默认目标。

![](img/9db9da51e7347433671cebb0e1b06c9c_21.png)

![](img/9db9da51e7347433671cebb0e1b06c9c_23.png)

## 单用户模式与密码重置 🔑

上一节我们介绍了系统目标和相关命令，本节中我们来看看一个重要的排错场景：使用单用户模式重置root密码。

![](img/9db9da51e7347433671cebb0e1b06c9c_25.png)

![](img/9db9da51e7347433671cebb0e1b06c9c_27.png)

系统启动出现GRUB菜单时，按任意键（除回车键）可以打断自动启动。移动光标到要编辑的启动项，按 `e` 进入编辑模式。

![](img/9db9da51e7347433671cebb0e1b06c9c_29.png)

![](img/9db9da51e7347433671cebb0e1b06c9c_31.png)

![](img/9db9da51e7347433671cebb0e1b06c9c_32.png)

找到以 `linux16` 开头的内核参数行，按 `Ctrl+E` 跳至行尾。添加参数 `rd.break`。有时为了确保成功，还会添加 `console=tty0`。修改完成后，按 `Ctrl+X` 使用修改后的参数启动。

系统会启动到单用户模式。此时根文件系统 `/sysroot` 通常以只读权限挂载。需要重新挂载为读写权限才能修改文件。

```
mount -o remount,rw /sysroot
```

然后切换到硬盘的根文件系统，因为密码文件存储在硬盘上。

![](img/9db9da51e7347433671cebb0e1b06c9c_34.png)

```
chroot /sysroot
```

现在可以修改root用户密码。建议使用以下命令，避免输入两次密码。

![](img/9db9da51e7347433671cebb0e1b06c9c_36.png)

![](img/9db9da51e7347433671cebb0e1b06c9c_38.png)

```
echo "新密码" | passwd --stdin root
```

修改密码后，在根目录下创建一个 `.autorelabel` 文件。这告诉系统在下次启动时恢复所有文件的SELinux安全上下文。

```
touch /.autorelabel
```

![](img/9db9da51e7347433671cebb0e1b06c9c_40.png)

执行 `sync` 命令确保数据写入磁盘，然后连续执行两次 `exit` 命令：第一次退出 `chroot` 环境，第二次退出单用户模式并重启系统。

![](img/9db9da51e7347433671cebb0e1b06c9c_42.png)

![](img/9db9da51e7347433671cebb0e1b06c9c_44.png)

系统重启时会重新标记SELinux上下文，完成后即可使用新密码登录。

![](img/9db9da51e7347433671cebb0e1b06c9c_46.png)

## 修复损坏的 `/etc/fstab` 文件 🛠️

![](img/9db9da51e7347433671cebb0e1b06c9c_48.png)

上一节我们学习了如何使用单用户模式重置密码，本节中我们来看看另一个常见故障：`/etc/fstab` 文件损坏。

![](img/9db9da51e7347433671cebb0e1b06c9c_50.png)

如果 `/etc/fstab` 文件内容错误或为空，系统在启动时无法找到或正确挂载文件系统，会导致启动失败，屏幕上可能出现大量错误信息。

![](img/9db9da51e7347433671cebb0e1b06c9c_52.png)

![](img/9db9da51e7347433671cebb0e1b06c9c_54.png)

解决方法同样是进入单用户模式。启动流程与重置密码相同：打断GRUB菜单，编辑内核参数，添加 `rd.break` 后启动。

![](img/9db9da51e7347433671cebb0e1b06c9c_56.png)

![](img/9db9da51e7347433671cebb0e1b06c9c_58.png)

![](img/9db9da51e7347433671cebb0e1b06c9c_60.png)

进入单用户模式并重新挂载 `/sysroot` 为读写权限后，切换到硬盘根目录。

![](img/9db9da51e7347433671cebb0e1b06c9c_62.png)

![](img/9db9da51e7347433671cebb0e1b06c9c_64.png)

![](img/9db9da51e7347433671cebb0e1b06c9c_66.png)

```
chroot /sysroot
```

![](img/9db9da51e7347433671cebb0e1b06c9c_68.png)

![](img/9db9da51e7347433671cebb0e1b06c9c_69.png)

![](img/9db9da51e7347433671cebb0e1b06c9c_70.png)

![](img/9db9da51e7347433671cebb0e1b06c9c_71.png)

编辑 `/etc/fstab` 文件，修正其中的错误。例如，对于网络文件系统，可能需要添加 `_netdev` 选项，表示该设备需要网络服务启动后才能挂载。

![](img/9db9da51e7347433671cebb0e1b06c9c_73.png)

![](img/9db9da51e7347433671cebb0e1b06c9c_75.png)

修正文件后，同样创建 `.autorelabel` 文件并执行 `sync`，然后退出重启。

```
touch /.autorelabel
sync
exit
exit
```

![](img/9db9da51e7347433671cebb0e1b06c9c_77.png)

![](img/9db9da51e7347433671cebb0e1b06c9c_79.png)

## 救援模式与GRUB修复 🆘

![](img/9db9da51e7347433671cebb0e1b06c9c_81.png)

![](img/9db9da51e7347433671cebb0e1b06c9c_83.png)

![](img/9db9da51e7347433671cebb0e1b06c9c_85.png)

![](img/9db9da51e7347433671cebb0e1b06c9c_87.png)

上一节我们修复了文件系统配置，本节中我们来看看更严重的故障：GRUB配置文件或程序本身损坏，以及如何使用救援模式修复。

![](img/9db9da51e7347433671cebb0e1b06c9c_89.png)

![](img/9db9da51e7347433671cebb0e1b06c9c_91.png)

![](img/9db9da51e7347433671cebb0e1b06c9c_93.png)

如果GRUB配置文件丢失，系统启动时会直接进入GRUB命令行，无法显示启动菜单。此时需要使用救援模式。

![](img/9db9da51e7347433671cebb0e1b06c9c_95.png)

![](img/9db9da51e7347433671cebb0e1b06c9c_97.png)

![](img/9db9da51e7347433671cebb0e1b06c9c_99.png)

救援模式类似于Windows的PE环境，使用外部介质引导系统。可以通过网络、光盘或U盘启动。假设我们使用网络救援，在启动时选择从网络启动，进入安装界面后选择 `Troubleshooting` -> `Rescue a Red Hat Enterprise Linux system`。

![](img/9db9da51e7347433671cebb0e1b06c9c_101.png)

![](img/9db9da51e7347433671cebb0e1b06c9c_103.png)

救援程序会尝试查找并挂载已安装的系统到 `/mnt/sysimage` 目录。选择 `Continue` 以读写权限挂载，然后选择 `OK` 获得shell。

![](img/9db9da51e7347433671cebb0e1b06c9c_105.png)

![](img/9db9da51e7347433671cebb0e1b06c9c_107.png)

![](img/9db9da51e7347433671cebb0e1b06c9c_109.png)

在救援模式的shell中，切换到已挂载的硬盘系统根目录。

```
chroot /mnt/sysimage
```

![](img/9db9da51e7347433671cebb0e1b06c9c_111.png)

现在可以重新生成GRUB配置文件。

```
grub2-mkconfig > /boot/grub2/grub.cfg
```

执行 `sync` 后，退出 `chroot` 环境并重启，系统应能正常启动。

![](img/9db9da51e7347433671cebb0e1b06c9c_113.png)

![](img/9db9da51e7347433671cebb0e1b06c9c_115.png)

![](img/9db9da51e7347433671cebb0e1b06c9c_116.png)

![](img/9db9da51e7347433671cebb0e1b06c9c_118.png)

如果MBR中的GRUB程序本身被破坏，现象可能是启动时只有一个闪烁的光标。修复方法同样是进入救援模式。

![](img/9db9da51e7347433671cebb0e1b06c9c_120.png)

![](img/9db9da51e7347433671cebb0e1b06c9c_122.png)

![](img/9db9da51e7347433671cebb0e1b06c9c_123.png)

![](img/9db9da51e7347433671cebb0e1b06c9c_125.png)

在救援模式中切换到硬盘根目录后，执行以下命令重新安装GRUB到硬盘。

![](img/9db9da51e7347433671cebb0e1b06c9c_127.png)

```
grub2-install /dev/vda
```

![](img/9db9da51e7347433671cebb0e1b06c9c_129.png)

建议执行两次以确保成功。完成后退出并重启。

![](img/9db9da51e7347433671cebb0e1b06c9c_131.png)

![](img/9db9da51e7347433671cebb0e1b06c9c_133.png)

![](img/9db9da51e7347433671cebb0e1b06c9c_135.png)

![](img/9db9da51e7347433671cebb0e1b06c9c_137.png)

## 防火墙管理 🔥

上一节我们探讨了系统引导的深度修复，本节中我们回到运行中的系统，学习防火墙管理。

Linux内核中包含网络过滤框架 `netfilter`。`firewalld` 和 `iptables` 是管理 `netfilter` 的前端工具。`firewalld` 支持动态更新，并管理IPv4和IPv6规则。

配置 `firewalld` 有两种主要方式：命令行和图形界面。配置思路通常分四步：
1.  指定配置为永久生效（`--permanent`）。
2.  添加或移除规则（如服务、端口）。
3.  重新加载配置使规则立即生效（`--reload`）。
4.  查看运行时规则以确认。

![](img/9db9da51e7347433671cebb0e1b06c9c_139.png)

![](img/9db9da51e7347433671cebb0e1b06c9c_140.png)

以下是使用命令行管理防火墙的示例：

添加HTTP服务（永久生效并立即重载）：
```
firewall-cmd --permanent --add-service=http
firewall-cmd --reload
```

查看当前生效的规则：
```
firewall-cmd --list-all
```

如果服务未预定义，则需要添加端口：
```
firewall-cmd --permanent --add-port=8080/tcp
firewall-cmd --reload
```

![](img/9db9da51e7347433671cebb0e1b06c9c_142.png)

也可以使用图形化工具 `firewall-config` 进行配置，其操作步骤与命令行逻辑一致。

## 本章总结

![](img/9db9da51e7347433671cebb0e1b06c9c_144.png)

在本章中，我们一起学习了Linux系统的完整引导流程，从BIOS到GRUB，再到内核加载和 `systemd` 初始化。我们重点掌握了两个至关重要的排错技能：使用**单用户模式重置root密码**和修复损坏的 `/etc/fstab` 文件。此外，我们还了解了在GRUB严重损坏时如何使用**救援模式**进行修复。最后，我们学习了运行系统中如何使用 `firewalld` 管理防火墙规则。理解这些知识点，对于维护系统稳定和快速排除启动故障具有重要意义。