_ _ _

## GP2X

<img style="display: block; margin-left: auto; margin-right: auto;"  src="https://msh0411.github.io/assets/Gp2xWiz.jpg" alt="drawing" width="500"/>

GP2Xは(株)ゲームパークホールディングスで開発するビデオゲーム・コンソール兼用ポータブルメディアプレーヤーで、前作であるGP32の機能を極大化した後続機種。 (ただし両機器のプログラム互換性はない。) 2005年9月2日名前の公募展を通じてブランド名を確定し、同年11月10日に発売された。 以降、タッチスクリーンを追加したF-200と内部仕様が変更されたF-300が発売されており、後編作はGP2XWiz。

_ _ _

## ゲーム紹介
<img style="margin-left: auto; margin-right: auto;"  src="https://msh0411.github.io/assets/TheAlchemist_01.jpg" alt="drawing" width="150"/>
<img style="margin-left: auto; margin-right: auto;"  src="https://msh0411.github.io/assets/TheAlchemist_02.jpg" alt="drawing" width="150"/>
<img style="margin-left: auto; margin-right: auto;"  src="https://msh0411.github.io/assets/TheAlchemist_03.jpg" alt="drawing" width="150"/>  

The AlchemistはGP2X専用ゲームアプリとして弾幕シューティングゲームジャンルのゲーム。2人のキャラクターの中で一人のキャラクターを選択し、ステージを進行し、シナリオをクリアするゲーム。

_ _ _

## 構造

**GameView**  
<img style="display: block; margin-left: auto; margin-right: auto;"  src="https://msh0411.github.io/assets/TheAlchemist_04.jpg" alt="drawing" width="1000"/>  
 * 画面を表すシーンとして、実装した機能を画面上に表現、シーンの役割によって機能を分離して管理する。

**Object**  
<img style="display: block; margin-left: auto; margin-right: auto;"  src="https://msh0411.github.io/assets/TheAlchemist_05.jpg" alt="drawing" width="1000"/>  
 * ゲーム上に存在する全てのObjectを定義、GameObjectを相続してキャラクター、敵、ミサイルなどを実装、管理する。

**他**  
<img style="display: block; margin-left: auto; margin-right: auto;"  src="https://msh0411.github.io/assets/TheAlchemist_06.jpg" alt="drawing" width="300"/>  
 * ミサイル及びベクトル表現等の数学、物理を演算するためのクラス定義、Objectの種類等を管理するためのEnumを定義。

_ _ _
