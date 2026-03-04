# Linux基础教程：P40：在bashrc和profile等文件中定义变量永久生效

在本节课中，我们将学习如何让Shell变量永久生效。变量默认只在当前Shell会话中有效，一旦关闭终端就会消失。为了让变量（如`PATH`或自定义变量）在每次登录或打开新终端时都能自动生效，我们需要将它们写入特定的配置文件中。

## 变量生效范围回顾

上一节我们介绍了本地变量和环境变量的区别。现在我们来回顾一下：在Shell中直接定义的变量是临时的。

例如，开启一个子Shell（`bash`），在其中定义一个变量`A=100`。当退出这个子Shell后，变量`A`就消失了。这是因为变量存储在内存中，Shell进程结束，其内存空间就被释放。

但是，像`PS1`（提示符）或`PATH`这样的变量似乎永远存在。这是因为它们被定义在了系统或用户的配置文件中，在每次启动Shell时自动加载。

## 全局配置文件：/etc/profile

为了让变量对所有用户永久生效，我们可以将其写入全局配置文件。

第一个重要的文件是 `/etc/profile`。这个文件包含了许多系统级别的环境变量设置，它们通常以大写字母命名，并使用 `export` 命令导出为环境变量，例如 `PATH`。

如果我想定义一个永久变量，比如 `A=100`，可以编辑这个文件：
```bash
A=100
export A
```
保存文件后，变量`A`并不会立即生效，因为它只是被写入了磁盘，尚未加载到内存。需要使用 `source` 命令来重新读取文件：
```bash
source /etc/profile
```
执行后，变量`A`就在当前Shell中生效了。但请注意，此时`A`只是一个**本地变量**。虽然它定义在`/etc/profile`中，但如果没有`export`，它就不能被其子Shell继承。

![](img/d4a0e4959d4b43dd74dd0163a0cf5c6a_1.png)

为了让子Shell也能使用，必须将其导出为环境变量：
```bash
export A
```
保存并再次 `source /etc/profile` 后，`A`就变成了环境变量。此时，在当前Shell中执行 `bash` 开启子Shell，使用 `echo $A` 命令就能看到其值。

![](img/d4a0e4959d4b43dd74dd0163a0cf5c6a_3.png)

定义在 `/etc/profile` 中的环境变量被称为**全局环境变量**。所有用户（如 `root`、`user1`）在通过 `su - 用户名` 方式登录时，都会读取这个文件，从而继承这些变量。

![](img/d4a0e4959d4b43dd74dd0163a0cf5c6a_5.png)

![](img/d4a0e4959d4b43dd74dd0163a0cf5c6a_7.png)

## 用户配置文件：~/.bash_profile

有时，我们可能希望为特定用户设置不同的变量值。例如，全局设置 `A=100`，但希望 `admin` 用户的 `A=200`。

![](img/d4a0e4959d4b43dd74dd0163a0cf5c6a_9.png)

这时可以使用用户级别的配置文件，即用户家目录下的 `~/.bash_profile`（例如 `/home/admin/.bash_profile`）。

编辑该文件，加入：
```bash
A=200
export A
```
保存后，同样需要用 `source ~/.bash_profile` 使其在当前Shell生效。此时，切换到 `admin` 用户，变量 `A` 的值是200；而切换到其他用户（如 `user1`），`A` 的值仍然是全局设置的100。

![](img/d4a0e4959d4b43dd74dd0163a0cf5c6a_11.png)

定义在 `~/.bash_profile` 中的环境变量被称为**用户环境变量**，只有该用户自己可以继承。

当全局和用户配置存在冲突时（例如都定义了变量`A`），**用户配置的优先级更高**。

![](img/d4a0e4959d4b43dd74dd0163a0cf5c6a_13.png)

## 登录Shell与非登录Shell

![](img/d4a0e4959d4b43dd74dd0163a0cf5c6a_15.png)

![](img/d4a0e4959d4b43dd74dd0163a0cf5c6a_17.png)

之前提到，需要手动`source`文件变量才生效，这很麻烦。系统通过区分Shell的启动方式来自动加载不同的文件，解决了这个问题。

![](img/d4a0e4959d4b43dd74dd0163a0cf5c6a_19.png)

