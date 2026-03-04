# RHEL 9 精通课程：P49：04-04-025 Webserver - 在 Linux 上设置本地开发环境 🖥️

![](img/69ad03f4ef465e5529b7acd3c4486b19_1.png)

在本节课中，我们将学习如何在 Linux 系统上搭建一个完整的本地 Web 开发环境。我们将安装并配置 Apache 服务器、PHP 和 MySQL 数据库，并学习如何通过虚拟主机管理多个本地网站。

![](img/69ad03f4ef465e5529b7acd3c4486b19_3.png)

---

![](img/69ad03f4ef465e5529b7acd3c4486b19_5.png)

## 概述

当您需要使用 PHP、Ruby 或其他脚本语言开发应用程序时，最好先在本地机器上进行开发，而不是频繁地将文件上传到远程服务器进行测试。对于需要服务器的特定语言，您需要在本地设置一个服务器环境。如果您只是构建一个前端应用程序（例如静态 HTML 网站），则不需要本地服务器，直接在浏览器中打开 HTML 文件即可。但是，如果您正在开发使用 PHP 或需要连接 MySQL 数据库的应用程序，那么您需要在计算机上运行一个服务器。Linux 是一个完美的起点，因为它内置了服务器系统，非常适合作为本地开发环境。

![](img/69ad03f4ef465e5529b7acd3c4486b19_7.png)

## 安装 LAMP 堆栈

![](img/69ad03f4ef465e5529b7acd3c4486b19_9.png)

首先，我们要在机器上安装一个 LAMP 堆栈。LAMP 是 Linux、Apache、MySQL 和 PHP 的缩写，它代表了一个功能齐全的、能在 PHP 中运行 SQL 的服务器环境。安装 Linux 操作系统后，我们已经完成了 50% 的工作，因为我们已经有了 Linux (L) 和 Apache (A)。

![](img/69ad03f4ef465e5529b7acd3c4486b19_11.png)

![](img/69ad03f4ef465e5529b7acd3c4486b19_13.png)

要测试 Apache 服务器是否运行，您可以打开一个新的浏览器标签页，输入 `localhost`。新安装的 Linux 的 `localhost` 应该直接指向 Apache 的欢迎页面。

![](img/69ad03f4ef465e5529b7acd3c4486b19_15.png)

## 检查 Apache 状态

![](img/69ad03f4ef465e5529b7acd3c4486b19_17.png)

![](img/69ad03f4ef465e5529b7acd3c4486b19_19.png)

让我们首先访问终端，检查 Apache 是否已安装并正在运行。

要检查是否安装了 Apache，可以输入命令：
```bash
which apache2
```
如果没有收到错误，并且返回了 Apache 的实际安装目录，则意味着 Apache 已安装在您的机器上。

要检查 Apache 的服务状态，可以输入命令：
```bash
sudo service apache2 status
```
系统会询问您的 root 密码。如果服务状态显示为绿色并处于“active (running)”状态，则表示 Apache 正在运行。如果 Apache 服务器未运行（状态为 inactive 或 disabled），您可以通过以下命令启动它：
```bash
sudo service apache2 start
```

![](img/69ad03f4ef465e5529b7acd3c4486b19_21.png)

## 理解本地主机解析

当我们在浏览器地址栏中输入 `localhost` 时，它会进入我们的 Apache 文件夹，而不是指向 Google 或其他远程服务器。这是因为 Apache 处理了这些请求，并且特定的 URL 映射关系由 `hosts` 文件定义。

![](img/69ad03f4ef465e5529b7acd3c4486b19_23.png)

![](img/69ad03f4ef465e5529b7acd3c4486b19_25.png)

要检查 `hosts` 文件，我们可以在终端中使用文本编辑器打开它。例如，使用 Sublime Text：
```bash
subl /etc/hosts
```
在 `hosts` 文件中，您会看到类似以下的行：
```
127.0.0.1   localhost
```
`127.0.0.1` 是本地主机的 IP 地址，它仅用于本地计算机。每次我们想要将某个域名指向本地 Apache 服务器时，都可以在这里添加映射。

例如，添加一行：
```
127.0.0.1   test.dev
```
保存文件后，每次在浏览器中输入 `test.dev` 并按下回车，它就会指向我们的本地 Apache 安装，而不是尝试访问互联网上名为 `test.dev` 的网站。

![](img/69ad03f4ef465e5529b7acd3c4486b19_27.png)

## 配置网站根目录

![](img/69ad03f4ef465e5529b7acd3c4486b19_29.png)

![](img/69ad03f4ef465e5529b7acd3c4486b19_31.png)

接下来，我们需要将这个本地 IP 地址映射到服务器中的实际文件夹，以模拟真实网站的工作方式。在 Ubuntu 的 Apache 演示页面中，我们可以看到默认的服务器文件夹位于 `/var/www/html`。

![](img/69ad03f4ef465e5529b7acd3c4486b19_33.png)

