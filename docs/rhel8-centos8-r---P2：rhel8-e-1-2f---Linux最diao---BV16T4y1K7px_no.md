# Linux防火墙管理：P2：firewalld防火墙详解 🔥

![](img/a1cac66a449e51551fcf5ea416f36986_1.png)

![](img/a1cac66a449e51551fcf5ea416f36986_3.png)

在本节课中，我们将学习RHEL 8/CentOS 8系统中firewalld防火墙的四种管理方式：图形化界面、命令行、配置文件以及Web控制台。我们将重点掌握如何添加、删除服务与端口，并理解永久生效与临时生效的区别。

## 图形化管理界面 🖥️

![](img/a1cac66a449e51551fcf5ea416f36986_5.png)

上一节我们介绍了防火墙的基本概念，本节中我们来看看图形化的管理方式。图形化管理通过`firewall-config`命令启动，适合通过点击鼠标进行配置。

![](img/a1cac66a449e51551fcf5ea416f36986_7.png)

启动图形化界面后，可以看到不同的“区域”。默认区域是`public`，通常我们在此区域进行设置。

![](img/a1cac66a449e51551fcf5ea416f36986_9.png)

以下是图形化界面的主要操作步骤：
*   在“服务”列表中勾选需要放行的服务，例如`SSH`。
*   如需放行特定端口，可在“端口”选项卡中添加。
*   关键步骤：点击“选项”，选择“永久”配置，使更改永久生效。
*   更改后，需点击“选项”中的“重载防火墙”使新规则立即生效。

![](img/a1cac66a449e51551fcf5ea416f36986_11.png)

图形化操作完成后，可以通过命令行验证。使用`firewall-cmd --list-all`命令可以查看当前生效的所有规则。

## 命令行管理方式 ⌨️

![](img/a1cac66a449e51551fcf5ea416f36986_13.png)

![](img/a1cac66a449e51551fcf5ea416f36986_15.png)

图形化方式虽然直观，但命令行方式更为常用和高效。本节中我们来看看如何使用`firewall-cmd`命令进行管理。

![](img/a1cac66a449e51551fcf5ea416f36986_17.png)

首先，我们可以查看防火墙状态和基本信息：
*   `firewall-cmd --state`：查看防火墙运行状态。
*   `firewall-cmd --get-default-zone`：查看默认区域。
*   `firewall-cmd --set-default-zone=<zone_name>`：设置默认区域，例如 `firewall-cmd --set-default-zone=work`。

![](img/a1cac66a449e51551fcf5ea416f36986_19.png)

![](img/a1cac66a449e51551fcf5ea416f36986_21.png)

核心操作是添加或移除规则。**重要**：要使规则永久生效，必须在命令中加入`--permanent`参数，否则规则仅为临时生效，重启防火墙后会丢失。

![](img/a1cac66a449e51551fcf5ea416f36986_23.png)

以下是添加和删除规则的命令示例：
*   **添加端口**：`firewall-cmd --permanent --add-port=8080/tcp`
*   **添加服务**：`firewall-cmd --permanent --add-service=http`
*   **删除规则**：`firewall-cmd --permanent --remove-service=http`
*   **重载防火墙**：`firewall-cmd --reload`
*   **查看所有规则**：`firewall-cmd --list-all`

![](img/a1cac66a449e51551fcf5ea416f36986_25.png)

**请注意**：使用`--permanent`参数添加的规则，在删除时也必须使用`--permanent`参数，否则无法真正删除。

![](img/a1cac66a449e51551fcf5ea416f36986_27.png)

## 配置文件管理方式 📄

![](img/a1cac66a449e51551fcf5ea416f36986_29.png)

![](img/a1cac66a449e51551fcf5ea416f36986_31.png)

除了使用命令，我们也可以直接编辑防火墙的配置文件。配置文件位于`/etc/firewalld/`目录下。

*   `zones/`目录：存放各个区域的配置文件，如`public.xml`。
*   `services/`目录：可存放自定义服务的定义文件。

系统预定义的服务文件模板位于`/usr/lib/firewalld/services/`目录下。我们可以参考这些模板（如`http.xml`）来创建或理解服务规则。

