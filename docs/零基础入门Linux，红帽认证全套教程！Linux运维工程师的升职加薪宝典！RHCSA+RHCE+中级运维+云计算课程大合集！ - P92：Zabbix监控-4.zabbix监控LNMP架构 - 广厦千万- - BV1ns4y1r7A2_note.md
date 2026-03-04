# Zabbix监控：P92：Zabbix监控LNMP架构

![](img/8aa73cfdb5005f57f15b0b31f134ee2e_1.png)

![](img/8aa73cfdb5005f57f15b0b31f134ee2e_3.png)

![](img/8aa73cfdb5005f57f15b0b31f134ee2e_5.png)

![](img/8aa73cfdb5005f57f15b0b31f134ee2e_7.png)

![](img/8aa73cfdb5005f57f15b0b31f134ee2e_9.png)

![](img/8aa73cfdb5005f57f15b0b31f134ee2e_11.png)

## 概述
在本节课中，我们将学习如何部署一套完整的LNMP（Linux, Nginx, MySQL, PHP）架构，并上线一个论坛项目。随后，我们将为该项目添加Redis缓存服务以提升性能，并最终通过Zabbix监控整个LNMP架构的运行状态。

## 环境检查与回顾
上一节我们介绍了Zabbix报警的配置流程。本节中，我们首先检查上节课配置的Zabbix服务器与客户端服务是否正常运行。

以下是检查步骤：
1.  在Zabbix服务器上，使用 `ss -lntp` 命令检查端口 `10050`（Agent）和 `10051`（Server）是否处于监听状态。
2.  在Zabbix客户端上，检查端口 `10050` 是否处于监听状态。
3.  通过浏览器访问Zabbix Web界面（例如 `http://192.168.0.70/zabbix`），使用管理员账号登录，确认主机状态正常且能获取到监控数据。

![](img/8aa73cfdb5005f57f15b0b31f134ee2e_13.png)

![](img/8aa73cfdb5005f57f15b0b31f134ee2e_15.png)

![](img/8aa73cfdb5005f57f15b0b31f134ee2e_17.png)

![](img/8aa73cfdb5005f57f15b0b31f134ee2e_19.png)

![](img/8aa73cfdb5005f57f15b0b31f134ee2e_20.png)

确认环境正常后，我们简单回顾报警配置的逻辑关系：
*   **监控项**：负责从主机采集数据。
*   **触发器**：为监控项的数据定义一个报警条件（阈值）。
*   **动作**：当触发器条件被满足时，执行发送报警消息等操作。

![](img/8aa73cfdb5005f57f15b0b31f134ee2e_21.png)

![](img/8aa73cfdb5005f57f15b0b31f134ee2e_23.png)

![](img/8aa73cfdb5005f57f15b0b31f134ee2e_25.png)

![](img/8aa73cfdb5005f57f15b0b31f134ee2e_27.png)

![](img/8aa73cfdb5005f57f15b0b31f134ee2e_29.png)

![](img/8aa73cfdb5005f57f15b0b31f134ee2e_31.png)

![](img/8aa73cfdb5005f57f15b0b31f134ee2e_33.png)

![](img/8aa73cfdb5005f57f15b0b31f134ee2e_35.png)

## 部署LNMP架构
接下来，我们将在Zabbix客户端主机上部署LNMP架构。

![](img/8aa73cfdb5005f57f15b0b31f134ee2e_37.png)

![](img/8aa73cfdb5005f57f15b0b31f134ee2e_38.png)

### 安装必要软件包
首先，安装LNMP架构所需的基础软件包。

![](img/8aa73cfdb5005f57f15b0b31f134ee2e_40.png)

![](img/8aa73cfdb5005f57f15b0b31f134ee2e_42.png)

![](img/8aa73cfdb5005f57f15b0b31f134ee2e_44.png)

![](img/8aa73cfdb5005f57f15b0b31f134ee2e_46.png)

![](img/8aa73cfdb5005f57f15b0b31f134ee2e_48.png)

![](img/8aa73cfdb5005f57f15b0b31f134ee2e_50.png)

![](img/8aa73cfdb5005f57f15b0b31f134ee2e_52.png)

