# Docker容器数据卷实战教程：P102：Docker-5.docker容器数据卷

![](img/41cde506cf85dbdeb6e50b632f1b09fd_1.png)

![](img/41cde506cf85dbdeb6e50b632f1b09fd_2.png)

![](img/41cde506cf85dbdeb6e50b632f1b09fd_4.png)

![](img/41cde506cf85dbdeb6e50b632f1b09fd_6.png)

![](img/41cde506cf85dbdeb6e50b632f1b09fd_7.png)

在本节课中，我们将学习Docker容器数据卷的核心概念与实战应用。我们将通过部署MySQL、Nginx和Tomcat三个常见服务，来掌握如何利用数据卷实现容器数据的持久化、配置管理以及宿主机与容器间的文件同步。

![](img/41cde506cf85dbdeb6e50b632f1b09fd_9.png)

![](img/41cde506cf85dbdeb6e50b632f1b09fd_10.png)

## 概述：什么是容器数据卷？

![](img/41cde506cf85dbdeb6e50b632f1b09fd_12.png)

![](img/41cde506cf85dbdeb6e50b632f1b09fd_13.png)

![](img/41cde506cf85dbdeb6e50b632f1b09fd_15.png)

容器数据卷是Docker用于实现数据持久化和共享的机制。它可以将宿主机上的目录或文件挂载到容器内部，使得容器内产生的数据（如数据库文件、日志、配置文件）能够保存在宿主机上，从而实现：
*   **数据持久化**：容器删除后，数据依然存在。
*   **配置同步**：方便地在宿主机上修改配置文件并应用到容器。
*   **数据共享**：多个容器可以挂载同一个数据卷，实现数据交换。

![](img/41cde506cf85dbdeb6e50b632f1b09fd_17.png)

![](img/41cde506cf85dbdeb6e50b632f1b09fd_19.png)

---

## 实战一：部署MySQL并配置数据卷 🗄️

上一节我们介绍了数据卷的基本概念，本节中我们来看看如何为MySQL容器配置数据卷。

### 准备工作：提取容器配置文件

在创建挂载数据卷的MySQL容器前，我们需要先将容器内的配置文件拷贝到宿主机。这是因为如果直接将一个空目录挂载到容器的配置目录，会导致容器内原有的配置文件被覆盖。

![](img/41cde506cf85dbdeb6e50b632f1b09fd_21.png)

![](img/41cde506cf85dbdeb6e50b632f1b09fd_23.png)

以下是操作步骤：

![](img/41cde506cf85dbdeb6e50b632f1b09fd_25.png)

![](img/41cde506cf85dbdeb6e50b632f1b09fd_27.png)

1.  **运行临时MySQL容器**：用于提取配置文件。
    ```bash
    docker run -d --name mysql_temp mysql:5.7
    ```
2.  **创建宿主机存储目录**：
    ```bash
    mkdir -p /docker/mysql
    ```
3.  **拷贝配置文件**：将容器内的 `/etc/mysql` 目录拷贝到宿主机。
    ```bash
    docker cp mysql_temp:/etc/mysql /docker/mysql/conf
    ```
4.  **处理主配置文件软链接**：MySQL的主配置文件 `my.cnf` 通常是一个软链接，直接拷贝会失效。需要找到原文件并替换。
    *   进入容器查看 `my.cnf` 链接指向的实际文件。
    *   拷贝实际文件到宿主机目录。
    *   删除失效的软链接文件。
    *   将拷贝的实际文件重命名为 `my.cnf`。
5.  **删除临时容器**：
    ```bash
    docker rm -f mysql_temp
    ```

![](img/41cde506cf85dbdeb6e50b632f1b09fd_29.png)

![](img/41cde506cf85dbdeb6e50b632f1b09fd_31.png)

### 创建并运行挂载数据卷的MySQL容器

配置文件准备就绪后，我们可以创建正式的数据卷容器了。

以下是创建容器的命令详解：
```bash
docker run -d \
  --name=mysql \
  -p 3306:3306 \
  -v /docker/mysql/conf:/etc/mysql \
  -v /docker/mysql/data:/var/lib/mysql \
  -v /docker/mysql/logs:/var/log/mysql \
  -e MYSQL_ROOT_PASSWORD=123456 \
  mysql:5.7
```

**参数解释**：
*   `-v /docker/mysql/conf:/etc/mysql`：将宿主机的配置目录挂载到容器，避免配置被覆盖。
*   `-v /docker/mysql/data:/var/lib/mysql`：将数据库文件目录挂载出来，实现数据持久化。
*   `-v /docker/mysql/logs:/var/log/mysql`：将日志目录挂载出来，方便查看。
*   `-e MYSQL_ROOT_PASSWORD=123456`：设置MySQL root用户的密码。

