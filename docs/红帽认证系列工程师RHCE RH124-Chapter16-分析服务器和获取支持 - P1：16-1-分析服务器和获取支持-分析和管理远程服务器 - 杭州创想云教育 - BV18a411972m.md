# 红帽认证系列工程师RHCE RH124-Chapter16-分析服务器和获取支持 - P1：16-1-分析服务器和获取支持-分析和管理远程服务器 - 杭州创想云教育 - BV18a411972m

![](img/499f44cd9243aff55aa149e25540f8ff_0.png)

好我们来看2h u24 的最后一章啊，分析服务器和获取支持呃，这招呢我们带着大家呢，通过一种全新的方式来管理我们的服务器，并且呢通过红帽的门户网站，来获取一些技术支持，还有一个功能呢就是红帽七晚八。

在发布的时候呢，也推出了一个新的啊产品啊，叫做esi，它是一个智能化的工具，可以帮助我们判断服务器，当前所面临的问题，好我们先看这个第一小节，第一小节呢是分析和管理远程服务器啊，那么在我们的红帽切板7。

6的时候呀，那么发布之后，那么增加了一个功能叫cocp，那么q v p的什么意思呢，kv p的呢指的是飞行员的驾驶舱啊，那么飞行员可以对驾舱里面所有的按钮呢，进行的控制，来实现对整个飞机的控制对吧。

非常强大，而这个real 7。6的时候呢，这个q a p的呀只能临时的起作用啊，没有办法像啊手游竞争一样啊，跟随系统而启动，然后呢从real 8之后呀，他就把这个服务呢当做一个默认的了。

也就意味着你做完系统之后，那么这个cp的已经安装在了你的系统里面，你所需要做到的事情呢，就是把它激活就可以了，那么这个站点呢是我们的上游啊，上游，那么我们可以去访问一下，好打开浏览器啊。



![](img/499f44cd9243aff55aa149e25540f8ff_2.png)

我们去输入刚才的站点，那么我们来看一看这个产品啊，这个产品你看它的logo就是一个什么呀，飞行器的logo啊，我们可以在这个cp上啊，完成非常多的任务啊，那么这里有各种各样的截图啊。

但是呢红帽企业版linux里面的这个creepy呀，更新速度可能没有上游的产品呢，没有那么快，所以功能没有这么的丰富啊，没有丰富呃，如果我们用的是这个snos啊，这种系统比如rocky linux。

那么就可以使用最新版本的这个creepy，那么通过kp的呀，我们可以实现一些非常复杂的一些任务，在目前我们的教室里面呀，实验的功能呢还比较单一啊，但是我们可以先体验一番啊，体验一番。

那么要想让我们的服务器啊支持kopp的啊。

![](img/499f44cd9243aff55aa149e25540f8ff_4.png)

可以通过速度一样install coopp来实现，然后呢再把这个cp的要起来，注意了，在启动的时候它的指令啊指令，那么服务的名称呢叫crep的点socket啊，一定要看清楚，不然会失效的。

那么然后呢再添加防火墙规则，而这些动作呢，在我的虚拟机里面都已经做好了，我们来看一下好，我们打开我的sa啊，那么去检查一下rtm杠，q a甘道夫grape kipet啊，已经装过了啊，已经装过了。

这是它的核心啊，核心啊，然后呢，接着呢我去看一下我的这个这个单元呀，是什么状态，他目前状态已经是运行的了，但是呢没有跟随系统启动，现在我让它跟随系统启动，然后呢这个服务呢它的端口呀。



![](img/499f44cd9243aff55aa149e25540f8ff_6.png)

走的是9090，然后呢我们去访问一下啊，在浏览器里面输入我们的soa。live，点3。com，9090。



![](img/499f44cd9243aff55aa149e25540f8ff_8.png)

输入我们的管理员账号，我就用root了。

![](img/499f44cd9243aff55aa149e25540f8ff_10.png)

密码呢是red hat，登录成功之后呢，我们就能看到一个啊看到一个这样的啊，一个信息啊，一个信息，ok哎这里面还有一个非常可爱的提示服啊，欢迎来到什么呀，红帽的培训对吧啊。

welcome to red head training啊，一个training诶，那么在整个页面都包含了大量内容啊，大量内容，那么左侧啊是我们的导航栏啊，导航栏在导航栏里面有什么内容呢。

首先有个预览预览，然后呢是我们的日志啊，日志啊可以根据情况呢去查看啊，不同时间段的日志，以及不同的这个级别的啊，然后呢是网络信息，网络信息呢，可以看到我们整体的流量的使用情况，还有防火墙的啊这个策略。

那么防火墙，我们可以点击右边的这个按钮啊，选择关闭或打开我们的防火墙，非常方便啊，然后呢如果是开机状态呢，还可以干嘛呢，还可以点击这里的fireword呀，去添加一些简单的规则啊，简单规则啊，回过头啊。

回过头我们再来点击我们的网络，那么在网络下方呢，还有一些网络接口的设置，但是我不推荐大家使用这里的方法啊，去做啊，还是总通过nm cli来管理，ok在下面呢是关于网络相关的日志。

然后呢account呢是我们的账户，你看这里是我们之前创建的。

![](img/499f44cd9243aff55aa149e25540f8ff_12.png)

各种各样的账户对吧，也可以通过这里呢创建一些简单的账户。

![](img/499f44cd9243aff55aa149e25540f8ff_14.png)

