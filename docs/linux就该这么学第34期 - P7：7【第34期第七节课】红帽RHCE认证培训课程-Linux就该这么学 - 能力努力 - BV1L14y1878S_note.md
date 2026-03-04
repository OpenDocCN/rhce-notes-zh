# Linux就该这么学：第34期：第7课：循环语句与计划任务 🚀

![](img/0340d5f09e196b74b3c8e0e0e79def8d_1.png)

![](img/0340d5f09e196b74b3c8e0e0e79def8d_3.png)

在本节课中，我们将要学习Shell脚本中的`while`循环语句和`case`条件测试语句，以及如何使用`at`和`cron`来设置计划任务。这些知识将帮助你编写更强大、更自动化的脚本。

![](img/0340d5f09e196b74b3c8e0e0e79def8d_5.png)

![](img/0340d5f09e196b74b3c8e0e0e79def8d_7.png)

![](img/0340d5f09e196b74b3c8e0e0e79def8d_9.png)

![](img/0340d5f09e196b74b3c8e0e0e79def8d_11.png)

![](img/0340d5f09e196b74b3c8e0e0e79def8d_13.png)

## 概述 📋

![](img/0340d5f09e196b74b3c8e0e0e79def8d_15.png)

![](img/0340d5f09e196b74b3c8e0e0e79def8d_17.png)

上一节我们介绍了`for`循环语句，它根据一个范围（如列表或IP地址段）进行循环。本节中我们来看看`while`循环语句，它是根据一个条件来进行循环的。只要条件满足，循环就会一直执行下去。此外，我们还将学习`case`语句来处理多条件判断，并掌握如何使用计划任务来定时执行脚本。

![](img/0340d5f09e196b74b3c8e0e0e79def8d_19.png)

![](img/0340d5f09e196b74b3c8e0e0e79def8d_21.png)

![](img/0340d5f09e196b74b3c8e0e0e79def8d_23.png)

![](img/0340d5f09e196b74b3c8e0e0e79def8d_25.png)

![](img/0340d5f09e196b74b3c8e0e0e79def8d_27.png)

---

![](img/0340d5f09e196b74b3c8e0e0e79def8d_29.png)

![](img/0340d5f09e196b74b3c8e0e0e79def8d_31.png)

![](img/0340d5f09e196b74b3c8e0e0e79def8d_33.png)

## 7.1 While循环语句 🔄

`while`循环语句与`for`循环有共同点，也有区别。`for`循环是根据范围进行循环，而`while`循环是根据条件进行循环。只要条件为真（`true`），循环就会一直执行，直到条件为假（`false`）才会停止。

**核心概念公式：**
```
while [ 条件 ]
do
    命令序列
done
```

![](img/0340d5f09e196b74b3c8e0e0e79def8d_35.png)

![](img/0340d5f09e196b74b3c8e0e0e79def8d_37.png)

![](img/0340d5f09e196b74b3c8e0e0e79def8d_39.png)

例如，蠕虫病毒就是一种无限循环，它会不断消耗系统资源，直到系统崩溃。这就是一个条件永远为真的`while`循环。

![](img/0340d5f09e196b74b3c8e0e0e79def8d_41.png)

![](img/0340d5f09e196b74b3c8e0e0e79def8d_43.png)

![](img/0340d5f09e196b74b3c8e0e0e79def8d_45.png)

### 实战：猜数字游戏 🎮

![](img/0340d5f09e196b74b3c8e0e0e79def8d_47.png)

![](img/0340d5f09e196b74b3c8e0e0e79def8d_49.png)

我们将编写一个Shell脚本来模拟一个猜数字游戏。脚本会随机生成一个0到1000之间的数字，用户需要猜测这个数字。脚本会根据用户的输入给出“太高”、“太低”或“猜中”的提示，并统计猜测次数。

![](img/0340d5f09e196b74b3c8e0e0e79def8d_51.png)

![](img/0340d5f09e196b74b3c8e0e0e79def8d_53.png)

以下是脚本的编写步骤：

![](img/0340d5f09e196b74b3c8e0e0e79def8d_55.png)

![](img/0340d5f09e196b74b3c8e0e0e79def8d_57.png)

1.  **脚本声明与注释：**
    每个Shell脚本都需要以声明解释器开头，并可以添加注释说明脚本功能。
    ```bash
    #!/bin/bash
    # 这是一个猜数字游戏的脚本
    ```

