# Linux运维教程：P83：中级运维-22.读写分离 🚀

![](img/8e2a3838f3bfb9da6e231dfb71720e51_0.png)

在本节课中，我们将要学习如何基于主从复制架构，进一步实现读写分离。这是一种优化数据库性能、分担主库压力的重要技术。

上一节我们介绍了主从复制，它实现了数据的实时同步与备份，保证了数据的安全性。然而，在这种架构中，所有的读写操作都集中在主库上，从库仅作为备份，这可能导致主库在访问量较大时压力过大。本节中我们来看看如何通过读写分离来解决这个问题。

## 读写分离的原理与优势

读写分离的核心思想是将数据库的读操作和写操作分开处理。具体来说：
*   **写操作**（增、删、改）仍然由**主库**负责。
*   **读操作**（查询）则分发给一个或多个**从库**来处理。

![](img/8e2a3838f3bfb9da6e231dfb71720e51_2.png)

![](img/8e2a3838f3bfb9da6e231dfb71720e51_4.png)

这样做的好处是：
*   **减轻主库压力**：将占比较高的读操作分流，显著降低主库的负载。
*   **提升整体性能**：充分利用从库的资源，提高数据库集群的并发处理能力。
*   **提高可用性**：即使主库出现短暂故障，从库仍可提供读服务。

![](img/8e2a3838f3bfb9da6e231dfb71720e51_6.png)

![](img/8e2a3838f3bfb9da6e231dfb71720e51_8.png)

![](img/8e2a3838f3bfb9da6e231dfb71720e51_10.png)

![](img/8e2a3838f3bfb9da6e231dfb71720e51_12.png)

![](img/8e2a3838f3bfb9da6e231dfb71720e51_14.png)

需要注意的是，由于主从复制是**单向**的（数据从主库同步到从库），因此**写操作必须指向主库**。如果直接在从库上写入数据，主库将无法获取这些数据，导致数据不一致。

![](img/8e2a3838f3bfb9da6e231dfb71720e51_16.png)

## 实验环境与架构准备 🛠️

为了实现读写分离，我们需要在已有的主从复制架构基础上，新增一个**代理服务器**。本次实验的架构如下：

![](img/8e2a3838f3bfb9da6e231dfb71720e51_18.png)

![](img/8e2a3838f3bfb9da6e231dfb71720e51_20.png)

![](img/8e2a3838f3bfb9da6e231dfb71720e51_21.png)

![](img/8e2a3838f3bfb9da6e231dfb71720e51_0.png)

以下是实验环境的简要说明：
*   **主库 (Master)**: IP: 192.168.0.131， 负责处理所有写操作。
*   **从库 (Slave)**: IP: 192.168.0.129， 负责处理代理服务器分发来的读操作，并实时同步主库数据。
*   **代理服务器 (MyCat)**: IP: 192.168.0.254， 作为中间件，负责解析SQL，将写请求转发给主库，读请求转发给从库。

**重要前提**：请确保主从复制已正确配置并运行正常，数据可以实时同步。这是进行读写分离的基础。

## 配置数据库用户授权 🔑

在读写分离架构中，涉及到三类用户授权，需要仔细区分：

![](img/8e2a3838f3bfb9da6e231dfb71720e51_23.png)

![](img/8e2a3838f3bfb9da6e231dfb71720e51_24.png)

1.  **客户端连接代理服务器的用户**：用于应用程序或用户登录到MyCat代理。
2.  **代理服务器连接后端数据库的用户**：用于MyCat代理登录到真实的主库和从库。
3.  **主从复制同步的用户**：在上节课主从复制中已经配置完成。

我们需要在后端的主库和从库上，为MyCat代理创建访问用户。

在主库服务器(131)和从库服务器(129)上分别执行以下授权命令：
```sql
GRANT ALL ON *.* TO 'root'@'192.168.0.254' IDENTIFIED BY '123456';
```
此命令授权IP为`192.168.0.254`（即MyCat服务器）的`root`用户，可以访问所有数据库的所有表。

