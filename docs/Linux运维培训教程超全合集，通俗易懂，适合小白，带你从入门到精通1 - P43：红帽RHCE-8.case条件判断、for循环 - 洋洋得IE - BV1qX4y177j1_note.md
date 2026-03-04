# Linux运维培训教程：1.43：case条件判断与for循环

![](img/7aa6046b1dcf65d00fcaff77a397b612_1.png)

![](img/7aa6046b1dcf65d00fcaff77a397b612_3.png)

在本节课中，我们将要学习Shell脚本中的两种重要结构：`case`条件判断语句和`for`循环语句。通过学习，你将能够理解它们的基本语法、应用场景，并能够编写简单的脚本来实现自动化任务。

## case条件判断语句

上一节我们介绍了基础的`if`条件判断，本节中我们来看看另一种判断结构——`case`语句。`case`语句适用于对同一个变量的多种可能值进行匹配判断，其结构比多个`if-elif`语句更为清晰。

![](img/7aa6046b1dcf65d00fcaff77a397b612_5.png)

![](img/7aa6046b1dcf65d00fcaff77a397b612_7.png)

**case语句的基本语法如下：**
```bash
case 变量 in
模式1)
    命令序列1
    ;;
模式2)
    命令序列2
    ;;
*)
    默认命令序列
    ;;
esac
```
执行脚本时，需要给这个变量传递一个值。`case`语句会将该值与各个模式进行匹配，如果匹配成功，则执行对应的命令序列。每个命令序列以两个分号 `;;` 结束。`*)` 代表默认情况，即所有模式都不匹配时执行的命令。

![](img/7aa6046b1dcf65d00fcaff77a397b612_9.png)

![](img/7aa6046b1dcf65d00fcaff77a397b612_11.png)

![](img/7aa6046b1dcf65d00fcaff77a397b612_13.png)

![](img/7aa6046b1dcf65d00fcaff77a397b612_15.png)

![](img/7aa6046b1dcf65d00fcaff77a397b612_16.png)

![](img/7aa6046b1dcf65d00fcaff77a397b612_18.png)

`case`语句的核心作用是做条件判断：条件成功，执行对应命令；条件失败，则不执行。

![](img/7aa6046b1dcf65d00fcaff77a397b612_20.png)

![](img/7aa6046b1dcf65d00fcaff77a397b612_22.png)

![](img/7aa6046b1dcf65d00fcaff77a397b612_24.png)

![](img/7aa6046b1dcf65d00fcaff77a397b612_25.png)

![](img/7aa6046b1dcf65d00fcaff77a397b612_27.png)

## for循环语句

![](img/7aa6046b1dcf65d00fcaff77a397b612_29.png)

![](img/7aa6046b1dcf65d00fcaff77a397b612_31.png)

了解了`case`判断后，本节我们来看看`for`循环。`for`循环的核心功能是**循环处理**，即根据变量取值，重复执行某些命令。

![](img/7aa6046b1dcf65d00fcaff77a397b612_33.png)

![](img/7aa6046b1dcf65d00fcaff77a397b612_34.png)

**for循环的基本语法如下：**
```bash
for 变量名 in 值列表
do
    命令序列
done
```
循环会对`in`关键字后面的“值列表”中的每一个值进行操作。每次循环，会将列表中的一个值赋给“变量名”，只要变量中有值，就会执行一次`do`和`done`之间的命令序列。当列表中的所有值都被遍历后，循环结束。

为了更直观地理解，让我们看一个创建用户的脚本示例。

![](img/7aa6046b1dcf65d00fcaff77a397b612_36.png)

![](img/7aa6046b1dcf65d00fcaff77a397b612_37.png)

以下是`for`循环创建用户的脚本内容：
```bash
#!/bin/bash
for user in xiaofang xiaowei jiumei alian
do
    useradd $user 2>/dev/null
    echo "$user 创建成功。"
    echo "1" | passwd --stdin $user 2>/dev/null
    echo "$user 密码设置成功。"
done
```
脚本执行流程如下：
1.  第一次循环，变量`user`的值为`xiaofang`，执行命令：创建用户`xiaofang`并设置密码。
2.  本次循环结束，回到`in`后查看下一个值`xiaowei`。
3.  开始第二次循环，变量`user`的值变为`xiaowei`，重复执行创建用户和设置密码的命令。
4.  如此循环，直到列表`xiaofang xiaowei jiumei alian`中的所有值都被处理完毕，整个循环结束。

![](img/7aa6046b1dcf65d00fcaff77a397b612_39.png)

![](img/7aa6046b1dcf65d00fcaff77a397b612_41.png)

![](img/7aa6046b1dcf65d00fcaff77a397b612_43.png)

![](img/7aa6046b1dcf65d00fcaff77a397b612_45.png)

![](img/7aa6046b1dcf65d00fcaff77a397b612_46.png)

