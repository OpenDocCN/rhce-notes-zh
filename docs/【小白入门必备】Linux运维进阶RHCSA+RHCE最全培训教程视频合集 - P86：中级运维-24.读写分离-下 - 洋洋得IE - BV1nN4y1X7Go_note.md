# Linux运维进阶：P86：中级运维-24.读写分离-下 🚀

![](img/8003522e9545fc1bd5beebe9e3bf3e4a_0.png)

![](img/8003522e9545fc1bd5beebe9e3bf3e4a_2.png)

![](img/8003522e9545fc1bd5beebe9e3bf3e4a_4.png)

在本节课中，我们将继续学习读写分离的配置，重点介绍如何使用Amoeba（变形虫）中间件来实现读写分离和负载均衡。我们将学习其安装、配置过程，并与之前介绍的MyCat进行对比，理解不同中间件的适用场景。

---

## 软件安装与环境准备 🔧

上一节我们介绍了MyCat的读写分离配置，本节中我们来看看另一个常用的中间件Amoeba。首先，我们需要完成其运行环境JDK和Amoeba本身的安装。

### 安装JDK

![](img/8003522e9545fc1bd5beebe9e3bf3e4a_6.png)

Amoeba基于Java开发，因此需要先安装Java运行环境。以下是安装步骤：

![](img/8003522e9545fc1bd5beebe9e3bf3e4a_7.png)

![](img/8003522e9545fc1bd5beebe9e3bf3e4a_9.png)

1.  上传JDK安装包到服务器。
2.  执行安装命令。安装过程类似于执行一个脚本或Windows中的`.exe`安装程序。
    ```bash
    ./jdk-6u45-linux-x64.bin
    ```
3.  安装过程中，当提示输入`yes`或`no`时，选择`yes`即可。
4.  安装完成后，会生成`jdk1.6.0_45`目录。可以将其移动到统一目录下管理。
    ```bash
    mkdir /usr/local/java
    mv jdk1.6.0_45 /usr/local/java/
    ```
5.  为了方便使用，可以创建软链接并配置环境变量。
    ```bash
    # 删除旧链接（如果存在）
    rm -f /usr/bin/java
    # 创建新软链接
    ln -s /usr/local/java/jdk1.6.0_45/bin/java /usr/bin/java
    # 编辑环境变量配置文件
    vim /etc/profile
    # 在文件末尾添加
    export JAVA_HOME=/usr/local/java/jdk1.6.0_45
    export PATH=$JAVA_HOME/bin:$PATH
    # 使配置生效
    source /etc/profile
    ```

### 安装Amoeba

![](img/8003522e9545fc1bd5beebe9e3bf3e4a_11.png)

![](img/8003522e9545fc1bd5beebe9e3bf3e4a_13.png)

![](img/8003522e9545fc1bd5beebe9e3bf3e4a_15.png)

完成JDK安装后，接下来安装Amoeba中间件。

![](img/8003522e9545fc1bd5beebe9e3bf3e4a_17.png)

![](img/8003522e9545fc1bd5beebe9e3bf3e4a_19.png)

1.  上传Amoeba安装包。其命名通常包含软件名、针对的数据库（MySQL）和包类型（binary，二进制包）。
2.  创建专属目录并解压安装包到该目录。二进制包无需编译，解压即可使用。
    ```bash
    mkdir /usr/local/amoeba
    tar -zxvf amoeba-mysql-binary-2.2.0.tar.gz -C /usr/local/amoeba/
    ```
3.  进入Amoeba目录，其可执行命令位于`bin`目录下。
    ```bash
    cd /usr/local/amoeba
    ```
4.  启动Amoeba服务。默认服务端口是`8066`，用于接收客户端连接。它会使用其他端口与后端MySQL服务器保持长连接，以提升响应效率。
    ```bash
    /usr/local/amoeba/bin/amoeba start &
    # 使用 & 符号是为了将服务放到后台运行，不占用当前终端。
    ```

![](img/8003522e9545fc1bd5beebe9e3bf3e4a_21.png)

![](img/8003522e9545fc1bd5beebe9e3bf3e4a_23.png)

![](img/8003522e9545fc1bd5beebe9e3bf3e4a_25.png)

![](img/8003522e9545fc1bd5beebe9e3bf3e4a_26.png)