![](img/8e2a3838f3bfb9da6e231dfb71720e51_26.png)

## 安装与配置MyCat代理 🐱

![](img/8e2a3838f3bfb9da6e231dfb71720e51_28.png)

![](img/8e2a3838f3bfb9da6e231dfb71720e51_29.png)

MyCat是一个基于Java的数据库中间件，我们将使用它来实现读写分离。

### 步骤一：安装JDK环境

![](img/8e2a3838f3bfb9da6e231dfb71720e51_31.png)

![](img/8e2a3838f3bfb9da6e231dfb71720e51_33.png)

MyCat需要Java环境。我们安装JDK 1.8。

![](img/8e2a3838f3bfb9da6e231dfb71720e51_35.png)

![](img/8e2a3838f3bfb9da6e231dfb71720e51_37.png)

1.  上传JDK安装包（如`jdk-8u91-linux-x64.tar.gz`）到代理服务器(254)。
2.  解压安装包：`tar -zxf jdk-8u91-linux-x64.tar.gz`
3.  移动并重命名JDK目录：`mv jdk1.8.0_91 /usr/local/java`
4.  配置环境变量，编辑 `/etc/profile` 文件，在末尾添加：
    ```bash
    export JAVA_HOME=/usr/local/java
    export PATH=$JAVA_HOME/bin:$PATH
    ```
5.  使环境变量生效：`source /etc/profile`
6.  验证安装：`java -version`，应显示JDK 1.8版本信息。

![](img/8e2a3838f3bfb9da6e231dfb71720e51_39.png)

![](img/8e2a3838f3bfb9da6e231dfb71720e51_41.png)

### 步骤二：安装MyCat

1.  上传MyCat安装包（如`Mycat-server-1.6.7.3-release-20210913163959-linux.tar.gz`）到代理服务器。
2.  解压安装包：`tar -zxf Mycat-server-1.6.7.3-release-20210913163959-linux.tar.gz`
3.  移动MyCat目录：`mv mycat /usr/local/`
4.  配置MyCat环境变量，编辑 `/etc/profile`，添加：
    ```bash
    export MYCAT_HOME=/usr/local/mycat
    export PATH=$MYCAT_HOME/bin:$PATH
    ```
5.  使环境变量生效：`source /etc/profile`

![](img/8e2a3838f3bfb9da6e231dfb71720e51_43.png)

### 步骤三：配置MyCat实现读写分离

![](img/8e2a3838f3bfb9da6e231dfb71720e51_45.png)

MyCat的核心配置文件位于 `/usr/local/mycat/conf` 目录下，我们主要修改两个文件：`server.xml` 和 `schema.xml`。

#### 1. 配置 `server.xml` - 定义用户与虚拟库

此文件用于定义连接到MyCat代理的用户信息。
```xml
<user name="root" defaultAccount="true">
    <property name="password">123456</property>
    <property name="schemas">TESTDB</property>
</user>
```
*   `name/password`: 客户端连接MyCat时使用的用户名和密码。
*   `schemas`: **虚拟数据库名**，客户端连接后看到的数据库名称，用于隐藏后端真实的数据库。

![](img/8e2a3838f3bfb9da6e231dfb71720e51_47.png)

#### 2. 配置 `schema.xml` - 定义数据节点与读写分离规则

