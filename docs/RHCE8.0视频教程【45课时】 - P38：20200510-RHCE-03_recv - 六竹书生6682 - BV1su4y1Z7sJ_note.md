# RHCE8.0视频教程：P38：Ansible模块详解（下）📚

![](img/30c82edde2a6addb26fd642085846cd8_1.png)

![](img/30c82edde2a6addb26fd642085846cd8_3.png)

在本节课中，我们将要学习Ansible中几个非常实用的模块，包括**yum**、**service**、**user**和**group**模块。我们将通过具体的操作示例，掌握如何使用这些模块来管理远程主机的软件包、系统服务以及用户和组。

![](img/30c82edde2a6addb26fd642085846cd8_5.png)

![](img/30c82edde2a6addb26fd642085846cd8_7.png)

上一节我们介绍了Ansible的基础模块，本节中我们来看看如何管理软件包和服务。

![](img/30c82edde2a6addb26fd642085846cd8_9.png)

## 配置Yum源与挂载光盘 💿

![](img/30c82edde2a6addb26fd642085846cd8_11.png)

在开始使用yum模块管理软件包之前，如果目标主机没有配置yum源，我们需要先完成准备工作。这包括挂载光盘和创建yum源配置文件。

![](img/30c82edde2a6addb26fd642085846cd8_13.png)

以下是配置流程：
1.  在目标主机上创建挂载目录。
2.  将光盘挂载到该目录。
3.  验证挂载是否成功。
4.  在本地创建yum源配置文件。
5.  将配置文件复制到目标主机的 `/etc/yum.repos.d/` 目录下。

首先，我们使用shell模块创建挂载目录并挂载光盘。

```bash
ansible web1 -m shell -a "mkdir /iso"
ansible web1 -m shell -a "mount /dev/sr0 /iso"
ansible web1 -m shell -a "df -h | grep sr0"
```

![](img/30c82edde2a6addb26fd642085846cd8_15.png)

![](img/30c82edde2a6addb26fd642085846cd8_17.png)

接着，在本地创建一个yum源配置文件，例如 `local.repo`，内容如下：

![](img/30c82edde2a6addb26fd642085846cd8_19.png)

![](img/30c82edde2a6addb26fd642085846cd8_20.png)

![](img/30c82edde2a6addb26fd642085846cd8_22.png)

```ini
[BaseOS]
name=BaseOS
baseurl=file:///iso/BaseOS
enabled=1
gpgcheck=0

![](img/30c82edde2a6addb26fd642085846cd8_24.png)

[AppStream]
name=AppStream
baseurl=file:///iso/AppStream
enabled=1
gpgcheck=0
```

![](img/30c82edde2a6addb26fd642085846cd8_26.png)

![](img/30c82edde2a6addb26fd642085846cd8_28.png)

![](img/30c82edde2a6addb26fd642085846cd8_30.png)

然后，使用copy模块将此文件传输到目标主机。

![](img/30c82edde2a6addb26fd642085846cd8_31.png)

![](img/30c82edde2a6addb26fd642085846cd8_33.png)

![](img/30c82edde2a6addb26fd642085846cd8_35.png)

```bash
ansible web1 -m copy -a "src=local.repo dest=/etc/yum.repos.d/"
```

![](img/30c82edde2a6addb26fd642085846cd8_37.png)

![](img/30c82edde2a6addb26fd642085846cd8_39.png)

最后，可以验证yum源是否配置成功。

![](img/30c82edde2a6addb26fd642085846cd8_41.png)

```bash
ansible web1 -m shell -a "yum repolist"
```

## 使用Yum模块管理软件包 📦

![](img/30c82edde2a6addb26fd642085846cd8_43.png)

现在，目标主机已经具备了安装软件的条件，我们可以开始使用yum模块。yum模块的核心参数是 `name`（指定软件包名）和 `state`（指定操作状态）。

![](img/30c82edde2a6addb26fd642085846cd8_45.png)

![](img/30c82edde2a6addb26fd642085846cd8_47.png)

以下是常用操作状态：
*   `present` 或 `installed`：安装软件包（默认状态）。
*   `latest`：将软件包升级到最新版本。
*   `absent` 或 `removed`：卸载软件包。

![](img/30c82edde2a6addb26fd642085846cd8_49.png)

![](img/30c82edde2a6addb26fd642085846cd8_51.png)

![](img/30c82edde2a6addb26fd642085846cd8_53.png)

首先，检查一个软件（如vsftpd）是否已安装。

![](img/30c82edde2a6addb26fd642085846cd8_54.png)

![](img/30c82edde2a6addb26fd642085846cd8_56.png)

```bash
ansible web1 -m shell -a “yum list vsftpd”
```

![](img/30c82edde2a6addb26fd642085846cd8_58.png)

使用yum模块安装软件。

![](img/30c82edde2a6addb26fd642085846cd8_60.png)

```bash
ansible web1 -m yum -a “name=vsftpd state=present”
```

