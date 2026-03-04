# Linux系统管理：P42：RH133-ULE115-4-4-init-chkconfig-rc

![](img/a5e466b8934a01854d78b19131a955b0_0.png)

![](img/a5e466b8934a01854d78b19131a955b0_2.png)

## 📖 概述
在本节课中，我们将深入学习Linux系统的启动过程，特别是`init`进程、运行级别（runlevel）以及`chkconfig`服务管理工具。我们将了解系统如何从内核加载后，一步步启动用户空间的服务，并掌握如何管理和自定义这些服务。

---

![](img/a5e466b8934a01854d78b19131a955b0_4.png)

## 🧠 系统启动与init进程
上一节我们介绍了内核通过`initrd`加载根文件系统。本节中我们来看看系统启动后如何进入用户空间。

![](img/a5e466b8934a01854d78b19131a955b0_6.png)

![](img/a5e466b8934a01854d78b19131a955b0_8.png)

![](img/a5e466b8934a01854d78b19131a955b0_10.png)

![](img/a5e466b8934a01854d78b19131a955b0_11.png)

内核启动完成后，会启动第一个用户空间进程`/sbin/init`。这个进程是系统的“管家”，负责初始化系统环境，例如加载桌面、输入法、网络服务等。它的主配置文件是`/etc/inittab`。

![](img/a5e466b8934a01854d78b19131a955b0_13.png)

---

## 📋 init配置文件：/etc/inittab
`/etc/inittab`是`init`进程的总蓝图，它定义了系统启动和运行级别的行为。其内容有固定的格式，通常由四个字段组成，用冒号分隔。

![](img/a5e466b8934a01854d78b19131a955b0_15.png)

以下是`/etc/inittab`文件的主要作用：
*   **定义默认运行级别**：例如 `id:5:initdefault:` 表示默认进入图形界面（运行级别5）。
*   **执行系统初始化脚本**：例如 `si::sysinit:/etc/rc.d/rc.sysinit`。
*   **根据运行级别启动/停止服务**：例如 `l3:3:wait:/etc/rc.d/rc 3`。
*   **启动终端**：例如 `1:2345:respawn:/sbin/mingetty tty1`。
*   **启动图形界面**（运行级别5时）：例如 `x:5:respawn:/etc/X11/prefdm -nodaemon`。

**注意**：如果将默认运行级别错误地设置为`0`（关机）或`6`（重启），系统将陷入循环关机或重启。

![](img/a5e466b8934a01854d78b19131a955b0_17.png)

![](img/a5e466b8934a01854d78b19131a955b0_19.png)

![](img/a5e466b8934a01854d78b19131a955b0_21.png)

---

## 🔄 运行级别与服务脚本
运行级别定义了系统在某一时刻应该运行哪些服务。Linux标准运行级别通常有7个（0-6）。

![](img/a5e466b8934a01854d78b19131a955b0_23.png)

当系统进入一个运行级别（例如运行级别3）时，`init`会执行`/etc/rc.d/rc 3`这个脚本。这个脚本会处理`/etc/rc.d/rc3.d/`目录下的所有脚本。

![](img/a5e466b8934a01854d78b19131a955b0_25.png)

`/etc/rc.d/rc3.d/`目录下的文件命名规则如下：
*   **以`S`开头的文件**：表示启动（Start）服务。脚本执行时会带上`start`参数。
*   **以`K`开头的文件**：表示停止（Kill）服务。脚本执行时会带上`stop`参数。
*   **文件名中的数字**：表示启动或停止的顺序，数字小的先执行。

![](img/a5e466b8934a01854d78b19131a955b0_27.png)

![](img/a5e466b8934a01854d78b19131a955b0_29.png)

这些文件实际上都是软链接，指向`/etc/rc.d/init.d/`目录下真正的服务管理脚本。

![](img/a5e466b8934a01854d78b19131a955b0_31.png)

![](img/a5e466b8934a01854d78b19131a955b0_33.png)

![](img/a5e466b8934a01854d78b19131a955b0_35.png)

**示例**：`S55sshd` 链接到 `/etc/rc.d/init.d/sshd`，在进入运行级别3时，系统会执行 `/etc/rc.d/init.d/sshd start` 来启动SSH服务。

---

![](img/a5e466b8934a01854d78b19131a955b0_37.png)

![](img/a5e466b8934a01854d78b19131a955b0_38.png)

## ⚙️ 服务管理工具：chkconfig
手动管理这些软链接非常繁琐，因此我们使用`chkconfig`工具。

`chkconfig`的核心功能是管理`/etc/rc.d/rc[0-6].d/`目录中服务的启动链接。它通过读取服务脚本（位于`/etc/rc.d/init.d/`）开头的特定注释行来获取信息。

一个标准的服务脚本开头包含以下`chkconfig`配置行：
```bash
# chkconfig: 2345 55 45
# description: MyService is a great service.
```
*   **`2345`**：表示在运行级别2、3、4、5下默认启用此服务。
*   **`55`**：表示启动顺序编号（S55）。
*   **`45`**：表示停止顺序编号（K45）。

