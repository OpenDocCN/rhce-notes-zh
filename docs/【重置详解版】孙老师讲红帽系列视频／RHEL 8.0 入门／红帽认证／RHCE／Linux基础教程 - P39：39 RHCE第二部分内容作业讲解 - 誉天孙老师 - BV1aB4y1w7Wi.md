# 【重置详解版】孙老师讲红帽系列视频／RHEL 8.0 入门／红帽认证／RHCE／Linux基础教程 - P39：39 RHCE第二部分内容作业讲解 - 誉天孙老师 - BV1aB4y1w7Wi

我们先把上周的作业给大家看一下啊。

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_1.png)

后来教的同学我没来得及改，所以。嗯。看一下自己哪个地方有问题啊啊，包括之前改过的同学也要看一下啊，因为。



![](img/6b7d4911cf4b1ade9a2b6b93355972a9_3.png)

呃，有一些。我没有指出来，因为这个可能是题目理解，看我是怎么去理解这个题目的。包括如果你跟我理解有偏差，对吧？你是怎么做的，做的正不正确啊。



![](img/6b7d4911cf4b1ade9a2b6b93355972a9_5.png)

好。嗯，第一个创建一个目录。这个da塔是吧。哦，这个没什么问题啊。第二个创建用户，三个用户，三个用户要求user一的加呃加目录在data目录上。该用户的描述信息为test user。呃。

us2的UID是2000是吧？us3的是这个he去登录的啊。

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_7.png)

那第一个呢其实最大的问题就是这个shar对不这个个加木录对吧？加木路说是在贝塔目录下面，其实这个我也没没说清楚啊。



![](img/6b7d4911cf4b1ade9a2b6b93355972a9_9.png)

呃，比如说你创建你一般情况下会做第一题，创建一个目录data，对吧？然后这个目录已经存在了。我看一下啊。我把这个以前的东西清一下。好。嗯，Uer一的加目落在data这个下面。

那当然也有同学直接这样去指的对吧？呃，如果你直接杠D的话。呃，这个就会有一定的问题。就相当于挖了个坑在这里是吧？T the user。有仔衣。就这个地方啊，如果你指了的话。

那么data就会成为user一的加目录。当然这位同学还是在这个data下面指个user一，对吧？这个user一呢事先是不需要创建的。它会自动帮你去创建这个user一目录。那创建出来之后呢。

这个目录的权限。就是700，而且拥有人用组都是U则1。那换作是这个这样这种方法去做的话，就会导致我们的用户的加目录在data上。那data是我刚刚创建的对吧？刚刚创建。那创建好之后。

这个目录它是拥有人用组都是root，而且权限是755。所以优子一对这个目录没有写权限啊，才会导致最后就会报错了。比如说你这样去创建创建好之后，那你创建的时候就会报错。首先加目录已经存在了。

所以杠低指定加目录的时候，加目录可以不用存在啊，而且是不需要存在。对吧然后什么时候报了一些错。那你再切到U子级的话，到后面。嗯。虽然进来了，对吧？没什么问题，但是。嗯。

你看有一些这个我们说加木下面会有一些这个这个什么这一些文件，但是是没有的对吧？而且你对你加盟应该是没有写权限的。好，这个是这道题啊，ts used的话这个我就不说了。然后USD指定的话杠U。



![](img/6b7d4911cf4b1ade9a2b6b93355972a9_11.png)

然后us the add杠SSB no log in对吧？

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_13.png)

有的さ。A11钢筋。那755对吧？

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_15.png)

好。这个杠D指的啊再说一遍啊，这个杠D指的是pass word里面的啊，它只会去修改这个password里面那个呃加木录那一栏那一栏。好，第三题，创建组GID为3000。Group a。

将这三个组加入到ITIT组里面。啊，这个怎么加进去看你啊，这个这个你没问题。然后要求IT组内的所有成员都可以在IT目录下面创建文件和删除文件。那就需要对这个目录要有写权限了，对吧？啊。

目前为止我们学到的只能是什么组，我要是这个目录的组，并且。组需要对他有权限，所以你要首先第一步要设置它的组为ID组。然后把再把这个组的这个这个权限加上，对吧？RWX啊。这样的话。

