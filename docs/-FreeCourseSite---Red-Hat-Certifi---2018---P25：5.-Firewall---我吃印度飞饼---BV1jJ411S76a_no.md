# 🔥 Firewalld 教程：P25：5. 向防火墙规则添加端口

在本节课中，我们将学习如何为防火墙区域添加端口规则。当预定义的防火墙服务无法满足您的应用程序需求时，您有两种选择：直接为区域开放端口，或者定义一个新的服务。本节我们将重点介绍第一种方法，即如何开放单个端口或端口范围。

---

## 为区域开放端口

为特定应用程序添加支持的最简单方法，是在相应的防火墙区域中开放其使用的端口。这需要指定要开放的端口（或端口范围）及其关联的协议（TCP 或 UDP）。

例如，如果我们的应用程序运行在端口 5000 上并使用 TCP 协议，我们可以使用以下命令将其添加到 `public` 区域的当前会话配置中：

```bash
firewall-cmd --zone=public --add-port=5000/tcp
```

命令执行后，我们可以验证端口是否已成功添加。以下是列出 `public` 区域所有已开放端口的方法：

![](img/2f1e5b01ec66e3c464e5b17e4cfca34f_1.png)

```bash
firewall-cmd --zone=public --list-ports
```
执行此命令后，您将看到已开放的端口列表，例如之前已开放的 80 端口和新添加的 5000 端口。

![](img/2f1e5b01ec66e3c464e5b17e4cfca34f_3.png)

---

## 开放连续的端口范围

![](img/2f1e5b01ec66e3c464e5b17e4cfca34f_5.png)

您还可以通过指定起始和结束端口来开放一个连续的端口范围，中间用连字符分隔。

例如，如果您的应用程序使用 UDP 端口 4990 到 4999，您可以通过以下命令在 `public` 区域开放这些端口：

```bash
firewall-cmd --zone=public --add-port=4990-4999/udp
```

![](img/2f1e5b01ec66e3c464e5b17e4cfca34f_7.png)

![](img/2f1e5b01ec66e3c464e5b17e4cfca34f_9.png)

---

![](img/2f1e5b01ec66e3c464e5b17e4cfca34f_11.png)

## 将规则设为永久生效

以上添加的规则默认只对当前会话有效，重启后会失效。在测试确认无误后，您需要将这些规则添加到永久配置中，以确保重启后依然生效。

![](img/2f1e5b01ec66e3c464e5b17e4cfca34f_13.png)

![](img/2f1e5b01ec66e3c464e5b17e4cfca34f_15.png)

以下是将之前添加的 TCP 5000 端口规则设为永久生效的命令：

![](img/2f1e5b01ec66e3c464e5b17e4cfca34f_17.png)

```bash
firewall-cmd --zone=public --permanent --add-port=5000/tcp
```

同样地，将 UDP 端口范围 4990-4999 的规则设为永久生效：

![](img/2f1e5b01ec66e3c464e5b17e4cfca34f_19.png)

![](img/2f1e5b01ec66e3c464e5b17e4cfca34f_21.png)

```bash
firewall-cmd --zone=public --permanent --add-port=4990-4999/udp
```

![](img/2f1e5b01ec66e3c464e5b17e4cfca34f_23.png)

![](img/2f1e5b01ec66e3c464e5b17e4cfca34f_25.png)

添加永久规则后，您可以使用以下命令列出 `public` 区域的永久端口配置以进行验证：

![](img/2f1e5b01ec66e3c464e5b17e4cfca34f_27.png)

![](img/2f1e5b01ec66e3c464e5b17e4cfca34f_29.png)

```bash
firewall-cmd --zone=public --permanent --list-ports
```
执行此命令后，您应该能看到所有已配置的永久端口，例如 80、5000 以及 4990-4999。

![](img/2f1e5b01ec66e3c464e5b17e4cfca34f_31.png)

---

![](img/2f1e5b01ec66e3c464e5b17e4cfca34f_33.png)

![](img/2f1e5b01ec66e3c464e5b17e4cfca34f_35.png)

## 本节总结

![](img/2f1e5b01ec66e3c464e5b17e4cfca34f_37.png)

本节课中，我们一起学习了如何通过 `firewall-cmd` 工具为防火墙区域添加端口规则。主要内容包括：
1.  **开放单个端口**：使用 `--add-port=<端口号>/<协议>` 参数。
2.  **开放端口范围**：使用 `--add-port=<起始端口>-<结束端口>/<协议>` 的格式。
3.  **使规则永久生效**：在命令中添加 `--permanent` 参数。
4.  **验证配置**：使用 `--list-ports` 参数查看已开放的端口。

![](img/2f1e5b01ec66e3c464e5b17e4cfca34f_39.png)

通过掌握这些命令，您可以根据应用程序的具体需求，灵活地配置防火墙的端口访问规则。