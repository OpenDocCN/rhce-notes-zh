# Redhat红帽 RHCE8.0认证体系课程：P69：第六章：文件目录管理及JINJA2模板 🗂️

![](img/f6cf099bbac19981b12bf9ebf8830f64_1.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_3.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_5.png)

在本节课中，我们将要学习Ansible中用于管理文件和目录的几个补充模块，并深入探讨JINJA2模板的创建与应用。JINJA2模板是Ansible实现配置自动化、动态生成文件的核心工具，理解和掌握它对于通过RHCE认证至关重要。

![](img/f6cf099bbac19981b12bf9ebf8830f64_7.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_9.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_10.png)

## 第一部分：补充的文件管理模块 📝

![](img/f6cf099bbac19981b12bf9ebf8830f64_12.png)

上一节我们介绍了基础的`copy`、`file`等模块，本节中我们来看看三个补充的模块：`sefcontext`、`blockinfile`和`stat`。

![](img/f6cf099bbac19981b12bf9ebf8830f64_14.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_15.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_16.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_17.png)

### 1. `sefcontext` 模块：设置文件安全上下文

![](img/f6cf099bbac19981b12bf9ebf8830f64_19.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_20.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_22.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_24.png)

`sefcontext`模块用于设置文件或目录的SELinux安全上下文。虽然我们可以在`file`或`copy`模块中使用`setype`参数设置默认上下文，但那是临时生效的。`sefcontext`模块可以设置永久的安全上下文。

![](img/f6cf099bbac19981b12bf9ebf8830f64_25.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_27.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_29.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_30.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_31.png)

**核心概念**：通过`sefcontext`模块，我们可以为文件指定一个持久化的SELinux标签。

![](img/f6cf099bbac19981b12bf9ebf8830f64_33.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_35.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_36.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_38.png)

**示例**：将`/var/www/html/index.html`文件的安全上下文设置为Web服务器类型（`httpd_sys_content_t`）。

![](img/f6cf099bbac19981b12bf9ebf8830f64_39.png)

```yaml
- name: 设置index.html的SELinux安全上下文
  sefcontext:
    target: /var/www/html/index.html
    setype: httpd_sys_content_t
    state: present
```

![](img/f6cf099bbac19981b12bf9ebf8830f64_41.png)

**注意**：如果目标系统是最小化安装，可能需要先安装`python3-libselinux`包才能使用此模块。

![](img/f6cf099bbac19981b12bf9ebf8830f64_42.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_43.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_45.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_46.png)

### 2. `blockinfile` 模块：以文本块形式管理文件

![](img/f6cf099bbac19981b12bf9ebf8830f64_48.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_50.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_52.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_54.png)

我们之前学过`lineinfile`模块，它可以按行添加或修改文件内容。而`blockinfile`模块则是以文本块的形式来处理文件内容，它非常适合插入或替换多行配置。

![](img/f6cf099bbac19981b12bf9ebf8830f64_56.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_57.png)

**核心概念**：`blockinfile`模块通过定义开始和结束标记，来管理一个完整的文本块。

![](img/f6cf099bbac19981b12bf9ebf8830f64_59.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_60.png)

以下是`blockinfile`模块的使用方法：

![](img/f6cf099bbac19981b12bf9ebf8830f64_62.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_63.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_64.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_65.png)

*   **`path`**：指定要修改的目标文件路径。
*   **`block`**：定义要插入的文本块内容。
*   **`marker`**：自定义标记文本，用于标识被管理的文本块范围。默认标记为`# {mark} ANSIBLE MANAGED BLOCK`和`# {mark} ANSIBLE MANAGED BLOCK`。

![](img/f6cf099bbac19981b12bf9ebf8830f64_67.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_68.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_70.png)

**示例**：在`/etc/httpd/conf/httpd.conf`文件的`<VirtualHost>`块后追加一段配置。

![](img/f6cf099bbac19981b12bf9ebf8830f64_72.png)

```yaml
- name: 在httpd.conf中插入配置块
  blockinfile:
    path: /etc/httpd/conf/httpd.conf
    block: |
      <Directory "/var/www/html/secure">
        Require all granted
        SSLRequireSSL
      </Directory>
    marker: "# {mark} CUSTOM SECURE DIRECTORY"
    insertafter: '<VirtualHost'
```

运行后，文件中会添加指定内容，并用自定义的注释行包围，便于Ansible后续识别和管理。

![](img/f6cf099bbac19981b12bf9ebf8830f64_74.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_76.png)

### 3. `stat` 模块：检索文件状态信息

