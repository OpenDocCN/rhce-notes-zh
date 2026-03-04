# 红帽RHCE认证考试视频：P11：RHCE集训-2

在本节课中，我们将学习RHCE考试中关于Apache Web服务器、脚本编写、iSCSI存储服务以及MariaDB数据库配置的核心操作。这些是考试中的重点和难点，需要熟练掌握。

![](img/131b5306187f559599ebd91737cf37ed_1.png)

## Apache Web服务器配置

上一节我们完成了基础系统配置，本节中我们来看看Apache Web服务器的相关考题，这部分内容在考试中占比很大。

### 第12题：实现一个Web服务器

在server0上配置一个站点 `http://server0.example.com`。主页文件需从指定URL下载并重命名为 `index.html`，放置在Web服务器的文档根目录下。要求 `example.com` 域中的客户可以访问，但 `my133t.com` 域中的客户不能访问。

以下是具体操作步骤：

1.  在server0上安装Apache软件包。
    ```bash
    yum install -y httpd
    ```

2.  启动httpd服务并设置开机自启，同时在防火墙中允许http服务。
    ```bash
    systemctl enable --now httpd
    firewall-cmd --permanent --add-service=http
    firewall-cmd --reload
    ```

![](img/131b5306187f559599ebd91737cf37ed_3.png)

3.  下载主页文件到默认文档根目录 `/var/www/html` 并重命名。
    ```bash
    wget -O /var/www/html/index.html http://classroom.example.com/materials/station.html
    ```

![](img/131b5306187f559599ebd91737cf37ed_5.png)

4.  编辑虚拟主机配置文件 `/etc/httpd/conf.d/vhost.conf`。
    ```bash
    vi /etc/httpd/conf.d/vhost.conf
    ```
    文件内容如下：
    ```apache
    <VirtualHost *:80>
        ServerName server0.example.com
        DocumentRoot /var/www/html
        <Directory "/var/www/html">
            Require all granted
            Require not host .my133t.com
        </Directory>
    </VirtualHost>
    ```

5.  重启httpd服务使配置生效。
    ```bash
    systemctl restart httpd
    ```

**注意**：检测脚本可能提示SELinux安全上下文错误，但使用默认目录 `/var/www/html` 通常不会有问题，可忽略此警告。

### 第13题：配置安全Web服务（HTTPS）

为 `server0.example.com` 站点配置TLS加密（HTTPS）。需要从指定位置下载证书和密钥文件。

以下是具体操作步骤：

1.  安装Apache的SSL模块。
    ```bash
    yum install -y mod_ssl
    ```

2.  下载所需的证书和密钥文件到指定目录。
    ```bash
    wget -O /etc/pki/tls/certs/server0.crt http://classroom.example.com/pub/tls/certs/server0.crt
    wget -O /etc/pki/tls/private/server0.key http://classroom.example.com/pub/tls/private/server0.key
    wget -O /etc/pki/tls/certs/example-ca.crt http://classroom.example.com/pub/example-ca.crt
    ```
    **关键点**：证书和密钥的文件名必须与配置文件中指定的完全一致。在本实验环境中，检测脚本要求文件名为 `server0.crt` 和 `server0.key`。

3.  编辑SSL配置文件 `/etc/httpd/conf.d/ssl.conf`。
    *   找到并修改 `SSLCertificateFile` 指令，指向证书文件。
    *   找到并修改 `SSLCertificateKeyFile` 指令，指向密钥文件。
    *   找到并取消注释 `SSLCACertificateFile` 指令，将其指向CA证书文件。
    ```apache
    SSLCertificateFile /etc/pki/tls/certs/server0.crt
    SSLCertificateKeyFile /etc/pki/tls/private/server0.key
    SSLCACertificateFile /etc/pki/tls/certs/example-ca.crt
    ```

![](img/131b5306187f559599ebd91737cf37ed_7.png)

