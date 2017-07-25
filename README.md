# 关于RISC-V你所需要知道的一切

当人们在谈论RISC-V的时候，实际在谈些什么？本书尝试告诉您答案。

本书计划采用众包的方式完成，欢迎RISC-V的爱好者贡献自己的力量，以推动RISC-V在中国的普及，同时共同学习进步。

[![](/assets/import.png)](https://creativecommons.org/licenses/by-nc-sa/3.0/cn/)

本作品采用[知识共享署名-非商业性使用-相同方式共享 3.0 中国大陆许可协议](https://creativecommons.org/licenses/by-nc-sa/3.0/cn/)进行许可。

**待讨论大纲（以下是囿于我的见识列出的大纲草稿，有很多不完善地方，大家多讨论）：**

# 第一章 RISC-V产生的时代背景

## 1 计算机体系结构和处理器微结构

- 区分计算机体系结构和微结构
  - 体系结构(computer architecture)定义的是硬件和软件的接口，并没有指定实现。RISC-V即定义的体系结构。
> leishangwen:<br>
> 建议从ISA的产生背景开始讲，再讲RISC的产生，这样就讲解了ISA的作用，以及RISC的优势
>
> wsong83:<br>
> 个人不太明这部分建议。说实话我也不知道ISA产生的背景是什么？RISC为何就产生了呢？现代指令集怎么就长这种样子了呢？
> 关于RISC的优势，其实RISC和CISC现在都不存粹了。RISC中的instruction-fusion, compressed instruction, multi-cycle instruction等等都是跟CISC学的。CISC的micro-op显然又是学RISC的。
> 无论从执行速度，面积，代码密度，离开具体应用环境都很难说谁好谁坏。
> 再说，ISA其实也不是唯一体系结构。比如说stream processor，就不执行普通意义上的指令。
> 这个问题其实很复杂，只有折中的看才是正确的吧。

  - 微结构(processor microarchitecture)则描述的是如何设计一个处理器来符合一个体系结构。体系结构并不定义微结构。

- 指令级架构(instruction set architecture, ISA)
  - ISA定义的内容：指令编码，内存模型，IO模型，逻辑寄存器数量和功能，控制寄存器，特权级别。
  - 一条指令都包含什么： 指令功能，操作对象(立即数，普通寄存器，特殊寄存器，内存), 环境变量(标志位，特权级别)，指令编码。
  - 指令的功能分类：逻辑指令，数学指令，控制指令，内存指令，IO指令，特殊指令。
- 处理器微结构
  - 流水线
  - 多发乱序流水线
  - 多线程流水线
  - 多核
  - 特殊流水线：VLWI, vector, GPU
- 体系结构与微结构的互相影响
  - 指令功能：RISC和CISC。
  RISC的每条指令功能复杂度基本一致，执行时间基本一致，编码长度基本一致，流水线控制简单，指令调度简单，代码密度相对CISC较小。
  CISC的指令功能复杂度不一，执行时间长短不一，编码长度也不一样。直接运行导致流水线控制复杂调度困难，一般动态拆做类似RISC的微指令执行。代码密度相对RISC较高。
  - 操作数：单指令寄存器个数。从栈寻址到多寄存器寻址在代码密度，流水线调度，执行时间上的考虑。
  - 操作数：内存寻址方式。RISC的内存寻址方式单一，调度简单，可做单独流水线，但指令密度高。CISC调度方式复杂，流水线设计复杂，指令密度高。
  - 环境变量： 条件执行对流水线的影响。（第二章具体讲）
  - 指令编码：RISC编码和SISC编码，解码难度，取址难度，分支预测难度等等。（第二章具体讲RISC-V的编码设计）
  - 编译器：
    编译器是指令集和高级语言的接口。
    语言的抽象等级越高，ISA的使用就更加受限：ABI，systemcall，context switching。
    通用逻辑寄存器的功能异化，数量对编译器的影响。
  - 操作系统：硬件资源的管理者，特权软件。
    操作系统需要ISA的支持：控制寄存器，特权指令，内存模型，IO模型等等，hyervisor模式。
    （更具体的，比如对VM，安全，上下文切换效率，中断和异常的定义等等，都留到第二章）

> wsong83:<br>
> 第一节介绍和ISA息息相关，但是又不是专属于RISC-V的内容。和ISA离得较远的内容，比如面向对象和函数函数式语言，JIT，解释编译等等现在都被去掉了。
> 和RISC-V设计直接相关的内容留到第二章具体讲，但是这一节自成系统的把相关内存大致的介绍一遍。
> 目的在于简历概念之间的联系，第二章的时候就不觉得一个概念冒出来很突兀了。

## 2 现有指令集 (leishangwen)
- CISC指令集的代表：x86和x86-64。
- RISC指令集的代表：ARM(ARMv7,AArch32,AArch64,thumb, thumb2)
- 其他的指令集：MIPS, PowerPC, SPARC

## 3 硬件开发的变迁 (wsong83)
介绍从最开始的晶体管，到CMOS，到基于标准单元的版定制流程，自动综合和布局布线，物理综合，仿真，前仿和后仿，LVS和formal verification，最终到SystemVerilog的verification特性和HLS的出现。这张的目的是给不理解硬件设计的读者入个门。后面讲到Rocket的某些硬件优化的时候会有好处。

## 4 开源运动
这有三个方面：开源软件，发生的原因和意义，现在的广泛使用，现在的问题：License的斗争，开发缓慢的问题，分支严重的问题，patent的问题，难以商业化不挣钱的问题。
> leishangwen:<br>
> 是不是应该多讲讲软件开源的好处和成功之处，linux的成功是不是也是由于其是开源的？<br>
> 目的是借鉴软件开源的成功经验，从而预期硬件开源的未来
>
> wsong83:<br>
> 啊，抱歉，我的习惯，上来先看哪有问题。当然要讲开源软件的好处和成功。
> Linux的成功得助于开源，也是机缘所致吧。

开源硬件，早已出现，OpenRISC，为何不成功：开发人员少群众基础不够，项目多数未完成，完成的也缺少验证，难以流片验证，缺少开源工具链，模拟设计难以开源，开源设计难以流片的各种实际因素。当然也说现在已有的成果，开源模拟器，简单的开源逻辑综合器，开源的硬件设计等等。
> leishangwen:<br>
> 建议在这里介绍一下开源硬件（主要是开源处理器）的现状
>
> wsong83:<br>
> OpenRISC就是接受面最广的开源处理器了吧？个人理解，其现状就是不太成功。的确会说一说。

指令及开源：指令集本身对性能影响其实不大，但是一个指令集所附带的生态环境非常重要。选用一个指令集其实是选择了一个生态环境，为这个生态环境付费。开源指令集的主要目的其实是提供一个开源的生态环境，包括ISA, 编译器，操作系统，软件库等等。



# 第二章 RISC-V

这一章就讲RISC-V指令集

## 1 RISC-V的历史
介绍UCB, MIPS, RISC-V的出现，开源，基金会的建立，它的目的和意义。

> wsong83:<br>
> - 关于UCB的历史，可以看看[RISC-V Geneology](https://www2.eecs.berkeley.edu/Pubs/TechRpts/2016/EECS-2016-6.pdf)

## 2 RISC-V的基本设计原理
介绍RV64I和RV32I的基本情况，重点介绍设计原理，可扩展的方式，面向硬件设计的编码方式，面向简单流水线设计的指令选择（不用条件执行，不用多周期指令）。

## 3 RISC-V特权指令设计
为何选择使用CSR，CSR的设计细节，不同特权级的定义，特权级之间的跳转，delegation的使用。（这里可以说的惊人细节太多了）

## 4 内存模型
介绍什么是内存模型，virtual memory的设计，和RISC-V的内存模型（这一部分快定义好了）

## 5 RISC-V的压缩指令
为什要有压缩指令及，怎么设计的，性能数据。结合压缩指令及讨论instruction fusion。简单说说压缩指令对硬件的影响，比如指令地址unalign，fetch级的动态取指速度，节省I$等等。

## 6 RISC-V的扩展指令集

AMO和LR/SC的设计意图（原子操作和关键区支持）。M/D/F/E等等已经可以用的扩展，和计划中的扩展。

## 7 Spike

Spike是RISC-V的instruction set simulator (ISS)，也是独立于实现的标准参照。
应该讲一讲Spike的使用和其背后的原理。

## 8 RISC-V的软件生态
介绍围绕RISC-V开发的编译器、移植的操作系统等软件的情况。

## 9 RISC-V在产业界与学术界的现状
概述基于RISC-V指令集的处理器、SoC，并进行简单地汇总统计。


# 第三章 Rocket-Chip概述

简单介绍Rocket-chip最初设计的由来，几次流片经历，作为RV64G的主要硬件实现，最终作为freechipsproject脱离UCB。
> leishangwen:<br>
> 建议加上Rocket-Chip与Rocket、Boom、Freedom、sodor的关系介绍，主要是共享的一些类
>
> wsong83:<br>
> Rocket-chip的基本结构会在3.2节介绍。那里会说明Rocket和Boom是Rocket-chip可选的两种处理器。现在Rocket-chip好像已经支持同时有Rocket和Boom的核。
> 这里可以提及Rocket和Boom是Rocket-chip支持的两种处理器实现，Freedom和lowRISC是基于Rocket-chip的两个SoC扩展。
> Rocket-chip好像和sodor没有关系，除了它们都是Chisel写的？

## 1 Chisel和FIRRTL

简单说明Chisel和FIRRTL的功能。简单介绍Chisel相对SystemVerilog等HDL的优势，同时区分Chisel和HLS。
举简单例子来说明：

- Chisel可以被用来实现简单电路。
- Chisel在泛型上的优势。
- Chisel在面向对象上的优势。
- Chisel用LazyModule来实现编译时代码生成机制。
> leishangwen:<br>
> 建议加上Chisel的基本语法、基本概念的讲解，以及基本的试验步骤、实验环境，这样便于读者理解所举的例子
>
> wsong83:<br>
> 我的想法是弱化Chisel。引用一个美国初创公司老板的话：Chisel就是UCB的一帮顶级中的顶级alpha male互相之间炫耀谁能用更抽象更难懂的代码写硬件的产物。虽然这说的有点极端，说的其实也不是Chisel本身，而是使用于Rocket设计的diplomacy和cake pattern。但是有一点他说的很中肯，想学会Chisel去改Rocket-chip这个目标对于大多数人来说是不可实现的。如果我们在这里介绍Chisel的目的是讲如何去改Rocket，这个目标基本是达不到的。我们能做到的，最多也就是点一点思路，然后指条路（列出参考文档和代码的地址）。
>
> 为了这种目标，上面的例子其实就是循序渐进的介绍用Chisel的不同抽象级别。到了最终的LazyModule实际上已经很接近cake pattern了。
> 我在思考是否加上一些介绍diplomacy和cake pattern的内容，但应该不会用Chisel源码去讲，那个基本不能被理解。
>
> 回到你的建议，实现简单电路的部分就是在介绍最基础的Chisel语法。我们可以给出UCB Chisel Tutorial的地址，那个是最官方，也是会被持续更新的。
> 实验步骤和实验环境，就涉及到大量的scala/sbt编译环境，包配置内容。作为我自己，如果不去实现一些小例子，自己都不会去了解。我并不十分赞同将这些。如果代码的例子选的好，读者并不需要自己去跑就能建立感性认识。我们能达到的目标估计也只是感性认识。


## 2 Rocket-Chip的基本结构

画几个结构图来形式化的表示Rocket-chip的内部链接。同时叙述Rocket-chip的可配置功能。
同时在这里介绍devicetree的自动生成。
> leishangwen:<br>
> 建议加上对Rocket-Chip的src/main/scala目录下每个文件夹的功能介绍
>
> wsong83:<br>
> 我是反对用源码结构，特别是具体到文件夹的这种方式来介绍Rocket-chip的。原因有以下几点：
> - 给出了好像大家可以自己去尝试修改源码并成功的错觉。如我上面说的，这基本是不可能达到的。
> - 实际上说这些也没什么意义。Rocket的源代码就没有稳定过。就这一个月，Rocket的源码目录结构就已经经历了两次大变动，这里说的是整个文件夹被改名或者拆散的改动。Rocket的源码在这三年就不断地经历着这种改动。我作为lowRISC的维护者，就被这种频繁的改动拖累甚苦。
> - lowRISC和Rocket-chip自己的文档都已经给出了源码目录的大概功能，而且会随着源码改动自动更新，我们为何还要在书里面自己去讲呢。

## 3. TileLink片上总线

TileLink总线的channel名称和功能，支持的报文类型和传输协议等等。

## 4. Rocket-chip的仿真和测试

这里主要是讲仿真的基本方法和Rocket-chip/Chisel提供的几种测试方法：require check, assertion, bus-monitor, unitest, groundtest, isa regression.
这一章并不具体讲测试和仿真的基本步骤。

# 第四章 Rocket处理器

## 1. Rocket处理器的基本结构
包括特点（比如：无阻塞存储等）、性能参数、流水线图、结构框图、接口图、地址空间分配等，此外，简单介绍src/main/scala/rocket目录下各个文件的作用。

> wsong83:<br>
> 不太明白Rocket的性能参数的具体含义。可配置参数？
>
> 空间地址分配并不由Rocket处理器决定，而由SoC平台决定，在生成DeviceTree的时候自动计算并分配。在3.2节讲DeviceTree的时候会讲一些。我建议具体空间分配的例子挪到Freedom的部分用SiFive的配置举例。lowRISC和SiFive在地址空间上是不匹配的。
>
> 反对一一介绍src/main/scala/rocket里的文件。
> 不过，可以考虑在讲Rocket的主要模块的时候，点出对应的文件，有一些文件的文件名已经相对稳定。

## 2. Rocket处理器的运行原理场景分析
尽量结合源码或者以图示的方式分析一些典型场景的实现，如果源码太复杂，可以只通过画图等方式直观展示。包括：第一条指令的取指地址是哪里、指令解码的实现、分支预测的实现、PTW的步骤、DCache的无阻塞存储、custom指令的执行、自定义指令的添加等。

> wsong83:<br>
> 这一节应该被拆分。
> 首先我反对用代码去讲，原因一样，讲不清楚，Chisel的语法坑太多。另外讲清楚了读者也改不了，我们不可能把整个代码都给讲了。结构图加语言叙述是更合理的方式。
> 上电步骤，比如第一条指令在哪里，bootloader跳转，这些是由SoC平台决定的。RISC-V标准并不决定这些，Rocket核只是设定了一个启动地址的常数，然后具体过程取决于BootROM的存在与否和其地址。实际上，RISC-V标准建议启动地址为0或者是内存高位，就是因为不同SoC会使用不同的地址配置。
> 相关内容应该挪到Freedom用SiFive的定义去讲。
>
> 分支预测，PTW，nonblocking D$等等都属于第一节的内容。
> custom指令部分，没想好，这部分设计内容很多。但基本是和指令集相关的？这里介绍一下RoCC接口是不是就可以了？添加指令其实是和编译器相关的。也应该放到RISC-V那一章。
> 我倒是支持说如果在这里举一个硬件的例子说明RoCC硬件怎么设计。（我自己没有相关经验）

## 3. Rocket多核处理器的基本结构
介绍内容包括特点、性能参数、结构图、缓存一致性、片上互联等。

> wsong83: <br>
> 所有涉及多核的部分都属于Rocket-chip。多核也可以是异质核，比如Rocket和Boom。
> L1 D$需要讲一下对probe的支持，这个的确是和coherence相关的。但是缓存一致性，片上互联这都是3.2或者3.3的内容。
>
> 性能参数？

## 4. Rocket处理器的仿真测试
实验环境搭建，仿真步骤，实验步骤，对编译过程中的显示的设备配置树（Device Configure Tree）、address map等信息可以进行解释，但是重点放在对试验结果的解读，使用spike-asm对结果进行整理，通过这个结果分析Rocket的执行过程，比如：前几百个时钟周期在干什么，什么时刻才跳转到用户写的程序开始执行等。

> wsong83: <br>
> 这整个都应该挪到Freedom。离开SoC，处理器本身无法仿真。
> BTW: 执行用户程序之前，基本就是硬件cache的初始化，和启动部分的machine-mode初始代码。大部分其实是软件的事情，rocket就是傻瓜的执行。

这是之前讨论的内容：<br>
<br>

大概分三个部分：

第一部分

> 使用Rocket-core的开发板并没有统一吧，除非你是专指SiFive的板子。作为lowRISC，我们支持Nexys4-DDR。UCB也曾经支持过多款Zynq的板子。
> 我的主要意见是，讲这些对读者有多少帮助。我觉得我们应该避免把自己知道的东西去堆成一本书，而应该想想些什么东西是对以后有用的，可以作为知识去指导实践的。
> 有些东西讲细了，就变成英文文档的翻译。一旦别人的文档变了，书就没用了。
>
> leishangwen:<br>
> 同意宋同学的意见，多讲特性、结构、内部工作机理、参数化使用等，包括第七章的boom也是如此<br>
> 但是对于freedom系列，还是要讲试验，因为freedom e310已经流片了，是一款相对稳定的产品了<br>

第二部分介绍RoCC的相关知识，包括机制、使用方法、测试等（lsl之前积累过这方面的资料）；

> 要小心。ROCC接口本身就从没稳定过。
>
> leishangwen:<br>
> 在邮件里经常有RoCC的邮件，所以我觉得这个的关注度还是蛮高的，当然目前RoCC只能在特定的版本使用，<br>
> 在写的时候要注明<br>

第三部分介绍多核配置、仿真、下载试验的步骤。

> 一样，有点觉得这些不该是一本书的内容。给出链接指向SiFive的正确网页即可。
> 当然，有些逻辑上的东西是有意义的，比如说什么结构是可以配置的，为什么这么配。
> 举个例子子，陪scratch pad和nonblocking L1 D$就是互相矛盾的。
>
> leishangwen:<br>
> 关于多核，可以结合试验，介绍一下多核的结构，包括缓存一致性等关键技术<br>
> 另外，我还是觉得这里的多核试验可以保留，理由有两点<br>
> 1、有一点试验，可操作的内容，对于读者来说不显得太过于枯燥<br>
> 2、多核配置试验可以结合试验，分析内部工作流程，比如：结合试验，分析缓存一致性的实现原理等。<br>

# 第五章 BOOM处理器

## 1. BOOM处理器的基本结构
包括特点（比如：乱序、超标量）、性能参数、结构框图、接口图、地址空间分配等，src/main/scala目录下的文件的基本作用。

## 2. BOOM处理器的运行原理场景分析
尽量结合源码或者以图示的方式分析一些典型场景的实现，如果源码太复杂，可以只通过画图等方式直观展示。包括：寄存器重命名、重排序、推测发射等。

# 第六章 SiFive公司的Freedom系列

## 1. Freedom系列现状

介绍SiFive公司及其Freedom系列的基本情况（lsl之前积累过这方面的资料）；

第二部分介绍Freedom E310的性能参数、结构框图、接口图、各个文件的作用，仿真步骤，实验环境搭建，实验步骤（包括下载到开发板的步骤）；

第三部分分析Freedom E310的移植到Altera的步骤、调试环境建设（lsl之前积累过这方面的资料）。

第四部分从代码分析调试原理（lsl之前积累过这方面的资料）；

第五部分介绍HiFive的基本情况，实验环境搭建，实验步骤，试验例程分析，ucosII的移植。

# 第七章 LowRISC (wsong83)

包括LowRISC的产生原因、发展历史、相比Rocket的主要改进点，仿真步骤，实验环境搭建，实验步骤（包括下载到开发板的步骤）；
> leishangwen：<br>
> lowrisc这一部分需要宋同学多费心了，建议多讲特性、结构、内部工作机理、参数化使用等<br>
> 另外，建议结合试验或者仿真，可以更好地讲解lowrisc的一些特性，比如tagged memory等<br>
> 所以，这里的试验不是简单地试验，不是按照网站的说明，一步步操作，而是要结合内容自己设计试验<br>

# 第八章 RISCV-mini源代码分析

分析RISCV-mini或者sodo、zscala的源代码，以小见大，而且因为简单便于大家理解（lsl之前积累过sodor源代码的相关的资料）

