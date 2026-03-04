# Linux运维全套培训课程：P87：中级运维-24.读写分离-下 🚀

![](img/6ced28367fd7503708bb83490452189c_0.png)

![](img/6ced28367fd7503708bb83490452189c_2.png)

![](img/6ced28367fd7503708bb83490452189c_4.png)

在本节课中，我们将继续学习读写分离的配置，重点介绍另一个常用的中间件——Amoeba。我们将完成其安装、配置，并验证读写分离及负载均衡的效果。

---

## 安装Java环境

上一节我们介绍了MyCat的配置，本节中我们来看看Amoeba的安装。Amoeba基于Java开发，因此首先需要安装Java运行环境。

以下是安装JDK 1.6的步骤：

![](img/6ced28367fd7503708bb83490452189c_6.png)

1.  执行安装脚本。看到提示时，选择“yes”即可。
    ```bash
    ./jdk-6u45-linux-x64.bin
    ```
2.  安装完成后，将生成的`jdk1.6.0_45`目录移动到指定位置。
    ```bash
    mkdir /usr/local/java
    mv jdk1.6.0_45 /usr/local/java/
    ```
3.  为了方便使用，可以创建软链接并配置环境变量。
    ```bash
    rm -rf /usr/bin/java
    ln -s /usr/local/java/jdk1.6.0_45/bin/java /usr/bin/java
    ```
4.  编辑`/etc/profile`文件，添加Java环境变量。
    ```bash
    export JAVA_HOME=/usr/local/java/jdk1.6.0_45
    export PATH=$JAVA_HOME/bin:$PATH
    ```
5.  使环境变量生效。
    ```bash
    source /etc/profile
    ```

![](img/6ced28367fd7503708bb83490452189c_7.png)

![](img/6ced28367fd7503708bb83490452189c_9.png)

---

## 安装与启动Amoeba

![](img/6ced28367fd7503708bb83490452189c_11.png)

![](img/6ced28367fd7503708bb83490452189c_13.png)

![](img/6ced28367fd7503708bb83490452189c_15.png)

Java环境准备就绪后，接下来安装Amoeba中间件。

![](img/6ced28367fd7503708bb83490452189c_17.png)

![](img/6ced28367fd7503708bb83490452189c_19.png)

以下是安装与启动Amoeba的步骤：

![](img/6ced28367fd7503708bb83490452189c_21.png)

![](img/6ced28367fd7503708bb83490452189c_23.png)

![](img/6ced28367fd7503708bb83490452189c_24.png)

![](img/6ced28367fd7503708bb83490452189c_26.png)

1.  将Amoeba软件包上传至服务器并解压到指定目录。
    ```bash
    mkdir /usr/local/amoeba
    tar -zxvf amoeba-mysql-binary-2.2.0.tar.gz -C /usr/local/amoeba/
    ```
2.  进入Amoeba目录，其可执行命令位于`bin`目录下。
    ```bash
    cd /usr/local/amoeba
    ```
3.  启动Amoeba服务。默认服务端口为8066，用于连接客户端；后端端口用于连接MySQL数据库。
    ```bash
    ./bin/amoeba start &
    ```
    > **注意**：命令后的`&`符号表示在后台运行，避免占用当前终端。

![](img/6ced28367fd7503708bb83490452189c_28.png)

![](img/6ced28367fd7503708bb83490452189c_29.png)

![](img/6ced28367fd7503708bb83490452189c_31.png)

---

![](img/6ced28367fd7503708bb83490452189c_33.png)

## 配置MySQL主从复制

![](img/6ced28367fd7503708bb83490452189c_35.png)

在配置Amoeba之前，需要确保后端MySQL数据库的主从复制架构已经搭建完成。

![](img/6ced28367fd7503708bb83490452189c_37.png)

![](img/6ced28367fd7503708bb83490452189c_39.png)

以下是配置一主两从的简要步骤：

1.  在主库上查看状态，记录`File`和`Position`。
    ```sql
    SHOW MASTER STATUS;
    ```
2.  在从库上执行命令，指向主库。
    ```sql
    CHANGE MASTER TO
    MASTER_HOST='192.168.0.131',
    MASTER_USER='root',
    MASTER_PASSWORD='1',
    MASTER_LOG_FILE='mysql-bin.000004',
    MASTER_LOG_POS=154;
    ```
3.  启动从库复制线程。
    ```sql
    START SLAVE;
    ```

---

## 配置Amoeba实现读写分离

Amoeba通过两个核心配置文件实现读写分离，思路是将数据库按“写”和“读”分成两个资源池。