这个组内所有的成员都可以对这个目录有写权限了。好，这个下面有进行测试。Uer3登不进去，因为它是非登录需要。是道吧？非登录 share。啊，不是说非登录 share啊。

它是这个SB no log in这个 sharere是吧？这个需要是没有办法登录的。呃，那既然没办法登录，为什么要创建这个用户？将来我们有很多的用户都是无法登录的啊，就是它仅仅是无法登录这个操系统呃。

但是呢他可以去用这个系统上的资源，对吧？这个user3啊。好，然后第六题给IT组更名为cloud组group mode，刚刚这个没什么问题啊。大家在更名的时候。

比如说老师我也可以去到group当中去把这个栏位改成cloud，对吧？但是这种方法不太好，因为你这样改的话，可能会有一些其他地方没有改。比如说我们加入的组组名有没有有没有可能没有改，对吧？

所以最好的方法就是用命行去改。group mode这样的话，把所有跟这个IT组相关的全部会改成cloud组。这个地方要注意啊。好。

新建组用户ID user一ID user2将呃ID user一的加入移动到这个data下面，ID users是吧？这个的话嗯。说移动到加目路移动到这个I users是吧？

这个应该写成改成IT user对吧？好，这个移动的话嗯，首先啊你的加入要变成什么date ID users是吧？ID user一的加木勒变成它吧。然后为什么要说移动呢？是因为。

这个下面还有一些什么加目下面还有一些文件。所以你在做这一题的时候，要注意在这块加杠MD啊，我可以直接指D直接指D的话。🤧嗯嗯。这地方如果你直接指D的话，那么就会将password里面那个改成它，对吧？

啊，改成它之后呢，你再切到这个IDU字一子的时候，又会报错了。是这样吧。因为有一些变量没有改过来，变量在那个什么一些有一些隐藏文件，所以你要加个M啊，加个MM就会改成它之后。

然后又会把加盟下面的文件移到什么，移到这个。新的这个账目下面啊。

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_17.png)

嗯。嗯，我。看一下啊。啊，比如说我们创建了一个用户use add。IT user IT。us则一是吧。好，然后这个时候呢，它需要把这个user mode修改一下项目杠。如果你直接杠D的话。

会怎么样data下面的。I T users。然后IT userE是吧？这样也可以改，就改的话呢，它只会变成了这个。



![](img/6b7d4911cf4b1ade9a2b6b93355972a9_19.png)

这个里面是吧，这嗯。这地方是改了对吧？然后你切到这个。ITU则一。No。他就会报错了，不能切过去。还在这里对吧？他想进入到这个部分下面，但切不过去，因为你还要复制，还要杠MD啊，要把一些这个。

移过去这样移过去啊。好，你再来一遍user dial。杠R。I T users。好，然后再来啊加一个M的时候。嗯哦，优先优先创建是吧。IT user一。然后再来。这应该没问题吧。ITU。No。

这个它没问题的啊，所以大家要注意，因为这个下面会有一些这样的文件，你要加一个M把它移过来，你手动移或者是怎么移都可以啊。



![](img/6b7d4911cf4b1ade9a2b6b93355972a9_21.png)

好，第九题，这个没什么问题，改用人权嗯，用人用组权限。嗯。

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_23.png)

第十题哦，还有一点啊呃说到第十题，给cloud组设置临时的登录口令。这个临时的登录口令呢。它是给用户啊，给组设置密码，对吧？给主设置密码。我们上课讲到一个命令叫G passwordsword。

G passwordword后面加一个组名。嗯，有上课讲过了啊，但是还是有同学犯这样的错误，他是这样改的，use the mode或者是use the a。



![](img/6b7d4911cf4b1ade9a2b6b93355972a9_25.png)

这样杠P是吧，这样去指定了。当时是不是说过这个杠P指定的这个用户名，对吧？指定的这个密码是无法登录的啊，所以这个不是登录密码。是加密过后制的密加密过后的密码啊，所以大家不要去这样去指啊。

给用户修改密码还是pas呃pasword给主修改密码叫Gpasword。

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_27.png)

非常注意啊。好，第十一题，在time保幕下创建这个文件夹，并并创建时指定文件夹权限为764。创建的时候我们可以加杠P呃，同时指令文件夹权限是764。杠M是你看你慢你慢这个MKDR啊。

