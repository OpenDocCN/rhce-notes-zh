# 拿下证书！Redhat红帽 RHCE8.0认证体系课程 RH124+RH134+RH294三门认证视频教程 - P65：65_Video_Day11_Day09Day10回顾02 - 16688888 - BV1734y117vT

![](img/a4e90304be13539835b38aa6cf93b10e_0.png)

因为刚才网络断了，所以的话我看到他的那个突然他的它暂停了。好吧，刚才我们讲的第一部分。ible的安装以及初始化配置啊，所以看到不断的同学进进入我们的直播间。



![](img/a4e90304be13539835b38aa6cf93b10e_2.png)

然后呢，先欢一下大家哈。然后呢，接下来我们讲ansible的临时命令。对不对？来首灵行命令。我们在讲剧本之前。我们在讲剧本之前，是不是提到了一个安伯的一个临时命令啊哦，8月13截止是吧？那好那好。

我可能记错时间了啊。呃，张荣华提醒我了，谢谢啊。然后呢，我们看一下艾伯的临时命令。S5临时命令呢主要是。分为以下几部分组成。第一个anible后面ho pattern。

资产清单里面定义的主基础或属性对吧？然后后面杠M加模块名。然后杠A加上模块对应的一个参数啊，模块对应参数。然后如果我要定义不同的资产清单文件。我们可以通过。杠I来进行，但通常不用啊。

我们配置文件里面也定义了，所以通常不用。然后接下来还有一个密等性密等性，知道什么意思吧？密等性呢也就是我们的。主控受管主我们的控制主机主主机。我们发号失应之后呢。

我们想要我们的目标主机达到一个什么样的状态。就是完成任务之后，我的状态怎么样的？如果状态相同，它就任务不会做第二遍，对吧？直接返回okK，而不是chrench。那如果不是好。

它是通过一些操作达到我们的所期待的一个目标状态。就是这样啊，我们的一个密等性密等性啊，能明白啊。然后接下来就接下来就是我们的一个。as文档参考啊，通常在第一步呢，as of do杠V加模块名。

加加模幻名呢，这个是我推荐的啊，为什么考试里面呢，我们的真正练习里面模拟练习，包括因为它是不对外的啊，就网络是不对外的。所以的话你第二个你的docs点艾框我们平常练习。可以参考。但是我们的考试练习呢。

抱歉，你这个环境通不了外网，对吧？通不了外网，所以的话我们只能。用anible do，但你前提你的英文环境要好啊，你的英文的练习水平还是要好，还还是可以要可读的哈，可读，你才OK。然后呢。

接下来我们讲到一些anible的一个管理模块啊，行用管理模块。我们分为4个文件模块、软件包模块、服务模块以及。我们的系统模块，对吗？好。管理模块文件模块我们讲了几个呢？第一个是copy。

copy可以用两种目的啊，两种两种作用。第一个。字面的我们都知道的就是将文件从我们的一个受我们的控制节点复制到我们收款主题上，这是第一个作用。第二个作用呢。还记得吗？直接在售管主机上生成对应的文件。

把s只要我们把source换成destination，那是是不是加我们的受官，就是直接相当在里面写文件啊，对吧？通常我们生成文件有几种方法，一种是拉一 far，一种就是这种copy。对吧能回忆起来吗？

所以的话它的source跟content它是两种参数，两个参数你可以任选一个。但是destination呢这是man呃 mandatory，也就它是一个。必须的对吧？必要的一个参数，对吧？

所以你要看到destination，它这里前面会有个等号。我们在asible do对吧？张威 copypy。然后我grape一下。DST。看到没有？它这里是一个等号，也就是他这个选项是必备的。啊。

必备的。但S do有个好处呢，万一你忘了怎么写。好，我里面有例子，对吧？它有教你参数，怎么它的参数类型，还有下面还有对你ample。对不对？然后接下来一些可选参数，比如说我可以定义它的文件的权限。

我的所有者，我的SElinux类型是吧？然后是否在我们。生成的时候啊，我们是否在我们在复制文件的时候。生成一个备份。对吧。备看是吧，它会生成一个日期的，就日期为后缀的一个备份。然后生成完之后呢。