再往下面的是sources，那么系统当中所有的服务啊，目标啊，套接字，定时器和路径都是能够看到的啊，其中定时器呢还可以去自定义创建，我们服务呢也可以去管理，那这里呢看到有一个什么呀。

感叹号说明我们某个服务啊肯定不正常，那么不正常的的话，它会放在第一个，你看失败了，这个失败是无法解决的，目前因为我们后面会讲到，如何解决这个问题啊，应用程序，那么这个应用呢并非是系统里面的应用。

而是指的是我们cp上面安装的这个，一些插件，ok那么诊断报告。

![](img/499f44cd9243aff55aa149e25540f8ff_16.png)

当我们的服务器出现问题，可以生成诊断报告，啊这个速度很快的啊，那么生成之后呢，我们可以把它提交给红帽的基础支持啊，也当然也可以把它分享给第三方啊，但是呢嗯一定要是受信任的第三方。

虽然收集的时候呢不会收集敏感信息，但是一定还是要小心为上啊，啊马上就收集结束了，收集油之后呢，它会生成一个压缩包，我们可以选择下载，是一个压缩包，看到没，sos report啊，那么sa主题名时间啊。

然后呢是一个xz的压缩包啊，里面没有什么敏感信息的啊，我们可以打开看一下，啊你看这些配置的呀啊命令啊，日志呀等等啊，信息没有敏感的啊，没有敏感的，ok好，那么再往左呢是我们的一个内核的啊。



![](img/499f44cd9243aff55aa149e25540f8ff_18.png)

这个崩溃转储机制啊，默认我们没有开啊，six啊，我们后面再讲到啊，软件的升级，软件升级啊，订阅哎，我们之前已经订阅过了啊，这个sa啊，你看已经啊这个还没有订阅啊。



![](img/499f44cd9243aff55aa149e25540f8ff_20.png)

没有订阅啊，终端我们可以通过啊，页面也可以执行一些命令啊。

![](img/499f44cd9243aff55aa149e25540f8ff_22.png)

然后呢我这边呀再去访问一下wastation，我的word station呢也是执行过的啊，执行过的好，我去访问一下。



![](img/499f44cd9243aff55aa149e25540f8ff_24.png)

然后呢登录一个叫做root，密码是red hat。

![](img/499f44cd9243aff55aa149e25540f8ff_26.png)

这是我们的wastation，那么它是已经订阅过的，已经订阅过了啊，但是你看这里啊已经订阅啊，如果我想在war station呢也去看到sera的情况，那怎么办，那我们需要在我们的word上啊。

去装一个包啊。

![](img/499f44cd9243aff55aa149e25540f8ff_28.png)

装一个什么包呢，哎我们去要么试试一下啊，要么search c o c k p i t，那么在这个里面有一个包啊，叫做kipate gdh bo，啊这里面有个dashboard。

唉dboard可以帮助我们来走一下，e杠y install cookie啊，pet杠大师啊，board，诶做完之后我们看它有没有生效。



![](img/499f44cd9243aff55aa149e25540f8ff_30.png)

好吧，我们这边刷新一下页面。

![](img/499f44cd9243aff55aa149e25540f8ff_32.png)

唉没有内容是吧，那么我们最好还是给它重新启动一下吧。

![](img/499f44cd9243aff55aa149e25540f8ff_34.png)

restart coo c k p i t。socket好。

![](img/499f44cd9243aff55aa149e25540f8ff_36.png)

然后呢我们这边呢重新去刷新一下页面。

![](img/499f44cd9243aff55aa149e25540f8ff_38.png)

登录密码red hat。

![](img/499f44cd9243aff55aa149e25540f8ff_40.png)

那么登录成功之后呢，我们的左侧呢会有一个，这边应该会有个大shboard doub，ok。

![](img/499f44cd9243aff55aa149e25540f8ff_42.png)

那么我们可以选择添加一个新的主机啊，把主机名字写上来，然后用户名就足root好了啊。

![](img/499f44cd9243aff55aa149e25540f8ff_44.png)

颜色你可以区分啊，添加成功，那么这时候呢我们就可以去看玛利亚啊，去不仅观察自己，还可以观察我们的这个嗯server a啊，sa好，这边刷新一下页面啊，看看能不能打印出来，诶，怎么没有显示出来呢。

这个版本更新了啊，我们再来稍微等一下。

![](img/499f44cd9243aff55aa149e25540f8ff_46.png)

嗯我再添加一次试一试。

![](img/499f44cd9243aff55aa149e25540f8ff_48.png)

或者我直接搜看能不能搜出来吧，没有。

![](img/499f44cd9243aff55aa149e25540f8ff_50.png)

那说明刚才没有添加成功，应该是server a，点live点儿，example，点com root。

![](img/499f44cd9243aff55aa149e25540f8ff_52.png)

嗯我们是不是因为有些配置的问题。

![](img/499f44cd9243aff55aa149e25540f8ff_54.png)

我去登一下试试，诶是可以登录的呀，啊是可以登录的。

![](img/499f44cd9243aff55aa149e25540f8ff_56.png)

为什么我们这里没有这个内容呢。

![](img/499f44cd9243aff55aa149e25540f8ff_58.png)

我用ip地址试一下，用户名root。

![](img/499f44cd9243aff55aa149e25540f8ff_60.png)

好体现上来了啊，在这里啊，那么我们直接去点击不同的主机列表，就可以查看不同的主机信息啊，现在是sora的啊，现在是啊，were stationed啊，都可以去查看去管理，非常的方便啊，非常方便啊。



![](img/499f44cd9243aff55aa149e25540f8ff_62.png)