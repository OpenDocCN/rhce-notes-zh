# Linux运维全套培训课程：P85：中级运维-22.读写分离-上 🚀

![](img/7b69d612dfa73f3748e58726c7365f38_1.png)

在本节课中，我们将要学习**读写分离**技术。这是对上一节主从复制架构的进一步优化，旨在提升数据库集群的性能和稳定性。

上一节我们介绍了主从复制，它实现了数据的实时同步与备份，保证了数据的安全性。然而，主从复制架构存在一个潜在问题：所有的读写操作压力都集中在主库上。本节中我们来看看如何通过读写分离来解决这个问题。

## 读写分离概述

读写分离的核心思想是将数据库的**读操作**和**写操作**分离到不同的服务器上。通常，**写操作**（增、删、改）由主库处理，而**读操作**（查询）则由一个或多个从库分担。这样做可以显著降低主库的负载压力，提升整个数据库集群的并发处理能力。

其原理基于主从复制的单向性：数据只能从主库同步到从库。因此，如果在从库上执行写操作，主库无法获取这些数据，会导致数据不一致。所以，必须由主库负责所有写操作。

## 实验环境准备

![](img/7b69d612dfa73f3748e58726c7365f38_3.png)

![](img/7b69d612dfa73f3748e58726c7365f38_5.png)

本次实验基于上一节搭建好的**主从复制**环境。请确保您已完成一主一从的配置，并且数据同步正常。

![](img/7b69d612dfa73f3748e58726c7365f38_7.png)

![](img/7b69d612dfa73f3748e58726c7365f38_9.png)

以下是实验所需的三台服务器角色与IP规划：
*   **主库 (Master)**: 192.168.0.131
*   **从库 (Slave)**: 192.168.0.129
*   **代理服务器 (MyCat)**: 192.168.0.254

![](img/7b69d612dfa73f3748e58726c7365f38_11.png)

![](img/7b69d612dfa73f3748e58726c7365f38_13.png)

![](img/7b69d612dfa73f3748e58726c7365f38_14.png)

> **重要提示**：如果主从复制未搭建成功，本节的读写分离实验将无法进行。

![](img/7b69d612dfa73f3748e58726c7365f38_16.png)

![](img/7b69d612dfa73f3748e58726c7365f38_18.png)

## 用户授权规划

在配置读写分离前，需要理清三个关键的用户授权，它们分别用于不同的连接场景：

1.  **客户端连接代理服务器**：用户用于登录MyCat代理。
2.  **代理服务器连接后端数据库**：MyCat需要用此用户去连接真实的主库和从库。
3.  **主从复制同步用户**：在上节课主从复制中已配置完成。

![](img/7b69d612dfa73f3748e58726c7365f38_20.png)

接下来，我们先配置第二个用户，即允许代理服务器访问数据库的用户。在主库和从库上分别执行以下授权命令：

![](img/7b69d612dfa73f3748e58726c7365f38_22.png)

![](img/7b69d612dfa73f3748e58726c7365f38_24.png)

```sql
GRANT ALL ON *.* TO 'root'@'192.168.0.254' IDENTIFIED BY '1';
```
此命令授权IP为`192.168.0.254`的服务器（即我们的MyCat代理）以root用户和密码`1`访问所有数据库。

## 安装与配置MyCat

我们将使用 **MyCat** 作为读写分离的代理服务器。MyCat是一个基于Java开发的数据库中间件。

### 1. 安装JDK环境

由于MyCat基于Java，首先需要在代理服务器上安装JDK。

*   上传JDK安装包（如`jdk-8u91-linux-x64.tar.gz`）到服务器。
*   解压并配置环境变量。

![](img/7b69d612dfa73f3748e58726c7365f38_26.png)

![](img/7b69d612dfa73f3748e58726c7365f38_28.png)

```bash
# 解压JDK
tar -zxf jdk-8u91-linux-x64.tar.gz

# 移动至常用目录
mkdir -p /usr/java
cp -r jdk1.8.0_91 /usr/java/

# 配置环境变量
echo 'export JAVA_HOME=/usr/java/jdk1.8.0_91' >> /etc/profile
echo 'export PATH=$JAVA_HOME/bin:$PATH' >> /etc/profile

# 使配置生效
source /etc/profile

# 验证安装
java -version
```

### 2. 安装MyCat

![](img/7b69d612dfa73f3748e58726c7365f38_30.png)

![](img/7b69d612dfa73f3748e58726c7365f38_32.png)

![](img/7b69d612dfa73f3748e58726c7365f38_33.png)

MyCat的安装同样采用解压即用的方式。

*   上传MyCat安装包（如`Mycat-server-1.6.7.3-release-20210913163959-linux.tar.gz`）。
*   解压并配置环境变量。

```bash
# 解压MyCat
tar -zxf Mycat-server-1.6.7.3-release-20210913163959-linux.tar.gz

![](img/7b69d612dfa73f3748e58726c7365f38_35.png)

# 移动至安装目录
mkdir -p /usr/local/mycat
cp -r mycat /usr/local/

![](img/7b69d612dfa73f3748e58726c7365f38_37.png)

# 配置环境变量
echo 'export MYCAT_HOME=/usr/local/mycat' >> /etc/profile
echo 'export PATH=$MYCAT_HOME/bin:$PATH' >> /etc/profile

![](img/7b69d612dfa73f3748e58726c7365f38_39.png)

![](img/7b69d612dfa73f3748e58726c7365f38_41.png)

# 使配置生效
source /etc/profile
```