4.  在防火墙中开启https服务。
    ```bash
    firewall-cmd --permanent --add-service=https
    firewall-cmd --reload
    ```

5.  重启httpd服务。
    ```bash
    systemctl restart httpd
    ```

### 第14题：配置虚拟主机

在server0上扩展Web服务器，为站点 `www0.example.com` 创建一个新的虚拟主机。其文档根目录为 `/var/www/virtual`，主页文件需从指定URL下载。

以下是具体操作步骤：

![](img/131b5306187f559599ebd91737cf37ed_9.png)

1.  为系统临时添加一个主机名别名（非必需，但有助于测试）。
    ```bash
    hostnamectl set-hostname www0.example.com
    ```

![](img/131b5306187f559599ebd91737cf37ed_11.png)

2.  创建虚拟主机的文档根目录。
    ```bash
    mkdir -p /var/www/virtual
    ```

3.  创建一个本地用户 `webadmin`，并赋予其对文档根目录的写权限。
    ```bash
    useradd webadmin
    setfacl -m u:webadmin:rwx /var/www/virtual
    ```

4.  下载主页文件到虚拟主机的文档根目录。
    ```bash
    wget -O /var/www/virtual/index.html http://classroom.example.com/materials/www.html
    ```

5.  为 `www0.example.com` 创建新的虚拟主机配置文件 `/etc/httpd/conf.d/vhost-www0.conf`。
    ```apache
    <VirtualHost *:80>
        ServerName www0.example.com
        DocumentRoot /var/www/virtual
        <Directory "/var/www/virtual">
            Require all granted
        </Directory>
    </VirtualHost>
    ```

6.  重启httpd服务。
    ```bash
    systemctl restart httpd
    ```

![](img/131b5306187f559599ebd91737cf37ed_13.png)

**注意**：检测脚本可能提示用户权限或SELinux问题，若配置无误可忽略。

![](img/131b5306187f559599ebd91737cf37ed_15.png)

### 第15题：配置Web内容的访问

在server0的Web服务器上，于 `/var/www/virtual` 目录下创建一个名为 `private` 的子目录。该目录下的内容仅允许从server0本机浏览，其他客户端均不能访问。

![](img/131b5306187f559599ebd91737cf37ed_17.png)

以下是具体操作步骤：

1.  在虚拟主机文档根目录下创建 `private` 子目录并下载测试页面。
    ```bash
    mkdir /var/www/virtual/private
    wget -O /var/www/virtual/private/index.html http://classroom.example.com/materials/private.html
    ```

![](img/131b5306187f559599ebd91737cf37ed_19.png)

2.  编辑 `www0` 的虚拟主机配置文件 `/etc/httpd/conf.d/vhost-www0.conf`，在 `</VirtualHost>` 标签前添加针对 `private` 目录的访问控制。
    ```apache
    <Directory "/var/www/virtual/private">
        Require all denied
        Require local
    </Directory>
    ```

3.  重启httpd服务。
    ```bash
    systemctl restart httpd
    ```

### 第16题：配置动态Web内容

在server0上配置一个提供动态Web内容的虚拟主机，名为 `webapp0.example.com`，监听在8909端口。需要安装 `mod_wsgi` 模块以支持动态内容。

以下是具体操作步骤：

1.  安装 `mod_wsgi` 模块。
    ```bash
    yum install -y mod_wsgi
    ```

![](img/131b5306187f559599ebd91737cf37ed_21.png)

2.  创建动态Web应用的文档根目录并下载应用文件。
    ```bash
    mkdir -p /var/www/webapp
    wget -O /var/www/webapp/webinfo.wsgi http://classroom.example.com/materials/webinfo.wsgi
    ```

![](img/131b5306187f559599ebd91737cf37ed_23.png)

