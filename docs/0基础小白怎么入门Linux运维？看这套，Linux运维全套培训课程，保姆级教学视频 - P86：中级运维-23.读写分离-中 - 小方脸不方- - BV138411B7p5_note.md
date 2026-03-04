# Linux运维全套培训课程：P86：中级运维-23.读写分离-中 🖥️

在本节课中，我们将学习如何配置和使用MyCat中间件来实现MySQL数据库的读写分离。我们将详细讲解配置文件的修改、服务的重启与验证，并对比MyCat与另一个中间件Amoeba的特点。

![](img/0a2413b128f7bc3acb1c5efc39fc1fcd_1.png)

---

## 配置文件详解 📝

上一节我们介绍了读写分离的基本概念，本节中我们来看看MyCat的具体配置方法。

配置的核心在于修改 `schema.xml` 文件。我们需要在主库（writeHost）的配置中，添加从库（readHost）的信息。

![](img/0a2413b128f7bc3acb1c5efc39fc1fcd_3.png)

![](img/0a2413b128f7bc3acb1c5efc39fc1fcd_5.png)

![](img/0a2413b128f7bc3acb1c5efc39fc1fcd_7.png)

以下是一个配置示例，展示了如何将一个主库和一个从库关联起来：

![](img/0a2413b128f7bc3acb1c5efc39fc1fcd_9.png)

```xml
<writeHost host="host1" url="jdbc:mysql://192.168.0.131:3306" user="root" password="123456">
    <readHost host="host2" url="192.168.0.129:3306" user="repl_user" password="repl_pass" />
</writeHost>
```

**核心配置项说明：**
*   **writeHost**: 定义主库的连接信息。
*   **readHost**: 定义从库的连接信息，必须嵌套在对应的 `writeHost` 标签内。
*   **balance** 与 **switchType**: 在 `dataHost` 标签中设置，分别控制负载均衡策略和主库故障切换模式。

**重要注意事项：**
1.  **URL格式**：`readHost` 中的 `url` 不能使用 `jdbc:mysql://` 开头，直接写 `IP:端口` 即可，否则重启会报错。
2.  **括号与引号**：XML标签必须正确闭合，属性值需要用引号括起来，格式错误会导致服务无法启动。
3.  **数据库存在性**：配置文件中指定的逻辑数据库名，必须在后端真实的MySQL服务器中存在。

---

## 服务重启与状态检查 🔄

配置文件修改完成后，需要重启MyCat服务使配置生效。

以下是重启和检查服务的命令：

```bash
# 进入MyCat的bin目录
cd /usr/local/mycat/bin

# 重启MyCat服务
./mycat restart

![](img/0a2413b128f7bc3acb1c5efc39fc1fcd_11.png)

![](img/0a2413b128f7bc3acb1c5efc39fc1fcd_13.png)

![](img/0a2413b128f7bc3acb1c5efc39fc1fcd_15.png)

![](img/0a2413b128f7bc3acb1c5efc39fc1fcd_17.png)

# 检查MyCat服务状态
./mycat status
```

**状态解读与故障排查：**
*   **状态为 `running`**：表示服务已成功启动。
*   **状态为 `not running`**：表示服务启动失败，通常是配置文件有错误。
*   **查看详细日志**：如果启动失败，可以使用以下命令查看启动日志，根据错误提示修正配置文件。
    ```bash
    ./mycat console
    ```

---

## 连接测试与效果验证 ✅

![](img/0a2413b128f7bc3acb1c5efc39fc1fcd_19.png)

![](img/0a2413b128f7bc3acb1c5efc39fc1fcd_21.png)

服务成功启动后，我们需要验证读写分离是否配置正确。

![](img/0a2413b128f7bc3acb1c5efc39fc1fcd_23.png)

首先，使用MySQL客户端连接MyCat代理服务器（注意端口是8066，不是MySQL的3306）：

```bash
mysql -u root -p123456 -h 192.168.0.254 -P 8066
```

连接成功后，可以执行 `SHOW DATABASES;` 和 `USE test_db;` 等命令操作逻辑数据库。

**验证读写分离效果：**
为了清晰看到读写分离的效果，可以临时停止主从复制。
1.  在从库服务器上执行 `STOP SLAVE;`。
2.  在MyCat客户端执行 `INSERT` 语句插入数据（写入主库）。
3.  执行 `SELECT` 语句查询数据（从从库读取）。
此时，查询结果将不会包含刚插入的数据，因为写入和读取是在不同的数据库上进行的，从而证明了读写分离在生效。
4.  验证完成后，记得在从库上执行 `START SLAVE;` 恢复主从同步。

![](img/0a2413b128f7bc3acb1c5efc39fc1fcd_25.png)

---

## MyCat 与 Amoeba 对比 ⚖️

除了MyCat，Amoeba（变形虫）也是一个常用的读写分离中间件。以下是两者的主要区别：

**架构策略不同：**
*   **MyCat**：采用“分组”策略，一个主库和其对应的从库被视为一个分组（dataHost）。读写分离和故障切换在组内管理。
*   **Amoeba**：采用“分离”策略，将所有主库放在一起处理写请求，所有从库放在一起处理读请求，两者在配置上完全独立。

**特点与选型建议：**
*   **MyCat 优势**：功能丰富，支持分库分表、故障自动切换等高级功能，社区活跃。
*   **Amoeba 优势**：配置相对简单，在读写负载均衡方面策略可能更灵活。
*   **如何选择**：如果只需要基础的读写分离，两者均可。如果需要更全面的数据库中间件功能（如分片、高可用），MyCat是更主流的选择。

---

![](img/0a2413b128f7bc3acb1c5efc39fc1fcd_27.png)

![](img/0a2413b128f7bc3acb1c5efc39fc1fcd_28.png)

![](img/0a2413b128f7bc3acb1c5efc39fc1fcd_30.png)

![](img/0a2413b128f7bc3acb1c5efc39fc1fcd_31.png)

![](img/0a2413b128f7bc3acb1c5efc39fc1fcd_32.png)

## 总结 📚

![](img/0a2413b128f7bc3acb1c5efc39fc1fcd_34.png)

![](img/0a2413b128f7bc3acb1c5efc39fc1fcd_36.png)

![](img/0a2413b128f7bc3acb1c5efc39fc1fcd_38.png)

本节课中我们一起学习了MyCat实现读写分离的核心实践。
我们首先详细解读了 `schema.xml` 配置文件的写法，然后掌握了重启服务与排查故障的方法，最后通过客户端连接和暂停主从同步验证了读写分离的实际效果。
我们还对比了MyCat和Amoeba两款中间件的不同架构与特点。理解这些配置和原理，是构建高性能、高可用数据库架构的重要基础。