我们再进行一个复制。懂我意思吗？好，接着接下来我们讲完一我们讲完我们的toppy模块。我们接下来是一个fire模块。fire模块呢主要是一般用于我们的授管主机上创建目录以及我们的文件。

创建呢我们主要是一个pass，也就是我们受管主机的一个文件路径。然后还有一个参数就是一个state。state这里不是说文件的存在与删除啊，这里不是文件的存在与删除。

这里呢它是一个你创建文件的一个类型啊，比如说如果我是一个directory。那说明是不是申请个目录，如果是touch是文件，但是fire呢你必须有文件，你要存在。懂我意思吧？然后还有link。

我们的链接软链接对吧？然后跟copy相关，跟相反的，我们有一个fatch，对不对？FETCH。fetch呢是用于在从售管主机上，我反向拉文件过来回来到我们的。节点对吧？反正回到我们的节点。

所以呢它这里的话有一个保存路径，我们的destination是吧？然后我们的source，我们拉我们的原路径。theft的是可以直接我们保存对应的文件到我们的目标位置。

而不是说生成我们的送管主机的名字的文件夹，然后里面再一层层它的一个原路径，对吧？linening find用于增加跟修改我们的文件内容啊，它这里是以行为单位作为一个流式处理。

通常我们用的最多的第一个我们的远端文，我们要修改哪个文件？对吧我们的远程文件路径，然后呢。我定位。用正则表达式进行定位，就是哎你哪一行开头的？完进修改。对吧或者是我的关键字到底在哪里？然后呢。

有几种要有几种就是第一个当前位置替换是吧？iner就line这里我直接就写替换的内容。然后我的定位呢，除了用正则表达式之外，我是不是可以用inser after跟in色 before啊？

对吧我们定位的话可以用正的表达式，可以用iner after也可以用iner beforeiner after就是在指定位置的下一行iner before上一行，然后line呢是整行啊，它是修改后内容。

注意它不是在这个指定字符之后，它是整行插入。整行修改啊，然后state我们通常如果不写。默认生效。如果但我要移除这个，我就直接用SS就可以了。而且我不那你这个不用写，我就直接定位完之后，exon就搞定。

呃，schronize文件同步，这个跟我们的arthink目录差不多啊，i think这个命令差不多哈。软件包模块大家掌握的啊，我们练综合练习里面必有的。这两个。y repository对吧？

我们的软件仓库啊，可能在中文练习里面呢会告诉大家限立一个一次性命令，然后通过ensible临时命令来配送我们的软件仓库。通常都会有S软件仓库，我们的那个红贸八的软件仓库，你没有软件仓库。

你售管主机怎么装软件啊？对吧通常软件仓库呢。在还记得我们是有两个的。能告诉我什么？个月。啊，一个镜像。



![](img/a4e90304be13539835b38aa6cf93b10e_4.png)

我们在红帽八在八系的系统里面学上看看看能不能回答我哈。就我们的红帽八里面，我们的软件仓库是有两个的。可以告诉我吗？我们的以前期系统只有一个就直接挂光盘之后它。

你看到packagepackage目录我就完事了，对吧嗯。像岳伟龙说的一个baOS。还有一个呢还有一个S stream啊 spoon stream app啊，不要搞错啊，一个是应用流，一个是基本系统。

它把两个包拆出来了。像现在最新的8。2版本系统的话，已经有8000多个包了。不然的话很肿大，对不对？它可以有些是所于说像我们的啊系统。运行必须的就写在base OS里面。然后像应用流，特别是我们的。

跟软件系统就是说我在单独软件系统跑的一些应用。但应用里面呢，我又可以通过应用流来区分我们的一个版本是吧？像应用流，这是红帽。其实像我想想啊DNF其实很早就有了。但像应用流的话，我们在8。

0之后是有这个新特性的？就是说我可以用来区分我们的我们可以按版本按需来安装我们对应的一个版本。明白我意思吗？比如说我的postscript circlecle，我这里我在应用流里面，我们在8。

0这个镜像是不是有个9。6跟是，我们是不是拿它来做例子。对吧我们要配置两个啊，所以在这里的话，当然有有人会问我说综文练习里面啊，就以后我们综文练习里面。呃，我一条命令，我一定是命令写一条可以吧？可以啊。

