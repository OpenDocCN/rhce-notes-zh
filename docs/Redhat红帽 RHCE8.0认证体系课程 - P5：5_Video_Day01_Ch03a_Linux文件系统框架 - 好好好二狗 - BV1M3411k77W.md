# Redhat红帽 RHCE8.0认证体系课程 - P5：5_Video_Day01_Ch03a_Linux文件系统框架 - 好好好二狗 - BV1M3411k77W

![](img/37214d03e2074c3f97481879ed98221e_0.png)

第三章我们开个头，从命令行管理文件。首先我们要知道linux里面文件以及目录的一个方式。在linux里面呢，它是一个树形结构啊，从树形结构，然后所有文件跟目录都是从根我们的斜杠啊，想要叫根啊。

我们录我们根挂在点开始。然后这些文件呢不是随意创建，而要争取我们的文件系统目录的标准，特定的功能及目录呢，应要按照我们功能存放。我们这里有个链接哈。

CSDN这里然后先今天这里我们有有讲关于一些常用的一些目录目录的文件目录的用途啊，然后我们这里的话有一个我画的一个思维导图啊，根目录到底里面做什么用的。我们简单来看一看。



![](img/37214d03e2074c3f97481879ed98221e_2.png)

![](img/37214d03e2074c3f97481879ed98221e_3.png)

![](img/37214d03e2074c3f97481879ed98221e_4.png)

![](img/37214d03e2074c3f97481879ed98221e_5.png)

![](img/37214d03e2074c3f97481879ed98221e_6.png)

![](img/37214d03e2074c3f97481879ed98221e_7.png)

![](img/37214d03e2074c3f97481879ed98221e_8.png)

![](img/37214d03e2074c3f97481879ed98221e_9.png)

![](img/37214d03e2074c3f97481879ed98221e_10.png)

![](img/37214d03e2074c3f97481879ed98221e_11.png)

放大姐来看一下啊，能看清楚啊，有点小啊。

![](img/37214d03e2074c3f97481879ed98221e_13.png)

我们这里跟挂达点我们重点讲几个目录哈。

![](img/37214d03e2074c3f97481879ed98221e_15.png)

讲解目录首先ETC对吧？

![](img/37214d03e2074c3f97481879ed98221e_17.png)

我们跟关在我我们那个。

![](img/37214d03e2074c3f97481879ed98221e_19.png)

我看一下LL。跟对吧？我们跟挂载点是不是有这么多东西，对不对？这么多目录，我们就讲这些目的用途，好不好？



![](img/37214d03e2074c3f97481879ed98221e_21.png)

![](img/37214d03e2074c3f97481879ed98221e_22.png)

首先我们讲ETCETC通常是跟我我在放大啊，我看能不能再放大。

![](img/37214d03e2074c3f97481879ed98221e_24.png)

不能这样这样能不能看得清楚，可以吗？EDC。

![](img/37214d03e2074c3f97481879ed98221e_26.png)

常常是那个放置我们的配置文件啊。

![](img/37214d03e2074c3f97481879ed98221e_28.png)

所有的配置文件，也就是它的应用我们的系统，我们的网卡设备等配置文件都是放在ETC下面。

![](img/37214d03e2074c3f97481879ed98221e_30.png)

![](img/37214d03e2074c3f97481879ed98221e_31.png)

比如说我们的y仓库，我们的软件仓库配置文件，y要么点reple选D是吧？reheadrapple，那我们网卡的配置文件都是放在这里。



![](img/37214d03e2074c3f97481879ed98221e_33.png)

![](img/37214d03e2074c3f97481879ed98221e_34.png)

知道吗？这是ETCmod的作用。然后还有一个bo，boot主要是放内核的引导文件，以及我们的引导程序有关的，还有内核文件，以及它的尾根尾根知道什么意思吗？就是它的镜像。



![](img/37214d03e2074c3f97481879ed98221e_36.png)

![](img/37214d03e2074c3f97481879ed98221e_37.png)

![](img/37214d03e2074c3f97481879ed98221e_38.png)

它系统你要加载你你你要加载镜像，它就是一加载镜像之后，它是一个伪的根目录。那后我们要切到真正的根目录，对吧？我们才能进行操作。但是它这个切换呢，我们引导的时候，系统已经帮我们做了。

