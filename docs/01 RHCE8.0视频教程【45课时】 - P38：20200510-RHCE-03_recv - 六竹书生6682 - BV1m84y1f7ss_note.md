# RHCE8.0视频教程：P38：Ansible模块进阶 - YUM与服务管理 📦

![](img/aeee1ddae929234864dbbec01a660e9c_1.png)

![](img/aeee1ddae929234864dbbec01a660e9c_3.png)

![](img/aeee1ddae929234864dbbec01a660e9c_5.png)

在本节课中，我们将学习Ansible中两个重要的模块：`yum`模块和`service`模块。我们将了解如何使用`yum`模块在目标主机上管理软件包，以及如何使用`service`模块管理系统服务的状态。课程内容将结合具体操作步骤，帮助初学者掌握这些核心模块的用法。

![](img/aeee1ddae929234864dbbec01a660e9c_7.png)

---

![](img/aeee1ddae929234864dbbec01a660e9c_9.png)

## 一、YUM模块：软件包管理

![](img/aeee1ddae929234864dbbec01a660e9c_11.png)

![](img/aeee1ddae929234864dbbec01a660e9c_13.png)

上一节我们介绍了Ansible的基础模块，本节中我们来看看如何使用`yum`模块进行软件包管理。`yum`模块用于在基于RPM的Linux系统（如Red Hat、CentOS）上安装、升级、降级、移除和列出软件包。

### 1.1 准备工作：配置YUM源

在使用`yum`模块前，需要确保目标主机已正确配置YUM源。如果主机没有挂载安装光盘或配置本地仓库，我们需要先完成这些步骤。

![](img/aeee1ddae929234864dbbec01a660e9c_15.png)

![](img/aeee1ddae929234864dbbec01a660e9c_17.png)

![](img/aeee1ddae929234864dbbec01a660e9c_19.png)

以下是配置YUM源的基本流程：

![](img/aeee1ddae929234864dbbec01a660e9c_20.png)

![](img/aeee1ddae929234864dbbec01a660e9c_22.png)

1.  **创建挂载目录**：在目标主机上创建一个目录，用于挂载ISO镜像。
2.  **挂载光盘**：将安装光盘或ISO镜像挂载到刚创建的目录。
3.  **创建YUM仓库文件**：在本地编写一个`.repo`文件，定义仓库信息。
4.  **复制仓库文件**：将本地的`.repo`文件复制到目标主机的`/etc/yum.repos.d/`目录下。
5.  **验证配置**：检查YUM源是否配置成功。

![](img/aeee1ddae929234864dbbec01a660e9c_24.png)

![](img/aeee1ddae929234864dbbec01a660e9c_26.png)

接下来，我们通过Ansible命令逐步执行这些操作。

![](img/aeee1ddae929234864dbbec01a660e9c_28.png)

![](img/aeee1ddae929234864dbbec01a660e9c_30.png)

![](img/aeee1ddae929234864dbbec01a660e9c_31.png)

![](img/aeee1ddae929234864dbbec01a660e9c_33.png)

**第一步：创建挂载目录**
我们使用`shell`模块在目标主机`web1`上创建一个名为`/sos`的目录。
```bash
ansible web1 -m shell -a “mkdir /sos”
```

![](img/aeee1ddae929234864dbbec01a660e9c_35.png)

![](img/aeee1ddae929234864dbbec01a660e9c_37.png)

**第二步：挂载光盘**
使用`mount`命令将光盘（例如`/dev/sr0`）挂载到`/sos`目录。
```bash
ansible web1 -m shell -a “mount /dev/sr0 /sos”
```

![](img/aeee1ddae929234864dbbec01a660e9c_39.png)

![](img/aeee1ddae929234864dbbec01a660e9c_41.png)

**第三步：验证挂载**
使用`df -h`命令检查光盘是否成功挂载。
```bash
ansible web1 -m shell -a “df -h | grep sr0”
```

**第四步：创建本地YUM仓库文件**
在Ansible控制节点上，创建一个名为`local.repo`的文件，内容如下：
```ini
[BaseOS]
name=BaseOS
baseurl=file:///sos/BaseOS
enabled=1
gpgcheck=0

![](img/aeee1ddae929234864dbbec01a660e9c_43.png)

[AppStream]
name=AppStream
baseurl=file:///sos/AppStream
enabled=1
gpgcheck=0
```

![](img/aeee1ddae929234864dbbec01a660e9c_45.png)

![](img/aeee1ddae929234864dbbec01a660e9c_47.png)

