_ _ _

Android Game Libraryは2D Android Game製作のために製作された独自のLibraryにlibgdx Libraryの構造を基に製作されたLibrary.  
大きくLibraryの構造はファイルI/O, Graphics, Sound, Interface, Input, Managerで構成。

_ _ _

## 構成

 * **File I/O**  
Packageフォルダ拡張SDカードのI/O制御とAsset FolderでのFile Loadingに使用。

 * **Graphics**  
2D基盤グラフィック表現や下位互換性のためにOpenGL ES 1.0を基に制作される。

 * **Sound**  
Androidで提供されるSoundPoolとMediaPlayerを使用。

 * **Input**  
ユーザ入力を受けて処理し、処理可能な種類はKeyboard, Touch, Vibrate, Acceleration, Orientation などの入力を処理。

 * **UI**  
Android Layout機能をもとに当該Libraryで使用できるUI Systemを構築。

 * **Manager**  
提供される機能の管理およびアプリの制御を担当。

_ _ _

## 構造と機能

 * **File I/O**  
<img style="display: block; margin-left: auto; margin-right: auto;"  src="https://msh0411.github.io/assets/AndroidGameLibrary_03.jpg" alt="drawing" width="150"/>

ファイルを読み書きする機能を担当。 AndroidはJavaを使うため、標準方式のInputStreamとOutputStreamの形でデータを読み書きする。 主に一時停止したゲームのデータを保存したり、ランキング情報、ゲームのデータ処理のために使われる。 FileIOというInterfaceを使用し、Android用のI/OはAndroidFileIOで実装している。

File I/Oは大きく3つの機能に分類される。
  1. Assetでのファイル読み込み
  2. インストールされたパケットフォルダ内のファイル生成および読み込み/書き出し。
  3. 拡張SD カード内のファイル生成および読み込み/書き出し。

当該LibraryはAndroidで提供するI/O機能を使用せず、Assetフォルダから自由な拡張子のファイルを読み取って使用する。 Androidで提供するI/O機能を使う場合、一部の拡張子のImageファイルやSoundファイルを自動的に変換してしまうため、データの損失や画質に差異が見られるため、Assetフォルダを通じたI/O機能を活用している。

_ _ _

 * **Graphics**  
<img style="display: block; margin-left: auto; margin-right: auto;"  src="https://msh0411.github.io/assets/AndroidGameLibrary_04.jpg" alt="drawing" width="500"/>

GraphicsはTextureを読み込んで画面に表現するクラスでOpenGL ES 1.0を支援する。 OpenGL ES 2.0を支援すれば、多くの効果を適用したり、最適化できる部分が生まれるが、、まだ多くの使用者が円滑にOpenGL ES 2.0を支援することがないために互換性のためにOpenGL ES 1.0を基に製作した。

<img img style="display: block; margin-left: auto; margin-right: auto;" src="https://msh0411.github.io/assets/AndroidGameLibrary_05.jpg" alt="drawing" width="300"  />

TextureはImage 1枚に対応されず、数枚のImageを一枚のTextureに保存する方式で使用している。 Textureの大きさは2の乗数に合わせて出力してこそ、十分な画面に表現される。 しかし、すべてのImageが2の乗数の大きさで作られことができない部分のために数枚のImageを一つのTextureに保存して各Imageの位置情報をもとに、Imageを切って使用している。

TextureGridはこのような方法で保存されたImageの座標情報や属性情報を持っている。 保存されたTextureGridはLibraryでサポートするResource Batch Toolで簡単に作業ができ、該当情報を読み取って使用することができる。

<img style="display: block; margin-left: auto; margin-right: auto;"  src="https://msh0411.github.io/assets/AndroidGameLibrary_06.jpg" alt="drawing" width="400"/>

ImageBatcherは生成されたImageを配置する作業を行う。 2D Imageはすべて四角形に生成されるためにVertex情報を、四角形で持つようになる。 4つの点で行われ、Imageの再利用性を高め、パフォーマンス向上のために同じTextureを使用するImageを一気に描いてTextureのbind()回数を最小化している。 また、これらの部分が煩わしかったり、性能上の問題がない場合は、便宜のためにPictureというClassを提供している。

