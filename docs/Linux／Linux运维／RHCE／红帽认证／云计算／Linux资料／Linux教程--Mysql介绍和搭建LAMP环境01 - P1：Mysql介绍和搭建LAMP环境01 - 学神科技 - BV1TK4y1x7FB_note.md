# Linux运维：01：MySQL介绍与LAMP环境搭建

在本节课中，我们将学习MySQL数据库的基本概念，并通过YUM方式在Linux系统上搭建一个完整的LAMP（Linux + Apache + MySQL + PHP）环境。我们还将通过部署一个个人网站来验证环境的可用性。

## 什么是MySQL？ 🗄️

上一节我们介绍了课程概述，本节中我们来看看MySQL是什么。

MySQL是一个关系型数据库管理系统。常见的Oracle、SQL Server、DB2等也是关系型数据库。与之相对的非关系型数据库有MongoDB、Redis等。

MySQL由瑞典MySQL AB公司开发，目前属于Oracle公司。它是最流行的关系型数据库管理系统之一，主要因其开源免费的特性，被广泛应用于各类Web应用。在Web应用方面，MySQL常被认为是最佳选择。

RDBMS是关系型数据库管理系统的统称。这种系统将数据保存在不同的表中，而非将所有数据放在一个大仓库内，从而提升了速度和灵活性。

MySQL使用的SQL语言是用于访问数据库最常用的标准化语言。这意味着学会一种SQL语言，在其他大部分数据库中也能通用。

MySQL软件采用双授权政策，分为社区版和商业版。由于其体积小、速度快、总体拥有成本低，尤其是开放源码的特点，一般中小型网站的开发都选择MySQL作为网站数据库。搭配PHP和Apache可组成良好的开发环境。

![](img/4d1384ca9eb5861fca5385c488c0c13b_1.png)

## MySQL的安装与配置 ⚙️

上一节我们认识了MySQL，本节中我们来看看如何安装和配置它。

![](img/4d1384ca9eb5861fca5385c488c0c13b_3.png)

在RHEL/CentOS 7.0及之后的系统中，默认使用MariaDB代替了MySQL数据库。这是因为Oracle收购MySQL后，存在闭源风险。MySQL的创始人以他女儿Maria的名字创建了MariaDB分支，它被看作是MySQL的替代品，两者在使用上差异不大。

![](img/4d1384ca9eb5861fca5385c488c0c13b_5.png)

我们将使用YUM包管理器来安装LAMP环境，YUM会自动解决软件依赖关系。

以下是安装LAMP环境所需的软件包列表：
*   `httpd`： Apache Web服务器。
*   `mariadb-server`： MariaDB数据库服务器。
*   `mariadb`： MariaDB数据库客户端。
*   `php`： PHP解释器。
*   `php-mysql`： PHP连接MySQL数据库的驱动模块。

使用以下命令一键安装：
```bash
yum -y install httpd mariadb-server mariadb php php-mysql
```

![](img/4d1384ca9eb5861fca5385c488c0c13b_7.png)

![](img/4d1384ca9eb5861fca5385c488c0c13b_8.png)

安装完成后，启动Apache和MariaDB服务，并设置为开机自启：
```bash
systemctl start httpd
systemctl start mariadb
systemctl enable httpd
systemctl enable mariadb
```

此时，直接输入`mysql`命令可以无密码登录数据库，这存在安全隐患。我们需要进行安全初始化。

MySQL提供了一个安全配置脚本。执行以下命令：
```bash
mysql_secure_installation
```
脚本会引导你完成以下操作：
1.  为root用户设置密码。
2.  移除匿名用户。
3.  禁止root账号远程登录。
4.  移除测试数据库（test）。
5.  重载权限表使设置生效。

安全初始化后，需要使用密码登录数据库：
```bash
mysql -u root -p
```

## 测试LAMP环境 🧪

上一节我们完成了MySQL的安装与安全配置，本节中我们来测试整个LAMP环境是否工作正常。

