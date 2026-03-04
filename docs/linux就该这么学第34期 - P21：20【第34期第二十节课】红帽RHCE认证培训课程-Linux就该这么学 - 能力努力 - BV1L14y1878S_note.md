# Linux就该这么学：第34期：第20节课：DNS服务高级配置与考试安排 📚

![](img/d10fd177a62911fb17f6d97cd6476d6f_0.png)

![](img/d10fd177a62911fb17f6d97cd6476d6f_2.png)

![](img/d10fd177a62911fb17f6d97cd6476d6f_4.png)

在本节课中，我们将深入学习DNS服务的高级配置，包括TSIG加密、缓存服务器和分离解析技术。同时，课程开始时会详细介绍即将到来的红帽RHCE考试安排、约考流程及注意事项。

---

![](img/d10fd177a62911fb17f6d97cd6476d6f_6.png)

## 课程概述

![](img/d10fd177a62911fb17f6d97cd6476d6f_8.png)

本节课内容分为两部分。首先，我们将说明关于红帽RHCE认证考试的重要安排，包括变题时间、考场信息、约考流程和考前准备。随后，我们将进入技术学习环节，深入探讨DNS服务的三个高级主题：使用TSIG加密确保主从服务器间通信安全、配置DNS缓存服务器以提升解析效率，以及实现分离解析技术（CDN原理）。这些知识将帮助您更全面地理解企业级DNS服务的部署与优化。

![](img/d10fd177a62911fb17f6d97cd6476d6f_10.png)

![](img/d10fd177a62911fb17f6d97cd6476d6f_12.png)

![](img/d10fd177a62911fb17f6d97cd6476d6f_14.png)

![](img/d10fd177a62911fb17f6d97cd6476d6f_16.png)

![](img/d10fd177a62911fb17f6d97cd6476d6f_18.png)

---

![](img/d10fd177a62911fb17f6d97cd6476d6f_20.png)

## 第一部分：RHCE考试安排与约考指南 ⏰

近期，红帽认证即将迎来版本更新。根据红帽官方的发布周期，预计在**2022年8月中旬**，考试将从当前的RHEL 8变更为RHEL 9。因此，**6月至8月初是参加当前版本考试的最后窗口期**。对于计划在2023年2月前获得认证的学员，现在报考至关重要。

![](img/d10fd177a62911fb17f6d97cd6476d6f_22.png)

![](img/d10fd177a62911fb17f6d97cd6476d6f_24.png)

![](img/d10fd177a62911fb17f6d97cd6476d6f_26.png)

![](img/d10fd177a62911fb17f6d97cd6476d6f_28.png)

![](img/d10fd177a62911fb17f6d97cd6476d6f_30.png)

以下是已确认的考场时间与地点（每场约10个座位，按报名顺序锁定）：
*   **北京**：6月13日
*   **上海**：6月24日
*   **广州**：6月24日
*   **深圳**：6月22日、6月23日（优先第34期学员）
*   **南京**：6月16日
*   **济南**：6月7日
*   **武汉**：已满员

![](img/d10fd177a62911fb17f6d97cd6476d6f_32.png)

![](img/d10fd177a62911fb17f6d97cd6476d6f_34.png)

其他城市（如天津、青岛、大连等）6月份暂无考位。

![](img/d10fd177a62911fb17f6d97cd6476d6f_36.png)

![](img/d10fd177a62911fb17f6d97cd6476d6f_38.png)

![](img/d10fd177a62911fb17f6d97cd6476d6f_40.png)

### 约考具体流程

![](img/d10fd177a62911fb17f6d97cd6476d6f_42.png)

如果您决定报考，请严格按以下步骤操作：

![](img/d10fd177a62911fb17f6d97cd6476d6f_44.png)

![](img/d10fd177a62911fb17f6d97cd6476d6f_46.png)

**第一步：查找报名QQ并发送信息**
请找到您当初报名付费时联系的QQ号码（可能是 `5604****` 或另一个工作号）。向该QQ发送消息，格式必须为：`城市+具体考试日期`。例如：`北京6月13日`。切勿只发送城市名。

![](img/d10fd177a62911fb17f6d97cd6476d6f_48.png)

![](img/d10fd177a62911fb17f6d97cd6476d6f_50.png)

