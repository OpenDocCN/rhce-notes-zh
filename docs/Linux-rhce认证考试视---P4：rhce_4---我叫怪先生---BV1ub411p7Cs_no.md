Linux RHCE认证考试视频教程：P4：Samba服务配置实战

![](img/c24b849d86d65f70ec966314c0fd4014_1.png)

![](img/c24b849d86d65f70ec966314c0fd4014_3.png)

![](img/c24b849d86d65f70ec966314c0fd4014_5.png)

![](img/c24b849d86d65f70ec966314c0fd4014_6.png)

在本节课中，我们将学习RHCE考试中关于Samba服务的两个核心题目：配置一个基本共享和一个支持多用户访问的共享。我们将从安装软件开始，逐步完成目录创建、用户管理、配置文件修改、服务启动以及最终的客户端测试，确保每一步都清晰明了。

![](img/c24b849d86d65f70ec966314c0fd4014_8.png)

![](img/c24b849d86d65f70ec966314c0fd4014_9.png)

---

![](img/c24b849d86d65f70ec966314c0fd4014_11.png)

![](img/c24b849d86d65f70ec966314c0fd4014_13.png)

![](img/c24b849d86d65f70ec966314c0fd4014_15.png)

### **Samba基础：配置`common`共享**

![](img/c24b849d86d65f70ec966314c0fd4014_17.png)

![](img/c24b849d86d65f70ec966314c0fd4014_19.png)

上一节我们介绍了课程概述，本节中我们来看看如何配置一个基础的Samba共享。

![](img/c24b849d86d65f70ec966314c0fd4014_21.png)

首先，需要在服务器上安装Samba服务所需的软件包。Samba服务器需要安装两个核心包。

![](img/c24b849d86d65f70ec966314c0fd4014_23.png)

以下是需要安装的软件包列表：
*   `samba`
*   `samba-common`

![](img/c24b849d86d65f70ec966314c0fd4014_25.png)

安装完成后，需要创建题目要求的共享目录`/common`。

![](img/c24b849d86d65f70ec966314c0fd4014_27.png)

![](img/c24b849d86d65f70ec966314c0fd4014_29.png)

以下是创建和配置目录的步骤：
1.  创建目录：`mkdir /common`
2.  设置SELinux安全上下文：`semanage fcontext -a -t samba_share_t ‘/common(/.*)?’` 然后执行 `restorecon -Rv /common`

![](img/c24b849d86d65f70ec966314c0fd4014_30.png)

![](img/c24b849d86d65f70ec966314c0fd4014_32.png)

接下来，需要创建Samba用户`andy`。Samba用户基于系统用户，因此需要先创建系统用户，再将其添加到Samba用户数据库。

以下是创建Samba用户的命令：
```
useradd -s /sbin/nologin andy
echo “redhat” | smbpasswd -a andy -s
```

![](img/c24b849d86d65f70ec966314c0fd4014_34.png)

![](img/c24b849d86d65f70ec966314c0fd4014_36.png)

现在，开始配置Samba服务。主配置文件是`/etc/samba/smb.conf`。

![](img/c24b849d86d65f70ec966314c0fd4014_38.png)

![](img/c24b849d86d65f70ec966314c0fd4014_40.png)

以下是需要修改的配置项：
*   设置工作组：`workgroup = STAFF`
*   在文件末尾添加共享定义：
    ```
    [common]
            path = /common
            hosts allow = 172.25.0.
            browseable = Yes
    ```

![](img/c24b849d86d65f70ec966314c0fd4014_41.png)

![](img/c24b849d86d65f70ec966314c0fd4014_43.png)

配置完成后，需要启动Samba服务并设置开机自启。

![](img/c24b849d86d65f70ec966314c0fd4014_45.png)

![](img/c24b849d86d65f70ec966314c0fd4014_47.png)

以下是管理服务的命令：
```
systemctl enable --now smb
```

![](img/c24b849d86d65f70ec966314c0fd4014_49.png)

![](img/c24b849d86d65f70ec966314c0fd4014_51.png)

最后，我们可以在客户端进行测试。首先在客户端安装必要的软件包。

![](img/c24b849d86d65f70ec966314c0fd4014_53.png)

![](img/c24b849d86d65f70ec966314c0fd4014_55.png)

以下是客户端需要安装的软件包：
*   `samba-client`
*   `cifs-utils`

![](img/c24b849d86d65f70ec966314c0fd4014_57.png)

![](img/c24b849d86d65f70ec966314c0fd4014_59.png)

同时，需要在服务器端配置防火墙，允许Samba服务。

![](img/c24b849d86d65f70ec966314c0fd4014_61.png)

![](img/c24b849d86d65f70ec966314c0fd4014_63.png)

以下是配置防火墙的命令：
```
firewall-cmd --permanent --add-service=samba
firewall-cmd --reload
```

![](img/c24b849d86d65f70ec966314c0fd4014_65.png)

![](img/c24b849d86d65f70ec966314c0fd4014_67.png)

现在，从客户端使用`smbclient`命令测试连接和读取权限。

![](img/c24b849d86d65f70ec966314c0fd4014_68.png)

![](img/c24b849d86d65f70ec966314c0fd4014_70.png)

![](img/c24b849d86d65f70ec966314c0fd4014_72.png)