**第五步：复制仓库文件到目标主机**
使用`copy`模块将本地`local.repo`文件复制到目标主机的`/etc/yum.repos.d/`目录。
```bash
ansible web1 -m copy -a “src=./local.repo dest=/etc/yum.repos.d/”
```

![](img/aeee1ddae929234864dbbec01a660e9c_49.png)

![](img/aeee1ddae929234864dbbec01a660e9c_51.png)

![](img/aeee1ddae929234864dbbec01a660e9c_53.png)

![](img/aeee1ddae929234864dbbec01a660e9c_54.png)

**第六步：验证YUM源**
在目标主机上运行`yum repolist`命令，检查仓库是否可用且软件包数量不为零。
```bash
ansible web1 -m shell -a “yum repolist”
```

![](img/aeee1ddae929234864dbbec01a660e9c_56.png)

### 1.2 使用YUM模块管理软件包

![](img/aeee1ddae929234864dbbec01a660e9c_58.png)

完成YUM源配置后，就可以使用`yum`模块来管理软件包了。以下是`yum`模块的常用参数：

![](img/aeee1ddae929234864dbbec01a660e9c_60.png)

![](img/aeee1ddae929234864dbbec01a660e9c_62.png)

*   **`name`**: 指定软件包名称。可以是单个包名，也可以是包含多个包名的列表。
*   **`state`**: 定义软件包的目标状态。
    *   `present` 或 `installed`: 确保软件包已安装（默认状态）。
    *   `latest`: 确保软件包升级到最新版本。
    *   `absent` 或 `removed`: 确保软件包已被移除。

**查看已安装的软件包**
使用`list`参数可以列出已安装的软件包，或检查特定软件包的状态。
```bash
ansible web1 -m yum -a “list=installed”
# 或检查特定软件包
ansible web1 -m shell -a “yum list installed vsftpd”
```

![](img/aeee1ddae929234864dbbec01a660e9c_64.png)

![](img/aeee1ddae929234864dbbec01a660e9c_66.png)

**安装软件包**
使用`state=present`（可省略）来安装一个软件包，例如安装`vsftpd`。
```bash
ansible web1 -m yum -a “name=vsftpd state=present”
```

![](img/aeee1ddae929234864dbbec01a660e9c_68.png)

![](img/aeee1ddae929234864dbbec01a660e9c_70.png)

**移除软件包**
使用`state=absent`来移除一个已安装的软件包。
```bash
ansible web1 -m yum -a “name=vsftpd state=absent”
```

![](img/aeee1ddae929234864dbbec01a660e9c_72.png)

**安装本地RPM包**
如果需要安装一个不在YUM源中的本地RPM包，需要先将RPM文件复制到目标主机，然后使用`yum`模块指定本地文件路径进行安装。

![](img/aeee1ddae929234864dbbec01a660e9c_74.png)

![](img/aeee1ddae929234864dbbec01a660e9c_76.png)

![](img/aeee1ddae929234864dbbec01a660e9c_78.png)

![](img/aeee1ddae929234864dbbec01a660e9c_80.png)

1.  **复制RPM包**：例如，将本地的`tree-1.7.0-15.el8.x86_64.rpm`复制到目标主机的`/tmp/`目录。
    ```bash
    ansible web1 -m copy -a “src=./tree-1.7.0-15.el8.x86_64.rpm dest=/tmp/”
    ```
2.  **安装本地RPM包**：在`name`参数中指定RPM文件的完整路径。
    ```bash
    ansible web1 -m yum -a “name=/tmp/tree-1.7.0-15.el8.x86_64.rpm state=present”
    ```

![](img/aeee1ddae929234864dbbec01a660e9c_82.png)

![](img/aeee1ddae929234864dbbec01a660e9c_84.png)

**清理YUM缓存**
在安装过程中如果遇到问题，可能需要清理YUM缓存。有两种方法：

![](img/aeee1ddae929234864dbbec01a660e9c_86.png)

![](img/aeee1ddae929234864dbbec01a660e9c_88.png)

1.  使用`shell`模块执行`yum`命令。
    ```bash
    ansible web1 -m shell -a “yum clean all”
    ```
2.  使用`yum`模块的`update_cache`参数。
    ```bash
    ansible web1 -m yum -a “update_cache=yes”
    ```

![](img/aeee1ddae929234864dbbec01a660e9c_90.png)

![](img/aeee1ddae929234864dbbec01a660e9c_92.png)

---

![](img/aeee1ddae929234864dbbec01a660e9c_94.png)

![](img/aeee1ddae929234864dbbec01a660e9c_96.png)

## 二、Service模块：服务管理 🚀

