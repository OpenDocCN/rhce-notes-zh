# Redhat红帽 RHCE8.0认证体系课程：P62：Ansible Vault 🔐

![](img/eecaccfe1f87e080dec60a5cb900b764_0.png)

![](img/eecaccfe1f87e080dec60a5cb900b764_1.png)

![](img/eecaccfe1f87e080dec60a5cb900b764_3.png)

在本节课中，我们将要学习 Ansible Vault 的核心概念和操作方法。Ansible Vault 是用于加密敏感数据（如密码、密钥等）的工具，确保这些信息在存储和传输过程中的安全性。我们将通过创建、查看、编辑和管理加密文件来掌握其基本用法。

![](img/eecaccfe1f87e080dec60a5cb900b764_5.png)

![](img/eecaccfe1f87e080dec60a5cb900b764_6.png)

![](img/eecaccfe1f87e080dec60a5cb900b764_7.png)

![](img/eecaccfe1f87e080dec60a5cb900b764_8.png)

## 概述

![](img/eecaccfe1f87e080dec60a5cb900b764_10.png)

Ansible Vault 的主要作用是加密敏感数据，这些数据通常定义在变量文件中。它适用于加密变量文件或证书等场景。本节我们将重点学习如何加密变量文件。

![](img/eecaccfe1f87e080dec60a5cb900b764_12.png)

## Ansible Vault 的基本用法

![](img/eecaccfe1f87e080dec60a5cb900b764_14.png)

![](img/eecaccfe1f87e080dec60a5cb900b764_15.png)

上一节我们介绍了 Ansible Vault 的作用，本节中我们来看看它的具体命令和参数。通过 `ansible-vault --help` 命令可以查看其简化版的使用说明。

![](img/eecaccfe1f87e080dec60a5cb900b764_17.png)

![](img/eecaccfe1f87e080dec60a5cb900b764_19.png)

以下是 `ansible-vault` 命令的一些常用参数：
*   `create`：创建一个新的加密文件。
*   `decrypt`：解密一个加密文件。
*   `edit`：编辑一个已加密的文件。
*   `view`：查看一个已加密文件的内容。
*   `encrypt`：对一个已存在的文件进行加密。
*   `rekey`：更改加密文件的密码。
*   `encrypt_string`：加密一个字符串。

![](img/eecaccfe1f87e080dec60a5cb900b764_21.png)

![](img/eecaccfe1f87e080dec60a5cb900b764_23.png)

## 创建加密文件

![](img/eecaccfe1f87e080dec60a5cb900b764_25.png)

![](img/eecaccfe1f87e080dec60a5cb900b764_27.png)

现在，我们来实际操作如何创建一个加密文件。使用 `ansible-vault create` 命令可以创建并编辑一个新的加密文件。

![](img/eecaccfe1f87e080dec60a5cb900b764_29.png)

![](img/eecaccfe1f87e080dec60a5cb900b764_31.png)

执行以下命令：
```bash
ansible-vault create secret.yml
```
系统会提示你输入并确认加密密码。之后，会调用 Vim 编辑器，你可以在其中定义变量，例如：
```yaml
username: testuser
uid: 2022
```
保存并退出后，文件 `secret.yml` 就被创建并加密了。使用 `cat secret.yml` 查看，你会发现内容已被加密，无法直接阅读。

## 查看加密文件内容

要查看加密文件的内容，需要使用 `ansible-vault view` 命令并提供密码。

![](img/eecaccfe1f87e080dec60a5cb900b764_33.png)

执行以下命令：
```bash
ansible-vault view secret.yml
```
输入创建时设置的密码后，即可看到文件的明文内容。

## 在 Playbook 中调用加密文件

在编写 Playbook 时，我们经常需要引用外部变量文件。如果这个变量文件是加密的，就需要特殊处理。下面我们编写一个 Playbook 来调用加密的变量文件。

创建一个名为 `test-vault.yml` 的 Playbook：
```yaml
---
- name: Create Users via Vault
  hosts: servers
  vars_files:
    - /home/student/ansible/secret.yml
  tasks:
    - name: Create user
      user:
        name: "{{ username }}"
        state: present
```
直接运行这个 Playbook (`ansible-playbook test-vault.yml`) 会报错，提示找不到 Vault 密码。有三种方法可以解决这个问题。