![](img/7b69d612dfa73f3748e58726c7365f38_43.png)

![](img/7b69d612dfa73f3748e58726c7365f38_45.png)

### 3. 配置MyCat

MyCat的核心配置主要集中在`/usr/local/mycat/conf`目录下的两个文件：`server.xml`和`schema.xml`。

#### 配置 `server.xml` (定义用户与虚拟库)

此文件用于配置连接MyCat的客户端用户信息。

![](img/7b69d612dfa73f3748e58726c7365f38_47.png)

```xml
<!-- 找到文件末尾的user标签，修改或确认以下配置 -->
<user name="root" defaultAccount="true">
    <property name="password">123456</property>
    <property name="schemas">TESTDB</property> <!-- 这是客户端看到的虚拟数据库名 -->
</user>
```
*   `name/password`: 客户端连接MyCat时使用的用户名和密码。
*   `schemas`: 虚拟逻辑库名，用于对客户端隐藏后端真实的数据库。

![](img/7b69d612dfa73f3748e58726c7365f38_49.png)

#### 配置 `schema.xml` (定义数据节点与读写分离规则)

此文件是核心，用于定义真实的数据源、数据节点以及读写分离策略。

以下是需要修改的关键部分：

```xml
<?xml version="1.0"?>
<!DOCTYPE mycat:schema SYSTEM "schema.dtd">
<mycat:schema xmlns:mycat="http://io.mycat/">

    <!-- 定义逻辑库，name与server.xml中的schemas对应 -->
    <schema name="TESTDB" checkSQLschema="false" sqlMaxLimit="100" dataNode="dn1">
    </schema>

    <!-- 定义数据节点，name与schema的dataNode对应，dataHost指向下面定义的数据主机组，database是后端真实数据库名 -->
    <dataNode name="dn1" dataHost="localhost1" database="xinhaibiao" />

    <!-- 定义数据主机组 -->
    <dataHost name="localhost1" maxCon="1000" minCon="10" balance="3"
              writeType="0" dbType="mysql" dbDriver="native" switchType="1"  slaveThreshold="100">
        <!-- 心跳检测语句 -->
        <heartbeat>select user()</heartbeat>

        <!-- 写节点（主库）配置 -->
        <writeHost host="hostM1" url="192.168.0.131:3306" user="root" password="1">
            <!-- 读节点（从库）配置 -->
            <readHost host="hostS1" url="192.168.0.129:3306" user="root" password="1" />
        </writeHost>
    </dataHost>
</mycat:schema>
```

![](img/7b69d612dfa73f3748e58726c7365f38_51.png)

**关键参数解释**：

*   `dataNode`： 将逻辑库映射到具体的数据主机组和真实数据库。
*   `balance`： 负载均衡模式，决定读操作的分配策略。
    *   **0**： 不开启读写分离。
    *   **1**： 所有读操作随机分发到`writeHost`和`readHost`。
    *   **2**： 所有读操作随机分发到所有`readHost`。
    *   **3**： **所有读操作全部分发到`readHost`**。这是最彻底的读写分离模式，推荐使用。
*   `writeHost`： 定义负责写操作的主库信息。
*   `readHost`： 定义在`writeHost`标签内，表示隶属于该主库的从库，负责读操作。
*   `switchType`： 设置主库故障后是否自动切换。
    *   **-1**： 不自动切换。
    *   **1**： 自动切换（当`writeHost`宕机，其下的`readHost`也不会被使用）。
    *   **2**： 基于MySQL主从同步状态决定是否切换。

### 4. 启动MyCat

![](img/7b69d612dfa73f3748e58726c7365f38_53.png)

配置完成后，可以启动MyCat服务。

![](img/7b69d612dfa73f3748e58726c7365f38_55.png)

```bash
# 进入MyCat的bin目录
cd /usr/local/mycat/bin

# 启动MyCat
./mycat start

![](img/7b69d612dfa73f3748e58726c7365f38_57.png)

# 查看启动状态
./mycat status
```
如果显示`Mycat-server is running (PID: XXXX)`，则表示启动成功。

## 连接测试与验证

![](img/7b69d612dfa73f3748e58726c7365f38_59.png)

现在，客户端不再直接连接MySQL数据库，而是连接MyCat代理（默认端口`8066`）。

```bash
# 使用mysql客户端连接MyCat
mysql -h 192.168.0.254 -P 8066 -u root -p123456

# 连接成功后，会看到虚拟逻辑库 TESTDB
show databases;
use TESTDB;
show tables;
```
此时，您对`TESTDB`的所有**写操作**（INSERT, UPDATE, DELETE）都会被MyCat转发到主库（131），所有**读操作**（SELECT）都会被转发到从库（129）。您可以在主从库上开启通用查询日志来验证请求的转发路径。

![](img/7b69d612dfa73f3748e58726c7365f38_61.png)

本节课中我们一起学习了读写分离的基本概念、优势，并完成了基于MyCat实现读写分离的安装与基础配置。我们了解了如何通过分离读写流量来减轻主库压力，并规划了三个必要的用户授权。下一节，我们将进行更深入的测试，并介绍另一个常用的读写分离中间件——Amoeba（变形虫）。

![](img/7b69d612dfa73f3748e58726c7365f38_63.png)

---
**总结**：本节课的核心是理解读写分离如何优化主从架构，以及掌握使用MyCat配置读写分离的基本步骤。关键在于正确配置`schema.xml`中的`balance`参数和主从节点信息，以实现读写流量的智能分发。