![](img/0340d5f09e196b74b3c8e0e0e79def8d_59.png)

![](img/0340d5f09e196b74b3c8e0e0e79def8d_61.png)

2.  **生成随机数字并限制范围：**
    使用`$RANDOM`变量生成随机数，并通过取余运算`%`将其限制在0-999之间。使用`$(())`进行算术运算。
    ```bash
    PRICE=$(($RANDOM % 1000))
    TIMES=0
    ```

3.  **构建主循环：**
    使用`while true`创建一个无限循环。在循环内，使用`read`命令读取用户输入，并使用`let`命令增加猜测次数。
    ```bash
    while true
    do
        read -p "请输入您猜测的价格（0-999）：" INT
        let TIMES++
    ```

![](img/0340d5f09e196b74b3c8e0e0e79def8d_63.png)

4.  **使用if语句进行判断：**
    比较用户输入`$INT`与随机数`$PRICE`。
    ```bash
        if [ $INT -eq $PRICE ]; then
            echo "恭喜您猜中了！实际价格是 $PRICE"
            echo "您总共猜测了 $TIMES 次"
            exit 0
        elif [ $INT -gt $PRICE ]; then
            echo "太高了！"
        else
            echo "太低了！"
        fi
    done
    ```

![](img/0340d5f09e196b74b3c8e0e0e79def8d_65.png)

![](img/0340d5f09e196b74b3c8e0e0e79def8d_67.png)

![](img/0340d5f09e196b74b3c8e0e0e79def8d_69.png)

![](img/0340d5f09e196b74b3c8e0e0e79def8d_71.png)

![](img/0340d5f09e196b74b3c8e0e0e79def8d_73.png)

![](img/0340d5f09e196b74b3c8e0e0e79def8d_75.png)

**完整脚本示例：**
```bash
#!/bin/bash
# 猜数字游戏脚本

![](img/0340d5f09e196b74b3c8e0e0e79def8d_77.png)

PRICE=$(($RANDOM % 1000))
TIMES=0

echo "商品实际价格在0-999之间，猜猜看是多少？"

while true
do
    read -p "请输入您猜测的价格：" GUESS
    let TIMES++

    if [ $GUESS -eq $PRICE ]; then
        echo "恭喜！猜对了！价格正是 $PRICE。"
        echo "您总共猜了 $TIMES 次。"
        exit 0
    elif [ $GUESS -gt $PRICE ]; then
        echo "太高了，再低一点。"
    else
        echo "太低了，再高一点。"
    fi
done
```

![](img/0340d5f09e196b74b3c8e0e0e79def8d_79.png)

---

![](img/0340d5f09e196b74b3c8e0e0e79def8d_81.png)

![](img/0340d5f09e196b74b3c8e0e0e79def8d_83.png)

![](img/0340d5f09e196b74b3c8e0e0e79def8d_85.png)

![](img/0340d5f09e196b74b3c8e0e0e79def8d_87.png)

## 7.2 Case条件测试语句 🧩

![](img/0340d5f09e196b74b3c8e0e0e79def8d_89.png)

![](img/0340d5f09e196b74b3c8e0e0e79def8d_91.png)

![](img/0340d5f09e196b74b3c8e0e0e79def8d_93.png)

![](img/0340d5f09e196b74b3c8e0e0e79def8d_95.png)

![](img/0340d5f09e196b74b3c8e0e0e79def8d_97.png)

![](img/0340d5f09e196b74b3c8e0e0e79def8d_99.png)

![](img/0340d5f09e196b74b3c8e0e0e79def8d_101.png)

`case`语句用于多条件分支匹配，比多个`if-elif`语句更清晰。它常用于判断用户输入的类型。

**核心概念格式：**
```bash
case 变量值 in
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
**注意：** 每个模式分支必须以两个分号`;;`结束。`*`代表匹配所有未指定的模式。`esac`是`case`的反写，表示语句结束。

![](img/0340d5f09e196b74b3c8e0e0e79def8d_103.png)

![](img/0340d5f09e196b74b3c8e0e0e79def8d_105.png)

![](img/0340d5f09e196b74b3c8e0e0e79def8d_107.png)

![](img/0340d5f09e196b74b3c8e0e0e79def8d_109.png)

### 实战：判断输入类型 ✍️

以下脚本判断用户输入的是字母、数字还是其他字符。

![](img/0340d5f09e196b74b3c8e0e0e79def8d_111.png)

```bash
#!/bin/bash