<img style="display: block; margin-left: auto; margin-right: auto;"  src="https://msh0411.github.io/assets/AndroidGameLibrary_07.jpg" alt="drawing" width="400"/>

Picture Class は基本的に Image Class を相続している。 Image ClassのようにImageBatcherを使わずに生成及びオプション設定だけで自動に描かれるようになっている。 生成されたPicture ClassはRenderManagerで自動的に描かれ、Order値によって描かれる順序が決まる。

しかし、Picture Class は、Image Class のようにパフォーマンス向上のための bind() 最小化されていないため、多くの量の同じ Image を描く時は適切ではない。 このような部分を解決するために IRender という Interface を提供する。

<img style="display: block; margin-left: auto; margin-right: auto;"  src="https://msh0411.github.io/assets/AndroidGameLibrary_08.jpg" alt="drawing" width="400"/>

IRender Interface をImplements するようになれば、Render()関数が追加され、与えられた関数にImageBatcher で最適化して作成すればよい。 作成されたRender()関数はRenderManagerで管理され、自動的に描かれるようになる。

Render()関数内の描き順はグリーンの順に従うことになり、Render()関数そのものの順序はOrder値によって適用される。

<img style="display: block; margin-left: auto; margin-right: auto;"  src="https://msh0411.github.io/assets/AndroidGameLibrary_09.jpg" alt="drawing" width="150"/>

作業を行うと、衝突ボックスやUIの位置、あるいはTest Imageがないときに可視的な確認が必要なときがある。 このような部分を煩わなく解決するため、DebugRenderを提供する。 Config Classで_Debug値がTrueのときのみ適用され、追加のImageがなくても任意の色のボックスが画面上に表現される。

- - -

 * **Sound**  
<img style="display: block; margin-left: auto; margin-right: auto;"  src="https://msh0411.github.io/assets/AndroidGameLibrary_10.jpg" alt="drawing" width="300"/>

Audio Interface は Music と Sound に分けられる。 AudioはMusicとSoundの生成だけを管理する。
生成されたMusicとSoundはResourceManagerで管理される。 基本的にAssetsフォルダにBGMフォルダとSEフォルダに入っているファイルを自動的に読み込んで使うことになる。 全体的なボリュームはAppManager で管理され、全体ファイルに適用される。
個別の適用のためには、Play時に個別の設定をしなければならない。

<img style="display: block; margin-left: auto; margin-right: auto;"  src="https://msh0411.github.io/assets/AndroidGameLibrary_11.jpg" alt="drawing" width="150"/>

MusicはBGMのように再生時間の長いストリーミングオーディオファイルに使われる。 主にBGM は Play されているファイルが同時的に複数である必要がないため、複数のファイルをロードしておく必要がない。 したがって、Libraryでは、Play中の背景音がある場合、再生を中止させ、新しい背景音を実行することになっている。 効果音のような多重使用ファイルに比べ、背景音はメモリを多く使いこなすため、正確な管理が必要である。 実質的な実装はAndroidMusic Class で実装される。 基本的にAndroid が提供する MediaPlayer を使用して再生を行っている。

<img style="display: block; margin-left: auto; margin-right: auto;"  src="https://msh0411.github.io/assets/AndroidGameLibrary_12.jpg" alt="drawing" width="150"/>

SoundはEffectのように再生時間が短く反復回数が高かったり、同時に使用されるファイルを再生する。 再生されるファイルはAndroidで提供されるSoundPoolで再生され管理される。
MusicのようにResourceManagerで自動的に追加と管理され、使用するファイル名を入力して使用することになる。

- - -

 * **Input**  
<img style="display: block; margin-left: auto; margin-right: auto;"  src="https://msh0411.github.io/assets/AndroidGameLibrary_13.jpg" alt="drawing" width="700"/>

Inputは大きく5つに分類される。 一般に画面に反応するタッチ操作と加速度、振動、方向、キー入力でサポートしている。 その他にも、追加的に様々な機能をAndroid でサポートしているが、センサー付きのハードウェアがあり、無いハードウェアが存在するため、全て追加しなかった。
個々のHandlerは独立して動作し、AndroidInputで管理される。 それぞれのHandlerから移ってきたメッセージをAndroidInputからPoolに順次保存し、メッセージを教えてくれる。
TouchHandlerの場合、SingleTouchとMultiTouchに分けられるが、Android 2.2バージョン以上のみMultiTouchを支援するために区分してTouchHandlerを支援している。