![](img/aeee1ddae929234864dbbec01a660e9c_98.png)

![](img/aeee1ddae929234864dbbec01a660e9c_100.png)

安装好软件后，我们通常需要管理其对应的系统服务。`service`模块用于管理远程主机上的服务，例如启动、停止、重启服务，以及设置开机自启。

![](img/aeee1ddae929234864dbbec01a660e9c_102.png)

![](img/aeee1ddae929234864dbbec01a660e9c_104.png)

### 2.1 Service模块参数详解

![](img/aeee1ddae929234864dbbec01a660e9c_106.png)

以下是`service`模块的核心参数：

![](img/aeee1ddae929234864dbbec01a660e9c_108.png)

![](img/aeee1ddae929234864dbbec01a660e9c_110.png)

*   **`name`**: **必需**。指定要管理的服务名称，如`vsftpd`、`httpd`。
*   **`state`**: 控制服务的**当前运行状态**。
    *   `started`: 启动服务。
    *   `stopped`: 停止服务。
    *   `restarted`: 重启服务。
    *   `reloaded`: 重新加载服务的配置文件（平滑重启）。
*   **`enabled`**: 控制服务是否**开机自动启动**。接受布尔值（`yes`/`no`, `true`/`false`）。
    *   `yes` 或 `true`: 设置开机自启。
    *   `no` 或 `false`: 禁止开机自启。

### 2.2 服务管理实践

![](img/aeee1ddae929234864dbbec01a660e9c_112.png)

![](img/aeee1ddae929234864dbbec01a660e9c_113.png)

![](img/aeee1ddae929234864dbbec01a660e9c_115.png)

假设我们已经在`web1`主机上安装了`vsftpd`软件包，现在来管理其服务。

![](img/aeee1ddae929234864dbbec01a660e9c_117.png)

![](img/aeee1ddae929234864dbbec01a660e9c_119.png)

**第一步：安装VSFTPD**
确保软件包已安装。
```bash
ansible web1 -m yum -a “name=vsftpd”
```

![](img/aeee1ddae929234864dbbec01a660e9c_121.png)

**第二步：启动服务并设置开机自启**
使用`service`模块，设置`state=started`以启动服务，设置`enabled=yes`以开启开机自启。
```bash
ansible web1 -m service -a “name=vsftpd state=started enabled=yes”
```

![](img/aeee1ddae929234864dbbec01a660e9c_123.png)

**第三步：验证服务状态**
在目标主机上验证服务是否已启动并设置为开机自启。
```bash
ansible web1 -m shell -a “systemctl is-active vsftpd”
ansible web1 -m shell -a “systemctl is-enabled vsftpd”
```

![](img/aeee1ddae929234864dbbec01a660e9c_125.png)

![](img/aeee1ddae929234864dbbec01a660e9c_127.png)

![](img/aeee1ddae929234864dbbec01a660e9c_128.png)

![](img/aeee1ddae929234864dbbec01a660e9c_130.png)

![](img/aeee1ddae929234864dbbec01a660e9c_131.png)

**第四步：停止服务（但不改变开机设置）**
可以单独管理服务的运行状态。例如，停止服务但保持开机自启设置不变。
```bash
ansible web1 -m service -a “name=vsftpd state=stopped”
```
执行后，服务状态会变为`inactive`，但`enabled`状态仍为`yes`。

![](img/aeee1ddae929234864dbbec01a660e9c_133.png)

![](img/aeee1ddae929234864dbbec01a660e9c_135.png)

**第五步：禁用开机自启**
单独修改服务的开机自启设置。
```bash
ansible web1 -m service -a “name=vsftpd enabled=no”
```

![](img/aeee1ddae929234864dbbec01a660e9c_137.png)

![](img/aeee1ddae929234864dbbec01a660e9c_139.png)

---

![](img/aeee1ddae929234864dbbec01a660e9c_141.png)

![](img/aeee1ddae929234864dbbec01a660e9c_143.png)

![](img/aeee1ddae929234864dbbec01a660e9c_145.png)

## 三、补充：User与Group模块 👥

![](img/aeee1ddae929234864dbbec01a660e9c_147.png)

![](img/aeee1ddae929234864dbbec01a660e9c_149.png)

除了包和服务，Ansible也提供了管理用户和用户组的模块，它们的使用方式与上述模块类似。

![](img/aeee1ddae929234864dbbec01a660e9c_151.png)

![](img/aeee1ddae929234864dbbec01a660e9c_153.png)

### 3.1 User模块

![](img/aeee1ddae929234864dbbec01a660e9c_155.png)

