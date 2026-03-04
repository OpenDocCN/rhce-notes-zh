# Linux用户管理：06-2：su与sudo命令详解（第二部分）📖

![](img/18699a5c0bfa2c2b08c255eaa05f471f_1.png)

在本节课中，我们将深入学习 `su` 和 `sudo` 命令的高级配置，包括如何通过别名机制灵活定义权限，以及理解不同切换方式对环境变量的影响。课程内容基于 `/etc/sudoers` 配置文件的定制化展开。

## 配置文件中的别名机制 🔧

上一节我们介绍了 `sudoers` 文件的基本规则。本节中，我们来看看如何通过定义别名来更灵活、更清晰地管理权限。别名主要分为三类：主机别名、用户别名和命令别名。

以下是定义别名的基本语法和示例：

![](img/18699a5c0bfa2c2b08c255eaa05f471f_3.png)

*   **主机别名 (Host_Alias)**：用于定义一组主机。
    ```bash
    Host_Alias LABSERVICE = servera, workstation
    ```

*   **用户别名 (User_Alias)**：用于定义一组用户。
    ```bash
    User_Alias ADMINS = laoma, laoma1, laoma2
    ```

*   **命令别名 (Cmnd_Alias)**：用于定义一组命令。
    ```bash
    Cmnd_Alias SOFTWARE = /usr/bin/yum, /usr/bin/dnf
    Cmnd_Alias LOCATE = /usr/bin/updatedb
    ```

![](img/18699a5c0bfa2c2b08c255eaa05f471f_5.png)

定义别名后，可以在规则中使用它们，使配置更简洁。例如，以下规则允许 `ADMINS` 用户组中的成员在 `LABSERVICE` 主机上以任何用户身份执行 `SOFTWARE` 和 `LOCATE` 命令组中的所有命令：
```bash
ADMINS LABSERVICE = (ALL) SOFTWARE, LOCATE
```

![](img/18699a5c0bfa2c2b08c255eaa05f471f_7.png)

![](img/18699a5c0bfa2c2b08c255eaa05f471f_9.png)

![](img/18699a5c0bfa2c2b08c255eaa05f471f_11.png)

## 配置无需密码执行sudo 🚀

默认情况下，`sudo` 执行命令需要输入当前用户的密码。但在某些自动化场景或高权限操作中，我们可以配置特定规则，使其无需密码。

![](img/18699a5c0bfa2c2b08c255eaa05f471f_13.png)

在 `sudoers` 文件规则中，在命令列表前添加 `NOPASSWD:` 即可实现。例如，以下规则允许 `laoma` 用户无需密码即可执行 `yum` 命令：
```bash
laoma ALL=(root) NOPASSWD: /usr/bin/yum
```

![](img/18699a5c0bfa2c2b08c255eaa05f471f_15.png)

![](img/18699a5c0bfa2c2b08c255eaa05f471f_16.png)

配置完成后，`laoma` 用户执行 `sudo yum install package` 时将不再提示输入密码。

![](img/18699a5c0bfa2c2b08c255eaa05f471f_18.png)

![](img/18699a5c0bfa2c2b08c255eaa05f471f_20.png)

## 包含其他配置文件 📂

为了使主配置文件 `/etc/sudoers` 更清晰，我们可以将额外的规则定义在独立文件中，然后在主文件中包含它们。

`/etc/sudoers` 文件末尾通常包含以下行，这意味着 `/etc/sudoers.d/` 目录下所有文件（排除带 `~` 或 `.` 的文件）都会被读取，其语法与主文件一致：
```bash
#includedir /etc/sudoers.d
```

## sudo命令的常用选项详解 ⚙️

![](img/18699a5c0bfa2c2b08c255eaa05f471f_22.png)

`sudo` 命令本身也提供了丰富的选项来控制其行为。

![](img/18699a5c0bfa2c2b08c255eaa05f471f_24.png)

![](img/18699a5c0bfa2c2b08c255eaa05f471f_25.png)

![](img/18699a5c0bfa2c2b08c255eaa05f471f_27.png)

以下是 `sudo` 命令的一些常用参数：
*   `-u <user>`：以指定用户的身份执行命令。默认为 `root` 用户。
    ```bash
    sudo -u laoma whoami
    ```
