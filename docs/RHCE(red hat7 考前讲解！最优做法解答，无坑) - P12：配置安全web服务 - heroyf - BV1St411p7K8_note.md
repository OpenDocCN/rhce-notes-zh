**RHCE 考前讲解：P12：配置安全 Web 服务 🔒**

![](img/a9f6853e157b5c81c860103c49de6f32_1.png)

![](img/a9f6853e157b5c81c860103c49de6f32_3.png)

在本节课中，我们将学习如何为 Apache Web 服务器配置 HTTPS 安全访问。这涉及到安装 SSL/TLS 模块、获取并配置数字证书，以及调整防火墙规则。

![](img/a9f6853e157b5c81c860103c49de6f32_5.png)

---

![](img/a9f6853e157b5c81c860103c49de6f32_7.png)

上一节我们介绍了基础 Web 服务的配置，本节中我们来看看如何为其增加安全层，即配置 HTTPS。

![](img/a9f6853e157b5c81c860103c49de6f32_9.png)

![](img/a9f6853e157b5c81c860103c49de6f32_11.png)

首先，需要安装 Apache 的 SSL 模块。该模块提供了对 HTTPS 协议的支持。

![](img/a9f6853e157b5c81c860103c49de6f32_13.png)

![](img/a9f6853e157b5c81c860103c49de6f32_15.png)

以下是安装命令：
```bash
yum install mod_ssl -y
```

![](img/a9f6853e157b5c81c860103c49de6f32_17.png)

安装完成后，需要进入 Apache 的配置文件目录，并获取所需的数字证书文件。证书通常由考试环境提供。

![](img/a9f6853e157b5c81c860103c49de6f32_19.png)

![](img/a9f6853e157b5c81c860103c49de6f32_21.png)

以下是获取证书文件的命令示例：
```bash
# 进入配置目录
cd /etc/pki/tls/certs

![](img/a9f6853e157b5c81c860103c49de6f32_23.png)

![](img/a9f6853e157b5c81c860103c49de6f32_25.png)

# 下载证书文件（具体文件名和路径需根据题目要求）
wget http://server.domain/certs/server.crt
wget http://server.domain/private/server.key
wget http://server.domain/certs/example.crt
```

![](img/a9f6853e157b5c81c860103c49de6f32_27.png)

接下来，需要修改 SSL 配置文件以指定证书和密钥的路径。

![](img/a9f6853e157b5c81c860103c49de6f32_29.png)

以下是关键配置步骤：
1.  编辑 SSL 配置文件 `/etc/httpd/conf.d/ssl.conf`。
2.  找到 `SSLCertificateFile` 和 `SSLCertificateKeyFile` 指令。
3.  将其值修改为刚才下载的证书和密钥文件的**完整路径**。

![](img/a9f6853e157b5c81c860103c49de6f32_31.png)

![](img/a9f6853e157b5c81c860103c49de6f32_32.png)

例如，修改后的配置行可能如下：
```
SSLCertificateFile /etc/pki/tls/certs/server.crt
SSLCertificateKeyFile /etc/pki/tls/private/server.key
```

配置文件修改完成后，需要重启 Apache 服务以使更改生效。

![](img/a9f6853e157b5c81c860103c49de6f32_34.png)

![](img/a9f6853e157b5c81c860103c49de6f32_36.png)

以下是重启命令：
```bash
systemctl restart httpd
```

![](img/a9f6853e157b5c81c860103c49de6f32_38.png)

此外，必须确保防火墙允许 HTTPS（端口 443）的流量通过。

![](img/a9f6853e157b5c81c860103c49de6f32_40.png)

![](img/a9f6853e157b5c81c860103c49de6f32_42.png)

以下是配置防火墙的命令：
```bash
firewall-cmd --permanent --add-service=https
firewall-cmd --reload
```

![](img/a9f6853e157b5c81c860103c49de6f32_43.png)

![](img/a9f6853e157b5c81c860103c49de6f32_45.png)

最后，可以通过浏览器使用 `https://` 前缀访问你的服务器地址来测试配置。首次访问时，浏览器可能会提示证书不受信任（因为使用的是自签名或测试证书），这属于正常现象，只要能够通过 HTTPS 协议访问页面，即表示配置成功。

![](img/a9f6853e157b5c81c860103c49de6f32_47.png)

---

![](img/a9f6853e157b5c81c860103c49de6f32_49.png)

本节课中我们一起学习了为 Apache Web 服务器配置 HTTPS 的完整流程。核心步骤包括：安装 `mod_ssl` 模块、获取并放置证书文件、修改 SSL 配置文件指定证书路径、重启服务以及配置防火墙规则。掌握这些步骤对于构建安全的 Web 服务至关重要。