![](img/69ad03f4ef465e5529b7acd3c4486b19_35.png)

要查看此位置的内容，可以打开文件管理器导航到 `/var/www`。您会看到 `html` 文件夹，这是 Apache 默认的网站根目录。我们需要在此位置创建与本地网站相关的所有文件夹。

由于 `/var/www` 目录受 root 权限保护，建议通过终端来创建和管理文件夹。让我们为 `test.dev` 网站创建一个新文件夹。

切换回终端，使用以下命令创建目录：
```bash
sudo mkdir -p /var/www/test
```
`sudo` 用于获取管理员权限，`mkdir` 是创建目录的命令，`-p` 参数表示如果需要，会创建父目录。

![](img/69ad03f4ef465e5529b7acd3c4486b19_37.png)

现在，我们必须更改此文件夹的权限，以便我们可以创建和修改文件。我们需要更改文件夹的所有权。

![](img/69ad03f4ef465e5529b7acd3c4486b19_39.png)

使用以下命令递归地更改文件夹所有权给当前用户：
```bash
sudo chown -R $USER:$USER /var/www/test
```
`chown` 用于更改所有权，`-R` 表示递归操作，`$USER` 是一个系统变量，代表当前用户名。

![](img/69ad03f4ef465e5529b7acd3c4486b19_41.png)

![](img/69ad03f4ef465e5529b7acd3c4486b19_43.png)

接下来，更改 `/var/www` 目录的权限，使其全局可读：
```bash
sudo chmod -R 755 /var/www
```
`chmod` 用于修改权限，`755` 表示所有者拥有读、写、执行权限，而组用户和其他用户拥有读和执行权限。

现在，我们可以在 `/var/www/test` 目录下创建文件了。让我们创建一个简单的 PHP 文件作为示例。

使用 `touch` 命令创建一个名为 `index.php` 的空文件：
```bash
touch /var/www/test/index.php
```
然后，在编辑器中打开它并添加以下内容：
```php
<?php
echo phpinfo();
?>
```
保存并关闭文件。现在，我们的服务器上有了 `test` 文件夹和 `index.php` 文件。但是，如果我们刷新 `test.dev`，它仍然会显示默认的 Apache 页面，因为我们还没有将域名映射到实际的文件夹。这就是虚拟主机发挥作用的地方。

![](img/69ad03f4ef465e5529b7acd3c4486b19_45.png)

![](img/69ad03f4ef465e5529b7acd3c4486b19_47.png)

## 配置 Apache 虚拟主机

通过虚拟主机，我们可以将文档根目录（网站文件存放的目录）连接到 Apache 服务器，并指定服务器名称（如 `test.dev`）。

![](img/69ad03f4ef465e5529b7acd3c4486b19_49.png)

![](img/69ad03f4ef465e5529b7acd3c4486b19_51.png)

首先，让我们找到 Apache 虚拟主机的配置文件位置。它们通常位于 `/etc/apache2/sites-available/` 目录下。在该目录中，您会看到一个名为 `000-default.conf` 的默认配置文件。

![](img/69ad03f4ef465e5529b7acd3c4486b19_53.png)

![](img/69ad03f4ef465e5529b7acd3c4486b19_55.png)

**重要提示**：永远不要直接编辑 `sites-enabled` 目录下的文件。这个目录由 Apache 自动管理。每次需要创建虚拟主机时，都应在 `sites-available` 目录中创建或编辑文件。

![](img/69ad03f4ef465e5529b7acd3c4486b19_57.png)

让我们复制默认配置文件作为我们新网站的基础。

在终端中，输入以下命令：
```bash
sudo cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/test.dev.conf
```
这会将默认配置文件复制为 `test.dev.conf`。

![](img/69ad03f4ef465e5529b7acd3c4486b19_59.png)

![](img/69ad03f4ef465e5529b7acd3c4486b19_61.png)

现在，我们需要编辑这个新文件。使用文本编辑器打开它：
```bash
sudo subl /etc/apache2/sites-available/test.dev.conf
```
编辑文件内容。一个最基本的虚拟主机配置如下：
```
<VirtualHost *:80>
    ServerAdmin admin@test.dev
    ServerName test.dev
    DocumentRoot /var/www/test

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```
*   `ServerAdmin`：服务器管理员的邮箱（本地环境可随意填写）。
*   `ServerName`：您要使用的域名（如 `test.dev`）。
*   `DocumentRoot`：网站文件的实际路径（如 `/var/www/test`）。
*   `ErrorLog` 和 `CustomLog`：指定错误日志和访问日志的存放位置。

保存并关闭文件。

![](img/69ad03f4ef465e5529b7acd3c4486b19_63.png)

接下来，我们需要启用这个新的虚拟主机站点。使用 Apache 提供的工具 `a2ensite`：
```bash
sudo a2ensite test.dev.conf
```
这个命令会在 `sites-enabled` 目录中创建一个指向我们配置文件的符号链接。