它里面会有个选项叫杠M，它可以去怎么样？它可以指定这个用户的权限啊，这个这个目录的权限764。所以你可以一步搞定啊，当然你分开也可以。



![](img/6b7d4911cf4b1ade9a2b6b93355972a9_29.png)

好，第二呢呃第十二题创建这个文件夹，要求递归文件的用人为user一U组为user2。好，那我这个地方这个地方其实呃有个坑是吧？嗯，首先啊。创建这个目录的时候。

我们最终其实创建的目录是tenology这个目录，对吧？那这个呢是什么？是它的一个副呃副目录，其实前面都是它的路径。所以在你给它修改，比如说你再给它修改这个。好，这样我把它创建出来啊。



![](img/6b7d4911cf4b1ade9a2b6b93355972a9_31.png)

好，你杠加一个杠P把它创建出来了，对吧？那你以后再去引用这个目录的时候，比如说你引用这个目录。然后给他把拥有人改成user一。好，因为组我们改觉addmin好吧。啊，那如果你这样去改的话。

那么它改的目录是最后一层目录，注意啊，是最后一个technology这个目录的拥有人和拥有组。那。你要递归的话。是不是往它往下递归呀，那它下面递轨对吧？但是刚好这一题呢。

是因为这个目录下面它没有文件和目录。所以有同学就疑惑了，就是哎呀是不是把它有同学写到这儿。比如说这个同学他就写到什么，他就写到centize，对吧？



![](img/6b7d4911cf4b1ade9a2b6b93355972a9_33.png)

那如果你写到sto递归的话，那么ss拥有权限就会变成了us子拥有人和拥组变成us子user2。那同时technology也会变。因为它是从stos这一层开始递归的，当是这一层的目录权限也会改。

所以大家要注意这个地方啊，你引用的是这个目录。那么它改的是最后一级目录。一定要注意啊，那如果你写修改是它往下递归，那么这个目录跟这个目录都会改。



![](img/6b7d4911cf4b1ade9a2b6b93355972a9_35.png)

好，这个是大家注意的地方啊，递归。

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_37.png)

好，第十三题。所以这道题其实我指定的是这个啊，是这个目录的，往下递归，你直接在这个地方加上这个目录就可以了。然后杠2虽然目录下面没有文件，也没有目录，那没关系，不管它啊。



![](img/6b7d4911cf4b1ade9a2b6b93355972a9_39.png)

啊，十三题group一组指定ID为2100，创建这个文件夹，要求权限为要求设置tab group一的文件夹，用有组为group一是吧？这个只是设置一个组，这个没什么好说的啊。



![](img/6b7d4911cf4b1ade9a2b6b93355972a9_41.png)

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_42.png)

好，然后这个十4题U子的加木创建这个好，这个加木是之前的嗯改了是吧？没关系啊，这个你。呃，你自己改一下题目，或者是自己做一下也可以啊。好，要求指定你看指定文件夹权限为递归指定，对吧？

所以你看我指的是homeusU这个就没问题啊，600。好，因为有可能user一的这个目录，最后一级目录下面是有文件和目录的，所以递归的话就往这一层目录往下递归啊。



![](img/6b7d4911cf4b1ade9a2b6b93355972a9_44.png)

第十五题，在tamp文件夹上创建这个。cap demo我记讲，并且复制它的权限是吧？

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_46.png)

好，这个上课没有讲，呃，有同学是直接把这个什么创建这个文件夹啊，它是把这个文件夹复制成demo这个文件夹，对吧？你复制它的权限，我其实不管你怎么去把权限复制过来，但是你不要复制文件夹吧。😊，对不对？

你不要把它文件夹把这个user与文件夹复制给demo，这不行啊。啊，这其实里面有一个选项叫杠杠reference的选项，你可以去man这个呃change mode里面啊。

它会有一个更高reference的选项。当然你说我要是我自己改成600，行不行，也可以啊。好，它是参考这个权限，然后把权限复制给demo，就这个选项叫杠杠reference。啊，有同学已经找到了是吧？

就参考它的选，参考它的权限给demo。啊，第十六题。将password按着UID的大小进行排序，降序要降序啊，结果保存在这个。root password点 baker这个文件里面是吧？好。

