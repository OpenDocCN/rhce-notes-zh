# Redhat红帽 RHCE8.0认证体系课程：P72：管理大项目 🚀

![](img/bac2fa3e4ce2c279175403b54634a67f_1.png)

在本节课中，我们将要学习如何管理大型的 Ansible 项目。主要内容包括：如何灵活地引用和管理清单主机、控制任务的并发执行（分叉与滚动更新），以及通过包含和导入功能实现 Playbook 的模块化设计，让复杂的自动化任务变得井井有条。

---

## 引用清单主机 📋

![](img/bac2fa3e4ce2c279175403b54634a67f_3.png)

![](img/bac2fa3e4ce2c279175403b54634a67f_4.png)

上一节我们介绍了 Ansible 的基础概念，本节中我们来看看如何更灵活地引用清单主机。

![](img/bac2fa3e4ce2c279175403b54634a67f_6.png)

![](img/bac2fa3e4ce2c279175403b54634a67f_7.png)

我们可以使用默认清单，也可以自定义清单文件。在自定义清单中，主机可以写 IP 地址或域名，但需注意 IP 和域名是不同的。

![](img/bac2fa3e4ce2c279175403b54634a67f_9.png)

![](img/bac2fa3e4ce2c279175403b54634a67f_10.png)

![](img/bac2fa3e4ce2c279175403b54634a67f_11.png)

以下是引用主机的几种方式：

![](img/bac2fa3e4ce2c279175403b54634a67f_13.png)

*   **通配符**：`*` 和 `:` 的作用完全相同，都代表所有主机。例如 `*.example.com` 代表所有 `example.com` 域下的主机。
*   **列表**：可以使用逗号分隔多个主机或组名。
*   **逻辑运算符**：
    *   `:` 表示并集（属于组1 **或** 组2的主机）。
    *   `:&` 表示交集（既属于组1 **也** 属于组2的主机）。
    *   `:!` 表示排除（在指定组中，但排除某些特定主机）。

![](img/bac2fa3e4ce2c279175403b54634a67f_15.png)

![](img/bac2fa3e4ce2c279175403b54634a67f_16.png)

## 动态清单与多清单 🔄

![](img/bac2fa3e4ce2c279175403b54634a67f_18.png)

![](img/bac2fa3e4ce2c279175403b54634a67f_20.png)

除了静态清单文件，Ansible 还支持动态清单。例如，可以使用 `ansible-inventory` 命令动态生成清单，并通过 `-i` 选项指定或导出为 JSON 格式文件。

我们也可以在配置中指定清单目录，从而实现多清单管理。将 `inventory` 参数指向一个目录，并在该目录中存放多个清单文件即可。

![](img/bac2fa3e4ce2c279175403b54634a67f_22.png)

**注意**：如果动态清单中定义的组是静态清单中某个组的子组，那么在静态清单中需要为该子组留空占位，因为其内容是动态可变的。

![](img/bac2fa3e4ce2c279175403b54634a67f_24.png)

![](img/bac2fa3e4ce2c279175403b54634a67f_26.png)

```ini
[group1]
host1

![](img/bac2fa3e4ce2c279175403b54634a67f_28.png)

![](img/bac2fa3e4ce2c279175403b54634a67f_30.png)

[group1:children]
group2  # group2 是动态清单定义的组，此处仅作占位，内容为空
```

![](img/bac2fa3e4ce2c279175403b54634a67f_32.png)

---

![](img/bac2fa3e4ce2c279175403b54634a67f_34.png)

![](img/bac2fa3e4ce2c279175403b54634a67f_36.png)

![](img/bac2fa3e4ce2c279175403b54634a67f_38.png)

## 控制任务执行：分叉 🍴

![](img/bac2fa3e4ce2c279175403b54634a67f_40.png)

![](img/bac2fa3e4ce2c279175403b54634a67f_41.png)

默认情况下，Ansible 按顺序对清单中的每台主机逐个执行每个任务。所有主机完成当前任务后，才会开始下一个任务。虽然它可以同时连接所有主机，但在管理数百或数千台主机时，这可能会成为瓶颈。

![](img/bac2fa3e4ce2c279175403b54634a67f_43.png)

我们可以通过设置 **分叉（fork）** 来控制并发的主机数量。分叉数限制了 Ansible 能同时管理的主机数。

*   默认分叉数为 **5**。
*   可以通过命令行参数 `-f` 或 `--forks` 临时指定并发数量，例如 `ansible-playbook playbook.yml -f 1` 表示一次只在一台主机上执行。
*   也可以在 `ansible.cfg` 配置文件的 `[defaults]` 部分设置 `forks` 参数。

![](img/bac2fa3e4ce2c279175403b54634a67f_45.png)

**公式**：`ansible-playbook -f <并发数> playbook.yml`

![](img/bac2fa3e4ce2c279175403b54634a67f_47.png)

![](img/bac2fa3e4ce2c279175403b54634a67f_48.png)

![](img/bac2fa3e4ce2c279175403b54634a67f_50.png)

设置分叉数为 1 时，任务会在一台主机上完成后，再在下一台主机上执行，适合需要严格顺序控制的场景。

![](img/bac2fa3e4ce2c279175403b54634a67f_52.png)

---

![](img/bac2fa3e4ce2c279175403b54634a67f_54.png)