![](img/30c82edde2a6addb26fd642085846cd8_62.png)

使用yum模块卸载软件。

```bash
ansible web1 -m yum -a “name=vsftpd state=absent”
```

![](img/30c82edde2a6addb26fd642085846cd8_64.png)

![](img/30c82edde2a6addb26fd642085846cd8_66.png)

### 安装本地RPM包

![](img/30c82edde2a6addb26fd642085846cd8_68.png)

![](img/30c82edde2a6addb26fd642085846cd8_70.png)

有时我们需要安装一个本地存在的RPM包，而不是从yum源获取。这需要两个步骤：先将RPM包复制到目标主机，然后使用yum模块指定本地路径进行安装。

![](img/30c82edde2a6addb26fd642085846cd8_72.png)

以下是操作步骤：
1.  将本地的RPM包复制到目标主机。
2.  使用yum模块，通过指定本地文件路径来安装该包。

![](img/30c82edde2a6addb26fd642085846cd8_74.png)

![](img/30c82edde2a6addb26fd642085846cd8_76.png)

例如，安装本地的 `tree` 软件包：

![](img/30c82edde2a6addb26fd642085846cd8_78.png)

![](img/30c82edde2a6addb26fd642085846cd8_80.png)

![](img/30c82edde2a6addb26fd642085846cd8_82.png)

```bash
# 1. 复制RPM包到目标主机的/tmp目录
ansible web1 -m copy -a “src=./tree-1.7.0-15.el8.x86_64.rpm dest=/tmp/”

![](img/30c82edde2a6addb26fd642085846cd8_84.png)

![](img/30c82edde2a6addb26fd642085846cd8_86.png)

# 2. 验证文件是否已复制
ansible web1 -m shell -a “ls -l /tmp/tree*.rpm”

![](img/30c82edde2a6addb26fd642085846cd8_88.png)

# 3. 安装本地RPM包
ansible web1 -m yum -a “name=/tmp/tree-1.7.0-15.el8.x86_64.rpm state=present”
```

![](img/30c82edde2a6addb26fd642085846cd8_90.png)

![](img/30c82edde2a6addb26fd642085846cd8_92.png)

### 清理Yum缓存

![](img/30c82edde2a6addb26fd642085846cd8_94.png)

![](img/30c82edde2a6addb26fd642085846cd8_96.png)

在安装过程中如果遇到问题，可能需要清理yum缓存。可以使用shell命令，也可以直接使用yum模块的 `update_cache` 参数。

![](img/30c82edde2a6addb26fd642085846cd8_98.png)

![](img/30c82edde2a6addb26fd642085846cd8_100.png)

使用shell模块清理缓存。

![](img/30c82edde2a6addb26fd642085846cd8_102.png)

![](img/30c82edde2a6addb26fd642085846cd8_104.png)

```bash
ansible web1 -m shell -a “yum clean all”
```

使用yum模块清理缓存。

![](img/30c82edde2a6addb26fd642085846cd8_106.png)

```bash
ansible web1 -m yum -a “update_cache=yes”
```

![](img/30c82edde2a6addb26fd642085846cd8_108.png)

![](img/30c82edde2a6addb26fd642085846cd8_110.png)

## 使用Service模块管理系统服务 ⚙️

安装软件后，我们通常需要管理其对应的系统服务。service模块用于管理服务的状态和是否开机自启。其核心参数是 `name`（服务名）、`state`（运行状态）和 `enabled`（是否开机自启）。

![](img/30c82edde2a6addb26fd642085846cd8_112.png)

![](img/30c82edde2a6addb26fd642085846cd8_113.png)

![](img/30c82edde2a6addb26fd642085846cd8_115.png)

![](img/30c82edde2a6addb26fd642085846cd8_117.png)

以下是状态参数说明：
*   `state`:
    *   `started`：启动服务。
    *   `stopped`：停止服务。
    *   `restarted`：重启服务。
    *   `reloaded`：重新加载配置文件（平滑重启）。
*   `enabled`:
    *   `yes` 或 `true`：设置开机自启。
    *   `no` 或 `false`：禁止开机自启。

![](img/30c82edde2a6addb26fd642085846cd8_119.png)

首先，确保vsftpd服务已安装。

![](img/30c82edde2a6addb26fd642085846cd8_121.png)

```bash
ansible web1 -m yum -a “name=vsftpd state=present”
```

![](img/30c82edde2a6addb26fd642085846cd8_123.png)

使用service模块启动vsftpd服务并设置开机自启。

![](img/30c82edde2a6addb26fd642085846cd8_125.png)

![](img/30c82edde2a6addb26fd642085846cd8_127.png)

![](img/30c82edde2a6addb26fd642085846cd8_128.png)

```bash
ansible web1 -m service -a “name=vsftpd state=started enabled=yes”
```

![](img/30c82edde2a6addb26fd642085846cd8_130.png)

