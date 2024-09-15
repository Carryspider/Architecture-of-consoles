---
short_title: ニンテンドーDSのアーキテクチャ
long_title: ニンテンドーDSのアーキテクチャ
name: ニンテンドー DS
date: 2020-08-11
subtitle: 新しい形のインタラクション
release_date: 2004-11-21
cover: "nintendods"
javascript:
  - 'audioswitcher'
  - 'threejs'
generation: 7
top_tabs:
  Models:
    - 
      title: "初期型"
      caption: "オリジナルのニンテンドー DS (青版)<br>アメリカでは2004年11月21日、日本では2004年12月2日、ヨーロッパでは2005年3月11日に発売。"
      active: true
      file: original
    - 
      title: "Lite"
      caption: "ニンテンドー DS Lite (黒版) <br> 日本では2006年3月2日、アメリカでは2006年6月11日、ヨーロッパでは2006年6月23日に発売。"
      file: lite
  Motherboard:
    caption: "初版"
  Diagram:
    caption: "この文章の構成を追うのに困った場合のヒント: TopはARM9のみアクセスできます。下のセクションはARM7のみ、中央のセクションは共有されます。"
#Historical
aliases:
  - /projects/consoles/nintendo-ds/
---

## クイックスタート

このゲーム機は、従来の携帯機では不可能であった多くのニーズに対する興味深い答えです。 妥協されたと思しき点と革新的な点がありますが、この組み合わせが新しい独創的なコンテンツに繋がったのかもしれません。

## {.supporting-imagery}

## CPU

