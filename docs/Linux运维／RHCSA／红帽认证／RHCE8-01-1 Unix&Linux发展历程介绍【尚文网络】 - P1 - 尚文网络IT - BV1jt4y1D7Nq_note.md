# Linux运维：RHCSA：红帽认证：RHCE8-01-1：Unix & Linux发展历程介绍 🐧

![](img/3429b1f146fe10a637f95cc5dc6d1fdb_1.png)

在本节课中，我们将学习Unix与Linux操作系统的发展历史。我们将了解Unix的起源、Linux的诞生过程，以及它们如何共同构成了现代开源操作系统的基础。

![](img/3429b1f146fe10a637f95cc5dc6d1fdb_3.png)

---

## Unix与Linux的发展历史

上一节我们介绍了课程的整体框架，本节中我们来看看Unix与Linux的具体发展历程。

![](img/3429b1f146fe10a637f95cc5dc6d1fdb_5.png)

Unix操作系统诞生于1969年，由贝尔实验室、麻省理工学院和通用电气公司合作开发。它凭借良好的稳定性、多用户和多任务特性而受到青睐。然而，其最初的版本存在可移植性差的问题。

![](img/3429b1f146fe10a637f95cc5dc6d1fdb_7.png)

![](img/3429b1f146fe10a637f95cc5dc6d1fdb_9.png)

![](img/3429b1f146fe10a637f95cc5dc6d1fdb_10.png)

1973年，C语言诞生。使用C语言对Unix进行重写和优化后，其可移植性问题得到了显著改善。

![](img/3429b1f146fe10a637f95cc5dc6d1fdb_12.png)

![](img/3429b1f146fe10a637f95cc5dc6d1fdb_14.png)

1978年，Unix系统出现了两个主要分支：
*   **BSD系列**：由加州大学伯克利分校开创。
*   **System V系列**：由AT&T（贝尔实验室的母公司）商业发布。

![](img/3429b1f146fe10a637f95cc5dc6d1fdb_16.png)

![](img/3429b1f146fe10a637f95cc5dc6d1fdb_17.png)

1979年，AT&T宣布对Unix产品拥有版权，并停止了对Unix源代码的开放。这促使了自由软件运动的发展。

以下是基于这两个分支的一些知名操作系统：
*   **基于System V**：IBM AIX， HP-UX， Oracle Solaris。
*   **基于BSD**：macOS， FreeBSD， OpenBSD。

---

## GNU项目与自由软件运动

![](img/3429b1f146fe10a637f95cc5dc6d1fdb_19.png)

![](img/3429b1f146fe10a637f95cc5dc6d1fdb_21.png)

![](img/3429b1f146fe10a637f95cc5dc6d1fdb_23.png)

上一节我们了解了Unix的商业化分支，本节中我们来看看与之对应的自由软件运动。

1983年，理查德·斯托曼发起GNU项目，目标是建立一个完全自由、开放且类Unix的操作系统。他创立了自由软件基金会并制定了GNU通用公共许可证。

![](img/3429b1f146fe10a637f95cc5dc6d1fdb_25.png)

![](img/3429b1f146fe10a637f95cc5dc6d1fdb_27.png)

GNU项目开发了许多重要的软件组件，例如：
*   **GCC**：GNU编译器套件，用于编译程序。
*   **Emacs/Vim**：文本编辑器。
*   **Bash**：命令行解释器。

![](img/3429b1f146fe10a637f95cc5dc6d1fdb_29.png)

然而，GNU项目始终缺少一个核心部件——操作系统内核。

![](img/3429b1f146fe10a637f95cc5dc6d1fdb_31.png)

![](img/3429b1f146fe10a637f95cc5dc6d1fdb_33.png)

---

## Linux内核的诞生

上一节我们介绍了GNU项目提供的软件生态，本节中我们来看看构成完整操作系统所缺失的核心部分。

1991年，芬兰大学生林纳斯·托瓦兹在互联网上发布了Linux内核的0.02版本。他参照了POSIX标准进行开发，以确保与现有Unix软件的兼容性。

![](img/3429b1f146fe10a637f95cc5dc6d1fdb_35.png)

1994年，Linux 1.0版本正式发布，并采用了GPL许可证。林纳斯选择企鹅作为Linux的吉祥物，象征着自由与开放。

一个完整的Linux操作系统，实际上是 **GNU项目的软件** 与 **Linux内核** 的结合。其关系可以用一个简单的公式表示：

**完整的Linux操作系统 = GNU软件 + Linux内核**

![](img/3429b1f146fe10a637f95cc5dc6d1fdb_37.png)

---

![](img/3429b1f146fe10a637f95cc5dc6d1fdb_38.png)

![](img/3429b1f146fe10a637f95cc5dc6d1fdb_40.png)

## 核心概念与要点回顾

![](img/3429b1f146fe10a637f95cc5dc6d1fdb_42.png)

![](img/3429b1f146fe10a637f95cc5dc6d1fdb_43.png)

以下是本节课需要掌握的核心概念：

![](img/3429b1f146fe10a637f95cc5dc6d1fdb_45.png)

![](img/3429b1f146fe10a637f95cc5dc6d1fdb_47.png)

1.  **Unix的编程语言**：Unix系统后来主要使用 **C语言** 编写。
2.  **Unix的两大分支**：**BSD** 与 **System V**。
3.  **GNU项目的目标**：建立一个自由、开放的类Unix操作系统。
4.  **林纳斯·托瓦兹的贡献**：他开发并发布了 **Linux内核**。
5.  **Linux内核官方网站**：获取内核源代码的官方站点是 **https://www.kernel.org**。

![](img/3429b1f146fe10a637f95cc5dc6d1fdb_49.png)

---

![](img/3429b1f146fe10a637f95cc5dc6d1fdb_51.png)

本节课中，我们一起学习了从Unix诞生到Linux成型的关键历史脉络。我们了解了Unix的商业化与自由软件运动的兴起，并最终看到GNU软件与Linux内核的结合，形成了今天广泛使用的Linux操作系统。理解这段历史有助于我们更好地认识开源文化的本质和Linux系统的构成。