![](img/f6cf099bbac19981b12bf9ebf8830f64_78.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_79.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_80.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_81.png)

`stat`模块用于获取远程节点上文件或目录的详细状态信息（类似于Linux的`stat`命令）。它返回一个包含文件各种属性的字典，我们可以注册为变量并在后续任务中引用。

![](img/f6cf099bbac19981b12bf9ebf8830f64_83.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_85.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_87.png)

**核心概念**：`stat`模块返回一个哈希字典，包含`checksum`、`size`、`mode`、`mtime`等文件属性。

![](img/f6cf099bbac19981b12bf9ebf8830f64_89.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_90.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_91.png)

**示例**：获取`/etc/passwd`文件的MD5校验和并输出。

![](img/f6cf099bbac19981b12bf9ebf8830f64_92.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_93.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_95.png)

```yaml
- name: 获取/etc/passwd文件状态
  stat:
    path: /etc/passwd
  register: file_stat

![](img/f6cf099bbac19981b12bf9ebf8830f64_96.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_98.png)

- name: 输出文件的MD5校验和
  debug:
    msg: "文件校验和为：{{ file_stat.stat.checksum }}"

![](img/f6cf099bbac19981b12bf9ebf8830f64_100.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_102.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_104.png)

- name: 输出文件的所有状态信息
  debug:
    var: file_stat.stat
```

![](img/f6cf099bbac19981b12bf9ebf8830f64_106.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_108.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_110.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_112.png)

第一个任务将文件状态信息注册到变量`file_stat`中。第二个任务通过`file_stat.stat.checksum`引用了其MD5校验和。第三个任务使用`debug`模块的`var`参数输出完整的`stat`字典，可以看到文件大小、修改时间、权限等所有信息。

![](img/f6cf099bbac19981b12bf9ebf8830f64_114.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_115.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_117.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_119.png)

---

![](img/f6cf099bbac19981b12bf9ebf8830f64_121.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_123.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_125.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_127.png)

## 第二部分：JINJA2模板引擎 🧩

![](img/f6cf099bbac19981b12bf9ebf8830f64_129.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_131.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_133.png)

Ansible虽然有许多模块可以修改现有文件，但管理配置文件更强大、灵活的方法是使用JINJA2模板。通过构建模板，我们可以利用Ansible变量和事实（facts）来编写一个通用的配置文件，在部署时自动为每台受管主机进行自定义，从而实现配置的标准化和自动化。

![](img/f6cf099bbac19981b12bf9ebf8830f64_135.png)

### 1. JINJA2模板基础

![](img/f6cf099bbac19981b12bf9ebf8830f64_137.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_139.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_141.png)

JINJA2是基于Python的模板引擎。在Ansible中，模板文件通常以`.j2`为后缀。模板中包含静态文本和动态部分，动态部分会在渲染时被替换为实际值。

![](img/f6cf099bbac19981b12bf9ebf8830f64_143.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_144.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_145.png)

**核心分隔符**：

![](img/f6cf099bbac19981b12bf9ebf8830f64_147.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_148.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_150.png)

*   **`{% ... %}`**：用于执行控制语句，如循环和条件判断。
    *   例如：`{% for item in list %} ... {% endfor %}`
*   **`{{ ... }}`**：用于输出变量或表达式的值。
    *   例如：`{{ ansible_default_ipv4.address }}`
*   **`{# ... #}`**：用于添加注释，注释内容不会出现在最终生成的文件中。
    *   例如：`{# 这是一行注释 #}`

![](img/f6cf099bbac19981b12bf9ebf8830f64_152.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_154.png)

### 2. 模板使用流程：一个Web服务器配置案例

![](img/f6cf099bbac19981b12bf9ebf8830f64_156.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_158.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_159.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_160.png)

让我们通过一个部署Apache HTTPD配置的案例，理解模板的使用流程。

**目标**：为不同的服务器生成自定义的`httpd.conf`配置文件，主要区别在于监听的IP地址。

![](img/f6cf099bbac19981b12bf9ebf8830f64_162.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_163.png)

**步骤**：

1.  **准备模板文件**：首先，从一台已安装Apache的服务器上，提取纯净的配置内容作为模板基础。可以使用命令过滤掉注释和空行。
    ```bash
    grep -v ‘^[[:space:]]*#’ /etc/httpd/conf/httpd.conf | grep -v ‘^$’ > httpd.conf.j2
    ```
    这样得到的`httpd.conf.j2`文件只包含有效配置项。

