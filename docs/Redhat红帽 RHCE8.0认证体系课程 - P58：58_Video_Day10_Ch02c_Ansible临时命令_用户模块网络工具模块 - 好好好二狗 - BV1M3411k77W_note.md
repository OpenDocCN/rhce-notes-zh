# Redhat红帽 RHCE8.0认证体系课程：P58：Ansible临时命令之用户、系统与网络工具模块

## 概述
在本节课中，我们将学习Ansible临时命令中几个重要的系统管理模块，包括用户管理、服务控制和网络文件下载。这些模块是编写Ansible剧本的基础。

![](img/328f5d88a48e0c52c74ecc13c9bd0c9e_1.png)

---

## 回顾与引入
上一节我们介绍了软件包管理模块。本节中，我们来看看如何使用Ansible临时命令管理用户、系统服务和网络工具。

---

![](img/328f5d88a48e0c52c74ecc13c9bd0c9e_3.png)

## 用户与组管理模块

![](img/328f5d88a48e0c52c74ecc13c9bd0c9e_5.png)

![](img/328f5d88a48e0c52c74ecc13c9bd0c9e_7.png)

### user模块
`user`模块集成了`useradd`、`usermod`和`userdel`命令的功能，用于管理用户账户。

![](img/328f5d88a48e0c52c74ecc13c9bd0c9e_9.png)

以下是`user`模块的常用参数示例：
*   `name`: 指定用户名。
*   `uid`: 指定用户ID。
*   `comment`: 为用户添加描述信息。
*   `generate_ssh_key`: 设置为`yes`可自动为用户生成SSH密钥。
*   `state`: 定义用户状态，`present`为创建或存在，`absent`为删除。

**创建用户示例：**
在节点`node1`上创建一个名为`test_user`，UID为`1010`的用户。
```bash
ansible node1 -m user -a "name=test_user uid=1010 comment='test user' generate_ssh_key=yes state=present"
```
执行后，可使用`id test_user`或`cat /etc/passwd`命令在`node1`上验证用户是否创建成功。

**删除用户示例：**
删除`node1`上的`test_user`用户。
```bash
ansible node1 -m user -a "name=test_user state=absent"
```
此操作仅删除用户记录，不会删除用户的家目录。

![](img/328f5d88a48e0c52c74ecc13c9bd0c9e_11.png)

![](img/328f5d88a48e0c52c74ecc13c9bd0c9e_13.png)

### group模块
`group`模块用于管理用户组。

![](img/328f5d88a48e0c52c74ecc13c9bd0c9e_15.png)

以下是`group`模块的常用参数示例：
*   `name`: 指定组名。
*   `gid`: 指定组ID。
*   `state`: 定义组状态，`present`为创建或存在，`absent`为删除。

**创建用户组示例：**
在节点`node1`上创建一个名为`test_group`，GID为`1010`的组。
```bash
ansible node1 -m group -a "name=test_group gid=1010 state=present"
```
**删除用户组示例：**
删除`node1`上的`test_group`组。
```bash
ansible node1 -m group -a "name=test_group state=absent"
```

![](img/328f5d88a48e0c52c74ecc13c9bd0c9e_17.png)

![](img/328f5d88a48e0c52c74ecc13c9bd0c9e_19.png)

![](img/328f5d88a48e0c52c74ecc13c9bd0c9e_20.png)

---

![](img/328f5d88a48e0c52c74ecc13c9bd0c9e_22.png)

![](img/328f5d88a48e0c52c74ecc13c9bd0c9e_24.png)

![](img/328f5d88a48e0c52c74ecc13c9bd0c9e_25.png)

## 系统服务管理模块

![](img/328f5d88a48e0c52c74ecc13c9bd0c9e_27.png)

![](img/328f5d88a48e0c52c74ecc13c9bd0c9e_29.png)

### service模块
`service`模块用于管理系统服务，其功能类似于`systemctl`命令。

![](img/328f5d88a48e0c52c74ecc13c9bd0c9e_31.png)

![](img/328f5d88a48e0c52c74ecc13c9bd0c9e_33.png)

![](img/328f5d88a48e0c52c74ecc13c9bd0c9e_35.png)

以下是`service`模块的常用参数示例：
*   `name`: 指定服务名称。
*   `state`: 控制服务运行状态，如`started`、`stopped`、`restarted`。
*   `enabled`: 控制服务是否开机自启，`yes`为启用，`no`为禁用。

![](img/328f5d88a48e0c52c74ecc13c9bd0c9e_37.png)

![](img/328f5d88a48e0c52c74ecc13c9bd0c9e_39.png)