<img style="display: block; margin-left: auto; margin-right: auto;"  src="https://msh0411.github.io/assets/AndroidGameLibrary_14.jpg" alt="drawing" width="300"/>

SingleTouchの場合、画面に1つの点を認識することができ、MultiTouchの場合機器のスペックによって異なりますが、平均8つの点を認識する。 また、Androidで追加された様々なGesture機能を追加し、Touchの様々な反応を認識し活用できる。

- - -

 * **UI**  
<img style="display: block; margin-left: auto; margin-right: auto;"  src="https://msh0411.github.io/assets/AndroidGameLibrary_15.jpg" alt="drawing" width="500"/>

LayoutはUIが含まれる一つの空間であり、UI自体もLayoutを相続している。 大きく3つにLayoutを分けますが,LinearLayout、FrameLayout、UIに分けている。
LinearLayoutの場合、含まれるLayoutとUIは自動整列され、方向値を基準に整列される。 また、自動的なサイズ変更を進めるため、Layoutの大きさを考える必要がない。
FrameLayoutの場合、含まれたLayoutとUIが位置整列だけ進行され、それぞれのLayoutが重なって表現することができる。
UIの場合は多様な機能を果たすUI Classをサポートし、基本的な機能はFrameLayoutと同じだ。
Androidは機器毎の解像度が多いためにUI製作に当たり、多くの困難があるが、Libraryで支援するUIシステムは基準解像度800 X 480を基準に自動的にすべての解像度に合わせて位置や大きさ変更が行われる。
この機能を追加的に支援してくれる部分がCamera2D Classだ。

<img style="display: block; margin-left: auto; margin-right: auto;"  src="https://msh0411.github.io/assets/AndroidGameLibrary_16.jpg" alt="drawing" width="150"/>

Camera2D Classは、当該機器に支援される解像度を確認して、800 X 480に最も近接した画面に縮小させる。 この時、機器で支援する解像度の割合を維持するため、イメージの歪みや外れる現象はない。 ただ、基準解像度と異なる割合を維持するため、若干の余白があったり、カットされて見えることもある。 このような部分は製作当時、CameraのZoomの割合を0.8程度拡大して余白を見えないように製作すれば解決できる。
UIにはUIBox、UIButton、UIStaticText、UIProgressBar、UIListBoxの5つが支援されていて、後に追加されるものだ。
これらのUIはそれぞれの追加機能および特徴を持つが、下記の通りである。

<img style="display: block; margin-left: auto; margin-right: auto;"  src="https://msh0411.github.io/assets/AndroidGameLibrary_17.jpg" alt="drawing" width="700"/>

- - -

 * **Manager**  
<img style="display: block; margin-left: auto; margin-right: auto;"  src="https://msh0411.github.io/assets/AndroidGameLibrary_18.jpg" alt="drawing" width="500"/>

Libraryには、上記で一部紹介されているようにManager が存在する。 それぞれのManagerはResource管理、UI管理、Rendering管理などを担当している。
これらのManagerはAppManagerで統合的に管理され、Appで管理することになる情報を担当している。
それぞれのManagerはAppManagerで使用されているが、それぞれが独立したManagerであるため独自使用が可能である。

<img style="display: block; margin-left: auto; margin-right: auto;"  src="https://msh0411.github.io/assets/AndroidGameLibrary_19.jpg" alt="drawing" width="200"/>

GLGameはActivityを相続するため、Activityの役割をすることになり、別途のActivityを宣言しなくてもいい。
Screenは実質的な実装のための空間であり、Libraryで提供する機能を使って実装することができる。
基本的にUpdateやDraw、TouchEvent、KeyEventなどの処理を行うことになり、AndroidのLifeCycleに合わせて管理される。
Appの中止や一時停止、再スタートなどは当該関数部分で処理を実装すればよく、Resource管理はResourceManagerで自動的に処理するため、別途の管理を必要としない。