![](img/8aa73cfdb5005f57f15b0b31f134ee2e_53.png)

以下是需要安装的软件包列表：
*   `mariadb-server`， `mariadb`：数据库服务。
*   `php`， `php-fpm`：PHP及其FastCGI进程管理器。
*   `php-mysqlnd`：PHP连接MySQL的驱动。
*   `php-gd`， `php-mbstring`：PHP相关依赖。

![](img/8aa73cfdb5005f57f15b0b31f134ee2e_55.png)

![](img/8aa73cfdb5005f57f15b0b31f134ee2e_57.png)

![](img/8aa73cfdb5005f57f15b0b31f134ee2e_59.png)

使用YUM命令进行安装：
```bash
yum install -y mariadb-server mariadb php php-fpm php-mysqlnd php-gd php-mbstring
```

![](img/8aa73cfdb5005f57f15b0b31f134ee2e_61.png)

![](img/8aa73cfdb5005f57f15b0b31f134ee2e_63.png)

![](img/8aa73cfdb5005f57f15b0b31f134ee2e_64.png)

![](img/8aa73cfdb5005f57f15b0b31f134ee2e_66.png)

### 配置MySQL数据库
安装完成后，开始配置MySQL（MariaDB）数据库。

![](img/8aa73cfdb5005f57f15b0b31f134ee2e_68.png)

![](img/8aa73cfdb5005f57f15b0b31f134ee2e_70.png)

![](img/8aa73cfdb5005f57f15b0b31f134ee2e_72.png)

![](img/8aa73cfdb5005f57f15b0b31f134ee2e_74.png)

1.  编辑MySQL配置文件 `/etc/my.cnf`，在 `[mysqld]` 段落下添加字符集配置，以支持中文：
    ```
    [mysqld]
    character-set-server=utf8
    ```
2.  启动MySQL服务并设置为开机自启：
    ```bash
    systemctl start mariadb
    systemctl enable mariadb
    ```
3.  检查MySQL服务端口（3306）是否成功监听：
    ```bash
    ss -lntp | grep 3306
    ```

![](img/8aa73cfdb5005f57f15b0b31f134ee2e_76.png)

![](img/8aa73cfdb5005f57f15b0b31f134ee2e_78.png)

![](img/8aa73cfdb5005f57f15b0b31f134ee2e_80.png)

### 配置PHP-FPM
接下来，配置PHP-FPM以与Nginx协同工作。

![](img/8aa73cfdb5005f57f15b0b31f134ee2e_82.png)

![](img/8aa73cfdb5005f57f15b0b31f134ee2e_84.png)

1.  编辑PHP-FPM配置文件 `/etc/php-fpm.d/www.conf`。
2.  找到 `user` 和 `group` 配置项（约第39行），将其值从 `apache` 改为 `nginx`。
3.  找到 `pm.status_path` 配置项（约第121行），取消注释，并将其值修改为 `/php_status`，以避免与Nginx状态页面路径冲突。
4.  编辑PHP主配置文件 `/etc/php.ini`，设置正确的时区。找到 `date.timezone` 配置项（约第878行），取消注释并设置为 `Asia/Shanghai`。

![](img/8aa73cfdb5005f57f15b0b31f134ee2e_86.png)

### 安装与配置Nginx
我们将通过Nginx官方仓库安装最新稳定版Nginx。

![](img/8aa73cfdb5005f57f15b0b31f134ee2e_88.png)

![](img/8aa73cfdb5005f57f15b0b31f134ee2e_90.png)

![](img/8aa73cfdb5005f57f15b0b31f134ee2e_91.png)

![](img/8aa73cfdb5005f57f15b0b31f134ee2e_92.png)

![](img/8aa73cfdb5005f57f15b0b31f134ee2e_94.png)

1.  创建Nginx官方YUM仓库文件 `/etc/yum.repos.d/nginx.repo`，并填入以下内容：
    ```
    [nginx-stable]
    name=nginx stable repo
    baseurl=http://nginx.org/packages/centos/$releasever/$basearch/
    gpgcheck=1
    enabled=1
    gpgkey=https://nginx.org/keys/nginx_signing.key
    module_hotfixes=true
    ```
2.  安装Nginx：
    ```bash
    yum install -y nginx
    ```
