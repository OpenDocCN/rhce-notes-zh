# Linux-RHCSA入门实战课：P6：RHCSA-第5课-软件包管理 📦

![](img/b43f32d7c242232d28f61ba726a11936_0.png)

![](img/b43f32d7c242232d28f61ba726a11936_2.png)

## 概述
在本节课中，我们将要学习Linux系统中的软件包管理。这是系统运维中的核心技能之一，我们将了解如何通过不同的方式（如RPM、YUM、源码编译等）来安装、管理和部署软件。掌握这些方法，将帮助你在不同的工作场景中高效地完成软件部署任务。

---

## 软件包类型与基础概念

在Linux系统中，软件包主要分为两种类型：**二进制包**和**源码包**。

*   **二进制包**：软件已经被预先编译成计算机可以直接识别的机器语言（0和1）。用户可以直接安装使用，无需编译过程。
*   **源码包**：软件以源代码（如C、C++语言）的形式提供。用户需要在目标系统上将其编译成二进制文件后才能安装使用。

计算机的CPU只能理解二进制指令。高级编程语言（如C、Java、Python）编写的源代码，需要通过一个“翻译官”转换成二进制。在Linux下，这个翻译过程称为**编译**，而C/C++语言常用的编译工具是 **GCC**。

---

## RPM软件包管理 🔧

![](img/b43f32d7c242232d28f61ba726a11936_4.png)

![](img/b43f32d7c242232d28f61ba726a11936_6.png)

上一节我们介绍了软件包的基本概念，本节中我们来看看Linux中一个经典的软件包管理工具：RPM。

RPM（Red Hat Package Manager）是由红帽公司提出的二进制软件包管理系统。它主要用于红帽系的Linux发行版（如RHEL、CentOS），其软件包通常以 `.rpm` 结尾。

![](img/b43f32d7c242232d28f61ba726a11936_8.png)

![](img/b43f32d7c242232d28f61ba726a11936_9.png)

### RPM包命名格式
一个典型的RPM包名称包含以下信息：
`软件名-版本号-发布次数.硬件平台.rpm`
例如：`nginx-1.16.1-1.el7.x86_64.rpm`
*   `nginx`: 软件名称
*   `1.16.1`: 版本号
*   `1.el7`: 发布次数和系统版本（el7代表RHEL/CentOS 7）
*   `x86_64`: 硬件平台（64位系统）
*   `.rpm`: 后缀名

### 常用RPM命令
以下是RPM管理中最常用的一些命令和参数。

![](img/b43f32d7c242232d28f61ba726a11936_11.png)

![](img/b43f32d7c242232d28f61ba726a11936_12.png)

![](img/b43f32d7c242232d28f61ba726a11936_14.png)

![](img/b43f32d7c242232d28f61ba726a11936_16.png)

**安装软件包**
```bash
rpm -ivh 软件包名.rpm
```
*   `-i`: 安装（install）
*   `-v`: 显示详细信息（verbose）
*   `-h`: 显示安装进度条（hash）

**查询软件包**
```bash
rpm -qa | grep 软件名      # 查询系统中是否安装了某个软件
rpm -ql 软件名            # 列出软件安装的所有文件
rpm -qi 软件名            # 显示软件的详细信息
```

**升级软件包**
```bash
rpm -Uvh 软件包名.rpm     # 升级软件包
```
> **注意**：生产环境中升级单个软件需谨慎，可能引发依赖问题，建议先在测试环境验证。

![](img/b43f32d7c242232d28f61ba726a11936_18.png)

**卸载软件包**
```bash
rpm -e 软件名             # 卸载（erase）指定软件
```

![](img/b43f32d7c242232d28f61ba726a11936_20.png)

**处理依赖问题**
RPM的一个主要缺点是依赖关系需要手动解决。如果安装时提示缺少依赖（如 `error: Failed dependencies`），需要先安装所需的依赖包。依赖包通常以 `-devel` 结尾（开发包）。

对于多个相互依赖的包，可以尝试一次性安装：
```bash
rpm -ivh *.rpm           # 安装当前目录下所有rpm包，自动解决依赖
```

---

## 压缩与解压工具 📁

在获取软件源码或备份文件时，我们经常需要处理压缩包。Linux下最常用的归档压缩工具是 `tar`。

### 常用tar命令
以下是使用tar进行压缩和解压的核心命令。

![](img/b43f32d7c242232d28f61ba726a11936_22.png)

**解压 `.tar.gz` 或 `.tgz` 文件**
```bash
tar -xzvf 文件名.tar.gz -C 目标目录
```
*   `-x`: 解压（extract）
*   `-z`: 处理gzip压缩（对于 `.tar.gz` 文件）
*   `-v`: 显示过程（verbose）
*   `-f`: 指定文件名（file）
*   `-C`: 解压到指定目录（Change）

