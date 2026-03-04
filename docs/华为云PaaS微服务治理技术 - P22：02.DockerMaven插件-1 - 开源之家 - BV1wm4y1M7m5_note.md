# 华为云PaaS微服务治理技术：P22：02.Docker Maven插件-1 🐳

![](img/f93d1c1a2a2f4c3d0d23e2ead06dba54_1.png)

![](img/f93d1c1a2a2f4c3d0d23e2ead06dba54_3.png)

在本节课中，我们将要学习如何使用Docker Maven插件来实现微服务的自动化部署。我们将了解其工作原理、配置步骤，并与手动部署方式进行对比。

## 概述

![](img/f93d1c1a2a2f4c3d0d23e2ead06dba54_5.png)

微服务的部署有两种主要方法：手动部署和自动部署。手动部署需要将工程打包并编写Dockerfile，过程较为繁琐。而自动部署则通过集成Maven插件，只需执行一条Maven命令即可完成从构建到部署的全过程，极大地提升了效率。本节我们将重点介绍如何配置和使用Docker Maven插件。

![](img/f93d1c1a2a2f4c3d0d23e2ead06dba54_7.png)

## 手动部署与自动部署对比

![](img/f93d1c1a2a2f4c3d0d23e2ead06dba54_9.png)

![](img/f93d1c1a2a2f4c3d0d23e2ead06dba54_11.png)

上一节我们提到了两种部署方式。手动部署的步骤如下：
1.  将微服务工程导出为JAR包。
2.  编写Dockerfile文件。
3.  执行Docker命令构建镜像并运行容器。

![](img/f93d1c1a2a2f4c3d0d23e2ead06dba54_13.png)

![](img/f93d1c1a2a2f4c3d0d23e2ead06dba54_15.png)

这种方式步骤较多，容易出错。因此，我们引入一种更简单的方法：使用Docker Maven插件。添加此插件后，可以直接在本地工程中执行Maven命令，实现Docker容器的自动部署。

![](img/f93d1c1a2a2f4c3d0d23e2ead06dba54_17.png)

## 配置宿主机Docker以支持远程访问

![](img/f93d1c1a2a2f4c3d0d23e2ead06dba54_19.png)

![](img/f93d1c1a2a2f4c3d0d23e2ead06dba54_20.png)

![](img/f93d1c1a2a2f4c3d0d23e2ead06dba54_22.png)

在开始使用插件之前，我们需要先修改宿主机的Docker配置。这是因为，当我们在本地工程执行Maven命令时，对于宿主机上的Docker守护进程而言，这实际上是一次远程操作。而Docker默认关闭了远程访问功能。

![](img/f93d1c1a2a2f4c3d0d23e2ead06dba54_24.png)

以下是开启远程访问的步骤：

![](img/f93d1c1a2a2f4c3d0d23e2ead06dba54_26.png)

![](img/f93d1c1a2a2f4c3d0d23e2ead06dba54_28.png)

我们需要修改Docker的systemd服务配置文件。

![](img/f93d1c1a2a2f4c3d0d23e2ead06dba54_30.png)

![](img/f93d1c1a2a2f4c3d0d23e2ead06dba54_32.png)

1.  打开配置文件：
    ```bash
    vi /lib/systemd/system/docker.service
    ```

![](img/f93d1c1a2a2f4c3d0d23e2ead06dba54_34.png)

2.  找到以 `ExecStart` 开头的行，在其后添加 `-H tcp://0.0.0.0:2375 -H unix:///var/run/docker.sock` 配置。
    *   修改后的行可能类似：
    ```
    ExecStart=/usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock -H tcp://0.0.0.0:2375 -H unix:///var/run/docker.sock
    ```

3.  保存并退出编辑器。

修改配置后，需要重新加载配置并重启Docker服务。

![](img/f93d1c1a2a2f4c3d0d23e2ead06dba54_36.png)

![](img/f93d1c1a2a2f4c3d0d23e2ead06dba54_38.png)

