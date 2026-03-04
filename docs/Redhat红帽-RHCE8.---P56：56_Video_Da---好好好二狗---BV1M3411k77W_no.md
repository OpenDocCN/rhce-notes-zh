# Redhat红帽 RHCE8.0认证体系课程：P56：软件包与仓库管理模块 🛠️

![](img/9b5d5218b3c1d100ebb462c76a80ba34_1.png)

在本节课中，我们将学习Ansible中两个非常重要的模块：`yum_repository`（软件仓库模块）和`yum`（软件包模块）。我们将了解如何使用它们自动化地配置软件仓库、安装、卸载和更新软件包。

## 软件仓库模块（yum_repository）🏗️

上一节我们介绍了Ansible临时命令的基础，本节中我们来看看如何管理软件仓库。软件仓库模块的主要作用是创建或管理YUM/DNF软件仓库。使用它的前提是软件仓库的源（如ISO镜像或网络源）必须存在。

在考试中，使用自动化方法配置软件仓库是常见的考点。手动方法是在`/etc/yum.repos.d/`目录下创建`.repo`文件，而Ansible可以让我们批量、自动化地完成这项工作。

以下是`yum_repository`模块的常用参数，它们与手动编写的`.repo`文件内容非常相似：

![](img/9b5d5218b3c1d100ebb462c76a80ba34_3.png)

*   **`name`**: 软件仓库的名称（对应`.repo`文件中的`[repository_id]`）。
*   **`baseurl`**: 软件仓库的地址。
*   **`description`**: 软件仓库的描述信息。
*   **`enabled`**: 是否启用该仓库（1为启用，0为禁用）。
*   **`file`**: 保存配置的文件名（通常为`xxx.repo`，`xxx`部分可自定义）。
*   **`gpgcheck`**: 是否进行GPG完整性校验（1为检查，0为不检查）。
*   **`gpgkey`**: 提供完整性校验的GPG密钥文件路径（通常当`gpgcheck=1`时才需要）。

![](img/9b5d5218b3c1d100ebb462c76a80ba34_5.png)

![](img/9b5d5218b3c1d100ebb462c76a80ba34_7.png)

### 配置本地软件仓库示例

![](img/9b5d5218b3c1d100ebb462c76a80ba34_9.png)

![](img/9b5d5218b3c1d100ebb462c76a80ba34_11.png)

我们以在受管主机`node1.lab.example.com`上挂载本地ISO镜像并配置仓库为例。在红帽8中，通常需要配置`BaseOS`和`AppStream`两个仓库。

![](img/9b5d5218b3c1d100ebb462c76a80ba34_13.png)

首先，创建一个挂载点目录：

```bash
ansible node1.lab.example.com -m file -a “path=/mnt/iso state=directory”
```

![](img/9b5d5218b3c1d100ebb462c76a80ba34_15.png)

![](img/9b5d5218b3c1d100ebb462c76a80ba34_17.png)

接着，使用`mount`模块挂载ISO镜像。`mount`模块的关键参数包括`src`（源设备）、`path`（挂载点）、`fstype`（文件系统类型）和`state`（状态）。

![](img/9b5d5218b3c1d100ebb462c76a80ba34_19.png)

![](img/9b5d5218b3c1d100ebb462c76a80ba34_21.png)

```bash
ansible node1.lab.example.com -m mount -a “src=/dev/sr0 path=/mnt/iso fstype=iso9660 state=mounted”
```

![](img/9b5d5218b3c1d100ebb462c76a80ba34_23.png)

挂载成功后，即可使用`yum_repository`模块创建仓库配置。

![](img/9b5d5218b3c1d100ebb462c76a80ba34_25.png)

配置`BaseOS`仓库：

![](img/9b5d5218b3c1d100ebb462c76a80ba34_27.png)

![](img/9b5d5218b3c1d100ebb462c76a80ba34_29.png)

![](img/9b5d5218b3c1d100ebb462c76a80ba34_31.png)

```bash
ansible node1.lab.example.com -m yum_repository -a “name=HEL8-BaseOS description=‘BaseOS’ baseurl=file:///mnt/iso/BaseOS gpgcheck=0 enabled=1 state=present”
```

![](img/9b5d5218b3c1d100ebb462c76a80ba34_33.png)

![](img/9b5d5218b3c1d100ebb462c76a80ba34_35.png)

配置`AppStream`仓库：

![](img/9b5d5218b3c1d100ebb462c76a80ba34_37.png)

