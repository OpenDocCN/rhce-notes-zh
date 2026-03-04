# Linux运维进阶：P88：MHA高可用配置与视图概念 - 中

![](img/f2e221d5132cab924f71486e5de5a52e_1.png)

![](img/f2e221d5132cab924f71486e5de5a52e_3.png)

![](img/f2e221d5132cab924f71486e5de5a52e_5.png)

![](img/f2e221d5132cab924f71486e5de5a52e_7.png)

## 概述
在本节课程中，我们将继续学习MHA高可用集群的配置，完成最后的关键步骤，包括配置故障切换脚本、测试环境以及启动MHA服务。同时，我们还将初步了解MySQL中“视图”的概念及其基本作用。

上一节我们介绍了MHA配置文件的详细编写，本节中我们来看看如何完成后续的配置与测试。

![](img/f2e221d5132cab924f71486e5de5a52e_9.png)

![](img/f2e221d5132cab924f71486e5de5a52e_11.png)

## MHA配置收尾与测试

![](img/f2e221d5132cab924f71486e5de5a52e_13.png)

![](img/f2e221d5132cab924f71486e5de5a52e_15.png)

![](img/f2e221d5132cab924f71486e5de5a52e_17.png)

配置文件检查无误并保存退出后，我们需要进行最后三步配置来启动MHA。

![](img/f2e221d5132cab924f71486e5de5a52e_19.png)

![](img/f2e221d5132cab924f71486e5de5a52e_21.png)

![](img/f2e221d5132cab924f71486e5de5a52e_23.png)

### 配置故障切换脚本
倒数第三步是配置主动故障切换的脚本。这个脚本的路径在配置文件中已有定义，其核心作用是在主库发生故障时，将虚拟IP（VIP）漂移到新的主库上。

以下是脚本中需要修改的关键部分：
1.  **虚拟IP**：`my $vip = '192.168.0.252/24';` 此IP不能与集群中任何真实IP冲突，它将作为对外提供服务的固定IP。
2.  **网卡名称**：`my $key = “1”;` 和 `my $ssh_start_vip = “/sbin/ifconfig eth0:$key $vip”;` 需要将 `eth0` 修改为你服务器上真实的物理网卡名称（如 `ens33`, `eno1` 等）。

![](img/f2e221d5132cab924f71486e5de5a52e_25.png)

![](img/f2e221d5132cab924f71486e5de5a52e_27.png)

![](img/f2e221d5132cab924f71486e5de5a52e_29.png)

这个脚本利用 `ifconfig` 命令临时绑定IP，其原理与后文将讲到的VRRP协议类似。虚拟IP确保了无论后台哪台数据库成为主库，外部服务始终通过同一个IP进行访问，从而实现无缝切换。

![](img/f2e221d5132cab924f71486e5de5a52e_31.png)

![](img/f2e221d5132cab924f71486e5de5a52e_33.png)

![](img/f2e221d5132cab924f71486e5de5a52e_35.png)

修改完成后，务必为该脚本添加执行权限：
```bash
chmod +x /usr/local/bin/master_ip_failover
```

![](img/f2e221d5132cab924f71486e5de5a52e_37.png)

![](img/f2e221d5132cab924f71486e5de5a52e_38.png)

![](img/f2e221d5132cab924f71486e5de5a52e_40.png)

![](img/f2e221d5132cab924f71486e5de5a52e_42.png)

### 测试MHA运行环境
最后两步是对MHA运行环境进行测试，确保免密登录和主从复制状态均正常。两个测试都必须通过才能成功启动MHA。

![](img/f2e221d5132cab924f71486e5de5a52e_44.png)

![](img/f2e221d5132cab924f71486e5de5a52e_46.png)

![](img/f2e221d5132cab924f71486e5de5a52e_47.png)

以下是需要执行的两个测试命令：

![](img/f2e221d5132cab924f71486e5de5a52e_49.png)

首先，测试SSH免密登录配置：
```bash
masterha_check_ssh --conf=/etc/mha/mha.cnf
```
如果此步骤报错，通常会明确指出哪两台服务器之间的SSH连接有问题，只需重新配置那对服务器的免密登录即可。

![](img/f2e221d5132cab924f71486e5de5a52e_51.png)

![](img/f2e221d5132cab924f71486e5de5a52e_53.png)

![](img/f2e221d5132cab924f71486e5de5a52e_55.png)

