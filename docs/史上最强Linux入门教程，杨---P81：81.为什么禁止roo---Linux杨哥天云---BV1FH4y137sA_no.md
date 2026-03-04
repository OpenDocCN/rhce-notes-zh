Linux入门与红帽认证：P81：为什么禁止root用户远程登录会失败 🔐

在本节课中，我们将学习如何通过SSH服务禁止root用户远程登录，并分析配置后可能仍然失败的原因。这是一个重要的安全设置，因为root用户权限过大，一旦密码泄露，可能对系统造成严重危害。

---

### 环境与目标 🎯

![](img/61ca81eb4c0060034a95ff1e282dfbae_1.png)

我们的操作环境包含两台机器：左侧是作为客户端的普通用户，右侧是作为服务器的`server`。我们的目标是从客户端通过SSH登录到服务器，并成功配置以禁止root用户的远程登录。

![](img/61ca81eb4c0060034a95ff1e282dfbae_2.png)

![](img/61ca81eb4c0060034a95ff1e282dfbae_3.png)

---

![](img/61ca81eb4c0060034a95ff1e282dfbae_4.png)

![](img/61ca81eb4c0060034a95ff1e282dfbae_5.png)

### 修改SSH主配置文件 📁

![](img/61ca81eb4c0060034a95ff1e282dfbae_6.png)

上一节我们介绍了操作环境，本节中我们来看看如何修改SSH的主配置文件。

![](img/61ca81eb4c0060034a95ff1e282dfbae_7.png)

![](img/61ca81eb4c0060034a95ff1e282dfbae_8.png)

首先，我们需要打开SSH服务的主配置文件`sshd_config`。可以使用`vi`或`vim`编辑器进行操作。

![](img/61ca81eb4c0060034a95ff1e282dfbae_9.png)

```bash
vim /etc/ssh/sshd_config
```

在文件中，我们需要找到关于`PermitRootLogin`的配置项。默认情况下，该行可能被注释（以`#`开头），但这代表了系统的默认值。我们可以使用搜索功能快速定位。

以下是搜索该配置项的命令：
```bash
/ PermitRootLogin
```
为了忽略大小写，可以在搜索前在vim中执行`:set ic`。

默认情况下，`PermitRootLogin`的参数是`prohibit-password`，这表示禁止root用户使用密码登录。

---

![](img/61ca81eb4c0060034a95ff1e282dfbae_11.png)

![](img/61ca81eb4c0060034a95ff1e282dfbae_12.png)

### 理解PermitRootLogin参数 🔧

![](img/61ca81eb4c0060034a95ff1e282dfbae_13.png)

![](img/61ca81eb4c0060034a95ff1e282dfbae_14.png)

上一节我们找到了配置项，本节中我们来看看`PermitRootLogin`参数的不同取值及其含义。

![](img/61ca81eb4c0060034a95ff1e282dfbae_15.png)

`PermitRootLogin`参数控制root用户通过SSH登录的权限，主要有以下几种设置：

![](img/61ca81eb4c0060034a95ff1e282dfbae_16.png)

![](img/61ca81eb4c0060034a95ff1e282dfbae_17.png)

*   **`prohibit-password`**：这是常见默认值。它**禁止root用户使用密码登录**，但允许使用更安全的**公钥认证**方式登录。
*   **`no`**：此设置**完全禁止root用户以任何方式远程登录**，包括密码和公钥。
*   **`yes`**：此设置**允许root用户使用密码或公钥登录**。
*   **`forced-commands-only`**：此设置允许root用户登录，但仅限于执行预定义的命令，通常用于自动化任务。

![](img/61ca81eb4c0060034a95ff1e282dfbae_18.png)

![](img/61ca81eb4c0060034a95ff1e282dfbae_19.png)

根据你的安全需求，可以选择合适的值。例如，如果希望完全禁止root远程登录，应将其设置为`no`。

![](img/61ca81eb4c0060034a95ff1e282dfbae_20.png)

---

### 首次配置与测试 🧪

![](img/61ca81eb4c0060034a95ff1e282dfbae_22.png)

![](img/61ca81eb4c0060034a95ff1e282dfbae_23.png)

上一节我们理解了各个参数，本节中我们来进行首次配置和测试。

![](img/61ca81eb4c0060034a95ff1e282dfbae_24.png)

我们将`PermitRootLogin`的值修改为`yes`，以允许root登录进行测试。

1.  在配置文件中，将`PermitRootLogin`的值改为`yes`。
2.  保存并退出编辑器。
3.  重启SSH服务以使配置生效。

以下是重启SSH服务的命令：
```bash
systemctl restart sshd
```

![](img/61ca81eb4c0060034a95ff1e282dfbae_26.png)

![](img/61ca81eb4c0060034a95ff1e282dfbae_27.png)

配置完成后，我们从客户端尝试使用root身份登录服务器（假设服务器IP为`192.168.10.20`）。

![](img/61ca81eb4c0060034a95ff1e282dfbae_28.png)

![](img/61ca81eb4c0060034a95ff1e282dfbae_29.png)

以下是SSH登录命令：
```bash
ssh root@192.168.10.20
```

![](img/61ca81eb4c0060034a95ff1e282dfbae_30.png)

![](img/61ca81eb4c0060034a95ff1e282dfbae_31.png)

