Android Game Library のガイド文書でクラス構造と機能紹介について叙述。

1. 紹介

 * Android Game Libraryは2D Android Game製作のために製作された独自のLibraryにlibgdx Libraryの構造を基に製作されたLibrary. 大きくLibraryの構造はファイルI/O, Granphics, Sound, Interface, User Input, Managerで構成。

2.　構成

 * File I/O

  + Packageフォルダ拡張SDカードのI/O制御とAsset FolderでのFile Loadingに使用。

 * Graphics

  + 2D基盤グラフィック表現や下位互換性のためにOpenGL ES 1.0を基に制作される。

 * Sound

  + Androidで提供されるSoundPoolとMediaPlayerを使用。

 * Input
  
  + ユーザ入力を受けて処理し、処理可能な種類はKeyboard, Touch, Vibrate, Acceleration, Orientation などの入力を処理。

 * UI

  + Android Layout機能をもとに当該Libraryで使用できるUI Systemを構築。

 * Manager
 
  + 提供される機能の管理およびアプリの制御を担当。
