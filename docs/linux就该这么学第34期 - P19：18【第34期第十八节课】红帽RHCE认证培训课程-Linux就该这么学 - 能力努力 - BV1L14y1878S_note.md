# Linux就该这么学：第34期：第19课：文件传输与共享服务配置教程 🚀

![](img/4d11d20a234e35576c78f33dda0a38ac_1.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_3.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_4.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_5.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_7.png)

在本节课中，我们将学习如何配置和管理Linux系统中的文件传输服务（VSFTPD）以及文件共享服务（Samba）。课程内容包括VSFTPD的三种验证模式（匿名公开模式、本地用户模式、虚拟用户模式）的配置，以及Samba服务的跨平台文件共享实现。通过本课的学习，您将掌握在Linux系统中高效、安全地传输和共享文件的方法。

![](img/4d11d20a234e35576c78f33dda0a38ac_9.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_11.png)

---

![](img/4d11d20a234e35576c78f33dda0a38ac_13.png)

## 概述 📋

![](img/4d11d20a234e35576c78f33dda0a38ac_15.png)

本节课主要分为两部分：第一部分介绍VSFTPD文件传输服务的配置，包括三种验证模式的详细步骤；第二部分讲解Samba文件共享服务的配置，实现Linux与Windows之间的跨平台文件共享。课程内容注重实践操作，适合初学者学习。

![](img/4d11d20a234e35576c78f33dda0a38ac_17.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_18.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_20.png)

---

![](img/4d11d20a234e35576c78f33dda0a38ac_22.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_23.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_24.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_26.png)

## 第十章节收尾：Apache访问控制实验 🔧

![](img/4d11d20a234e35576c78f33dda0a38ac_28.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_29.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_31.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_32.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_33.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_34.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_35.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_36.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_37.png)

上一节我们介绍了Apache网站服务的基本配置，本节中我们来看看如何通过Apache实现访问控制，限制只有特定浏览器（如Firefox）可以访问网站。

![](img/4d11d20a234e35576c78f33dda0a38ac_39.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_41.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_42.png)

以下是配置步骤：

1. **编辑Apache主配置文件**  
   找到Apache的主配置文件（通常位于`/etc/httpd/conf/httpd.conf`），在目标目录的配置段中添加访问控制规则。

![](img/4d11d20a234e35576c78f33dda0a38ac_44.png)

2. **设置访问控制条件**  
   使用`SetEnvIf`指令根据用户浏览器的User-Agent标识进行条件判断。例如，只允许Firefox浏览器访问：
   ```apache
   SetEnvIf User-Agent "Firefox" ff
   Order Allow,Deny
   Allow from env=ff
   Deny from all
   ```

![](img/4d11d20a234e35576c78f33dda0a38ac_46.png)

3. **创建测试页面并重启服务**  
   在网站目录中创建测试页面，然后重启Apache服务使配置生效：
   ```bash
   systemctl restart httpd
   systemctl enable httpd
   ```

![](img/4d11d20a234e35576c78f33dda0a38ac_48.png)

4. **测试访问控制效果**  
   使用Firefox浏览器访问网站，应能正常显示页面；使用其他浏览器（如Chrome）访问，则会被拒绝。

![](img/4d11d20a234e35576c78f33dda0a38ac_49.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_50.png)

---

![](img/4d11d20a234e35576c78f33dda0a38ac_52.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_54.png)

## 第十一章节：VSFTPD文件传输服务配置 📁

![](img/4d11d20a234e35576c78f33dda0a38ac_56.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_58.png)

上一节我们完成了Apache访问控制实验，本节中我们来看看VSFTPD文件传输服务的配置。VSFTPD支持三种验证模式：匿名公开模式、本地用户模式和虚拟用户模式。

![](img/4d11d20a234e35576c78f33dda0a38ac_60.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_62.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_64.png)

### 匿名公开模式配置

匿名公开模式允许用户无需密码即可访问FTP服务，适合公开文件下载。

![](img/4d11d20a234e35576c78f33dda0a38ac_66.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_68.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_70.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_71.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_72.png)

以下是配置步骤：

![](img/4d11d20a234e35576c78f33dda0a38ac_74.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_75.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_77.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_78.png)

1. **安装VSFTPD服务**  
   使用包管理器安装VSFTPD：
   ```bash
   dnf install vsftpd
   ```

