# Linux运维全套培训课程：P89：中级运维-26.MHA高可用，视图-中 🛠️

![](img/ad3f5a83da643a283653e891ddb07757_1.png)

![](img/ad3f5a83da643a283653e891ddb07757_3.png)

![](img/ad3f5a83da643a283653e891ddb07757_5.png)

![](img/ad3f5a83da643a283653e891ddb07757_7.png)

在本节课中，我们将继续学习MHA高可用集群的配置与启动，并初步了解MySQL视图的概念。我们将完成MHA配置的最后步骤，启动服务，模拟故障切换，并理解虚拟IP的关键作用。

![](img/ad3f5a83da643a283653e891ddb07757_9.png)

## 配置故障切换脚本

![](img/ad3f5a83da643a283653e891ddb07757_11.png)

上一节我们完成了MHA主配置文件的编写。本节中，我们来看看配置的最后三步。首先，我们需要配置故障切换时使用的脚本。

![](img/ad3f5a83da643a283653e891ddb07757_13.png)

![](img/ad3f5a83da643a283653e891ddb07757_15.png)

![](img/ad3f5a83da643a283653e891ddb07757_17.png)

以下是配置步骤：

![](img/ad3f5a83da643a283653e891ddb07757_19.png)

![](img/ad3f5a83da643a283653e891ddb07757_21.png)

![](img/ad3f5a83da643a283653e891ddb07757_23.png)

1.  在配置文件中，`master_ip_failover_script` 参数定义了故障切换脚本的路径。你可以根据需要修改此路径，但脚本的实际存放位置需与之对应。
2.  使用文本编辑器（如`vim`）创建或编辑该脚本文件。即使文件不存在，直接编辑即可。
3.  脚本内容涉及`keepalived`服务和VRRP协议，用于管理虚拟IP。现阶段无需记忆脚本细节，重点是修改其中的关键参数。
4.  脚本中需要修改两部分：
    *   **虚拟IP地址**：将其修改为一个未被占用的IP地址（例如 `192.168.0.252`）。
    *   **网卡名称**：将 `eth1` 修改为你服务器上物理网卡的实际名称（可能是 `eth0`、`ens33` 或 `eno16777736` 等）。

此脚本的作用是在主库故障时，通过 `ifconfig` 命令在新主库上临时绑定指定的虚拟IP，确保对外服务的IP地址不变。

![](img/ad3f5a83da643a283653e891ddb07757_25.png)

![](img/ad3f5a83da643a283653e891ddb07757_27.png)

## 测试环境与启动MHA

![](img/ad3f5a83da643a283653e891ddb07757_29.png)

![](img/ad3f5a83da643a283653e891ddb07757_31.png)

![](img/ad3f5a83da643a283653e891ddb07757_33.png)

脚本配置完成后，接下来是最后两步：测试环境和启动MHA服务。

![](img/ad3f5a83da643a283653e891ddb07757_35.png)

![](img/ad3f5a83da643a283653e891ddb07757_37.png)

![](img/ad3f5a83da643a283653e891ddb07757_39.png)

![](img/ad3f5a83da643a283653e891ddb07757_41.png)

以下是具体操作：

![](img/ad3f5a83da643a283653e891ddb07757_43.png)

![](img/ad3f5a83da643a283653e891ddb07757_45.png)

![](img/ad3f5a83da643a283653e891ddb07757_46.png)

1.  **赋予脚本执行权限**：执行命令 `chmod +x /usr/bin/master_ip_failover`。没有执行权限的脚本无法运行。
2.  **测试SSH免密登录**：执行命令 `masterha_check_ssh --conf=/etc/mha.cnf`。此命令检查配置文件中所有节点间的SSH连接是否正常。如果报错，请根据提示重新配置对应节点间的免密登录。
3.  **测试主从复制状态**：执行命令 `masterha_check_repl --conf=/etc/mha.cnf`。此命令检查主从复制集群的状态。常见的错误是缺少MySQL命令的软链接。
    *   若报错提示 `mysqlbinlog` 命令未找到，需要在所有数据库节点上创建软链接：
        ```bash
        ln -s /usr/local/mysql/bin/mysql /usr/bin/mysql
        ln -s /usr/local/mysql/bin/mysqlbinlog /usr/bin/mysqlbinlog
        ```
