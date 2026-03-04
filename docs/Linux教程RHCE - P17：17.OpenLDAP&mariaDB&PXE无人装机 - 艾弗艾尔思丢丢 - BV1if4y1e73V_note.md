# Linux教程RHCE：P17：OpenLDAP & MariaDB & PXE无人装机 🚀

![](img/bcff49efd8e6d7a411cb276271242a77_1.png)

![](img/bcff49efd8e6d7a411cb276271242a77_3.png)

![](img/bcff49efd8e6d7a411cb276271242a77_5.png)

![](img/bcff49efd8e6d7a411cb276271242a77_6.png)

在本节课中，我们将学习三个核心主题：OpenLDAP目录服务、MariaDB数据库管理以及PXE网络无人值守安装系统。这些内容涵盖了高级系统管理、数据存储和自动化部署的关键技能。

---

## 概述 📋

本节课内容较多，我们将依次学习：
1.  **OpenLDAP**：一个用于集中式身份验证的目录服务协议。
2.  **MariaDB**：一个开源的关系型数据库管理系统，我们将学习其基本管理操作。
3.  **PXE无人值守安装**：通过网络批量自动化安装操作系统的解决方案。

![](img/bcff49efd8e6d7a411cb276271242a77_8.png)

![](img/bcff49efd8e6d7a411cb276271242a77_9.png)

![](img/bcff49efd8e6d7a411cb276271242a77_10.png)

![](img/bcff49efd8e6d7a411cb276271242a77_12.png)

![](img/bcff49efd8e6d7a411cb276271242a77_14.png)

![](img/bcff49efd8e6d7a411cb276271242a77_16.png)

请注意，OpenLDAP服务端的配置属于进阶内容，难度较高，主要目的是扩展知识面。实际工作中和RHCE考试中，更侧重于客户端的配置和使用。我们将重点讲解客户端配置和数据库的基本操作。

---

## 第一部分：OpenLDAP 目录服务 🔐

![](img/bcff49efd8e6d7a411cb276271242a77_18.png)

![](img/bcff49efd8e6d7a411cb276271242a77_19.png)

上一节我们概述了本节课的内容，本节中我们来看看OpenLDAP服务。

![](img/bcff49efd8e6d7a411cb276271242a77_21.png)

![](img/bcff49efd8e6d7a411cb276271242a77_23.png)

OpenLDAP是一个开源的轻量级目录访问协议（LDAP）实现。目录服务特别适合存储**短小**且**读取频繁**的数据，例如用户账户和密码信息。

### OpenLDAP 的核心概念

*   **作用**：实现集中式身份认证。可以将用户账户信息集中存储在一台服务器上，网络中的其他机器（客户端）都通过这台服务器来验证用户身份。
*   **好处**：
    1.  **统一管理**：只需在中心服务器上创建或修改一次账户，所有客户端立即生效。
    2.  **单点登录**：用户可以使用同一套账户密码登录网络内的不同系统。
*   **数据组织**：为了避免数据重复（如重名用户），OpenLDAP使用树状结构（DIT）和**识别名（DN）**来唯一标识条目。
    *   **DN公式**：`cn=用户名,ou=部门,dc=域名组件1,dc=域名组件2`
    *   例如：`cn=liuchuan,ou=instructor,dc=linuxprobe,dc=com`

### OpenLDAP 服务端配置（了解即可）

![](img/bcff49efd8e6d7a411cb276271242a77_25.png)

![](img/bcff49efd8e6d7a411cb276271242a77_26.png)

![](img/bcff49efd8e6d7a411cb276271242a77_28.png)

![](img/bcff49efd8e6d7a411cb276271242a77_29.png)

由于服务端配置复杂，且RHCE考试不要求，此处仅简述关键流程。工作中配置时，通常遵循“添加模板，再修改”的原则。

![](img/bcff49efd8e6d7a411cb276271242a77_31.png)

![](img/bcff49efd8e6d7a411cb276271242a77_32.png)

![](img/bcff49efd8e6d7a411cb276271242a77_34.png)