*   `-s`：运行指定的 shell。如果不指定，则使用 `$SHELL` 环境变量或 `passwd` 文件中指定的 shell。
*   `--stdin`：从标准输入读取密码，而不是从终端设备读取。这在脚本中结合管道传递密码时非常有用。
    ```bash
    echo “password” | sudo -S command
    ```
*   `-l`：列出当前用户被允许（或禁止）执行的 `sudo` 命令。

![](img/18699a5c0bfa2c2b08c255eaa05f471f_28.png)

![](img/18699a5c0bfa2c2b08c255eaa05f471f_30.png)

## su与sudo的环境变量差异 🔄

![](img/18699a5c0bfa2c2b08c255eaa05f471f_32.png)

![](img/18699a5c0bfa2c2b08c255eaa05f471f_34.png)

理解 `su` 和 `sudo` 切换用户时对环境变量的处理差异至关重要，这会影响命令的执行环境。

![](img/18699a5c0bfa2c2b08c255eaa05f471f_36.png)

![](img/18699a5c0bfa2c2b08c255eaa05f471f_37.png)

我们通过一个练习来观察这种差异。首先以 `student` 用户登录，并记录当前 `$HOME` 和 `$PATH` 环境变量的值。

**使用 `sudo su`（不带 `-`）切换：**
执行 `sudo su` 后，您会以 `root` 身份进入 shell。此时检查环境变量，会发现 `$HOME` 变量变为了 `/root`（root 的家目录），但 `$PATH` 变量可能部分继承了之前 `student` 用户的环境。这是出于安全考虑，`sudo` 会重置关键环境变量。

![](img/18699a5c0bfa2c2b08c255eaa05f471f_39.png)

**使用 `sudo su -`（带 `-`）切换：**
执行 `sudo su -` 后，您会以 `root` 身份登录一个完整的登录会话。此时检查环境变量，会发现 `$HOME` 和 `$PATH` 都完全切换为 `root` 用户的环境。`-` 选项会执行目标用户的登录脚本，模拟一次完整的登录过程。

![](img/18699a5c0bfa2c2b08c255eaa05f471f_41.png)

**核心区别：** 带 `-` 选项会完全初始化目标用户的环境；不带 `-` 则会继承当前 shell 的部分环境变量。在生产环境中，根据是否需要完整的目标用户环境来选择合适的切换方式。

## 实践练习：权限验证 🧪

![](img/18699a5c0bfa2c2b08c255eaa05f471f_43.png)

最后，我们通过一个练习来巩固对 `sudo` 权限的理解。配置允许 `operator` 用户通过 `sudo` 执行文件查看和管理的命令。

例如，在 `sudoers` 文件中添加规则后，`operator` 用户原本无法直接查看 `/root/` 目录下的文件，但使用 `sudo` 前缀后即可获得相应权限：
```bash
# operator 用户无法直接访问
ls /root/secretfile
# 使用 sudo 后获得权限
sudo ls /root/secretfile
sudo cat /root/secretfile
```

![](img/18699a5c0bfa2c2b08c255eaa05f471f_45.png)

这个练习清晰地展示了 `sudo` 如何在不共享 `root` 密码的前提下，赋予特定用户执行特定管理任务的能力。

![](img/18699a5c0bfa2c2b08c255eaa05f471f_47.png)

![](img/18699a5c0bfa2c2b08c255eaa05f471f_49.png)

## 总结 📝

![](img/18699a5c0bfa2c2b08c255eaa05f471f_51.png)

![](img/18699a5c0bfa2c2b08c255eaa05f471f_53.png)

本节课中我们一起学习了 `su` 和 `sudo` 命令的高级配置与应用。我们掌握了如何在 `/etc/sudoers` 文件中使用主机、用户和命令别名来简化权限管理；学会了配置无需密码的 `sudo` 规则；理解了通过包含目录来组织配置文件的方法；探讨了 `sudo` 命令的常用选项；并重点分析了 `su` 与 `sudo` 切换用户时对环境变量处理的差异，这对于理解命令执行上下文至关重要。通过实践练习，我们进一步验证了 `sudo` 权限配置的实际效果。