3.  配置Nginx。备份默认配置文件，然后使用我们预定义的配置。
    ```bash
    cd /etc/nginx/conf.d
    cp default.conf default.conf.bak
    > default.conf # 清空原文件
    vim default.conf
    ```
    将以下配置内容粘贴到文件中，该配置开启了Nginx状态页、PHP状态页，并配置了Nginx与PHP-FPM的连接：
    ```nginx
    server {
        listen       80;
        server_name  localhost;
        root   /usr/share/nginx/html;

        location / {
            index  index.php index.html index.htm;
        }

        location /nginx_status {
            stub_status on;
            access_log off;
            allow 127.0.0.1;
            deny all;
        }

        location ~ \.php$ {
            fastcgi_pass   127.0.0.1:9000;
            fastcgi_index  index.php;
            fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
            include        fastcgi_params;
        }

        location /php_status {
            fastcgi_pass 127.0.0.1:9000;
            fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
            include fastcgi_params;
            access_log off;
            allow 127.0.0.1;
            deny all;
        }
    }
    ```
4.  启动Nginx和PHP-FPM服务，并设置为开机自启：
    ```bash
    systemctl start nginx php-fpm
    systemctl enable nginx php-fpm
    ```
5.  验证服务端口（Nginx: 80, PHP-FPM: 9000）是否正常监听。
6.  访问状态页面进行验证：
    *   Nginx状态页：`http://<客户端IP>/nginx_status`
    *   PHP状态页：`http://<客户端IP>/php_status`

![](img/8aa73cfdb5005f57f15b0b31f134ee2e_96.png)

![](img/8aa73cfdb5005f57f15b0b31f134ee2e_98.png)

![](img/8aa73cfdb5005f57f15b0b31f134ee2e_100.png)

### 测试服务连通性
现在测试Nginx与PHP之间，以及PHP与MySQL之间的连通性。

![](img/8aa73cfdb5005f57f15b0b31f134ee2e_102.png)

![](img/8aa73cfdb5005f57f15b0b31f134ee2e_104.png)

1.  **测试Nginx与PHP**：在Nginx网页根目录创建PHP信息页。
    ```bash
    cd /usr/share/nginx/html
    vim phpinfo.php
    ```
    写入内容 `<?php phpinfo(); ?>`。访问 `http://<客户端IP>/phpinfo.php`，若能显示PHP详细信息，则连通成功。
2.  **测试PHP与MySQL**：在网页根目录创建数据库测试页。
    ```bash
    vim mysql.php
    ```
    写入以下内容（使用无密码的root用户）：
    ```php
    <?php
    $link = mysqli_connect('localhost', 'root', '');
    if (!$link) {
        die('Could not connect: ' . mysqli_error());
    }
    echo 'Connected successfully';
    mysqli_close($link);
    ?>
    ```
    访问 `http://<客户端IP>/mysql.php`，若显示“Connected successfully”，则连通成功。

![](img/8aa73cfdb5005f57f15b0b31f134ee2e_106.png)

![](img/8aa73cfdb5005f57f15b0b31f134ee2e_108.png)

![](img/8aa73cfdb5005f57f15b0b31f134ee2e_110.png)

![](img/8aa73cfdb5005f57f15b0b31f134ee2e_112.png)

![](img/8aa73cfdb5005f57f15b0b31f134ee2e_114.png)

## 部署论坛项目
LNMP架构测试无误后，我们将部署一个Discuz!论坛项目。

![](img/8aa73cfdb5005f57f15b0b31f134ee2e_116.png)

![](img/8aa73cfdb5005f57f15b0b31f134ee2e_118.png)

1.  清理Nginx默认网页目录，并上传Discuz!安装包。
    ```bash
    cd /usr/share/nginx/html
    rm -rf *
    # 上传 Discuz_X3.4_SC_UTF8.zip 到当前目录
    ```
2.  解压安装包，并将程序文件移动到网页根目录。
    ```bash
    yum install -y unzip
    unzip Discuz_X3.4_SC_UTF8.zip
    mv upload/* .
    ```
3.  修改项目文件的所有者为Nginx用户，确保Nginx有写入权限。
    ```bash
    chown -R nginx:nginx /usr/share/nginx/html/
    ```