![](img/4d1384ca9eb5861fca5385c488c0c13b_10.png)

![](img/4d1384ca9eb5861fca5385c488c0c13b_12.png)

首先，测试Apache Web服务。在浏览器中输入服务器的IP地址，如果能看到Apache的默认测试页面，说明Web服务运行正常。

![](img/4d1384ca9eb5861fca5385c488c0c13b_14.png)

接着，测试PHP是否能被Apache正常解析。我们需要创建一个PHP测试页面。

Apache的网站根目录默认位于`/var/www/html/`。在此目录下创建一个名为`index.php`的文件，内容如下：
```php
<?php
phpinfo();
?>
```

修改Apache主配置文件`/etc/httpd/conf/httpd.conf`，找到`ServerName`一行，取消注释并设置为服务器IP，例如：
```
ServerName 192.168.2.63:80
```

重启Apache服务使配置生效：
```bash
systemctl restart httpd
```

![](img/4d1384ca9eb5861fca5385c488c0c13b_16.png)

![](img/4d1384ca9eb5861fca5385c488c0c13b_18.png)

在浏览器中访问服务器的IP地址，如果能看到显示PHP配置信息的页面，则表明LAMP环境已成功搭建，Apache可以正常解析PHP代码。

![](img/4d1384ca9eb5861fca5385c488c0c13b_20.png)

## 实战：部署个人网站 🚀

上一节我们验证了LAMP环境，本节中我们利用该环境实际部署一个个人网站程序（以Ucenter为例）。

![](img/4d1384ca9eb5861fca5385c488c0c13b_22.png)

首先，将网站程序包（如`ucenter.zip`）上传到服务器并解压。网站的核心代码通常在`upload`目录中。

在网站根目录下创建一个子目录用于存放后台管理代码，例如`uc_admin`：
```bash
mkdir /var/www/html/uc_admin
```

![](img/4d1384ca9eb5861fca5385c488c0c13b_24.png)

![](img/4d1384ca9eb5861fca5385c488c0c13b_26.png)

将解压后`upload`目录中的所有文件复制到新建的目录中：
```bash
cp -rp upload/* /var/www/html/uc_admin/
```

![](img/4d1384ca9eb5861fca5385c488c0c13b_28.png)

![](img/4d1384ca9eb5861fca5385c488c0c13b_30.png)

![](img/4d1384ca9eb5861fca5385c488c0c13b_32.png)

网站程序通常需要向某些目录（如`data`）写入数据，需要为Web服务进程（Apache用户）赋予写权限：
```bash
chown -R apache:apache /var/www/html/uc_admin/data/
```

![](img/4d1384ca9eb5861fca5385c488c0c13b_34.png)

![](img/4d1384ca9eb5861fca5385c488c0c13b_36.png)

![](img/4d1384ca9eb5861fca5385c488c0c13b_38.png)

通过浏览器访问安装页面，例如`http://你的服务器IP/uc_admin/`。按照安装向导的提示进行操作，主要步骤包括：
1.  检查环境，根据提示修改PHP配置文件（如开启短标签`short_open_tag = On`）。
2.  配置数据库连接信息（数据库地址、名称、用户名、密码）。
3.  设置网站管理员账号和密码。

![](img/4d1384ca9eb5861fca5385c488c0c13b_40.png)

![](img/4d1384ca9eb5861fca5385c488c0c13b_42.png)

安装完成后，即可通过后台地址登录网站管理界面。按照类似步骤，可以继续部署网站的前台展示页面（如Ucenter Home），从而构建一个完整的个人站点。

## 总结 📝

![](img/4d1384ca9eb5861fca5385c488c0c13b_44.png)

本节课中我们一起学习了MySQL数据库的基本概念，掌握了在Linux系统上使用YUM搭建LAMP环境的方法。我们完成了从软件安装、安全配置到环境测试的全过程，并最终通过部署一个实际的网站程序（Ucenter）来巩固所学知识。这套流程是Linux运维工作中搭建Web应用基础平台的常见操作。