![](img/b43f32d7c242232d28f61ba726a11936_24.png)

![](img/b43f32d7c242232d28f61ba726a11936_26.png)

**压缩文件或目录为 `.tar.gz`**
```bash
tar -czvf 压缩包名.tar.gz 要压缩的文件或目录
```
*   `-c`: 创建压缩包（create）

![](img/b43f32d7c242232d28f61ba726a11936_28.png)

**其他压缩格式**
*   解压 `.tar.bz2` 文件：`tar -xjvf 文件名.tar.bz2`
*   解压 `.zip` 文件：`unzip 文件名.zip`

---

## 源码编译安装 🛠️

对于追求高度定制化、可控性和性能优化的场景（常见于中小型互联网公司），源码编译安装是首选方式。

![](img/b43f32d7c242232d28f61ba726a11936_30.png)

源码安装通常包含三个标准步骤：
1.  **配置（预编译）**：检查系统环境，生成编译配置文件 `Makefile`。
2.  **编译**：将源代码翻译成二进制可执行文件。
3.  **安装**：将编译好的文件复制到系统指定目录。

![](img/b43f32d7c242232d28f61ba726a11936_32.png)

![](img/b43f32d7c242232d28f61ba726a11936_34.png)

### 源码安装实战示例（以Nginx为例）
以下是使用源码安装一个软件的典型流程。

**1. 下载源码包**
```bash
wget -c http://nginx.org/download/nginx-1.18.0.tar.gz
```
`-c` 参数支持断点续传。

![](img/b43f32d7c242232d28f61ba726a11936_36.png)

![](img/b43f32d7c242232d28f61ba726a11936_38.png)

**2. 解压并进入目录**
```bash
tar -xzvf nginx-1.18.0.tar.gz
cd nginx-1.18.0
```

![](img/b43f32d7c242232d28f61ba726a11936_40.png)

![](img/b43f32d7c242232d28f61ba726a11936_42.png)

**3. 执行配置脚本（预编译）**
这是最关键的一步，用于检查依赖和设定安装参数。
```bash
./configure --prefix=/usr/local/nginx --user=nginx --group=nginx
```
*   `--prefix=/usr/local/nginx`: 指定安装目录。
*   `--user=nginx --group=nginx`: 指定运行该软件的系统用户和组。

如果此步骤报错，通常会明确提示缺少哪个库（如 `PCRE library`），你需要使用YUM安装对应的 **开发包**（`-devel`）来解决。
```bash
yum install -y pcre-devel openssl-devel
```
安装依赖后，重新执行 `./configure` 命令。

**4. 编译与安装**
使用 `make` 命令进行编译，`make install` 进行安装。可以合并执行以提升速度（`-j` 参数指定并行编译的CPU核心数）。
```bash
make -j 4 && make install
```
`&&` 表示前一条命令成功后才执行后一条。

![](img/b43f32d7c242232d28f61ba726a11936_44.png)

**5. 启动与验证**
源码安装的软件，其启动脚本通常位于安装目录的 `sbin/` 下。
```bash
/usr/local/nginx/sbin/nginx        # 启动Nginx
ps aux | grep nginx                # 查看进程
netstat -lntp | grep 80            # 查看80端口是否监听
```

![](img/b43f32d7c242232d28f61ba726a11936_46.png)

![](img/b43f32d7c242232d28f61ba726a11936_48.png)

---

![](img/b43f32d7c242232d28f61ba726a11936_50.png)

## YUM软件仓库管理 🚀

![](img/b43f32d7c242232d28f61ba726a11936_52.png)

RPM解决了单个软件包的安装，但依赖管理依然繁琐。YUM（Yellowdog Updater Modified）是建立在RPM之上的高级包管理器，它通过维护一个**仓库**（Repository）来自动解决软件依赖问题。

![](img/b43f32d7c242232d28f61ba726a11936_54.png)

你可以把YUM仓库理解为一个存放了大量RPM包及其元数据（如依赖关系）的服务器。YUM客户端可以从仓库中搜索、下载并自动安装软件及其所有依赖。

![](img/b43f32d7c242232d28f61ba726a11936_56.png)

### 常用YUM命令
以下是使用YUM管理软件的核心命令。

![](img/b43f32d7c242232d28f61ba726a11936_58.png)

![](img/b43f32d7c242232d28f61ba726a11936_60.png)

![](img/b43f32d7c242232d28f61ba726a11936_62.png)