4.  通过浏览器访问客户端IP地址，进入Discuz!安装向导。
5.  在安装过程中，需要配置数据库信息。首先在MySQL中创建数据库和用户。
    ```bash
    mysql
    ```
    在MySQL命令行中执行：
    ```sql
    CREATE DATABASE discuz;
    GRANT ALL PRIVILEGES ON discuz.* TO 'discuz'@'localhost' IDENTIFIED BY '123456';
    FLUSH PRIVILEGES;
    EXIT;
    ```
6.  在Discuz!安装页面填写数据库信息：
    *   数据库服务器：`localhost`
    *   数据库名：`discuz`
    *   用户名：`discuz`
    *   密码：`123456`
7.  按照向导完成论坛管理员账号设置，直至安装成功。安装完成后，即可登录论坛并发帖测试。

![](img/8aa73cfdb5005f57f15b0b31f134ee2e_120.png)

![](img/8aa73cfdb5005f57f15b0b31f134ee2e_122.png)

![](img/8aa73cfdb5005f57f15b0b31f134ee2e_124.png)

## 集成Redis缓存
为了提升论坛性能，我们将集成Redis作为缓存服务。

![](img/8aa73cfdb5005f57f15b0b31f134ee2e_126.png)

![](img/8aa73cfdb5005f57f15b0b31f134ee2e_128.png)

![](img/8aa73cfdb5005f57f15b0b31f134ee2e_130.png)

![](img/8aa73cfdb5005f57f15b0b31f134ee2e_132.png)

![](img/8aa73cfdb5005f57f15b0b31f134ee2e_134.png)

![](img/8aa73cfdb5005f57f15b0b31f134ee2e_136.png)

1.  安装Redis服务器及PHP的Redis扩展。
    ```bash
    yum install -y redis php-pecl-redis
    ```
2.  重启PHP-FPM服务以加载Redis扩展。
    ```bash
    systemctl restart php-fpm
    ```
3.  配置Discuz!以使用Redis。编辑论坛的全局配置文件。
    ```bash
    vim /usr/share/nginx/html/config/config_global.php
    ```
    找到 `$_config['memory']['redis']['server']` 配置项，将其值设置为 `'127.0.0.1'`。如果Redis有密码，还需配置 `$_config['memory']['redis']['authrequire']` 和 `$_config['memory']['redis']['auth']`。
4.  启动Redis服务并设置为开机自启。
    ```bash
    systemctl start redis
    systemctl enable redis
    ```
5.  登录Discuz!论坛后台（管理中心），在“全局” -> “性能优化” -> “内存优化”中，查看Redis是否已支持并启用。同时，可以连接Redis命令行查看是否有缓存数据写入。
    ```bash
    redis-cli
    KEYS *
    ```

![](img/8aa73cfdb5005f57f15b0b31f134ee2e_138.png)

![](img/8aa73cfdb5005f57f15b0b31f134ee2e_140.png)

## 网站数据统计简介
在项目上线后，了解网站的访问情况至关重要。常见的网站统计指标包括：

以下是三个核心指标：
*   **IP**：独立IP访问数。统计访问网站的独立IP地址数量，一个IP可能代表一个网络设备（如家庭路由器）。
*   **UV**：独立访客数。统计访问网站的实际独立用户数量，比IP更精准（如同一IP下的多个用户）。
*   **PV**：页面浏览量。统计网站所有页面被浏览的总次数，反映了用户的活跃度和内容受欢迎程度。

这些数据可以通过在网站页面中嵌入第三方统计代码（如百度统计、CNZZ）或自行部署日志分析工具来获取。它们对于评估网站流量、用户行为和商业价值具有重要意义。

![](img/8aa73cfdb5005f57f15b0b31f134ee2e_142.png)

## 总结
本节课中，我们一起学习了如何从零开始部署一套LNMP架构，并成功上线了Discuz!论坛项目。我们进一步集成了Redis服务为论坛提供缓存加速。最后，我们介绍了网站数据统计的基本概念和核心指标（IP、UV、PV）。至此，我们已经拥有了一个可供监控的完整应用环境，为后续使用Zabbix对其进行全面监控打下了基础。