![](img/eecaccfe1f87e080dec60a5cb900b764_35.png)

以下是三种为 Playbook 提供 Vault 密码的方法：

**方法一：交互式询问密码**
使用 `--ask-vault-pass` 参数，运行时会提示输入密码。
```bash
ansible-playbook test-vault.yml --ask-vault-pass
```

**方法二：使用密码文件（推荐）**
创建一个纯文本文件（例如 `pass.yml`）存放密码，例如内容为 `redhat`。然后使用 `--vault-password-file` 参数指定该文件。
```bash
ansible-playbook test-vault.yml --vault-password-file=pass.yml
```
**注意**：此文件应妥善保管，建议设置为隐藏文件（文件名前加 `.`，如 `.pass.yml`）并设置严格的权限。

![](img/eecaccfe1f87e080dec60a5cb900b764_37.png)

![](img/eecaccfe1f87e080dec60a5cb900b764_39.png)

![](img/eecaccfe1f87e080dec60a5cb900b764_40.png)

**方法三：通过环境变量（旧版本方式）**
此方法在较新版本中可能已被弃用，仅供参考。
```bash
ansible-playbook test-vault.yml --ask-vault-pass
```

![](img/eecaccfe1f87e080dec60a5cb900b764_41.png)

![](img/eecaccfe1f87e080dec60a5cb900b764_42.png)

![](img/eecaccfe1f87e080dec60a5cb900b764_44.png)

## 管理加密文件

![](img/eecaccfe1f87e080dec60a5cb900b764_46.png)

![](img/eecaccfe1f87e080dec60a5cb900b764_48.png)

除了创建和查看，我们还需要掌握其他管理操作。

![](img/eecaccfe1f87e080dec60a5cb900b764_50.png)

![](img/eecaccfe1f87e080dec60a5cb900b764_52.png)

**解密文件**
使用 `ansible-vault decrypt` 命令可以将加密文件永久解密为明文。
```bash
ansible-vault decrypt secret.yml
```

![](img/eecaccfe1f87e080dec60a5cb900b764_53.png)

![](img/eecaccfe1f87e080dec60a5cb900b764_55.png)

![](img/eecaccfe1f87e080dec60a5cb900b764_57.png)

**重新加密文件**
对已解密的文件，可以使用 `ansible-vault encrypt` 命令再次加密。
```bash
ansible-vault encrypt secret.yml
```

![](img/eecaccfe1f87e080dec60a5cb900b764_59.png)

**更改加密密码**
使用 `ansible-vault rekey` 命令可以修改加密文件的密码。命令会先要求输入旧密码，然后输入并确认新密码。
```bash
ansible-vault rekey secret.yml
```

![](img/eecaccfe1f87e080dec60a5cb900b764_60.png)

![](img/eecaccfe1f87e080dec60a5cb900b764_62.png)

![](img/eecaccfe1f87e080dec60a5cb900b764_64.png)

**编辑加密文件**
不能直接用文本编辑器打开加密文件。必须使用 `ansible-vault edit` 命令，它会解密文件、调用编辑器、编辑完成后自动重新加密。
```bash
ansible-vault edit secret.yml
```

![](img/eecaccfe1f87e080dec60a5cb900b764_65.png)

![](img/eecaccfe1f87e080dec60a5cb900b764_66.png)

## 总结

![](img/eecaccfe1f87e080dec60a5cb900b764_68.png)

![](img/eecaccfe1f87e080dec60a5cb900b764_70.png)

![](img/eecaccfe1f87e080dec60a5cb900b764_71.png)

本节课中我们一起学习了 Ansible Vault 工具。我们了解了它的核心作用是加密敏感数据，并逐步实践了如何**创建**、**查看**、**解密**、**编辑**和**更改密码**（`rekey`）管理加密文件。最关键的是，我们掌握了在 Playbook 中调用加密变量文件的三种方法，其中**使用密码文件**是当前推荐的做法。合理使用 Ansible Vault 能有效提升自动化配置管理中的安全性。