**第二步：提前注册红帽ID**
在课程QQ群的“文件”中，找到名为“注册红帽ID”的文档。请在上课期间，参照文档步骤提前完成红帽账号注册。这是参加考试的必要条件。

**第三步：等待表格与付款**
工作人员将严格按照大家发送QQ信息的先后顺序进行回复。前10-12位学员会收到考试报名表。收到表格后，请仔细阅读并填写个人信息，随后完成考试费用支付。支付成功后，您将收到确认信息，即代表报考成功。

![](img/d10fd177a62911fb17f6d97cd6476d6f_52.png)

![](img/d10fd177a62911fb17f6d97cd6476d6f_54.png)

**重要提示**：
*   约考处理将从今晚21:00左右开始，工作人员会按顺序回复，请耐心等待。
*   若因疫情导致考场关闭，考试将统一延期。
*   本次未约上的学员，可等待后续考位或7-8月的安排，但届时考位将更加紧张。

![](img/d10fd177a62911fb17f6d97cd6476d6f_56.png)

![](img/d10fd177a62911fb17f6d97cd6476d6f_58.png)

---

![](img/d10fd177a62911fb17f6d97cd6476d6f_60.png)

## 第二部分：DNS服务高级配置 🔧

在了解了考试安排后，我们回归技术学习。上节课我们完成了DNS主从服务器之间正向及反向解析的同步。本节中，我们将在此基础上，进一步提升DNS服务的**安全性**、**效率**和**智能性**。

![](img/d10fd177a62911fb17f6d97cd6476d6f_62.png)

![](img/d10fd177a62911fb17f6d97cd6476d6f_64.png)

![](img/d10fd177a62911fb17f6d97cd6476d6f_66.png)

![](img/d10fd177a62911fb17f6d97cd6476d6f_68.png)

### 一、使用TSIG加密确保主从同步安全 🔐

![](img/d10fd177a62911fb17f6d97cd6476d6f_70.png)

![](img/d10fd177a62911fb17f6d97cd6476d6f_72.png)

在主从服务器同步数据的过程中，查询链路上的任何一次篡改都可能导致后续所有解析结果错误。为了提高安全性，我们可以使用**TSIG（事务签名）** 加密技术，为服务器间的通信增加一个“暗号”验证。

![](img/d10fd177a62911fb17f6d97cd6476d6f_74.png)

![](img/d10fd177a62911fb17f6d97cd6476d6f_76.png)

![](img/d10fd177a62911fb17f6d97cd6476d6f_78.png)

其核心原理是：主从服务器共享一个相同的密钥字符串。当主服务器向从服务器发送数据时，会附带由该密钥生成的签名。从服务器收到后，使用相同的密钥验证签名。只有验证通过，才接受数据，否则拒绝。

![](img/d10fd177a62911fb17f6d97cd6476d6f_80.png)

![](img/d10fd177a62911fb17f6d97cd6476d6f_82.png)

![](img/d10fd177a62911fb17f6d97cd6476d6f_84.png)

![](img/d10fd177a62911fb17f6d97cd6476d6f_86.png)

**配置步骤如下**：

![](img/d10fd177a62911fb17f6d97cd6476d6f_88.png)

![](img/d10fd177a62911fb17f6d97cd6476d6f_90.png)

![](img/d10fd177a62911fb17f6d97cd6476d6f_92.png)

![](img/d10fd177a62911fb17f6d97cd6476d6f_94.png)

1.  **在主服务器上生成密钥**：
    ```bash
    dnssec-keygen -a HMAC-MD5 -b 128 -n HOST master-slave
    ```
    此命令会生成两个文件（`.key`和`.private`），但我们需要的是文件中的密钥字符串。

![](img/d10fd177a62911fb17f6d97cd6476d6f_96.png)

![](img/d10fd177a62911fb17f6d97cd6476d6f_98.png)

![](img/d10fd177a62911fb17f6d97cd6476d6f_100.png)

![](img/d10fd177a62911fb17f6d97cd6476d6f_102.png)

![](img/d10fd177a62911fb17f6d97cd6476d6f_104.png)

![](img/d10fd177a62911fb17f6d97cd6476d6f_106.png)