以下是配置OpenLDAP服务端的关键步骤：

![](img/bcff49efd8e6d7a411cb276271242a77_36.png)

1.  **安装软件包**：
    ```bash
    yum install -y openldap openldap-servers openldap-clients migrationtools
    ```
2.  **生成加密密码和密钥**：
    ```bash
    slappasswd -s linuxprobe -n > /etc/openldap/passwd
    openssl req -new -x509 -nodes -out /etc/openldap/certs/cert.pem -keyout /etc/openldap/certs/priv.pem -days 365
    ```
3.  **导入基础数据库结构**：
    ```bash
    ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/cosine.ldif
    ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/nis.ldif
    ```
4.  **修改基础配置（修改域名、密码、密钥路径）**：
    创建一个修改文件（如 `chdomain.ldif`），然后导入：
    ```bash
    ldapmodify -Y EXTERNAL -H ldapi:/// -f chdomain.ldif
    ```
5.  **创建组织单元（OU）并导入用户数据**：
    使用 `migrationtools` 工具将本地系统用户（`/etc/passwd`, `/etc/group`）迁移为LDAP格式，然后导入。

### OpenLDAP 客户端配置（重点）🎯

![](img/bcff49efd8e6d7a411cb276271242a77_38.png)

![](img/bcff49efd8e6d7a411cb276271242a77_40.png)

![](img/bcff49efd8e6d7a411cb276271242a77_42.png)

对于客户端，我们的目标是配置系统，使其能够使用远程OpenLDAP服务器进行用户认证。

![](img/bcff49efd8e6d7a411cb276271242a77_44.png)

以下是配置OpenLDAP客户端的步骤：

1.  **安装必要软件**：
    ```bash
    yum install -y openldap-clients nss-pam-ldapd authconfig-gtk krb5-workstation
    ```
2.  **使用图形化工具配置**：
    运行 `authconfig-gtk` 命令，打开图形界面。
    *   在“身份验证”选项卡中，选择“LDAP”。
    *   填写LDAP服务器地址和基础DN（例如 `dc=linuxprobe,dc=com`）。
    *   在“TLS”选项卡中，选择“下载CA证书”，并输入服务端提供证书的URL（如 `http://192.168.10.10/cert.pem`）。
    *   点击“应用”。
3.  **验证配置**：
    配置完成后，原本本地不存在的LDAP用户（如 `ldapuser`）应该可以被查询到，并且可以切换登录。
    ```bash
    id ldapuser
    su - ldapuser
    ```
4.  **挂载用户家目录（可选）**：
    如果服务端通过NFS共享了用户家目录，需要在客户端上挂载，以提供完整的用户环境。
    ```bash
    # 编辑 /etc/fstab
    192.168.10.10:/home/ldapuser /home/ldapuser nfs defaults 0 0
    # 创建挂载点并挂载
    mkdir -p /home/ldapuser
    mount -a
    ```

**总结**：OpenLDAP客户端配置的核心是使用 `authconfig-gtk` 工具将系统认证源指向LDAP服务器。服务端的复杂配置由管理员完成，客户端只需正确连接即可。

![](img/bcff49efd8e6d7a411cb276271242a77_46.png)

![](img/bcff49efd8e6d7a411cb276271242a77_48.png)

---

## 第二部分：MariaDB 数据库管理 🗄️

上一节我们学习了集中式认证，本节中我们来看看如何管理数据库。MariaDB是MySQL的一个流行分支，在RHEL/CentOS 7中已成为默认的数据库管理系统。

![](img/bcff49efd8e6d7a411cb276271242a77_50.png)

### 为什么学习 MariaDB？

![](img/bcff49efd8e6d7a411cb276271242a77_52.png)

1.  **技术趋势**：红帽等公司为摆脱Oracle MySQL的许可限制，转而支持完全开源的MariaDB。
2.  **技能扩展**：系统管理员需要具备基本的数据库管理能力。
3.  **考试要求**：RHCE考试中包含简单的数据库查询题目。

![](img/bcff49efd8e6d7a411cb276271242a77_54.png)