2.  **修改模板，插入变量**：在模板中，找到需要动态化的部分（如`Listen`指令），用JINJA2变量替换。
    ```jinja2
    # httpd.conf.j2 片段
    ...
    Listen {{ ansible_default_ipv4.address }}:80
    ...
    ```
    这里，`{{ ansible_default_ipv4.address }}`会在模板渲染时被替换为每台主机的实际IP地址。

![](img/f6cf099bbac19981b12bf9ebf8830f64_165.png)

3.  **编写Playbook部署模板**：使用`template`模块将模板渲染并复制到目标主机。
    ```yaml
    - name: 部署自定义的httpd配置
      hosts: web_servers
      tasks:
        - name: 使用模板生成配置文件
          template:
            src: /path/to/templates/httpd.conf.j2
            dest: /etc/httpd/conf/httpd.conf
          notify: restart httpd
      handlers:
        - name: restart httpd
          service:
            name: httpd
            state: restarted
    ```
    `template`模块会自动读取`src`指定的`.j2`模板文件，用主机的变量渲染它，然后将结果保存到`dest`指定的路径。

![](img/f6cf099bbac19981b12bf9ebf8830f64_167.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_169.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_171.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_173.png)

**`copy` vs `template`**：`copy`模块原封不动地复制文件，而`template`模块会在复制前渲染模板中的变量和逻辑。这是两者最本质的区别。

![](img/f6cf099bbac19981b12bf9ebf8830f64_175.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_176.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_177.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_178.png)

### 3. 在模板中使用控制结构

![](img/f6cf099bbac19981b12bf9ebf8830f64_180.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_181.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_183.png)

JINJA2模板的强大之处在于支持逻辑控制。

![](img/f6cf099bbac19981b12bf9ebf8830f64_185.png)

**循环 (`for`)**：用于遍历列表或字典，批量生成内容。

![](img/f6cf099bbac19981b12bf9ebf8830f64_187.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_189.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_190.png)

**语法**：
```jinja2
{% for item in list %}
内容行，可以使用变量 {{ item }}
{% endfor %}
```

![](img/f6cf099bbac19981b12bf9ebf8830f64_191.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_192.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_194.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_195.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_197.png)

**示例**：动态生成`/etc/hosts`文件条目。
```jinja2
{# hosts.j2 模板 #}
127.0.0.1 localhost
::1 localhost

![](img/f6cf099bbac19981b12bf9ebf8830f64_198.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_199.png)

{# 遍历所有主机，生成条目 #}
{% for host in groups[‘all’] %}
{{ hostvars[host].ansible_default_ipv4.address }} {{ hostvars[host].ansible_fqdn }} {{ hostvars[host].ansible_hostname }}
{% endfor %}
```
这个模板会为清单中的所有主机生成一行IP地址、全限定域名和主机名的记录。

![](img/f6cf099bbac19981b12bf9ebf8830f64_201.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_202.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_204.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_206.png)

**条件判断 (`if`)**：根据条件决定是否包含某些内容。

![](img/f6cf099bbac19981b12bf9ebf8830f64_207.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_209.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_211.png)

**语法**：
```jinja2
{% if condition %}
当条件为真时包含的内容
{% endif %}
```

![](img/f6cf099bbac19981b12bf9ebf8830f64_213.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_215.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_216.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_218.png)

**示例**：只在特定IP的主机上包含某段配置。
```jinja2
{% if ansible_default_ipv4.address == ‘192.168.1.100’ %}
# 这是特殊主机100的配置
CustomLog /var/log/httpd/access_special.log combined
{% endif %}
```

![](img/f6cf099bbac19981b12bf9ebf8830f64_219.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_220.png)

![](img/f6cf099bbac19981b12bf9ebf8830f64_221.png)

---

## 本章总结 🎯

本节课中我们一起学习了：
1.  **三个补充的文件管理模块**：`sefcontext`用于设置SELinux安全上下文，`blockinfile`用于以文本块形式管理文件内容，`stat`用于检索文件的详细状态信息。
2.  **JINJA2模板的核心概念与应用**：理解了`{% %}`、`{{ }}`和`{# #}`三个核心分隔符的作用。掌握了通过`template`模块部署模板的完整流程，即“准备模板 -> 插入变量 -> 渲染部署”。
3.  **模板中的逻辑控制**：学习了使用`{% for ... %}`循环来遍历生成内容，以及使用`{% if ... %}`进行条件判断，这使得模板能够根据不同的主机变量动态生成高度定制化的配置文件。

掌握JINJA2模板是迈向Ansible高级自动化的重要一步，它极大地提升了配置管理的灵活性和效率。