2.  **在主服务器配置文件中加载密钥**：
    编辑 `/etc/named.conf` 文件，添加密钥定义并允许从服务器使用该密钥。
    ```bash
    key "master-slave" {
        algorithm hmac-md5;
        secret "生成的密钥字符串";
    };
    ```
    然后在 `zone` 区域配置中，使用 `allow-transfer { key master-slave; };` 来指定允许传输的密钥。

![](img/d10fd177a62911fb17f6d97cd6476d6f_108.png)

3.  **在从服务器上配置相同密钥**：
    在从服务器的 `/etc/named.conf` 中，以完全相同的方式定义 `key "master-slave" { ... };`。
    同时，在指向主服务器的 `server` 语句中，添加 `keys { master-slave; };` 来声明使用此密钥进行通信。

![](img/d10fd177a62911fb17f6d97cd6476d6f_110.png)

![](img/d10fd177a62911fb17f6d97cd6476d6f_112.png)

![](img/d10fd177a62911fb17f6d97cd6476d6f_114.png)

![](img/d10fd177a62911fb17f6d97cd6476d6f_116.png)

配置完成后，重启两端的 `named` 服务。此时，只有携带正确密钥签名的同步请求才会被接受，从而保障了数据传输的安全性。

![](img/d10fd177a62911fb17f6d97cd6476d6f_118.png)

![](img/d10fd177a62911fb17f6d97cd6476d6f_120.png)

![](img/d10fd177a62911fb17f6d97cd6476d6f_122.png)

### 二、配置DNS缓存服务器 ⚡

![](img/d10fd177a62911fb17f6d97cd6476d6f_124.png)

![](img/d10fd177a62911fb17f6d97cd6476d6f_126.png)

缓存服务器（Forwarder）通常部署在网络出口（如网关），其核心作用是**加速域名解析并减少外部查询流量**。

![](img/d10fd177a62911fb17f6d97cd6476d6f_128.png)

![](img/d10fd177a62911fb17f6d97cd6476d6f_130.png)

**工作原理**：当内网用户首次请求一个域名（如 `www.linuxprobe.com`）时，缓存服务器会向外部的上游DNS（如 `114.114.114.114`）发起查询，并将结果返回给用户的同时，**在本地保存一份副本**。当其他内网用户再次请求同一域名时，缓存服务器直接返回本地保存的结果，无需再次向外查询，从而显著提升解析速度。

![](img/d10fd177a62911fb17f6d97cd6476d6f_132.png)

![](img/d10fd177a62911fb17f6d97cd6476d6f_134.png)

![](img/d10fd177a62911fb17f6d97cd6476d6f_136.png)

![](img/d10fd177a62911fb17f6d97cd6476d6f_138.png)

![](img/d10fd177a62911fb17f6d97cd6476d6f_140.png)

![](img/d10fd177a62911fb17f6d97cd6476d6f_141.png)

**配置方法非常简单**：
编辑缓存服务器的 `/etc/named.conf` 主配置文件，在 `options { ... }` 段落中添加转发规则即可：
```bash
options {
    forwarders { 114.114.114.114; 8.8.8.8; }; # 指定上游DNS服务器地址
    forward only; # 仅转发，自身不进行递归查询（可选）
};
```
重启 `named` 服务后，将该缓存服务器的IP地址配置为内网客户端的DNS，即可体验加速效果。

![](img/d10fd177a62911fb17f6d97cd6476d6f_143.png)

![](img/d10fd177a62911fb17f6d97cd6476d6f_145.png)

![](img/d10fd177a62911fb17f6d97cd6476d6f_147.png)

![](img/d10fd177a62911fb17f6d97cd6476d6f_149.png)

![](img/d10fd177a62911fb17f6d97cd6476d6f_151.png)

![](img/d10fd177a62911fb17f6d97cd6476d6f_153.png)

![](img/d10fd177a62911fb17f6d97cd6476d6f_155.png)

### 三、实现分离解析（CDN原理） 🌍

![](img/d10fd177a62911fb17f6d97cd6476d6f_157.png)

![](img/d10fd177a62911fb17f6d97cd6476d6f_159.png)

![](img/d10fd177a62911fb17f6d97cd6476d6f_161.png)

![](img/d10fd177a62911fb17f6d97cd6476d6f_163.png)

![](img/d10fd177a62911fb17f6d97cd6476d6f_165.png)