st我们来看一下啊，我们说要按照UID的大小进行排序。那么UID在第几列，我们说是在第123第三列，对吧？第在第三列第三列要按呃第三列，那你要指定分割符啊，就是杠T冒号指定分割符杠K3第三列123。

对吧？好，然后这个R呢是指降序，因为默认是升序啊。默认是从小到大，对吧？好，那你就要加杠R，还有一个是N，这个N这个选项呢。呃，指的是我可以把它当做这个数值的大小进行排序。嗯，否则就当自符串牌了。

能理解吧？否则就当字符串排了啊。所以有可能如果当字符串排100有可能还小于2。就这样子啊，它是按照第一个字符上偶是一，第二个字符上是2，这样排是吧？所以你要按照字符算的二应该在什么？二在100的前面啊。

所以要当数值排的话，2在100的前面啊，所以加个杠N啊，这两个选项进行合并了，对吧？这个因为没关系，这个两个它没有接参数。但是这两个选项不能合并，因为杠T后面有个参数，杠K后面有个参数。

当然这个参数是可以这样空格可空开的。有同学说老师不空开好像也可以对吧？是的，就是你这个地方你空开也行，不空开也行，这个是可以，因为它可以识别，这是个短选项，后面加一个后面这个短选项的这个字母的。

后面就是它的选，就是它的参数啊，所以这个地方可以不仅不用加空格。啊，但是这两个的话。这两个合并的话，它是两个选项啊，那这是两个选项，OK吧。选项进行合并。



![](img/6b7d4911cf4b1ade9a2b6b93355972a9_48.png)

所以不要弄混了啊。嗯。啊，这个我就不演示这个上这是我上课做过的这个同学做的很好，你知道吗？你把这个。那个做这个这个这个是OK的啊。



![](img/6b7d4911cf4b1ade9a2b6b93355972a9_50.png)

好，然后第十七题定一个别名copy，要求当所有用户执行copy的时候执行的是CP杠2这个是吧？好，后面呢这个时间我是标了个备注啊，要求是备份当天的时间，对吧？那么这个时间呢，就是今天备份我写是19号。

那我明天备份可能20号，那今天25号了，对吧？所以你今天备份就25号，所以这个时间上课我也讲过，它应该是动态的，它是一个应该会有一个什么来生成一个时间，生成到那些时间，对吧？好。

这个地方用data呢用data这个地方呢，后面这个是我们学过的吧。这是之前我们第一周的时候就学过这个时间怎么去改。改成这个格式。好，还有的同学把data这个地方做成了一个变量。

他是这样做的啊他是这样做的。呃，或者是data这样做了个变量是吧？然后用do了data去引用这个变量。呃，然后还把这个变量给。呃，变成环境变量了，也就是说它export了。嗯，export的这样。

我看有同学这样做啊，但是我觉得这样不好，嗯，不太好，虽然也能实现。但是呢你就绕了几大圈啊，其实没必要完全这个命令它就可以让我们怎么样呃让我们去实现啊，因为你定义成变量，在引用变量。

引用变量有可能会有一些风险，也不是就是有可能会引用不到啊，或者所以在后面你们尽量像这个变量也是我们自己定义的对吧？嗯，有同学可能之前学过，所以他也做成功了啊，应该是有两个同学，两个同学都都这样做的。

就定义了个变量。呃，其实这题没必要去定义变量啊，虽然也能实现。好，然后这个地方就这样做啊这个。爱丽丝。copy这是别名，等于后面是你要执行的这个内容。啊，执行copy的时候执行后面这个对吧？😡。

这个没有什么疑问了吧。有作业有疑问，大家可以提出来啊。嗯嗯嗯。

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_52.png)

哦，第十八题的同学说也不会做是吧？😡，第十八题上课也讲过了。第十八题啊，查找ATTC下所有文件内容包含pass字符串的文件。



![](img/6b7d4911cf4b1ade9a2b6b93355972a9_54.png)

呃，并且显示这个字符上在哪个文件的哪一行。上课我是不是讲过gragra会有一个什么gra会有一个选项叫杠R的选项。对不对？他可以去根据。根据一个字符串去查找这个字符串在文件的哪个在哪个文件里面。