没问题，你在一个文件配置两个都可以，或者是我在两个配置文件，我们分别把base OS跟X dream都写上，没问题。但具体你要看你的练习里面的要求，它怎么配的。好。

接下来啊这这个是不是跟我们写的一个report文件很相似啊。跟我们rapple文件很相似，对不对？我们rapple文件。首先，软件仓库的原地址。描述。是否使用？它的保存的rapcor文件名。

是否进行完整性验证？如果这里啊如果。练习里面有要求，需要完整信验证，这里请搞一，然后后面的GPGK完整ZmeR等于我们整个的地址。强做地主。啊，就不是软件仓库地址，这是一个密钥的地址。

密钥地址的后我们在练习环境里面有在我们在测试里面啊，在测试里面就真正的我们的考试是有这块的。好，接下来安装软件包。安装软件包。样模块参数就两个，一个你要安装哪个软件包，你的名字name。是吧。那。

然后接下来啊这里重复了。然后接下来我们的state，对不对？对呀，就name state，对不对？对我删掉哈。这里的话我详细一点啊，么软件包模块name跟state对不对？我这里把它。去掉啊。

sate有包含几种，一一个是安装。一个移除对吧？安装。证对不对？删除卸载。Ecent。左，然后升级lates。对不对？然后呢，组安装记得是name后面我们这个。group对不对？

有有也是是我们的一个组安装，我们的组名前面叫艾。但前面但前提啊我们是一个组的话是要存在的。这个组这个我们软件组是要存在的。好。然后接下来我们讲系统模块。系统模块呢我们常我们讲了好几个，一个是。

user用户模块用户模块呢主要是创建删除用户。是不是跟是不是我们三个命令的。一个综合。user adduser dial和user mode。对吧三个命令综合这一块就是我们的右所模块。

然后里面我可以写注释信息，主要组附加组我们的状态，对吧？是否生成SSH的密钥，用户名校友类型UID。对吧同样group我们的也是同样的啊，床垫操作创建双存储。然后然然后我们的service模块。

控制一个服务的启停。对吧启婷。主要首首先是一个服务名，状他的服务状态。这里就不是present present啊，它是一个比如说started。

restaring就是我们到底是它的stop停止开启动还重启等等这些。然后是否开启启动enable，对不对？好，然后接下来就是我们的防火墙模块。

fireworkD跟我们的fireworkCMD是不是很像？就通过他加通过它里面加，记得很多人。会漏了这有permanent之后是会漏了一个immediate的。

对吧immediaulate是立即生效啊，如果不加这句，只知道加加加这句相当于什么吗？可以他回答我不？对。通常大家都会记得把规则写完，把我们的那个参数写完，然后加上permanent永久。

但是immedia通常都忘了，大家请可以在群上回答我哈。记得吗？隐们也是干嘛的，立即生效，那相当于我们的哪条命令。对，法尔沃CND杠杠reload对不对？是相当于女露的啊。所以这句话务必加上。

immediate啊等于yes或是等于一。对，因为它是一个布尔值。好吧。然后我们也可以同样写附规则，对吧？然后netw模块呢通我们只讲了一个，它前下面什么reboot啊，这些是我网络管理模块是很弱的啊。

因为真的就是说像那个马娟军同学，他是在那个在实验环节在增长环境实验过的，就网络管理模块实在是太差。所以的话我们就没讲这个，我们主要讲如何去下载文件。好吧。好，第二部分内容就是我们第二章内容。

第二部分给大家回顾完毕。如果没有问题，请打2。再欢迎进入课堂的各位同学们啊，各位朋友同学们啊，欢迎你们。呃在抖音渠道呢可能会看不到画面哈，因为看因为因为主要是一个级便未过。

所以的话只能看到自己我自己在讲。好吧，也希望大家能够就是说如果有真的有那个运维，就在做运维的各位朋友的话，可以就是说从里面学到更多，好吧，可能讲的知识未必能懂啊。我们现在是进行一个我们红帽的一个课堂。

然后呢，接下来我们来复习一下剧版。演员包括我们的一个表演者，不我们的一个就说演员好，说主持人好，做事是不是有手上有稿的对吧？或者是有一个剧本，就说明了你们的一个就我们的需要做的想的话，做的神态动作等等。

