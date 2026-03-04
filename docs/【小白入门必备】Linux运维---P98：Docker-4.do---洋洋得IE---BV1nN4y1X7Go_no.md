# Docker容器管理：P98：Docker容器数据卷

![](img/16c8f19ec37eb6a2fede6831472cb0d5_1.png)

![](img/16c8f19ec37eb6a2fede6831472cb0d5_3.png)

![](img/16c8f19ec37eb6a2fede6831472cb0d5_5.png)

## 概述
在本节课中，我们将学习Docker容器数据卷的核心概念与使用方法。数据卷是解决容器数据持久化、容器间数据共享以及宿主机与容器间文件同步的关键技术。我们将通过具体的命令演示，帮助你理解如何创建和使用数据卷。

![](img/16c8f19ec37eb6a2fede6831472cb0d5_7.png)

![](img/16c8f19ec37eb6a2fede6831472cb0d5_8.png)

---

![](img/16c8f19ec37eb6a2fede6831472cb0d5_10.png)

![](img/16c8f19ec37eb6a2fede6831472cb0d5_12.png)

## 从文件拷贝到数据卷的演进

![](img/16c8f19ec37eb6a2fede6831472cb0d5_14.png)

上一节我们介绍了使用`docker cp`命令在宿主机和容器之间拷贝文件。本节中我们来看看数据卷如何提供更优雅和强大的数据管理方案。

![](img/16c8f19ec37eb6a2fede6831472cb0d5_16.png)

![](img/16c8f19ec37eb6a2fede6831472cb0d5_18.png)

`docker cp`命令的基本格式如下：
```bash
docker cp [宿主机路径] [容器名]:[容器内路径]  # 宿主机到容器
docker cp [容器名]:[容器内路径] [宿主机路径]  # 容器到宿主机
```
例如，将宿主机文件拷贝到Nginx容器：
```bash
docker cp /root/some_directory nginx01:/usr/share/nginx/html
```
将容器配置文件拷贝到宿主机：
```bash
docker cp nginx01:/etc/nginx/nginx.conf /opt/
```
虽然拷贝可以实现文件交换，但这种方式是手动、一次性的，无法满足实时同步和持久化的需求。

---

## 容器数据管理的挑战

在深入数据卷之前，我们需要明确容器数据管理面临的几个核心问题：

![](img/16c8f19ec37eb6a2fede6831472cb0d5_20.png)

![](img/16c8f19ec37eb6a2fede6831472cb0d5_21.png)

以下是容器数据管理存在的三个主要问题：
1.  **宿主机与容器间文件交换不便**：虽然可以通过`docker cp`命令实现，但过程繁琐，无法实现自动同步。
2.  **容器与容器间无法直接交换文件**：容器之间是相互隔离的，默认没有共享数据的机制。
3.  **容器删除导致数据丢失**：容器内的数据生命周期与容器本身绑定，容器被删除，其中的数据也会丢失。

![](img/16c8f19ec37eb6a2fede6831472cb0d5_23.png)

![](img/16c8f19ec37eb6a2fede6831472cb0d5_25.png)

数据卷技术正是为了解决这些问题而设计的。

---

## 数据卷的核心概念与优势

数据卷是宿主机上的一个目录或文件，它可以被挂载到一个或多个容器中。其核心优势体现在以下三个方面：

![](img/16c8f19ec37eb6a2fede6831472cb0d5_27.png)

以下是数据卷解决的三大问题：
1.  **实现宿主机与容器的数据实时同步**：挂载后，数据卷目录中的任何更改都会立刻反映到容器内，反之亦然。这简化了配置修改和数据交换。
2.  **实现多个容器间的数据共享**：同一个数据卷可以同时挂载给多个容器，从而实现容器间的数据交换与共享。
3.  **实现容器数据的持久化**：即使容器被删除，数据卷中的内容仍然保留在宿主机上，实现了数据的持久化存储。

![](img/16c8f19ec37eb6a2fede6831472cb0d5_29.png)

![](img/16c8f19ec37eb6a2fede6831472cb0d5_31.png)

---

## 配置与使用数据卷

