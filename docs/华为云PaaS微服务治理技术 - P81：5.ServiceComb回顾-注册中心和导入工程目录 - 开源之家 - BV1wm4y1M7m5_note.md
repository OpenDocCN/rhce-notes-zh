# 华为云PaaS微服务治理技术 - P81：5.ServiceComb回顾-注册中心和导入工程目录 🚀

![](img/26cbac90ff6b36387f8fb1a6c4dc1e32_1.png)

![](img/26cbac90ff6b36387f8fb1a6c4dc1e32_2.png)

在本节课中，我们将回顾Apache ServiceComb微服务框架的核心开发流程，重点讲解其注册中心的作用与启动方法，并完成基础工程目录的导入。这为后续学习华为云微服务引擎CSE（基于ServiceComb的商业版本）打下坚实基础。

## 什么是ServiceComb？ 🤔

![](img/26cbac90ff6b36387f8fb1a6c4dc1e32_4.png)

![](img/26cbac90ff6b36387f8fb1a6c4dc1e32_5.png)

![](img/26cbac90ff6b36387f8fb1a6c4dc1e32_6.png)

![](img/26cbac90ff6b36387f8fb1a6c4dc1e32_7.png)

![](img/26cbac90ff6b36387f8fb1a6c4dc1e32_8.png)

ServiceComb是华为在2017年开源的一款微服务框架。它与Spring Cloud类似，都是用于微服务开发和治理的框架。ServiceComb在华为内部经过了长期实践，积累了丰富的经验。该项目于2017年12月进入Apache孵化器，成为Apache旗下的开源项目。

![](img/26cbac90ff6b36387f8fb1a6c4dc1e32_10.png)

![](img/26cbac90ff6b36387f8fb1a6c4dc1e32_12.png)

## 为何要学习ServiceComb？ 💡

![](img/26cbac90ff6b36387f8fb1a6c4dc1e32_13.png)

![](img/26cbac90ff6b36387f8fb1a6c4dc1e32_14.png)

上一节我们介绍了ServiceComb的基本概念，本节中我们来看看学习它的原因。你可能会问，既然Spring Cloud已经很好用，为何还要学习ServiceComb？

以下是两个主要原因：

![](img/26cbac90ff6b36387f8fb1a6c4dc1e32_16.png)

![](img/26cbac90ff6b36387f8fb1a6c4dc1e32_17.png)

1.  **无缝接入华为云平台**：华为云的微服务引擎CSE是ServiceComb的商业版本。如果采用ServiceComb开发项目，可以非常顺畅地接入华为云平台进行治理。虽然Spring Cloud项目也能接入，但ServiceComb是华为云平台的底层基础框架。
2.  **相比Spring Cloud的优势**：ServiceComb相比Spring Cloud具备一些优势。
    *   **协议支持更丰富**：Spring Cloud主要支持基于HTTP的REST协议。而ServiceComb不仅支持REST，还支持性能更高的**RPC**协议。
    *   **提供一站式解决方案**：ServiceComb的商业版本CSE不仅是一个开发框架，更是一个平台。它提供了基于云平台的微服务部署、管理、治理等**一站式解决方案**，这是Spring Cloud所不具备的。

![](img/26cbac90ff6b36387f8fb1a6c4dc1e32_18.png)

## 回顾开发流程：注册中心 🏢

了解了ServiceComb及其优势后，我们开始回顾其核心开发流程。微服务开发的第一步通常是搭建注册中心，因为服务之间需要相互发现和调用。

注册中心的作用如下图所示：微服务启动后，会将自己的信息（如服务名、地址）注册到注册中心。当微服务A需要调用微服务B时，会先从注册中心查询B的地址，然后再进行调用。

![](img/26cbac90ff6b36387f8fb1a6c4dc1e32_20.png)

![](img/26cbac90ff6b36387f8fb1a6c4dc1e32_21.png)

接下来，我们启动ServiceComb自带的注册中心。

