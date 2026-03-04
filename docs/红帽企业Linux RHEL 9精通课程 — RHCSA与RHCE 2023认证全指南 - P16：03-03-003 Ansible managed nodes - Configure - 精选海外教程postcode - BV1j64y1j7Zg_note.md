# Ansible 精通课程：P16：03-03-003：配置 Ansible 受管节点 🚀

![](img/147741f36bae9957dd9d266d1df2407d_1.png)

## 概述
在本节课程中，我们将学习如何配置 Ansible 受管节点。主要内容包括为 Ansible 用户生成 SSH 密钥、将公钥分发到受管节点，以及配置受管节点上的权限提升（sudo）。完成这些步骤后，Ansible 控制节点将能够无密码、安全地连接并管理目标主机。

---

![](img/147741f36bae9957dd9d266d1df2407d_3.png)

![](img/147741f36bae9957dd9d266d1df2407d_5.png)

## 配置受管节点的步骤
配置 Ansible 受管节点主要涉及三个核心步骤。以下是每个步骤的详细说明。

![](img/147741f36bae9957dd9d266d1df2407d_7.png)

![](img/147741f36bae9957dd9d266d1df2407d_9.png)

![](img/147741f36bae9957dd9d266d1df2407d_11.png)

### 第一步：生成 SSH 密钥对
首先，我们需要在 Ansible 控制节点上为执行 Ansible 任务的用户（例如 `cloud-user`）生成 SSH 密钥对。这包括一个私钥和一个公钥。

![](img/147741f36bae9957dd9d266d1df2407d_13.png)

![](img/147741f36bae9957dd9d266d1df2407d_14.png)

![](img/147741f36bae9957dd9d266d1df2407d_16.png)

![](img/147741f36bae9957dd9d266d1df2407d_17.png)

![](img/147741f36bae9957dd9d266d1df2407d_19.png)

打开终端，使用 `ssh-keygen` 命令生成密钥：

![](img/147741f36bae9957dd9d266d1df2407d_21.png)

![](img/147741f36bae9957dd9d266d1df2407d_23.png)

```bash
ssh-keygen
```
命令执行后，会提示你输入保存密钥的文件路径，通常接受默认路径（如 `/home/cloud-user/.ssh/id_rsa`）即可。接着，系统会询问是否设置密钥密码，为了简化演示，我们直接按回车留空。

![](img/147741f36bae9957dd9d266d1df2407d_25.png)

生成成功后，你可以在 `~/.ssh/` 目录下看到两个文件：
*   **`id_rsa`**：私钥文件，必须妥善保管。
*   **`id_rsa.pub`**：公钥文件，需要分发到受管节点。

![](img/147741f36bae9957dd9d266d1df2407d_27.png)

![](img/147741f36bae9957dd9d266d1df2407d_29.png)

![](img/147741f36bae9957dd9d266d1df2407d_31.png)

![](img/147741f36bae9957dd9d266d1df2407d_33.png)

### 第二步：分发 SSH 公钥
接下来，我们需要将上一步生成的公钥复制到所有受管节点上。这样，控制节点就可以通过 SSH 密钥认证的方式无密码登录受管节点。

![](img/147741f36bae9957dd9d266d1df2407d_35.png)

![](img/147741f36bae9957dd9d266d1df2407d_37.png)

我们可以使用 `ssh-copy-id` 命令方便地完成这个操作。例如，要将密钥复制到主机 `mhs-pearson-2` 上的 `cloud-user` 用户：

![](img/147741f36bae9957dd9d266d1df2407d_39.png)

![](img/147741f36bae9957dd9d266d1df2407d_41.png)

```bash
ssh-copy-id cloud-user@mhs-pearson-2
```
系统会提示你输入目标主机上 `cloud-user` 用户的密码。输入正确密码后，公钥就会被自动添加到目标主机的 `~/.ssh/authorized_keys` 文件中。

![](img/147741f36bae9957dd9d266d1df2407d_43.png)

![](img/147741f36bae9957dd9d266d1df2407d_45.png)

你可以使用 `ssh` 命令测试是否配置成功：
```bash
ssh cloud-user@mhs-pearson-2
```
如果无需输入密码即可登录，说明密钥分发成功。请对所有需要管理的受管节点重复此步骤。

![](img/147741f36bae9957dd9d266d1df2407d_47.png)

![](img/147741f36bae9957dd9d266d1df2407d_49.png)

### 第三步：配置受管节点的权限提升（Sudo）
为了让 Ansible 能够在受管节点上执行需要管理员权限的任务（如安装软件、修改系统文件），我们需要配置 sudo 权限，允许执行 Ansible 任务的用户无需密码即可运行所有命令。

登录到受管节点（例如 `mhs-pearson-3`），使用 `visudo` 命令安全地编辑 sudoers 文件：
```bash
sudo visudo
```
在文件末尾，添加以下行（假设用户是 `cloud-user`）：
```
cloud-user ALL=(ALL) NOPASSWD: ALL
```
这行配置的含义是：允许 `cloud-user` 用户在任何主机上以任何用户身份执行所有命令，且无需输入密码。

