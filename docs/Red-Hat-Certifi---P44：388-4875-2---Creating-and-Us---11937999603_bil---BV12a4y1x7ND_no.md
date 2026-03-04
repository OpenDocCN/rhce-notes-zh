# Ansible 角色：第10章：创建与使用角色 🛠️

![](img/9168d8cd67d2ca9fa21312e9b23df99d_1.png)

![](img/9168d8cd67d2ca9fa21312e9b23df99d_3.png)

在本节课中，我们将学习如何创建一个完整的 Ansible 角色，并将其应用到实际的 Playbook 中。我们将通过创建一个名为 “Apache” 的角色来实践，该角色负责安装、配置和管理 Apache HTTP 服务器。

## 概述

上一节我们介绍了 Ansible 角色的基本概念。本节中，我们将动手创建一个具体的角色，并学习如何在 Playbook 中引用和使用它。我们将涵盖创建角色目录结构、定义任务、设置变量、使用模板和处理器等核心步骤。

![](img/9168d8cd67d2ca9fa21312e9b23df99d_5.png)

## 创建角色目录结构

![](img/9168d8cd67d2ca9fa21312e9b23df99d_7.png)

首先，我们需要为新的 “Apache” 角色创建标准的目录结构。这可以通过 `ansible-galaxy init` 命令轻松完成。

```bash
ansible-galaxy init apache
```

执行此命令后，系统会生成一个包含 `tasks`、`defaults`、`templates`、`handlers` 等子目录的骨架结构。

## 定义角色任务

角色的核心逻辑定义在任务文件中。我们需要编辑 `roles/apache/tasks/main.yml` 文件。

![](img/9168d8cd67d2ca9fa21312e9b23df99d_9.png)

以下是该角色需要执行的任务列表：

![](img/9168d8cd67d2ca9fa21312e9b23df99d_11.png)

![](img/9168d8cd67d2ca9fa21312e9b23df99d_13.png)

*   **创建 Web 内容目录**：使用 `file` 模块创建一个目录，并设置其权限和 SELinux 上下文。
*   **安装 Apache 软件包**：使用 `yum` 模块安装最新版本的 `httpd` 软件包。
*   **部署配置文件**：使用 `template` 模块将 Jinja2 模板 `httpd.conf.j2` 推送到目标服务器，并配置 `notify` 以触发处理器。
*   **部署网站首页**：使用 `template` 模块将 `index.html.j2` 模板部署到 Web 内容目录。
*   **启动并启用服务**：使用 `service` 模块确保 `httpd` 服务已启动并设置为开机自启。

## 设置角色变量

为了增加角色的灵活性，我们使用变量。默认变量定义在 `roles/apache/defaults/main.yml` 文件中。

![](img/9168d8cd67d2ca9fa21312e9b23df99d_15.png)

```yaml
apache_content_dir: /webcontent
apache_http_port: 8080
apache_admin: clouduser
```

在任务中，我们可以通过 `{{ variable_name }}` 的形式引用这些变量。

![](img/9168d8cd67d2ca9fa21312e9b23df99d_17.png)

## 创建 Jinja2 模板

![](img/9168d8cd67d2ca9fa21312e9b23df99d_19.png)

![](img/9168d8cd67d2ca9fa21312e9b23df99d_21.png)

模板文件存放在 `roles/apache/templates/` 目录中。它们允许我们动态生成配置文件。

![](img/9168d8cd67d2ca9fa21312e9b23df99d_23.png)

![](img/9168d8cd67d2ca9fa21312e9b23df99d_25.png)

*   **`httpd.conf.j2`**：Apache 主配置文件模板，其中嵌入了变量，如 `Listen {{ apache_http_port }}` 和 `ServerAdmin {{ apache_admin }}`。
*   **`index.html.j2`**：网站首页 HTML 模板，可以包含由 Ansible 事实收集的系统信息。

## 定义处理器

处理器用于响应任务中的变更通知。我们编辑 `roles/apache/handlers/main.yml` 文件来定义一个重启 Apache 的处理器。

```yaml
- name: restart webservers
  service:
    name: httpd
    state: restarted
```

![](img/9168d8cd67d2ca9fa21312e9b23df99d_27.png)

当 `httpd.conf.j2` 模板任务发生变更时，其 `notify: restart apache` 语句会触发此处理器。

![](img/9168d8cd67d2ca9fa21312e9b23df99d_29.png)

## 在 Playbook 中使用角色

![](img/9168d8cd67d2ca9fa21312e9b23df99d_31.png)

创建好角色后，我们可以在 Playbook 中以多种方式调用它。创建一个名为 `role.yml` 的 Playbook。

![](img/9168d8cd67d2ca9fa21312e9b23df99d_33.png)

![](img/9168d8cd67d2ca9fa21312e9b23df99d_34.png)

![](img/9168d8cd67d2ca9fa21312e9b23df99d_35.png)

![](img/9168d8cd67d2ca9fa21312e9b23df99d_37.png)

![](img/9168d8cd67d2ca9fa21312e9b23df99d_39.png)

以下是引用角色的几种方法：

![](img/9168d8cd67d2ca9fa21312e9b23df99d_41.png)

*   **使用 `roles` 关键字**：这是最简洁的方式，直接在 Playbook 中列出角色名。
*   **使用 `include_role` 模块**：提供了更动态的包含方式。

![](img/9168d8cd67d2ca9fa21312e9b23df99d_43.png)

![](img/9168d8cd67d2ca9fa21312e9b23df99d_45.png)

![](img/9168d8cd67d2ca9fa21312e9b23df99d_47.png)

在 Playbook 中，我们还可以定义变量来覆盖角色中的默认值。例如，将 `apache_http_port` 从默认的 `8080` 改为 `80`。

![](img/9168d8cd67d2ca9fa21312e9b23df99d_49.png)

![](img/9168d8cd67d2ca9fa21312e9b23df99d_51.png)

```yaml
vars:
  apache_http_port: 80
```

![](img/9168d8cd67d2ca9fa21312e9b23df99d_53.png)

## 验证角色执行结果

运行 Playbook 后，我们可以通过一系列命令验证角色是否成功执行：

![](img/9168d8cd67d2ca9fa21312e9b23df99d_55.png)

1.  检查 `/webcontent` 目录是否被创建。
2.  验证 `httpd` 服务是否已安装、启用并正在运行。
3.  检查 `httpd.conf` 和 `index.html` 文件的内容是否正确。
4.  使用 `curl` 命令测试网站是否可以通过指定的端口（如 80）正常访问。

![](img/9168d8cd67d2ca9fa21312e9b23df99d_57.png)

## 总结

![](img/9168d8cd67d2ca9fa21312e9b23df99d_59.png)

![](img/9168d8cd67d2ca9fa21312e9b23df99d_61.png)

本节课中我们一起学习了 Ansible 角色的完整创建和使用流程。我们实践了从初始化角色结构、编写任务和处理器、定义变量和模板，到最终在 Playbook 中集成并运行角色的全过程。掌握角色的使用能极大地提升 Ansible 代码的可重用性、可维护性和组织性。