那我们asible的话发号施令，是不是。同样你也要。有一个东西啊。有一个一些任务对吧？我们就是相当于有个任务有一个脚本，对不对？像我们需要脚本，像我们这里就是一个剧本，对不对？那剧本的话。

那我们需要的话就是。一个亚某语言。YAML要么圆懂吗？要么圆的话，首先三个横杠代表是一个。开始。对吧。三日横杠代表是一个剧本的开始。然后剧本开始之后呢，我们的。剧本呢这里的剧本名次哈。剧本的名字呢。

主要是我们来写注释用的懂吧？就声明哎我们这个剧本到底要干嘛？我们每一行的一个net模块，这里是相当一个列表，懂吗？列表用横杠空格开头，这里很多人都少了个空格啊。记住啊，这里是有空格的哈，对吧？然后呢。

首先我们的剧本的名字，然后接下来我们的售管主机在哪儿，对吧？我这个剧本我要作用在哪一个收管主机上？

![](img/a4e90304be13539835b38aa6cf93b10e_6.png)

然后接下来我的任务。我们之前是不是写了很多剧本啊，我看讲play books目录里面的，比如说HTTPD。🎼哎，不是我不是CDR，我应该是cat。就类似这种剧本，对不对？然后呢。

接下来任务任务有123对吧？1234任务我里面首先任务的名称记住这里有缩进的啊，它是属于任务的一个模块里，我们任务的一个指向里面指向。所以的话它是有。缩进的剧本是缩进，而不是全部也都是左对齐，对吧？

你是应该是这种层层递进那种三角形，那种三角形倒立着三角形就空我们空白出来的地方，是不是将当立着一个三角形这样的一个不规则的一个对吧？然后呢，任务里面哎我这任务是在干嘛的？然后呢。

接下来就是我们的模块用到的哪一个模块对吧？然后模块里面哪些参数，哪些值对吧？用什用哪个值。懂我意思吧？然后我建议啊就是按我这种写法啊，按这种写法的话，任务与任务之间可以用空行隔开。

那这样是不是我们在以后我们再分析再排账。的时候是不是更方便？然后如果你写完任务之后，你要写写第二个剧本的话，就同一个文件里面第二个剧本，你就还是三个撇三个杠，对不对？我们注意几点哈。

第一个缩进用两个空格，就每一层用两个空格可以了，不用不需要用系统tab键。用tab键的话，你会哇非常的。长对吧？也很不好也不好去识别，就这样的话是不是很好？第二段，我们列凡是列表。用它啊。

这里的话其错了啊。应该是这样应该是这样排对啊，对吧？列表。这里我是我是少了啊，列表应该是这样子。不要搞错啊。然后这里我要缩进的。我这个4D格式好像不太严谨。对吧可以啊。然后呢。

通常的值呢就name我们的冒号，记得后面带空格哈，这里关路了。很多人就把空格漏了，好吧。然后接下来我们的as play book，我们的验证语法。我们有个习惯哈。

as of playbook记得在我们写完语法作sign text check。对吧。语但是它只验证语法，不验证你的逻辑。懂我意思吗？你的逻辑对对我不管，但语法它可以验证是否正确。

然后我们先试一下空运行看看什么效果。杠大C。

![](img/a4e90304be13539835b38aa6cf93b10e_8.png)

![](img/a4e90304be13539835b38aa6cf93b10e_9.png)

对吧然后后面再运行具的。

![](img/a4e90304be13539835b38aa6cf93b10e_11.png)

脚本啊，这就是剧本。然后可以用一个V2个V3个V4个V来看我们的详细程度。通常我们带一个B就足够了。S offacebook杠B看一下我们的脚本执行的结果就可以了。在我们综合练习，还有我们的考试软件。

不需要啊，不就就带V没问题的。好，接下来变量事实我们稍微过一下。变量事实呢像变量名称啊，第一个我们讲变量，变量的话命名规则是有要求的。不能数字开头。Yeah。不能与下划线开头。因为下划线的话。

我们在炫要里面是不是下划线可以啊？但但在在那个playbook里面不建一个下划线。字母数次，然后还有下划，下划线是可以在中间的，但是不能用。点。因为这里的话，你点的话是一个，它这里是一个分。