`for`循环能帮助我们自动化完成需要重复执行的任务。

## for循环应用实例：测试服务器连通性

`for`循环的一个典型应用是批量测试网络中服务器的连通性。例如，作为运维人员，你需要检查一批服务器（IP地址为192.168.0.1到192.168.0.254）是否在线。手动逐个ping测试显然不现实，此时就可以利用`for`循环。

![](img/7aa6046b1dcf65d00fcaff77a397b612_48.png)

![](img/7aa6046b1dcf65d00fcaff77a397b612_50.png)

![](img/7aa6046b1dcf65d00fcaff77a397b612_52.png)

![](img/7aa6046b1dcf65d00fcaff77a397b612_54.png)

最初的脚本思路可能是这样的：
```bash
#!/bin/bash
for ip in 192.168.0.{1..254}
do
    ping $ip
done
```
但直接运行`ping`命令会持续进行，不会自动停止，导致脚本卡住。因此，我们需要控制ping的次数和超时时间。

以下是优化后的脚本：
```bash
#!/bin/bash
for ip in 192.168.0.{1..254}
do
    ping -c 2 -i 0.1 -W 1 $ip &>/dev/null
    if [ $? -eq 0 ]; then
        echo "$ip is up."
    else
        echo "$ip is down."
    fi
done
```
**代码解释：**
*   `ping -c 2 -i 0.1 -W 1 $ip &>/dev/null`：对每个IP执行ping命令。
    *   `-c 2`：发送2个数据包。
    *   `-i 0.1`：每个数据包间隔0.1秒。
    *   `-W 1`：等待回复的超时时间为1秒。
    *   `&>/dev/null`：将命令的所有输出（包括正确和错误）重定向到“黑洞”设备，即不显示在屏幕上。
*   `if [ $? -eq 0 ]; then`：判断上一条命令（即ping）的返回值。
    *   `$?` 是一个特殊变量，代表上一个命令的退出状态。退出状态为0通常表示命令成功执行（此处表示主机可达）。
    *   `-eq` 是用于整数比较的运算符，表示“等于”。
*   根据ping命令的返回值，判断主机是`up`（在线）还是`down`（离线），并将结果输出到屏幕。

![](img/7aa6046b1dcf65d00fcaff77a397b612_56.png)

为了进一步优化，我们可以将结果分类保存到文件中，并将脚本放在后台运行，这样不会干扰前台工作。

![](img/7aa6046b1dcf65d00fcaff77a397b612_58.png)

![](img/7aa6046b1dcf65d00fcaff77a397b612_60.png)

![](img/7aa6046b1dcf65d00fcaff77a397b612_61.png)

![](img/7aa6046b1dcf65d00fcaff77a397b612_63.png)

以下是最终版的脚本示例：
```bash
#!/bin/bash
for ip in 192.168.0.{1..254}
do
    ping -c 2 -i 0.1 -W 1 $ip &>/dev/null
    if [ $? -eq 0 ]; then
        echo "$ip" >> /opt/net_up.txt
    else
        echo "$ip" >> /opt/net_down.txt
    fi
done
```
执行时，在命令后加上`&`符号即可放入后台：
```bash
bash ping_hosts.sh &
```
之后，可以通过查看`/opt/net_up.txt`和`/opt/net_down.txt`文件来获取所有在线和离线的主机列表。

![](img/7aa6046b1dcf65d00fcaff77a397b612_64.png)

![](img/7aa6046b1dcf65d00fcaff77a397b612_66.png)

![](img/7aa6046b1dcf65d00fcaff77a397b612_67.png)

**关于值列表的生成：**
脚本中`192.168.0.{1..254}`是大括号扩展，用于生成1到254的数字序列。你也可以使用`seq`命令达到相同效果：
```bash
for i in $(seq 1 254)
do
    ping 192.168.0.$i ...
done
```
两种方式均可，选择你习惯的使用即可。

![](img/7aa6046b1dcf65d00fcaff77a397b612_69.png)

![](img/7aa6046b1dcf65d00fcaff77a397b612_71.png)

## 总结

![](img/7aa6046b1dcf65d00fcaff77a397b612_73.png)

![](img/7aa6046b1dcf65d00fcaff77a397b612_74.png)

本节课中我们一起学习了Shell脚本中的`case`条件判断和`for`循环。
*   `case`语句：用于对变量进行多分支匹配判断，结构清晰。
*   `for`循环：用于遍历一个列表，并对列表中的每个元素重复执行相同的命令序列，是实现批量操作和自动化的利器。

![](img/7aa6046b1dcf65d00fcaff77a397b612_76.png)

通过ping测试主机连通性的实例，我们展示了如何将`for`循环与条件判断结合，解决实际的运维问题，并将结果输出整理。掌握这些结构是编写高效Shell脚本的基础。