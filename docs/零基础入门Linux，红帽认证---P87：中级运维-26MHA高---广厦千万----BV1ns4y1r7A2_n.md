# Linux运维：P87：MHA高可用配置与视图

![](img/a8150b45e9b9d0059e5a1a1002c07088_1.png)

![](img/a8150b45e9b9d0059e5a1a1002c07088_3.png)

![](img/a8150b45e9b9d0059e5a1a1002c07088_5.png)

![](img/a8150b45e9b9d0059e5a1a1002c07088_7.png)

![](img/a8150b45e9b9d0059e5a1a1002c07088_9.png)

![](img/a8150b45e9b9d0059e5a1a1002c07088_11.png)

在本节课中，我们将学习MHA高可用集群的后续配置步骤，包括故障切换脚本的编写、环境测试以及服务的启动。同时，我们也将初步了解MySQL视图的概念及其作用。

## 配置故障切换脚本

![](img/a8150b45e9b9d0059e5a1a1002c07088_13.png)

![](img/a8150b45e9b9d0059e5a1a1002c07088_15.png)

上一节我们介绍了MHA主配置文件的编写，本节中我们来看看如何配置关键的故障切换脚本。这个脚本用于在主库发生故障时，将虚拟IP（VIP）漂移到新的主库上，确保服务IP不变。

![](img/a8150b45e9b9d0059e5a1a1002c07088_17.png)

在MHA配置文件中，我们定义了脚本的路径。脚本内容本身无需记忆，它主要与VRRP协议及后续会讲到的`keepalived`服务原理相关。其核心作用是执行`ifconfig`命令，为指定网卡临时绑定一个虚拟IP。

![](img/a8150b45e9b9d0059e5a1a1002c07088_19.png)

![](img/a8150b45e9b9d0059e5a1a1002c07088_20.png)

![](img/a8150b45e9b9d0059e5a1a1002c07088_22.png)

![](img/a8150b45e9b9d0059e5a1a1002c07088_24.png)

以下是脚本中需要修改的关键部分：
1.  **虚拟IP**：`my $vip = ‘192.168.0.252/24’;` 此IP不能与集群内其他真实IP冲突，且对外提供服务时将始终使用此IP。
2.  **网卡名称**：`my $key = “1”;` 和 `my $ssh_start_vip = “/sbin/ifconfig eth0:$key $vip”;` 需要将 `eth0` 修改为你服务器上真实的物理网卡名称（如 `eth0`, `ens33` 等）。

![](img/a8150b45e9b9d0059e5a1a1002c07088_26.png)

![](img/a8150b45e9b9d0059e5a1a1002c07088_28.png)

虚拟IP是高可用方案中的关键，它使得无论后台哪台服务器成为主库，客户端始终通过同一个IP地址进行访问，从而实现无感知的故障切换。

## 测试MHA运行环境

![](img/a8150b45e9b9d0059e5a1a1002c07088_30.png)

![](img/a8150b45e9b9d0059e5a1a1002c07088_32.png)

脚本配置并赋予执行权限后，在启动MHA服务之前，必须进行环境测试，确保SSH免密登录和MySQL主从复制状态均正常。

![](img/a8150b45e9b9d0059e5a1a1002c07088_34.png)

![](img/a8150b45e9b9d0059e5a1a1002c07088_36.png)

以下是两个必要的测试命令：

![](img/a8150b45e9b9d0059e5a1a1002c07088_38.png)

![](img/a8150b45e9b9d0059e5a1a1002c07088_40.png)

![](img/a8150b45e9b9d0059e5a1a1002c07088_41.png)

![](img/a8150b45e9b9d0059e5a1a1002c07088_43.png)

![](img/a8150b45e9b9d0059e5a1a1002c07088_45.png)

**1. 测试SSH免密登录连通性**
```bash
masterha_check_ssh --conf=/etc/mha/mha.cnf
```
此命令会检测配置文件中所有节点之间的SSH免密登录是否畅通。如果报错，会明确指出哪两个节点之间连接失败，重新配置免密登录即可。

![](img/a8150b45e9b9d0059e5a1a1002c07088_47.png)

**2. 测试MySQL主从复制状态**
```bash
masterha_check_repl --conf=/etc/mha/mha.cnf
```
此命令检测主从复制配置。如果报错“mysqlbinlog command not found”，是因为MHA服务需要`mysql`和`mysqlbinlog`命令的软链接。需要在所有数据库节点上执行：
```bash
ln -s /usr/local/mysql/bin/mysql /usr/bin/mysql
ln -s /usr/local/mysql/bin/mysqlbinlog /usr/bin/mysqlbinlog
```
两个测试命令均显示`OK`后，才能进行下一步。

![](img/a8150b45e9b9d0059e5a1a1002c07088_49.png)

![](img/a8150b45e9b9d0059e5a1a1002c07088_50.png)

## 启动MHA服务并模拟故障

![](img/a8150b45e9b9d0059e5a1a1002c07088_52.png)