比如说我是我这道题是查找什么pass这个字符串，对吧？好，后面你可以接一个什么，可以接一个目录，它的意思是查找ETC目录下面。所有的文件当中，哪个文件是带有pass的？如果哪个文件带有pass。

它就会打印出来。也就是说这么多文件都是带有pass的，看到没有？

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_56.png)

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_57.png)

那这个文件的这个地方是带有pass的是吧？啊，有同学想各种办法，有fin的，反正反正范的反正也可以实现，是吧？其实就一条命令杠2。就可以搞定了。我，不会做啊，这个上上课没听，而且你没有看笔记，其实。



![](img/6b7d4911cf4b1ade9a2b6b93355972a9_59.png)

大家我那个PPT上面已经写的很详细了，你只要上课听过了呃，并且上课稍微做了一下笔记。然后下课你根本没有从没必要从头到尾把视频看一遍，只需要把我的PPT拿出来，把你的笔记拿出来。

然后就可以做就可以把所有的题目都做了。其实有个别做不出来也很正常。嗯，然后再不行，你百度一下是吧，最后实现了不也行吗？对吧？只要你能有本事做出来就行啊。啊呃。

OK这个就是grave杠R啊gra杠R然后我要显示在哪一行，所以加了个杠N。

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_61.png)

那加杠RN是吧，然后pass。好，这个就是十八题啊，十八题。

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_63.png)

嗯。

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_65.png)

好，下面我们看一下正则表示这个地方啊，很多同学都没有做。嗯，这个下次不搞这么难了啊，要不然同学直接放弃了是吧？我看好多学同学直接放弃了，做不出来，搞不出来。🤧嗯。好，呃。

其实你把能做的你就做上去就可以了。我看一下他做的情况。嗯，就是没做的，你可以再下去再去看一下，或者上课到时候再听一下。好，我们先看一下这位同学做的啊，呃，他基本上是每一道题都做了的，看到吗？

而且实现了这些答案，我没有给他们，真的没有给他们啊，就是没有给你们任何人。

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_67.png)

但是你看。他全部都完基本上都完成了，而且不止他一位同学呃，前面叫同学基本上都完成了，而且完成的非常好。你看。



![](img/6b7d4911cf4b1ade9a2b6b93355972a9_69.png)

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_70.png)

然后呢基本上按照我的要求都完成了。

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_72.png)

应哎这种是吧，每一道题都做了。

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_74.png)

呃，且不说他跟我的题目对吧？可能有的时候会有一点差距，但是也没关系，但是它基本上是吧，你只要做了，你基本上每一个这个这个什么这个呃。



![](img/6b7d4911cf4b1ade9a2b6b93355972a9_76.png)

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_77.png)

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_78.png)

这个正则表式里面哪个怎么用？其实你只要做完这这些题的话，你平时的graSED的功能基本上都能完成了，绝大多数都是可以实现的了啊。但关键你就不做嘛，嗯就没时间嘛。



![](img/6b7d4911cf4b1ade9a2b6b93355972a9_80.png)

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_81.png)

哦，你今你这周没时间，我相信你以后永远都没有时间。

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_83.png)

那都实现了，看到没有？

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_85.png)

所以做的非常好啊。而且上课基本上我都讲过了，但是你不做没办法啊。

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_87.png)

好嗯，那来看一下啊。

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_89.png)

我给大家总结了这个答案啊，呃你可以按照我做，当然你也可以按照你说老师我的方法比你更好也可以。因为我的也不是一定说是最好的，你看一下我怎么做的，对吧？别人怎么做的啊，待会下课的时候。

我把这个雷浩的同学把这个发给大家，大家可以看一下他怎么做的。好，第一道题显示ETCpa文件里bu结尾的行。这个以什么什么结尾，我们说就多了符号，以什么时么开头用间号，对吧？这个没什么好说的啊。

然后第二个是找出pasword当中，文件有三位或者4位的数。就是password里面有一个数值是三位或者4位的，是吧？哦，首先你要准备一个数值啊，那一个数字是不是就是0到9，这个是中括要括起来。

