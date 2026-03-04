# Linux运维：P85：中级运维-24.读写分离 🚀

![](img/770d456a5780a7753f65a31ac93c3dcb_0.png)

![](img/770d456a5780a7753f65a31ac93c3dcb_2.png)

![](img/770d456a5780a7753f65a31ac93c3dcb_4.png)

在本节课中，我们将学习如何配置和使用Amoeba中间件来实现MySQL数据库的读写分离。我们将详细讲解Amoeba的安装、配置过程，并通过实际操作演示其读写分离和负载均衡的效果。

---

## 概述 📋

读写分离是提升数据库性能和高可用性的重要手段。它通过将数据库的读操作和写操作分发到不同的服务器上来分担主库的压力。本节课我们将使用Amoeba这一中间件来实现这一架构。

上一节我们介绍了MyCat中间件，本节中我们来看看另一个流行的读写分离工具——Amoeba。

---

![](img/770d456a5780a7753f65a31ac93c3dcb_6.png)

![](img/770d456a5780a7753f65a31ac93c3dcb_7.png)

## 安装Java环境 ☕️

![](img/770d456a5780a7753f65a31ac93c3dcb_9.png)

Amoeba基于Java开发，因此首先需要安装Java运行环境。

以下是安装步骤：

![](img/770d456a5780a7753f65a31ac93c3dcb_11.png)

![](img/770d456a5780a7753f65a31ac93c3dcb_13.png)

![](img/770d456a5780a7753f65a31ac93c3dcb_15.png)

1.  **下载并解压JDK**：将JDK压缩包上传至服务器并解压。
    ```bash
    tar -zxvf jdk-1_6_0_45-linux-x64.bin
    ```
    执行解压命令后，会生成一个`jdk1.6.0_45`目录。

![](img/770d456a5780a7753f65a31ac93c3dcb_17.png)

2.  **移动JDK目录**：将解压后的JDK目录移动到系统常用位置，例如`/usr/local/`。
    ```bash
    mkdir /usr/local/java
    mv jdk1.6.0_45 /usr/local/java/
    ```

![](img/770d456a5780a7753f65a31ac93c3dcb_19.png)

![](img/770d456a5780a7753f65a31ac93c3dcb_21.png)

![](img/770d456a5780a7753f65a31ac93c3dcb_22.png)

![](img/770d456a5780a7753f65a31ac93c3dcb_24.png)

![](img/770d456a5780a7753f65a31ac93c3dcb_26.png)

![](img/770d456a5780a7753f65a31ac93c3dcb_27.png)

3.  **配置环境变量**：编辑`/etc/profile`文件，添加Java环境变量。
    ```bash
    export JAVA_HOME=/usr/local/java/jdk1.6.0_45
    export PATH=$JAVA_HOME/bin:$PATH
    ```
    保存后执行`source /etc/profile`使配置生效。

![](img/770d456a5780a7753f65a31ac93c3dcb_29.png)

![](img/770d456a5780a7753f65a31ac93c3dcb_31.png)

4.  **验证安装**：使用`java -version`命令检查Java是否安装成功。

---

![](img/770d456a5780a7753f65a31ac93c3dcb_33.png)

## 安装与配置Amoeba 🐜

![](img/770d456a5780a7753f65a31ac93c3dcb_35.png)

![](img/770d456a5780a7753f65a31ac93c3dcb_37.png)

完成Java环境配置后，我们就可以安装Amoeba了。

以下是安装与配置步骤：

1.  **解压Amoeba**：将Amoeba的二进制包解压到指定目录。
    ```bash
    mkdir /usr/local/amoeba
    tar -zxvf amoeba-mysql-binary-2.2.0.tar.gz -C /usr/local/amoeba/
    ```

2.  **启动Amoeba**：进入Amoeba的`bin`目录并启动服务。
    ```bash
    cd /usr/local/amoeba/bin
    ./amoeba start &
    ```
    使用`&`符号是为了让服务在后台运行。Amoeba默认监听**8066**端口。