![](img/8003522e9545fc1bd5beebe9e3bf3e4a_28.png)

---

![](img/8003522e9545fc1bd5beebe9e3bf3e4a_30.png)

![](img/8003522e9545fc1bd5beebe9e3bf3e4a_31.png)

![](img/8003522e9545fc1bd5beebe9e3bf3e4a_33.png)

## 配置Amoeba实现读写分离 ⚙️

![](img/8003522e9545fc1bd5beebe9e3bf3e4a_35.png)

Amoeba的配置思路与MyCat类似，但更侧重于将数据库集群抽象为“写资源池”和“读资源池”，配置相对简洁，尤其适合大规模数据库架构。

![](img/8003522e9545fc1bd5beebe9e3bf3e4a_37.png)

### 主从复制环境确认

![](img/8003522e9545fc1bd5beebe9e3bf3e4a_39.png)

![](img/8003522e9545fc1bd5beebe9e3bf3e4a_41.png)

在配置Amoeba前，需确保MySQL主从复制环境已搭建并运行正常。本节示例为一主两从架构。

*   **主库 (Master)**: 192.168.0.131
*   **从库1 (Slave1)**: 192.168.0.129
*   **从库2 (Slave2)**: 192.168.0.128

请确保所有从库都已正确指向主库，并开启了复制功能。

### 配置文件修改

Amoeba主要有两个配置文件需要修改：`amoeba.xml` 和 `dbServers.xml`。

#### 1. 配置 `amoeba.xml`

![](img/8003522e9545fc1bd5beebe9e3bf3e4a_43.png)

此文件主要定义Amoeba服务本身参数、连接用户以及读写资源池。

![](img/8003522e9545fc1bd5beebe9e3bf3e4a_45.png)

![](img/8003522e9545fc1bd5beebe9e3bf3e4a_47.png)

1.  编辑配置文件。
    ```bash
    vim /usr/local/amoeba/conf/amoeba.xml
    ```
2.  找到用户认证部分，设置连接Amoeba代理的用户名和密码。
    ```xml
    <!-- 第30行左右 -->
    <property name="user">root</property>
    <property name="password">123456</property>
    ```
3.  找到并修改读写资源池配置部分（通常在文件末尾）。取消注释并定义写池（`writePool`）和读池（`readPool`）的名称。
    ```xml
    <!-- 第115行左右 -->
    <property name="writePool">master</property>
    <property name="readPool">slaves</property>
    ```
    这里定义了两个资源池：`master`（负责写操作）和`slaves`（负责读操作）。名称可自定义，但需与下一个配置文件对应。

#### 2. 配置 `dbServers.xml`

此文件用于定义具体的数据库服务器信息，并将它们分配到上一步定义的资源池中。

1.  编辑配置文件。
    ```bash
    vim /usr/local/amoeba/conf/dbServers.xml
    ```
2.  修改默认数据库连接用户和密码（这是连接后端真实MySQL的凭据）。
    ```xml
    <!-- 第26行左右 -->
    <property name="user">root</property>
    <property name="password">123456</property>
    ```
3.  修改默认进入的数据库。Amoeba会默认让客户端连接此数据库，它必须是后端真实存在的数据库。
    ```xml
    <!-- 第30行左右 -->
    <property name="schema">test</property>
    ```
4.  定义具体的数据库服务器。找到`dbServer`标签部分，修改或添加主库和从库的信息。
    ```xml
    <!-- 定义主库 (写池) -->
    <dbServer name="master" parent="abstractServer">
        <factoryConfig>
            <property name="ipAddress">192.168.0.131</property>
        </factoryConfig>
    </dbServer>

    <!-- 定义从库1 (读池) -->
    <dbServer name="slave1" parent="abstractServer">
        <factoryConfig>
            <property name="ipAddress">192.168.0.129</property>
        </factoryConfig>
    </dbServer>

    <!-- 定义从库2 (读池) -->
    <dbServer name="slave2" parent="abstractServer">
        <factoryConfig>
            <property name="ipAddress">192.168.0.128</property>
        </factoryConfig>
    </dbServer>
    ```