3.  创建新的虚拟主机配置文件 `/etc/httpd/conf.d/webapp.conf`。
    ```apache
    Listen 8909
    <VirtualHost *:8909>
        ServerName webapp0.example.com
        WSGIScriptAlias / /var/www/webapp/webinfo.wsgi
        <Directory "/var/www/webapp">
            Require all granted
        </Directory>
    </VirtualHost>
    ```

4.  由于8909不是标准HTTP端口，需要为其添加SELinux标签并配置防火墙。
    ```bash
    semanage port -a -t http_port_t -p tcp 8909
    firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="172.25.0.0/24" port port="8909" protocol="tcp" accept'
    firewall-cmd --reload
    ```

5.  重启httpd服务。
    ```bash
    systemctl restart httpd
    ```

![](img/131b5306187f559599ebd91737cf37ed_25.png)

## 脚本编写

![](img/131b5306187f559599ebd91737cf37ed_27.png)

上一节我们完成了Apache服务器的全部配置，本节中我们来看看考试中关于Shell脚本编写的题目。

![](img/131b5306187f559599ebd91737cf37ed_29.png)

![](img/131b5306187f559599ebd91737cf37ed_31.png)

### 第17题：创建一个简单的脚本

![](img/131b5306187f559599ebd91737cf37ed_33.png)

![](img/131b5306187f559599ebd91737cf37ed_35.png)

在server0上创建脚本 `/root/foo.sh`。当脚本参数为 `redhat` 时输出 `fedora`；参数为 `fedora` 时输出 `redhat`；无参数或参数错误时输出使用说明。

![](img/131b5306187f559599ebd91737cf37ed_37.png)

![](img/131b5306187f559599ebd91737cf37ed_39.png)

以下是脚本内容：
```bash
#!/bin/bash
case $1 in
    redhat)
        echo "fedora"
        ;;
    fedora)
        echo "redhat"
        ;;
    *)
        echo "/root/foo.sh redhat|fedora"
        ;;
esac
```
创建后需赋予执行权限：
```bash
chmod +x /root/foo.sh
```

![](img/131b5306187f559599ebd91737cf37ed_41.png)

![](img/131b5306187f559599ebd91737cf37ed_43.png)

### 第18题：创建批量添加用户的脚本

在server0上创建脚本 `/root/batch_users.sh`，该脚本接受一个包含用户名的文件作为参数，并批量创建用户（使用 `/bin/false` 作为shell）。

以下是脚本内容：
```bash
#!/bin/bash
if [ $# -eq 0 ]; then
    echo "Usage: /root/batch_users.sh <userfile>"
    exit 1
fi

![](img/131b5306187f559599ebd91737cf37ed_45.png)

if [ ! -f $1 ]; then
    echo "Input file not found"
    exit 2
fi

for user in $(cat $1); do
    useradd -s /bin/false $user
done
```
创建后需赋予执行权限：
```bash
chmod +x /root/batch_users.sh
```
**注意**：需要提前从指定URL下载用户列表文件以供测试。

![](img/131b5306187f559599ebd91737cf37ed_47.png)

![](img/131b5306187f559599ebd91737cf37ed_49.png)

![](img/131b5306187f559599ebd91737cf37ed_51.png)

## iSCSI存储服务

![](img/131b5306187f559599ebd91737cf37ed_53.png)

![](img/131b5306187f559599ebd91737cf37ed_55.png)

上一节我们完成了脚本编写的练习，本节中我们来看看iSCSI存储服务的配置，这是RHCE考试中的另一个重点。

![](img/131b5306187f559599ebd91737cf37ed_57.png)

![](img/131b5306187f559599ebd91737cf37ed_59.png)

### 第19题：配置iSCSI服务器端

在server0上配置iSCSI target，提供一个后端大小为3G的LVM逻辑卷 `iscsi-storage`。iSCSI Qualified Name (IQN) 为 `iqn.2014-11.com.example:server0`，服务端口为3260，且只允许 `desktop0.example.com` 访问。

以下是具体操作步骤：