此文件是核心，用于配置真实的数据源、库表信息以及读写分离规则。以下是一个简化配置示例：
```xml
<?xml version="1.0"?>
<!DOCTYPE mycat:schema SYSTEM "schema.dtd">
<mycat:schema xmlns:mycat="http://io.mycat/">
    <!-- 定义逻辑库，name与server.xml中的schemas对应 -->
    <schema name="TESTDB" checkSQLschema="false" sqlMaxLimit="100" dataNode="dn1">
    </schema>

    <!-- 定义数据节点，name自定义，关联真实数据库 -->
    <dataNode name="dn1" dataHost="localhost1" database="your_real_db_name" />

    <!-- 定义数据主机（即数据库服务器组） -->
    <dataHost name="localhost1" maxCon="1000" minCon="10" balance="3"
              writeType="0" switchType="1"  slaveThreshold="100">
        <!-- 心跳检测，保持连接 -->
        <heartbeat>select user()</heartbeat>
        <!-- 写主机（主库）配置 -->
        <writeHost host="hostM1" url="192.168.0.131:3306" user="root" password="123456">
            <!-- 读主机（从库）配置 -->
            <readHost host="hostS1" url="192.168.0.129:3306" user="root" password="123456" />
        </writeHost>
    </dataHost>
</mycat:schema>
```
**关键参数解释**：
*   `dataNode/database`: 指定**后端真实的数据库名**。
*   `balance`：负载均衡模式，决定读操作的分配策略。
    *   `0`：不开启读写分离。
    *   `1`：所有读操作随机分发到`writeHost`和`readHost`。
    *   `2`：所有读操作随机分发到所有`readHost`。
    *   **`3`：所有读操作全部分发到`readHost`，即最彻底的读写分离。**（推荐）
*   `writeHost`：定义**主库**的连接信息。
*   `readHost`：定义**从库**的连接信息，嵌套在`writeHost`内。
*   `switchType`：`1` 表示主库故障时，会自动切换到从库。

**⚠️ 重要**：请将配置中的 `your_real_db_name` 替换为您在主从服务器上实际存在的、需要做读写分离的数据库名。`balance` 参数设置为 `3`。

![](img/8e2a3838f3bfb9da6e231dfb71720e51_49.png)

## 启动MyCat与测试 🧪

![](img/8e2a3838f3bfb9da6e231dfb71720e51_51.png)

1.  **启动MyCat服务**：在MyCat服务器上执行 `/usr/local/mycat/bin/mycat start`
2.  **查看状态**：`/usr/local/mycat/bin/mycat status` 应显示运行中。
3.  **连接测试**：使用MySQL客户端连接MyCat代理：
    ```bash
    mysql -h 192.168.0.254 -P 8066 -u root -p123456
    ```
    *   `-h`: MyCat服务器IP
    *   `-P`: MyCat服务端口（默认8066）
    *   用户名密码为`server.xml`中配置的`root/123456`
4.  连接成功后，执行 `show databases;` 应能看到虚拟库 `TESTDB`。
5.  **读写分离验证**：
    *   在MyCat连接中执行`INSERT/UPDATE/DELETE`语句，数据应被写入主库(131)，并同步到从库(129)。
    *   执行`SELECT`语句，请求应该被MyCat路由到从库(129)执行。可以通过对比主从库的通用日志或进程列表来验证。

![](img/8e2a3838f3bfb9da6e231dfb71720e51_53.png)

## 总结 📚

本节课中我们一起学习了数据库读写分离的完整实现。我们从主从复制架构的局限性出发，引入了MyCat作为代理中间件，通过将读操作和写操作分离到不同的数据库实例，有效分摊了主库的压力，提升了数据库集群的整体处理能力和资源利用率。

![](img/8e2a3838f3bfb9da6e231dfb71720e51_55.png)

关键步骤包括：
1.  确保主从复制基础架构正常。
2.  在后端数据库上为代理服务器授权。
3.  在代理服务器上安装JDK和MyCat。
4.  正确配置MyCat的`server.xml`（用户）和`schema.xml`（数据源与读写规则）。
5.  启动服务并进行功能验证。

![](img/8e2a3838f3bfb9da6e231dfb71720e51_57.png)

读写分离是构建高性能、高可用数据库架构的关键技术之一，熟练掌握其原理与配置，对于运维工程师至关重要。