**常用`chkconfig`命令**：
*   `chkconfig --list [服务名]`：列出服务的启动状态。
*   `chkconfig --add 服务名`：将一个新服务添加到`chkconfig`管理列表中。
*   `chkconfig --del 服务名`：从`chkconfig`管理列表中删除一个服务。
*   `chkconfig [--level 级别] 服务名 on/off`：在指定运行级别开启或关闭服务。

![](img/a5e466b8934a01854d78b19131a955b0_40.png)

**示例**：设置`sshd`在运行级别3和5开启，在运行级别4关闭。
```bash
chkconfig --level 35 sshd on
chkconfig --level 4 sshd off
```

---

## 🛠️ 即时服务控制：service命令
`chkconfig`只管理开机自启，要立即启动或停止一个服务，需要使用`service`命令。

`service`命令会直接调用`/etc/rc.d/init.d/`目录下对应的脚本。

![](img/a5e466b8934a01854d78b19131a955b0_42.png)

![](img/a5e466b8934a01854d78b19131a955b0_44.png)

**`service`命令格式**：
```bash
service 服务名 start|stop|restart|status
```

![](img/a5e466b8934a01854d78b19131a955b0_46.png)

![](img/a5e466b8934a01854d78b19131a955b0_47.png)

**示例**：立即重启SSH服务。
```bash
service sshd restart
```

![](img/a5e466b8934a01854d78b19131a955b0_49.png)

---

![](img/a5e466b8934a01854d78b19131a955b0_51.png)

![](img/a5e466b8934a01854d78b19131a955b0_53.png)

![](img/a5e466b8934a01854d78b19131a955b0_55.png)

## 📝 自定义启动脚本
如果安装了第三方软件（如自己编译的MySQL），如何让它像系统服务一样被管理呢？

**步骤如下**：
1.  **编写服务管理脚本**：在`/etc/rc.d/init.d/`目录下创建一个脚本（例如`myservice`）。这个脚本必须接受`start`、`stop`、`restart`等标准参数。
2.  **添加`chkconfig`配置行**：在脚本开头添加注释行，定义运行级别和启动顺序。
    ```bash
    #!/bin/bash
    # chkconfig: 35 80 20
    # description: My custom service
    ```
3.  **添加可执行权限**：`chmod +x /etc/rc.d/init.d/myservice`
4.  **添加到`chkconfig`管理**：`chkconfig --add myservice`
5.  **设置开机启动**：`chkconfig myservice on`
6.  **现在即可使用**：`service myservice start` 和 `chkconfig` 来管理你的服务了。

![](img/a5e466b8934a01854d78b19131a955b0_57.png)

---

![](img/a5e466b8934a01854d78b19131a955b0_59.png)

![](img/a5e466b8934a01854d78b19131a955b0_61.png)

## 🎯 特殊服务：xinetd
`xinetd`（扩展的互联网服务守护进程）是一个特殊的“超级服务”，它本身是一个常驻服务，但负责管理很多不常用的小服务（如`telnet`, `tftp`）。当有连接请求时，`xinetd`才启动相应的服务进程，用完即关闭，以节省系统资源。

![](img/a5e466b8934a01854d78b19131a955b0_63.png)

![](img/a5e466b8934a01854d78b19131a955b0_65.png)

![](img/a5e466b8934a01854d78b19131a955b0_66.png)

![](img/a5e466b8934a01854d78b19131a955b0_68.png)

![](img/a5e466b8934a01854d78b19131a955b0_69.png)

![](img/a5e466b8934a01854d78b19131a955b0_70.png)

管理`xinetd`下的服务，通常需要两步：
1.  使用`chkconfig`配置子服务：`chkconfig telnet on`
2.  重启`xinetd`服务使配置生效：`service xinetd restart`

![](img/a5e466b8934a01854d78b19131a955b0_72.png)

![](img/a5e466b8934a01854d78b19131a955b0_74.png)

---

## ✨ 总结
本节课中我们一起学习了：
1.  **`init`进程**：它是Linux系统所有进程的父进程，由`/etc/inittab`配置文件控制。
2.  **运行级别**：定义了系统在不同状态下应运行的服务集合。
3.  **服务管理机制**：通过`/etc/rc.d/rcN.d/`目录下的`S`和`K`软链接，在系统启动或切换运行级别时批量启动或停止服务。
4.  **`chkconfig`工具**：用于管理系统服务的开机自启状态，通过修改软链接实现。
5.  **`service`命令**：用于立即控制服务的运行状态（启动、停止、重启等）。
6.  **自定义服务**：通过编写符合规范的脚本并添加`chkconfig`配置行，可以将任何程序注册为系统服务。
7.  **`xinetd`超级服务**：用于按需管理一系列不常用的网络服务。

![](img/a5e466b8934a01854d78b19131a955b0_76.png)

![](img/a5e466b8934a01854d78b19131a955b0_78.png)

理解这套`System V init`体系，是掌握传统Linux系统（如RHEL 6及之前版本）服务管理的核心。