![](img/d10fd177a62911fb17f6d97cd6476d6f_167.png)

![](img/d10fd177a62911fb17f6d97cd6476d6f_169.png)

分离解析，是**CDN（内容分发网络）** 技术的核心原理之一。它能够根据访问者的来源IP地址不同，将同一个域名解析到不同的服务器IP，从而实现就近访问，提升用户体验。

![](img/d10fd177a62911fb17f6d97cd6476d6f_171.png)

![](img/d10fd177a62911fb17f6d97cd6476d6f_173.png)

**应用场景**：假设您的网站 `www.linuxprobe.com` 在北京和美国各有一台服务器。您希望中国用户访问时解析到北京的IP，美国用户访问时解析到美国的IP。

![](img/d10fd177a62911fb17f6d97cd6476d6f_175.png)

**配置步骤**：

![](img/d10fd177a62911fb17f6d97cd6476d6f_177.png)

![](img/d10fd177a62911fb17f6d97cd6476d6f_179.png)

1.  **定义访问者IP地址列表**：
    在 `/etc/named.conf` 中，使用 `acl` 语句定义不同地区的IP网段。
    ```bash
    acl "china" { 122.71.115.0/24; }; // 中国IP段
    acl "america" { 106.185.25.0/24; }; // 美国IP段
    ```

![](img/d10fd177a62911fb17f6d97cd6476d6f_181.png)

![](img/d10fd177a62911fb17f6d97cd6476d6f_183.png)

![](img/d10fd177a62911fb17f6d97cd6476d6f_185.png)

![](img/d10fd177a62911fb17f6d97cd6476d6f_187.png)

2.  **根据来源IP调用不同的区域数据文件**：
    同样在 `/etc/named.conf` 中，为不同的 `acl` 组匹配不同的区域文件。
    ```bash
    view "china" {
        match-clients { "china"; }; // 匹配中国IP
        zone "linuxprobe.com" {
            type master;
            file "linuxprobe.com.china.zone"; // 中国用户使用的数据文件
        };
    };
    view "america" {
        match-clients { "america"; }; // 匹配美国IP
        zone "linuxprobe.com" {
            type master;
            file "linuxprobe.com.america.zone"; // 美国用户使用的数据文件
        };
    };
    ```

![](img/d10fd177a62911fb17f6d97cd6476d6f_189.png)

3.  **准备不同的区域数据文件**：
    创建 `linuxprobe.com.china.zone` 文件，其中 `www` 记录指向北京服务器的IP（如 `122.71.115.1`）。
    创建 `linuxprobe.com.america.zone` 文件，其中 `www` 记录指向美国服务器的IP（如 `106.185.25.1`）。

![](img/d10fd177a62911fb17f6d97cd6476d6f_191.png)

当中国用户查询 `www.linuxprobe.com` 时，DNS服务器会匹配到 `china` 视图，从而读取 `.china.zone` 文件，返回北京的IP。美国用户则会匹配到 `america` 视图，返回美国的IP。这样就实现了智能的、基于地理位置的解析。

![](img/d10fd177a62911fb17f6d97cd6476d6f_193.png)

![](img/d10fd177a62911fb17f6d97cd6476d6f_195.png)

---

![](img/d10fd177a62911fb17f6d97cd6476d6f_197.png)

![](img/d10fd177a62911fb17f6d97cd6476d6f_199.png)

![](img/d10fd177a62911fb17f6d97cd6476d6f_201.png)

![](img/d10fd177a62911fb17f6d97cd6476d6f_203.png)

## 课程总结

本节课我们首先重点沟通了RHCE考试的紧迫性和具体的约考流程，请大家务必重视并按要求操作。在技术层面，我们深入学习了DNS服务的三项高级配置：
1.  **TSIG加密**：通过共享密钥字符串，保障主从服务器间数据同步的安全。
2.  **缓存服务器**：通过本地缓存查询结果，大幅提升内网域名解析效率。
3.  **分离解析**：根据访问者来源IP提供不同的解析结果，是CDN和全球负载均衡的基础。

![](img/d10fd177a62911fb17f6d97cd6476d6f_205.png)

这些内容虽然超出了当前RHCE考试的范围，但对于构建稳健、高效、智能的企业级网络服务体系至关重要。下节课，我们将进入第14章的新内容学习。