![](img/4d11d20a234e35576c78f33dda0a38ac_80.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_81.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_82.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_83.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_84.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_86.png)

2. **编辑配置文件**  
   打开VSFTPD的主配置文件`/etc/vsftpd/vsftpd.conf`，修改以下参数：
   ```bash
   anonymous_enable=YES
   anon_upload_enable=YES
   anon_mkdir_write_enable=YES
   anon_other_write_enable=YES
   ```

![](img/4d11d20a234e35576c78f33dda0a38ac_88.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_89.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_90.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_92.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_94.png)

3. **设置目录权限**  
   为匿名用户访问的目录设置写入权限：
   ```bash
   chmod -R 777 /var/ftp/pub
   ```

![](img/4d11d20a234e35576c78f33dda0a38ac_95.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_97.png)

4. **重启服务并测试**  
   重启VSFTPD服务，并使用FTP客户端匿名登录测试：
   ```bash
   systemctl restart vsftpd
   systemctl enable vsftpd
   ```

![](img/4d11d20a234e35576c78f33dda0a38ac_99.png)

### 本地用户模式配置

本地用户模式允许系统用户通过账号密码登录FTP服务。

![](img/4d11d20a234e35576c78f33dda0a38ac_101.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_103.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_105.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_106.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_107.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_108.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_110.png)

以下是配置步骤：

1. **编辑配置文件**  
   在`/etc/vsftpd/vsftpd.conf`中启用本地用户登录：
   ```bash
   local_enable=YES
   write_enable=YES
   ```

![](img/4d11d20a234e35576c78f33dda0a38ac_112.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_113.png)

2. **管理用户黑名单**  
   编辑黑名单文件`/etc/vsftpd/user_list`和`/etc/vsftpd/ftpusers`，移除需要允许登录的用户（如root）。

![](img/4d11d20a234e35576c78f33dda0a38ac_115.png)

3. **重启服务并测试**  
   重启VSFTPD服务，使用本地用户账号登录测试。

![](img/4d11d20a234e35576c78f33dda0a38ac_117.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_118.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_119.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_121.png)

### 虚拟用户模式配置

![](img/4d11d20a234e35576c78f33dda0a38ac_123.png)

虚拟用户模式通过独立的用户数据库进行验证，安全性更高。

![](img/4d11d20a234e35576c78f33dda0a38ac_125.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_127.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_129.png)

以下是配置步骤：

![](img/4d11d20a234e35576c78f33dda0a38ac_131.png)

1. **创建虚拟用户数据库**  
   创建用户列表文件（如`/etc/vsftpd/vuser.list`），格式为奇数行用户名、偶数行密码。然后使用`db_load`命令生成数据库文件：
   ```bash
   db_load -T -t hash -f /etc/vsftpd/vuser.list /etc/vsftpd/vuser.db
   ```

![](img/4d11d20a234e35576c78f33dda0a38ac_133.png)

2. **创建映射用户**  
   创建一个本地用户作为虚拟用户的映射用户：
   ```bash
   useradd -d /home/virtual -s /sbin/nologin virtual
   ```

![](img/4d11d20a234e35576c78f33dda0a38ac_135.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_137.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_138.png)

3. **配置PAM模块**  
   创建PAM配置文件`/etc/pam.d/vsftpd.vu`，指定虚拟用户数据库：
   ```bash
   auth required pam_userdb.so db=/etc/vsftpd/vuser
   account required pam_userdb.so db=/etc/vsftpd/vuser
   ```

![](img/4d11d20a234e35576c78f33dda0a38ac_140.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_141.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_142.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_144.png)

4. **编辑主配置文件**  
   在`/etc/vsftpd/vsftpd.conf`中启用虚拟用户模式并指定PAM文件：
   ```bash
   pam_service_name=vsftpd.vu
   guest_enable=YES
   guest_username=virtual
   user_config_dir=/etc/vsftpd/vuser_conf
   ```

![](img/4d11d20a234e35576c78f33dda0a38ac_146.png)

5. **为虚拟用户单独配置权限**  
   在`/etc/vsftpd/vuser_conf`目录中为每个虚拟用户创建配置文件，设置个性化权限。

![](img/4d11d20a234e35576c78f33dda0a38ac_148.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_150.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_151.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_152.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_153.png)