但是我们如果在修修改我们救援模式，这些都没有啊，就它我们手动切换到我们的真正根目录，就是然后还有就是我们的引导程序有关的一个文件都放在这儿。



![](img/37214d03e2074c3f97481879ed98221e_40.png)

![](img/37214d03e2074c3f97481879ed98221e_41.png)

![](img/37214d03e2074c3f97481879ed98221e_42.png)

![](img/37214d03e2074c3f97481879ed98221e_43.png)

![](img/37214d03e2074c3f97481879ed98221e_44.png)

![](img/37214d03e2074c3f97481879ed98221e_45.png)

啊，这是部署目录，你们记只要记得，只要跟引导有关的，全部放在这里。

![](img/37214d03e2074c3f97481879ed98221e_47.png)

能明白吧？第3个，DV。

![](img/37214d03e2074c3f97481879ed98221e_49.png)

它是deviceDEVIC的一个简写device它是与适硬件设备有关的文件，尤其是跟磁盘有关的文件全部放到这儿啊。比如说我像我们的分区磁盘文件名叫做DEV0NMBM10N1，对不对？

也就是我们MBME磁盘第0个。

![](img/37214d03e2074c3f97481879ed98221e_51.png)

![](img/37214d03e2074c3f97481879ed98221e_52.png)

![](img/37214d03e2074c3f97481879ed98221e_53.png)

![](img/37214d03e2074c3f97481879ed98221e_54.png)

![](img/37214d03e2074c3f97481879ed98221e_55.png)

MVM1磁盘里面的1个第一个设备啊，MVME0N1然后我后面分区呢，后面带一个P啊，这个命名方法的话，我们在后面讲快讲设备的时候，我们会讲到它是怎么命名的啊，我们就记得我们磁盘跟硬件有关的。

全部放在第一批目录。

![](img/37214d03e2074c3f97481879ed98221e_57.png)

![](img/37214d03e2074c3f97481879ed98221e_58.png)

![](img/37214d03e2074c3f97481879ed98221e_59.png)

![](img/37214d03e2074c3f97481879ed98221e_60.png)

root它是超级用户，我们root的加目录，也就是它的工作目录里面比如说像私有变量文件是吧？就它的一些环境变量，什么叫环境变量？就是我们在企在切到这个用户的时候，我们预加载的一些命令。

或者是一些刚才说的一些路径等等是吧？环境变量应该都知道啊，就预加载一些路径，包括我们我们一些需要执行的一些命令，或者是一些命的别名等等，就放在这个私有变量里面。



![](img/37214d03e2074c3f97481879ed98221e_62.png)

![](img/37214d03e2074c3f97481879ed98221e_63.png)

![](img/37214d03e2074c3f97481879ed98221e_64.png)

![](img/37214d03e2074c3f97481879ed98221e_65.png)

![](img/37214d03e2074c3f97481879ed98221e_66.png)

![](img/37214d03e2074c3f97481879ed98221e_67.png)

明白吗？比如说我自定一个命令别名，对吧？这是不是属于一个环境变量，对吗？就只能在你这个用户上面才能使用。



![](img/37214d03e2074c3f97481879ed98221e_69.png)

![](img/37214d03e2074c3f97481879ed98221e_70.png)

![](img/37214d03e2074c3f97481879ed98221e_71.png)

这个是一个私有变量文件啊，我们就放在这儿放在加目录里面。然后还有就是你切换用用户在root的时候，它工作目录会回到该用户的加目录下，是也就是为它是为了读取我们这个用户的环境变量文件，懂我意思吗？

你切换的时候切换切换用户的时候，他会读取我们的变量文件，然后再进驻到我们的用户的我们的。

![](img/37214d03e2074c3f97481879ed98221e_73.png)

![](img/37214d03e2074c3f97481879ed98221e_74.png)

![](img/37214d03e2074c3f97481879ed98221e_75.png)

![](img/37214d03e2074c3f97481879ed98221e_76.png)

![](img/37214d03e2074c3f97481879ed98221e_77.png)

命令好，社会环境懂吗？

![](img/37214d03e2074c3f97481879ed98221e_79.png)

然后他还他这个权限呢，他是他权限比较特殊啊，通常说他就是那个。

![](img/37214d03e2074c3f97481879ed98221e_81.png)

他用户的加入的权限就只有他用户以及同组的能读跟执行。然后其他你不能访问，为什么你这个用户，你只你只能自己访，你自己同组的能能能能进，但你其他人你能进吗？对吧？比如说你的你的家门对吧？你的家门。