![](img/a1cac66a449e51551fcf5ea416f36986_33.png)

![](img/a1cac66a449e51551fcf5ea416f36986_35.png)

**不建议**直接修改`/usr/lib/firewalld/services/`下的文件。如需自定义，可将模板复制到`/etc/firewalld/services/`目录下再进行修改。

![](img/a1cac66a449e51551fcf5ea416f36986_37.png)

![](img/a1cac66a449e51551fcf5ea416f36986_39.png)

直接修改区域配置文件（如`/etc/firewalld/zones/public.xml`）后，需要执行`firewall-cmd --reload`命令重新加载配置才能生效。

![](img/a1cac66a449e51551fcf5ea416f36986_41.png)

![](img/a1cac66a449e51551fcf5ea416f36986_42.png)

## Web控制台管理方式 🌐

![](img/a1cac66a449e51551fcf5ea416f36986_44.png)

![](img/a1cac66a449e51551fcf5ea416f36986_46.png)

![](img/a1cac66a449e51551fcf5ea416f36986_48.png)

RHEL 8/CentOS 8提供了基于Web的Cockpit管理工具，它也可以管理防火墙。首先需要确保Cockpit服务已启动。

![](img/a1cac66a449e51551fcf5ea416f36986_50.png)

![](img/a1cac66a449e51551fcf5ea416f36986_52.png)

![](img/a1cac66a449e51551fcf5ea416f36986_54.png)

通过浏览器访问服务器的9090端口（例如`https://<服务器IP>:9090`），使用系统用户名和密码登录。

![](img/a1cac66a449e51551fcf5ea416f36986_56.png)

在Cockpit界面中，导航至“网络”->“防火墙”部分。在这里可以：
*   查看当前放行的服务。
*   点击“添加服务”，搜索并选择服务（如`http`）进行添加，添加后立即生效。
*   点击服务旁边的垃圾桶图标可以删除该服务。

![](img/a1cac66a449e51551fcf5ea416f36986_58.png)

![](img/a1cac66a449e51551fcf5ea416f36986_60.png)

![](img/a1cac66a449e51551fcf5ea416f36986_61.png)

![](img/a1cac66a449e51551fcf5ea416f36986_63.png)

Cockpit提供了一种直观的图形化操作方式，其效果与命令行和`firewall-config`工具一致。

![](img/a1cac66a449e51551fcf5ea416f36986_65.png)

![](img/a1cac66a449e51551fcf5ea416f36986_67.png)

![](img/a1cac66a449e51551fcf5ea416f36986_69.png)

## 高级规则：富规则 ⚙️

![](img/a1cac66a449e51551fcf5ea416f36986_71.png)

![](img/a1cac66a449e51551fcf5ea416f36986_73.png)

对于更复杂的访问控制需求，firewalld提供了“富规则”功能。富规则允许在单条规则中设置源地址、目标地址、端口、协议等组合条件。

以下是一个富规则示例，它将来自`192.168.43.0/24`网段、访问`5423`端口的TCP流量，转发到本机的`80`端口：
```bash
firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="192.168.43.0/24" forward-port port="5423" protocol="tcp" to-port="80"'
```
添加富规则后，同样需要重载防火墙(`firewall-cmd --reload`)才能生效。富规则的语法较为复杂，通常用于实现端口转发或更精细的流量控制策略。

---

![](img/a1cac66a449e51551fcf5ea416f36986_75.png)

![](img/a1cac66a449e51551fcf5ea416f36986_77.png)

![](img/a1cac66a449e51551fcf5ea416f36986_79.png)

![](img/a1cac66a449e51551fcf5ea416f36986_81.png)

本节课中我们一起学习了firewalld防火墙的四种管理方式。**核心要点**是：使用`firewall-cmd`命令时，务必区分`--permanent`（永久生效）参数的使用；任何配置更改后，记得使用`firewall-cmd --reload`重载配置。对于日常管理，推荐掌握命令行方式；对于复杂规则，可考虑使用富规则或直接编辑配置文件。