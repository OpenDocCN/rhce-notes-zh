# Linux运维：P84：中级运维-23.读写分离 🚀

![](img/4f24f95192a994c09aa6def1454c3aa8_1.png)

![](img/4f24f95192a994c09aa6def1454c3aa8_3.png)

在本节课中，我们将学习如何配置读写分离，以提升数据库系统的性能和可用性。读写分离是一种常见的数据库架构优化手段，它将数据库的读操作和写操作分发到不同的服务器上，从而减轻单台服务器的压力。

![](img/4f24f95192a994c09aa6def1454c3aa8_5.png)

## 概述 📖

读写分离的核心思想是将数据库的写操作（如 `INSERT`、`UPDATE`、`DELETE`）定向到主库（Master），而将读操作（如 `SELECT`）定向到一个或多个从库（Slave）。这样，主库可以专注于处理数据变更，而从库则分担了查询请求的压力。为了实现这一目标，我们需要使用一个中间件代理服务器，如 MyCat 或 Amoeba，来智能地路由 SQL 语句。

上一节我们介绍了主从复制的基本配置，本节中我们来看看如何利用中间件实现读写分离。

## MyCat 读写分离配置 ⚙️

![](img/4f24f95192a994c09aa6def1454c3aa8_7.png)

MyCat 是一个流行的数据库中间件，它支持读写分离、分库分表等功能。配置 MyCat 实现读写分离主要涉及修改其配置文件。

![](img/4f24f95192a994c09aa6def1454c3aa8_9.png)

![](img/4f24f95192a994c09aa6def1454c3aa8_11.png)

![](img/4f24f95192a994c09aa6def1454c3aa8_13.png)

![](img/4f24f95192a994c09aa6def1454c3aa8_15.png)

### 1. 修改 `schema.xml` 文件

![](img/4f24f95192a994c09aa6def1454c3aa8_17.png)

首先，我们需要编辑 MyCat 的 `schema.xml` 配置文件，定义数据节点和读写分离规则。

以下是配置的核心部分，我们需要在 `<dataHost>` 标签内指定主库和从库的信息：

```xml
<dataHost name="localhost1" maxCon="1000" minCon="10" balance="3"
          writeType="0" dbType="mysql" dbDriver="native" switchType="1">
    <heartbeat>select user()</heartbeat>
    <!-- 主库写节点 -->
    <writeHost host="hostM1" url="192.168.0.131:3306" user="root" password="123456">
        <!-- 从库读节点 -->
        <readHost host="hostS1" url="192.168.0.129:3306" user="root" password="123456"/>
    </writeHost>
</dataHost>
```

![](img/4f24f95192a994c09aa6def1454c3aa8_19.png)

**关键参数解释：**
*   **`balance`**：负载均衡类型。
    *   `0`：不开启读写分离。
    *   `1`：所有读操作随机发送到 `writeHost` 和 `readHost`。
    *   `2`：所有读操作随机发送到所有 `readHost`。
    *   **`3`**：所有读操作随机发送到所有 `readHost`，`writeHost` 不参与读。**这是最常用的读写分离模式。**
*   **`switchType`**：主从切换模式。
    *   `-1`：不自动切换。
    *   **`1`**：自动切换。当主库宕机时，从库会临时顶替为主库。
*   **`writeHost`**：定义主库的连接信息。
*   **`readHost`**：定义从库的连接信息，必须嵌套在对应的 `writeHost` 标签内。

**配置注意事项：**
1.  **格式必须正确**：XML 标签必须成对出现，有始有终。例如，以 `<readHost>` 开始，就必须以 `</readHost>` 结束。
2.  **驱动选择**：`dbDriver` 建议使用 `native`，使用 JDBC 驱动在重启时可能会报错。
3.  **数据库必须存在**：配置文件中指定的逻辑数据库和真实数据库名必须存在，否则 MyCat 启动时会报错。
4.  **用户权限**：`writeHost` 和 `readHost` 中配置的用户，必须在对应的 MySQL 主从服务器上拥有访问指定数据库的权限。

### 2. 重启并验证 MyCat 服务

配置文件修改完成后，需要重启 MyCat 服务以使配置生效。

以下是管理 MyCat 服务的常用命令：
```bash
# 启动 MyCat
/usr/local/mycat/bin/mycat start

# 停止 MyCat
/usr/local/mycat/bin/mycat stop

# 重启 MyCat
/usr/local/mycat/bin/mycat restart

![](img/4f24f95192a994c09aa6def1454c3aa8_21.png)

![](img/4f24f95192a994c09aa6def1454c3aa8_23.png)

![](img/4f24f95192a994c09aa6def1454c3aa8_25.png)

# 查看 MyCat 状态
/usr/local/mycat/bin/mycat status
```

**验证服务状态：**
执行 `mycat status` 后，如果显示 `MyCat-server is running`，则说明启动成功。如果显示 `MyCat-server is not running`，则说明配置文件有错误，需要根据日志排查。

**查看日志定位错误：**
```bash
# 查看控制台日志，通常会显示启动失败的具体原因
/usr/local/mycat/bin/mycat console
```

### 3. 客户端连接与测试

![](img/4f24f95192a994c09aa6def1454c3aa8_27.png)

MyCat 服务正常启动后，客户端需要连接到 MyCat 的代理端口（默认为 `8066`），而不是直接连接 MySQL 的 `3306` 端口。