![](img/d4a0e4959d4b43dd74dd0163a0cf5c6a_21.png)

Shell主要分为两种类型：
*   **登录Shell (Login Shell)**：需要用户进行身份验证的完整会话。
*   **非登录Shell (Non-Login Shell)**：不需要完整登录验证的Shell会话。

![](img/d4a0e4959d4b43dd74dd0163a0cf5c6a_23.png)

以下是常见的几种情况：

**登录Shell包括：**
1.  通过图形界面或字符界面输入用户名密码登录系统时打开的Shell。
2.  使用 `su - 用户名` 命令切换用户时。

![](img/d4a0e4959d4b43dd74dd0163a0cf5c6a_25.png)

![](img/d4a0e4959d4b43dd74dd0163a0cf5c6a_27.png)

**非登录Shell包括：**
1.  在图形界面中打开的终端（如GNOME Terminal）。
2.  使用 `su 用户名`（不加 `-`）命令切换用户时。
3.  在已有Shell中直接执行 `bash` 命令开启的子Shell。
4.  执行Shell脚本时。

`su -` 与 `su` 的关键区别在于：`su -` 会模拟一次完整的用户登录，加载该用户的所有环境配置；而 `su` 则是在当前环境基础上切换用户身份，会保留部分原用户的环境属性。

![](img/d4a0e4959d4b43dd74dd0163a0cf5c6a_29.png)

## 配置文件读取规则

![](img/d4a0e4959d4b43dd74dd0163a0cf5c6a_31.png)

![](img/d4a0e4959d4b43dd74dd0163a0cf5c6a_33.png)

不同的Shell类型会读取不同的配置文件组合。除了已介绍的 `/etc/profile` 和 `~/.bash_profile`，还有两个重要文件：
*   `/etc/bashrc`：全局的Shell配置文件。
*   `~/.bashrc`：用户个人的Shell配置文件。

以下是读取规则：

![](img/d4a0e4959d4b43dd74dd0163a0cf5c6a_35.png)

**登录Shell启动时，按顺序读取以下文件：**
1.  `/etc/profile`
2.  `~/.bash_profile`

**非登录Shell启动时，按顺序读取以下文件：**
1.  `~/.bashrc`
2.  `/etc/bashrc`

![](img/d4a0e4959d4b43dd74dd0163a0cf5c6a_37.png)

为了更直观地理解，我们通过一个例子来测试。在四个文件中分别定义不同的变量：
1.  `/etc/profile` 中定义 `A=100`
2.  `~/.bash_profile` 中定义 `B=200`
3.  `/etc/bashrc` 中定义 `C=300`
4.  `~/.bashrc` 中定义 `D=400`

![](img/d4a0e4959d4b43dd74dd0163a0cf5c6a_39.png)

![](img/d4a0e4959d4b43dd74dd0163a0cf5c6a_41.png)

然后观察不同情况下哪些变量存在：

![](img/d4a0e4959d4b43dd74dd0163a0cf5c6a_43.png)

![](img/d4a0e4959d4b43dd74dd0163a0cf5c6a_45.png)

*   **情况1：使用 `su - admin` 登录**
    *   Shell类型：登录Shell
    *   结果：变量 `A`, `B`, `C`, `D` 全部存在。因为登录Shell会读取全部四个文件。

![](img/d4a0e4959d4b43dd74dd0163a0cf5c6a_46.png)

![](img/d4a0e4959d4b43dd74dd0163a0cf5c6a_48.png)

*   **情况2：使用 `su - user3` 登录**
    *   Shell类型：登录Shell
    *   结果：变量 `A`, `C` 存在。因为会读取全局的 `/etc/profile` 和 `/etc/bashrc`，但 `user3` 的家目录下没有自定义的 `.bash_profile` 和 `.bashrc`，所以 `B` 和 `D` 不存在。

![](img/d4a0e4959d4b43dd74dd0163a0cf5c6a_50.png)

![](img/d4a0e4959d4b43dd74dd0163a0cf5c6a_52.png)

*   **情况3：使用 `su admin` 切换**
    *   Shell类型：非登录Shell
    *   结果：变量 `C`, `D` 存在。因为非登录Shell只读取 `~/.bashrc` 和 `/etc/bashrc`。`admin` 用户的 `~/.bashrc` 定义了 `D`。