依次执行以下命令：
```bash
systemctl daemon-reload
systemctl restart docker
```

![](img/f93d1c1a2a2f4c3d0d23e2ead06dba54_40.png)

![](img/f93d1c1a2a2f4c3d0d23e2ead06dba54_42.png)

## 在Maven工程中配置Docker插件

配置好宿主机后，我们现在需要在需要进行Docker部署的Maven工程的 `pom.xml` 文件中添加插件配置。

以下是一个配置示例，我们将其添加到项目的 `pom.xml` 文件中：

![](img/f93d1c1a2a2f4c3d0d23e2ead06dba54_44.png)

```xml
<build>
    <finalName>app</finalName>
    <plugins>
        <!-- Spring Boot Maven 插件 -->
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
        <!-- Docker Maven 插件 -->
        <plugin>
            <groupId>com.spotify</groupId>
            <artifactId>docker-maven-plugin</artifactId>
            <version>1.0.0</version>
            <configuration>
                <!-- 生成的镜像名称，格式为 仓库/镜像名:标签 -->
                <imageName>192.168.66.100:5000/${project.artifactId}:${project.version}</imageName>
                <!-- 基础镜像，相当于 Dockerfile 中的 FROM -->
                <baseImage>jdk1.8</baseImage>
                <!-- 容器启动时执行的命令，相当于 Dockerfile 中的 ENTRYPOINT -->
                <entryPoint>["java", "-jar", "/${project.build.finalName}.jar"]</entryPoint>
                <resources>
                    <resource>
                        <!-- 指定要复制到镜像中的文件路径 -->
                        <targetPath>/</targetPath>
                        <!-- 指定要复制的文件，这里指打包后的JAR文件 -->
                        <directory>${project.build.directory}</directory>
                        <include>${project.build.finalName}.jar</include>
                    </resource>
                </resources>
            </configuration>
        </plugin>
    </plugins>
</build>
```

![](img/f93d1c1a2a2f4c3d0d23e2ead06dba54_46.png)

现在，我们来详细解析一下这个配置中各个部分的作用：

*   **`<imageName>`**：此配置定义了最终生成的Docker镜像的名称。例如 `192.168.66.100:5000/tensquare-base:1.0-SNAPSHOT`，它对应 `docker images` 列表中的镜像名。
*   **`<baseImage>`**：此配置指定了基础镜像，其作用等同于Dockerfile中的 `FROM jdk1.8` 指令。
*   **`<entryPoint>`**：此配置定义了容器启动时的入口点命令。它相当于Dockerfile中的 `ENTRYPOINT ["java", "-jar", "/app.jar"]`。配置中的 `${project.build.finalName}` 会被替换为上面定义的 `app`。
*   **`<resources>`**：此配置指定了需要复制到Docker镜像中的资源文件。它相当于Dockerfile中的 `ADD target/app.jar /` 指令。`<directory>` 指定源文件目录（通常是 `target`），`<include>` 指定要包含的文件（打包后的JAR文件）。

![](img/f93d1c1a2a2f4c3d0d23e2ead06dba54_48.png)

通过以上配置，Docker Maven插件就替代了手动编写Dockerfile的过程，将代码编译、打包和构建Docker镜像的步骤整合在一起。

![](img/f93d1c1a2a2f4c3d0d23e2ead06dba54_50.png)

## 总结

![](img/f93d1c1a2a2f4c3d0d23e2ead06dba54_52.png)

![](img/f93d1c1a2a2f4c3d0d23e2ead06dba54_54.png)

本节课中我们一起学习了Docker Maven插件的初步使用。我们首先对比了手动部署与自动部署的优劣，然后完成了两个关键配置：一是修改宿主机Docker配置以开启远程访问；二是在Maven工程的 `pom.xml` 文件中添加并配置Docker Maven插件。该插件的配置核心定义了镜像名称、基础镜像、启动命令和需要加入镜像的文件，从而实现了“一键式”的镜像构建与部署流程，简化了微服务的部署操作。