以下是测试命令示例：
```
smbclient //172.25.0.11/common -U andy
```
输入密码`redhat`后，应能成功列出共享目录`/common`中的文件，例如之前创建的`1.txt`。

![](img/c24b849d86d65f70ec966314c0fd4014_73.png)

![](img/c24b849d86d65f70ec966314c0fd4014_75.png)

---

![](img/c24b849d86d65f70ec966314c0fd4014_77.png)

![](img/c24b849d86d65f70ec966314c0fd4014_79.png)

### **Samba进阶：配置多用户`devops`共享**

![](img/c24b849d86d65f70ec966314c0fd4014_81.png)

![](img/c24b849d86d65f70ec966314c0fd4014_83.png)

上一节我们完成了基础共享的配置，本节中我们来看看更复杂的多用户Samba共享配置。

![](img/c24b849d86d65f70ec966314c0fd4014_85.png)

首先，创建共享目录`/devops`并设置正确的SELinux上下文。

以下是创建目录的命令：
```
mkdir /devops
semanage fcontext -a -t samba_share_t ‘/devops(/.*)?’
restorecon -Rv /devops
```

接着，编辑Samba配置文件`/etc/samba/smb.conf`，添加新的共享定义。

![](img/c24b849d86d65f70ec966314c0fd4014_87.png)

以下是`[devops]`共享的配置内容：
```
[devops]
        path = /devops
        hosts allow = 172.25.0.
        browseable = Yes
        write list = cai
```
此配置允许`kay`用户读取，`cai`用户拥有读写权限。

![](img/c24b849d86d65f70ec966314c0fd4014_89.png)

然后，创建系统用户`kay`和`cai`，并将他们添加为Samba用户。

以下是创建用户的命令：
```
useradd -s /sbin/nologin kay
useradd -s /sbin/nologin cai
echo “redhat” | smbpasswd -a kay -s
echo “redhat” | smbpasswd -a cai -s
```

![](img/c24b849d86d65f70ec966314c0fd4014_91.png)

![](img/c24b849d86d65f70ec966314c0fd4014_93.png)

配置修改后，重启Samba服务使配置生效。

![](img/c24b849d86d65f70ec966314c0fd4014_95.png)

![](img/c24b849d86d65f70ec966314c0fd4014_97.png)

![](img/c24b849d86d65f70ec966314c0fd4014_99.png)

以下是重启服务的命令：
```
systemctl restart smb
```

现在，配置客户端。题目要求将共享永久挂载到`/mnt/dev`目录，并使用用户`kay`进行身份验证，但允许任何用户通过`cai`的凭证获得写权限。

![](img/c24b849d86d65f70ec966314c0fd4014_101.png)

![](img/c24b849d86d65f70ec966314c0fd4014_102.png)

需要在客户端的`/etc/fstab`文件中添加以下挂载条目：
```
//172.25.0.11/devops /mnt/dev cifs credentials=/root/smb-kay,multiuser,sec=ntlmssp 0 0
```
其中，`/root/smb-kay`文件内容为：
```
username=kay
password=redhat
```

![](img/c24b849d86d65f70ec966314c0fd4014_104.png)

![](img/c24b849d86d65f70ec966314c0fd4014_105.png)

创建凭证文件并设置权限后，执行挂载。

![](img/c24b849d86d65f70ec966314c0fd4014_107.png)

以下是相关命令：
```
echo -e “username=kay\npassword=redhat” > /root/smb-kay
chmod 600 /root/smb-kay
mount -a
```

![](img/c24b849d86d65f70ec966314c0fd4014_109.png)

挂载成功后，进行测试。当前挂载会话使用`kay`的凭证，因此只有读权限。

![](img/c24b849d86d65f70ec966314c0fd4014_111.png)

可以尝试在`/mnt/dev`目录下列出文件进行验证。

![](img/c24b849d86d65f70ec966314c0fd4014_113.png)

若要获得写权限，需要切换到普通用户（非root），并为该会话添加`cai`用户的凭证。

![](img/c24b849d86d65f70ec966314c0fd4014_115.png)

![](img/c24b849d86d65f70ec966314c0fd4014_116.png)

![](img/c24b849d86d65f70ec966314c0fd4014_118.png)

以下是切换用户并添加凭证的命令示例：
```
su - user01
cifscreds add -u cai 172.25.0.11
```
输入密码`redhat`后，该`user01`用户的会话即可在`/mnt/dev`目录下拥有写权限。

![](img/c24b849d86d65f70ec966314c0fd4014_120.png)

可以尝试创建文件进行验证，例如`touch /mnt/dev/2.txt`。文件实际会以`cai`用户的身份写入服务器端的`/devops`目录。

![](img/c24b849d86d65f70ec966314c0fd4014_122.png)

---

![](img/c24b849d86d65f70ec966314c0fd4014_124.png)

本节课中我们一起学习了RHCE考试中Samba服务的核心配置。我们首先配置了一个基础的只读共享`common`，然后完成了一个支持多用户、区分读写权限的高级共享`devops`。关键点包括：安装软件包、创建目录并设置SELinux上下文、管理Samba用户、编写`smb.conf`配置文件、配置防火墙以及从客户端进行挂载和测试。特别是对于多用户共享，我们掌握了使用`multiuser`挂载选项和`cifscreds`命令来动态切换写权限用户的方法。通过这两个练习，你应该能够应对考试中关于Samba服务的各类要求。