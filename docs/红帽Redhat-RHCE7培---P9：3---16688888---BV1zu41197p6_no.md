# 红帽RHCE7培训课程：P9：3 - 进程优先级、ACL与SELinux

## 概述
在本节课中，我们将学习三个核心知识点：**进程优先级**、**文件访问控制列表**以及**SELinux安全增强**。这些内容对于系统资源管理和安全配置至关重要。

---

## 上节回顾：Kickstart、grep、vim与cron
上一节我们介绍了Kickstart自动化安装、grep文本过滤、vim编辑器以及cron计划任务。本节中，我们将深入探讨系统资源管理和高级权限控制。

---

## 第五章：进程优先级 🎯

### 概述
系统资源有限，我们需要决定哪个进程优先使用CPU和内存。这通过**优先级**来实现。

### 核心概念：优先级范围
进程优先级范围是 **-20 到 19**。**数值越小，优先级越高**。例如，-20的优先级最高，19的优先级最低。

![](img/1a6e8a5dc7b16aa2d6c8d31c1ddef998_1.png)

![](img/1a6e8a5dc7b16aa2d6c8d31c1ddef998_2.png)

### 查看进程优先级
使用 `ps` 命令查看进程及其优先级。
```bash
ps axo pid,comm,nice
```
*   `a` 和 `x`：显示所有进程。
*   `o`：自定义输出列（`pid` 进程ID，`comm` 命令，`nice` 优先级）。

### 修改进程优先级
有两种命令，区别在于进程是否已运行：

1.  **nice**：在**启动进程时**指定优先级。
    ```bash
    nice -n -10 vim file.txt  # 以优先级-10启动vim
    ```

2.  **renice**：修改**已运行进程**的优先级。
    ```bash
    renice -n -20 1611  # 将PID为1611的进程优先级改为-20
    ```

**注意**：普通用户只能降低优先级（增大nice值），只有root用户可以提升优先级。

### 本节总结
本节我们学习了如何通过 `ps` 查看进程，并使用 `nice` 和 `renice` 命令来调整进程的优先级，以优化系统资源分配。

---

## 第六章：文件访问控制列表 🔐

### 概述
标准的Linux文件权限（用户、组、其他人）粒度较粗。**ACL** 允许我们为**特定用户或组**设置精细的文件访问权限。

### 识别ACL权限
使用 `ls -l` 命令时，如果权限位末尾是 **+** 号，表示该文件设置了ACL。
```bash
ls -l file.txt
# -rw-rw-r--+ 表示设置了ACL
```

### 核心命令
*   **`setfacl`**：设置ACL权限。
*   **`getfacl`**：查看ACL权限。

### 设置ACL权限
以下是 `setfacl` 命令的常用选项和格式：

![](img/1a6e8a5dc7b16aa2d6c8d31c1ddef998_4.png)

![](img/1a6e8a5dc7b16aa2d6c8d31c1ddef998_5.png)

![](img/1a6e8a5dc7b16aa2d6c8d31c1ddef998_6.png)

*   **`-m` (modify)**：修改或添加ACL条目。
*   **`-x` (remove)**：删除指定的ACL条目。
*   **`-b` (remove all)**：删除所有ACL条目。
*   **`-d` (default)**：设置默认ACL（对新创建的文件/目录生效）。

![](img/1a6e8a5dc7b16aa2d6c8d31c1ddef998_8.png)

![](img/1a6e8a5dc7b16aa2d6c8d31c1ddef998_10.png)

![](img/1a6e8a5dc7b16aa2d6c8d31c1ddef998_12.png)

![](img/1a6e8a5dc7b16aa2d6c8d31c1ddef998_14.png)

**基本语法**：
```bash
setfacl -m u:username:permissions file  # 为用户设置
setfacl -m g:groupname:permissions file # 为组设置
setfacl -m o::permissions file          # 为其他人设置
```

![](img/1a6e8a5dc7b16aa2d6c8d31c1ddef998_16.png)

![](img/1a6e8a5dc7b16aa2d6c8d31c1ddef998_18.png)

![](img/1a6e8a5dc7b16aa2d6c8d31c1ddef998_20.png)

**示例**：
```bash
setfacl -m u:student:rwx /folder      # 赋予student用户rwx权限
setfacl -m g:wheel:rx /folder         # 赋予wheel组rx权限
setfacl -x u:student /folder          # 删除student用户的ACL条目
setfacl -b /folder                    # 删除/folder的所有ACL条目
```

![](img/1a6e8a5dc7b16aa2d6c8d31c1ddef998_22.png)

![](img/1a6e8a5dc7b16aa2d6c8d31c1ddef998_24.png)

### 权限生效规则
系统按顺序检查权限：**用户ACL -> 所属组权限 -> 组ACL -> 其他人权限**。匹配到第一条即生效。

![](img/1a6e8a5dc7b16aa2d6c8d31c1ddef998_26.png)

![](img/1a6e8a5dc7b16aa2d6c8d31c1ddef998_28.png)

![](img/1a6e8a5dc7b16aa2d6c8d31c1ddef998_30.png)

![](img/1a6e8a5dc7b16aa2d6c8d31c1ddef998_32.png)

### 掩码
设置ACL时会自动生成一个 **`mask`** 值，它是**实际生效的最大权限**。用户或组的ACL权限会与mask做“与”运算。
```bash
setfacl -m m::r /folder  # 将mask设置为只读，即使ACL条目有写权限也无效
```