### 1. 配置 `amoeba.xml`

![](img/6ced28367fd7503708bb83490452189c_41.png)

此文件类似于MyCat的`server.xml`，用于定义全局设置。

![](img/6ced28367fd7503708bb83490452189c_43.png)

![](img/6ced28367fd7503708bb83490452189c_45.png)

需要修改的关键部分如下：

*   **用户认证**：设置连接Amoeba代理的用户名和密码。
*   **资源池定义**：取消注释并定义写池（`writePool`）和读池（`readPool`），分别对应主库和从库。
    ```xml
    <property name="writePool">master</property>
    <property name="readPool">slaves</property>
    ```
    > **提示**：`master`和`slaves`是自定义的资源池名称，将在下一个配置文件中引用。

### 2. 配置 `dbServers.xml`

此文件用于定义具体的数据库服务器信息，并分配到上一步定义的资源池中。

![](img/6ced28367fd7503708bb83490452189c_47.png)

需要修改的关键部分如下：

*   **数据库连接信息**：设置用于连接后端MySQL数据库的用户名和密码。
*   **定义具体服务器**：为每个数据库服务器（主库、从库）定义唯一的名称和IP地址。
    ```xml
    <dbServer name="master" parent="abstractServer">
        <factoryConfig>
            <property name="ipAddress">192.168.0.131</property>
        </factoryConfig>
    </dbServer>
    <dbServer name="slave1" parent="abstractServer">
        <factoryConfig>
            <property name="ipAddress">192.168.0.129</property>
        </factoryConfig>
    </dbServer>
    ```
*   **配置负载均衡**：将多个从库服务器组合到读资源池（`slaves`）中，实现读请求的负载均衡。
    ```xml
    <dbServer name="slaves" virtual="true">
        <poolConfig class="com.meidusa.amoeba.server.MultipleServerPool">
            <property name="loadbalance">1</property>
            <property name="poolNames">slave1,slave2</property>
        </poolConfig>
    </dbServer>
    ```
    > **注意**：`dbServers.xml`中还需要指定一个`defaultPool`，这是客户端连接Amoeba后默认进入的**真实数据库**，需确保该数据库已存在。

配置完成后，重启Amoeba服务使配置生效。

---

![](img/6ced28367fd7503708bb83490452189c_49.png)

![](img/6ced28367fd7503708bb83490452189c_51.png)

## 验证读写分离与负载均衡

![](img/6ced28367fd7503708bb83490452189c_53.png)

![](img/6ced28367fd7503708bb83490452189c_55.png)

![](img/6ced28367fd7503708bb83490452189c_56.png)

![](img/6ced28367fd7503708bb83490452189c_58.png)

![](img/6ced28367fd7503708bb83490452189c_60.png)

配置完成后，我们需要验证读写分离和负载均衡是否正常工作。

![](img/6ced28367fd7503708bb83490452189c_62.png)

![](img/6ced28367fd7503708bb83490452189c_63.png)

以下是验证步骤：

1.  通过Amoeba代理连接MySQL。
    ```bash
    mysql -uroot -p1 -h192.168.0.254 -P8066
    ```
2.  **验证读写分离**：在从库中插入数据（正常情况下从库不应直接写入），然后通过代理查询。如果能查询到从库插入的数据，说明读请求被正确分流到了从库，验证了读写分离。
3.  **验证负载均衡**：反复执行查询语句，观察返回结果。如果数据轮流来自不同的从库（例如一次是slave1的数据，下一次是slave2的数据），则说明读请求在从库间实现了负载均衡。

> **故障排查**：如果无法连接某些从库，请检查防火墙设置以及MySQL的远程访问授权。

---

## 总结

本节课中我们一起学习了如何使用Amoeba实现MySQL的读写分离。

![](img/6ced28367fd7503708bb83490452189c_65.png)

![](img/6ced28367fd7503708bb83490452189c_66.png)

我们完成了Java环境的搭建、Amoeba的安装与启动，并详细讲解了其配置文件的修改，核心在于将数据库划分为“写”（`master`）和“读”（`slaves`）两个资源池。最后，我们验证了读写分离和负载均衡的效果。

![](img/6ced28367fd7503708bb83490452189c_68.png)

与MyCat相比，Amoeba的配置更为简洁直观，特别适合在大型分布式数据库架构中管理多台数据库服务器。掌握多种中间件工具，能让你在面对不同运维场景时更加得心应手。

![](img/6ced28367fd7503708bb83490452189c_70.png)

下节课，我们将开始学习数据库高可用方案，解决主库故障时的自动切换问题。