![](img/aeee1ddae929234864dbbec01a660e9c_157.png)

`user`模块用于管理用户账户。其核心参数包括：

![](img/aeee1ddae929234864dbbec01a660e9c_159.png)

![](img/aeee1ddae929234864dbbec01a660e9c_161.png)

![](img/aeee1ddae929234864dbbec01a660e9c_163.png)

*   **`name`**: 用户名。
*   **`state`**: `present`（创建/修改，默认）或 `absent`（删除）。
*   **`groups`**: 用户所属的附加组列表。
*   **`comment`**: 用户的描述信息（全名）。
*   **`shell`**: 用户的默认shell。
*   **`create_home`**: 是否创建家目录。
*   **`remove`**: 删除用户时，是否同时删除其家目录和邮件池（需与`state=absent`配合使用）。
*   **`system`**: 是否创建为系统用户（`yes`/`no`）。

![](img/aeee1ddae929234864dbbec01a660e9c_165.png)

![](img/aeee1ddae929234864dbbec01a660e9c_167.png)

**创建用户**
创建一个名为`testuser`的普通用户。
```bash
ansible web1 -m user -a “name=testuser”
```

![](img/aeee1ddae929234864dbbec01a660e9c_169.png)

![](img/aeee1ddae929234864dbbec01a660e9c_171.png)

**修改用户属性**
为用户添加附加组（如`wheel`和`root`）并添加注释信息。如果用户已存在，此操作为修改。
```bash
ansible web1 -m user -a “name=testuser groups=wheel,root comment=’Test User’”
```

![](img/aeee1ddae929234864dbbec01a660e9c_173.png)

![](img/aeee1ddae929234864dbbec01a660e9c_175.png)

**删除用户**
删除用户`testuser`，并同时删除其家目录等相关文件。
```bash
ansible web1 -m user -a “name=testuser state=absent remove=yes”
```

![](img/aeee1ddae929234864dbbec01a660e9c_177.png)

![](img/aeee1ddae929234864dbbec01a660e9c_179.png)

### 3.2 Group模块

![](img/aeee1ddae929234864dbbec01a660e9c_181.png)

![](img/aeee1ddae929234864dbbec01a660e9c_183.png)

`group`模块用于管理用户组，参数更为简单。

![](img/aeee1ddae929234864dbbec01a660e9c_185.png)

![](img/aeee1ddae929234864dbbec01a660e9c_187.png)

*   **`name`**: 组名。
*   **`state`**: `present`（创建/修改）或 `absent`（删除）。
*   **`gid`**: 指定组的GID。

![](img/aeee1ddae929234864dbbec01a660e9c_189.png)

![](img/aeee1ddae929234864dbbec01a660e9c_191.png)

**创建组**
创建一个GID为2048的组`group001`。
```bash
ansible web1 -m group -a “name=group001 gid=2048”
```

![](img/aeee1ddae929234864dbbec01a660e9c_193.png)

![](img/aeee1ddae929234864dbbec01a660e9c_195.png)

![](img/aeee1ddae929234864dbbec01a660e9c_197.png)

**删除组**
删除组`group001`。
```bash
ansible web1 -m group -a “name=group001 state=absent”
```

![](img/aeee1ddae929234864dbbec01a660e9c_199.png)

![](img/aeee1ddae929234864dbbec01a660e9c_201.png)

---

![](img/aeee1ddae929234864dbbec01a660e9c_203.png)

![](img/aeee1ddae929234864dbbec01a660e9c_205.png)

![](img/aeee1ddae929234864dbbec01a660e9c_207.png)

## 总结 📝

![](img/aeee1ddae929234864dbbec01a660e9c_209.png)

本节课中我们一起学习了Ansible的几个核心模块：
1.  **`yum`模块**：用于在目标主机上管理RPM软件包，包括安装、卸载、更新以及处理本地RPM文件。
2.  **`service`模块**：用于管理系统服务的状态（启动、停止、重启）和开机自启设置。
3.  **`user`和`group`模块**：用于管理系统的用户和用户组账户。

![](img/aeee1ddae929234864dbbec01a660e9c_211.png)

![](img/aeee1ddae929234864dbbec01a660e9c_213.png)

这些模块是编写Ansible Playbook自动化任务的基础。掌握它们的关键在于理解每个模块的核心参数（如`state`, `name`, `enabled`）及其可能的值。通过组合使用这些模块，我们可以轻松实现复杂的系统配置与管理任务。Ansible拥有超过1000个模块，在后续学习中，我们可以根据实际需求查阅官方文档来学习和使用更多特定功能的模块。