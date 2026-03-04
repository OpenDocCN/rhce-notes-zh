# Unix&Linux快速入门超详细教程：P1：01-1 Unix&Linux发展历程介绍 🐧

![](img/f6277423ab479b1589c0865cdbfedcb5_1.png)

在本节课中，我们将要学习Unix与Linux操作系统的发展历史，了解它们是如何诞生、演变，并最终成为现代计算领域重要基石的。

![](img/f6277423ab479b1589c0865cdbfedcb5_3.png)

## Unix的诞生与演变

上一节我们介绍了课程的整体结构，本节中我们来看看Unix操作系统的起源。

![](img/f6277423ab479b1589c0865cdbfedcb5_5.png)

Unix操作系统诞生于1969年，由贝尔实验室、麻省理工学院和通用电气公司合作开发。它凭借**良好的稳定性**、**多用户**和**多任务**特性而受到青睐。然而，其最初的版本存在一个主要缺点：**可移植性较差**，这意味着它难以在不同的计算机硬件平台上运行。

![](img/f6277423ab479b1589c0865cdbfedcb5_7.png)

![](img/f6277423ab479b1589c0865cdbfedcb5_9.png)

![](img/f6277423ab479b1589c0865cdbfedcb5_10.png)

1973年，C语言诞生。开发者使用C语言对Unix进行了重写和优化，显著改善了其可移植性问题。

![](img/f6277423ab479b1589c0865cdbfedcb5_12.png)

![](img/f6277423ab479b1589c0865cdbfedcb5_14.png)

到了1978年，Unix的发展出现了两个主要分支：
*   **BSD系列**：由加州大学伯克利分校的Bill Joy等人开创。
*   **System V系列**：由AT&T（贝尔实验室的母公司）主导开发。

![](img/f6277423ab479b1589c0865cdbfedcb5_16.png)

![](img/f6277423ab479b1589c0865cdbfedcb5_17.png)

1979年，AT&T宣布了对Unix的所有权，并停止了其源代码的开放，这推动了Unix的商业化进程。

以下是基于这两个分支的一些知名商业Unix系统：
*   **基于System V的版本**：IBM AIX， HP-UX， Sun Solaris。
*   **基于BSD的版本**：macOS， FreeBSD， OpenBSD。

## GNU项目与自由软件运动

![](img/f6277423ab479b1589c0865cdbfedcb5_19.png)

![](img/f6277423ab479b1589c0865cdbfedcb5_21.png)

![](img/f6277423ab479b1589c0865cdbfedcb5_23.png)

上一节我们了解了Unix的商业化分支，本节中我们来看看与之对应的自由软件运动。

![](img/f6277423ab479b1589c0865cdbfedcb5_25.png)

由于Unix走向商业化，1983年，Richard Stallman发起了**GNU项目**（GNU‘s Not Unix的递归缩写）。其目标是建立一个**完全自由、开放**的类Unix操作系统。为此，他创立了**自由软件基金会（FSF）** 并制定了**GNU通用公共许可证（GPL）**。

![](img/f6277423ab479b1589c0865cdbfedcb5_27.png)

![](img/f6277423ab479b1589c0865cdbfedcb5_29.png)

GNU项目开发了大量高质量的软件，但它们共同缺少一个核心部分：操作系统内核。

![](img/f6277423ab479b1589c0865cdbfedcb5_31.png)

![](img/f6277423ab479b1589c0865cdbfedcb5_33.png)

以下是GNU项目开发的部分关键软件：
*   **GCC**： GNU编译器集合，用于编译程序。
*   **Emacs**： 强大的文本编辑器。
*   **Vim**： 另一个高效的文本编辑器。
*   **Apache**： 著名的网页服务器软件。

## Linux内核的诞生

上一节我们提到GNU项目缺少一个内核，本节中我们来看看这个缺失的部分是如何被填补的。

![](img/f6277423ab479b1589c0865cdbfedcb5_35.png)

1991年，芬兰赫尔辛基大学的学生**Linus Torvalds**在互联网上发布了**Linux内核（Kernel）** 的0.02版本。他开发这个内核的初衷是为了能在个人电脑上运行一个类Unix系统。

Linux内核遵循了**POSIX标准**，这保证了它与现有Unix软件的兼容性。1994年，Linux 1.0版本正式发布，并采用了GPL许可证。

Linus Torvalds将一只名为**Tux**的企鹅选作Linux的官方标志，象征着自由与活力。

![](img/f6277423ab479b1589c0865cdbfedcb5_37.png)

![](img/f6277423ab479b1589c0865cdbfedcb5_38.png)

> **核心概念**：一个完整的Linux操作系统 = **Linux内核** + **GNU软件**及其他自由软件。
> 用公式表示即：`完整的Linux系统 = Linux Kernel + GNU Project Software`

![](img/f6277423ab479b1589c0865cdbfedcb5_40.png)

## 关键回顾与总结

![](img/f6277423ab479b1589c0865cdbfedcb5_42.png)

![](img/f6277423ab479b1589c0865cdbfedcb5_43.png)

![](img/f6277423ab479b1589c0865cdbfedcb5_45.png)

本节课中我们一起学习了Unix与Linux波澜壮阔的发展历程。

![](img/f6277423ab479b1589c0865cdbfedcb5_47.png)

![](img/f6277423ab479b1589c0865cdbfedcb5_49.png)

我们来回顾几个关键问题：
1.  Unix最初使用什么语言重写，从而大大提升了可移植性？**C语言**。
2.  Unix的两大主要分支是什么？**BSD**和**System V**。
3.  我们日常能接触到的基于BSD的系统是什么？**苹果的macOS**。
4.  GNU项目的目标是什么？建立一个**自由、开放**的类Unix操作系统。
5.  Linus Torvalds最初发布的是什么？**Linux操作系统内核（Kernel）**。
6.  一个完整的Linux系统包含什么？**Linux内核**和**GNU等自由软件**。

![](img/f6277423ab479b1589c0865cdbfedcb5_51.png)

理解这段历史有助于我们认识到Linux系统**开放、协作、自由**的精神内核，这也是它在服务器、嵌入式设备乃至个人计算领域获得巨大成功的文化基础。