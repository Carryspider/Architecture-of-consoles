---
short_title: 超级任天堂的架构
long_title: 超级任天堂的架构
name: 超级任天堂
subtitle: 带有惊人特性的老旧硬件
date: 2019-06-28
release_date: 1990-11-21
generation: 4
cover: snes
published: true
top_tabs:
  Models:
    - 
      title: "国际版（International）"
      file: international
      caption: "SNES（欧版）或者叫SFC（日版）<br>1990年11月21日于日本，1992年4月11日于欧洲发售"
      active: true
    - 
      title: "美版"
      file: american
      caption: "SNES<br>1991年8月13日于美国发售"
  Motherboard:
    caption: "显示修订版本号“SNS-RGB-CPU-01”<br>早期的版本将声音子系统作为子板连接，后来的版本则将两个PPU统一起来"
    bib_source: photography-yaca
    extension: "jpg"
  Diagram:
    caption: "总线“A”与“B“是地址总线，8位数据总线沿着地址总线”B“的路径."
#Historical
aliases:
  - /projects/consoles/super-nintendo/
  - /writings/consoles/snes/
---

## 快速入门

任天堂似乎设法在不使用昂贵的现成组件的情况下带来了下一代图形和声音。 但有一个问题：新控制台在设计时也**考虑到了可扩展性**。 在 CPU 发展速度快于光速的世界中，任天堂最终依靠游戏卡带使其游戏机大放异彩。

## {.supporting-imagery}

## 中央处理器 (CPU)