![](img/0340d5f09e196b74b3c8e0e0e79def8d_113.png)

![](img/0340d5f09e196b74b3c8e0e0e79def8d_115.png)

![](img/0340d5f09e196b74b3c8e0e0e79def8d_117.png)

![](img/0340d5f09e196b74b3c8e0e0e79def8d_119.png)

![](img/0340d5f09e196b74b3c8e0e0e79def8d_121.png)

![](img/0340d5f09e196b74b3c8e0e0e79def8d_123.png)

read -p "请输入一个字符：" KEY

![](img/0340d5f09e196b74b3c8e0e0e79def8d_125.png)

![](img/0340d5f09e196b74b3c8e0e0e79def8d_127.png)

![](img/0340d5f09e196b74b3c8e0e0e79def8d_129.png)

![](img/0340d5f09e196b74b3c8e0e0e79def8d_131.png)

![](img/0340d5f09e196b74b3c8e0e0e79def8d_133.png)

case $KEY in
[a-z]|[A-Z])
    echo "您输入的是一个字母。"
    ;;
[0-9])
    echo "您输入的是一个数字。"
    ;;
*)
    echo "您输入的是其他字符（空格、符号等）。"
    ;;
esac
```

![](img/0340d5f09e196b74b3c8e0e0e79def8d_135.png)

![](img/0340d5f09e196b74b3c8e0e0e79def8d_137.png)

---

![](img/0340d5f09e196b74b3c8e0e0e79def8d_139.png)

![](img/0340d5f09e196b74b3c8e0e0e79def8d_141.png)

![](img/0340d5f09e196b74b3c8e0e0e79def8d_143.png)

![](img/0340d5f09e196b74b3c8e0e0e79def8d_145.png)

![](img/0340d5f09e196b74b3c8e0e0e79def8d_147.png)

## 7.3 计划任务 ⏰

![](img/0340d5f09e196b74b3c8e0e0e79def8d_149.png)

![](img/0340d5f09e196b74b3c8e0e0e79def8d_151.png)

计划任务允许我们在指定的时间自动执行命令或脚本，实现自动化运维。Linux中有两种计划任务：`at`（一次性）和`cron`（周期性）。

![](img/0340d5f09e196b74b3c8e0e0e79def8d_153.png)

### 7.3.1 At一次性计划任务

![](img/0340d5f09e196b74b3c8e0e0e79def8d_155.png)

![](img/0340d5f09e196b74b3c8e0e0e79def8d_157.png)

![](img/0340d5f09e196b74b3c8e0e0e79def8d_159.png)

`at`命令用于安排一个在指定时间运行一次的任务。

![](img/0340d5f09e196b74b3c8e0e0e79def8d_161.png)

![](img/0340d5f09e196b74b3c8e0e0e79def8d_163.png)

**常用命令：**
- `at 时间`：设置任务，在交互界面输入要执行的命令，按`Ctrl+D`保存。
- `at -l` 或 `atq`：查看等待中的任务列表。
- `at -c 任务编号`：查看特定任务的详情。
- `atrm 任务编号`：删除指定任务。

**示例：** 在20:10重启系统
```bash
at 20:10
at> reboot
at> <按 Ctrl+D 保存>
```

### 7.3.2 Cron周期性计划任务

![](img/0340d5f09e196b74b3c8e0e0e79def8d_165.png)

![](img/0340d5f09e196b74b3c8e0e0e79def8d_167.png)

![](img/0340d5f09e196b74b3c8e0e0e79def8d_169.png)

![](img/0340d5f09e196b74b3c8e0e0e79def8d_171.png)

`cron`服务用于设置周期性执行的任务。其配置文件遵循特定格式。

**时间格式：**
```
分 时 日 月 星期 命令
```
- **字段范围：**
    - 分：0-59
    - 时：0-23
    - 日：1-31
    - 月：1-12
    - 星期：0-7 (0和7都代表周日)
- **特殊符号：**
    - `*`：代表任意值。
    - `,`：代表多个不连续的时间点（如`1,3,5`）。
    - `-`：代表一个连续的时间范围（如`1-5`）。
    - `*/n`：代表每隔n个单位（如`*/2`表示每两小时）。

![](img/0340d5f09e196b74b3c8e0e0e79def8d_173.png)

**配置方法：**
使用`crontab -e`命令编辑当前用户的计划任务（推荐，因为有语法检查）。也可以直接编辑`/etc/crontab`文件（系统级任务，需指定用户）。

**示例：**
- 每天3:20重启：`20 3 * * * /usr/sbin/reboot`
- 每周六4:20重启：`20 4 * * 6 /usr/sbin/reboot`
- 每隔2小时重启：`0 */2 * * * /usr/sbin/reboot`
- 每月1号和15号的3:20重启：`20 3 1,15 * * /usr/sbin/reboot`

![](img/0340d5f09e196b74b3c8e0e0e79def8d_175.png)

![](img/0340d5f09e196b74b3c8e0e0e79def8d_177.png)

**管理Cron服务：**
- 查看任务：`crontab -l`
- 删除所有任务：`crontab -r`
- 确保服务运行：
    ```bash
    systemctl status crond    # 查看状态
    systemctl restart crond   # 重启服务
    systemctl enable crond    # 设置开机自启
    ```

![](img/0340d5f09e196b74b3c8e0e0e79def8d_179.png)

---

![](img/0340d5f09e196b74b3c8e0e0e79def8d_181.png)

![](img/0340d5f09e196b74b3c8e0e0e79def8d_183.png)

![](img/0340d5f09e196b74b3c8e0e0e79def8d_185.png)

![](img/0340d5f09e196b74b3c8e0e0e79def8d_187.png)

![](img/0340d5f09e196b74b3c8e0e0e79def8d_189.png)

## 7.4 用户身份与能力 👤

Linux系统中用户身份分为三类：
1.  **管理员（root）**：UID为0，拥有最高权限。
2.  **系统用户**：UID范围为1-999（RHEL 7/8）或1-499（RHEL 5/6），通常用于运行服务，不可登录系统，权限受限。
3.  **普通用户**：UID从1000开始（RHEL 7/8）或500开始（RHEL 5/6），用于日常操作。

**注意：** UID只是一个标识符。虽然UID 0一定是管理员，但普通用户和系统用户的UID范围只是惯例，可以手动修改。区分用户类型主要依据其功能和是否允许登录。

![](img/0340d5f09e196b74b3c8e0e0e79def8d_191.png)

**相关命令：**
- `id [用户名]`：查看用户UID、GID及所属组信息。
- `useradd [选项] 用户名`：创建新用户。
    - `-u`：指定UID。
    - `-g`：指定基本组。
    - `-G`：指定扩展组。
    - `-d`：指定家目录。
- `usermod [选项] 用户名`：修改用户属性（选项同`useradd`）。
- `passwd [用户名]`：修改用户密码。
- `userdel [-r] 用户名`：删除用户。`-r`选项会同时删除其家目录。

![](img/0340d5f09e196b74b3c8e0e0e79def8d_193.png)

![](img/0340d5f09e196b74b3c8e0e0e79def8d_195.png)

![](img/0340d5f09e196b74b3c8e0e0e79def8d_197.png)

**用户与组的概念：**
- **基本组（初始组）**：用户创建时自动生成的同名组，一个用户只有一个基本组。
- **扩展组（附加组）**：用户后续可以加入的其他组，一个用户可以有多个扩展组。
- 文件权限检查时，只要用户属于文件所属组（无论是基本组还是扩展组），就拥有该组的权限。

![](img/0340d5f09e196b74b3c8e0e0e79def8d_199.png)

---

## 总结 🎯

![](img/0340d5f09e196b74b3c8e0e0e79def8d_201.png)

![](img/0340d5f09e196b74b3c8e0e0e79def8d_203.png)

本节课中我们一起学习了：
1.  **`while`循环语句**：基于条件进行循环，常用于需要持续判断的场景。
2.  **`case`条件测试语句**：用于多分支模式匹配，使脚本逻辑更清晰。
3.  **计划任务**：
    - `at`：管理一次性定时任务。
    - `cron`：管理周期性定时任务，重点掌握其时间格式`分 时 日 月 星期 命令`及特殊符号用法。
4.  **用户身份管理**：理解了管理员、系统用户和普通用户的区别，并掌握了用户创建、修改和删除的基本命令。

![](img/0340d5f09e196b74b3c8e0e0e79def8d_205.png)

![](img/0340d5f09e196b74b3c8e0e0e79def8d_207.png)

![](img/0340d5f09e196b74b3c8e0e0e79def8d_209.png)

通过结合循环、条件判断和计划任务，你可以让系统自动完成许多重复性工作，极大地提升运维效率。