![](img/41cde506cf85dbdeb6e50b632f1b09fd_33.png)

![](img/41cde506cf85dbdeb6e50b632f1b09fd_35.png)

### 验证与测试

![](img/41cde506cf85dbdeb6e50b632f1b09fd_37.png)

容器运行后，我们可以进行验证：

![](img/41cde506cf85dbdeb6e50b632f1b09fd_38.png)

1.  **验证数据卷**：在宿主机 `/docker/mysql/data` 目录下可以看到数据库文件。
2.  **进入MySQL**：在容器内或使用宿主机客户端连接（密码为`123456`）并创建数据库。
    ```bash
    mysql -uroot -p123456
    CREATE DATABASE dbtest;
    ```
3.  **观察数据同步**：在宿主机 `/docker/mysql/data` 目录下，会立即出现 `dbtest` 数据库的文件夹。
4.  **外部连接**：使用数据库客户端工具（如SQLyog），连接宿主机IP的3306端口，可以成功管理容器内的MySQL。

![](img/41cde506cf85dbdeb6e50b632f1b09fd_40.png)

![](img/41cde506cf85dbdeb6e50b632f1b09fd_42.png)

![](img/41cde506cf85dbdeb6e50b632f1b09fd_43.png)

**核心要点**：首次启动时，如果挂载的宿主机目录为空，那么容器内对应目录的内容会被覆盖。因此，像 `配置文件` 这类容器启动必需的、已有的数据，需要提前从容器内拷贝到宿主机目录。而像 `数据文件` 和 `日志文件` 这类容器运行后产生的数据，则可以直接挂载空目录。

![](img/41cde506cf85dbdeb6e50b632f1b09fd_45.png)

---

![](img/41cde506cf85dbdeb6e50b632f1b09fd_47.png)

## 实战二：部署Nginx并配置数据卷 🌐

![](img/41cde506cf85dbdeb6e50b632f1b09fd_49.png)

![](img/41cde506cf85dbdeb6e50b632f1b09fd_51.png)

![](img/41cde506cf85dbdeb6e50b632f1b09fd_53.png)

接下来，我们使用数据卷部署Nginx容器，以实现网页文件、日志和配置的灵活管理。

![](img/41cde506cf85dbdeb6e50b632f1b09fd_55.png)

### 准备工作：提取Nginx配置文件

![](img/41cde506cf85dbdeb6e50b632f1b09fd_57.png)

![](img/41cde506cf85dbdeb6e50b632f1b09fd_59.png)

![](img/41cde506cf85dbdeb6e50b632f1b09fd_61.png)

与MySQL类似，我们需要先获取Nginx的默认配置。

![](img/41cde506cf85dbdeb6e50b632f1b09fd_63.png)

![](img/41cde506cf85dbdeb6e50b632f1b09fd_65.png)

以下是操作步骤：

![](img/41cde506cf85dbdeb6e50b632f1b09fd_67.png)

1.  **运行临时Nginx容器**：
    ```bash
    docker run -d --name nginx_temp nginx:1.22.1
    ```
2.  **创建宿主机存储目录**：
    ```bash
    mkdir -p /docker/nginx
    ```
3.  **拷贝配置文件**：将容器内的整个配置目录拷贝出来。
    ```bash
    docker cp nginx_temp:/etc/nginx /docker/nginx/conf
    ```
4.  **处理模块目录**：`/etc/nginx/modules` 是一个指向 `/usr/lib/nginx/modules` 的软链接。需要将其删除，并重新拷贝实际的模块目录内容。
5.  **删除临时容器**：
    ```bash
    docker rm -f nginx_temp
    ```

![](img/41cde506cf85dbdeb6e50b632f1b09fd_69.png)

### 创建并运行挂载数据卷的Nginx容器

现在创建正式的Nginx容器。

以下是创建容器的命令：
```bash
docker run -d \
  --name=nginx \
  -p 80:80 \
  -v /docker/nginx/conf:/etc/nginx \
  -v /docker/nginx/logs:/var/log/nginx \
  -v /docker/nginx/html:/usr/share/nginx/html \
  nginx:1.22.1
```

**参数解释**：
*   `-v /docker/nginx/conf:/etc/nginx`：挂载配置文件目录。
*   `-v /docker/nginx/logs:/var/log/nginx`：挂载日志目录。
*   `-v /docker/nginx/html:/usr/share/nginx/html`：挂载网页根目录。

### 验证与动态管理

![](img/41cde506cf85dbdeb6e50b632f1b09fd_71.png)

![](img/41cde506cf85dbdeb6e50b632f1b09fd_73.png)

