_ _ _

Android Game Libraryは2D Android Game製作のために製作された独自のLibraryにlibgdx Libraryの構造を基に製作されたLibrary.

大きくLibraryの構造はファイルI/O, Granphics, Sound, Interface, User Input, Managerで構成。

_ _ _

<h2>構成</h2>
<ul>
<li style="padding-left: 0px;"><strong>File I/O</strong>
<ul>
<li style="padding-left: 0px;">Packageフォルダ拡張SDカードのI/O制御とAsset FolderでのFile Loadingに使用。</li>
</ul>
</li>
<li style="padding-left: 0px;"><strong>Graphics</strong>
<ul>
<li style="padding-left: 0px;">2D基盤グラフィック表現や下位互換性のためにOpenGL ES 1.0を基に制作される。</li>
</ul>
</li>
<li style="padding-left: 0px;"><strong>Sound</strong>
<ul>
<li style="padding-left: 0px;">Androidで提供されるSoundPoolとMediaPlayerを使用。</li>
</ul>
</li>
<li style="padding-left: 0px;"><strong>Input</strong>
<ul>
<li style="padding-left: 0px;">ユーザ入力を受けて処理し、処理可能な種類はKeyboard, Touch, Vibrate, Acceleration, Orientation などの入力を処理。</li>
</ul>
</li>
<li style="padding-left: 0px;"><strong>UI</strong>
<ul>
<li style="padding-left: 0px;">Android Layout機能をもとに当該Libraryで使用できるUI Systemを構築。</li>
</ul>
</li>
<li style="padding-left: 0px;"><strong>Manager</strong>
<ul>
<li style="padding-left: 0px;">提供される機能の管理およびアプリの制御を担当。</li>
</ul>
</li>
</ul>

_ _ _

### 構造と機能

#### File I/O

<img src="https://msh0411.github.io/assets/AndroidGameLibrary_03.jpg" alt="drawing" width="150"/>

파일을 읽고 쓰는 기능을 담당한다. Android는 자바를 사용하기 때문에 표준 방식인 InputStream과 OutputStream형태로 데이터를 읽고 쓴다.

주로 일시 정지된 게임의 데이터를 저장하거나, 랭킹정보, 게임의 데이터 처리를 위해서 사용된다.

FileIO라는 인터페이스를 사용하며 Android용 I/O는 AndroidFileIO에서 구현하고 있다.

File I/O는 크게 3가지 기능으로 분류된다.

 1. Asset에서의 파일 읽기.

 2. 설치된 Package 폴더 내 파일 생성 및 읽기 / 쓰기.

 3. 확장 SD 카드 내 파일 생성 및 읽기 / 쓰기.

엔진 라이브러리는 Android에서 제공하는 I/O 기능을 사용하지 않고 Asset폴더에서 자유로운 확장자의 파일들을 읽어 사용한다.

Android에서 제공하는 I/O 기능을 사용할 경우 일부 확장자의 이미지 파일이나 사운드 파일을 자동적으로 변환해버리기 때문에 데이터의 손실이나 화질에 차이를 보일 수 있기에 Asset 폴더를 통한 I/O 기능을 활용하고 있다.

#### Graphics

<img src="https://msh0411.github.io/assets/AndroidGameLibrary_04.jpg" alt="drawing" width="500"/>

Graphics는 Texture를 읽어 들여 화면에 표현하는 클래스로 OpenGL ES 1.0 을 지원한다. OpenGL ES 2.0을 지원하면 많은 효과를 적용하거나 최적화할 수 있는 부분들이 생기지만, 아직까지 많은 사용자가 원활하게 OpenGL ES 2.0을 지원하는 것이 아니기 때문에 호환성을 위해 OpenGL ES 1.0을 바탕으로 제작하였다.

<img src="https://msh0411.github.io/assets/AndroidGameLibrary_05.jpg" alt="drawing" width="300"/>

Texture는 Image 1장으로 대응되지 않고 여러 장의 Image를 한 장의 Texture에 저장하는 방식으로 사용하고 있다. Texture의 크기는 2의 승수로 맞추어 출력해야 제대로 화면에 표현된다. 하지만 모든 이미지가 2의 승수 크기로 만들어질 수 없는 부분이기 때문에 여러 장의 Image를 하나의Texture에 저장하여 각각의 위치를 판단하여 Image를 잘라서 사용하고 있다.

TextureGrid는 이러한 방법으로 저장된 Image의 좌표 정보 및 속성 정보를 가지고 있다. 저장된 TextureGrid는 Engine Library에서 지원하는 Resource Batch Tool에서 손쉽게 작업을 할 수 있으며, 해당 정보를 읽어 사용하고 있다.

<img src="https://msh0411.github.io/assets/AndroidGameLibrary_06.jpg" alt="drawing" width="400"/>

ImageBatcher는 생성된 Image를 배치하는 작업을 한다. 2D Image는 모두 사각형으로 생성되기 때문에 Vertex정보를 사각형 형태로 가지게 된다. 4개의 점으로 이루어지며 Image의 재사용성을 높이고 퍼포먼스 향상을 위해 같은 Texture를 사용하는 Image들을 한번에 그려 Texture의 bind() 횟수를 최소화 하고 있다. 또한 이러한 부분들이 번거롭거나 성능상의 문제가 없을 때는 사용자 편의를 위해서 Picture라는 Class를 제공하고 있다.

<img src="https://msh0411.github.io/assets/AndroidGameLibrary_07.jpg" alt="drawing" width="400"/>

Picture Class는 기본적으로 Image Class를 상속하고 있다. Image Class 처럼 ImageBatcher를 사용하지 않고 생성 및 옵션 설정만으로 자동으로 그려지게 되어 있다. 생성된 Picture Class는 RenderManager에서 자동적으로 그려주며, Order값에 따라 그려지는 순서가 정해진다.

하지만 Picture Class는 Image Class처럼 퍼포먼스 향상을 위한 bind() 최소화가 되어 있지 않기 때문에 많은 량의 같은 Image를 그릴 때는 적절하지 않다. 이러한 부분을 해결하기 위해서 IRender라는 Interface를 제공한다.

<img src="https://msh0411.github.io/assets/AndroidGameLibrary_08.jpg" alt="drawing" width="400"/>

IRender Interface를 Implements 하게 되면 Render() 함수가 추가되고, 주어진 함수에 ImageBatcher로 최적화하여 작성하면 된다. 작성된 Render() 함수는 RenderManager에서 관리되며, 자동으로 그려지게 된다.

Render()함수 내의 그리기 순서는 그린 순서를 따르게 되며, Render()함수 자체의 순서는 Order값에 따라 적용된다.

<img src="https://msh0411.github.io/assets/AndroidGameLibrary_09.jpg" alt="drawing" width="150"/>

작업을 하다 보면 충돌박스나 UI의 위치, 혹은 Test Image가 없을 때 가시적인 확인이 필요할 때가 있다. 이러한 부분을 번거롭지 않게 해결해주기 위해 DebugRenderer를 제공한다. Config Class에서 _Debug 값이 True일때만 적용이 되며, 추가적인 Image가 없어도 임의의 색상의 박스가 화면상에 표현된다.

#### Sound