![](img/bcff49efd8e6d7a411cb276271242a77_56.png)

![](img/bcff49efd8e6d7a411cb276271242a77_57.png)

![](img/bcff49efd8e6d7a411cb276271242a77_59.png)

![](img/bcff49efd8e6d7a411cb276271242a77_61.png)

![](img/bcff49efd8e6d7a411cb276271242a77_63.png)

### MariaDB 基本管理操作

以下是管理MariaDB数据库的核心操作：

1.  **安装与初始化**：
    ```bash
    yum install -y mariadb mariadb-server
    systemctl start mariadb
    systemctl enable mariadb
    mysql_secure_installation # 运行安全初始化脚本，设置root密码等
    ```
2.  **登录数据库**：
    ```bash
    mysql -u root -p
    ```
3.  **重置数据库root密码**：
    ```sql
    SET PASSWORD = PASSWORD('linuxprobe');
    ```
4.  **创建新用户并授权**：
    ```sql
    CREATE USER 'andy'@'localhost' IDENTIFIED BY 'linuxprobe';
    GRANT SELECT,UPDATE,DELETE,INSERT ON mysql.* TO 'andy'@'localhost';
    ```
5.  **撤销用户权限**：
    ```sql
    REVOKE SELECT,UPDATE,DELETE,INSERT ON mysql.* FROM 'andy'@'localhost';
    ```
6.  **数据库和表操作**：
    *   **创建数据库**：`CREATE DATABASE linuxprobe;`
    *   **创建表**：
        ```sql
        USE linuxprobe;
        CREATE TABLE mybook (name char(15), price int, pages int);
        ```
    *   **描述表结构**：`DESC mybook;`
7.  **数据的增删改查（CRUD）**：
    *   **插入数据**：
        ```sql
        INSERT INTO mybook(name, price, pages) VALUES('linuxprobe1', 60, 518);
        ```
    *   **查询数据**：
        ```sql
        SELECT * FROM mybook; -- 查询所有
        SELECT name, price FROM mybook; -- 查询特定列
        SELECT * FROM mybook WHERE price < 50; -- 条件查询
        SELECT * FROM mybook WHERE name='linuxprobe3'; -- 精确查询
        ```
    *   **更新数据**：
        ```sql
        UPDATE mybook SET price=50 WHERE name='linuxprobe1';
        ```
    *   **删除数据**：`DELETE FROM mybook;` (清空表) 或带条件的删除。
8.  **数据库备份与恢复**：
    *   **备份**：
        ```bash
        mysqldump -u root -p linuxprobe > /root/linuxprobeDB.dump
        ```
    *   **恢复**：
        ```bash
        mysql -u root -p linuxprobe < /root/linuxprobeDB.dump
        ```

![](img/bcff49efd8e6d7a411cb276271242a77_65.png)

![](img/bcff49efd8e6d7a411cb276271242a77_67.png)

**总结**：对于系统管理员，需要掌握MariaDB的安装、用户权限管理、基本的SQL操作（尤其是`SELECT`查询）以及数据库的备份恢复。这些技能足以应对日常维护和RHCE考试的要求。

![](img/bcff49efd8e6d7a411cb276271242a77_69.png)

![](img/bcff49efd8e6d7a411cb276271242a77_70.png)

---

![](img/bcff49efd8e6d7a411cb276271242a77_72.png)

![](img/bcff49efd8e6d7a411cb276271242a77_73.png)

![](img/bcff49efd8e6d7a411cb276271242a77_75.png)

![](img/bcff49efd8e6d7a411cb276271242a77_76.png)

![](img/bcff49efd8e6d7a411cb276271242a77_77.png)

## 第三部分：PXE + Kickstart 无人值守安装系统 🤖

![](img/bcff49efd8e6d7a411cb276271242a77_79.png)

![](img/bcff49efd8e6d7a411cb276271242a77_81.png)

上一节我们管理了静态数据，本节中我们来看看如何自动化部署系统。PXE（预启动执行环境）配合Kickstart应答文件，可以实现无需人工干预的网络化批量系统安装。

