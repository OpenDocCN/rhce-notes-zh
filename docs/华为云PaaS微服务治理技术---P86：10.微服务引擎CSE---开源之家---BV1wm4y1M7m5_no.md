# 华为云PaaS微服务治理技术：P86：10.微服务引擎CSE-ServiceComb项目接入CSE 🚀

![](img/370a16bcd583510d245af78ebce2d266_1.png)

![](img/370a16bcd583510d245af78ebce2d266_3.png)

在本节课中，我们将学习如何将一个基于ServiceComb框架开发的微服务项目，接入到华为云微服务引擎CSE平台。我们将看到，由于ServiceComb与CSE同源，这个过程可以实现近乎零代码修改的平滑迁移。

上一节我们介绍了微服务引擎CSE的基本概念，本节中我们来看看如何将ServiceComb项目接入CSE。

![](img/370a16bcd583510d245af78ebce2d266_5.png)

![](img/370a16bcd583510d245af78ebce2d266_6.png)

![](img/370a16bcd583510d245af78ebce2d266_8.png)

## 概述

![](img/370a16bcd583510d245af78ebce2d266_10.png)

![](img/370a16bcd583510d245af78ebce2d266_12.png)

CSE是ServiceComb的商业版本，它依赖于华为云平台。我们的目标是将一个本地运行的ServiceComb项目，改造为向云平台上的CSE服务注册中心注册，从而实现服务治理上云。整个过程主要分为两步：准备华为云环境，以及修改项目配置。

![](img/370a16bcd583510d245af78ebce2d266_14.png)

![](img/370a16bcd583510d245af78ebce2d266_16.png)

![](img/370a16bcd583510d245af78ebce2d266_17.png)

## 接入流程详解

![](img/370a16bcd583510d245af78ebce2d266_19.png)

![](img/370a16bcd583510d245af78ebce2d266_21.png)

### 第一步：准备华为云环境

由于CSE依赖于云平台，因此首先需要拥有华为云账号并获取必要的安全凭证。

![](img/370a16bcd583510d245af78ebce2d266_23.png)

![](img/370a16bcd583510d245af78ebce2d266_24.png)

以下是需要完成的操作：
1.  **注册/登录华为云账号**：访问华为云官网并完成账号注册与登录。
2.  **获取访问密钥（AK/SK）**：登录后，在“控制台” > “我的凭证”中，创建并获取访问密钥（AK和SK）。这是本地服务与云平台安全通信的凭证。

### 第二步：改造ServiceComb项目

![](img/370a16bcd583510d245af78ebce2d266_26.png)

![](img/370a16bcd583510d245af78ebce2d266_27.png)

![](img/370a16bcd583510d245af78ebce2d266_28.png)

我们将基于一个已开发完成的ServiceComb项目（例如一个简单的“Hello World”服务）进行改造。核心原则是**修改依赖和配置文件，而不修改业务代码**。

![](img/370a16bcd583510d245af78ebce2d266_30.png)

![](img/370a16bcd583510d245af78ebce2d266_32.png)

![](img/370a16bcd583510d245af78ebce2d266_34.png)

#### 1. 修改父工程依赖

![](img/370a16bcd583510d245af78ebce2d266_36.png)

![](img/370a16bcd583510d245af78ebce2d266_38.png)

在项目的父工程`pom.xml`中，将原有的ServiceComb SDK依赖替换为CSE的依赖管理。

![](img/370a16bcd583510d245af78ebce2d266_40.png)

**代码示例：**
```xml
<!-- 注释或删除原有的ServiceComb依赖 -->
<!-- <dependency> -->
<!--     <groupId>org.apache.servicecomb</groupId> -->
<!--     <artifactId>java-chassis-dependencies</artifactId> -->
<!--     <version>${servicecomb.version}</version> -->
<!--     <type>pom</type> -->
<!--     <scope>import</scope> -->
<!-- </dependency> -->

<!-- 引入CSE依赖 -->
<dependency>
    <groupId>com.huawei.paas.cse</groupId>
    <artifactId>cse-dependency</artifactId>
    <version>${cse.version}</version>
    <type>pom</type>
    <scope>import</scope>
</dependency>
```

![](img/370a16bcd583510d245af78ebce2d266_42.png)

![](img/370a16bcd583510d245af78ebce2d266_43.png)

#### 2. 修改子模块（服务提供方/消费方）依赖

![](img/370a16bcd583510d245af78ebce2d266_45.png)

![](img/370a16bcd583510d245af78ebce2d266_46.png)

在具体的服务模块（如`provider`、`consumer`）的`pom.xml`中，添加CSE服务引擎依赖。

以下是需要完成的操作：
*   在原有依赖的基础上，添加`cse-solution-service-engine`依赖。
*   为避免日志包冲突，需要排除该依赖中自带的`slf4j-log4j12`。

