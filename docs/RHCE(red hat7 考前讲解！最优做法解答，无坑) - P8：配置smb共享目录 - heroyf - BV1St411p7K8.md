# RHCE(red hat7 考前讲解！最优做法解答，无坑) - P8：配置smb共享目录 - heroyf - BV1St411p7K8

这道题的话是配置三马共赏服务。

![](img/08b7de6fb103db26af0d509c35ca93ab_1.png)

![](img/08b7de6fb103db26af0d509c35ca93ab_2.png)

我们在serv端上。

![](img/08b7de6fb103db26af0d509c35ca93ab_4.png)

先回到更加目录。招上三吧。Sber client。

![](img/08b7de6fb103db26af0d509c35ca93ab_6.png)

🤧。

![](img/08b7de6fb103db26af0d509c35ca93ab_8.png)

😀。

![](img/08b7de6fb103db26af0d509c35ca93ab_10.png)

Make a。搞 group。

![](img/08b7de6fb103db26af0d509c35ca93ab_12.png)

じ啊。这样的话都是可以看的。3吧，这个看不了。

![](img/08b7de6fb103db26af0d509c35ca93ab_14.png)

g啲。嗯。这个的说法是设置修改对象的目标安全环境。对用华的名。然后放大。先把。export哦这些的话都可以补全。然后嗯然后这句话的作用是修改SElinux策略内容各项规则的挂值。



![](img/08b7de6fb103db26af0d509c35ca93ab_16.png)

🤧。这话要稍微等一会儿。🤧。🤧。啊。🤧嗯。

![](img/08b7de6fb103db26af0d509c35ca93ab_18.png)

![](img/08b7de6fb103db26af0d509c35ca93ab_19.png)

他已经做好了，那我们就下的开始收传配置文件。

![](img/08b7de6fb103db26af0d509c35ca93ab_21.png)

基本上也就在s0。三宝里面。

![](img/08b7de6fb103db26af0d509c35ca93ab_23.png)

SNB点com。然后第一个是89号。我group的话改成star。设理得嘅。然后是95号。hos only改成他所要求的呃可以访问的域。因为他说example点comexle点com的域是172。25。

0点。同时本机也可以访问，所以前面会有个127点。

![](img/08b7de6fb103db26af0d509c35ca93ab_25.png)

啊，这是把它的注释给去掉。然后在321行，基本上由最后。然后我们加上。Common。

![](img/08b7de6fb103db26af0d509c35ca93ab_27.png)

因为它共享名为common，也就是在别的机子上显示这个文件夹的名字是common。然后把路径给写上group DIR。



![](img/08b7de6fb103db26af0d509c35ca93ab_29.png)

然后proible就是代表可以浏览。BROW。BOWSEABLE。OkayYes。然后的话这样的话就可以了。



![](img/08b7de6fb103db26af0d509c35ca93ab_31.png)

点WK保存。呃，把它加入开机启动。

![](img/08b7de6fb103db26af0d509c35ca93ab_33.png)

STC。SMP。

![](img/08b7de6fb103db26af0d509c35ca93ab_35.png)

然好把两个服务都在一起。力ら？然后他就就启动了。然后他前面说的话是用户bannet必须能够读取共享内容中的服务而，共享中的内容。如果需要的话，验证点是FLECTIEG。



![](img/08b7de6fb103db26af0d509c35ca93ab_37.png)

那么我们这里的话把这个用户给加上BARAAEY。

![](img/08b7de6fb103db26af0d509c35ca93ab_39.png)

SMBPSWD杠ABRNEY。

![](img/08b7de6fb103db26af0d509c35ca93ab_41.png)

FLECTRAAGFLECTRAAG。然后这样就可以了。

![](img/08b7de6fb103db26af0d509c35ca93ab_43.png)

然后看一下1001都可以了。这台飞4要的话，在CSA当中已讲过了设置权限的。2X。呃，在对于目录来说，R代表是可以读写这个目录X，虽然它是可执行的权限，但是代表它可以进入到这个目录当中。

我们设置好它的权限，然后client。

![](img/08b7de6fb103db26af0d509c35ca93ab_45.png)

嗯。一个你有。对司范应用这个。啊有。い。EARNEYSLECPIAD然后的话这里的话基本上就可以了。

![](img/08b7de6fb103db26af0d509c35ca93ab_47.png)

![](img/08b7de6fb103db26af0d509c35ca93ab_48.png)

6。exist最后千万别忘记。在防火墙中加入。对对对，三百伏。不然的话，别的肯定是别的clance上肯定访问不了的，等于。



![](img/08b7de6fb103db26af0d509c35ca93ab_50.png)

3吧牛皮。这里应该是写错了。Service。本。

![](img/08b7de6fb103db26af0d509c35ca93ab_52.png)

![](img/08b7de6fb103db26af0d509c35ca93ab_53.png)

这样的话思维端就配以置完全了。

![](img/08b7de6fb103db26af0d509c35ca93ab_55.png)

🤧。