1.  创建所需的LVM逻辑卷。
    ```bash
    pvcreate /dev/vdb
    vgcreate vg0 /dev/vdb
    lvcreate -n iscsi-storage -L 3G vg0
    ```

2.  安装targetcli管理工具。
    ```bash
    yum install -y targetcli
    ```

3.  启动并启用target服务，配置防火墙。
    ```bash
    systemctl enable --now target
    firewall-cmd --permanent --add-port=3260/tcp
    firewall-cmd --reload
    ```

![](img/131b5306187f559599ebd91737cf37ed_61.png)

4.  使用 `targetcli` 交互式命令进行配置。
    ```bash
    targetcli
    ```
    在targetcli中依次执行：
    ```
    /backstores/block create iscsi-storage /dev/vg0/iscsi-storage
    /iscsi create iqn.2014-11.com.example:server0
    /iscsi/iqn.2014-11.com.example:server0/tpg1/acls create iqn.2014-11.com.example:desktop0
    /iscsi/iqn.2014-11.com.example:server0/tpg1/luns create /backstores/block/iscsi-storage
    /iscsi/iqn.2014-11.com.example:server0/tpg1/portals create 172.25.0.11 3260
    saveconfig
    exit
    ```

![](img/131b5306187f559599ebd91737cf37ed_63.png)

### 第20题：配置iSCSI客户端

在desktop0上配置iSCSI initiator，连接到server0提供的target，并自动挂载。要求在发现的磁盘上创建一个2100MB的分区，格式化为ext4文件系统，并配置开机自动挂载到 `/mnt/data`。

以下是具体操作步骤：

1.  在desktop0上安装iSCSI客户端软件。
    ```bash
    yum install -y iscsi-initiator-utils
    ```

2.  编辑 `/etc/iscsi/initiatorname.iscsi`，设置客户端的IQN（需与服务器端ACL匹配）。
    ```
    InitiatorName=iqn.2014-11.com.example:desktop0
    ```

3.  启动并启用iscsi服务。
    ```bash
    systemctl enable --now iscsi
    ```

![](img/131b5306187f559599ebd91737cf37ed_65.png)

![](img/131b5306187f559599ebd91737cf37ed_67.png)

![](img/131b5306187f559599ebd91737cf37ed_69.png)

4.  发现并登录服务器端的target。
    ```bash
    iscsiadm -m discovery -t st -p server0
    iscsiadm -m node -l
    ```

![](img/131b5306187f559599ebd91737cf37ed_71.png)

5.  对新发现的磁盘（如 `/dev/sda`）进行分区、格式化、挂载。
    ```bash
    parted /dev/sda mklabel gpt
    parted /dev/sda mkpart primary 1MiB 2100MiB
    mkfs.ext4 /dev/sda1
    mkdir /mnt/data
    ```
    获取文件系统UUID并写入 `/etc/fstab` 实现开机自动挂载，**务必添加 `_netdev` 挂载选项**。
    ```bash
    blkid /dev/sda1
    echo "UUID=<上面命令输出的UUID> /mnt/data ext4 defaults,_netdev 0 0" >> /etc/fstab
    mount -a
    ```

## MariaDB数据库

![](img/131b5306187f559599ebd91737cf37ed_73.png)

上一节我们配置了网络存储服务，本节中我们最后来学习MariaDB数据库的配置与查询。

![](img/131b5306187f559599ebd91737cf37ed_75.png)

### 第21题：配置MariaDB数据库

在server0上安装并配置MariaDB数据库。创建名为 `contacts` 的数据库，并从指定备份文件恢复数据。配置要求：数据库只能被本地访问；除root用户外，创建一个用户 `raikon`，其密码为 `atenorth`，且仅对 `contacts` 数据库拥有查询权限；root用户的密码也是 `atenorth`，且不允许远程登录。

以下是具体操作步骤：