就是变量里面的分格，就上下级的一个分格符。像我们我们这后面讲的iphone fat是不是一样？然后也不能用多了，也不能竖字开头或是空格，空格会识别成两个变量，或是两条命令。好，电量定义的位置才首先。

剧本类定义。剧本的定义可以用va five后面跟文件。然后也可以就group vast，还有ho school。对啊，就group box里面是后面加我们的主机清单里面变量名。

或者是ho fast侧面加变量的主机名，就特定主机生效。这得里面是写。不是等于哈one name冒号空格bo留。然后资产间定义呢，这里就有区别了。这里的主机名。跟变量值是等于的，因为冒号会出错。

我试过了啊，冒号会出错。然后呢，还有一种直接。S5 playbook杠1哈export代表我当前剧本要导入在哪个变量，然后one name等于vol后面跟剧本。明白吧？变量引右。怎么引用？引浩。

两个括号大括号中间你变量名中间记得留空格。能没有啊。

![](img/a4e90304be13539835b38aa6cf93b10e_13.png)

上面的知识能明白的话，请扣个3，我看大家有什么问题没有啊。上面的这部分明白，请扣3。因今天主要是太久没讲了，所以的话还是复习。但是我22号那天那天的课我就不再复习了啊，今天相当把这个过一遍。好。

接下来我们来看一下变量的类型。变量类型有分几种。第一个常规型变量，常规型变量啊，我们的onenet，然后冒号boing跟等于boing，这是我们看场合的。看场合，如果是在清单里面呢。在清单里面。

我们就不许，我们就。等于其他情况越。冒号空格。懂我意思吗？然后呢，除了常规型变量，我们还有数组，对吧？数组。同一类型的变量，我们称为数组。比如说像我们的。username是吧，我们的用户名。

我们的软件安装的包名对吧？都是同一类型的那是不是用数组？那如果里面是不同类型的一个变量，我们用字典对吧？字典是不是很形象的？就是说比如说我的。这个字里面。它有多种用法。然后里面各自什么含义。

还有它的例子，对不对？很很形象吧，我们的新华字典，我们的现代汉语词典是不是这样子？那字典呢在这里，比如说我用户名我的一个用户里面包含了几个好几个不同元素。比如说他的用户名，它的一个需要类型。

它的UID对吧？它的描述是不是他同一个用户里面的不同元素，那就用字典。然后还有一个叫做register变量。register变量什么用呢？将我们的任务的输出结果放到一个变量里面，以便我们。凑续进行引用。

明白我意思吗？这个我们经常后面用到，特别是用到40的时候会经常用。接下来。s of加密我们练习里面会相关有两道题目啊，S of常见语法S of box后面创建对吧？查看这里不能用cat啊。

然后已有的S of已有的加密内容进行编辑，然后还有就是我们的。如何去调用，对吧？他不能直接那个。里面如果还有变量的，你要加密变量的话，你不能直接运行它，你要调用它的，要不就是把。密码写在文件里面。

但这个文件你要做好安全措施。还有一种呢就是。询问what ID d。对吧。关于事实，记得这里get fat等yes，对不对？gether get fast等于Yes。然后呢，调二种事实两种形式。

一种是我所说所说说的an of fact。帽里面的一个建值，对不对？有个变量。或这个值或者是answerable fat。点。对吧那这个点呢，如果你是那种说。本来我们变量像我们的主机完全主命主机名。

如果带有变量的。就带有点的那不适用。懂吧会冲突的。所以的话我们通常如果稳妥的话，用括号括起来。然后404例的话这么多是吧？我会收集主机名。完全确定域名IP地址端口列表。

然后还有磁盘工具大小服务器列表等等这些。然后我们也可以自定事实，这个的话就在fatD里面第一。那我要讲到一个魔法变量，就这几个不是通过anl fat里面。来生成的，而是。通过那个。

就自就他自身已经有的对吧？我们可以获取。新能列表里面的主机、组名、组以及它的inventory host。好，这一块第四章已经复习完。



![](img/a4e90304be13539835b38aa6cf93b10e_15.png)

明白了，扣一个4。

![](img/a4e90304be13539835b38aa6cf93b10e_17.png)

请公4啊。然后呢，我们稍微休息一下，稍后11点10分继续讲第五章内容，好吧。

![](img/a4e90304be13539835b38aa6cf93b10e_19.png)