![](img/30c82edde2a6addb26fd642085846cd8_131.png)

![](img/30c82edde2a6addb26fd642085846cd8_133.png)

验证服务状态。

![](img/30c82edde2a6addb26fd642085846cd8_135.png)

![](img/30c82edde2a6addb26fd642085846cd8_137.png)

![](img/30c82edde2a6addb26fd642085846cd8_139.png)

```bash
ansible web1 -m shell -a “systemctl is-active vsftpd”
ansible web1 -m shell -a “systemctl is-enabled vsftpd”
```

停止服务（但不改变开机自启设置）。

![](img/30c82edde2a6addb26fd642085846cd8_141.png)

![](img/30c82edde2a6addb26fd642085846cd8_143.png)

![](img/30c82edde2a6addb26fd642085846cd8_145.png)

```bash
ansible web1 -m service -a “name=vsftpd state=stopped”
```

![](img/30c82edde2a6addb26fd642085846cd8_147.png)

![](img/30c82edde2a6addb26fd642085846cd8_149.png)

## 使用User和Group模块管理用户和组 👥

![](img/30c82edde2a6addb26fd642085846cd8_151.png)

![](img/30c82edde2a6addb26fd642085846cd8_153.png)

最后，我们学习如何使用user和group模块来管理系统的用户和组。

![](img/30c82edde2a6addb26fd642085846cd8_155.png)

![](img/30c82edde2a6addb26fd642085846cd8_157.png)

### User模块

![](img/30c82edde2a6addb26fd642085846cd8_159.png)

![](img/30c82edde2a6addb26fd642085846cd8_161.png)

user模块用于创建、修改或删除用户。常用参数包括：
*   `name`：用户名。
*   `state`：`present`（创建/修改）或 `absent`（删除）。
*   `groups`：用户所属的附加组。
*   `comment`：用户全名或描述信息。
*   `create_home`：是否创建家目录。
*   `remove`：删除用户时，是否同时删除家目录等数据。

![](img/30c82edde2a6addb26fd642085846cd8_163.png)

![](img/30c82edde2a6addb26fd642085846cd8_165.png)

![](img/30c82edde2a6addb26fd642085846cd8_167.png)

创建一个新用户。

![](img/30c82edde2a6addb26fd642085846cd8_169.png)

```bash
ansible web1 -m user -a “name=testuser”
```

![](img/30c82edde2a6addb26fd642085846cd8_171.png)

修改现有用户，为其添加附加组并设置全名。

![](img/30c82edde2a6addb26fd642085846cd8_173.png)

![](img/30c82edde2a6addb26fd642085846cd8_175.png)

```bash
ansible web1 -m user -a “name=testuser groups=wheel,root comment=‘Test User‘”
```

![](img/30c82edde2a6addb26fd642085846cd8_177.png)

![](img/30c82edde2a6addb26fd642085846cd8_179.png)

删除用户及其所有相关数据（家目录等）。

![](img/30c82edde2a6addb26fd642085846cd8_181.png)

![](img/30c82edde2a6addb26fd642085846cd8_183.png)

```bash
ansible web1 -m user -a “name=testuser state=absent remove=yes”
```

![](img/30c82edde2a6addb26fd642085846cd8_185.png)

![](img/30c82edde2a6addb26fd642085846cd8_187.png)

### Group模块

![](img/30c82edde2a6addb26fd642085846cd8_189.png)

![](img/30c82edde2a6addb26fd642085846cd8_191.png)

group模块用于管理用户组，参数更为简单，主要是 `name` 和 `state`。

![](img/30c82edde2a6addb26fd642085846cd8_193.png)

![](img/30c82edde2a6addb26fd642085846cd8_195.png)

创建一个新组。

![](img/30c82edde2a6addb26fd642085846cd8_197.png)

![](img/30c82edde2a6addb26fd642085846cd8_199.png)

```bash
ansible web1 -m group -a “name=testgroup gid=2048”
```

![](img/30c82edde2a6addb26fd642085846cd8_201.png)

![](img/30c82edde2a6addb26fd642085846cd8_203.png)

![](img/30c82edde2a6addb26fd642085846cd8_205.png)

删除一个组。

![](img/30c82edde2a6addb26fd642085846cd8_207.png)

```bash
ansible web1 -m group -a “name=testgroup state=absent”
```

![](img/30c82edde2a6addb26fd642085846cd8_209.png)

---

![](img/30c82edde2a6addb26fd642085846cd8_211.png)

![](img/30c82edde2a6addb26fd642085846cd8_213.png)

本节课中我们一起学习了Ansible的四个核心模块。我们掌握了如何使用 **yum模块** 安装、卸载软件包及管理本地RPM包；如何使用 **service模块** 启动、停止服务并设置开机自启；以及如何使用 **user** 和 **group模块** 来管理系统中的用户和组。这些模块是编写自动化运维Playbook的基础，熟练掌握它们对于后续的学习至关重要。