你的那个办公桌，你抽屉，那只有你或者是跟你同组相关人才浏览，那其他人能能不能进来？

![](img/37214d03e2074c3f97481879ed98221e_83.png)

![](img/37214d03e2074c3f97481879ed98221e_84.png)

![](img/37214d03e2074c3f97481879ed98221e_85.png)

![](img/37214d03e2074c3f97481879ed98221e_86.png)

![](img/37214d03e2074c3f97481879ed98221e_87.png)

![](img/37214d03e2074c3f97481879ed98221e_88.png)

是不是一样道理？用过这目录你不是随所所有人随便都访问的，懂我意思吗？只有你跟你同组的人，就你的文，他前面一位是文件所有者，后后面是你同组的人，同组的那个用户，懂吗？然后后面三后面三个三个呢是代表其他人。



![](img/37214d03e2074c3f97481879ed98221e_90.png)

![](img/37214d03e2074c3f97481879ed98221e_91.png)

![](img/37214d03e2074c3f97481879ed98221e_92.png)

![](img/37214d03e2074c3f97481879ed98221e_93.png)

所以他用户这目录不是其你其他人是进不来的对吧？一定要你自己或者是他同组用户，这个能理解吗？

![](img/37214d03e2074c3f97481879ed98221e_95.png)

![](img/37214d03e2074c3f97481879ed98221e_96.png)

然后然后还有就是比如说像例子的话，比如说我们创建一个像oracle用户，对吧？我们比如说我们装数据库，我们要创建一个oracle用户，然后oracle用户呢，他就会放到他就会有自己的。



![](img/37214d03e2074c3f97481879ed98221e_98.png)

![](img/37214d03e2074c3f97481879ed98221e_99.png)

![](img/37214d03e2074c3f97481879ed98221e_100.png)

就我们讲到这个重用例子的话，我们先讲一下home啊，因为它这个作用例子是是跟下面有关系的。home的话是普通用户的加目录所在的目录，懂吗？root是在直接是斜杠我们的根目录root，对不对？杠root。

那普通用户呢是在home下面的一个用户名，刚才说过了。

![](img/37214d03e2074c3f97481879ed98221e_102.png)

![](img/37214d03e2074c3f97481879ed98221e_103.png)

![](img/37214d03e2074c3f97481879ed98221e_104.png)

![](img/37214d03e2074c3f97481879ed98221e_105.png)

![](img/37214d03e2074c3f97481879ed98221e_106.png)

它也是同样包含的环境变量文件。然后如果我们切换的话，会切换到它的项目录像，对吧？所以我们的波浪线是代表我们的用户这目录，就是我们。



![](img/37214d03e2074c3f97481879ed98221e_108.png)

![](img/37214d03e2074c3f97481879ed98221e_109.png)

比如说我们这里CD是吧？

![](img/37214d03e2074c3f97481879ed98221e_111.png)

杠杠路ot是吧，这波浪线对不对？我现在都工作目录了，这懂吗？像我们一个例子，就是我们创建一个用户oracle，对吧？



![](img/37214d03e2074c3f97481879ed98221e_113.png)

![](img/37214d03e2074c3f97481879ed98221e_114.png)

有所爱的。

![](img/37214d03e2074c3f97481879ed98221e_116.png)

对吧我们让我们切换到我们的oracle的这这用户。哎，我我打错了，ORAACLE。

![](img/37214d03e2074c3f97481879ed98221e_118.png)

![](img/37214d03e2074c3f97481879ed98221e_119.png)

是吧我同我们当前PWD是不是在这个加目下面？

![](img/37214d03e2074c3f97481879ed98221e_121.png)

懂吧，然后呢，比如说我们安创建用户，然后我可以设置。

![](img/37214d03e2074c3f97481879ed98221e_123.png)

![](img/37214d03e2074c3f97481879ed98221e_124.png)

特殊的环境变量。比如说我们的刚才的文件执行路径，我们的pass变量，对不对？也就是说我这个用户能执行的程序，那其他用户我不需要给他执行。那我们是不是要可以给特定用户去。



![](img/37214d03e2074c3f97481879ed98221e_126.png)

![](img/37214d03e2074c3f97481879ed98221e_127.png)

![](img/37214d03e2074c3f97481879ed98221e_128.png)