输入密码后，登录成功，这验证了`yes`设置是有效的。

---

### 配置禁止登录及遇到的问题 ❓

![](img/61ca81eb4c0060034a95ff1e282dfbae_33.png)

上一节我们测试了允许登录，本节中我们正式配置禁止登录并观察问题。

![](img/61ca81eb4c0060034a95ff1e282dfbae_34.png)

![](img/61ca81eb4c0060034a95ff1e282dfbae_35.png)

现在，我们将`PermitRootLogin`的值改回`no`，以实现禁止root远程登录的目标。

![](img/61ca81eb4c0060034a95ff1e282dfbae_36.png)

1.  再次编辑`/etc/ssh/sshd_config`文件，将`PermitRootLogin`的值改为`no`。
2.  保存文件。
3.  重启SSH服务。

![](img/61ca81eb4c0060034a95ff1e282dfbae_37.png)

```bash
systemctl restart sshd
```

![](img/61ca81eb4c0060034a95ff1e282dfbae_39.png)

再次从客户端尝试使用root身份登录。此时会发现，**即使配置为`no`，root用户仍然可以成功登录**。这就是许多初学者会遇到的问题。

![](img/61ca81eb4c0060034a95ff1e282dfbae_41.png)

---

![](img/61ca81eb4c0060034a95ff1e282dfbae_43.png)

### 问题分析与解决：子配置文件 🔍

上一节我们遇到了配置失效的问题，本节中我们来分析原因并找到解决方案。

许多Linux服务（包括SSH）的配置结构除了主配置文件外，还可能包含子配置文件。主配置文件通常会通过`Include`指令来包含其他目录下的配置文件。这样设计的好处是，无需频繁修改主配置文件，通过增删子配置文件即可管理服务配置。

SSH服务可能包含的子配置目录是`/etc/ssh/sshd_config.d/`。我们需要检查该目录下是否有文件覆盖了主配置。

1.  查看`/etc/ssh/sshd_config.d/`目录下的文件。
    ```bash
    ls /etc/ssh/sshd_config.d/
    ```
2.  检查这些文件的内容，特别是其中是否也设置了`PermitRootLogin`。
    ```bash
    cat /etc/ssh/sshd_config.d/*.conf
    ```

![](img/61ca81eb4c0060034a95ff1e282dfbae_45.png)

![](img/61ca81eb4c0060034a95ff1e282dfbae_46.png)

很可能在某个子配置文件（例如`50-cloud-init.conf`）中发现了`PermitRootLogin yes`的设置。**子配置文件的设置会覆盖主配置文件**。

![](img/61ca81eb4c0060034a95ff1e282dfbae_47.png)

![](img/61ca81eb4c0060034a95ff1e282dfbae_48.png)

解决方案是修改这个子配置文件中的值：

![](img/61ca81eb4c0060034a95ff1e282dfbae_49.png)

1.  编辑对应的子配置文件，将`PermitRootLogin`改为`no`。
    ```bash
    vim /etc/ssh/sshd_config.d/50-cloud-init.conf
    ```
2.  保存文件。
3.  再次重启SSH服务。

![](img/61ca81eb4c0060034a95ff1e282dfbae_50.png)

![](img/61ca81eb4c0060034a95ff1e282dfbae_51.png)

```bash
systemctl restart sshd
```

![](img/61ca81eb4c0060034a95ff1e282dfbae_52.png)

![](img/61ca81eb4c0060034a95ff1e282dfbae_53.png)

---

![](img/61ca81eb4c0060034a95ff1e282dfbae_54.png)

### 最终验证 ✅

![](img/61ca81eb4c0060034a95ff1e282dfbae_55.png)

![](img/61ca81eb4c0060034a95ff1e282dfbae_56.png)

完成上述修改并重启服务后，再次从客户端尝试使用root身份SSH登录。此时，系统会拒绝root用户的登录请求，而其他普通用户的登录不受影响。

![](img/61ca81eb4c0060034a95ff1e282dfbae_58.png)

这证明我们已成功禁止了root用户的远程登录。

---

### 总结 📝

![](img/61ca81eb4c0060034a95ff1e282dfbae_60.png)

本节课中我们一起学习了如何配置SSH以禁止root用户远程登录。核心要点如下：

![](img/61ca81eb4c0060034a95ff1e282dfbae_61.png)

![](img/61ca81eb4c0060034a95ff1e282dfbae_62.png)

1.  **修改主配置**：编辑`/etc/ssh/sshd_config`文件，设置`PermitRootLogin`参数为`no`。
2.  **理解参数**：掌握了`prohibit-password`、`no`、`yes`等参数的具体含义。
3.  **排查覆盖**：认识到服务配置可能被`/etc/ssh/sshd_config.d/`目录下的**子配置文件**覆盖，这是导致配置失败的主要原因。
4.  **完整流程**：正确的配置流程是，检查并确保**主配置文件和所有相关子配置文件**中的`PermitRootLogin`设置一致，然后重启SSH服务。

![](img/61ca81eb4c0060034a95ff1e282dfbae_63.png)

通过本课的学习，你不仅掌握了禁止root远程登录的方法，也理解了Linux服务配置文件的层次结构，这对于管理其他服务同样具有参考价值。