![](img/a8150b45e9b9d0059e5a1a1002c07088_54.png)

![](img/a8150b45e9b9d0059e5a1a1002c07088_56.png)

![](img/a8150b45e9b9d0059e5a1a1002c07088_58.png)

![](img/a8150b45e9b9d0059e5a1a1002c07088_60.png)

环境测试通过后，即可启动MHA服务进行监控。

**启动MHA服务**
```bash
nohup masterha_manager --conf=/etc/mha/mha.cnf &
```
服务启动后，可以在主库上看到虚拟IP已经绑定：
```bash
ifconfig
```
此时，客户端应通过虚拟IP `192.168.0.252` 访问数据库，而非真实的主库IP。

![](img/a8150b45e9b9d0059e5a1a1002c07088_62.png)

![](img/a8150b45e9b9d0059e5a1a1002c07088_63.png)

![](img/a8150b45e9b9d0059e5a1a1002c07088_65.png)

**模拟主库故障**
手动停止主库的MySQL服务：
```bash
systemctl stop mysqld
```
观察MHA管理节点的日志输出，通常在几秒内，MHA会完成以下操作：
1.  检测到主库故障。
2.  选举新的主库（通常是拥有最新日志的从库）。
3.  在原主库上移除虚拟IP。
4.  在新的主库上绑定虚拟IP。
5.  完成主从切换。

![](img/a8150b45e9b9d0059e5a1a1002c07088_67.png)

![](img/a8150b45e9b9d0059e5a1a1002c07088_69.png)

切换完成后，使用虚拟IP `192.168.0.252` 依然可以正常连接到数据库，但实际连接的是新的主库。通过 `SHOW SLAVE STATUS\G` 命令可以验证新的主从关系。

![](img/a8150b45e9b9d0059e5a1a1002c07088_71.png)

![](img/a8150b45e9b9d0059e5a1a1002c07088_73.png)

> **注意**：MHA要求至少“一主两从”才能运行。当一台从库故障切换为主库后，集群变为“一主一从”，MHA服务会自动停止，因为它无法再处理下一次故障。故障节点修复后，需要手动将其作为新的从库加入集群，并重新配置和启动MHA。

![](img/a8150b45e9b9d0059e5a1a1002c07088_75.png)

## 理解MySQL视图

在数据库管理部分，视图是一个非常有用的功能。视图可以被看作是一个虚拟表，其内容由查询定义。它并不存储真实数据，而是保存了一条查询语句。

![](img/a8150b45e9b9d0059e5a1a1002c07088_77.png)

![](img/a8150b45e9b9d0059e5a1a1002c07088_79.png)

视图的主要作用包括：
*   **简化复杂查询**：将冗长或复杂的SQL查询封装成一个视图，后续只需像查询普通表一样操作视图。
*   **增强安全性**：可以只暴露视图中的特定字段给用户，隐藏原始表结构或其他敏感字段。
*   **逻辑数据独立性**：应用程序可以基于视图进行查询，即使底层表结构发生变化，只需修改视图定义，无需改动应用代码。

![](img/a8150b45e9b9d0059e5a1a1002c07088_81.png)

![](img/a8150b45e9b9d0059e5a1a1002c07088_83.png)

![](img/a8150b45e9b9d0059e5a1a1002c07088_85.png)

![](img/a8150b45e9b9d0059e5a1a1002c07088_87.png)

![](img/a8150b45e9b9d0059e5a1a1002c07088_89.png)

**创建视图**
```sql
CREATE VIEW student_info AS
SELECT s.id, s.name, c.class_name
FROM student s
JOIN class c ON s.class_id = c.id;
```
以上命令创建了一个名为 `student_info` 的视图，它联合了`student`和`class`表。

![](img/a8150b45e9b9d0059e5a1a1002c07088_91.png)

![](img/a8150b45e9b9d0059e5a1a1002c07088_93.png)

![](img/a8150b45e9b9d0059e5a1a1002c07088_95.png)

![](img/a8150b45e9b9d0059e5a1a1002c07088_97.png)

![](img/a8150b45e9b9d0059e5a1a1002c07088_98.png)

![](img/a8150b45e9b9d0059e5a1a1002c07088_100.png)

![](img/a8150b45e9b9d0059e5a1a1002c07088_102.png)

**使用视图**
创建后，可以像查询普通表一样使用视图：
```sql
SELECT * FROM student_info;
```
当原始表`student`或`class`的数据发生变化时，查询视图`student_info`得到的结果也会随之改变。

![](img/a8150b45e9b9d0059e5a1a1002c07088_104.png)

![](img/a8150b45e9b9d0059e5a1a1002c07088_106.png)

---

![](img/a8150b45e9b9d0059e5a1a1002c07088_108.png)

本节课中我们一起学习了MHA高可用集群的最终配置与测试，掌握了故障切换脚本的作用和虚拟IP的原理，并通过模拟故障验证了MHA的自动切换能力。最后，我们介绍了MySQL视图的基本概念，了解了它作为虚拟表在简化查询和数据安全方面的用途。