0到9代表一个数字。啊，这个数字出现了多少次呢？三次或4次就小括号括起来，3逗号4。嗯，就可以实现了。那3逗号4，而且为什么用egra？因为这个括号后面这个这个括号是扩展扩展正则，所以用的是egra。

如果你用gra的话。那你就要加一个什么，加一个杠大E这样子啊，加一个杠大EOK吧。好。

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_91.png)

你可以去查一下嘛。我到时候这个也发给大家，你可以再去。测一下。No。

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_93.png)

对吧三位或者4位的数字都找出来了啊。啊，第三题找出ETCg up呃，to点CFG文件中，至少以至少一个空白字符开头。也就是说呃它要有空白字符开头，并且后面又跟了空半空呃非空白字符。为什么加这个呢？

因为空行不算呢？对吧我们不算空行啊。好，那至少一个空白字符开头至少一个嘛，那一个的话是用什么用什么呀？这个是空白字符，对吧？😡，看到没有？这个是这个间号在中括号的外面代表是以什么什么开头。

然后这个是什么？这个是这个space，这是一个是一个这个这个空白字符啊，那这么空白字符出现了什么？1到多次，因为加号代表是1到多次啊，然后后面呢这个指的是什么？后面又有一个非非空白。

因为中呃间号在中括号的里面，所以它指的是除了什么什么以外，那就是说除了空白以外的字符。OK吧。好，第四题我没有给大家出啊，这个我就算了。第五题这个同学说老师没有是吧？没关系啊。

你只要把这个正子表式写出来就可以了。因为有时候装磁盘的时候，我们选的不一样啊。啊，中括括起来的表这个一个字符对吧？S或H，然后后面是A到Z啊，A到Z。这个比较简单吧。然后找出这个啊。

这个有同学说老师没做出来是吧？嗯，好，它是找出ALDUSR并cat这个命令中结果命令结果中文件的路径是吧？好，我给大家看一下我做的啊。最后要实现什么样的效果？



![](img/6b7d4911cf4b1ade9a2b6b93355972a9_95.png)

好，你可以看一下啊，我们先。现在是不是实现了这个命令执行的结果是这个对吧？其实我要找出的是这个路径和这个路径看到吗？那这个路径跟这个路径啊啊，那么这两个路径怎么找到呢？

我们可以用匹配group e group杠O啊，E group的话，我们可以这样啊。呃，如果是一柜不匹配的话，我们不加杠窝的话，看一下。他就会把后面这个你看这个是斜杠嘛，点是不是任意字符啊。

然后星是指它前面这个出现了任意次嘛，其实这中间就是。任意个字符，任意多个随意的任何一个字符，那么以斜杠开头对吧？斜杠开头，那么后面匹配到了。好，后来加个杠窝杠窝指的是什么？杠窝指的是它只匹配。哎。

匹配到了这个地方就显示，其他都不显示了，这些就不显示，只显示这个就是杠窝的意思。好，然后路径的话，我们可以用什么用空格呀，这种分隔符，然后把那最后一个是不是路径，那后面这个不路径嘛，不是路径吧，对吧？

所以把它给过滤掉，O吧，就这样子。所以这个是这道题啊，就把它给过滤掉了啊。

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_97.png)

好，路径截取出来了。啊，第七题，pro memory inform文件当中要求以大小写S开头的行，至少有三种方式实现啊，这个有同学也做出来了啊，以什么呃，这个忽略大小写，然后这个是一个以什么什么开头。

对吧？这个是一个做一个整体，然后S或者是小S或者大S这三种方式都可以啊。啊，这是扩展政则里面的是吧？啊，第七题，这个是什么或什么或什么，这个就扩展正则嘛。用竖杠隔开，这个没什么说了啊。

看PPT如果还不知道的话啊。第九题，输出一个绝对路径，要求用egroup，这个也不用说了吧，这个就是典型斜杠，就是直接把ETC截取出来。那呃里面包含一个斜杠，还包含一个斜杠，这个两个斜杠中间什么？

中间有任意个任意一个字符。然后这个杠窝指的是支匹配这个。啊，把这个提取出来就可以了，只直接显示ETC那我说提取出ETC对吧？我说例如。它的话就提取出ETCOK吧。好，然后取出ifconIPV4地址啊。

这个呢呃有同学做了。

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_99.png)