![](img/bcff49efd8e6d7a411cb276271242a77_83.png)

![](img/bcff49efd8e6d7a411cb276271242a77_85.png)

### 工作原理

![](img/bcff49efd8e6d7a411cb276271242a77_87.png)

![](img/bcff49efd8e6d7a411cb276271242a77_88.png)

![](img/bcff49efd8e6d7a411cb276271242a77_90.png)

1.  客户端开机从网卡启动，通过**DHCP**服务器获取IP地址和引导文件位置。
2.  客户端通过**TFTP**协议从服务器下载引导文件（如 `pxelinux.0`）和内核镜像。
3.  客户端启动引导程序，读取配置文件（如 `default`），指定安装源（通过**HTTP**或FTP）和Kickstart应答文件（`ks.cfg`）的位置。
4.  客户端从**HTTP**服务器下载完整的系统安装文件，并根据`ks.cfg`中的预设答案自动完成安装。

![](img/bcff49efd8e6d7a411cb276271242a77_92.png)

![](img/bcff49efd8e6d7a411cb276271242a77_93.png)

![](img/bcff49efd8e6d7a411cb276271242a77_95.png)

![](img/bcff49efd8e6d7a411cb276271242a77_96.png)

![](img/bcff49efd8e6d7a411cb276271242a77_98.png)

![](img/bcff49efd8e6d7a411cb276271242a77_100.png)

![](img/bcff49efd8e6d7a411cb276271242a77_102.png)

![](img/bcff49efd8e6d7a411cb276271242a77_103.png)

![](img/bcff49efd8e6d7a411cb276271242a77_105.png)

### 服务端配置步骤

![](img/bcff49efd8e6d7a411cb276271242a77_106.png)

![](img/bcff49efd8e6d7a411cb276271242a77_108.png)

![](img/bcff49efd8e6d7a411cb276271242a77_109.png)

以下是搭建PXE无人值守安装服务器的步骤：

![](img/bcff49efd8e6d7a411cb276271242a77_111.png)

![](img/bcff49efd8e6d7a411cb276271242a77_113.png)

![](img/bcff49efd8e6d7a411cb276271242a77_114.png)

![](img/bcff49efd8e6d7a411cb276271242a77_116.png)

1.  **配置DHCP服务**：为客户端分配IP并告知引导文件。
    ```bash
    # /etc/dhcp/dhcpd.conf
    subnet 192.168.10.0 netmask 255.255.255.0 {
      range 192.168.10.100 192.168.10.200;
      option subnet-mask 255.255.255.0;
      option routers 192.168.10.1;
      default-lease-time 21600;
      max-lease-time 43200;
      next-server 192.168.10.10; # TFTP服务器地址
      filename "pxelinux.0"; # 引导文件名
    }
    systemctl start dhcpd
    ```
2.  **配置TFTP服务**：提供引导文件。
    ```bash
    yum install -y tftp-server xinetd
    # 编辑 /etc/xinetd.d/tftp，设置 disable = no
    systemctl start xinetd
    ```
3.  **提供PXE引导文件**：
    ```bash
    yum install -y syslinux
    cp /usr/share/syslinux/pxelinux.0 /var/lib/tftpboot/
    # 挂载系统镜像，复制内核和驱动文件
    mount /dev/cdrom /mnt
    cp /mnt/images/pxeboot/{vmlinuz,initrd.img} /var/lib/tftpboot/
    cp /mnt/isolinux/vesamenu.c32 /var/lib/tftpboot/
    ```
4.  **配置引导菜单**：
    ```bash
    mkdir /var/lib/tftpboot/pxelinux.cfg
    cp /mnt/isolinux/isolinux.cfg /var/lib/tftpboot/pxelinux.cfg/default
    # 编辑default文件，指定安装源和kickstart文件路径
    # 例如：
    label linux
      menu label ^Install Red Hat Enterprise Linux 7.0
      kernel vmlinuz
      append initrd=initrd.img ks=http://192.168.10.10/pub/ks.cfg
    ```