3.  **配置数据库主从**：确保后端MySQL数据库已经配置好主从复制。Amoeba服务器需要能连接所有数据库节点。

---

## 详解Amoeba配置文件 ⚙️

![](img/770d456a5780a7753f65a31ac93c3dcb_39.png)

![](img/770d456a5780a7753f65a31ac93c3dcb_41.png)

Amoeba的核心功能通过两个XML配置文件实现：`amoeba.xml`和`dbServers.xml`。

![](img/770d456a5780a7753f65a31ac93c3dcb_43.png)

### 1. 配置 `amoeba.xml`

这个文件主要定义Amoeba服务本身的基本参数和读写分离策略。

需要修改的关键部分：
*   **连接端口**：默认为8066。
*   **管理用户**：定义连接Amoeba本身的用户名和密码。
*   **读写池定义**：这是实现读写分离的核心。我们需要取消注释并定义写池（`writePool`）和读池（`readPool`）。
    ```xml
    <property name="writePool">master</property>
    <property name="readPool">slaves</property>
    ```
    这里`master`和`slaves`是我们为资源池起的名字，将在下一个文件中具体关联数据库。

### 2. 配置 `dbServers.xml`

![](img/770d456a5780a7753f65a31ac93c3dcb_45.png)

这个文件用于定义具体的后端数据库服务器信息，并将它们归类到上一步定义的资源池中。

需要修改的关键部分：
*   **数据库连接信息**：指定连接每个MySQL实例的用户名、密码和默认数据库。
*   **定义服务器**：为每个数据库服务器定义一个`<dbServer>`节点，包含其IP地址和唯一名称（如`master`, `slave1`, `slave2`）。
*   **配置资源池**：使用`<poolConfig>`将具体的数据库服务器分配到`amoeba.xml`中定义的资源池。
    *   `master`池：通常只包含主库服务器。
    *   `slaves`池：包含所有从库服务器。Amoeba会自动对这个池中的服务器进行**负载均衡**，将读请求平均分发。

---

## 验证读写分离与负载均衡 ✅

配置完成后，重启Amoeba服务使配置生效。

我们可以通过以下步骤验证效果：

1.  **连接Amoeba**：使用MySQL客户端连接Amoeba的8066端口。
    ```bash
    mysql -uroot -p123456 -h192.168.0.254 -P8066
    ```

2.  **验证读写分离**：
    *   执行`INSERT`、`UPDATE`等写操作，这些请求会被Amoeba转发到`master`池（即主库）。
    *   执行`SELECT`读操作，请求会被转发到`slaves`池（即从库）。

3.  **验证读负载均衡**：
    *   在不同的从库上插入具有标识性的数据（例如，在`slave1`插入“from 129”，在`slave2`插入“from 128”）。
    *   通过Amoeba多次执行`SELECT`查询。如果查询结果交替出现“from 129”和“from 128”，则证明Amoeba成功地在多个从库间进行了负载均衡。

---

## 总结 🎯

本节课中我们一起学习了如何使用Amoeba实现MySQL的读写分离。

![](img/770d456a5780a7753f65a31ac93c3dcb_47.png)

我们首先安装了必要的Java环境，然后部署并配置了Amoeba中间件。通过修改`amoeba.xml`和`dbServers.xml`两个核心配置文件，我们定义了写池和读池，并将后端的数据库服务器分别归入这两个池中。最后，我们通过实际操作验证了Amoeba不仅能正确地将读写请求分离，还能在多个从库间实现读请求的负载均衡。

![](img/770d456a5780a7753f65a31ac93c3dcb_49.png)

与之前学习的MyCat相比，Amoeba的配置更为简洁直观，特别适合在大型数据库集群中快速部署读写分离架构。掌握多种中间件的使用，能让我们在面对不同运维场景时更加得心应手。