5.  配置读池的负载均衡。将多个从库服务器组合到名为`slaves`的池中，实现读请求的均衡分发。
    ```xml
    <dbServer name="slaves" virtual="true">
        <poolConfig class="com.meidusa.amoeba.server.MultipleServerPool">
            <property name="loadbalance">1</property> <!-- 1代表轮询策略 -->
            <property name="poolNames">slave1,slave2</property>
        </poolConfig>
    </dbServer>
    ```

![](img/8003522e9545fc1bd5beebe9e3bf3e4a_49.png)

---

## 功能验证与总结 🎯

配置完成后，重启Amoeba服务使配置生效。

```bash
/usr/local/amoeba/bin/amoeba restart &
```

![](img/8003522e9545fc1bd5beebe9e3bf3e4a_51.png)

![](img/8003522e9545fc1bd5beebe9e3bf3e4a_53.png)

### 连接测试与验证

![](img/8003522e9545fc1bd5beebe9e3bf3e4a_55.png)

![](img/8003522e9545fc1bd5beebe9e3bf3e4a_57.png)

![](img/8003522e9545fc1bd5beebe9e3bf3e4a_58.png)

![](img/8003522e9545fc1bd5beebe9e3bf3e4a_60.png)

![](img/8003522e9545fc1bd5beebe9e3bf3e4a_62.png)

1.  **连接Amoeba代理**：使用MySQL客户端连接Amoeba的`8066`端口。
    ```bash
    mysql -uroot -p123456 -h192.168.0.254 -P8066
    ```
    连接成功后，会默认进入在`dbServers.xml`中配置的`test`数据库。

![](img/8003522e9545fc1bd5beebe9e3bf3e4a_64.png)

![](img/8003522e9545fc1bd5beebe9e3bf3e4a_65.png)

2.  **验证读写分离**：
    *   执行`INSERT`、`UPDATE`、`DELETE`等写操作，请求会被转发到主库（`master`）。
    *   执行`SELECT`读操作，请求会被转发到从库池（`slaves`）中的服务器。

3.  **验证读负载均衡**：
    *   为了直观验证，可以分别在两个从库的`test`库中插入标识性数据（例如，各插入一条包含本机IP的记录）。
    *   在Amoeba代理上多次执行`SELECT`查询。由于配置了轮询负载均衡，查询结果会交替显示来自`slave1`和`slave2`的数据，证明读请求被均匀分配。

### 注意事项

*   **防火墙**：确保Amoeba服务器以及所有MySQL服务器的防火墙规则允许相关端口的通信。
*   **数据库授权**：确保Amoeba配置中使用的MySQL用户（如`root`）拥有从Amoeba服务器IP连接并访问相应数据库的权限。
*   **虚拟数据库**：与MyCat不同，Amoeba的`schema`属性指定的是真实存在的数据库名，而非虚拟库。

---

### MyCat 与 Amoeba 对比

![](img/8003522e9545fc1bd5beebe9e3bf3e4a_67.png)

![](img/8003522e9545fc1bd5beebe9e3bf3e4a_68.png)

| 特性 | MyCat | Amoeba |
| :--- | :--- | :--- |
| **配置复杂度** | 相对复杂，需定义数据节点、分片规则等。 | 相对简单，核心是定义读写资源池。 |
| **设计侧重点** | 强大的数据分片（分库分表）功能。 | 读写分离与负载均衡，配置简洁明了。 |
| **适用场景** | 需要处理超大规模数据、进行复杂分片的场景。 | 读写压力分明、需要简单有效读写分离的大规模集群。 |
| **学习曲线** | 较陡峭，概念较多。 | 较平缓，易于快速上手。 |

![](img/8003522e9545fc1bd5beebe9e3bf3e4a_70.png)

---

![](img/8003522e9545fc1bd5beebe9e3bf3e4a_72.png)

本节课中我们一起学习了使用Amoeba中间件实现MySQL读写分离和负载均衡。我们完成了从JDK环境搭建、Amoeba安装到核心配置文件`amoeba.xml`和`dbServers.xml`的详细配置，并进行了功能验证。通过与MyCat的对比，可以看出Amoeba在配置简洁性和读写分离专注度上的优势。掌握多种中间件有助于在实际运维中根据具体场景选择最合适的工具。下节课，我们将开始学习数据库高可用方案**MHA**，实现主库故障时的自动切换。