![](img/69ad03f4ef465e5529b7acd3c4486b19_65.png)

最后，重新加载 Apache 配置以使更改生效：
```bash
sudo service apache2 reload
```
现在，在浏览器中访问 `test.dev`，您应该能看到之前创建的 `phpinfo()` 页面了。如果看不到 PHP 信息，说明 PHP 没有安装。

![](img/69ad03f4ef465e5529b7acd3c4486b19_67.png)

![](img/69ad03f4ef465e5529b7acd3c4486b19_69.png)

## 安装 PHP 和 MySQL

![](img/69ad03f4ef465e5529b7acd3c4486b19_71.png)

![](img/69ad03f4ef465e5529b7acd3c4486b19_72.png)

要安装 PHP，首先检查是否已安装：
```bash
php -v
```
如果未安装，可以使用包管理器安装。例如，在 Ubuntu 上：
```bash
sudo apt-get install php
```
对于完整的 LAMP 堆栈，我们还需要数据库。MySQL 是一个流行的选择。

![](img/69ad03f4ef465e5529b7acd3c4486b19_74.png)

![](img/69ad03f4ef465e5529b7acd3c4486b19_76.png)

安装 MySQL 服务器：
```bash
sudo apt-get install mysql-server
```
安装过程中，系统会提示您设置 root 用户的密码。

![](img/69ad03f4ef465e5529b7acd3c4486b19_78.png)

![](img/69ad03f4ef465e5529b7acd3c4486b19_80.png)

安装完成后，检查 MySQL 服务状态：
```bash
sudo systemctl status mysql
```
如果状态显示为“active (running)”，则表示 MySQL 正在运行。

![](img/69ad03f4ef465e5529b7acd3c4486b19_82.png)

为了更方便地管理数据库，您可以安装一个图形化数据库管理工具，如 DBeaver。它是一个免费、开源的通用数据库客户端。

![](img/69ad03f4ef465e5529b7acd3c4486b19_84.png)

![](img/69ad03f4ef465e5529b7acd3c4486b19_86.png)

## 使用自动化脚本简化流程

![](img/69ad03f4ef465e5529b7acd3c4486b19_88.png)

手动设置虚拟主机对于单个站点来说是可以接受的，但如果您需要管理多个网站，这个过程可能会变得繁琐。幸运的是，有自动化脚本可以帮助我们。

![](img/69ad03f4ef465e5529b7acd3c4486b19_90.png)

![](img/69ad03f4ef465e5529b7acd3c4486b19_92.png)

一个流行的脚本是 `virtualhost.sh`。您可以从 GitHub 等平台找到它。下载后，使其可执行并放置到系统路径中，例如 `/usr/local/bin`。

![](img/69ad03f4ef465e5529b7acd3c4486b19_94.png)

基本使用步骤如下：
1.  下载脚本。
2.  赋予执行权限：`chmod +x virtualhost.sh`
3.  移动到系统目录：`sudo mv virtualhost.sh /usr/local/bin/virtualhost`

![](img/69ad03f4ef465e5529b7acd3c4486b19_96.png)

使用脚本创建新站点：
```bash
sudo virtualhost create example.dev
```
使用脚本删除站点：
```bash
sudo virtualhost delete example.dev
```
这个脚本会自动处理创建目录、配置虚拟主机、更新 `hosts` 文件和重载 Apache 等一系列操作，极大地提高了效率。

![](img/69ad03f4ef465e5529b7acd3c4486b19_98.png)

## 总结

![](img/69ad03f4ef465e5529b7acd3c4486b19_100.png)

![](img/69ad03f4ef465e5529b7acd3c4486b19_102.png)

本节课中，我们一起学习了如何在 Linux 系统上搭建一个完整的本地 Web 开发环境。我们涵盖了以下核心步骤：

![](img/69ad03f4ef465e5529b7acd3c4486b19_104.png)

1.  **安装与验证 Apache**：确保 Web 服务器基础运行正常。
2.  **配置本地域名解析**：通过编辑 `hosts` 文件，将自定义域名指向本地服务器。
3.  **设置网站目录与权限**：在 `/var/www/` 下创建网站目录，并正确设置所有权和权限。
4.  **创建与配置虚拟主机**：在 Apache 中创建虚拟主机配置文件，将域名与网站目录绑定。
5.  **安装必要组件**：安装 PHP 以支持动态脚本，安装 MySQL 以提供数据库支持。
6.  **引入自动化工具**：使用 shell 脚本自动化虚拟主机的创建和删除流程，提升工作效率。

![](img/69ad03f4ef465e5529b7acd3c4486b19_106.png)

通过本教程，您已经掌握了在 Linux 上建立和管理本地开发环境的核心技能。无论是手动配置以深入理解原理，还是使用自动化脚本以提高效率，您都可以根据项目需求灵活选择，为后续的 Web 应用开发打下坚实基础。