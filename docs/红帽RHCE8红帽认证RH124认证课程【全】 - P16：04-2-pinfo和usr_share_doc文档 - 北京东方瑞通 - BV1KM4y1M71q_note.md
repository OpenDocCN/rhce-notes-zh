# 红帽RHCE8认证课程：04-2：pinfo和usr_share_doc文档

![](img/c2326b77e96a061def407cc75253cffd_1.png)

![](img/c2326b77e96a061def407cc75253cffd_3.png)

在本节课中，我们将要学习Linux系统中除`man`手册外的另外两种重要文档资源：`pinfo`命令查看的GNU Info文档，以及存放在`/usr/share/doc`目录下的软件包文档。我们还将了解用于收集系统信息的`sosreport`命令。

![](img/c2326b77e96a061def407cc75253cffd_5.png)

![](img/c2326b77e96a061def407cc75253cffd_6.png)

![](img/c2326b77e96a061def407cc75253cffd_7.png)

上一节我们介绍了`man`手册的使用，本节中我们来看看另外两种获取帮助和文档的方式。

![](img/c2326b77e96a061def407cc75253cffd_9.png)

![](img/c2326b77e96a061def407cc75253cffd_10.png)

![](img/c2326b77e96a061def407cc75253cffd_11.png)

## 使用pinfo查看Info文档

![](img/c2326b77e96a061def407cc75253cffd_13.png)

在Linux系统中，许多遵循GPL协议的软件由GNU项目开发，并配有专门的在线文档系统，称为GNU Info。在RHEL系统中，例如`coreutils`、`grep`等工具都包含Info文档。对于这类文档，我们使用`pinfo`命令来查看。

![](img/c2326b77e96a061def407cc75253cffd_15.png)

![](img/c2326b77e96a061def407cc75253cffd_16.png)

![](img/c2326b77e96a061def407cc75253cffd_17.png)

![](img/c2326b77e96a061def407cc75253cffd_18.png)

![](img/c2326b77e96a061def407cc75253cffd_19.png)

![](img/c2326b77e96a061def407cc75253cffd_20.png)

`pinfo`命令与`man`类似，但文档的组织形式不同。`man`手册将内容完整显示在一页，而Info文档采用树形结构组织，通过超链接的方式逐级深入查看具体内容。

![](img/c2326b77e96a061def407cc75253cffd_22.png)

![](img/c2326b77e96a061def407cc75253cffd_24.png)

以下是`pinfo`的基本使用方法：
```bash
pinfo
```
执行该命令会进入Info文档的顶层目录。

![](img/c2326b77e96a061def407cc75253cffd_25.png)

![](img/c2326b77e96a061def407cc75253cffd_27.png)

![](img/c2326b77e96a061def407cc75253cffd_29.png)

![](img/c2326b77e96a061def407cc75253cffd_30.png)

在`pinfo`界面中，您会看到一个树形菜单。当前所在的层级称为“节点”。您可以使用方向键在链接间移动，按**回车键**进入选中的子节点。

![](img/c2326b77e96a061def407cc75253cffd_32.png)

![](img/c2326b77e96a061def407cc75253cffd_34.png)

与浏览网页类似，Info文档支持在节点间导航。以下是关键操作：

![](img/c2326b77e96a061def407cc75253cffd_36.png)

![](img/c2326b77e96a061def407cc75253cffd_38.png)

*   **`u`**：返回上一级节点。
*   **`d`**：直接返回到最顶层的目录节点。
*   **`q`**：退出`pinfo`。
*   **`/`**：在文档内进行搜索。输入关键词后按回车开始搜索。

![](img/c2326b77e96a061def407cc75253cffd_40.png)

![](img/c2326b77e96a061def407cc75253cffd_42.png)

需要注意的是，Info文档的搜索体验与`man`略有不同。在`man`中按`n`键可查找下一个匹配项，而在`pinfo`中，每次搜索后如需继续查找，通常需要重新输入`/`和关键词。

![](img/c2326b77e96a061def407cc75253cffd_44.png)

虽然`pinfo`提供了结构化的文档浏览方式，但在实际工作中，`man`手册因其一次性展示全部内容的特性而更为常用。`pinfo`可作为特定场景下的补充查阅工具。

![](img/c2326b77e96a061def407cc75253cffd_46.png)

![](img/c2326b77e96a061def407cc75253cffd_48.png)

## 查阅/usr/share/doc文档

除了`man`和`pinfo`，系统在安装软件包时，还会在`/usr/share/doc`目录下存放相关的说明文档。

![](img/c2326b77e96a061def407cc75253cffd_50.png)

![](img/c2326b77e96a061def407cc75253cffd_52.png)