![](img/26cbac90ff6b36387f8fb1a6c4dc1e32_23.png)

![](img/26cbac90ff6b36387f8fb1a6c4dc1e32_24.png)

1.  **下载注册中心**：从ServiceComb官网下载注册中心程序，提供Linux和Windows版本。本例使用Windows版本。
2.  **启动注册中心服务**：在下载目录中，找到 `start-service-center.bat`（Windows）脚本并双击运行。这将启动注册中心的后台服务进程。
3.  **启动注册中心前端界面**：双击运行 `start-frontend.bat`（Windows）脚本。这将启动一个Web管理界面，用于查看注册中心内的服务信息。
4.  **访问管理界面**：前端界面默认运行在 `30103` 端口。在浏览器中访问 `http://localhost:30103`，即可看到管理界面。当前界面中仅显示注册中心自身的服务（`SERVICECENTER`），我们自行开发的服务注册后也会显示在这里。

至此，服务中心已成功启动。

## 回顾开发流程：导入工程目录 📁

![](img/26cbac90ff6b36387f8fb1a6c4dc1e32_26.png)

上一节我们启动了注册中心，本节中我们来看看如何准备开发环境。我们将开发一个简单的“Hello World”示例，包含服务提供方和服务消费方。

以下是该示例的工程结构，我们将快速导入到IDE中：

![](img/26cbac90ff6b36387f8fb1a6c4dc1e32_28.png)

![](img/26cbac90ff6b36387f8fb1a6c4dc1e32_29.png)

![](img/26cbac90ff6b36387f8fb1a6c4dc1e32_30.png)

*   **`api` 工程**：存放服务接口定义。
*   **`model` 工程**：存放数据模型对象。
*   **`provider` 工程**：服务提供方实现。
*   **`consumer` 工程**：服务消费方实现。

![](img/26cbac90ff6b36387f8fb1a6c4dc1e32_32.png)

![](img/26cbac90ff6b36387f8fb1a6c4dc1e32_34.png)

导入步骤如下：

![](img/26cbac90ff6b36387f8fb1a6c4dc1e32_36.png)

1.  在你本地的工作目录中，创建文件夹（例如 `test-servicecomb`），并将准备好的四个工程文件夹（`api`, `model`, `provider`, `consumer`）复制进去。
2.  使用IntelliJ IDEA打开你刚创建的根目录文件夹（`test-servicecomb`）。
3.  在IDEA中，依次将这四个子目录作为Maven模块导入到项目中。具体操作是：`File` -> `New` -> `Module from Existing Sources...`，然后选择每个子目录下的 `pom.xml` 文件。
4.  导入成功后，项目结构会显示为标准的Maven模块图标。检查各工程，目前它们大多是空的结构，后续我们将填充代码。

![](img/26cbac90ff6b36387f8fb1a6c4dc1e32_38.png)

导入完成后，请注意父工程的 `pom.xml` 已经包含了一些基础依赖（如MyBatis、通用工具包）。`model` 工程中预定义了一个 `Student` 类，`api` 和 `consumer` 工程目前是空的，`provider` 工程只有一个启动类（可能报错，暂时忽略）。

## 总结 📝

![](img/26cbac90ff6b36387f8fb1a6c4dc1e32_40.png)

![](img/26cbac90ff6b36387f8fb1a6c4dc1e32_41.png)

![](img/26cbac90ff6b36387f8fb1a6c4dc1e32_42.png)

本节课中我们一起学习了ServiceComb的回顾内容。我们首先了解了ServiceComb是什么以及学习它的必要性。然后，我们重点回顾了其开发流程的两个起点：**启动注册中心**和**导入基础工程目录**。注册中心是微服务协同工作的基石，而清晰的工程结构是高效开发的前提。在接下来的课程中，我们将基于这个工程结构，快速编写服务提供方和消费方的代码，以完整回顾ServiceComb的开发流程，为后续的华为云CSE学习做好铺垫。