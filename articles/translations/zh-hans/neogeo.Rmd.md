---
long_title: Neo Geo架构
short_title: Neo Geo架构
name: Neo Geo
long_name: Neo Geo
subtitle: 家里的街机
date: 2024-06-19
release_date: 1990-04-26
generation: 4
aliases:
  - /writings/consoles/neogeo-private-330
cover: neogeo
top_tabs:
  Model:
    caption: Neo Geo 'AES'<br>日版 1990年4月26日发售，美版和欧版1991年7月1日发售
    file: international
  Motherboard:
    caption: 显示的是修订版 'NEO-AES3-6'。<br>我使用我购买的型号拍摄了这张照片。 另外，之前的主人重新排列了一些电容以改善视频信号。
  Diagram: {}
---

## 简介

毕竟其直接来自街机世界，Neo Geo 毫无疑问是第四代游戏机中最昂贵的硬件。 这就引出了一个问题：它的性能如何？与其他机型相比又如何呢？

在这一篇目中，我们将探讨一家公司（SNK）不设预算限制并推出一款旨在取悦街机业主和富裕家庭的产品的结果。

## {.supporting-imagery}

### 型号和变种

Neo Geo 在设计上非常独特，因为它同时面向两个市场：街机和家用主机。 因此，SNK 创造了一种单一架构，并以**两种**不同的形式销售，每种都针对特定的市场：

- **Multi Video System**  或 'MVS' 是专为街机中心设计的形式。 它的输入输出遵循 'JAMMA' 规范（尽管有一些偏差[@general-jamma]），这使得它能够轻松地与街机柜集成。 此外，它的专用主板还提供了额外的电路来处理投币功能。 无论如何，在其生命周期中，SNK 最终推出了大量修订版，其中一些可以甚至可以同时容纳多达六个游戏卡带[@general-mvs_boards]。
- **Advanced Entertainment System** 或 'AES'是针对家用机市场的简化设计。 虽然体积更小且减少了输入输出端口，但并没有牺牲其基本功能。

我应该也提一下 **Neo Geo CD**，它是 AES 版本的后继者，如其名字所示，明显采用了 CD-ROM 作为游戏分发的媒介。 就像之前的形态一样，这个型号也有多个修订版。

无论如何，对于这次分析，我将重点放在 AES 版本上，因为这是我拥有的版本。 不过，在涉及到额外功能时，我也会适当地提及 MVS。

## 中央处理器 (CPU)

该系统采用**双处理器**配置，由一个 **Motorola 68000** 和一个 **Zilog Z80** CPU 组成，这两个处理器都是 80 年代电子产品的标志性产品。 68000 负责执行**主程序**（也就是游戏），而 Z80 则被分配用于**声音管理** [@cpu-z80]。 你可能会回想起 [MD](mega-drive-genesis) 中类似的分工角色，但相似之处仅此而已，因为芯片的变体和布局有所不同。

![Neo Geo 内部使用的 Motorola 68HC000 芯片，这款芯片是由东芝作为第二来源提供的。](cpu/photo.jpg)

举个例子，Neo Geo 的 68000 运行频率为 **12 MHz**（比 MD 的快 4 MHz）。 此外，安装的具体芯片是 **68HC000**，这是一种通过 CMOS 门组成的变体（与之前效率较低的 NMOS 工艺相比）。