1.  **验证访问**：访问宿主机IP，由于 `html` 目录初始为空，会看到403错误，但日志已生成在 `/docker/nginx/logs` 下。
2.  **部署网页**：将网页文件放入宿主机的 `/docker/nginx/html` 目录，容器内会自动同步。
    ```bash
    cp -r /path/to/your/webpage/* /docker/nginx/html/
    ```
    刷新浏览器即可看到页面。
3.  **修改配置**：直接编辑宿主机 `/docker/nginx/conf/nginx.conf` 文件，修改后需重启容器生效。
    ```bash
    docker restart nginx
    ```

![](img/41cde506cf85dbdeb6e50b632f1b09fd_75.png)

![](img/41cde506cf85dbdeb6e50b632f1b09fd_77.png)

---

## 实战三：部署Tomcat并配置数据卷 🚀

![](img/41cde506cf85dbdeb6e50b632f1b09fd_79.png)

![](img/41cde506cf85dbdeb6e50b632f1b09fd_81.png)

最后，我们部署Tomcat。Tomcat的结构比较集中，通常整个安装目录都需要持久化。

### 准备工作：提取Tomcat全部数据

![](img/41cde506cf85dbdeb6e50b632f1b09fd_83.png)

![](img/41cde506cf85dbdeb6e50b632f1b09fd_84.png)

1.  **运行临时Tomcat容器**：
    ```bash
    docker run -d --name tomcat_temp tomcat
    ```
2.  **创建宿主机存储目录**：
    ```bash
    mkdir -p /docker/tomcat
    ```
3.  **拷贝全部数据**：进入容器后，发现默认路径 (`/usr/local/tomcat`) 下包含了所有文件（conf、logs、webapps等）。直接全部拷贝。
    ```bash
    docker cp tomcat_temp:/usr/local/tomcat /docker/tomcat/
    ```
4.  **删除临时容器**：
    ```bash
    docker rm -f tomcat_temp
    ```

### 创建并运行挂载数据卷的Tomcat容器

![](img/41cde506cf85dbdeb6e50b632f1b09fd_86.png)

![](img/41cde506cf85dbdeb6e50b632f1b09fd_87.png)

![](img/41cde506cf85dbdeb6e50b632f1b09fd_89.png)

创建正式的Tomcat容器，只需挂载一个总目录。

![](img/41cde506cf85dbdeb6e50b632f1b09fd_91.png)

以下是创建容器的命令：
```bash
docker run -d \
  --name=tomcat \
  -p 8080:8080 \
  -v /docker/tomcat:/usr/local/tomcat \
  tomcat
```

![](img/41cde506cf85dbdeb6e50b632f1b09fd_93.png)

![](img/41cde506cf85dbdeb6e50b632f1b09fd_95.png)

**参数解释**：
*   `-v /docker/tomcat:/usr/local/tomcat`：将宿主机目录挂载到Tomcat的安装根目录，实现所有数据（配置、日志、应用、工作目录）的持久化。

### 验证

1.  访问 `http://宿主机IP:8080`，可以看到Tomcat默认页。
2.  应用可以部署到宿主机的 `/docker/tomcat/webapps` 目录。
3.  日志可以在宿主机的 `/docker/tomcat/logs` 目录查看。

![](img/41cde506cf85dbdeb6e50b632f1b09fd_97.png)

![](img/41cde506cf85dbdeb6e50b632f1b09fd_99.png)

![](img/41cde506cf85dbdeb6e50b632f1b09fd_101.png)

---

![](img/41cde506cf85dbdeb6e50b632f1b09fd_103.png)

## 总结 📝

![](img/41cde506cf85dbdeb6e50b632f1b09fd_105.png)

![](img/41cde506cf85dbdeb6e50b632f1b09fd_107.png)

本节课中我们一起学习了Docker容器数据卷的实战应用。通过部署MySQL、Nginx和Tomcat三个案例，我们掌握了：

1.  **数据卷的核心价值**：实现数据持久化、配置外部化管理、宿主机与容器数据实时同步。
2.  **部署通用流程**：
    *   运行临时容器，拷贝出必要的初始文件（尤其是配置文件）。
    *   创建宿主机目录结构。
    *   使用 `docker run -v` 命令挂载数据卷创建正式容器。
3.  **不同服务的挂载策略**：
    *   **MySQL**：需分别挂载配置(`conf`)、数据(`data`)、日志(`logs`)目录。
    *   **Nginx**：需分别挂载配置(`conf`)、日志(`logs`)、网页(`html`)目录。
    *   **Tomcat**：可以简单地将整个安装目录挂载。

![](img/41cde506cf85dbdeb6e50b632f1b09fd_109.png)

利用数据卷，我们无需再频繁进入容器内部操作，所有数据都在宿主机上可见、可管理，极大地简化了容器化应用的管理和维护工作。