**代码示例：**
```xml
<dependency>
    <groupId>com.huawei.paas.cse</groupId>
    <artifactId>cse-solution-service-engine</artifactId>
    <exclusions>
        <exclusion>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```

![](img/370a16bcd583510d245af78ebce2d266_48.png)

#### 3. 修改配置文件

![](img/370a16bcd583510d245af78ebce2d266_50.png)

![](img/370a16bcd583510d245af78ebce2d266_51.png)

![](img/370a16bcd583510d245af78ebce2d266_53.png)

这是最关键的一步。我们需要将原本指向本地ServiceCenter的配置，改为指向云平台CSE的地址。

![](img/370a16bcd583510d245af78ebce2d266_55.png)

*   **原ServiceComb配置** (`microservice.yaml`):
    ```yaml
    servicecomb:
      service:
        registry:
          address: http://127.0.0.1:30100
      rest:
        address: 0.0.0.0:8080
    ```

![](img/370a16bcd583510d245af78ebce2d266_57.png)

*   **改造后的CSE配置**:
    ```yaml
    # 注意：前缀由 servicecomb 改为 cse
    cse:
      service:
        registry:
          address: https://cse.cn-north-1.myhuaweicloud.com
      credentials:
        accessKey: your_access_key  # 替换为你的AK
        secretKey: your_secret_key  # 替换为你的SK
        project: cn-north-1
      rest:
        address: 0.0.0.0:8080
    ```
    **公式说明：**
    *   `cse.service.registry.address`：华为云CSE服务注册中心的**公网地址**。
    *   `cse.credentials`：包含从第一步获取的**AK/SK**，用于身份认证。
    *   `cse.rest.address`：当前服务运行的**本地端口**。

![](img/370a16bcd583510d245af78ebce2d266_59.png)

**服务消费方（Consumer）的配置文件也需进行完全相同的修改**，只需将其`rest.address`改为自己的端口（如8081）。

### 第三步：验证与测试

![](img/370a16bcd583510d245af78ebce2d266_61.png)

完成配置后，启动服务提供方和消费方。

![](img/370a16bcd583510d245af78ebce2d266_63.png)

以下是验证步骤：
1.  **查看云平台控制台**：登录华为云控制台，进入“微服务引擎CSE” > “微服务管理”。在“服务目录”中，应能看到刚启动的服务（如`hello-world-provider`和`hello-world-consumer`）。
2.  **查看服务契约**：点击服务名称，在“服务契约”标签页中可以查看接口的详细信息，确认接口定义已正确注册。
3.  **发起接口调用**：通过浏览器或工具（如Postman）访问本地消费方接口（如`http://localhost:8081/request/黑马`）。消费方会从云平台CSE获取提供方地址并完成调用，最终返回预期结果。

![](img/370a16bcd583510d245af78ebce2d266_65.png)

![](img/370a16bcd583510d245af78ebce2d266_67.png)

## 注意事项 ⚠️

![](img/370a16bcd583510d245af78ebce2d266_69.png)

![](img/370a16bcd583510d245af78ebce2d266_71.png)

在开发过程中，如果修改了服务的接口（如URL路径、HTTP方法），需要特别注意：

![](img/370a16bcd583510d245af78ebce2d266_73.png)

![](img/370a16bcd583510d245af78ebce2d266_74.png)

*   **服务实例更新**：CSE注册中心会保留旧的服务实例信息。修改接口后重启服务，新实例会注册，但旧实例可能仍存在。
*   **解决方案**：在华为云CSE控制台的“服务目录”中，找到对应的微服务，将其**删除**。然后重启你的应用，它会以全新的配置重新注册。这样可以确保服务契约与代码定义完全一致。

![](img/370a16bcd583510d245af78ebce2d266_76.png)

![](img/370a16bcd583510d245af78ebce2d266_77.png)

## 总结

![](img/370a16bcd583510d245af78ebce2d266_79.png)

![](img/370a16bcd583510d245af78ebce2d266_80.png)

![](img/370a16bcd583510d245af78ebce2d266_82.png)

本节课中我们一起学习了将ServiceComb项目接入华为云CSE的完整流程。整个过程非常简洁，核心在于：
1.  **切换依赖**：将`servicecomb`依赖替换为`cse`依赖。
2.  **修改配置**：在`microservice.yaml`中，将注册中心地址改为CSE公网地址，并配置安全的AK/SK凭证。

![](img/370a16bcd583510d245af78ebce2d266_84.png)

![](img/370a16bcd583510d245af78ebce2d266_85.png)

通过以上改造，我们便实现了微服务从本地部署到云平台治理的平滑迁移，后续即可利用CSE提供的全套微服务治理能力。下一节，我们将探讨如何将更主流的Spring Cloud项目接入CSE。