因老师这个IPV4进去是吧？呃，IPV4地址的话。我们知道IPV4地址是在这个地方。是样吧，是不是在这个叫annet是吧？哦，那那你结的时候，那你就要结什么？and net但innet这个地方又有什么。

又有这个。呃，有个6是吧，这是IV6地址啊。所以你要加一个。杠W就可以实现吧。你看杠W是不是就可以实现呀？啊，然后你这个就随意了，这个应该会了吧，这个就可以多种方式去实现了。比如说AWK啊。

或者用我们学过的，我说上课讲过了一个cut啊，或者讲一个TR是吧？首先因为有多个空格，所以我们可以把这个多个空格变成一个空格。那这样的话，我将来就可以怎么样就可以去以空格为分格符啊。呃。

以空格为分割服务。呃，D是吧。然后是第几列啊？哦，那就是第三列。

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_101.png)

对吧那这样是不是就出来了？啊，或者用AWK来实现也可以啊。有同学用AWKAWK呢就是第几列啊，就是第一列第二列对吧？AWK这样的。



![](img/6b7d4911cf4b1ade9a2b6b93355972a9_103.png)

呃，AWK然后。嗯。嗯，print是吧。do了2。好，这样啊就是第二列，不是你多了二，就是第二列。你你想打，比如说打一第一列，那就多了一。多了二多了一降，而且可以改变它的任意顺序，看到没有？啊。

然后它默认是以空格为分隔符的啊，会打印出来这样。

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_105.png)

啊呃，这个是grave部分的SED的话，前面其实有很多都我都不说了啊，这都讲过了，上课期也讲过了。然后第三行第三行的第十行。是吧。然后这个是匹配一个正则表达式，这个正则表式里面可以是作串嘛。

也可以是正则表式来表示，对吧？然后删除test呃，15行以及后面所有的行，15加15到doll了是15到最后一行是吧？所以dollar符号就是最后一行D就是删除的意思。啊。

然后这个teacher插替换就比较简单了吧。S将原来的这个root替换成TOOR并打印出替换的行是吧？然后P打印。好，第七题是将这个替换成这个，那这个也没什么，这个也很简单。因为这个里面有个斜杠。

所以你可以把原来这个替查找替换的这个斜杠换成。艾特符号或者换成任何一个字符都可以。比如井号啊。感叹号啊对吧？都可以啊。那你不想换的话，那你就把这个给加一个反斜杠，前面这样子。去掉它的特殊含义。

加个反斜杠啊。好，然后第八题。呃，5到10行所有数字匹配数字嘛，那就是这个数字是不是就这样匹配啊？1到多个吧，对吧？数字，那你数字至少要存在一个吧，所以就是加嘛。然后这政则这是扩展政则。

所以要加杠R杠R看到没有？5到第十行S就是替换，还要替换数字替换成空就删掉了。然后即是这一行的每一个查找到的这个匹配到的词啊。都替换。好，然后P打印。好，删除删除这个地方啊。

他说删除test这个所有特殊字符是吧？其实除了我们正常的数字大小写以外，其实其他的基本上都是特殊字符。所以你就把这些都排除掉就行了。对不对？0到9A到Z大A到点大Z。然后加是1到多个字符，然后替换成空。

这个就啊加正这是扩展政则是吧？因为加号嘛，加号是指扩展政策里面的，所以加个杠R。哦，然后这个是在20行前面。加入AA是吧。啊，哦，这个是题目的问题啊，这个有同学是在20行的行首加入AA哦，这个是哦。

我这样写的话，就是在20行的上面一行加上AA，因为这个是指。插入是吧，插入，然后A的话是追加，就在下面一行。查呃加入是吧？然后这个是嗯。这是在上面一行插入啊。好，这个上课也讲过了吧。

这第一列都加上一个井号是吧？这个代表是最开头啊。啊，都换成井号啊。然后这个也讲过了上课。是不是上上课也讲过了，对吧？将test每行的第一个字符删除嘛？第一这点就代表第一个点代表一个字符，对吧？任意字符。

啊，将这个任意字符替换成空没了，对吧？就就就删掉了。

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_107.png)

🤧好，第十三题我讲一下啊。呃，第十三题。

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_109.png)