测置我们的环境变量啊，对不对？就例如我们刚才的pass。

![](img/37214d03e2074c3f97481879ed98221e_130.png)

对吧刚才的执行文件的一个路查找路径，是不是我们可以给除了我们给自己设置，我们也可以给单独的用户设置，对不对？



![](img/37214d03e2074c3f97481879ed98221e_132.png)

![](img/37214d03e2074c3f97481879ed98221e_133.png)

也可以全局，他既然全能全局也可以局部，懂我意思吗？

![](img/37214d03e2074c3f97481879ed98221e_135.png)

然后呢，还有就特殊行境面料，比如说像我们的bsh profile，就我们的一一些加预加载这些文件，对吧？这两个文件大家稍微记一下就好了，我们以后会用到啊。



![](img/37214d03e2074c3f97481879ed98221e_137.png)

![](img/37214d03e2074c3f97481879ed98221e_138.png)

然后呢，ho我们讲完了，我们讲VAR。

![](img/37214d03e2074c3f97481879ed98221e_140.png)

VR呢它是大部分日志有关的文件都是在这个目录下。比如说像通常是VRlog，对吧？我们VR log目录是存放日志用的啊，然后自定义如果是除非我们某个服务自定的日志路径，否则基本上都在这个这个目录下。

比如说我们像邮件有关的在R mail里面的这个目录存放了邮件相关的日志啊，邮件呢邮件不仅仅是我们普通的这种收件箱，还包括我们用户之间对吧？用户之间我们内系统内部产生的一些私有邮件，然后它都会存到这里。

还有呢跟计划有任务有关的，比如说像我们的一次性计划任务，还有我们的周期性计划任务都会存在这里面懂我意思吗？这VR通常是存放我们的比如说像日志啊。



![](img/37214d03e2074c3f97481879ed98221e_142.png)

![](img/37214d03e2074c3f97481879ed98221e_143.png)

![](img/37214d03e2074c3f97481879ed98221e_144.png)

![](img/37214d03e2074c3f97481879ed98221e_145.png)

![](img/37214d03e2074c3f97481879ed98221e_146.png)

![](img/37214d03e2074c3f97481879ed98221e_147.png)

![](img/37214d03e2074c3f97481879ed98221e_148.png)

![](img/37214d03e2074c3f97481879ed98221e_149.png)

![](img/37214d03e2074c3f97481879ed98221e_150.png)

![](img/37214d03e2074c3f97481879ed98221e_151.png)

![](img/37214d03e2074c3f97481879ed98221e_152.png)

![](img/37214d03e2074c3f97481879ed98221e_153.png)

等等，主要是纯日制对吧？还有纯本的一些运行库。

![](img/37214d03e2074c3f97481879ed98221e_155.png)

TMPt临时文件夹啊，就是说我们大量软件安装的时候，我们会有大量的临时文件。它通常说就是那个它会存放在这里，就是它会产生一些临时文件啊，就是类似于我们C盘windows里面t。



![](img/37214d03e2074c3f97481879ed98221e_157.png)

![](img/37214d03e2074c3f97481879ed98221e_158.png)

![](img/37214d03e2074c3f97481879ed98221e_159.png)

![](img/37214d03e2074c3f97481879ed98221e_160.png)

对不对？临时的他一般弄完之后说会删除，但这个tamp空间你要足够大，否则的话你可能软件，比如说我要零时解压出来一些数据，对不对？你的tamp不够大，它会安装失败的。



![](img/37214d03e2074c3f97481879ed98221e_162.png)

![](img/37214d03e2074c3f97481879ed98221e_163.png)

现在prolock是吧，pro目录，它这两个是内存的啊，它是不占磁盘空间，它是启动后的内存数据，只是把它映射出来。它这两个挂它这两个目录，它它是映射到它是内存映射下来的，所以它不占用任何的磁盘空间。



![](img/37214d03e2074c3f97481879ed98221e_165.png)

![](img/37214d03e2074c3f97481879ed98221e_166.png)

![](img/37214d03e2074c3f97481879ed98221e_167.png)

![](img/37214d03e2074c3f97481879ed98221e_168.png)

![](img/37214d03e2074c3f97481879ed98221e_169.png)

懂吗？不正常任的磁盘供间，它是从系统启动之后，哪些数据呢？比如说像我们跟。

![](img/37214d03e2074c3f97481879ed98221e_171.png)