5.  **配置HTTP服务**：提供系统安装文件和Kickstart文件。
    ```bash
    yum install -y httpd
    systemctl start httpd
    # 复制整个镜像内容到Web目录
    cp -r /mnt/* /var/www/html/
    # 创建并配置Kickstart文件
    cp ~/anaconda-ks.cfg /var/www/html/pub/ks.cfg
    # 编辑ks.cfg，确保url指向正确，如：
    # url --url="http://192.168.10.10/"
    ```
6.  **关闭防火墙**（测试环境）：
    ```bash
    systemctl stop firewalld
    ```

![](img/bcff49efd8e6d7a411cb276271242a77_118.png)

![](img/bcff49efd8e6d7a411cb276271242a77_120.png)

![](img/bcff49efd8e6d7a411cb276271242a77_121.png)

### 客户端验证

![](img/bcff49efd8e6d7a411cb276271242a77_123.png)

![](img/bcff49efd8e6d7a411cb276271242a77_125.png)

新建一个虚拟机，不挂载任何安装介质（如光盘），将网卡启动设为第一启动项。开机后，客户端将自动从网络获取IP，下载引导文件，并从HTTP服务器拉取安装镜像，最后根据`ks.cfg`自动完成安装，无需任何手动操作。

![](img/bcff49efd8e6d7a411cb276271242a77_127.png)

![](img/bcff49efd8e6d7a411cb276271242a77_129.png)

![](img/bcff49efd8e6d7a411cb276271242a77_131.png)

![](img/bcff49efd8e6d7a411cb276271242a77_133.png)

![](img/bcff49efd8e6d7a411cb276271242a77_135.png)

**总结**：PXE无人值守安装整合了DHCP、TFTP、HTTP等多个服务，实现了系统部署的完全自动化。理解其工作流程和每个服务的作用是关键。通过Kickstart文件预设安装选项，是提升运维效率的强大工具。

![](img/bcff49efd8e6d7a411cb276271242a77_137.png)

![](img/bcff49efd8e6d7a411cb276271242a77_139.png)

![](img/bcff49efd8e6d7a411cb276271242a77_141.png)

![](img/bcff49efd8e6d7a411cb276271242a77_143.png)

![](img/bcff49efd8e6d7a411cb276271242a77_145.png)

---

![](img/bcff49efd8e6d7a411cb276271242a77_147.png)

## 本节课总结 🎉

![](img/bcff49efd8e6d7a411cb276271242a77_149.png)

在本节课中，我们一起学习了三个重要的高级主题：

![](img/bcff49efd8e6d7a411cb276271242a77_151.png)

![](img/bcff49efd8e6d7a411cb276271242a77_153.png)

![](img/bcff49efd8e6d7a411cb276271242a77_155.png)

![](img/bcff49efd8e6d7a411cb276271242a77_156.png)

![](img/bcff49efd8e6d7a411cb276271242a77_158.png)

1.  **OpenLDAP**：理解了目录服务用于集中认证的原理，并重点掌握了客户端如何配置以使用远程LDAP服务器登录。
2.  **MariaDB**：学习了数据库的基本管理，包括安装、用户权限控制、数据的增删改查（CRUD）以及备份恢复操作。
3.  **PXE无人值守安装**：掌握了通过网络自动化批量安装Linux系统的完整架构，涉及DHCP、TFTP、HTTP服务的协同工作，以及Kickstart应答文件的制作。

![](img/bcff49efd8e6d7a411cb276271242a77_159.png)

![](img/bcff49efd8e6d7a411cb276271242a77_161.png)

![](img/bcff49efd8e6d7a411cb276271242a77_162.png)

![](img/bcff49efd8e6d7a411cb276271242a77_164.png)

![](img/bcff49efd8e6d7a411cb276271242a77_165.png)

![](img/bcff49efd8e6d7a411cb276271242a77_166.png)

这些内容将系统管理、数据存储和自动化运维串联起来，极大地提升了作为Linux系统管理员的综合能力。虽然部分内容（如OpenLDAP服务端）难度较高，但了解其架构和客户端使用足以应对大多数工作场景和认证考试。