其次，测试主从复制状态：
```bash
masterha_check_repl --conf=/etc/mha/mha.cnf
```
这一步检测内容较多，包括数据库连接、复制状态等。常见的错误是MHA节点找不到 `mysqlbinlog` 或 `mysql` 命令。这是因为MHA需要通过软链接来定位这些命令，而环境变量对其可能不生效。

![](img/f2e221d5132cab924f71486e5de5a52e_57.png)

![](img/f2e221d5132cab924f71486e5de5a52e_58.png)

解决方法是在所有数据库节点上创建软链接：
```bash
ln -s /usr/local/mysql/bin/mysql /usr/bin/mysql
ln -s /usr/local/mysql/bin/mysqlbinlog /usr/bin/mysqlbinlog
```
两个测试命令均显示 `OK` 后，表明环境准备就绪。

![](img/f2e221d5132cab924f71486e5de5a52e_60.png)

### 启动MHA服务并模拟故障
环境测试通过后，即可启动MHA管理进程：
```bash
masterha_manager --conf=/etc/mha/mha.cnf
```
启动后，该进程会占用当前终端以实时输出监控日志。此时，可以手动停止原主库（192.168.0.142）的MySQL服务来模拟故障。

![](img/f2e221d5132cab924f71486e5de5a52e_62.png)

![](img/f2e221d5132cab924f71486e5de5a52e_64.png)

![](img/f2e221d5132cab924f71486e5de5a52e_66.png)

观察MHA管理终端，通常在几秒内会看到切换过程日志。切换完成后，使用虚拟IP（192.168.0.252）连接数据库，你会发现可以正常访问，但实际提供服务的主库已经变为192.168.0.143。通过 `show slave status\G` 命令可以验证新的主从关系已经建立。

故障切换完成后，MHA管理进程会自动退出，因为它要求至少“一主两从”的架构才能持续运行。当前集群变为“一主一从”，故服务终止。

![](img/f2e221d5132cab924f71486e5de5a52e_68.png)

## 初识MySQL视图

![](img/f2e221d5132cab924f71486e5de5a52e_70.png)

在数据库管理部分，我们接下来了解一个有用的功能——视图。

![](img/f2e221d5132cab924f71486e5de5a52e_72.png)

![](img/f2e221d5132cab924f71486e5de5a52e_74.png)

![](img/f2e221d5132cab924f71486e5de5a52e_76.png)

![](img/f2e221d5132cab924f71486e5de5a52e_78.png)

![](img/f2e221d5132cab924f71486e5de5a52e_80.png)

![](img/f2e221d5132cab924f71486e5de5a52e_82.png)

![](img/f2e221d5132cab924f71486e5de5a52e_84.png)

![](img/f2e221d5132cab924f71486e5de5a52e_86.png)

### 什么是视图？
视图（View）可以看作是一种虚拟表。其内容由查询语句定义，本身并不存储实际数据。你可以将复杂的常用查询语句保存为一个视图，之后像查询普通表一样使用它，从而简化操作。

![](img/f2e221d5132cab924f71486e5de5a52e_88.png)

![](img/f2e221d5132cab924f71486e5de5a52e_90.png)

![](img/f2e221d5132cab924f71486e5de5a52e_92.png)

![](img/f2e221d5132cab924f71486e5de5a52e_94.png)

### 视图的作用与创建
视图的主要作用是简化复杂查询，并提供一个统一的数据访问接口。例如，我们有一个关联查询：
```sql
SELECT * FROM student INNER JOIN class ON student.class_id = class.id;
```
如果这个查询需要频繁使用，可以将其创建为一个视图：
```sql
CREATE VIEW student_info AS
SELECT * FROM student INNER JOIN class ON student.class_id = class.id;
```
创建成功后，即可通过一个简单的命令使用该视图：
```sql
SELECT * FROM student_info;
```
视图中的数据会随着原始表（`student`, `class`）中数据的变化而自动更新，因为它保存的是查询逻辑，而非静态数据快照。

![](img/f2e221d5132cab924f71486e5de5a52e_96.png)

## 总结
本节课中我们一起学习了MHA高可用配置的最后关键步骤：配置故障切换脚本、测试SSH与复制环境、启动服务并验证故障切换效果。我们还初步认识了MySQL的视图概念，了解了其作为“虚拟表”简化查询的作用。理解虚拟IP的原理和视图的用途，对于构建稳健、易维护的数据库架构至关重要。