![](img/4f24f95192a994c09aa6def1454c3aa8_29.png)

![](img/4f24f95192a994c09aa6def1454c3aa8_31.png)

![](img/4f24f95192a994c09aa6def1454c3aa8_33.png)

![](img/4f24f95192a994c09aa6def1454c3aa8_35.png)

![](img/4f24f95192a994c09aa6def1454c3aa8_37.png)

**连接命令示例：**
```bash
mysql -uroot -p123456 -h 192.168.0.254 -P 8066
```
*   `-h`：指定 MyCat 服务器的 IP 地址。
*   `-P`：**必须指定为 MyCat 的端口 `8066`**。

**测试读写分离效果：**
1.  连接到 MyCat 后，执行 `SHOW DATABASES;` 和 `USE testDB;` 等操作，这些操作会被路由。
2.  为了清晰验证读写分离，可以**临时停止从库的复制进程**：
    ```sql
    -- 在从库服务器上执行
    STOP SLAVE;
    ```
3.  在 MyCat 客户端执行写操作（如 `INSERT`），数据只会写入主库。
4.  执行读操作（如 `SELECT`），此时数据是从从库读取的。由于复制已停止，从库数据不会更新，因此读不到刚写入的数据。这证明了写操作去了主库，读操作去了从库。
5.  测试完成后，记得重新启动复制：
    ```sql
    START SLAVE;
    ```

![](img/4f24f95192a994c09aa6def1454c3aa8_39.png)

![](img/4f24f95192a994c09aa6def1454c3aa8_41.png)

![](img/4f24f95192a994c09aa6def1454c3aa8_42.png)

![](img/4f24f95192a994c09aa6def1454c3aa8_44.png)

**测试主库切换：**
将 `switchType` 设置为 `1` 后，可以模拟主库宕机（停止主库 MySQL 服务）。理论上，MyCat 会将写请求自动切换到从库。但根据实践，此功能的稳定性可能因版本而异，更专业的高可用方案通常使用 MHA 或 Keepalived 等工具。

![](img/4f24f95192a994c09aa6def1454c3aa8_46.png)

![](img/4f24f95192a994c09aa6def1454c3aa8_47.png)

## Amoeba (变形虫) 读写分离配置 🦠

Amoeba 是另一个较早的读写分离中间件，其设计理念与 MyCat 略有不同。

![](img/4f24f95192a994c09aa6def1454c3aa8_49.png)

### 与 MyCat 的核心区别

*   **架构模型**：
    *   **MyCat**：采用“分组”模型，一个主库和其所属的从库为一组。读写分离和故障切换在组内进行。
    *   **Amoeba**：采用“池化”模型，将所有主库放入一个“写池”，所有从库放入一个“读池”。请求在池内进行负载均衡。
*   **功能侧重点**：
    *   **MyCat**：功能丰富，除读写分离外，还支持数据分片、全局序列等，并具备主从自动切换能力。
    *   **Amoeba**：设计更简洁，专注于读写分离和负载均衡，**不具备自动主从切换功能**，但在多从库负载均衡方面可能更直观。
*   **选择建议**：
    *   如果只需要简单的读写分离和负载均衡，Amoeba 配置更简单。
    *   如果需要分库分表、自动切换等更高级的功能，MyCat 是更好的选择。

### Amoeba 配置简述

Amoeba 的配置也主要通过 XML 文件完成，主要步骤包括：
1.  安装 JDK（要求 JDK 1.6）。
2.  修改 `conf/dbServers.xml`：定义所有主库和从库的服务器列表。
3.  修改 `conf/amoeba.xml`：配置读写分离规则、负载均衡算法以及指定使用哪些服务器作为写池和读池。
4.  启动 Amoeba 服务并测试。

由于其配置逻辑与 MyCat 类似但更简单，此处不再展开详细步骤。学员可参考具体笔记或官方文档进行配置。

## 总结 🎯

本节课中我们一起学习了数据库读写分离的配置与实现。

![](img/4f24f95192a994c09aa6def1454c3aa8_51.png)

![](img/4f24f95192a994c09aa6def1454c3aa8_52.png)

![](img/4f24f95192a994c09aa6def1454c3aa8_53.png)

![](img/4f24f95192a994c09aa6def1454c3aa8_55.png)

![](img/4f24f95192a994c09aa6def1454c3aa8_57.png)

1.  **读写分离原理**：通过中间件将读、写操作分流到不同的数据库服务器，提升整体性能。
2.  **MyCat 配置**：我们详细讲解了如何修改 `schema.xml` 文件来定义主从节点，设置负载均衡（`balance`）和切换策略（`switchType`），并完成了服务重启与功能验证。
3.  **效果验证**：通过临时停止主从复制，我们清晰地验证了写操作指向主库、读操作指向从库的分离效果。
4.  **工具对比**：我们对比了 MyCat 和 Amoeba 两款中间件在架构模型和功能上的区别，为不同场景下的技术选型提供了参考。

![](img/4f24f95192a994c09aa6def1454c3aa8_58.png)

![](img/4f24f95192a994c09aa6def1454c3aa8_60.png)

![](img/4f24f95192a994c09aa6def1454c3aa8_62.png)

读写分离是构建高性能、高可用数据库架构的重要基石。掌握其配置与原理，是中级运维工程师必备的技能。在接下来的课程中，我们将探讨更高级的数据库高可用方案。