这些文档可能包括示例配置文件、版权声明、变更日志、安全说明等。当您无法在`man`或`pinfo`中找到所需信息时，可以尝试在此目录下查找。

以下是进入该目录并查看内容的方法：
```bash
ls /usr/share/doc
```
您会看到许多以软件包命名的子目录。

![](img/c2326b77e96a061def407cc75253cffd_54.png)

对于系统管理员而言，`/usr/share/doc`目录中的`example`（示例）文件非常实用。例如，在配置`vsftpd` FTP服务器时，可以参考该目录下的示例配置文件，根据实际需求进行修改，这能有效提高配置效率和准确性。

![](img/c2326b77e96a061def407cc75253cffd_56.png)

![](img/c2326b77e96a061def407cc75253cffd_58.png)

![](img/c2326b77e96a061def407cc75253cffd_60.png)

![](img/c2326b77e96a061def407cc75253cffd_62.png)

## 使用sosreport收集系统信息

![](img/c2326b77e96a061def407cc75253cffd_64.png)

![](img/c2326b77e96a061def407cc75253cffd_66.png)

![](img/c2326b77e96a061def407cc75253cffd_68.png)

![](img/c2326b77e96a061def407cc75253cffd_69.png)

`sosreport`是一个用于收集系统配置和诊断信息的工具。当需要向技术支持人员（例如红帽支持工程师）报告问题时，他们通常会要求您运行此命令，以便获取完整的系统环境快照。

![](img/c2326b77e96a061def407cc75253cffd_71.png)

![](img/c2326b77e96a061def407cc75253cffd_73.png)

![](img/c2326b77e96a061def407cc75253cffd_75.png)

该命令会收集系统日志、配置文件、已安装软件包列表、运行状态等大量信息，并将其打包压缩成一个归档文件。

![](img/c2326b77e96a061def407cc75253cffd_77.png)

![](img/c2326b77e96a061def407cc75253cffd_78.png)

![](img/c2326b77e96a061def407cc75253cffd_80.png)

以root身份运行`sosreport`命令：
```bash
sosreport
```
命令执行后，根据提示操作，最终会生成一个`.tar.xz`格式的压缩包文件，并显示其存放路径和MD5校验值。您可以将此文件发送给技术支持人员。对方可以通过比对MD5值来验证文件在传输过程中是否完好无损。

![](img/c2326b77e96a061def407cc75253cffd_82.png)

![](img/c2326b77e96a061def407cc75253cffd_84.png)

![](img/c2326b77e96a061def407cc75253cffd_86.png)

## 实验指导

![](img/c2326b77e96a061def407cc75253cffd_88.png)

![](img/c2326b77e96a061def407cc75253cffd_90.png)

![](img/c2326b77e96a061def407cc75253cffd_92.png)

![](img/c2326b77e96a061def407cc75253cffd_94.png)

本节实验旨在练习使用`pinfo`查阅Info文档。

![](img/c2326b77e96a061def407cc75253cffd_96.png)

![](img/c2326b77e96a061def407cc75253cffd_98.png)

![](img/c2326b77e96a061def407cc75253cffd_99.png)

1.  在workstation主机上，以student用户身份执行以下命令，启动Info帮助系统：
    ```bash
    pinfo
    ```
2.  在顶层目录中，使用方向键找到并进入`Common options`主题。
3.  在`Common options`主题内，使用`Page Up`和`Page Down`键浏览内容，了解长选项是否可以简写。
4.  查找关于`--`（双连字符）作为命令参数含义的说明。它通常用于表示命令选项的结束，其后内容均视为命令参数，有助于Shell正确解析复杂的命令。
5.  尝试使用`u`键返回上一级节点，再次按`u`可返回到顶级主题节点。
6.  使用`/`键搜索`coreutils`关键词，并查看搜索结果。
7.  退出`pinfo`后，您也可以直接使用`pinfo coreutils`命令来查看特定主题的Info文档。
8.  按照实验步骤，继续探索`Disk usage`等子主题，完成整个练习。

![](img/c2326b77e96a061def407cc75253cffd_100.png)

![](img/c2326b77e96a061def407cc75253cffd_101.png)

![](img/c2326b77e96a061def407cc75253cffd_103.png)

![](img/c2326b77e96a061def407cc75253cffd_104.png)

![](img/c2326b77e96a061def407cc75253cffd_105.png)

本节课中我们一起学习了三种重要的Linux文档资源：用于查看GNU项目结构化文档的`pinfo`命令、存放软件包额外文档的`/usr/share/doc`目录，以及用于生成系统诊断报告的`sosreport`工具。掌握这些工具将帮助您更高效地获取系统信息、查找配置帮助并进行问题诊断。