配置数据卷主要在创建容器时使用 `-v` 参数。其命令格式为：
```bash
docker run -v [宿主机绝对路径]:[容器内绝对路径] [其他参数] [镜像名]
```
如果指定的宿主机路径不存在，Docker会自动创建该目录。

![](img/16c8f19ec37eb6a2fede6831472cb0d5_33.png)

### 实战：部署MySQL并配置数据卷

![](img/16c8f19ec37eb6a2fede6831472cb0d5_34.png)

![](img/16c8f19ec37eb6a2fede6831472cb0d5_36.png)

我们以部署MySQL 5.7为例，演示如何配置数据卷以实现配置文件、数据文件和日志文件的持久化。

![](img/16c8f19ec37eb6a2fede6831472cb0d5_38.png)

![](img/16c8f19ec37eb6a2fede6831472cb0d5_40.png)

![](img/16c8f19ec37eb6a2fede6831472cb0d5_42.png)

首先，在宿主机上创建用于持久化数据的目录：
```bash
mkdir -p /docker_mysql
```

![](img/16c8f19ec37eb6a2fede6831472cb0d5_44.png)

![](img/16c8f19ec37eb6a2fede6831472cb0d5_46.png)

![](img/16c8f19ec37eb6a2fede6831472cb0d5_48.png)

启动一个临时MySQL容器，以便查看其内部的关键路径：
```bash
docker run -d --name temp_mysql -e MYSQL_ROOT_PASSWORD=123456 mysql:5.7
docker exec -it temp_mysql /bin/bash
```
在容器内，我们确认需要持久化的路径：
*   配置文件目录：`/etc/mysql`
*   数据文件目录：`/var/lib/mysql`
*   日志文件目录：`/var/log/mysql`

![](img/16c8f19ec37eb6a2fede6831472cb0d5_50.png)

![](img/16c8f19ec37eb6a2fede6831472cb0d5_52.png)

退出容器后，将配置文件拷贝到宿主机目录（避免首次挂载时容器内配置被空目录覆盖）：
```bash
docker cp temp_mysql:/etc/mysql /docker_mysql/
```
清理临时容器：
```bash
docker rm -f temp_mysql
```

![](img/16c8f19ec37eb6a2fede6831472cb0d5_54.png)

![](img/16c8f19ec37eb6a2fede6831472cb0d5_56.png)

现在，使用数据卷启动正式的MySQL容器：
```bash
docker run -d \
  --name mysql57 \
  -p 3306:3306 \
  -e MYSQL_ROOT_PASSWORD=123456 \
  -v /docker_mysql/mysql:/etc/mysql \
  -v /docker_mysql/lib:/var/lib/mysql \
  -v /docker_mysql/log:/var/log/mysql \
  mysql:5.7
```
此命令创建了一个名为`mysql57`的容器，并将宿主机的三个目录分别挂载到容器内的配置、数据和日志路径。

---

![](img/16c8f19ec37eb6a2fede6831472cb0d5_58.png)

![](img/16c8f19ec37eb6a2fede6831472cb0d5_60.png)

## 其他常用容器管理命令

在结束之前，我们补充两个有用的容器管理命令。

**强制停止容器**：当容器无响应时，可以使用 `docker kill` 命令。
```bash
docker kill [容器名]
```

**查看容器元数据**：`docker inspect` 命令可以查看容器的详细配置信息，包括网络设置、挂载卷、IP地址等。
```bash
docker inspect [容器名]
```
元数据（Metadata）是指描述数据的数据。例如，对于一本书，它的作者、出版社、目录等信息就是这本书的元数据。对于容器，其ID、创建时间、网络配置、挂载卷等信息就是它的元数据。

---

![](img/16c8f19ec37eb6a2fede6831472cb0d5_62.png)

## 总结
本节课中我们一起学习了Docker数据卷的核心知识。我们首先指出了简单文件拷贝的局限性，然后明确了容器数据管理面临的三大挑战。接着，我们深入探讨了数据卷如何通过宿主机目录挂载的方式，完美解决数据实时同步、容器间共享和数据持久化的问题。最后，我们通过部署MySQL的实战演示了数据卷的具体用法。掌握数据卷是进行高效、可靠Docker容器管理的关键一步。