6. **重启服务并测试**  
   重启VSFTPD服务，使用虚拟用户账号登录测试。

![](img/4d11d20a234e35576c78f33dda0a38ac_155.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_156.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_158.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_159.png)

---

![](img/4d11d20a234e35576c78f33dda0a38ac_161.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_163.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_165.png)

## 简单文件传输协议（TFTP）配置 ⚡

![](img/4d11d20a234e35576c78f33dda0a38ac_167.png)

上一节我们介绍了VSFTPD的三种验证模式，本节中我们来看看更简单的文件传输协议TFTP。TFTP基于UDP协议，无需验证即可传输文件，适合轻量级应用。

![](img/4d11d20a234e35576c78f33dda0a38ac_169.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_170.png)

以下是配置步骤：

1. **安装TFTP服务**  
   安装TFTP服务器和客户端工具：
   ```bash
   dnf install tftp-server tftp
   ```

![](img/4d11d20a234e35576c78f33dda0a38ac_172.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_173.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_174.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_176.png)

2. **编辑配置文件**  
   编辑TFTP配置文件`/etc/xinetd.d/tftp`，将`disable`参数改为`no`以启用服务。

![](img/4d11d20a234e35576c78f33dda0a38ac_178.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_179.png)

3. **启动服务并测试**  
   重启xinetd服务，使用TFTP客户端下载文件测试：
   ```bash
   systemctl restart xinetd
   tftp 192.168.10.10
   get filename
   ```

![](img/4d11d20a234e35576c78f33dda0a38ac_181.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_182.png)

---

![](img/4d11d20a234e35576c78f33dda0a38ac_184.png)

## 第十二章节：Samba文件共享服务配置 🤝

![](img/4d11d20a234e35576c78f33dda0a38ac_186.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_188.png)

上一节我们介绍了文件传输服务，本节中我们来看看Samba文件共享服务的配置。Samba支持Linux与Windows之间的跨平台文件共享。

![](img/4d11d20a234e35576c78f33dda0a38ac_190.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_191.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_192.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_194.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_196.png)

以下是配置步骤：

![](img/4d11d20a234e35576c78f33dda0a38ac_198.png)

1. **安装Samba服务**  
   安装Samba服务器和客户端工具：
   ```bash
   dnf install samba samba-client
   ```

![](img/4d11d20a234e35576c78f33dda0a38ac_200.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_201.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_203.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_204.png)

2. **编辑配置文件**  
   打开Samba主配置文件`/etc/samba/smb.conf`，简化配置并添加共享目录：
   ```bash
   [linux]
   comment = Linux Shared Directory
   path = /home/linux
   public = no
   writable = yes
   ```

![](img/4d11d20a234e35576c78f33dda0a38ac_206.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_207.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_209.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_211.png)

3. **创建Samba用户**  
   将系统用户添加到Samba数据库：
   ```bash
   smbpasswd -a linux
   ```

![](img/4d11d20a234e35576c78f33dda0a38ac_213.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_214.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_215.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_216.png)

4. **设置SELinux权限**  
   启用Samba相关的SELinux布尔值：
   ```bash
   setsebool -P samba_enable_home_dirs on
   ```

![](img/4d11d20a234e35576c78f33dda0a38ac_218.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_220.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_222.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_224.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_225.png)

5. **重启服务并测试**  
   重启Samba服务，在Windows或Linux客户端上访问共享目录测试。

![](img/4d11d20a234e35576c78f33dda0a38ac_227.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_229.png)

---

![](img/4d11d20a234e35576c78f33dda0a38ac_231.png)

![](img/4d11d20a234e35576c78f33dda0a38ac_232.png)

## 总结 📚

本节课中我们一起学习了VSFTPD文件传输服务的三种验证模式（匿名公开模式、本地用户模式、虚拟用户模式）的配置方法，以及TFTP简单文件传输协议和Samba跨平台文件共享服务的配置。通过实际操作，您可以掌握在Linux系统中实现高效、安全的文件传输与共享的技术。

---

![](img/4d11d20a234e35576c78f33dda0a38ac_234.png)

**注意**：本教程内容基于实际操作编写，适合初学者学习。在实际生产环境中，请根据具体需求调整配置参数，并确保服务的安全性。