任天堂の [従来の携帯機](game-boy-advance)と同様に、 **CPU NTR** という名前の大きなチップを中心にシステムが構築されています。 'NTR' は、初代ニンテンドーDSのコードネームである**Nitro（ニトロ）**の略です。 このSoCは、ゲームボーイアドバンスから始まった [ニンテンドー & ARMパートナーシップ](game-boy-advance#the-nintendo-partnership)の後継作でもあります。

さて、新しいCPUを説明する前に、ニンテンドーDSの要素技術に影響を与えた、90年代後半におけるARMの進化を見てみましょう。

### ARMの新境地

90年代半ば、ARMはモバイル市場 (携帯電話が「スマート」になる前) でのビジネスを急拡大していました。 しかし、**ハイパフォーマンスコンピューティング** の領域ではあまり上手くいかなかったようです。 ARM7は多くのモバイル機器で採用されていましたが、性能面ではApple (の個人用端末『Newton』) とAcorn (のRiscPC) が搭載する高速なCPUとソフトウェアには及びませんでした。 これは40Mhz以上の速度で動作するCPUを生産できないこと([MIPSは既に商用化していた](nintendo-64#cpu)) に加えて、64ビットに対応していないことも問題でした。 ビジネス全体にボトルネックが積み重なり始めていました。

それでも、ARM7ラインの商業化の間に、ARMは高性能市場への最初の試みとして**ARM8**という後継機の開発をすでに開始していました。 その間、 PDP (いわゆる「ミニコン」) で歴史的に有名なアメリカの会社 **Digital Equipment Corporation (DEC)**は、反対の問題を経験していました。彼らは自らが持つ高性能化技術に基づいて低消費電力のCPUを開発することに苦労していたのです。 そこで、DECは[ARMのライセンスモデル](game-boy-advance#tab-1-2-a-new-cpu-venture)に目を付け、ARMから資料(命令セットとマイクロアーキテクチャ) を借りて新しいCPUを開発することにしました。

最終的にDECは、自社の**Alpha**プロセッサのデータパス設計を採用し、これをARMのマイクロアーキテクチャと混合しました [@cpu-jaggar]。 このコラボレーションは、1996年にリリースされたパフォーマンスを重視した**StrongARM**CPUにつながりました。

![Acorn RiscPCのアップグレード版CPUの一部である233MHzのStrongARM CPU](strongarm.jpg)

StrongARMはARMベースの新しいCPUで、[@cpu-furber] を搭載します:

- **ARMv4 命令セット**: これについて詳しく知りたい場合は、以前にゲームボーイアドバンスの記事で[紹介しています](game-boy-advance#commanding-the-cpu)。
- 最大**233 MHz**のクロックで、これは最も強力なARM7チップよりも約583%速い。
- **5段パイプライン**、オリジナルの [3段構成](game-boy-advance#the-core)から増加。
- 命令用に**16 KB**、データ用に**16 KB**が割り当てられた**新しいキャッシュシステム**を持つ**ハーバードアーキテクチャ**。
  - この設計はフォン・ノイマン・ボトルネックを軽減するのに役に立つ(フォン・ノイマン/プリンストンモデルが苦しんでいた問題です) 。

このチップは、以前のARMチップと同様に **3.3 V**の電源で動作できます。 実際、StrongARMは2ボルトを必要とし、消費電力はわずか1ワットです [@cpu-furber]。 対照的に、インテルの200 MHzペンティアムチップは3.5Vを引き、消費電力は最大で15.5Wです [@cpu-pentium]。

予想通り、Acorn と Apple はこの新しいチップに感銘を受け、すぐに各端末のCPUアップグレード版と、さらなるNewtonモデルを出荷しました。 もちろん、DECの発明を利用して。

同じ年に、ARMは約束していたARM8ベースのCPUであるARM810をリリースしました。 それは比較的遅く、StrongARMに対して実用的な利点を提供しませんでした。 なので、このCPUが顧客の関心を惹くことはありませんでした。 この結果をうけて、ARMはモバイル市場向けのARM7ラインの改良に移行しました。 しかし、DECのCPUの可能性は非常に革新的であったため、ARM HoldingsはStrongARMの設計を吸収し [@cpu-jaggar]、次のCPUラインである**ARM9**を製造しました（これをニンテンドーDSが搭載しています）。

#### 転換点

StrongARMのおかげで、ARMはハンドヘルド市場での地位を固め、[MIPS](nintendo-64#cpu)や[SuperH](dreamcast#cpu)を完全に置き換える有力な代替手段となりました。 それ以来、ARMはモバイルデバイス向けで最も採用されるアーキテクチャへの道を歩んでいました [@cpu_android_abi]。

DECにとって残念なことに、このCPUは1998年にCompaq社に買収されるまでの最後の成果となりました。 StrongARMの運命はインテルに委ねられ、その後インテルは新しい「Intel XScale」ラインの下で開発を継続しましたが、インテルは時折「低電力」x86 CPU（つまりIntel Atom）に集中するために部門を清算しました [@cpu-xscale]。 15年後、インテルはモバイル市場でのチャンスを失っただけでなく、今やデスクトップ市場でARMと激しく競り合うことになりました。

### ニンテンドー製SoCのデビュー

前述の進歩に加えて、CPU NTRは**ARM7TDMI**と**ARM946E-S**の2つの異なるARM CPUを使用した興味深い**マルチプロセッサアーキテクチャ**をバンドルしています。 このデザインはARM ホールディングスが正式に [マルチプロセッサソリューション](nintendo-3ds#cpu)をリリースする前に設計されました。 そのため、この二つのCPUの機能は少し風変わりと言えるかもしれない(現在の技術と比べると) 。

![CPU NTR チップ](cpu_ntr.jpg)

このシステムは[本シリーズ](consoles)で分析する最初の並列システムではありませんが、その設計は他のものとは大きく異なります。 例えば、 [セガサターン](sega-saturn) が実装した"実験的"なマスタースレーブの仕組みや、 [PS1](playstation) または [N64](nintendo-64) での"コプロセッサー"のアプローチとは異なります。 ニンテンドーDSには、専用のバスを持つ2台の独立したコンピュータが使われています。 この設計方法は **非対称マルチプロセシング** と呼ばれ、結果としてこの CPU の依存関係は、このシステムの全体的な性能を左右します。

それでは2つのCPUを見てみましょう。

#### ARM7TDMI {.tabs.active}

![ARM7の構造とコンポーネント](cpu/arm7_core.png) {.tab-float}

より身近なものから説明しましょう。**ARM7TDMI**は[ゲームボーイアドバンス](game-boy-advance#cpu)で採用されているCPUですが、ニンテンドーDSでは34MHz以上(オリジナルの2倍) で動作します。 このプロセッサはオリジナルの特徴(特に[Thumb](game-boy-advance#tab-2-3-squeezing-performance) 命令セット) をすべて備えます。

この変更は、任天堂の技術者がI/Oポートの脇にI/Oの調停を担うためのARM7を設置したことに由来します。 実際、他のプロセッサはI/Oに直接接続されていません。 ご覧のとおり、これはシステムを担当する"メイン"プロセッサではなく、多くのコンポーネントにデータを渡すことによってメインの CPU をオフロードする"サブプロセッサ"です

#### ARM946E-S {.tab}

![ARM9の構造とコンポーネント](cpu/arm9_core.png) {.tab-float}

これがニンテンドーDSの'メイン'CPU、**ARM946E-S**です。 "StrongARM"ほどの速度ではありませんが、**~ 67MHz**で動作します。 **ARM9シリーズ**の一部であるこのコアは、**ARM7TDMI** と **StrongARM** の全ての特徴を継承しつつ、以下のような興味深い変更が施されています。

- **ARMv5TE ISA** は、 [ 以前のARMv4 ISA](game-boy-advance#commanding-the-cpu)と[Thumb](game-boy-advance#tab-2-3-squeezing-performance)の拡張版であり、より多くの命令とより高速な乗算器を備えています。
  - コア名の「E」は**拡張DSP**を意味し、新しい命令の多くが信号処理のアプリケーションに関係していることを示唆している。
  - 拡張されたThumbは、 **Thumb v2** と呼ばれることもあります。 この拡張で追加される`BLX` と `BKPT`命令によってARMとThumbモードの切り替えが支援され、それぞれのデバッグを容易にします。
- **5段パイプライン**: StrongARM や ARM8 と同じ。
- **12KBのL1キャッシュ**：このキャッシュ(ARM7TDMIにはなかった) が特徴的で、ハーバード方式を採用するStrongARMと同様に、命令用に**8KB**、データ用に**4KB**が割り当てられています。
  - キャッシュには**ライトバッファ**を採用し、RAMの更新を非同期で行うため、CPUはRAMとの同期を待たずに他のタスクを処理できます。
- **48 KB の密結合メモリ (TCM) **: [Scratchpad memory](playstation#cpu) と似ています。 ただし、ここでは命令(32 KB) とデータ(16 KB) を区別して扱います。
- 統合された **CP15 コプロセッサ**: **メモリ保護ユニット (MPU)**として機能し、どのメモリ範囲にアクセス、キャッシュ、および書き込みが可能かを規定します。

また、任天堂は以下のコンポーネントを追加しました。

- **除算器と平方根ユニット** は、除算・平方根の演算を高速化します(ARM9自体はこれらの演算を行うことはできません)。
- **DMA**: CPUに依存しないメモリ転送を高速化します。 キャッシュと組み合わせて使用する場合には、CPUと DMAの両方が同時に動作する可能性があります。
  - キャッシュとDMAはパフォーマンスの向上に大きく寄与しますが、**データの整合性と一貫性**の問題も生じます。 そのため、プログラマーはDMAを起動する前に[ライトバッファをフラッシュ](playstation-2#preventing-past-mishaps)することで手動でメモリの一貫性を維持する等の対策を行う必要があります。

このようなハードウェアですから、子供たちがこのコンソールを*とても*気に入るのも分かりますよね？

### インターコネクション {.tabs-close}

ここまでで、2つのCPUが個別にどのように動作するかについてお話しました。 しかし、全体の動作には二つのCPUが常に協調して動作する必要があります。 そのために両方のCPUは、専用の **FIFOユニット** [@cpu-double] を使用して互いに直接"会話"します。このデータブロックは、**双方向通信**のために、2 つの 64 バイト キュー (16 エレメントまで) を保持します。

![FIFOユニットの表現](cpu/fifo.png)

これは以下のように動作します: 送信側のCPU (効果的に他のメッセージを送信する必要があります) は 32 ビットのデータブロックをキューに入れ、受信側のCPUは、そのブロックをキューから引き出して必要な操作を行います。

キューに置かれている値は、いつでもCPU が手動で取得することができます (**ポーリング**) 。 この際には常に新しい値をチェックする必要があります(この動作はコストが高い)。 または、 **割込みユニット** を有効にして、キューに新しい値がある場合に受信側に通知することもできます。

### メインメモリ

以前のRAMと同様に、さまざまな場所にRAMが配置されているため、アクセス速度別にメモリにアクセスすることができます。 まとめると、以下の汎用メモリが使用されています。

![本コンソールのRAMモデル](cpu/ram.png) {.open-float}

- **32 KB の WRAM** (ワーク RAM) **32 ビット** バス: ARM7とARM9で共有される高速データを保持します。
  - 同じアドレスに一度にアクセスできるCPUは1つだけです。
- **64 KBのWRAM** 高速アクセス用の**32 bit**バス: GBA等のARM7からのみアクセスできます。
- **4 MBのPSRAM** 低速の**16 bit** bus: CPUとメモリーインターフェースユニットによって制御される。
  - 疑似的なSRAM「PSRAM」は、チップ内からリフレッシュを行うDRAMです。 したがって、SRAM(より速いが、より高価なDRAMの代替品) のように振舞います。 このデザインは[1T-SRAM](gamecube#clever-memory-system) を思い出させます。

{.close-float}

### 後方互換性

アーキテクチャはゲームボーイアドバンスとは大きく異なりますが、ネイティブ互換性を持っています。

DSがGBAとして振舞うために、 **AGB互換モード** でコンソールを設定するソフトウェアルーチンのセットが含まれています。 これによりARM9は停止し、ほとんどの'固有'ハードウェアを無効にし、16.78MHzに低下したスピードでARM7にリダイレクトされます。 最後に、ARM7は(オリジナルのゲームボーイアドバンスのように) GamePakカートリッジをブートストラップするオリジナルのAGBBIOSを起動します。 このモードには、元のコンソールにはない特徴もあります。 例えば、オリジナルの方が画面解像度が大きいため、ゲームを黒い余白付きで表示します。(解像度の話は次のセクションで説明します)。 また、GBAのゲームをDSの二つの画面の内のどちらに表示するかをユーザが設定できます。

一度GBAモードになったら**元のモード**に戻ることはありません。ハードウェア全体を再起動するには、コンソールをリセットする必要があります。

### 未知の力

非常に多くの洗練されたコンポーネントが、単一の安価なチップに取り付けられていますが、この複雑さが問題を引き起こす可能性があります。

ARM9の説明から始めましょう。このCPUはARM7の2倍の速度で動作しますが、ほとんど(全てではない) のI/OはARM7に依存しています。 そのため、ARM9はARM7からの応答を待つ間はずっと停止していなければなりません。 さらに、**ARM9の外部バスは、その半分の速度**で動作するため、これらが大きなボトルネックになります。

また、メインメモリバスは **16ビット幅**です。 したがって、CPUが1ワード(32ビット幅) をメモリからフェッチする必要がある場合、 インターフェイスは、**1ワードが揃うまで最大3サイクル"待機"することで**CPUを停止します。 最悪の場合、つまりメモリアクセスがシーケンシャルではない場合には、1回のアクセスごとに待機が発生することになってしまいます。 この問題は、命令がフェッチされたときにも発生します(残念ながら、ARM はシーケンシャルなオペコードのフェッチをサポートしていません)。しかも、Thumbコードにも影響します(16ビットのフェッチはすべて32ビットブロックのフェッチとして行われるため) 。 一方、いくつかのソースがそれを呼び出すことでキャッシュとTCMを最大限に活用することによって、この「ペナルティ」を軽減することができます。

要するに、最悪の場合にはARM9の動作クロック66MHzが実質的にわずか8MHzに制限されます。 つまり、プログラムがキャッシュとTCMを全く有効に動かさない場合です。

詳細については、Martin Korthのドキュメント[@cpu-korth]、具体的には'DS Memory Timings'セクションをチェックすることをお勧めします。

## グラフィックス

このコンソールには複数の画面が表示されているため、このセクションは少し珍しいですが、 伝統的なタイルエンジンと現代のレンダラーを両方とも使うことができます。

物理的な特徴から説明しましょう。ニンテンドーDSには2つのLCDスクリーンがあります。 それぞれが **256x192ピクセル** 幅で、GBAよりも約20%多いピクセル数です。 262,144色(18ビット) を表示できて、60Hzでリフレッシュします。

### アーキテクチャ

グラフィックスサブシステムは、2D オブジェクトと 3D オブジェクトを描画できます。 前者は幅8x8ピクセルのビットマップ(タイルと呼ばれる) で満たされる2次元オブジェクトで構成されています。 後者は頂点を使用して3次元オブジェクト(多角形) を描画します。

これらのスクリーンを動作させる内蔵チップを深く知ることで、2Dと3Dジオメトリ向けのハードウェアが搭載された特徴的なコンソールであることを確認できます。 2Dデータは、おなじみのエンジンPPU(現在は **2Dエンジン**と呼ばれています) によって操作されています。 3Dデータはまったく新しいサブシステムによって処理されます。 これは3Dグラフィックスを実現した [最初のコンソール](nintendo-64) ではないが、言及する価値があります。しかし、 3Dグラフィックスのレンダリングユニットが社内で設計されたのは、このコンソールが初めてです。

![異なるグラフィックユニットのレイアウト](gpu/overall.png)

これらのエンジンは、いずれかの画面にリンクする必要があります、これは各画面に1つの2Dエンジンがあるので、2D専用のゲームでは問題ありません。 しかし、最先端の機能を披露したいと思っているゲームにとっては、利用可能な3Dエンジンは1つだけなのです。 そのため、3D機能は一度に1つの画面でのみ利用できます。 しかし、2Dと3Dのオブジェクトの混在はどうでしょうか？ まずはそれぞれのエンジンを別々に説明して、その後で解説しましょう。

### 2D グラフィックスを用いたフレームの構築

ここでは'次世代'の2Dゲームを構成する変更点だけを紹介するので、まずはGBAのPPUについて述べた[以前の記事](game-boy-advance#graphics)を読むことをおすすめします。 このコンソールは2つのエンジンを持ち、最初のエンジンは **メイン** 、2番目のエンジンは **サブ**と呼ばれます。 これは、必ずしもどのスクリーンに接続されているかを意味するわけではありません。 一方、'メイン' は 'サブ' よりも少し多くの機能を持っています。

説明のために、今回は *New Super Mario Bros* のアセットをお借りします。

#### タイル {.tabs.active}

![VRAMで見つかったいくつかのタイル デモ用では、デフォルトのパレットが適用されます](mario/tiles.png) {.tab-float.pixel}

現在では、基本的なタイルシステムがどのように機能するかは皆さんは知っていますが、このコンソールではタイルはどのように管理されているのでしょうか? 合計で **656 KBのVRAM** が利用可能です。このチャンクは別々のバンクに分かれています。4つの128KBと、1つの64KB、1つの32KB、3つの16KBです。 プログラマーは、図面でバンクを埋め、必要なデータがあるエンジンを指します。(?) 両方のエンジンは、これらのバンクのいずれからも読み取ることができますが、同時に同じものにアクセスすることはできません。

それにもかかわらず、データの配信方法にはいくつかの制限があります。 たとえば、ARM7は2つの128 KBのバンクにのみアクセスできます。 同時に、これらの2つのバンクはスプライトを保管することができず、唯一「サブ」だけが最後の16KBのバンクにアクセスできます。 リストはまだまだ続きますが…でも、要点はお分かりいただけたでしょうか。

最後に、後で説明する3Dエンジンは、テクスチャを取得するためにこれらのバンクのいくつかにアクセスします。

#### 背景のタイプ {.tab}

[スーパーファミコン](super-nintendo#graphics)の時代から見ると、PPUは背景レイヤーをより柔軟に構築する方向に進化してきました。 それから14年後、取得したタイルに多くの **アフィン変換** を施せるチップを手に入れたため、遂にフレームバッファからレイヤーを作れるほどになりました。

2Dエンジンがバックグラウンドを生成するために動作できるさまざまなモードを議論する前に、エンジンが生成できるバックグラウンドの種類を指定するこのリストをご覧ください：

- **文字タイプグループ**: これらの背景タイプは従来のタイルシステムに従って、フレームはタイルで塗りつぶしてレンダリングされます。
    - **静的** もしくは テキスト背景: 通常の背景。 最大512x512 ピクセル、256色、16パレット。 これは、すべての典型的な [エフェクト](super-nintendo#tab-1-2-background) (H/Vフリッピング、H/Vスクロール、モザイク、アルファブレンド) と追加の「フェード」エフェクトを含みます。 最大1024タイルまで使用できます。
    - **アフィン**背景: [アフィン変換](super-nintendo#unique-features) を適用する背景。 しかし、H/Vのフリップは許さず、256タイルのみを取得することができます(最大タイル数の4分の1)。 このレイヤーのサイズは、幅1024x1024 ピクセルです。
    - **アフィン拡張 - キャラクター**: アフィンと同じですが、タイルの全量を復元し、H/Vフリップをサポートします。
- **ビットマップタイプグループ**: タイルを処理する代わりに、エンジンは VRAM をフレームバッファとして扱います。
    - **アフィン拡張 - 256 色**: 「アフィン拡張 - キャラクター」から利用可能なすべてのエフェクトを継承します。 違いは、512x512 px の単一ビットマップに適用されることです。
    - **アフィン拡張 - ダイレクトカラー**: フレームバッファが32,768色(15ビット) をサポートするようになりました。
    - **大画面**: 大きな1024x512 ピクセルのフレームバッファをレンダリングするため、128 KB のVRAMのチャンクを使用します。
- **3D 背景**: 3Dエンジンの背景レイヤーを表示します。 3Dエンジンが処理したものを表示するために不可欠です。 それは多くの2Dエフェクトを提供しませんが、水平スクロールやアルファブレンド(他のバックグラウンドレイヤーとの) のような興味深い機能があります。 また、最大262,144色(18-bit) をサポートする唯一のタイプです。

これらのモードは任意に選択することはできません。代わりに、コンソールは **バックグラウンドモード** の一連の組み合わせを提供します。 それにもかかわらず、アフィン拡張モードには3種類のフレーバー(「文字」、「256色」、「ダイレクトカラー」) があります。 したがって、次のセクションでは、Xバックグラウンドモードが「アフィン拡張」タイプをサポートしていることがわかった場合、開発者はどのバリアントを必要とするかを選択できます。

#### 背景の表示モード {.tab}

::: {.subfigures .tabs-nested .tab-float .pixel}

![背景レイヤー0 (BG0) この特定のレイヤーは、雲の動きをシミュレートするために特定のスキャンラインで水平方向にシフトされます。](mario/bg1.png){.active title="Layer 0"}

![背景レイヤー2 (BG2)](mario/bg2.png){title="Layer 2"}

![背景レイヤー3 (BG3)](mario/bg3.png){title="Layer 3"}

使用中の静的背景レイヤー。

:::

ここでは、背景タイプが動作します。 'メイン' と 'サブ' は複数の操作モードを提供します。 それらはすべて **4つのバックグラウンドレイヤー**を生成しますが、各レイヤーはアクティブなモードに応じて異なる機能を持っています。

- **モード 0**: 4つの静的レイヤー
- **モード 1**: 3つの静的レイヤー + 1つのアフィンレイヤー
- **モード 2**: 2つの静的レイヤー + 2つのアフィンレイヤー
- **モード 3**: 3 スタティックレイヤー + 1 アフィン拡張レイヤー
- **モード 4**: 2つの静的レイヤー + 1つのアフィンレイヤー + 1つのアフィン拡張レイヤー
- **モード 5**: 2つの静的レイヤー + 2つのアフィン拡張レイヤー
    - これは非常に柔軟性があるため、最も一般的なモードです。
- **モード 6**: 1つの3D 背景レイヤー + 1つの大画面レイヤー
    - 単一フレームバッファ用のスペースしかないので、このモードは 'メイン' でのみ使用できます。

さらに、モード 0 から 5 では、'メイン' エンジンは最初の静的レイヤーを代わりに3D背景として使用することができます。 3D機能については後述します。

#### スプライト {.tab}

![スプライト層をレンダリングした。](mario/sprites.png) {.tab-float.pixel}

スプライトまたは「オブジェクト」はGBAのPPUから同じ機能を継承していますが、2つの大きな追加があります。

まず、OAM(スプライトエントリが保存される領域) が **2 KB** になりました。 画面ごとに最大128個のスプライトを表示することができます。 したがって、エンジンごとに1KBが割り当てられます。

次に、OAM はタイルとパレットのみを使用していたのとは対照的に、**VRAM**からビットマップを参照できるようになりました。 これはタイリングシステムの新たな進化の一つです。 実際、このオプションは個々のスプライトごとに設定されているため、両方のスプライト'モード'は同じフレーム内に共存することができます。

#### 結果 {.tab}

![すべてのレイヤーが統合したけど... なにか足りなくない？](mario/halfcomplete.png) {.tab-float.pixel}

各レイヤーがレンダリングされると、最終段階ですべてを統合し、選択した画面に送信されます。 これは、これまでのPPUを搭載したゲーム機ではよくあることだが、もうこれで終わりということなのだろうか？

いいえ、違います！ 'メイン' は最も強力な別のエンジンからレイヤーを取得する必要があります。

### 3D アクセラレーター {.tabs-close}

ニンテンドーDSをプレイしたことがある場合、このコンソールは *特定の* 3Dグラフィックスを表示できることがわかります。 一部のGBAゲームとは異なり、これらはCPUによって処理されません。 代わりに、CPU-NTR には **3D エンジン** を構成する **2つのコンポーネント** が含まれています。 不思議なことに、任天堂のデザインは [SGIのRCP](nintendo-64#graphics)を思い出させます。

「背景の表示モード」セクションを再確認すると、どのモードにも少なくとも一つの静的背景があることがわかります。 3Dエンジンによって生成されたグラフィックスでそのレイヤーを埋められるからです。 唯一の注意点は、'メイン'だけがこれを行えるということです。これが、モード6が"メイン"でしか利用できない理由の1つです。

#### ジオメトリ エンジン {.tabs.active}

![ジオメトリエンジンのアーキテクチャ](gpu/geometry.png) {.tab-float}

4世代機や5世代機の記事を読むと、疑問に思われるかもしれません。 「[SIMD プロセッサ](sega-saturn#graphics) はどこに行った？」 これは良い質問です。ARM9はベクトル演算が得意でない上、専用の除算器をもってしても十分ではないのです。 そのため任天堂は、**ジオメトリ エンジン**と呼ばれるコンポーネントを組み込みました。このエンジンは、**頂点変換**、**投影**、**ライティング**、 **クリッピング**、**カリング** そして、透明度機能を適切に使用するために不可欠な**ポリゴンのソート**を担当します。

このエンジンにはいくつかの制限があり、特に処理できるポリゴンの数に関して厳しい制限があります。追加の**248KBのRAM**が利用可能で、最大で**2048個の三角形または1706個の四角形**を格納できますが、ポリゴンストリップを使用することで制限は緩和されます(個々のポリゴンを扱う場合と比較して) 。 この数字を理解するには、以前の記事の「インタラクティブモデル」のセクションをチェックすることをお勧めします。 このコンソールの画面解像度もはるかに小さいことを忘れないでください。

とにかく、このエンジンは、CPUまたはDMAからのデータで満たされた **コマンドFIFO** を使用して動作します。 FIFOは256個のエントリを保存しますが、 **PIPE** と呼ばれるもう1つのバッファがあり、4つのコマンドを追加で格納します。(合計260コマンド)。

#### レンダリングエンジン： {.tab}

![レンダリングエンジンのアーキテクチャ](gpu/rendering.png) {.tab-float}

レンダリングエンジンは、ベクトルをピクセルに変換(ラスタライズ)、着色(テクスチャマッピング)、ライティングやその他のエフェクトを適用する役割を担います。 テクスチャと光を補間するためには、 **パースペクティブ補正** と **グローシェーディングシェーディング** を使用します。 さらに、このユニットは**フォグ**、**アルファブレンディング**、**深度バッファリング** ( [Zバッファリング](nintendo-64#modern-visible-surface-determination) または Wバッファリングと呼ばれる変種)、**ステンシルテスト**、**アンチエイリアシング**などの現代的な機能を提供します。 ただし、アンチエイリアシングの機能は非常に原始的です (ポリゴンの外側のエッジを透明に設定するだけ) 。

レンダリングシステムは古いものと新しいものが混在しています: フレームバッファへのレンダリングの代わりに、 **ラインバッファレンダリング**を採用しています。 この方法はスキャンライン(2Dエンジンと同様) を満たし、結果をより小さなバッファに格納します。 これは3Dエンジンが2Dの描画と同じペースで動作しなければならないためです。

従来のフレームバッファがないため、ラスタライザは **スキャンラインレンダリング**によって、各スキャンラインを横断して範囲内に見つかるポリゴンのエッジを処理します。 Arisoutra氏 ( MelonDS emulator の開発者) は、レンダラーは一つの四角形ごとに**1スキャンラインあたり1スパンのみ**を塗りつぶすことができると報告しています [@graphics-arisotura]。 この仕様によって、例えば四角形が凹型であったり、エッジが交差している場合に問題が発生します。

エフェクトに関しては、**シャドウイング**と**トゥーンシェーディング** ( [ セルシェーディング](gamecube#creativity)の別名) という特徴的な機能も提供します。このユニットは[プログラマブル](xbox#importance-of-programmability)ではありませんが、ライティングのパラメータを変更することでカートゥーン風の効果を実現できます。

#### 結果 {.tab}

![ああ、それはそうかもね。](mario/complete.png) {.tab-float.pixel}

表示用のフレームバッファに結果を書き戻す代わりに、レンダリングエンジンは**カラーバッファ**と呼ばれるブロックに書き込みます。これは最大48スキャンラインを格納できます。 各スキャンラインは2Dエンジンによって取得され、FIFO方式でBG0レイヤーを満たします。

3Dレンダリングは2Dよりも先に開始され、後者が必要に応じて新しいレイヤーに変換を適用できるようにします。 "メイン"は2D、3D、または生成したフレームをキャプチャし、VRAM内の別のフレームとブレンドして、その結果をVRAMに書き戻して後で表示することもできます。

制御に関しては、レンダリングエンジンはフレーム処理の途中でパラメータを変更することもできます。これは、古い状態のコピーを保持するダブルバッファリングに基づくメカニズムを使用しているため、現在のフレームが描画されるまで変更されないことを利用して実現しています。 したがって、画面のちらつきは発生しません。

### 別のコンソールとの比較 {.tabs-close}

このコンソール用にリリースされた最初のゲームのいくつかは、別のゲーム機(ニンテンドー64) のものに似ています。 そこで、2つのバージョン間の相違点を簡単にまとめました。

#### 例1 {.tabs.active}

![スーパーマリオ64(1996).<br>320×240ピクセルでレンダリング.](comparison/mario_n64.png) {.toleft.pixel}

![スーパーマリオ64 DS (2004).<br>256x192ピクセルでレンダリング。](comparison/mario_nds.png) {.toright.pixel}

#### 例2 {.tab}

![マリオカート64 (1996). <br>320×240ピクセルでレンダリング.](comparison/kart_n64.png) {.toleft.pixel}

![スーパーマリオ64 DS (2004).<br>256x192ピクセルでレンダリング.](comparison/kart_nds.png) {.toright.pixel.tabs-close}

フォーラムの参加者の意見に基づいて、これらの違いが発生する理由のいくつかを挙げます。

- ***NDSのテクスチャはより***カクカク******に見える → レンダリングエンジンはフィルタを使用せず、 テクスチャは最近傍補間されるため。
- ***NDSのテクスチャはより ***鮮やか******に見える → レンダリングエンジンが [4 KB TMEM](nintendo-64#tab-3-2-texture-memory)によって制限されていない。 代わりに512KBのVRAMを利用(圧縮機構を含む) し、より多くのデータを読み込むことができるため。
- ***NDSのモデルは ***ピクセル化エッジ******を含む → NDS モデルは、N64 と比較して低解像度でレンダリングされるため。
- *NDSのテクスチャは遠くから見ると **歪んで**みえる。* → ラスタライザは [固定小数点](playstation#missing-units) 座標を操作する。 低解像度であることと、ミップマッピングの欠如がエイリアシングに影響しているためだと考えられる。

これはあくまで大まかな話ですが、より専門的なケースについては両方のエンジンを深く掘り下げ、両方のプログラム内で使用されている関数等を分析し、調査する必要があります。

### インタラクティブ モデル

あなたのGPUを使用してニンテンドーDSのモデルを視覚化する「モデルビューワー」を、最近傍補間を適用して表示するように改修しました。

![ニンテンドッグス (2005).<br>750 ポリゴン.](dalmatian_ds){.toleft model3d="true"}

![New スーパーマリオブラザーズ (2006). <br>636ポリゴン.](mario_ds){.toright model3d="true"}

グラフィックサブシステムの多くの制限について話しましたが、多くのゲームはこれを非常に上手に活用しています。

## オーディオ

オーディオの改善の多くは、GBA [で特徴づけられた](game-boy-advance#audio) PCMチャネルの強化によるところが大きいです。 GBAがPSGよりもソフトウェアシーケンシングを優先したことはGBAの記事で説明しましたが、その結果はかなり印象的でした。

![ラストウィンドウ 真夜中の約束 (2010). 混合ステレオ出力を表示しています。](window){.open-float video="true"}

新しいオーディオシステムは合計**16のPCMチャンネル**を搭載しており、ミキシング作業をハードウェアで行います。 PCMサンプルは **8ビット** (*GBAスタイル*)、 **16ビット** (最適な解像度) または **ACPCM** (圧縮形式) のいずれかにすることができます。 いずれの場合でも、ミキサーはスピーカー (現在のステレオ) またはヘッドフォンで再生できるステレオ信号を生成します。 また、結果のステレオデータはWRAMに書き込むことができ、サブプロセッサ (ARM7) がリバーブなどの効果を適用することを可能にします。

{.close-float}

これは、Nintendo DSがエンコードされた音楽を再生できるということでしょうか (例えば MP3)? まさしく、その通りです (実際、多くのhomebrewプログラムは何らかの形で実装しています) 。しかしオーディオデコーディングは多くの帯域幅と処理能力を消費します。 したがって、オーディオシーケンシングは最も実現可能なオプションとして、その地位を保持しています。

### 風変わりなPSG (2つもある)

このコンソールがGBAゲームを実行するときには、GBAのPSGを再現する何かしらの機能 (ハードウェアまたはソフトウェアを通じて) を持つべきです。 実際、最後の6チャンネルには"PSG モード"が含まれており、パルスまたはカスタム波を合成することができ、そのうち2つはノイズを生成できます。 しかし、GBAゲームはこれらのいずれも使用しないのです!

ご覧の通り、ミキサーの出力周波数は **32 kHz**、分解能は**10-bit**です (供給されるサンプルよりもかなり品質が低い) 。 そればかりか、この精度の低下を軽減するための補間は**一切行いません**。 これらの制限によって、サンプルにはノイズが加えられてしまいます。 ただし、この現象を認識できるかどうかはあなたの聴力によります(私は音量を上げた上で16ビットバージョンの音声と比較しなければ、このノイズに気が付きませんでした) が、それでも8ビットでサンプリングした音声をソフトウェアでミックスしていた頃に比べれば一歩前進です。 逆に、信号をダウンサンプリングするとオリジナルのPSGのトーンを歪ませる高調波が発生する可能性があるため、PSGサウンドではエイリアシング効果の悪影響に一層気を付ける必要があります。 それにもかかわらず、New スーパーマリオブラザーズのようなゲームは伴奏としてパルス波を上手に利用するので、私はPSGを完全に無駄なものだとは考えていません。

本題に戻りましょう。GBAゲームはこれらをどのように利用するのでしょうか? _答え: 使用しない。_ 任天堂はGBAモード用に**別のサウンドシステム** (同じ筐体内) を実装し、GBAの仕様に従う独自のチャンネルとミキサーを含むようにしました。 このようにして、GBAゲームが新しいミキサーの制限の影響を受けないようにしています。 残念ながら、このサブシステムはDSから分離されているので(言い換えれば、DSのミキサーには出力されないということ) 、DSゲームからは使用できません。

### インタラクティブな比較 {.interactive-only}

新しいオーディオシステムが新世代のサウンドトラックに与えた影響を比較できるように、このインタラクティブなウィジェットを構築しました。 各ウィジェットは同じタイトルを再生しますが、古いものと新しいものの間で交互にすることができます (違いを聴き分けるため、ヘッドフォンを着用することをお勧めします)。 試してみてください!

::: {.subfigures .side-by-side figure="false"}

![**GBA:** 逆転裁判 (2001, 日本語版). <br>**NDS:** Phoenix Wright: Ace Attorney (2005).](){audio_switcher="true" src1="trial_gba" src2="trial_nds" label1="GBA" label2="NDS" .toleft}

![**GBA:** マリオカートアドバンス (2001).<br>**NDS:** マリオカートDS (2005).](){audio_switcher="true" src1="yoshi_gba" src2="yoshi_nds" label1="GBA" label2="NDS" .toright}

:::

(聞こえない場合は、使用しているブラウザとデバイスの情報と合わせて [連絡してください](code>r ref(about_url, root=TRUE)</code))

余談ですが、このウィジェットの実装ではGBAサウンドトラックのゲインを少し上げて大きさを正常化する必要がありました。これは信号対雑音比に影響を与える傾向があります(2つを切り替える際に頭に入れておくべきです)。 とにかく、サウンドサブシステムがどのように進化してきたのかを感じ取っていただければ幸いです。

### いくつかの困難 {.interactive-only}

NDSが再現できなかったGBAの独特なオーディオ機能がいくつかありますが、ここでは紹介だけにとどめておきます。

::: {.subfigures .side-by-side figure="false"}

![**SNES:** スーパーマリオカート (1992).<br>**NDS:** マリオカートDS (2005).](){audio_switcher="true" src1="mario_snes" src2="mario_nds" label1="SNES" label2="NDS" .toleft}

![**Mega Drive:** ソニック3Dブラスト(1996).<br>**NDS:*ソニッククロニクル* (2008).](){audio_switcher="true" src1="sonic_megadrive" src2="sonic_nds" label1="Mega Drive" label2="NDS" .toright}

:::

最初の音 (特に最後の10秒) を聴いてみてください。 スーパーファミコンの[S-SMP](super-nintendo#audio) 独特の音声は再現が難しいようです。

2つ目の音声はあえて紹介したんですが、*なんだこれ*？ この新しいアレンジは、まるでAtari用に作られたかのように聴こえます。 ニンテンドーDSは、FM音源のPCMへの再サンプリングが処理できると思うのですが... あえて*情報量を落とす*という創造的なアプローチをとったのでしょう。

## I/O

簡潔に言えば、I/O はARM7 によって厳格に処理されています。 実際、データの往来を除いて、そのCPU内部で起こっていることのほとんどは観測できません... これは本当に最悪です。ここの意味はまだ分かってないのであとで変更すること。

### カートリッジとメモリへのアクセス

3つのコンポーネントが外部メモリインタフェースと繋がっています。**スロット1**（ニンテンドーDSカードが入る）、**スロット2**（GBAカートリッジやアクセサリが入る）、そして**4MBのPSRAM**（メインメモリ）だ。 インターフェイスは両方の CPU からアクセスできます。 しかし、同じバスから2つのリクエストが同時にある場合に、1つのCPUを優先するように変更できるレジスタが含まれています。

![データバス幅を付記した外部メモリモデル](memoryaccess.png) {.open-float}

重要な点として、DSカートリッジは**メモリーマップトでない**ため、どちらのCPUがゲームデータを読むにしても、読む内容はRAMにコピーしなければなりません。 この動作は、まず32ビットアドレスを参照する8ビットコマンドのカートリッジブロックに送信することから始まります。 その後、32ビットレジスタまたはDMAを介してデータを手動で取得することができます。 データバスは8ビット幅ですが、データ転送速度は最大 **5.96 MB/秒** に達します(任天堂が主張した公称値)。

保存に使用される「バックアップ」チップ: (例えば EEPROM, FLASHまたはFRAM) は、独自のコマンドセットを使用し、24ビットのアドレスバスに接続されたSPIバス(シリアル) を通じてアクセスされます。

{.close-float}

スロット2カートリッジは、元のピンアウトを使用してメモリマッピングされています。 しかし、拡張機能を提供するハードウェア(余分なRAM、rumbleなど) に対応するため、アドレスはDSモードでシフトされます。 GBAと同じように、ROMバスは16ビット幅でRAMバスは8ビット幅です。

### ペリフェラル

ARM7は、下画面を操作する**タッチスクリーンコントローラー**(抵抗膜型で、スタイラスの使用を要する)、フラッシュメモリ(*ファームウェア*が保存されている場所、詳細は後述) と接する別のSPIノードにも接続されています。

![ウィッシュルーム 天使の記憶 (2007) に登場するスイッチボードパズル。 この子を救出するために、プレイヤーは2本の指で同時に画面をスワイプしてライトを点灯させなければならない。](puzzle.png){.tabs-nested .active .open-float .tab-float title="パズル"}

![でも、間違ってしまうと...](puzzle_fail.png){.tabs-nested-last title="失敗"}

興味深いことに、このタッチスクリーンにおいては、X/Y座標の検出以外のことが可能なことです。 **対角位置**( 圧力がかかっている領域。「圧力値」の計算に使用される) を返すこともできます。 残念ながら、この機能は **公式SDK**では公開されていません。 私の知る限り、この文書化されていない機能(自家製を除く) を使用するゲームはありませんでした。

多くの人が、「ウィッシュルーム 天使の記憶 」がパズルの1つでこの機能に依存していると指摘しています。 同時に2本の指を使う必要がありました しかしこれは、事実ではありません。 もしもno$gba デバッガを使う場合には、圧力値データを利用することはできません。 その代わりに、x/y値が交互に大幅に変化するかどうかをチェックする方法があります。 こうすることで、ユーザーが2本の指で画面を押したかのように解釈されます。

{.close-float}

他にも、 **リアルタイムクロック** または「RTC」が存在します。

### 無線ネットワーク

コンソールには、2.4 GHz帯で動作する **ワイヤレスコントローラ** が含まれており、いくつかの革新的な機能を提供します。

- **インターネットプレイ**: 標準Wi-Fi接続を使用したLANネットワークに接続できます。
- **マルチカード プレイ**: 最大16台のコンソールが独自のプロトコルを使用して相互に通信します。
- **シングルカードプレイ**: ゲームはゲームカードを含まない別のDSにプログラムをアップロードすることができます。

## オペレーティング システム

この世代では、すべてのコンソールにある種のインタラクティブなインターフェイスがバンドルされています。 NDSは引き続き、I/Oアクセスを簡素化する軽量APIで構成された従来のオペレーティングシステムモデルを継承しています。 しかし、いくつかの設定やフィドルを調整するための最小限のユーザーインターフェイスを提供します。

とはいえ、オペレーティングシステムは複数のチップに分散しているので、まずはブート時に読み取られるものから説明しましょう。

### エントリポイント

ある時点で、ARM7とARM9はハードウェアを初期化する必要があります。NTR-CPUには2つの異なる小さなROMチップが含まれています。

- ARM9のバスに接続された**4 KB BIOS**
- ARM7のバスに接続された **16 KB BIOS**

コンソールが起動すると、各 CPU がそれぞれの ROM から起動します。 これは、**リセット・ベクタ**が各チップに設定されているためです(参考までに、ARM9のベクタは`0xFFFF0000`にあり、ARM7のベクタは`0x00000000`です)[@operating_system-boot]。

その後、各BIOSには、ブートコードと割り込みコールの2セットのルーチンが格納されます。 後者には [以前のコンソール](game-boy-advance#anti-piracy--homebrew)の面影が残っています。 しかし、前者はより複雑になりました。ハードウェアの初期化に加えて、 ARM7のコードはDSカートリッジでいくつかのチェックを実行することによってセキュリティ処理を行います(挿入されたものがある場合)。

ブートコードを実行した後、両方のCPUは'単一のマシン'として動作を開始するために同期します。実際にはARM9がARM7よりもずっと早く読み込みを完了するので、その際にARM9はARM7に4ビットの値を送信してから、ARM7が応答するのを半無限ループで停止し、ARM7から応答が返ってくると両者が同時に「スタートラインに立つ」、つまり同期された状態になります。

### 機会の窓

DSを持っているか、あるいは持っていた人は、ゲーム機の電源を入れる前にカートリッジが挿入されていれば、ゲーム**しか**できないことに気づいただろう。 これは、ARM7のBIOSがブート中にカートリッジをいくつかチェックし(詳細は最後のセクションで説明します) すべてのテストが合格した場合に発生するためです。 ARM7のゲーム実行ファイルがWRAMにコピーされ、ARM9の実行ファイルがメインメモリにコピーされます。

何らかの理由で実行可能ファイルがコピーされない場合 (起動中にカートリッジが無効または検出されないため) ゲームを開始することはできません。ユーザーはゲームをプレイするためにコンソールをリセットする必要があります。

### インタラクティブシェル

ゲームがあろうとなかろうと、システムはインタラクティブシェルをロードして起動します。 これは外部 **256 KB Flash** メモリ [@operating_system-firmware] に存在するプログラムです。

![ホーム画面](shell/home.png){.tabs-nested .active .open-float .tab-float .desktop-margined title="ホーム"}

![DSを起動するたびにこの画面が表示される。 '任天堂'のロゴは有効なカードが挿入されている場合に表示される。](shell/welcome.png){.tab-nested title="スプラッシュ"}

![設定画面](shell/settings.png){.tabs-nested-last title="設定"}

同じチップは、いくつかのユーザー設定(言語、ニックネーム、誕生日) とともにファームウェア、 アラームとウェルカムメッセージといくつかのシステム設定 (タッチスクリーンキャリブレーション、最初の起動フラグ、ファームウェアバージョンとWi-Fi設定) を格納します。

このシェルは、同時代のものとほとんど同じです。 ユーザーはゲームを開始し、設定を変更し、ゲームをダウンロードします('ダウンロードプレイ')。 **Pictochat**: 近くのニンテンドーDSと会話するオープンチャットルーム。

読み取り専用と書き込み可能な両方のデータが同じ再書き込み可能なチップに存在することは強調すべき価値があります！理論的にはファームウェア上に書き込むことが可能です。 幸いにも(または明白な理由のため) 任天堂は、 `SL1`と呼ばれるマザーボードのある点にジャンパーを配置することによって、チップの上部(64 KB) が書き込まれないように保護しました。これはバッテリーコンパートメントを外すことで露出します。 それでも、残りのフラッシュメモリを上書きしてしまえば、壊滅的な結果を引き起こす可能性があります [@operating_system-bricker]！

{.close-float}

### 更新性

最後にひとつ。このファームウェアは、セキュリティ上の脆弱性を修正するために任天堂によって数回(正確には5回) アップデートされました。 このアップデートはユーザーによってインストールすることはできませんでした ( `SL1` 保護を思い出してください)。 代わりにニンテンドーは次のロット製造の際に、更新されたファームウェアを埋め込みました。

## ゲーム

あぁ、これについては説明したいことがたくさんあります！ このコンソールの可能性が、多くのプログラマーやアーティストの革新的なアイデアを想起させたからです！ じゃあ何から話そうかな...

### ゲームの通常形態

このコンソールは3つのソースからゲームを実行します。そのうち2つだけがハードウェアを「フル」に利用することができます。

![小売店で販売されるゲームの例](game.jpg) {.open-float}

- **NDS または 'Slot-1' カートリッジ**: ネイティブDSゲームをロードするために使用されるメインメディアです。 一般に流通した唯一の媒体です。
- **GBA、または「スロット 2」カートリッジ**: このスロットでは、ゲーム コンソールでゲーム ボーイ アドバンス ゲームをネイティブにプレイできます。また、スロット 1 のゲームもこのオリジンにアクセスできるため、**拡張カートリッジ**を接続して NDS ゲームを拡張できます。. これらは、より多くのRAM、より多くの入力制御やフィードバックデバイス(例: *rumble pack*)などを提供します。
- **ダウンロードプレイ または 'ワイヤレス マルチブート'**: これは、NDS ゲームを使用して、別のコンソールにワイヤレス通信を使用してプログラムをアップロードすることを可能にするオリジナルの [マルチブート](game-boy-advance#accessories) の進化したバージョンです。 ダウンロードしたコンテンツはWRAMに保存され、転送が完了した後に起動されます。 WRAMは揮発性のため、シャットダウン時にデータが失われます。
  - 認定小売店で提供された **ダウンロードステーション**では、この機能を使用され、ユーザーがゲームの体験版をダウンロードできました。

{.close-float}

### プログラムの構成

BIOSはARM9とARM7の別々のコードで分割する必要があることを見てきました。 ゲームでも同じようなことが起きます。 したがって、NDSカードは以下の領域で構成されています:

- **ヘッダー (4 KB)**: メタデータ (各実行ファイルの場所、シリアル番号など) が含まれます。
- **セキュアエリア (16 KB)**: コピープロテクト目的で使用されます。 この記事の最後のセクションで詳細を説明します。
- **メインコンテンツ (可変サイズ)**: 残りの領域には実行可能ファイルとゲームデータ (グラフィック、サウンドなど) が含まれています。 任天堂のSDKを使用した小売ゲームには、組み込みのファイルシステムによりファイルやディレクトリと階層的に整理されたデータが含まれています。

### 開発環境

このコンソール向けのゲーム開発に興味を持つゲームスタジオに対して、ニンテンドーは多くのユーティリティを備えたハードウェアキットとSDKの両方を配布しました。

#### ハードウェア {.tabs.active}

**IS-NITRO-EMULATOR**と呼ばれる開発キットは、DSの内部ハードウェアとI/O [@games-emulator] のほとんどを含む中くらいのサイズの青い箱で構成されていました。 この箱から出ている太いケーブルには、'コントローラ'とディスプレイの機能を持つダミーのニンテンドーDSが接続されています。 後期型では、オーディオ/ビデオアウト、Wi-Fi(デフォルトではEthernetを使用してのエミュレート) やデバッグなどのオプション機能が強化されました。 私は後者が同梱されることを期待していましたが、これらのユニットはテストチーム(著者の?) でも使用できることに気づきました。

キットはDSカードを読み込みますが、大きなケースや、交換可能なバックアップチップとは異なるタイプです。 これらのカードは、 **IS-NITRO-WRITER** と呼ばれる別のユニットを使用してフラッシュされます。

#### ソフトウェア {.tab}

このソフトウェアキットには、devkit、C/C++ ツールチェーン、およびハードウェア API との相互作用のためのユーティリティが含まれていました。 ここで言及すべき点は、このドキュメントではハードウェアに直接アクセスするために彼らのAPIを回避することを明示的に禁止していることです。**ゲームはベアメタル上で実行されます**が、任天堂は'禁止された'操作を実行するゲームの配布を承認しません。 この禁止された操作には、ARM7を直接制御したり、ディスプレイ解像度を超過したり、LCDをオフにしたりすることも含まれます。

品質を維持することなどを考えると、これらの規範を課す理由は理解できます。 しかし、私の意見では、ARM7をI/Oタスクに限定することは、可能性を狭めてしまう愚策でした。

### インタラクションの自由さ {.tabs-close}

![脳を鍛える大人のDSトレーニング (2005).<br>この新しいカテゴリーのゲームは青少年以外の顧客を魅了した。](kawashima.png) {.open-float}

新しい形のインタラクションが可能なコンソールであったため、開発者にはグラフィックの良さよりも、まったく新しいゲーム体験を提供する自由が与えられていました。

コンシューマ向け電化製品で初めて、タッチスクリーン、マイク、Wi-Fi、リアルタイムクロックがパッケージされたコンソールです。 それにもかかわらず、いくつかのゲームは、コンソールを横向きに保持するようにユーザーに指示するなど、インタラクションの新しい形を提示しました。

{.close-float}

### ネットワークサービス

[以前の同業他社](xbox#network-service)の成功後、任天堂はオンラインマルチプレイヤーゲームに参入し、中央集権型のインフラを展開しました。 'インターネットプレイ'ができるゲームは任天堂のサーバー(**ニンテンドーWi-Fiコネクション**と呼ばれる) に接続し、オンラインゲームを楽しむことができます。

## 海賊版対策とHomebrew

DSカードが*CDの呪い*の影響を受けなかったとはいえ、任天堂はゲームの流通をコントロールし続けるためにいくつかの保護システムを導入しました。

### セキュリティの仕組み

それでは、それぞれのセクションを見てみましょう。

#### 暗号化システム {.tabs.active}

ニンテンドーDSは主にメモリインターフェースとスロット1カード間の通信を暗号化するために対称暗号化システムを使用しています。 暗号化の実行方法について話し合う前に 使用されるアルゴリズムと、(暗号化を実行するために使用される) キーがどのように生成されるかについて話しましょう。

カードの「ヘッダー」領域には**ゲームコード**(ゲームの一意識別子) と呼ばれる値が含まれており、メモリインターフェイスはこのブロックを取得して**KEY1**を生成します。このKEY1は、カードに送信されるコマンドをさらに暗号化するために使用します。 KEY1 暗号化は **Blowfish アルゴリズム** に基づいています。

その後 KEY1 は、内部クロックやカートリッジヘッダーのその他の値と混合して、 **KEY2** という新しいキーを生成します。 KEY1との根本的な違いは、ランダムな値を使用して予測不可能にすることです。 KEY2暗号化は、データを難読化するために複数のXOR演算およびShift演算に依存します。

KEY1とKEY2は、スロット1インターフェースがカードとの通信を保護するために、異なる段階で使用されます。 また、KEY1はARM7 BIOSによってDSカードを検証し、カードインターフェースを初期化するためにも使用されます。

#### DSカードの認証 {.tab}

前述したとおり、BIOSには起動時にNDSカードを検証するいくつかのルーチンが含まれています。 これは以下のように動作します。

1. ARM7 BIOSがカートリッジのチップIDを取得し、RAMに保存した後、KEY1およびKEY2暗号化を有効にします。
2. 'セキュアエリア'の最初の2KBがランダムな順序でRAMにコピーされます。 この領域の最初の8 Bは、 **セキュア エリア ID**と呼ばれる文字列を格納します。その次の領域からは、いくつかのチェックサム(CRC16タイプ) とその他のメタデータが続きます。 これらはすべてKEY1で暗号化されており、特にセキュアエリアIDは異なるパラメータを使用してKEY1で2回暗号化されます。
3. ARM7 BIOSはセキュアエリアIDを復号し、復号された値が`encryObj`と一致するかどうかを確認します。 もし一致していれば、カード検証の**第一テストを通過した**ことになります。 その後、文字列はアルゴリズムを明らかにしないように破壊されます。 検証に失敗した場合、セキュアエリアの2KBはゴミデータで埋められ、カードの残りの部分が読み取られることを防ぎます。
4. 第二のテストは、ランダムなタイミングで再びチップIDを取得することです(ランダムシードは内部クロックに依存します)。 取り出したチップIDの値が保存しておいた最初のチップIDと一致する場合、 **2番目のテストは合格**です。
5. 最後に、セキュアエリアの残りの部分はランダムな順序で取得され、RAMに再構築されます。 この後、ファームウェアが実行されます。

もしすべてがうまくいった場合、ファームウェアはRAM内でカードの必要な実行ファイルを見つけ出し、ユーザはゲームを起動することができます。 そうでなければ、ゲームセレクターは灰色で表示されます。

#### ダウンロードプレイの保護機能 {.tab}

ダウンロードプレイ経由で受信したプログラムは、任天堂が [RSA署名](wii#tab-7-2-chain-of-trust) を使用して署名する必要があります(任天堂のみが秘密鍵を知っています)。

このチェックはファームウェアによって実行されます。

### 保護機能の敗北 {.tabs-close}

あなたがHomebrewのユーザーだった場合は、おそらくこのソフトウェアを実行するために利用可能なオプションを実行できます。 実際、現在のハック方法がすべて発見される以前は、ハッカーは任天堂の複雑な海賊版対策システムを回避するのに苦労していました。

#### 既存スロット2 {.tabs.active}

GBAサブシステムはセキュリティ保護が実装されていない([ロゴ・トリック](game-boy#anti-piracy)は除く) カートリッジをそのまま実行していたため、既存のGBAフラッシュカートはNDSと互換性がありました。 これによって、DSゲームに固有の新しい機能が使えないことを気にしなければ、GBA home brewを実行することが可能でした。

いつものとおり、フラッシュカートリッジでの海賊版ROMの実行ができます。しかし、任天堂はGBAの保護システムを変更できなかった(既存のゲームを使用不能にする可能性があるため) ので、ゲームの販売会社はそれを受け入れざるを得ませんでした。

#### 拡張スロット2 {.tab}

秘密裏にDSのBIOSとファームウェアの研究が行われた結果、NDSカードの実行がGBAスロットにリダイレクトされることが発見されました。 NDSカード自体はハックされていなかったのですが、この方法によって、スロット1のセキュリティシステムと**スロット2のプログラムの実行をDSモードで**行うことが可能になりました。

そのため、スロット2フラッシュ・カートリッジの新世代が市場に登場しました。 この新たなカートリッジには、スロット1からブートストラップされた、一度限り実行されるARM9の実行コードが埋めこまれています。 ブートストラップ自体は、以下の方法(「パススルーメソッド」と呼ばれる) の1つを使用して達成されました。

- **PassMe** カードの使用: スロット1カードが必要です。 PassMeはスロット1とゲーム本体のデータの仲介を行う、ある種のアダプターです。 基本的に、カードからコンソールに送られたヘッダを改ざんして、コンソールを騙してスロット2コードを実行し、スロット2カートリッジにロードします。
- **WifiMe**: 互換性のあるWi-Fiカードを搭載したPCが必要です。 変更されたドライバーを使用する場合 DSがDownload Play[@anti_piracy-wifime] を使ってダウンロードできるカスタマイズされたプログラムをブロードキャストすることが可能です。 一度起動すると、実行がスロット2にリダイレクトされます。 これは、ファームウェアがバイナリの特定の領域の RSA 署名をチェックしないことを悪用したものです。
- **FlashMe**: 永続的な解決策。 上記の方法を連続して行うことで、`SL1` 端末をブリッジします。 フラッシュカートリッジがあれば、スロット2から起動するために、ファームウェアを上書きできるhomebrewプログラムを実行することができました。 このハックは、ダウンロードプレイからhomebrewを起動するために、デジタル署名チェックも削除しました。

予想通り、ニンテンドーはこれらの改造に対して、パッチを当てたファームウェアの更新によるDSのバージョンアップを行いました。 なので、その後ハッカーはBIOSの利用に焦点を当てた他の方法を見つけようとしました(パッチを適用することは困難です)。

#### ネイティブスロット1 {.tab}

Martin Korthは、有名なニンテンドーDSエミュレータ「NO$GBA」の開発者です。 これを開発した後、BIOSを抽出し、スロット1セキュリティをリバースエンジニアリングしました。 これにより、ニンテンドーDSセキュリティの真のメカニズムが明らかになりました。 この記事でご覧になったように、誰かがリバースエンジニアリングしない限り、暗号化システムは機能しました(これが対称暗号化システムの限界です。 秘密鍵が保存されないRSAなどの非対称暗号化システムとは異なります) 。

それはともかく... これらは、あらゆる種類のコンソールで動作し、 **ネイティブ**なニンテンドーDSプログラム を実行した **プラグアンドプレイ-スロット1フラッシュカード**の大規模な流通につながりました。 本物のゲームを必要とせずにスロット2フラッシュカートをロードできる「NoPass」カードのデビューにより、パススルー方法も改善されました。

すべての既存の小売ゲームに影響を与えるような、破壊的な変更を加えなければ暗号化システムを変更できなかったので、任天堂は最終的にこの戦いに負けました。 唯一残された道は、以前のコンソールと同じように、法的な対応をとることでした。

#### 観察記録 {.tabs-close}

これは私の個人的な意見ですが、以前のコンソールの他のhomebrewメソッドと比べると、このフラッシュカードのシンプルさは本当に印象的です。 私の古い記事では、ユーザーがHomebrewプログラムや海賊版ゲームを実行したいと思った場合、最終的には虎穴に入るような複雑な方法に従わなければならない、と説明した。

DSの場合、違法のフラッシュカードは文字通り、ただただ小売り販売されていました。 ゲームスタジオが海賊行為に頼るのがどれだけ苦痛だったかを見るのは本当に心配でした。

もう一つは、市場に出回ったフラッシュカードの量(コピー品を除く) も驚きです。 技術的な観点から見ると、フラッシュカードは単なるSDカードアダプター[@anti_piracy-acekard] にすぎません。 あるカードを別のカードと区別する唯一の特徴は、ブートコードとSDカードリーダーです。 メーカーによっては、(カーネル/ファームウェアと呼ばれる) より優れたファイルブラウザを設計し、いくつかの追加ハードウェアを含むように努力していました。

## おわりに

![解析に使った_今_のマイDS。<br>実は最初に買ったものはずいぶん前に売ってしまったんだ... どこにいっちゃったのかな。](myds.jpg)

よし！ 話したかったことは99%話せたかな...

いくつかの部分を批判するときに、厳しく聞こえたかもしれませんが、本意ではありません。 誤解しないでください、私はこのコンソールをエンジニアリングの技術の結晶だと思います！ しかし、コスト削減のためか、デザインのミスなのか、疑問に思う箇所がいくつかあることも確かです。 それでも私が11歳の頃は、本当にDSが欲しかったのです。 任天堂はとても素晴らしい"使命"を果たしました!

また、友人やMeronDSコミュニティの方々にも、ドラフトをチェックし、 *多く* の修正点を指摘していただいたことに感謝します。 ニンテンドーDSは、私が前々から興味を持っていた多くのトピックを持っており、私の理解が及ばないのではないかと心配していました。この記事を楽しんでいただけたら幸いです。

それでは、また会いましょう！  
Rodrigo