![](img/bac2fa3e4ce2c279175403b54634a67f_56.png)

![](img/bac2fa3e4ce2c279175403b54634a67f_58.png)

## 控制任务执行：滚动更新 🔄

![](img/bac2fa3e4ce2c279175403b54634a67f_60.png)

在某些场景下（例如批量更新服务），我们需要更精细地控制主机的执行顺序，避免所有主机同时停机导致服务中断。这时可以使用 **滚动更新（serial）**。

![](img/bac2fa3e4ce2c279175403b54634a67f_62.png)

滚动更新与分叉不同：
*   **分叉** 控制一个任务能同时在多少台主机上运行。
*   **滚动更新** 控制整个 Play 按批次执行，一批完成后才进行下一批。

在 Playbook 中，使用 `serial` 关键字来定义每批的主机数量。

```yaml
- name: 滚动更新 Web 服务
  hosts: webservers
  serial: 1  # 每次只在一台主机上执行整个 Play
  tasks:
    - name: 停止服务
      service:
        name: httpd
        state: stopped
    - name: 更新应用
      # ... 更新任务
    - name: 启动服务
      service:
        name: httpd
        state: started
```

**注意**：`serial` 设置的每批主机数不能超过 `forks` 设置的并发数，实际生效的将是两者中较小的值。

---

## 模块化设计：包含与导入 📦

当一个 Playbook 变得非常庞大时，维护和排错会变得困难。Ansible 支持模块化设计，允许我们将任务拆分到不同的文件中，然后通过 **包含（include）** 或 **导入（import）** 的方式组合起来。这样可以提高代码的复用性和可管理性。

两者核心区别在于处理时机：
*   **导入（import）** 是 **静态** 操作。在 Playbook **解析初期** 就会将外部文件的内容完整地插入到指定位置。
*   **包含（include）** 是 **动态** 操作。在 Playbook **运行期间**，执行到该语句时才会处理外部文件的内容。

![](img/bac2fa3e4ce2c279175403b54634a67f_64.png)

![](img/bac2fa3e4ce2c279175403b54634a67f_66.png)

### 导入 Playbook

![](img/bac2fa3e4ce2c279175403b54634a67f_68.png)

![](img/bac2fa3e4ce2c279175403b54634a67f_70.png)

使用 `import_playbook` 指令可以导入一个完整的 Playbook。这只能在 Playbook 的顶层使用，不能在某个任务内部使用。

![](img/bac2fa3e4ce2c279175403b54634a67f_72.png)

![](img/bac2fa3e4ce2c279175403b54634a67f_73.png)

```yaml
# main.yml
- import_playbook: webserver.yml
- import_playbook: dbserver.yml
```

![](img/bac2fa3e4ce2c279175403b54634a67f_75.png)

![](img/bac2fa3e4ce2c279175403b54634a67f_76.png)

![](img/bac2fa3e4ce2c279175403b54634a67f_78.png)

### 导入任务文件

![](img/bac2fa3e4ce2c279175403b54634a67f_80.png)

使用 `import_tasks` 可以导入一个只包含任务列表的平面文件。这个任务文件没有 Playbook 的头部信息（如 `hosts`, `name`）。

![](img/bac2fa3e4ce2c279175403b54634a67f_82.png)

```yaml
# tasks/main.yml
- name: 安装软件包
  yum:
    name: "{{ packages }}"
    state: present

![](img/bac2fa3e4ce2c279175403b54634a67f_84.png)

# playbook.yml
- hosts: all
  tasks:
    - import_tasks: tasks/main.yml
```

![](img/bac2fa3e4ce2c279175403b54634a67f_86.png)

![](img/bac2fa3e4ce2c279175403b54634a67f_88.png)

### 包含任务文件

![](img/bac2fa3e4ce2c279175403b54634a67f_89.png)

![](img/bac2fa3e4ce2c279175403b54634a67f_91.png)

![](img/bac2fa3e4ce2c279175403b54634a67f_93.png)

使用 `include_tasks` 可以动态包含任务文件。由于是动态处理，因此被包含的任务文件中**不能**使用 `static` 指令，也**不能**直接触发在包含点之外定义的 Handler。通常建议在包含时配合变量使用，以增加灵活性。

![](img/bac2fa3e4ce2c279175403b54634a67f_95.png)

![](img/bac2fa3e4ce2c279175403b54634a67f_96.png)

```yaml
- hosts: all
  tasks:
    - name: 包含动态任务
      include_tasks: setup.yml
      vars:
        parameter: value
```

![](img/bac2fa3e4ce2c279175403b54634a67f_98.png)

![](img/bac2fa3e4ce2c279175403b54634a67f_100.png)

---

![](img/bac2fa3e4ce2c279175403b54634a67f_101.png)

![](img/bac2fa3e4ce2c279175403b54634a67f_103.png)

本节课中我们一起学习了管理大型 Ansible 项目的关键技巧。我们回顾了灵活引用清单主机的方法，深入探讨了通过 **分叉（forks）** 和 **滚动更新（serial）** 来控制任务的并发与执行顺序，以应对不同规模的运维场景。最后，我们掌握了通过 **导入（import）** 和 **包含（include）** 来实现 Playbook 的模块化，这将使我们的自动化代码结构更清晰、更易于维护和复用。这些技能是构建高效、可靠自动化体系的基础。