![](img/9b5d5218b3c1d100ebb462c76a80ba34_39.png)

```bash
ansible node1.lab.example.com -m yum_repository -a “name=HEL8-AppStream description=‘AppStream’ baseurl=file:///mnt/iso/AppStream gpgcheck=0 enabled=1 state=present”
```

![](img/9b5d5218b3c1d100ebb462c76a80ba34_41.png)

配置完成后，可以通过查看文件或使用`yum`模块来验证：

![](img/9b5d5218b3c1d100ebb462c76a80ba34_43.png)

```bash
# 方法一：查看配置文件
ansible node1.lab.example.com -m shell -a “cat /etc/yum.repos.d/HEL8-*.repo”

![](img/9b5d5218b3c1d100ebb462c76a80ba34_45.png)

![](img/9b5d5218b3c1d100ebb462c76a80ba34_46.png)

# 方法二：使用yum模块列出已启用的仓库（更规范）
ansible node1.lab.example.com -m yum -a “list=repos”
```

![](img/9b5d5218b3c1d100ebb462c76a80ba34_48.png)

## 软件包模块（yum）📦

配置好软件仓库后，我们就可以使用`yum`模块来管理软件包了。它的主要作用是安装、卸载、更新软件包或软件包组。

![](img/9b5d5218b3c1d100ebb462c76a80ba34_50.png)

![](img/9b5d5218b3c1d100ebb462c76a80ba34_52.png)

![](img/9b5d5218b3c1d100ebb462c76a80ba34_54.png)

`yum`模块的核心参数如下：

![](img/9b5d5218b3c1d100ebb462c76a80ba34_56.png)

![](img/9b5d5218b3c1d100ebb462c76a80ba34_58.png)

![](img/9b5d5218b3c1d100ebb462c76a80ba34_60.png)

*   **`name`**: 指定软件包或软件包组的名称。
*   **`state`**: 指定期望的状态。
    *   `present` 或 `installed`: 确保软件包已安装（默认）。
    *   `absent` 或 `removed`: 确保软件包已被卸载。
    *   `latest`: 确保软件包更新到最新版本。

![](img/9b5d5218b3c1d100ebb462c76a80ba34_62.png)

![](img/9b5d5218b3c1d100ebb462c76a80ba34_64.png)

### 软件包管理操作示例

![](img/9b5d5218b3c1d100ebb462c76a80ba34_65.png)

![](img/9b5d5218b3c1d100ebb462c76a80ba34_67.png)

以下操作均在受管主机`node1.lab.example.com`上执行。

![](img/9b5d5218b3c1d100ebb462c76a80ba34_69.png)

![](img/9b5d5218b3c1d100ebb462c76a80ba34_71.png)

**1. 安装软件包**
安装`httpd`服务：

```bash
ansible node1.lab.example.com -m yum -a “name=httpd state=present”
```

![](img/9b5d5218b3c1d100ebb462c76a80ba34_73.png)

**2. 卸载软件包**
卸载刚才安装的`httpd`服务：

```bash
ansible node1.lab.example.com -m yum -a “name=httpd state=absent”
```

**3. 安装软件包组**
安装`MariaDB`数据库服务器组（注意组名前加`@`并用引号括起）：

![](img/9b5d5218b3c1d100ebb462c76a80ba34_75.png)

```bash
ansible node1.lab.example.com -m yum -a “name=‘@mariadb’ state=present”
```

![](img/9b5d5218b3c1d100ebb462c76a80ba34_77.png)

**4. 更新软件包**
将所有已安装的软件包更新到最新版本（使用通配符`*`）：

```bash
ansible node1.lab.example.com -m yum -a “name=* state=latest”
```

## 总结 📝

本节课中我们一起学习了Ansible中管理软件仓库和软件包的两个核心模块。

![](img/9b5d5218b3c1d100ebb462c76a80ba34_79.png)

*   我们首先学习了 **`yum_repository`模块**，了解了其关键参数，并通过示例演示了如何自动化挂载ISO镜像并配置本地YUM仓库。这是实现后续软件安装自动化的基础。
*   接着，我们深入探讨了 **`yum`模块**，掌握了使用它进行软件包安装（`present`）、卸载（`absent`）、组安装（`@group_name`）和批量更新（`latest`）的方法。

通过这两个模块的组合使用，我们可以轻松地在多台受管主机上实现软件环境的快速、一致部署，这正是Ansible自动化运维的威力所在。请务必在实验环境中多加练习，特别是完成在`node2`上配置仓库并安装软件的任务。