4.  **启动MHA服务**：当以上两个测试均通过后，执行命令 `nohup masterha_manager --conf=/etc/mha.cnf &` 在后台启动MHA监控进程。也可以在前台运行以便观察日志。

![](img/ad3f5a83da643a283653e891ddb07757_48.png)

![](img/ad3f5a83da643a283653e891ddb07757_50.png)

## 模拟故障与切换验证

![](img/ad3f5a83da643a283653e891ddb07757_52.png)

服务启动后，我们可以模拟主库故障，观察MHA的自动切换效果。

![](img/ad3f5a83da643a283653e891ddb07757_54.png)

![](img/ad3f5a83da643a283653e891ddb07757_56.png)

![](img/ad3f5a83da643a283653e891ddb07757_58.png)

以下是验证过程：

![](img/ad3f5a83da643a283653e891ddb07757_60.png)

![](img/ad3f5a83da643a283653e891ddb07757_62.png)

1.  在主库上停止MySQL服务：`systemctl stop mysqld`。
2.  观察MHA管理节点的输出日志，会在几秒内显示检测到故障并开始切换。
3.  切换完成后，使用虚拟IP (`192.168.0.252`) 连接数据库，验证是否可以正常访问。
4.  检查新的主库（原从库）上是否已经绑定了虚拟IP。
5.  在新的主库上创建数据，验证复制是否同步到剩余的从库。

![](img/ad3f5a83da643a283653e891ddb07757_64.png)

![](img/ad3f5a83da643a283653e891ddb07757_66.png)

![](img/ad3f5a83da643a283653e891ddb07757_68.png)

**关键点总结**：
*   **虚拟IP的作用**：对于前端应用（如Web服务器）来说，数据库主库的访问地址应始终配置为这个虚拟IP，而不是任何一台数据库的真实IP。这样，无论后台主库如何切换，应用的连接配置都无需更改，实现了无缝故障转移。
*   **MHA服务状态**：完成一次故障切换后，MHA服务会自动停止，因为当前集群不再满足“一主两从”的最低运行要求。
*   **故障节点恢复**：当原主库修复后，需要手动将其数据同步至当前集群，并重新配置主从关系，然后修改MHA配置文件并重新启动服务。

## 初识MySQL视图

![](img/ad3f5a83da643a283653e891ddb07757_70.png)

在数据库管理部分，视图（View）是一个非常有用的功能。

![](img/ad3f5a83da643a283653e891ddb07757_72.png)

简单来说，视图可以被看作是一个**虚拟表**。它本身不存储数据，而是保存了一条查询（`SELECT`）语句。当查询视图时，数据库会执行该语句并返回结果。

![](img/ad3f5a83da643a283653e891ddb07757_74.png)

![](img/ad3f5a83da643a283653e891ddb07757_76.png)

![](img/ad3f5a83da643a283653e891ddb07757_78.png)

![](img/ad3f5a83da643a283653e891ddb07757_80.png)

![](img/ad3f5a83da643a283653e891ddb07757_82.png)

![](img/ad3f5a83da643a283653e891ddb07757_84.png)

![](img/ad3f5a83da643a283653e891ddb07757_86.png)

![](img/ad3f5a83da643a283653e891ddb07757_88.png)

视图的主要作用是**简化复杂查询**。对于一些编写繁琐、频繁使用的多表关联或复杂过滤的`SELECT`语句，可以将其创建为视图。之后只需像查询普通表一样查询视图即可。

![](img/ad3f5a83da643a283653e891ddb07757_90.png)

![](img/ad3f5a83da643a283653e891ddb07757_92.png)

![](img/ad3f5a83da643a283653e891ddb07757_94.png)

![](img/ad3f5a83da643a283653e891ddb07757_96.png)

例如，将一条关联学生表和班级表的查询创建为视图：
```sql
CREATE VIEW student_info AS
SELECT s.id, s.name, c.class_name
FROM students s
JOIN class c ON s.class_id = c.id;
```
创建后，只需执行 `SELECT * FROM student_info;` 即可获得与原来复杂查询相同的结果。当基础表中的数据发生变化时，通过视图查询到的结果也会自动更新。

![](img/ad3f5a83da643a283653e891ddb07757_98.png)

本节课中我们一起学习了MHA高可用集群的最终配置、启动、测试及故障模拟的全过程，理解了虚拟IP在高可用架构中的核心价值。同时，我们也初步了解了MySQL视图的概念及其简化查询的作用。