**启动并启用服务示例：**
在节点`node1`上启动`httpd`服务，并设置为开机自启。
```bash
ansible node1 -m service -a "name=httpd state=started enabled=yes"
```
如果服务未安装，需先使用`yum`模块安装。其他状态如停止(`stopped`)、禁用(`enabled=no`)等操作类似。

![](img/328f5d88a48e0c52c74ecc13c9bd0c9e_41.png)

![](img/328f5d88a48e0c52c74ecc13c9bd0c9e_43.png)

---

![](img/328f5d88a48e0c52c74ecc13c9bd0c9e_45.png)

![](img/328f5d88a48e0c52c74ecc13c9bd0c9e_47.png)

## 防火墙管理模块

![](img/328f5d88a48e0c52c74ecc13c9bd0c9e_48.png)

### firewalld模块
`firewalld`模块用于管理防火墙规则，其功能类似于`firewall-cmd`命令。

![](img/328f5d88a48e0c52c74ecc13c9bd0c9e_50.png)

以下是`firewalld`模块的常用参数示例：
*   `zone`: 指定防火墙区域，如`public`。
*   `service`: 指定要放行的服务名称。
*   `permanent`: 设置为`yes`使规则永久生效。
*   `immediate`: 设置为`yes`使规则立即生效。
*   `state`: 规则状态，`enabled`为启用规则。
*   `rich_rule`: 用于添加复杂的富规则。

![](img/328f5d88a48e0c52c74ecc13c9bd0c9e_52.png)

**放行服务示例：**
在`node1`的`public`区域永久放行`http`服务。
```bash
ansible node1 -m firewalld -a "zone=public service=http permanent=yes immediate=yes state=enabled"
```

![](img/328f5d88a48e0c52c74ecc13c9bd0c9e_54.png)

![](img/328f5d88a48e0c52c74ecc13c9bd0c9e_55.png)

**添加富规则示例：**
在`node1`上添加一条富规则，允许来自`192.168.145.0/24`网段的`ssh`连接。
```bash
ansible node1 -m firewalld -a "rich_rule='rule family=ipv4 source address=192.168.145.0/24 service name=ssh accept' permanent=yes immediate=yes state=enabled"
```

![](img/328f5d88a48e0c52c74ecc13c9bd0c9e_57.png)

![](img/328f5d88a48e0c52c74ecc13c9bd0c9e_59.png)

---

![](img/328f5d88a48e0c52c74ecc13c9bd0c9e_61.png)

![](img/328f5d88a48e0c52c74ecc13c9bd0c9e_63.png)

## 网络工具模块

![](img/328f5d88a48e0c52c74ecc13c9bd0c9e_65.png)

### get_url模块
`get_url`模块用于从网络下载文件。

![](img/328f5d88a48e0c52c74ecc13c9bd0c9e_67.png)

以下是`get_url`模块的常用参数示例：
*   `url`: 指定要下载文件的URL地址。
*   `dest`: 指定文件下载到本地的保存路径。

![](img/328f5d88a48e0c52c74ecc13c9bd0c9e_69.png)

**下载文件示例：**
从`node1`的Web服务器下载`index.html`文件到`node2`的`student`用户家目录。
1.  首先在`node1`上创建测试文件并确保`httpd`服务运行且防火墙已放行。
    ```bash
    ansible node1 -m copy -a "content='hello\n' dest=/var/www/html/index.html"
    ```
2.  在`node2`上下载该文件。
    ```bash
    ansible node2 -m get_url -a "url=http://node1.example.com/index.html dest=/home/student/"
    ```
> **注意**：此示例假设`node2`能通过主机名`node1.example.com`解析并访问`node1`。在实际操作中，可能需要配置DNS解析或`/etc/hosts`文件。

![](img/328f5d88a48e0c52c74ecc13c9bd0c9e_71.png)

---

![](img/328f5d88a48e0c52c74ecc13c9bd0c9e_73.png)

## 总结
本节课中我们一起学习了Ansible的四个核心系统管理模块：
1.  **user** 和 **group** 模块：用于管理用户和用户组。
2.  **service** 模块：用于控制系统服务的状态和自启配置。
3.  **firewalld** 模块：用于管理防火墙规则和服务放行。
4.  **get_url** 模块：用于从网络下载文件。

![](img/328f5d88a48e0c52c74ecc13c9bd0c9e_75.png)

![](img/328f5d88a48e0c52c74ecc13c9bd0c9e_77.png)

掌握这些临时命令的用法，是后续编写结构化Ansible剧本的重要基础。