超级任天堂选择的处理器很特别。 与捆绑成熟的68000的[竞争产品](mega-drive-genesis#cpu)不同，SNES 的芯片与其前身相比并没有根本性的突破。 概括地说，NES 采用的是[经过改进的6502 CPU](nes#cpu)，它是70年代末和80年代初计算机中备受推崇的部件。 现在，为了给新的十年（90年代）铺平道路，任天堂采用了更保守（也更便宜）的解决方案：**WDC 65C816**，这是6502的16 位扩展。

### 6502的现代化

65C816 CPU源自Western Design Center，特别是来自Bill Mensch，他曾是6502开发团队（在 MOS）和6800开发团队（在摩托罗拉）的成员。 1978年，在离开MOS一年后，Mensch创立了Western Design Center (WDC)，这是一家半导体公司，专门生产MOS 6502的克隆产品，并对其进行了极具吸引力的改进（如CMOS设计、额外的操作码、电路修复、新的寻址模式等）

![苹果Apple IIGS上的WDC 65C816芯片](photos/65816.webp)

有一天，苹果公司找到 WDC，要求其设计一种向后兼容的6502变体，可以处理更大量的数据。 于是，WDC 65C816 CPU 于1983年面世。 奇怪的是，苹果在开发使用这种新CPU的计算机过程中经历了许多挫折，直到三年后才发布了Apple IIGS。

![最终，Apple IIGS和超级任天堂成为65C816 CPU的仅有的主要采用者](photos/wdcstack.webp)

与此同时，任天堂与理光（Ricoh）及其为NES提供的芯片组保持着良好的合作关系。 我没有找到确切的文件来概述理光与WDC之间的关系，但我可以确定的是，在某个时间点，WDC同意将其65C816设计授权给理光[@cpu-interview]。 因此，理光根据超级任天堂的新要求对其进行了定制，并将其命名为**理光5A22**，专门供应给任天堂。

### 新的CPU

如前所述，该控制台的主处理器是**理光5A22**，是65C816的超集。

![理光5A22芯片，任天堂称之为“S-CPU”](photos/s-cpu.webp)

与具有Apple II软件的向后兼容能力的Apple IIGS不同，超级任天堂**与NES游戏不兼容**。 平心而论，从处理器的选择来看，SNES最初计划兼容NES游戏的可能性微乎其微，谁知道呢。

接下来，CPU采用了**可变时钟速度**，在进行寄存器操作时最高可达**3.58 MHz**，而在访问最慢的总线（即串行/手柄端口）时则低至**1.79 MHz**。

现在，为了正确理解5A22的功能，我们必须首先了解65C816的功能：

- **65816 ISA**： 65C816首次推出的16位指令。 它以 6502 ISA 为基础，但不执行某些NES游戏使用的未注明指令[@cpu-unoffopcodes]。
  - 指令的大小可在1字节（8位）和4字节（32位）之间变化，这取决于内存地址的引用方式（又称“寻址模式”）[@cpu-isaref]。
  - [被破坏的BCD模式](nes#scrapped-functions)又开始**工作**了（我猜这是_正当_授权的结果）。
- **10种不同的操作模式**： 由于结合了向下兼容模式、BCD模式的回归（在NES上缺失）以及在16位和8位寄存器组之间切换的能力 [@cpu-opcodes]，开发人员可以使用不同的组合来使用这款CPU。
  - 与后来的[MIPS CPU](nintendo-64#cpu)不同的是，它没有为8位和16位字提供专用操作码的混合指令集。 相反，相同的指令集会根据激活的模式进行不同的解释。
  - 出于兼容性考虑，CPU 始终以“仿真”模式（纯6502）启动，程序可自行切换到特定的“本地”模式，以启用16位功能。 顺便提一句，英特尔的x86处理器在现代CPU上仍然采用[相同的_工作方式_](xbox#boot-process)，这一点很有意思。
- **三个16位通用寄存器**。 这组寄存器与6502的寄存器（`X`、`Y`和`A`）相同。 不过，由于操作模式不同，这些寄存器现在可以在16位和8位之间切换。
  - 这个数字与[竞争对手](mega-drive-genesis#the-leader)提供的16个32位寄存器相比，可谓天壤之别！
- **16位内部数据总线**和**8位外部数据总线**： 这意味着在内存中移动大于8位的数值会带来性能损失（CPU 会花费额外的周期），特别是考虑到大多数指令都是16位长。 不过，总体成本是相对的，因为理光的变体包括DMA单元（稍后解释），CPU时钟会根据操作的不同而变化。
- **24位地址空间**： 允许CPU访问**多达16 MB的内存**。 与[摩托罗拉68000](mega-drive-genesis#cpu)类似，但 65C816 的24位地址是通过将额外的8位寄存器（`DBR`和`PBR`）与6502的原始16位寻址线相结合而获得的[@cpu-chibi]。 总的来说，这种方法类似于使用[内部映射器](pc-engine#memory-access)。

看到这里，我不得不承认65C816感觉过于繁琐，而收益却很少。 与[摩托罗拉68000](mega-drive-genesis#the-leader)等其他产品相比，我们不难得出结论，为什么Apple IIGS最终成为唯一采用65C816的个人电脑。 尽管如此，在这篇文章中，你将看到任天堂和理光如何将这种CPU的局限性转化为改造其游戏库的机会。

### 理光添加的硬件

65C816是一款通用CPU，于1983年推出，是其1975年祖先的后续产品，具有各种要求和限制。 然而，任天堂计划将其游戏机的寿命延长至整个90年代。 因此，理光必须加大力度去魔改（恕我直言）。

首先要解决的是算术上的限制，65C816没有包含任何乘法或除法的专用指令。 因此，理光增加了**16位乘法**和**除法单元**，使CPU能够通过硬件（而不是软件）执行这些运算。

现在，您可能会问“这有什么关系”，答案将在我们讨论超级任天堂图形芯片的某些新功能时揭晓（在“图形”部分）。

#### 高速内存访问

第二个挑战是提高数据带宽。 因此，我们增加了**两个独占的DMA（直接内存访问）**，以便在CPU不干预的情况下移动数据（从而提高速度）。 为使这一设计发挥作用，内存区域使用两条不同的地址总线进行引用[@cpu-manual]：

- 由CPU控制的24 位 **"A 总线"**: 连接卡带、CPU 与工作内存
- 由S-PPU控制的8 位 **'B 总线'** ：连接卡带、CPU、工作内存、S-PPU 和音频 CPU。

当设置DMA时， *数据来源* 必须与 *传输目标* 在不同的总线上。

此外，这两种DMA并不完全相同，其功能也截然不同[@graphics-piepgrass]：

- **通用DMA**可随时执行传输。 在传输完成之前，CPU会停止运行。
- **水平DMA**（HDMA）在每次水平扫描后（显像管光束准备绘制下一行时）执行少量传输。 这避免了长时间中断CPU，但传输限制为每扫描线4字节。

最后，系统提供8个通道用于设置DMA传输，因此可以同时调度多达8个独立的传输。

#### 片段故障

该游戏机还具有一种名为**“开放总线”**的特殊“异常”：如果有指令试图从一个未映射/无效的地址读取数据，则会提供最后读取的值（CPU会将该值存储在一个名为**内存数据寄存器**或`MDR`的寄存器中），并在不可预测的状态下继续执行。

相比之下，68000 使用向量表来处理异常，因此只要检测到故障，执行就会重定向。

### (大量的）更多内存

NES 的内存只有[2 KB](nes#memory)，但却能显示大量内容，这实在令人惊叹。 超级任天堂现在拥有**128 KB 的 SRAM**（仍称为 “工作内存 ”或WRAM）。 这比上一代产品多出6400%的通用内存。

那么，开发人员能用它做什么呢？ 可以做任何他们想做的事。 WRAM用于存储游戏的变量信息。 空间越大，可存储和处理的信息就越多（从而节省了[卡带上的硬件](nes#cartridgegame-data)） 不过，请记住，本文下面的章节将向你展示超级任天堂是一台相当复杂的机器（尽管它的CPU很 “简单”），我倾向于将这台游戏机称为“小型计算机/子系统的集合”。 现在，每个子系统都可能需要中央处理器提供数据。 因此，程序员可能会分配部分WRAM来处理这些信息，从而证明128 KB的必要性。

## 图形

说了这么多，让我告诉你们，这款游戏机的图形子系统是真正的工程杰作。 在CPU如此有限的情况下，我们可以想象，SNES绝不可能与采用“32位”摩托罗拉68000的[竞争对手](mega-drive-genesis)相提并论。 然而，任天堂和理光的工程师们设法找到了巧妙的技巧，利用CRT显示器的行为方式，从而在不需要昂贵的先进组件的情况下扩展了这款游戏机的功能

无论如何，在我们深入探讨之前，我强烈建议您先阅读[NES的文章](nes#graphics)，因为它介绍了一些有用的概念，我们将在这里重新讨论这些概念。

### 设计

与同时代的其他游戏机一样，超级任天堂使用2D图块（8 x 8像素）绘制图形。 NES 最初是通过捆绑标志性的“图像处理单元”（PPU）来实现这一功能的，该单元与CRT屏幕同步传输图像。 超级任天堂紧随其后，但它现在采用了更复杂的技术，以获得更丰富的效果。

#### 芯片组

超级任天堂内置两块不同的PPU芯片，构成图形子系统，合并后称为**超级PPU**或“S-PPU”。

![这两个PPU芯片](photos/s-ppus.webp)

这两个PPU被设计用于实现不同的功能[@cpu-manual]：

- **PPU 1**: 渲染图形(图块) 并在它们上应用变换(旋转和缩放)。
- **PPU 2**: 在渲染图形上提供像 *窗口*、*马赛克* 和 *淡出* 的效果。

从编程角度看，这种分离是多余的，因为两个芯片实际上被视为一体。

#### 显示模式

系统以**~60 Hz**（NTSC）[9]输出**256x224像素**的标准分辨率[@graphics-guide]。 欧洲PAL制式则以约**~50 Hz**的频率输出**256×240像素**（以符合 PAL 制式规格）。 尽管如此，大多数游戏并不使用额外的像素，而是显示*“letterbox”*（黑线）。

现在，棘手的问题来了，传统电视的宽高比是 4:3。 但如果你算一下，会发现超级任天堂的输出分辨率**长宽比为8:7**。 因此，将图像传输到电视上后，它看起来会被**水平拉伸**，就好像是一个**292x224像素**的画面（NTSC制式） [@graphics-aspect]。 换一种说法，可以说超级任天堂上的像素长宽比是8:7，而不是 “完美的正方形”。

::: {.subfigures .side-by-side .pixel}

![分辨率为256x224像素的渲染帧。 这就是游戏机发送到电视上的图像。](stretch/internal.png) {.toleft.snesratiosmaller}

![从电视机上看到的拉伸画面（分辨率明显为292x224像素）](stretch/external.png) {.toright.snesratiobigger}

星之卡比3（Kirby's Dream Land 3） (1997)

:::

有些游戏（如塞尔达传说：众神的三角力量（The Legend of Zelda: A Link to the Past））会考虑到这一因素，因此在设计时会明确将形状压扁，这样经过电视拉伸后看起来就正确了。 但这只是一个特例，因为大多数游戏都没有采取额外的措施来考虑这一因素。

### 硬件组织

![S-PPU的内存架构.](SPPU_architecture.png) {.no-borders}

由于成本和性能原因，图形数据分布在三个内存区域：

- 64 KB **VRAM** (视频 RAM)：存储图块和用于构建背景图层的映射 (表格)。
- 512 B **CGRAM** (彩色图形RAM)：存放512个彩色调色板条目，每个条目的大小为 一个*word* (16位)。
- 544 B **OAM** (Object Attribute Memory)：包含128个将被用作登记*精灵* 及其属性的查找表。

### 构造帧

现在让我们看看如何在游戏机上渲染画面，然后在电视上显示。 我们将以*超级马里奥世界*为例进行演示。

#### 图块 {.tabs.active}

![一些在 VRAM 中找到的16x16 图块.](sppu_mario/tiles.png) {.tab-float.pixel}

与前代产品一样，S-PPU使用图块来构建复杂的图形。 不过，与最初的PPU相比，S-PPU有了重大改进：

- 游戏**卡带不再与PPU连接**，因此图块必须先复制到VRAM（就像世嘉的Mega Drive一样）。 这时，DMA单元就派上用场了。
- 图块不再局限于传统的尺寸（8x8像素），从现在起，它们的大小也可以达到**16x16像素**。
- 图块存储在内存中时，将根据每个像素需要使用的颜色数量进行压缩。 大小单位是**bpp（位每像素）**。 最小值为**2bpp**（每个像素只占用内存中的两位，且只有4种颜色可用），最大值为**8bpp**，最多可编码256种颜色（代价是占用一整个字节）。

#### 背景 {.tab}

::: {.subfigures .tabs-nested .tab-float .pixel}

![背景图层 1 (BG1)。](sppu_mario/background1_map.png){.active title="Layer 1"}

![背景图层 2 (BG2)。](sppu_mario/background2_map.png){title="Layer 2"}

![背景图层3 (BG3)。](sppu_mario/background3_map.png){title="Layer 3"}

VRAM中的背景映射

:::

::: {.subfigures .tabs-nested .tab-float .pixel}

![渲染背景图层 1 (BG1)。](sppu_mario/background1.png){.active title="Layer 1"}

![渲染背景图层 2 (BG2)。](sppu_mario/background2.png){title="Layer 2"}

![渲染背景图层 3 (BG3)。](sppu_mario/background3.png){title="Layer 3"}

![渲染背景图层的组合。](sppu_mario/background_complete.png){title="混合"}

应用选区和透明度后渲染的背景图层

:::

超级任天堂可以生成多达四个不同的背景层。 通过使用8x8或16x16图块，区块由32x32像素（2x2 图块）构成。 尽管如此，每个背景图层的大小最多可达到1024x1024像素(32x32的图块)。 VRAM 中配置这些图层的区域称为 **图块映射区（Tilemap）** 并且这一部分的结构是表格(内存中的连续数值)。

Tilemap 中的每个条目都包含下列属性：

- 垂直 & 水平翻转值。
- 优先级（`0`或`1`）。
- 从CRAM调用的调色板引用.
- 图块引用。

一如既往，这些平面是可滚动的，但可用功能的数量（颜色、层数、独立滚动区域和选区大小）将取决于S-PPU上激活的**背景模式**，这就引出了下一部分...

#### 模式 {.tab}

S-PPU提供了许多背景操作，但这些操作不能任意选择。 相反，有八种背景模式可供选择，每种模式提供不同的功能[@cpu-rgme]：

- **模式0**: 4个图层，每层可用4种颜色。
  - 由于只有这种模式允许最多的图层，所以调色板的颜色特别平淡。
- **模式1**: 2个图层，每层可用16种颜色，再加上1个只支持4种颜色的图层。
  - 那个支持颜色少的图层可以分成前景和背景。
  - 这是最常用的模式。
- **模式2**: 2个图层，每层可用16种颜色。
  - 该模式具有额外的效果： 图层的每一列都可以独立滚动（类似于[Game Boy](game-boy#graphics)的效果，但垂直滚动）。
- **模式3**: 1个可用128种颜色的背景层，与1个可用16种颜色的背景层。
  - 颜色可以使用设置 RGB 值指定，而不是使用 CGRAM 引用。
- **模式4**: 模式2和3的组合(列滚动+RGB颜色映射)。
  - 第一层可用128色，第二层仅能使用4色。
- **模式5**: 1个可用16色的图层，与1个仅能使用4色的图层。
  - 所选区域的分辨率为**512x224**像素，将被水平压缩以适应屏幕（输出帧仍为256x224像素！）。 这样做的代价是将16x8像素的图块渲染为8x8的图块，将16x16像素的图块渲染为8x16的图块。
  - 此外，还可以通过激活**隔行扫描**来扩展垂直分辨率（达到**512x448 像素**，现在与输出尺寸成正比）。 作为交换，同样的压扁效果也会影响到图块，但现在也是垂直的。 这对于屏幕需要显示额外信息量的情况（如多人游戏/分屏）非常有用。
- **模式6**：模式2和5的组合（高分辨率+纵列滚动），但只允许使用一个32色图层。

如您所见，程序员现在可以选择所选区域的颜色、层数、效果或分辨率的优先级。

#### 精灵 {.tab}

![渲染的Sprite图层.](sppu_mario/sprites.png) {.tab-float.pixel}

内存中一个名为**对象属性内存**（OAM）的区域存储了一个表，其中最多可引用128个具有这些属性的精灵[@graphics-guidelines]：

- **大小**： PPU最多可将16个小图块以4x4图块象限的形式组合起来，形成一个精灵。
- **图块引用**： 该值指向用于绘制精灵的图块。
- **屏幕位置**。 只有位于可见区域内的精灵才会被渲染。
- **优先级**： 由于多个图层相互重叠，因此将显示优先级最高的图形，这也取决于正在使用的背景模式。
- **调色板插槽**，允许从CGRAM中选择9个插槽。
- **X/Y 翻转**。

S-PPU每扫描线最多可绘制32个精灵，如果溢出，S-PPU只会丢弃优先级最低的精灵。

#### 结果 {.tab}

![嗒哒——！](sppu_mario/complete.png) {.tab-float.pixel}

S-PPU在绘制每条扫描线时，首先处理每一层的相应部分，然后将它们混合在一起。

NES游戏的主要限制之一是只能在**垂直消隐（V-Blank）**期间更新图形。 显像管的光束从起点返回的那一刻提供了一个合理的时间帧，可以在不破坏图像的情况下重新调整一些图块。

现在，得益于SNES的新功能，这一限制有了不同的意义。

因为新的DMA/HDMA单元现在可以让程序员在不等待垂直扫描（V-Scan）[@cpu-dma]的情况下执行内存传输，游戏现在可以更新图块、颜色和寄存器，而无需等待整个画面绘制完成。 事实上，我们可以做得更多： 由于游戏现在可以在帧中**改变S-PPU设置**，这就意味着可以**在同一帧的不同阶段激活不同的背景模式**，为新颖的游戏设计打开了大门！

### 那个特点  {.tabs-close}

说实话，我还没有提到这个控制台最重要的特性...

::: {.subfigures .tabs-nested .tab-float .open-float .pixel max_subfigures=1}

![已渲染的背景层](mode7/layer.png){.active title="背景"}

![已分配的背景图](mode7/map.png){title="背景图"}

![屏幕上显示的已渲染的一帧<br>扫描线的第一部分使用另一种模式来模拟距离，模式7则从第二部分开始（这要归功于HDMA）。](mode7/displayed.png){title="显示"}

零式赛车（F-Zero）（1990）.

:::

介绍一下**模式7**，这是*另一种*背景模式，但这次的工作方式完全不同。 虽然它只能渲染一个8bpp的背景层，但却能在该平面上应用以下**仿射变换**[@cpu-rgme]：

- 平移
- 缩放
- 旋转
- 反射
- 剪切

S-PPU由**旋转矩阵**控制，以改变该模式的参数。 这里就不多讲线性代数了，但根据所需的效果，CPU必须执行一些三角函数（正弦和余弦），以相应地填充该表的条目。 对于65C816来说，即使使用定点精度，这也是非常昂贵的。 因此，理光在5A22中增加了乘法和除法寄存器，以卸载一些周期。

{.close-float}

顺便提一下，请注意变换列表中没有提到**透视**，而这正是您在示例游戏（F-Zero）中所看到的。 这是通过在每次调用HDMA时改变旋转矩阵来实现的，在此过程中产生了伪三维效果。 这应该能让你对S-PPU的实用性有所了解！

最后，由于需要进行大量计算，内存映射发生了变化，以优化两个PPU的流水线，第一个PPU处理**图块映射**（图块被引用处），而另一个PPU则获取**图块集**（图块被存储处）。

### 帧数下降的原因

还有一个话题，你有没有想过游戏卡顿的原因？ 当调用垂直消隐中断允许图形更新时，有时游戏仍在执行一些繁重的代码，并跳过了垂直消隐窗口，直到下一次调用垂直消隐时图形才能更新，由于帧没有更新，这就表现为帧速率下降。

这种情况也可能反过来发生，垂直消隐期间的大量处理将使PPU无法输出视频信号（因为总线被阻塞）。 因此，在扫描过程中会出现黑线，不过由于帧每秒更新50或60次，这种情况几乎不会引起注意。

### 方便的视频输出

如果游戏机不能通过双方都能理解的媒介将图像传送到电视上，那么上述所有进步都将是徒劳的。 随着超级任天堂的推出，该公司首次推出了一种名为**“多路输出”**的*通用但专有的*连接方式，它可以同时传输多种类型的信号，包括**复合信号**、**S-Video**和**RGB**[@graphics-pinouts]。

任天堂随游戏机附赠了一条“多路输出转复合接口”电缆，因为这在当时几乎是电视机的通用规格。 但在欧洲，**SCART**端口也非常流行，因为许多机顶盒和录像机都依赖它。 SCART端口的一大优点是可以传输多种类型的信号，这使得AV设备可以使用最理想的信号类型，而不会遇到兼容性问题。 据我所知，当时只有法国消费者获得了官方提供的SCART电缆，该电缆利用了超级任天堂的RGB针脚[@graphics-manuel]。

尽管如此，任天堂还是修改了其PAL制式游戏机的引脚布局，以符合SCART协议，并将 “复合同步 ”引脚换成了12V引脚（告诉电视机设置4:3宽高比）。 因此，尽管多路输出是“通用”的，但由此产生的RGB电缆（如果有的话）却是因地区而异的。

我认为，多路输出的真正优势是在当代开始显现的，因为它允许用户在修改游戏机内部结构的情况下，向最先进的电视机输出RGB信号。 不过，与复合视频和S-Video不同，RGB需要额外的“同步”信号。 为此，可以将电缆连接起来，从复合端口或S-Video捕捉同步信号；或者使用称为 “复合同 ”的专用同步线，以获得最佳效果。 但如上段所述，只有NTSC制式游戏机才使用后者。

## 音频

与图形处理能力一样，这款游戏机的音频部分也经历了重大革新。 我甚至可以说，它提供了同代产品中最先进的合成技术。

### 架构

有些公司[与雅马哈合作](mega-drive-genesis#audio)，有些公司则[自行设计解决方案](pc-engine#audio)。 任天堂与**索尼**（索尼是一家电子产品集团，也是_随身听（Walkman）_的发明者）合作，由索尼为其提供先进的合成器。 于是，索尼为他们提供了两个处理器：一个是可以对采样进行排序和混合的DSP，另一个是驱动DSP的CPU。

因此，该游戏机的音频子系统由以下部分组成：

- **S-DSP**：通过八个不同的通道播放ADPCM采样，然后将它们混合并通过音频输出发送。 DS 能够处理分辨率为16位、采样率为32 kHz的采样，它还提供以下功能
  - **立体声平移**： 分配通道，提供立体声效果。
  - **ADSR包络控制**： 设置音量在不同时间的变化方式。
  - **延时**：模拟回声效果。 它还包括一个频率滤波器，用于在反馈过程中切除一些频率。 不要将其与*混响*混淆！
  - **噪音发生器**： 产生听起来像白噪音的伪随机波形。
  - **音高调制**： 允许某些通道扭曲其他通道。 类似于调频合成（其[竞争对手](mega-drive-genesis#audio)使用）。
- **SPC700 CPU**： 也称为“S-SMP”，它是一个独立的8位 CPU，可与DSP通信并接收主 CPU 的指令[@audio-smp]。
- **64 KB PSRAM**：存储音频数据和程序。 主 CPU 负责将其填满。
  - 如果激活了“延迟”，则会为反馈数据分配一些空间（这实际上是非常危险的，因为如果使用不当，可能会覆盖现有数据！）。

该程序块与主 CPU 并行运行。 当游戏机启动时，SPC700 会启动一个64字节的内部ROM，将其设置为接收来自主CPU的命令[@audio-spc]。 之后，游戏机将处于空闲状态。

::: {.subfigures .tabs-nested .tab-float .open-float max_subfigures=1}

![旋律使用的通道](melody){.active video="true" title="旋律"}

![出于演示目的，对鼓进行了区分](drums){video="true" title="鼓"}

![所有音频通道。](complete){video="true" title="整个音频"}

星际火狐（StarFox）（1993）

:::

要让 S-SMP 开始执行有用的工作，它需要加载一种称为**声音驱动程序**的程序。 后者指示芯片如何处理主CPU发送到PSRAM的原始音频数据，以及如何命令S-DSP。

正如您所看到的，与上一代产品相比，声音子系统是一个巨大的进步，但其编程难度也很大。 任天堂提供的文档中包含了许多令人费解的解释，而且完全跳过了重要的功能，因此程序员必须自己进行研究。

因此，市场上出现了大量不同的音效驱动程序[@audio-drivers]，其中一些最终发现了令人印象深刻的功能。

{.close-float}

### 音高控制

音高弯曲可以使用相同的采样产生不同的音符。 S-SMP 包含一个有用的弯音器，可以平滑地改变音高。 请看这个从地球冒险2（Mother 2/Earthbound）中提取的通道，两个例子都来自原声带，不过第一个例子的音高控制被禁用了。

![无音高弯曲](pitch/no_pitch){.toleft video="true"}

![启用音高弯曲](pitch/pitch){.toright video="true"}

### 从 NES 开始的演变

为了展示从NES到SNES声音的演变，这里有两段配乐，一段来自NES游戏，另一段来自其SNES续集。 两款游戏都使用了相同的乐谱：

![地球冒险（Mother） (1989).](snowman_nes){.toleft video="true"}

![地球冒险2（Mother 2/Earthbound）（1994）](snowman_snes){.toright video="true"}

###

::: {.subfigures .tabs-nested .tab-float .open-float max_subfigures=1}

![旋律使用的通道](kirby/trebble){.active video="true" title="旋律"}

![出于演示目的，对鼓进行了区分](kirby/drums){video="true" title="鼓"}

![所有音频通道。](kirby/complete){video="true" title="整个音频"}

星之卡比3（Kirby's Dream Land 3） (1997)

:::

这是一首乐器更为丰富的乐曲，充分发挥了音高弯曲、回声和包络的优势。

这些技术的结合使得音乐总共只需要五个通道，而另外三个通道则留给了音效。

{.close-float}

### 立体声混淆

DSP的音量控制是以有符号的8位数值[@audio-gst]为单位组织的，这意味着音量可以设置为**负值**。 *但等等*，如果“0”表示静音，那么“-1”这样的数字会有什么作用呢？ 嗯，它会**对信号进行反相**。

这特别适用于营造特殊的**环绕效果**，通过设置立体声通道输出**失相立体声**（一个通道输出正常信号，另一个通道输出相同信号但反相）来实现。

不幸的是，滥用这一功能会导致非常不愉快的结果（例如，感觉音乐是从你的脑海中传出的），因此你会发现大多数SNES模拟器都完全跳过了这一设置。

此外，失相立体声在单声道设备上会被抵消，因此这些游戏必须包含一个立体声/单声道选择器，以避免自己的配乐失真。

## 游戏

说白了，主程序是用普通**65816汇编语言**编写的，音乐驱动程序是用**SPC700汇编语言**编写的。 恐怕你无法在21世纪的设备上找到这些商品。

不过，任天堂、Intelligent Systems和理光分发了一些工具，使程序员过的更加轻松，这些工具包括 [@games-sdk]：

- 不同类型的**开发单元**，可由主机（通常运行 MS-DOS）上的**调试器**控制。
- **可烧录卡带**（又称_烧录卡（Flashcarts）_）。 不是用于_盗版_，而是让开发人员能够在零售设备上试用他们的代码。
- 65816和SPC700**编译器**。
- **开发手册**，从底层角度解释游戏机的工作原理。 这些手册还可能包括开发人员必须遵守的准则和规范，以便他们的游戏获得任天堂的批准（发行所需）。

奇怪的是，Argonaut Software、Accolade、SN Systems 等多家游戏工作室也开发了自己的内部设备，提供了比官方产品更多的功能（如内存编辑器、软盘读取器、作为 ISA 子板的开发套件等）[@games-devkit]。

### 卡带配置

与NES相对简单的映射模式相比，在访问卡带时，其机制变得更加扑朔迷离。 尽管如此，我还是觉得很有意思，尤其是在了解如何扩展方面。

65C816使用24位地址总线，因此最多可访问16 MB的数据。 不过，由于该主机的设计方式，部分地址空间被[内存映射组件](nes#memory)占用。 此外，65C816只有16根地址线，然后与一个内部寄存器组合成24位地址。 这就好比安装了一个[内部映射器](pc-engine#memory-access)，需要通过[内存地址组切换](nes#cartridgegame-data)来访问地址总线边界以外的额外数据。 如果你阅读过同世代的其他游戏机的文章，你会发现这种方法有些熟悉。

![采用不同配置设计的卡带电路板示例[@photography-amos]<br>从上到下依次为：LoROM（带电池供电SRAM的双ROM）、LoROM（带电池供电SRAM的单ROM）和 LoROM（单ROM）](cartridges.png) {.no-borders}

尽管如此，在设计盒式磁带时，ROM和CPU之间的地址引脚有多种电气连接方式。 每种方式都能以不同的方式利用内存地址库切换。 有两种基本模式可以访问多达**4 MB的ROM**和**64 KB的SRAM**[@games-memory]，它们是这样工作的：

- 在**LoROM型**下，ROM数据以32KB为一块，有128个地址库可供选择[25]。 另一方面，SRAM有两个地址库，但可通过15个地址库访问，ROM数据也可在这些地址库中找到。
  - 这意味着游戏/程序在执行过程中可能需要进行大量的地址库切换。 另一方面，有一半的地址库也映射到WRAM的一部分（这意味着无需切换地址库即可访问ROM、SRAM和WRAM）。
- **HiROM型**下，ROM空间现在占据了全部的地址库，这意味着数据可以64 KB为一块，有64个地址库可供选择。 这就减少了库切换次数，但代价是无法读取同一地址库中的ROM、WRAM和SRAM。

在这两种情况下，ROM空间的很大一部分被镜像到CPU地址空间的剩余区域，但有趣的是：一半空间的读取频率约为~2.68 MHz，而另一半空间的读取频率可达**3.58 MHz**。 这只有在ROM芯片支持更高速度且CPU的`FastROM`标志被启用时才会发生[@games-registers]。

我还没有提到的一点是，捆绑SRAM的卡带还需要内置一个**MAD-1**（或类似的）芯片，用于执行地址解码。 这对于正确锁存ROM和SRAM芯片之间的选择引脚尤为重要[@games-pcb]。

#### 超越传统

现在，如果程序员需要更多空间，LoROM和HiROM的衍生型号就可以派上用场了。 例如，通常被称为**ExHiROM**和**ExLoROM**的两种变体通过减少镜像区域来扩展ROM的寻址空间。 两者都能访问**~7.9 MB的ROM**。

![星际火狐（1993）使用Super FX GSU芯片渲染3D表面（S-PPU只能看到由_不规则_图块组成的背景层）。](fox.png) {.open-float.pixel}

另外，最重要的是，LoROM和HiROM也可用于在卡带中安装**增强芯片**。 这些额外的处理器可以扩展游戏机的功能。 下面举几个新配置的例子：

- **MMC**型可通过本地增强芯片实现内存库切换。 这被**S-DD1**或**SPC7110**等组件所采用，它们都是硬件图块解压缩器。
- **超级加速器系统（Super Accelerator System）**型是为备受赞誉的**SA-1**设计的，SA-1是**基于65C816的副CPU**，工作频率为**10.74 MHz**，并集成了额外的功能。 它以 MMC 型号为基础，为额外的内存映射组件配备了额外的电路。
- 两个额外的配置（一个基于LoROM，另一个基于HiROM）为DSP系列芯片让路。 这些芯片是NEC µPD77C25 CPU的衍生产品，但运行不同的程序。 它们提供矢量和矩阵计算[@games-dsp]，不同的游戏用它来计算仿射变换、图形解压缩和最短路径计算。
- **SFX**型采用流行的**Super FX GSU**芯片。 这种配置映射了多达8 MB的ROM，其中2 MB由主CPU和Super FX共享。 其余地址空间还包括额外的备份RAM和SRAM。 Super FX是Argonaut制造的专有处理器，专门用于**3D 曲面渲染**和**2D仿真变换**，然后将结果以图块的形式流式传输，供S-PPU显示。 一些著名的游戏使用它来绘制3D模型和/或扩展模式7来绘制精灵（因为模式7只能变换背景）。

{.close-float}

很难忽视这项工程设计对90年代游戏的影响，许多游戏在不需要扩展模块的情况下就能超越这款游戏机的预期。 可以说，这让任天堂推出这款游戏机后续机型的计划变得复杂起来（这也解释了为什么星际火狐2等先进的游戏被取消）。

## 反盗版/锁区

随着超级任天堂的问世，任天堂再次成为游戏发行的唯一权威。 因此，为了执行公司的规定，工程师们设计了三个不同的层次来保护卡带，防止第三方分销（进而规避任天堂的版税）。

首先，**不同地区的卡带外部形状不同**，因此无法安装在不同地区的游戏机上。 尽管如此，任何人都可以通过使用第三方转接器轻松解决这个问题。

其次，这款游戏机和NES一样，仍然采用了[**10NES**保护系统](nes#anti-piracy-and-region-lock)来锁定非授权经销商。 尽管如此，CIC芯片最终还是被克隆了。

作为最后一层（也是为防止盗版而专门制作的），游戏还包括一连串的盗版检查，如：

1. 比较SRAM的大小（盗版游戏通常包含较大的SRAM块，以适应任何类型的游戏）。
2. 对代码进行一系列校验，以检查之前的比较是否被删除。 这些校验分散在游戏的不同阶段，因此很难找到。

这可以通过手动删除这些例程来消除，但要找到所有这些例程自然需要大量时间。 毕竟，它们散落在游戏中只会让玩家不爽（并最终让他们购买正版）。 实话实说，你会发现互联网上的大多数ROM都已删除了所有盗版检查。

## 这就是全部了，伙计们。

![我改装的SNES，装有美版卡带。<br>那款游戏只在美国发行。 幸运的是，一个小伙子在格拉斯哥卖这款游戏！](mysnes.png)