![](img/d4a0e4959d4b43dd74dd0163a0cf5c6a_54.png)

![](img/d4a0e4959d4b43dd74dd0163a0cf5c6a_56.png)

*   **情况4：使用 `su user3` 切换**
    *   Shell类型：非登录Shell
    *   结果：只有变量 `C` 存在。因为非登录Shell读取 `/etc/bashrc`（定义了`C`），但 `user3` 用户没有自定义的 `~/.bashrc` 文件。

![](img/d4a0e4959d4b43dd74dd0163a0cf5c6a_58.png)

![](img/d4a0e4959d4b43dd74dd0163a0cf5c6a_60.png)

这个例子清晰地展示了不同Shell类型读取配置文件的差异。

![](img/d4a0e4959d4b43dd74dd0163a0cf5c6a_62.png)

## 环境变量的继承

![](img/d4a0e4959d4b43dd74dd0163a0cf5c6a_64.png)

你可能会问：既然在图形界面打开的终端（非登录Shell）读不到 `/etc/profile`，为什么像 `PATH` 这样的全局变量依然存在？

![](img/d4a0e4959d4b43dd74dd0163a0cf5c6a_66.png)

这是因为环境变量具有**继承性**。当你首次登录图形界面时（这是一个登录Shell），系统已经读取了 `/etc/profile` 等文件，并将其中导出的环境变量加载到了内存中。之后在这个会话里打开的任何终端（非登录Shell），都是这个登录Shell的**子进程**，它们会继承父进程的所有环境变量。

所以，虽然非登录Shell没有直接读取 `/etc/profile`，但它通过继承获得了其中定义的环境变量。

![](img/d4a0e4959d4b43dd74dd0163a0cf5c6a_68.png)

## 实践建议：别名与变量定义

![](img/d4a0e4959d4b43dd74dd0163a0cf5c6a_70.png)

![](img/d4a0e4959d4b43dd74dd0163a0cf5c6a_71.png)

![](img/d4a0e4959d4b43dd74dd0163a0cf5c6a_72.png)

了解规则后，我们可以更合理地安排配置：

*   **定义全局环境变量**：编辑 `/etc/profile` 文件。
*   **定义用户环境变量**：编辑 `~/.bash_profile` 文件。
*   **定义命令别名**：通常建议将别名（`alias`）定义在 `bashrc` 文件中。
    *   想让所有用户生效：编辑 `/etc/bashrc`。
    *   只想让当前用户生效：编辑 `~/.bashrc`。

![](img/d4a0e4959d4b43dd74dd0163a0cf5c6a_74.png)

这样能确保无论在登录还是非登录Shell中，别名都能正常使用。

![](img/d4a0e4959d4b43dd74dd0163a0cf5c6a_76.png)

## 配置文件读取顺序与优先级

配置文件的读取是有顺序的，后读取的文件中的设置会覆盖先读取的文件中的同名设置。

对于登录Shell：
1.  先读 `/etc/profile`
2.  后读 `~/.bash_profile`
因此，用户配置 (`~/.bash_profile`) 会覆盖全局配置 (`/etc/profile`)。

![](img/d4a0e4959d4b43dd74dd0163a0cf5c6a_78.png)

对于非登录Shell：
1.  先读 `~/.bashrc`
2.  后读 `/etc/bashrc`
因此，全局配置 (`/etc/bashrc`) 会覆盖用户配置 (`~/.bashrc`)。这一点与登录Shell的顺序相反，需要特别注意。

---

![](img/d4a0e4959d4b43dd74dd0163a0cf5c6a_80.png)

本节课中我们一起学习了如何通过配置文件使Shell变量永久生效。我们掌握了两个全局配置文件 (`/etc/profile`, `/etc/bashrc`) 和两个用户配置文件 (`~/.bash_profile`, `~/.bashrc`) 的作用与区别。关键是要理解**登录Shell**与**非登录Shell**在启动时读取文件的差异，以及环境变量的继承特性。正确地将变量或别名写入对应的文件，可以让我们的Linux工作环境更加高效和个性化。