我来看一下啊。现在还是要求password的所有单词进行统计测频，并输出格式为一行每一个单词，一行一行一个单词是吧？好，并且要求是这种格式root2这样。啊，首先我们是要进行排序，对吧？呃，那排序的呃。

不不进统计持频。同计词频的话，我里面有很多很多特殊字符。比如说我们呃像这个封号解封号。封号它是B个风格符啊，然后前面后面都可以是一个组呃词品，只是一个单词，对吧？而且这个反斜杠是不是也是反斜杠前面。

后面是不是也可以是一个次屏，所以你可以将这个反斜杠将来去掉，或者是以它为风割，把它替换成这个换行符。呃，然后这个这个冒号也要替换成换行符啊，而且数字的话，我数字你排不排这个都行，反正你把数字也可以去掉。

就只排那个单词嘛，字符串对吧？呃，数字去掉的话，去掉也好去，就刚好我就把它去掉了。啊，那你就可以去掉SED吗？🤧嗯嗯。杠R，然后查找替换S，然后中括号。0到9。然后是加号对吧？然后出现然后替换成空。

然后记这样子。啊，这是把数字的去掉了。数字去掉之后，你还要把什么？你还要把空呃这个这个符号，比如说这个。这是一个冒号是吧，把它替换成换行符，换行符是反斜杠N啊，这个是换行符。这个是换行符的意思啊。好。

然后再来你还有一个斜杠吧。呃，斜杠是吧，斜杠那就是呃这样的斜杠啊。呃，这样的斜杠，然后也替换成换行符。呃。对吧然后这种就是空行了啊，空行了就不管它了。啊，然后你再进行一个排序。Thought。

那排序因为排序的话，它会将什么相同的数排在一起。然后排完之后。你就可以怎么样？我们有个统计视频的叫unicor。杠C是吧，是不是unor杠C啊，unicor是指将相邻的。呃，因为已经排过序了。

所以将相邻的这个数呃进行排呃，统计有几行相同的。就有几行相同的。好，那么这边他就会将把它的相同的行就出现相同的行就出现。因为一行就是一个单词，所以出现这个这个词名出现42次。啊，那我们可以改变它的顺序。

比如说呃可这个是空格，空格出现了211，你可以把它过滤掉，这个也可以把它过滤掉。好，我们就不管它了啊。然后还要最后一步是把它打印出来是吧？打印出来的时候，我们是单词在前，那个在后面，那就是杠F。

A杠A哦，不用指定了是吧，默认是空格。啊，单引号引起来print。打印。第几行呢，先打印第二行，然后先打印第二列，然后再打印什么？第一列回去了OK这个就是实现了啊，前面是单词，后面是次频。



![](img/6b7d4911cf4b1ade9a2b6b93355972a9_111.png)

对，后面是次平啊。OK吧。看大家还有什么问题吗？没做作业的同学，估计我现在再讲一遍，你也不知道，因为我不可能再从头到尾跟你讲一遍这个怎么实现那给你演个例子。因为上在上周我讲课的时候。

我就已经给大家演示过了。呃，这个我现在这样讲，你只能做过了，你才能听懂。没做过的话，我估计估计还是不知道我在讲什么。上周的没有没有把它给。补起来啊，O吗？这个地方。看大家还有什么问题吗？

所以作业一定要做啊，因为我其实那天非常生气。当然也是为了大家考虑啊，因为我不希望大家任何一个人落下太多了。如果你不做的话，你就真的就是你越来越多越来越多。这是一个恶性循环，不太好。嗯。好了，那我们。哦。

我们继续上课了啊，这个我没有给大家改，因为大家做的方法方式都不一样，而且你用的一些这个我还要去验证，所以我没有给大家改，我只把前面给大家改了。不过这个地方你可以去参考其他同学做的。

或者是参考我做的都可以。只要你实现了就没什么问题。而且要尽量用简单的方法去实现啊，不要搞太复杂了，越简单越好啊，越简单实现你的目这个这个这个这个功能是最好的啊，最好的方法。



![](img/6b7d4911cf4b1ade9a2b6b93355972a9_113.png)

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_114.png)

![](img/6b7d4911cf4b1ade9a2b6b93355972a9_115.png)