保存并退出编辑器。对每一个受管节点重复此配置步骤。

完成以上三步后，你的 Ansible 受管节点就配置完成了。

---

## 验证工作配置
上一节我们完成了受管节点的基本配置，本节中我们来看看如何使用 Ansible 临时命令来验证配置是否成功。

### Ansible 临时命令简介
Ansible 临时命令（ad-hoc command）是一种快速执行单次任务的方式，无需编写完整的剧本（playbook）。它的基本语法如下：
```bash
ansible [主机或组名] -i [库存文件] -m [模块名] -a “[模块参数]”
```
常用选项说明：
*   `-i`：指定库存文件路径。如果已在 `ansible.cfg` 中配置默认库存，则可省略。
*   `-m`：指定要使用的 Ansible 模块。
*   `-a`：传递给模块的参数。
*   `--become` 或 `-b`：以提升的权限（如 root）运行命令。
*   如果使用 `-a` 但不指定 `-m` 模块，则默认使用 `command` 模块执行 shell 命令。

![](img/147741f36bae9957dd9d266d1df2407d_51.png)

![](img/147741f36bae9957dd9d266d1df2407d_53.png)

### 常用验证命令示例
以下是几个用于验证配置的常用临时命令。

![](img/147741f36bae9957dd9d266d1df2407d_55.png)

![](img/147741f36bae9957dd9d266d1df2407d_57.png)

![](img/147741f36bae9957dd9d266d1df2407d_59.png)

![](img/147741f36bae9957dd9d266d1df2407d_61.png)

**1. 测试连通性（Ping 模块）**
使用 `ping` 模块测试 Ansible 是否能与受管节点通信。以下命令测试库存中 `all` 组的所有主机：
```bash
ansible all -i inventory -m ping
```
如果看到绿色的 `SUCCESS` 和 `pong` 回复，表示连通性正常。

![](img/147741f36bae9957dd9d266d1df2407d_63.png)

![](img/147741f36bae9957dd9d266d1df2407d_65.png)

![](img/147741f36bae9957dd9d266d1df2407d_67.png)

![](img/147741f36bae9957dd9d266d1df2407d_69.png)

**2. 收集主机信息（Setup 模块）**
使用 `setup` 模块收集受管节点的详细信息（称为 Ansible Facts）：
```bash
ansible mhs-pearson-2 -m setup | head -20
```
此命令会返回目标主机的大量系统信息，如 IP 地址、操作系统版本、硬件架构等。

![](img/147741f36bae9957dd9d266d1df2407d_71.png)

![](img/147741f36bae9957dd9d266d1df2407d_73.png)

**3. 执行 Shell 命令**
不指定模块，直接使用 `-a` 参数可以运行 shell 命令，用于快速检查：
```bash
ansible mhs-pearson-2 -a “ls -l /tmp”
```

![](img/147741f36bae9957dd9d266d1df2407d_75.png)

**4. 测试文件分发（Copy 模块）**
创建一个测试文件，然后使用 `copy` 模块将其分发到受管节点，验证文件操作权限：
```bash
echo “This is a test” > secret_file
ansible mhs-pearson-2 -m copy -a “src=secret_file dest=/tmp/”
```
然后可以验证文件是否成功创建：
```bash
ansible mhs-pearson-2 -a “cat /tmp/secret_file”
```

![](img/147741f36bae9957dd9d266d1df2407d_77.png)

![](img/147741f36bae9957dd9d266d1df2407d_79.png)

![](img/147741f36bae9957dd9d266d1df2407d_81.png)

**5. 对主机组进行操作**
你也可以针对在库存文件中定义的整个主机组（如 `lab_servers`）执行命令：
```bash
ansible lab_servers -m copy -a “src=secret_file dest=/tmp/”
ansible lab_servers -a “ls -l /tmp/secret_file”
```

通过这些临时命令，你可以快速验证 SSH 密钥认证、sudo 权限以及 Ansible 的基本功能是否全部正常工作。

![](img/147741f36bae9957dd9d266d1df2407d_83.png)

![](img/147741f36bae9957dd9d266d1df2407d_85.png)

![](img/147741f36bae9957dd9d266d1df2407d_87.png)

---

![](img/147741f36bae9957dd9d266d1df2407d_89.png)

![](img/147741f36bae9957dd9d266d1df2407d_91.png)

![](img/147741f36bae9957dd9d266d1df2407d_93.png)

![](img/147741f36bae9957dd9d266d1df2407d_95.png)

## 总结
本节课中我们一起学习了配置 Ansible 受管节点的完整流程。首先，我们在控制节点生成了 SSH 密钥对；然后，将公钥分发到了各个受管节点，实现了无密码 SSH 访问；接着，我们在受管节点上配置了无密码 sudo 权限，确保 Ansible 可以执行特权命令。最后，我们使用多种 Ansible 临时命令验证了整个配置的正确性。完成这些步骤后，你的 Ansible 控制节点就已经准备就绪，可以开始高效地管理所有受管主机了。