**安装软件**
```bash
yum install -y 软件名
```
`-y` 参数表示自动确认安装，避免交互提问。

![](img/b43f32d7c242232d28f61ba726a11936_64.png)

**搜索软件**
```bash
yum search 关键词          # 在仓库中搜索软件
yum provides */命令名      # 查找某个命令由哪个软件包提供
```

![](img/b43f32d7c242232d28f61ba726a11936_66.png)

![](img/b43f32d7c242232d28f61ba726a11936_68.png)

**卸载软件**
```bash
yum remove -y 软件名
```

![](img/b43f32d7c242232d28f61ba726a11936_70.png)

![](img/b43f32d7c242232d28f61ba726a11936_72.png)

**列出软件**
```bash
yum list installed        # 列出所有已安装的软件
yum list available        # 列出仓库中所有可用的软件
yum grouplist             # 列出软件组（如“开发工具”组）
yum groupinstall -y "开发工具" # 安装整个软件组
```

![](img/b43f32d7c242232d28f61ba726a11936_74.png)

![](img/b43f32d7c242232d28f61ba726a11936_75.png)

![](img/b43f32d7c242232d28f61ba726a11936_77.png)

**清理缓存**
```bash
yum clean all            # 清理所有YUM缓存
yum makecache            # 重建元数据缓存，加快搜索速度
```

![](img/b43f32d7c242232d28f61ba726a11936_79.png)

![](img/b43f32d7c242232d28f61ba726a11936_81.png)

### 配置YUM仓库源
系统默认的YUM源可能速度较慢或软件不全。我们可以配置更快的国内镜像源（如阿里云、清华源）或自定义本地源。

![](img/b43f32d7c242232d28f61ba726a11936_83.png)

![](img/b43f32d7c242232d28f61ba726a11936_85.png)

![](img/b43f32d7c242232d28f61ba726a11936_87.png)

![](img/b43f32d7c242232d28f61ba726a11936_89.png)

仓库配置文件位于 `/etc/yum.repos.d/` 目录，以 `.repo` 结尾。

![](img/b43f32d7c242232d28f61ba726a11936_91.png)

![](img/b43f32d7c242232d28f61ba726a11936_93.png)

**一个本地仓库配置示例** (`local.repo`)：
```ini
[Local-Base]             # 仓库ID，唯一标识
name=Local Repository    # 仓库描述名称
baseurl=file:///mnt      # 仓库路径，file://表示本地文件系统
enabled=1                # 启用此仓库
gpgcheck=0               # 不检查GPG密钥（用于本地或信任的源）
```
配置完成后，运行 `yum clean all && yum makecache` 即可使用新源。

![](img/b43f32d7c242232d28f61ba726a11936_95.png)

![](img/b43f32d7c242232d28f61ba726a11936_97.png)

### 搭建本地YUM仓库服务器
在企业内网，可以搭建自己的YUM仓库服务器，统一分发和管理软件包。

![](img/b43f32d7c242232d28f61ba726a11936_99.png)

![](img/b43f32d7c242232d28f61ba726a11936_101.png)

**基本步骤：**
1.  将收集好的RPM包放入一个目录，例如 `/var/www/html/yum/`。
2.  在该目录下生成仓库元数据：
    ```bash
    yum install -y createrepo
    createrepo /var/www/html/yum/
    ```
3.  使用Web服务器（如Nginx/Apache）将此目录发布出去。
4.  客户端机器配置 `baseurl=http://仓库服务器IP/yum/` 即可使用此内网源。

![](img/b43f32d7c242232d28f61ba726a11936_103.png)

![](img/b43f32d7c242232d28f61ba726a11936_105.png)

![](img/b43f32d7c242232d28f61ba726a11936_107.png)

![](img/b43f32d7c242232d28f61ba726a11936_108.png)

![](img/b43f32d7c242232d28f61ba726a11936_110.png)

---

![](img/b43f32d7c242232d28f61ba726a11936_112.png)

## 总结

本节课中我们一起学习了Linux下多种软件包管理方式：
*   **RPM**：底层的二进制包管理工具，需手动处理依赖，常见于对稳定性要求极高的封闭环境。
*   **源码编译**：提供最大的灵活性和控制力，可以深度定制软件，是互联网公司常用的部署方式。
*   **YUM**：高级的包管理工具，自动解决依赖，极大提升了运维效率，是红帽系Linux最常用的软件安装方式。
*   **压缩工具**：掌握了 `tar` 等工具，用于处理软件源码包和日常文件归档。

![](img/b43f32d7c242232d28f61ba726a11936_114.png)

理解这些工具的特点和适用场景，能够帮助你在未来的运维工作中，根据实际需求选择最合适的软件部署策略。