如果你想了解 68000 和 Z80 的内部构造，我在 [MD](mega-drive-genesis#cpu)  和 [SMS](master-system#cpu)  的文章中已经进行了详细的分析。 因此，在对 Neo Geo 的研究中，我将专注于它们的新用途。

### 多种支持芯片

Neo Geo 与同时代的任何家用游戏机之间最显著的区别在于，在设计前者时，SNK 并不受普通家庭预算限制的束缚。 因此，主板上集成了大量的控制器，这些不仅消除了许多 CPU 的瓶颈问题，还扩展了它们的功能。

列出所有的芯片令人费解，而更让人困惑的是 SNK 在不同版本中重新设计了芯片组，要么是将多个芯片合并为一个，要么是将单个芯片拆分成多个。 所以，现在让我告诉你，主板上包含以下几类“加速器”：

- **68k 控制器**：用于无缝切换向量表。 我会在接下来的段落中详细解释这一部分。
- **Z80 控制器**：为了解决内存寻址限制的问题。
- **I/O 仲裁器**：用于分配所有输入输出操作，从而节省处理周期。
- **视频显示控制器**：用于执行所有图形操作。 这部分将在专门的“图形”章节中讨论。
- **机柜控制器**：仅限街机版（即“MVS”）。 这类控制器处理投币口，提供记分板，并进行系统诊断。
- **家用机控制器**：同样仅见于家用版（即“AES”）。 这一组里包括一个视频编码器，可以生成符合电视标准的视频信号。

从现在起，我将在整篇文章中使用这些名称来指代各类芯片组。 在某些情况下，我还会提及特定的芯片名称以强调其重要性。

### 复杂的 I/O 处理

让我来介绍 68000 的一项之前未曾提及的高级功能：**向量化中断表**。 早期的 CPU 如 [6502](nes#cpu) 和 [Z80](master-system#cpu) 在处理 I/O 通信方面有着受限的设计。 每当外部设备需要 CPU 的关注时，它必须发送一个中断请求，而 CPU 则需要运行软件例程来处理这个请求。 在请求过程中传递的信息非常有限，这意味着 CPU 需要额外的周期来确定是谁触发了中断以及为什么中断 CPU。

公平地说，像 Z80 这样的 CPU 提供了额外的电路来允许外围设备告诉 CPU 下一步执行哪条指令。 然而，这种方法并未被广泛采用。

68000 改进了这项技术，将中断视为**异常**处理。 当外围设备中断 CPU[@cpu-68k_int] 时，它们需要自我标识。 然后，CPU 使用这个标识符组成一个内存地址，该地址指向一个定制的软件例程。 总的来说，这使得程序员能够高效地处理 I/O 通信，因为他们现在可以在一个名为**自动向量表**的内存区域中编目他们专用的中断处理程序，而 CPU 将自动执行每个处理程序。

Neo Geo 利用了这种设计，并增加了一个名为 **NEO-E0** 的控制器芯片。 基本上，Neo Geo 的操作系统和游戏都会实现一个专用的异常表，这是因为操作系统的表侧重于硬件接口，而游戏的表则在正常执行期间使用。 然而，68000 只能意识到一个表。 因此，NEO-E0 提供了自动的存储器区段切换功能（[bank switching](nes#going-beyond-existing-capabilities)），确保 68000 始终可以从正确的表中读取数据。

### 进程间通信

在具有双处理器架构的系统中，两个 CPU 必须有一种方式进行通信。 在这个游戏机中，SNK 通过使用**I/O 仲裁器** 芯片实现了进程间通信（IPC）。 该仲裁器暴露了一个单一的 8 位寄存器，68000 可以写入此寄存器 [@cpu-68k_z80]。

![68000和Z80之间的IPC通道表示。](cpu/ipc.png)

一旦寄存器被更新，仲裁器会向 Z80 发送一个不可屏蔽中断。 之后，Z80 可以读取该值（映射到一个[端口](master-system#accessing-the-rest-of-the-components)），并据此做出反应。 这包括将数据写回到该寄存器，以便 68000 能够接收到新的数据。 但是，出于某种原因，68000 不会被中断（因此它需要进行某种形式的轮询）。

具体的 I/O 仲裁器芯片在不同的主板版本中有所变化。 最初使用的是 'PRO-B0' 芯片，后来被 'NEO-C1' 所取代。

### 防扫兴机制

说到中断，街机机柜所不能承受的就是需要不断的维护。

对于因为软件错误而导致游戏突然冻结的情况我们并不陌生。 解决方法也并非秘密：只需重启电源，再次启动游戏即可。 现在想象一下，如果这种情况发生在街机机柜上。 除非机柜的所有者恰好就在附近，否则故障的机柜只会带来不断的金钱损失。

Neo Geo —— 继承了投币机的要求 —— 通过捆绑一个**看门狗程序** 提供了“自动修复”过程。 这个程序位于 I/O 仲裁器芯片中[@cpu-watchdog]。

![街机机柜无法承受持续的服务需求。 它们已经忙于吸引玩家。](games/dragon.png){.pixel}

系统的工作原理如下：游戏必须定期向看门狗通知其存在（通过写入特定的寄存器）。 不幸的是，如果在一定时间内看门狗没有收到心跳信号，它就会假设程序已冻结，并进而重置系统。

多亏有了这个功能，MVS 的所有者不必时刻留意他们的机柜。 与此同时，那些能够负担得起 AES 系统的幸运孩子们即使游戏崩溃也不必离开他们的豪华沙发。

不仅如此，MVS 主板还配备了一个额外的微控制器 **NEO-F0** 。 这个微控制器驱动着**投币锁定** 机制，防止用户在故障的系统上浪费硬币[@cpu-coin_lockout]，除此之外还有其他功能。

### 可用内存

内存分布在主板和游戏卡带中（顺便说一句，游戏卡带的尺寸相当大）。 请记住这是一个昂贵的系统，因此内存带宽和容量是首要考虑的因素。

对于通用内存来说，68000 可用的 **RAM 为 64 KB** 。 与此同时，Z80 获得了 **2 KB** 的内存。

MVS 版本还另外包含了 **64 KB** 的内存。 这部分内存连接到了电池上，用于存储游戏得分，因此对于一般用途来说并无用处。

有趣的是，这个系统并没有采用任何形式的**直接内存访问**（DMA）来加速内存传输。 我认为考虑到专用总线的数量，这可能并不是必需的。

## 图形

Neo Geo 是基于图块的图形技术的巅峰之作，其丰富的色彩可与[基于帧缓冲的系统](sega-saturn#graphics) 相媲美（后者直到四年之后才变得经济实惠）。 尽管如此，在效果方面，[SFC](super-nintendo#graphics) 用较少的资源实现了更多的功能。

### 设计

图形子系统围绕着典型的**视频显示控制器**（VDC）模型构建，但这次它被大量的组件所包围。 结果就是这款主机能够显示大量的精灵图像，数量之多以至于标志性的[背景层](nes#tab-5-2-background-layer) 不再那么重要。 这当然也说得通，因为背景原本就是为了应对有限数量的精灵图像而设计的。

:::{.subfigures .tabs-nested .pixel}

![合金弹头 3 (2000).](games/metalslug3.png){.active title="合金弹头"}

![大联盟高尔夫球 (1996).](games/turfmast.png){title="高尔夫"}

![Neo甩尾王 (1996).](games/neodrift.png){title="甩尾王"}

![龙虎之拳3: 斗士之路(1996).](games/artoffight.png){title="龙虎之拳"}

![野外飞盘 (1993).](games/windjammers.png){title="野外飞盘"}

Neo Geo 游戏示例。

:::

话虽如此，该主机以符合 NTSC 和 PAL 信号的标准广播每一帧画面（选择哪种信号取决于地区）。 在这种情况下，画面的尺寸为 **320 x 224 像素**（对于 NTSC）或 **320 x 256 像素**（对于 PAL）[@graphics-frame_size]。

#### 芯片组

负责在电视上绘制图像的组件群随着新版本的发布而不断发展。 多年来，SNK 在制造其集成电路时交替使用了 NEC 和富士通。

:::{.subfigures .side-by-side max_subfigures=1}

!['LSPC-A2'芯片。](vdc/lspca2.jpg){.toleft}

!['NEO-B1'芯片。](vdc/neob1.jpg){.toright}

在我的主板版本中，这两个组件组成了视频显示处理器（VDC）。 二者均由富士通生产。

:::

为了便于解释，我们先从这套芯片中最显著的部分开始描述：

- **Motorola 68000**： 您已经知道它是主CPU。 它的主要作用是填充视频RAM和调色板RAM，并（通过一组公开的寄存器）来设置VDC。
- **视频显示处理器（VDC）**：根据存储在多个内存芯片上的信息，它生成扫描线并将其发送到“调色板RAM”，后者将生成所需的彩色像素。
  - VDC的物理外观会根据主板型号的不同而变化。 例如，在最初的版本中包含了三个芯片（“LSPC-A0”、“NEO-B0”和“NEO-C0”），而后来的版本则使用了“LSPC-A2”和“NEO-B1”的组合。
- **视频DAC**：将从“调色板RAM”接收到的颜色流转换成视频信号。

如果之前的解释过于复杂，请不要担心。 接下来的章节将会更详细地解释这些内容。 现在，让我们继续分析这些芯片处理的信息。

### 硬件组织

图形数据分布在**两个独立的板卡** 上。 第一张是**游戏机的主板** ，另一张位于**游戏卡带**中。 您可能会想起[NES/Famicom](nes#cartridgegame-data). 中采用的类似模型。

![图像子系统的内存架构](vdc/content.png)

在主板区域，我们发现有：

- **68 KB的VRAM**：存储精灵属性和图块引用。
  - 物理上，VRAM被分为**4 KB 的“快速”VRAM** 和**64 KB的“慢速”VRAM** 。 每一种都通过专用总线连接，并且具有截然不同的延迟。 这决定了CPU在更新其内容后必须等待的周期数。
- **四个行缓冲区**：存储由VDC渲染的扫描线。 它们仅编码必须与调色板RAM中的其他数据结合使用的图块数据。
- **16 KB的调色板RAM**：存储用于图形的色彩调色板。 这个区块还负责向视频DAC发送像素颜色。
- **64 KB的'L0 ROM'**：恰好VDC可以**缩小精灵图**。 为了实现这一点，“L0 ROM”中存储了查找表，告诉VDC根据请求的缩放值应该渲染哪些扫描线。 [@graphics-l0_rom].

游戏卡带中的相应板卡被称为**CHA板**，包含以下部分：

- **多个C ROM芯片**：存储精灵图层的图块。
- **一个S ROM**：存储“固定”图层的图块（我将在下一节中解释这个图层）。

### 构造帧

既然您已经大致了解了最重要的组件，现在让我简要概述一下图形子系统如何将数据转换成图像的过程。 换句话说，它是如何渲染一帧的：

1. CPU用精灵图层和固定图层所需的引用填充VRAM。
2. 根据VRAM中的信息，VDC从C ROMs和S ROM中获取图形，并将它们存储在线缓冲区中。
3. 在获取过程中，VDC使用线缓冲区中的信息来锁定调色板RAM。 这样后者会将颜色像素数据发送给视频DAC。
4. 视频DAC输出视频信号到电视。

基于这些步骤，现在我将以《龙虎之拳3》为例给您一个详细的解释。

#### 图块 {.tabs .active}

![一些在CROM中发现的图块。](aof/tiles.jpg){.tab-float .pixel}

传统上被称为8 x 8像素位图，图块是构成第四代游戏机图形的基本元素。 由于其独特的属性，Neo Geo只绘制两种类型的图形：**固定图块**和**精灵图**。 因此，图块根据图形类型不同而分别进行编码和存储。 在这两种情况下，每个像素使用**4位**（换句话说，4bpp）进行编码，这意味着它可以选取最多**15种颜色加上透明度**。

就色彩组成而言，调色板RAM允许存储**256个色彩调色板**[@graphics-palettes]。 每个调色板包含十六种颜色，引用**16位RGB**值。 在所有可用的槽位中，有两个预留条目：一个是“参考颜色”，必须硬编码为$8000（纯黑色）；另一个是“背景颜色”，它编码了一个任意颜色，在没有图块的情况下显示。

CPU可以在任何时候更新调色板RAM，但它应该只在消隐期间这样做。 否则，屏幕上会出现雪花效果。

#### 固定平面 {.tab}

![和黑色背景一起的固定平面](aof/fixed_plane.png){.tab-float .pixel}

固定平面是一个**320 x 256像素**宽（40 x 32图块）的图层[@graphics-fixed_layer]，几乎占据了整个屏幕。 它的图块是完全静态的，并且只能访问16个色彩调色板。 实际上，这个图层用来显示“始终开启”的信息，类似于Game Boy的[窗口图层](game-boy#tab-3-3-window) 。

当在CRT屏幕上播放时，显示区域会更小。 因此，其安全区域为38 x 28图块宽。

固定图块是在VRAM的一个区域中声明的，称为**固定图块映射表**（Fix map）。 它以一个40 x 32的表格形式存在，每个条目对应屏幕上的一个位置。

S ROM在卡带中存储专用于固定图层的独有图块。 VDC最多只能寻址128 KB，但通过捆绑一个[映射器](nes#going-beyond-existing-capabilities)[@graphics-fix_bankswitching] 可以扩展这一限制。

#### 精灵图 {.tab}

![精灵图层，注意它是如何用于场景的所有区域的（不仅仅是两个角色）。](aof/sprites.png){.tab-float .pixel}

如您所知，精灵图是可以**自由移动的图块**。 然而，在Neo Geo中，用于精灵的图块异常地宽，达**16 x 16像素**。 考虑到这一点，VDC可以组合由**16 x 16像素**（1 x 1图块）到**16 x 512像素**（1 x 32图块）组成的精灵图。 之所以提供这种异常高的组合是因为任何精灵图也可以通过“连接”到前一个精灵图来进行**水平组合**（尽管它们仍然被计为单独的精灵图）。 总体来说，**每行扫描线上最多可以有96个精灵图**，**每帧最多可以有381个精灵图**[@graphics-sprites]，这个数量与竞争对手相比是非常大的。

就效果而言，精灵图可以**翻转**和/或**缩小**。 这些属性存储在VRAM的“快速”区块中，使得CPU可以在不产生显著延迟的情况下更新它们。

由于其复杂性，精灵图像在视讯内存（VRAM）中的四个区域进行编码，这些区域被称为**精灵图控制块**（SCB），每个区块存储以下属性[@graphics-beginners]：

- **SCB1**: 图块引用、色彩调色盘引用及X/Y轴翻转。
- **SCB2**: 水平和垂直缩小。
- **SCB3**: Y轴位置、垂直尺寸（以图块计算）以及精灵是否与前一个相连。
  - 精灵图相连会使第二个精灵图继承前者的属性，但不包括水平缩小。
- **SCB4**: X轴位置。

如您所见，出于实用考虑，最后三个区块存储在快速VRAM中，而第一个则存储在较慢的VRAM中[@graphics-vram]。

#### 结果 {.tab}

![显示在屏幕上的结果帧。](aof/result.png){.tab-float .pixel}

尽管[竞争对手](nes#constructing-the-frame) 以与CRT电子束同步渲染而闻名，Neo Geo却采用了一种**行缓冲渲染**系统。 对于后者，游戏机将渲染的扫描线存储在上述缓冲区中，而不是将它们直接发送到屏幕。 因为视频显示控制器（VDC）具备储存两条扫描线的记忆体容量，这使得VDC可以在显示一条扫描线的同时渲染另一条，从而允许游戏在画面正在**显示期间**更新图像（除了水平空白期[H-Blank](game-boy#tab-3-5-result) 和垂直空白期[H-Blank](game-boy#tab-3-5-result)）。

说实话，VDC仍然会同时访问VRAM，因此中央处理器（CPU）必须等待12到16个周期[@graphics-vram]（取决于操作）才能再次访问VRAM，否则下一个操作将被忽略（这也是使用两种VRAM变体的原因）。

#### 广播发送帧 {.tabs-close}

为了发送已渲染的扫描线，这一过程根据主机型号的不同（AES或MVS）而有所变化。

家用版增加了一个视频编码器来生成复合信号和RGB信号。 A/V接口与[SMS](master-system#video-output) 和[MD](mega-drive-genesis#video-output) 的类似，但不能互换。

MVS版则没有这样的设置，因为JAMMA协议将这项任务留给了街机柜的显示器。 所以主板发送的是原始RGB+同步信号。

## 音频

还记得我在文章开头提到的Z80 CPU吗？ 它仍然是你在许多其他游戏机和微型计算机上常见的**4 MHz** CPU，但现在它有了驱动一款非常多功能的声音芯片的特权：**Yamaha YM2610**。

![Yamaha YM2610搭配下方的YM3016。 输出音频需要这两个芯片共同工作。](audio/yamaha.jpg)

YM2610是来自日本知名制造商的另一款[FM合成器](mega-drive-genesis#tab-8-1-yamaha-ym2612) 。 它与[MD](mega-drive-genesis#audio) 中使用的[YM2612](mega-drive-genesis#tab-8-1-yamaha-ym2612) 非常相似，但不要被它的名字迷惑了，因为Neo Geo上的这个版本稍微更高端一些（尽管也有一些妥协）。

YM2610和YM2612都属于高端的“OPN”系列，这意味着Yamaha为每个FM通道提供了四个运算器。 现在，Neo Geo的芯片具有四个**FM通道**，相比之下比YM2612少了两个。 这就是它的不足之处。

FM通道配备了一个**低频振荡器**（LFO），用于调制运算器的幅度或频率。 LFO包含一组预定义的频率范围从3.98 Hz到72.2 Hz [@audio-fm]。

![合金弹头 2 (1998), 显示用于音乐的FM和ADPCM频道。](metalslug){.open-float video="true"}

尽管YM2610以其FM合成能力而闻名，但它还拥有一些额外的功能，这些功能对于音乐作曲家来说不会被忽视[@audio-app_manual2]：

- **软件控制的声音生成器**（SSG）：这是Yamaha对[可编程声音生成器](nes#audio)（PSG）的称呼。 本质上，该芯片内部集成了一个传统的Yamaha YM2149，可以生成**三个带有可选噪声的方波**。 它还配备了包络控制。
  - 原始的YM2149配备了一个名为“I/O端口”的接口，用于在CPU和其他组件之间传输数据[@audio-ym2149]。 由于现在这个接口已经不再相关，因此嵌入在YM2610中的变体并不包含它。
- 七个**ADPCM通道**：能够使用4位ADPCM格式播放**采样**。 这些通道分为两组：
  - ADPCM-A：由六个通道组成，它们提供**12位**的采样分辨率和大约**18.5 kHz**的采样频率。
  - ADPCM-B：这是剩余的一个通道，具有**16位**的分辨率和高达约**55.56 kHz**的采样频率（优于CD音质）。

{.close-float}

YM2610连接到卡带中的两个称为**V ROMs**的存储芯片。 这些芯片存储ADPCM采样，并且每组ADPCM通道都有一个对应的V ROM。

总的来说，音频子系统结合了第三代、第四代以及当时尚未发布的第五代游戏机的技术。 然而，与[CD介质](sega-saturn#the-compact-disc-cd) 不同，Neo Geo卡带只能存储有限的数据，因此ADPCM通道主要受限于**存储限制**。 无论如何，有些游戏巧妙地安排了ADPCM的使用，而将FM作为次要成分[@audio-dissection]。

最后，YM2610与另一个芯片**Yamaha YM3016**配合工作。 后者是一个专用的16位**数字模拟转换器**（DAC），它接收来自YM2610的数字信号输出并将其转换为模拟音频（扬声器可以理解）。

### 执行器

为了让音频芯片执行有意义的操作，Z80必须正确地对其进行控制。 因此，Z80运行一个存储在另一个单独的芯片**M1 ROM**中的程序。 这个芯片位于游戏卡带内，具体地说，在CHA板上。 Z80的程序通常被称为**声音驱动程序**，而且有许多不同的实现[@audio-drivers]。

![Zilog Z80, NEO-D0 的周围环境。](audio/z80.jpg)

同样地，Z80与一个专有的控制器**NEO-D0**配对，后者充当内存银行和I/O控制器。

## 操作系统

主板上有一个**128 KB的“系统”ROM**[@operating_system-bios_rom]，这相当于典型的**BIOS**，提供了以下功能：

- **引导加载程序**，用于初始化硬件、执行自检并启动引导动画。
- **配置界面**（仅限MVS型号）。
- 许多**系统例程**，用于抽象化硬件操作。

### 视觉服务

在打开游戏机后，用户会注意到一个称为“**Eyecatcher**”的启动画面[@operating_system-eyecatcher]。 它的代码存储在系统ROM中，但图形资源（如Neo Geo标志和其他标签）则从卡带的C ROM和S ROM中获取。

另外，P ROM的头部可能包含一个标志，指示系统ROM将启动画面程序委托给卡带[@operating_system-prom_header]。 这仅适用于AES版本[@graphics-beginners]，而后期的游戏采用了这种方式来显示不同的文本。

![由系统ROM实现的眼球捕捉画面。 它仍然依赖于卡带提供的图块。 官方实现显示了具有标志性的“Max 330 Mega”子标题，以此来展示Neo Geo卡带的理论存储容量。 后期的游戏通过使用标志，则显示为“Giga Power”。](system/eyecatcher.jpg){.toleft .pixel}

![Mvs系统还提供了一个测试菜单来配置机柜（例如调整日历、测试组件等）。](system/testmenu.jpg){.toright .pixel}

MVS系统还提供了一个**测试菜单**来配置机柜（例如调整日历、测试组件等）。 这个菜单通过翻转MVS上的一个DIP开关触发（终端用户无法访问）。 因此，其主板捆绑了一个S ROM，用于存储该菜单的固定

### 系统例程

除了视觉方面，系统ROM还提供了操作硬件的软件例程，这包括：

- 从控制器获取按键输入，并且在MVS的情况下还包括硬币投入口。
- 访问记忆卡。
- 初始化VRAM以便为精灵块和固定图块地图建立正确的结构。

有趣的是，系统例程反过来可能会调用游戏例程以继续某些操作。 这意味着游戏也需要负责实现某些调用[@operating_system-user_routine] [@operating_system-system_io]。 例如，系统ROM可能会要求游戏执行以下操作：

- 显示自定义的Eyecatcher动画（如果设置了所需标志）。
- 显示游戏启动动画。
- 开始游戏演示。 这是你在投入硬币之前看到的内容。
- 播放“投入硬币”的声音。
- 在投入足够的硬币后开始游戏。

如您所见，大部分低端I/O操作都委托给了系统ROM，而游戏则专注于提供其独特的内容。

## 游戏

首先，游戏是用**68k**和**Z80汇编语言**编写的。 直到下一代游戏机（随着[新CPU](playstation#tab-1-1-a-bit-of-history) 的到来）编程语言才会成为工具箱的一部分。

### 发行介质

遵循第四代游戏机的趋势，Neo Geo使用卡带作为其唯一的游戏媒介。

不过，我得说Neo Geo的卡带是我见过的家庭游戏机中最大的。 这是因为卡带内装载了大量的专用芯片。 与[SFC](super-nintendo), 不同，后者的游戏 [可以选择性地扩展](super-nintendo#beyond-convention) 游戏机的功能，而Neo Geo的游戏严格要求捆绑大量的电路。

![Neo Geo通用卡带\[@photography-amos\]。 请注意突出的两块电路板。](cartridge.png){.no-borders}

我在前面的部分已经提到了卡带系统的某些部分，但让我给你做一个快速总结，以便你能够更清楚地了解这个系统是如何工作的。

如你所知，Neo Geo的卡带由**两块电路板**组成。 第一块被称为**PROG板**，包含以下ROM芯片：

- **P ROM**：存储68000执行的程序。 它通过16位数据总线连接，最多可寻址**2MB**（更多容量需要使用映射器）。
- **V ROM**：存储由YM2610读取的ADPCM音频样本。

相比之下，第二块电路板就是前面提到的**CHA板**，其中嵌入了以下组件：

- **C ROM**和**S ROM**。 它们分别存储精灵图块和固定图块。
- 一个**M1 ROM**：存储Z80程序。 如你所知，Z80的寻址能力[非常有限](master-system#memory-available) （在这种情况下，上限是62KB）。 因此，M1 ROM的访问是通过一个专门的[映射器](nes#going-beyond-existing-capabilities)**NEO-ZMC**（位于卡带内）来接口的，它可以使得最多**512KB**的内存被寻址。 这可以通过在CHA板上添加额外的映射器来扩展，或者采用NEO-ZMC2（可以支持最多4MB）。

### 保存进度

作为家用与街机的混合体，SNK提供了一个基本的配件来追踪分数和进度：一种专有的**记忆卡**。

![记忆卡 \[@photography-amos\].](memorycard.png){.no-borders}

记忆卡适用于AES和MVS两种型号。 选项从**2KB**到**16KB**的电池支持SRAM不等[@games-memory_card]，但在所有情况下，电池都是不可更换的。

保存的数据是以64字节区块的形式分布在五个段落中的，而系统ROM提供了I/O例程来操作这些数据。

话虽如此，街机柜是否真的“准备好了”接受记忆卡…… 那就是另一回事了。

> 我是从杂志上了解到记忆卡的，但在我的城市里，官方的Neo Geo游戏柜非常罕见。 相反，我们有很多由Segasa提供的“Video Sonic”游戏柜。 有些甚至缺少玩游戏所需的所有按钮，更不用说记忆卡插槽了！
>
> \-- <cite>来自西班牙赫雷斯的自豪Neo Geo鉴赏家</cite>

## 反盗版与自制软件

无论是游戏机还是游戏本身都成为了未经授权方的目标，其中许多人最终生产了自己的盗版复制品[@anti_piracy-bootlegs]。 盗版硬件是由现成和逆向工程的芯片组合而成，目的是以更低的价格销售作为SNK硬件的替代品。

因此，一方面，SNK试图抵御这些盗版者。 另一方面，游戏工作室则在确保他们的代码仅能在正版游戏机和卡带中运行。

### 对抗盗版游戏机

SNK和游戏工作室都不希望其产品在盗版硬件上运行（无论是Neo Geo克隆机还是盗版卡带）。 因此，许多游戏内置了一些例程，用于检测主板上的特定区域（例如状态标志、实时时钟、看门狗等）。 以尝试发现来自克隆板的任何缺陷。[@anti_piracy-slot_checks]。 每当这些检测失败时，游戏就会显示警告信息并拒绝继续运行。

### 对抗盗版卡带

考虑到卡带内包含了大量的集成电路，游戏实施了许多创意措施来打击盗版。

从常见的技术开始讲起，系统ROM中包含了一部分被称为**安全码**的数据，在启动时会与卡带的P ROM进行逐一对比[@anti_piracy-security_code]。 只有当两个区域匹配时，引导加载程序才会继续执行。

此外，像《饿狼传说2》这样的游戏也会与其配套的'PRO-CT0'芯片（CHA板上的多路复用器）交互，以确保程序位于正版卡带中[@anti_piracy-fatal_fury]。 否则，它会认为程序已被克隆，并触发许多反盗版的**不愉快行为**（比如让对手无敌）。

后来，新版本的游戏还在C ROM中存储了**加密数据**，设计为可以无缝地被'NEO-CMC'（位于CHA板上）解密[@anti_piracy-neo_cmc]。 这个芯片包含了一系列非同寻常的异或函数，在图形数据到达VDC之前对其进行去混淆[@anti_piracy-mame_cmc]。 NEO-CMC还作为C ROM中的瓦片数据的多路复用器，允许制造商完全省略S ROM芯片的使用。

对于极端措施，像《合金弹头X》这样的游戏在其PROG板上捆绑了专门的集成电路，进一步增加了复杂性[@anti_piracy-progeop]。 这让我想起了任天堂的CIC系统，该系统需要持续的游戏芯片通信（尽管在目标和架构方面显著不同）。

总而言之，这说明反盗版斗争就像一场猫捉老鼠的游戏。 然而，在这种情况下，SNK试图让家用和街机的拥有者远离更便宜的仿冒品。

### 上市后的工具

随着这款游戏机生命周期的结束，一个新的阶段出现了：**自制软件**（运行自制软件和/或硬件）。 截至本文撰写之时（2024年6月），最常见的实践包括**硬件修改**、**固件替换**和**闪存卡**。

得益于其热情的社区，有许多在线资源可用来教授如何修正和/或修改MVS和AES硬件。 这些资源包括将MVS机型转换为可以在街机柜外工作的模式（所谓的“**家用化**”），而其他资源则专注于解决某些机型的不足之处。 例如，我拥有的AES机型对其视频编码器进行了旁路处理，提高了RGB输出的质量。

在固件方面，已经出现了一些发展，如'UNIVERSE BIOS'/UniBIOS。 这相当于一个**定制固件**，旨在替换原始的系统ROM。 背后的技术是基于MVS的系统ROM进行了修改，以提供额外的功能[@anti_piracy-unibios]：

- 在'MVS'模式和'AES'模式之间切换。 这对AES系统非常有用，因为游戏有硬编码的投币数/生命值数量，切换到MVS模式可以任意增加这些数值。
- 更改地区设置。 一些游戏根据所运行的地区会受到审查。
- 激活作弊功能。

最后，为了在这台游戏机上运行任意代码，商店正在销售第三方闪存卡，这些闪存卡可以重新编程以复制任何过去的CHA和PROG板。 由于它们的复杂性，这些闪存卡并不便宜，但这是如今Neo Geo用户可用的另一种产品。

## 这就是全部了，伙计们。

![我的 Neo Geo AES. 出于好奇，我购买了我的Neo Geo Aes，卖家提供了一整套设备，包括一个闪存卡和两个手柄。 大约半年后，这成了我撰写关于它的文章的一个好借口。](myneogeo.webp)

有趣的是，最后一款Neo Geo游戏是在2004年发布的，距离其首次亮相几乎过去了14年。 这对于一款家用/街机混合体来说是一个相当了不起的成就，我希望这篇分析能够帮助你从技术角度理解这些成果。

对我来说，写这篇文章很吸引人，因为我之前花了大量篇幅讨论[任天堂3DS](nintendo-3ds),，这有助于我回到网站2019年初创时采用的那种“经典”的写作结构。

考虑接下来的文章，我还有两条路线可以选择：一是继续 [任天堂3DS](nintendo-3ds) 之后的现代硬件时间线；二是继续介绍另一款2D游戏机：Pippin。 后者将有助于我引入PowerPC CPU，而现代硬件将使我能够讨论ARMv7和ARMv8。 让我们拭目以待吧！

下次见！\
Rodrigo