1.  安装MariaDB服务器软件包组。
    ```bash
    yum groupinstall -y "MariaDB" "MariaDB Client"
    ```

2.  启动并启用mariadb服务，配置防火墙。
    ```bash
    systemctl enable --now mariadb
    firewall-cmd --permanent --add-service=mysql
    firewall-cmd --reload
    ```

![](img/131b5306187f559599ebd91737cf37ed_77.png)

3.  运行安全初始化脚本，设置root密码并移除匿名用户、禁止root远程登录等。
    ```bash
    mysql_secure_installation
    ```
    根据提示输入密码 `atenorth` 并回答后续问题。

![](img/131b5306187f559599ebd91737cf37ed_79.png)

![](img/131b5306187f559599ebd91737cf37ed_81.png)

![](img/131b5306187f559599ebd91737cf37ed_83.png)

4.  登录数据库，创建 `contacts` 数据库。
    ```bash
    mysql -u root -p
    ```
    ```sql
    CREATE DATABASE contacts;
    ```

5.  下载数据库备份文件并导入到 `contacts` 数据库。
    ```bash
    wget http://classroom.example.com/materials/users.mdb
    mysql -u root -p contacts < users.mdb
    ```

6.  创建数据库用户 `raikon` 并授予查询权限。
    ```sql
    GRANT SELECT ON contacts.* TO 'raikon'@'localhost' IDENTIFIED BY 'atenorth';
    FLUSH PRIVILEGES;
    ```

![](img/131b5306187f559599ebd91737cf37ed_85.png)

**注意**：需要在客户端desktop0上也安装 `mariadb` 客户端软件包，否则检测脚本可能报错。

![](img/131b5306187f559599ebd91737cf37ed_87.png)

### 第22题：执行数据库查询

![](img/131b5306187f559599ebd91737cf37ed_89.png)

![](img/131b5306187f559599ebd91737cf37ed_91.png)

基于 `contacts` 数据库中的表，回答两个查询问题。需要了解表结构（`names`, `passwords`, `location`）及其关联关系（通过 `id` 字段）。

1.  **问题一**：密码为 `solarwind` 的人叫什么名字？
    *   **分析**：在 `passwords` 表中找到 `password` 为 `solarwind` 的记录，获取其 `id`，再到 `names` 表中根据此 `id` 查找对应的 `firstname` 和 `lastname`。
    *   **查询语句**：
        ```sql
        SELECT firstname, lastname FROM names WHERE id = (SELECT id FROM passwords WHERE password = 'solarwind');
        ```
    *   **答案**：`John Wang` （注意填写顺序：`firstname` + 空格 + `lastname`）。

![](img/131b5306187f559599ebd91737cf37ed_93.png)

2.  **问题二**：有多少人姓 `Barbara` 并且住在 `Sunnyvale`？
    *   **分析**：在 `names` 表中找到所有 `lastname` 为 `Barbara` 的记录，获取其 `id` 列表。在 `location` 表中，统计 `location` 为 `Sunnyvale` 且 `id` 在上一步获取的列表中的记录数量。
    *   **查询语句**：
        ```sql
        SELECT COUNT(*) FROM location WHERE location = 'Sunnyvale' AND id IN (SELECT id FROM names WHERE lastname = 'Barbara');
        ```
    *   **答案**：`2`。

![](img/131b5306187f559599ebd91737cf37ed_95.png)

![](img/131b5306187f559599ebd91737cf37ed_97.png)

---

![](img/131b5306187f559599ebd91737cf37ed_99.png)

![](img/131b5306187f559599ebd91737cf37ed_101.png)

本节课中我们一起学习了RHCE考试中Apache服务器配置、Shell脚本编写、iSCSI存储服务以及MariaDB数据库管理等核心技能。这些内容在考试中占有重要比重，需要通过反复练习来熟练掌握。请注意，实验环境中的检测脚本可能存在个别误报（如SELinux相关），应以题目要求为准进行配置和验证。