![](img/37214d03e2074c3f97481879ed98221e_172.png)

硬件有关的CPU信息是吧？内存有关信息。还有呢就是我们的进程有关的。它它进程这的话，它目录保存的是一个数字，对不对？进程ID号。



![](img/37214d03e2074c3f97481879ed98221e_174.png)

![](img/37214d03e2074c3f97481879ed98221e_175.png)

![](img/37214d03e2074c3f97481879ed98221e_176.png)

好，然后如果ss它有一个ss目录，它就对针对pro做一个整理。

![](img/37214d03e2074c3f97481879ed98221e_178.png)

这两个目录其实内内它的内容差不多的，只不过它ses是优化后的。然后还有呢还有几个木录啊这边。

![](img/37214d03e2074c3f97481879ed98221e_180.png)

USR。

![](img/37214d03e2074c3f97481879ed98221e_182.png)

这要是跟用户环境变量有关的一些数据啊，跟用户有关的，我们不我们这里不叫user啊，这里全称不叫user，它是一个将在用户环境变量用户变量里面的数据。像我们一般装的软件啊，保存的一些东西啊。

会报保存USR里面，包括我们的可执行文件，对吧？多在这里面OKT呢就是第三方软件，我们会编译到这里。

![](img/37214d03e2074c3f97481879ed98221e_184.png)

![](img/37214d03e2074c3f97481879ed98221e_185.png)

![](img/37214d03e2074c3f97481879ed98221e_186.png)

![](img/37214d03e2074c3f97481879ed98221e_187.png)

第三方软件啊OPT对吧？习惯性的就约定俗成的。还有一个叫做lifeveLID这与我们运行库有关的内容。



![](img/37214d03e2074c3f97481879ed98221e_189.png)

![](img/37214d03e2074c3f97481879ed98221e_190.png)

![](img/37214d03e2074c3f97481879ed98221e_191.png)

那整个能理解吗？我记我把整个目架给我介绍了一遍，都是从根木开始的。

![](img/37214d03e2074c3f97481879ed98221e_193.png)

![](img/37214d03e2074c3f97481879ed98221e_194.png)

能理解吗？可以的话打个一。

![](img/37214d03e2074c3f97481879ed98221e_196.png)

哎，我又回来了。

![](img/37214d03e2074c3f97481879ed98221e_198.png)

![](img/37214d03e2074c3f97481879ed98221e_199.png)

![](img/37214d03e2074c3f97481879ed98221e_200.png)

能理解啊，这个这个的话我们已经讲了文件系统结构的用法，就文整个架构都清楚了啊。还有是就这这些啊，就不可跟跟文件分跟目录分开的目录就与开析过程有有关的。就这几个EDCB是吧？

还有DVB这几个目录是不能是不能单独建分区的。我们刚才是自动的话，我们就全部建在根分区下面。那如果我们以后要区分的话，那是不是我们不能单独建分区这这些一定要跟我们的系统跟根分区是一块的。

就跟我们的系统一块的。比如说像你的USR我们的VIR我我们的那个USUSRVIR这些是不是我们可以单还后目录是不是我们可以单独建分区去存放，对吧？单独画直盘空间，但这几五个目录是不可以的。

它一定要跟根分区在一块啊，所以我们补充这点就是我们这5个目录不能单独的啊，不能单独的单独的存放在一个位置。那好，我们今天先到这里，然后我现在把笔例传上来视频我们统一明天晚上。啊，结束之后我会传上来。

好吧？那我现在我先把笔记传上，然后大家可以稍作整理。然后如果啊明天我们我们早上9点半继续啊，明天早上9点半，然后呢，如果现场的同学啊，现场的同学请麻烦请把垃圾带一下啊，把它带一下。

然后那个把东西把现场这些放好。然后明天我们9点半还是现场我们14号课室啊，资料格式，我们先把我们笔记啊，我们把笔记更新一下，我重新传1个1801的1个笔记取代取代这个文件。然后今晚的话大家先把它复习。

然后我们看书本上第三章内容。第三项内容好吧。还有第四章，明天会讲到一个帮助VI编辑器，用户跟组啊，可能会讲到权限，看时间啊。今天先把基本啊基本东西给大家过了，我现在先把笔记再次传上来。

网盘稍后也会有好吧，那我们。今天先到这里，5点16分，我们明天各位不见不散，好吧，9点半。