![](img/1a6e8a5dc7b16aa2d6c8d31c1ddef998_34.png)

![](img/1a6e8a5dc7b16aa2d6c8d31c1ddef998_36.png)

![](img/1a6e8a5dc7b16aa2d6c8d31c1ddef998_38.png)

![](img/1a6e8a5dc7b16aa2d6c8d31c1ddef998_40.png)

![](img/1a6e8a5dc7b16aa2d6c8d31c1ddef998_42.png)

### 默认ACL与备份恢复
*   **默认ACL**：使用 `-d` 选项设置，使目录下**新创建**的文件/目录继承ACL。
    ```bash
    setfacl -m d:u:student:rwx /folder
    ```
*   **备份与恢复**：
    ```bash
    getfacl -R /folder > acl_backup.txt  # 备份ACL到文件
    setfacl --restore=acl_backup.txt     # 从文件恢复ACL（需在正确目录下执行）
    ```

### 本节总结
本节我们学习了如何使用 `setfacl` 和 `getfacl` 命令进行精细化的文件权限控制，包括设置、查看、删除ACL，理解mask的作用，以及如何进行ACL的备份与恢复。

---

## 第七章：SELinux安全增强 🛡️

### 概述
SELinux在传统Linux安全机制（文件权限、服务配置、防火墙）之上，提供了**强制性的访问控制**，大幅增强了系统安全性。

### SELinux的三种主要控制对象
1.  **文件/目录**：通过**上下文**进行控制。
2.  **进程**：通过**布尔值**开关控制其能力。
3.  **端口**：控制服务与端口的绑定关系。

### 实验一：修改SELinux运行模式
SELinux有三种模式：
*   **`enforcing`**：强制模式，违反策略的行动将被阻止并记录。
*   **`permissive`**：宽容模式，只记录违反策略的行动，但不阻止。
*   **`disabled`**：完全禁用。

![](img/1a6e8a5dc7b16aa2d6c8d31c1ddef998_44.png)

![](img/1a6e8a5dc7b16aa2d6c8d31c1ddef998_46.png)

**修改方法**：
1.  **临时修改**（重启后失效）：
    ```bash
    setenforce 1  # 设置为enforcing
    setenforce 0  # 设置为permissive
    getenforce    # 查看当前模式
    ```
2.  **永久修改**（需重启生效）：
    编辑 `/etc/selinux/config` 文件，修改 `SELINUX=` 后的值。

![](img/1a6e8a5dc7b16aa2d6c8d31c1ddef998_48.png)

![](img/1a6e8a5dc7b16aa2d6c8d31c1ddef998_50.png)

![](img/1a6e8a5dc7b16aa2d6c8d31c1ddef998_51.png)

### 实验二：文件上下文管理
上下文是附加在文件上的SELinux标签，格式如 `user:role:type:level`。最常用的是 **`type`**。

*   **查看上下文**：
    ```bash
    ls -Z /etc  # 查看文件上下文
    ps -Z       # 查看进程上下文
    ```
*   **修改上下文**：
    *   `chcon`：临时修改。
        ```bash
        chcon -t httpd_sys_content_t /var/www/html/file.html
        ```
    *   `semanage fcontext` + `restorecon`：永久修改（推荐）。
        ```bash
        semanage fcontext -a -t httpd_sys_content_t "/web(/.*)?"  # 添加规则
        restorecon -Rv /web                                       # 应用规则
        ```

**重要特性**：文件被**移动**时保留上下文，被**创建**或**复制**时继承父目录的上下文。

### 实验三：管理SELinux布尔值
布尔值是预定义的策略开关，用于调整SELinux对特定服务或功能的控制。

*   **查看所有布尔值**：
    ```bash
    getsebool -a
    ```
*   **查看/设置特定布尔值**：
    ```bash
    getsebool ftp_home_dir
    setsebool -P ftp_home_dir on  # -P 表示永久生效
    ```

### 故障排查思路
当服务因SELinux问题无法正常工作时，请按以下顺序排查：
1.  **查看服务状态和日志**：
    ```bash
    systemctl status service_name
    grep "SELinux" /var/log/messages  # 或 journalctl -xe
    ```
2.  **日志通常会给出建议命令**，例如提示你运行某个 `setsebool` 或 `chcon` 命令。
3.  遵循日志建议进行修复。

### 本节总结
本节我们系统学习了SELinux的核心概念：三种运行模式的配置、文件上下文的管理与修复、以及如何使用布尔值来调整策略。掌握查看日志并按照提示解决问题的方法是关键。

---

## 课程总结
本节课我们一起学习了三个重要的系统管理主题：
1.  **进程优先级**：使用 `nice` 和 `renice` 管理进程对资源的访问顺序。
2.  **文件访问控制列表**：使用 `setfacl` 和 `getfacl` 实现比传统权限更精细的访问控制。
3.  **SELinux安全增强**：理解了其工作原理，并学会了通过调整模式、上下文和布尔值来配置和排查SELinux相关问题。

![](img/1a6e8a5dc7b16aa2d6c8d31c1ddef998_53.png)

![](img/1a6e8a5dc7b16aa2d6c8d31c1ddef998_55.png)

![](img/1a6e8a5dc7b16aa2d6c8d31c1ddef998_57.png)

这些技能对于构建安全、稳定且高效的红帽企业Linux系统至关重要。