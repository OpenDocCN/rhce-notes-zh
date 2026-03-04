# RHCE 考前讲解：P13：配置本地邮件服务 📧

![](img/1893500d7d7177e135492486ed631f1b_0.png)

在本节课中，我们将学习如何在 Red Hat 7 系统上配置本地邮件服务。我们将按照考试要求，修改 Postfix 邮件服务器的配置文件，确保邮件能够正确路由。整个过程步骤清晰，旨在提供最优做法，避免常见错误。

---

## 准备工作

![](img/1893500d7d7177e135492486ed631f1b_2.png)

首先，我们需要进入 Postfix 的主配置目录。

```
cd /etc/postfix
```

![](img/1893500d7d7177e135492486ed631f1b_4.png)

进入目录后，建议先备份原始配置文件，这是一个良好的操作习惯。

![](img/1893500d7d7177e135492486ed631f1b_5.png)

以下是备份命令：

![](img/1893500d7d7177e135492486ed631f1b_7.png)

```
cp -a main.cf main.cf.bak
```

这条命令将 `main.cf` 文件复制一份，并命名为 `main.cf.bak`，以便在配置出错时可以快速恢复。

---

![](img/1893500d7d7177e135492486ed631f1b_9.png)

## 配置服务器端 (server)

上一节我们完成了准备工作，本节中我们来看看如何修改服务器端（server）的 Postfix 配置文件。

![](img/1893500d7d7177e135492486ed631f1b_11.png)

我们需要编辑 `main.cf` 文件，并修改其中的几个关键参数。

![](img/1893500d7d7177e135492486ed631f1b_13.png)

以下是需要修改的行及其内容：

1.  **第75行：`myhostname`**
    此参数应设置为考试题目中指定的主机名或域名。
    ```
    myhostname = server.example.com
    ```

2.  **第83行：`mydomain`**
    此参数应设置为域的总称。
    ```
    mydomain = example.com
    ```

3.  **第99行：`myorigin`**
    此行通常被注释，需要去掉行首的 `#` 号以启用。
    ```
    myorigin = $mydomain
    ```

4.  **第164行：`mydestination`**
    此参数定义了本机接收邮件的域名。需要删除其后的所有内容，仅保留 `localhost` 和 `localhost.localdomain`。
    ```
    mydestination = $myhostname, localhost.$mydomain, localhost, $mydomain
    ```
    修改后应为：
    ```
    mydestination = localhost, localhost.localdomain
    ```

![](img/1893500d7d7177e135492486ed631f1b_15.png)

5.  **第316行：`relayhost`**
    此参数定义了邮件的中继路由。根据题目要求，所有发出的邮件都需要路由到指定主机。
    ```
    relayhost = [server.example.com]
    ```

![](img/1893500d7d7177e135492486ed631f1b_17.png)

配置文件修改完成后，需要重启 Postfix 服务以使更改生效。

![](img/1893500d7d7177e135492486ed631f1b_18.png)

```
systemctl restart postfix
```

---

![](img/1893500d7d7177e135492486ed631f1b_20.png)

## 配置客户端 (desktop)

![](img/1893500d7d7177e135492486ed631f1b_22.png)

服务器端配置完成后，我们同样需要在客户端（desktop）上进行类似的配置。

客户端配置的步骤与服务器端基本相同，但部分参数的值会根据主机名不同而变化。

![](img/1893500d7d7177e135492486ed631f1b_24.png)

以下是需要在客户端修改的行：

![](img/1893500d7d7177e135492486ed631f1b_26.png)

1.  **第75行：`myhostname`**
    将此处修改为客户端的主机名。
    ```
    myhostname = desktop.example.com
    ```

![](img/1893500d7d7177e135492486ed631f1b_28.png)

2.  **第83行：`mydomain`**
    此参数与服务器端保持一致。
    ```
    mydomain = example.com
    ```

![](img/1893500d7d7177e135492486ed631f1b_30.png)

3.  **第99行：`myorigin`**
    同样，去掉行首的注释符号 `#`。
    ```
    myorigin = $mydomain
    ```

4.  **第164行：`mydestination`**
    修改方式与服务器端相同。
    ```
    mydestination = localhost, localhost.localdomain
    ```

![](img/1893500d7d7177e135492486ed631f1b_32.png)

5.  **第316行：`relayhost`**
    此参数指向邮件的中继服务器，即我们之前配置的服务器端。
    ```
    relayhost = [server.example.com]
    ```

![](img/1893500d7d7177e135492486ed631f1b_34.png)

所有修改完成后，同样需要重启客户端的 Postfix 服务。

```
systemctl restart postfix
```

![](img/1893500d7d7177e135492486ed631f1b_36.png)

---

![](img/1893500d7d7177e135492486ed631f1b_38.png)

## 验证与总结

配置完成后，由于是本地环境，通常无法进行发送到外部网络的实际邮件测试。考试中，只要严格按照题目给出的参数进行配置，即可得分。

![](img/1893500d7d7177e135492486ed631f1b_40.png)

本节课中我们一起学习了如何在 Red Hat 7 系统上配置 Postfix 本地邮件服务。我们主要完成了以下工作：
1.  备份了原始的配置文件。
2.  在服务器端修改了 `myhostname`、`mydomain`、`myorigin`、`mydestination` 和 `relayhost` 参数。
3.  在客户端进行了类似的配置，并确保 `relayhost` 指向正确的服务器。
4.  重启了 Postfix 服务以使配置生效。

按照这个流程操作，即可成功完成本地邮件服务的配置。