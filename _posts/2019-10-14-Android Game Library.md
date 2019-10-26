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

파일을 읽고 쓰는 기능을 담당한다. Android는 자바를 사용하기 때문에 표준 방식인 InputStream과 OutputStream형태로 데이터를 읽고 쓴다.주로 일시 정지된 게임의 데이터를 저장하거나, 랭킹정보, 게임의 데이터 처리를 위해서 사용된다.
FileIO라는 인터페이스를 사용하며 Android용 I/O는 AndroidFileIO에서 구현하고 있다.

File I/O는 크게 3가지 기능으로 분류된다.
  1. Asset에서의 파일 읽기.
  2. 설치된 Package 폴더 내 파일 생성 및 읽기 / 쓰기.
  3. 확장 SD 카드 내 파일 생성 및 읽기 / 쓰기.

엔진 라이브러리는 Android에서 제공하는 I/O 기능을 사용하지 않고 Asset폴더에서 자유로운 확장자의 파일들을 읽어 사용한다.
Android에서 제공하는 I/O 기능을 사용할 경우 일부 확장자의 이미지 파일이나 사운드 파일을 자동적으로 변환해버리기 때문에 데이터의 손실이나 화질에 차이를 보일 수 있기에 Asset 폴더를 통한 I/O 기능을 활용하고 있다.

_ _ _

 * **Graphics**  
<img style="display: block; margin-left: auto; margin-right: auto;"  src="https://msh0411.github.io/assets/AndroidGameLibrary_04.jpg" alt="drawing" width="500"/>

Graphics는 Texture를 읽어 들여 화면에 표현하는 클래스로 OpenGL ES 1.0 을 지원한다. OpenGL ES 2.0을 지원하면 많은 효과를 적용하거나 최적화할 수 있는 부분들이 생기지만, 아직까지 많은 사용자가 원활하게 OpenGL ES 2.0을 지원하는 것이 아니기 때문에 호환성을 위해 OpenGL ES 1.0을 바탕으로 제작하였다.

<img img style="display: block; margin-left: auto; margin-right: auto;" src="https://msh0411.github.io/assets/AndroidGameLibrary_05.jpg" alt="drawing" width="300"  />

Texture는 Image 1장으로 대응되지 않고 여러 장의 Image를 한 장의 Texture에 저장하는 방식으로 사용하고 있다. Texture의 크기는 2의 승수로 맞추어 출력해야 제대로 화면에 표현된다. 하지만 모든 이미지가 2의 승수 크기로 만들어질 수 없는 부분이기 때문에 여러 장의 Image를 하나의Texture에 저장하여 각각의 위치를 판단하여 Image를 잘라서 사용하고 있다.

TextureGrid는 이러한 방법으로 저장된 Image의 좌표 정보 및 속성 정보를 가지고 있다. 저장된 TextureGrid는 Engine Library에서 지원하는 Resource Batch Tool에서 손쉽게 작업을 할 수 있으며, 해당 정보를 읽어 사용하고 있다.

<img style="display: block; margin-left: auto; margin-right: auto;"  src="https://msh0411.github.io/assets/AndroidGameLibrary_06.jpg" alt="drawing" width="400"/>

ImageBatcher는 생성된 Image를 배치하는 작업을 한다. 2D Image는 모두 사각형으로 생성되기 때문에 Vertex정보를 사각형 형태로 가지게 된다. 4개의 점으로 이루어지며 Image의 재사용성을 높이고 퍼포먼스 향상을 위해 같은 Texture를 사용하는 Image들을 한번에 그려 Texture의 bind() 횟수를 최소화 하고 있다. 또한 이러한 부분들이 번거롭거나 성능상의 문제가 없을 때는 사용자 편의를 위해서 Picture라는 Class를 제공하고 있다.

<img style="display: block; margin-left: auto; margin-right: auto;"  src="https://msh0411.github.io/assets/AndroidGameLibrary_07.jpg" alt="drawing" width="400"/>

Picture Class는 기본적으로 Image Class를 상속하고 있다. Image Class 처럼 ImageBatcher를 사용하지 않고 생성 및 옵션 설정만으로 자동으로 그려지게 되어 있다. 생성된 Picture Class는 RenderManager에서 자동적으로 그려주며, Order값에 따라 그려지는 순서가 정해진다.

하지만 Picture Class는 Image Class처럼 퍼포먼스 향상을 위한 bind() 최소화가 되어 있지 않기 때문에 많은 량의 같은 Image를 그릴 때는 적절하지 않다. 이러한 부분을 해결하기 위해서 IRender라는 Interface를 제공한다.

<img style="display: block; margin-left: auto; margin-right: auto;"  src="https://msh0411.github.io/assets/AndroidGameLibrary_08.jpg" alt="drawing" width="400"/>

IRender Interface를 Implements 하게 되면 Render() 함수가 추가되고, 주어진 함수에 ImageBatcher로 최적화하여 작성하면 된다. 작성된 Render() 함수는 RenderManager에서 관리되며, 자동으로 그려지게 된다.

Render()함수 내의 그리기 순서는 그린 순서를 따르게 되며, Render()함수 자체의 순서는 Order값에 따라 적용된다.

<img style="display: block; margin-left: auto; margin-right: auto;"  src="https://msh0411.github.io/assets/AndroidGameLibrary_09.jpg" alt="drawing" width="150"/>

작업을 하다 보면 충돌박스나 UI의 위치, 혹은 Test Image가 없을 때 가시적인 확인이 필요할 때가 있다. 이러한 부분을 번거롭지 않게 해결해주기 위해 DebugRenderer를 제공한다. Config Class에서 _Debug 값이 True일때만 적용이 되며, 추가적인 Image가 없어도 임의의 색상의 박스가 화면상에 표현된다.

- - -

 * **Sound**  
<img style="display: block; margin-left: auto; margin-right: auto;"  src="https://msh0411.github.io/assets/AndroidGameLibrary_10.jpg" alt="drawing" width="300"/>

Audio Interface는 Music과 Sound로 나뉜다. Audio는 Music과 Sound의 생성만을 관리한다.
생성된 Music과 Sound는 ResourceManager에서 관리된다. 기본적으로 Assets폴더에 BGM폴더와 SE폴더에 들어있는 파일을 자동적으로 읽어서 사용하게 되며, 해당 파일의 이름으로 불러서 사용하게 된다. 전체적인 볼륨은 AppManager에서 관리되며 전체 파일에 적용된다.
개별적인 적용을 위해서는 Play시에 개별적인 적용이 가능하다.

<img style="display: block; margin-left: auto; margin-right: auto;"  src="https://msh0411.github.io/assets/AndroidGameLibrary_11.jpg" alt="drawing" width="150"/>

Music은 배경음과 같이 재생시간이 긴 스트리밍 오디오 파일에 사용된다. 주로 배경음은 Play되고 있는 파일이 동시적으로 여러 개일 필요가 없기 때문에 여러 개의 파일을 로딩해둘 필요가 없다. 따라서 Engine Library에서는 Play 중인 배경음이 있을 경우 재생을 중지시키고 새로운 배경음을 실행하게 되어 있다. 효과음과 같은 다중 사용 파일에 비해 배경음은 메모리를 많이 잡아먹기 때문에 정확한 관리가 필요하다. 실질적인 구현은 AndroidMusic Class에서 구현된다. 기본적으로 Android에서 제공하는 MediaPlayer를 사용하여 재생을 하고 있다.

<img style="display: block; margin-left: auto; margin-right: auto;"  src="https://msh0411.github.io/assets/AndroidGameLibrary_12.jpg" alt="drawing" width="150"/>

Sound는 효과음과 같이 재생시간이 짧고 반복 횟수가 높거나 동시에 사용되는 파일들을 재생한다. 재생되는 파일들은 Android에서 제공되는 SoundPool로 재생이 되며 관리된다. 
Music과 같이 ResourceManager에서 자동적으로 추가와 관리가 되며, 사용할 파일명을 입력해 사용하게 된다.

- - -

 * **Input**  
<img style="display: block; margin-left: auto; margin-right: auto;"  src="https://msh0411.github.io/assets/AndroidGameLibrary_13.jpg" alt="drawing" width="700"/>

Input은 크게 5가지로 분류된다. 일반적으로 화면에 반응하는 터치조작과 가속도, 진동, 방향, 키 입력으로 지원하고 있다. 그 외에도 추가적으로 여러 가지 기능들을 Android에서 지원하고 있지만, 센서가 있는 하드웨어가 있고 없는 하드웨어가 존재하기 때문에 모두 추가하지 않았다.
각각의 Handler들은 독립적으로 작동하며 AndroidInput에서 관리된다. 각각의 Handler에서 넘겨진 메시지를 AndroidInput에서 Pool에 순차적으로 저장하여, 메시지를 알려준다.
TouchHandler의 경우 SingleTouch와 MultiTouch로 구분되는데, Android 2.2버전 이상에서만 MultiTouch를 지원하기 때문에 구분해서 TouchHandler를 지원하고 있다.

<img style="display: block; margin-left: auto; margin-right: auto;"  src="https://msh0411.github.io/assets/AndroidGameLibrary_14.jpg" alt="drawing" width="300"/>

SingleTouch의 경우 화면에 1개의 점을 인식할 수 있고, MultiTouch의 경우 기기의 스펙에 따라 달라지지만 평균 8개 점을 인식한다. 또한 Android에서 추가된 다양한 Gesture기능을 추가하여, Touch의 다양한 반응을 인식하고 활용할 수 있다.

- - -

 * **UI**  
<img style="display: block; margin-left: auto; margin-right: auto;"  src="https://msh0411.github.io/assets/AndroidGameLibrary_15.jpg" alt="drawing" width="500"/>

Layout은 UI가 포함되는 하나의 공간이며, UI자체도 Layout을 상속하고 있다. 크게 3가지로 Layout을 나누는데 LinearLayout, FrameLayout, UI로 나누고 있다.
LinearLayout의 경우 포함되는 Layout과 UI는 자동 정렬되며, 방향 값을 기준으로 정렬된다. 또한 자동적인 크기변경을 진행하기 때문에 Layout의 크기를 생각할 필요가 없다.
FrameLayout의 경우 포함된 Layout과 UI가 위치정렬만 진행되며, 각각의 Layout이 겹쳐서 표현할 수 있다.
UI의 경우는 다양한 기능을 하는 UI Class들을 지원하며 기본적인 기능은 FrameLayout과 같다.
Android는 기기 별 해상도가 다양하기 때문에 UI제작에 있어 많은 어려움이 있지만, Engine Library에서 지원하는 UI시스템은 기준해상도 800 X 480을 기준으로 자동적으로 모든 해상도에 맞추어 위치 및 크기변경이 진행된다.
이 기능을 추가적으로 지원해주는 부분이 Camera2D Class이다.

<img style="display: block; margin-left: auto; margin-right: auto;"  src="https://msh0411.github.io/assets/AndroidGameLibrary_16.jpg" alt="drawing" width="150"/>

Camera2D Class는 해당 기기에 지원되는 해상도를 확인하여, 800 X 480에 가장 근접한 화면으로 축소시킨다. 이때 기기에서 지원하는 해상도 비율을 유지하기 때문에 이미지의 찌그러짐이나 어긋나는 현상은 없다. 다만 기준 해상도와 다른 비율을 유지하기 때문에
약간의 여백이 나타나거나 잘려서 보일 수 있다. 이러한 부분들은 제작 당시 Camera의 Zoom 비율을 0.8정도 확대하여 여백을 보이지 않게 제작하면 해결 할 수 있다.
UI에는 5가지가 지원되고 있고, 추후 추가될 것이다. 기본적인 ImageBox를 생성할 수 있는 UIBox, Touch반응을 확인하는 UIButton, 문자열을 표현하기 위한 UIStaticText, 로딩 및 게이지바를 표현하기 위한 UIProgressBar, 랭킹 등 리스트 정보를 나타내기 위한 UIListBox가 있다. 
UI가 가지는 속성이 기본적으로 FrameLayout과 동일하기 때문에 사용하는 방법에 있어서 FrameLayout과 같이 사용하면 된다. 다만 UIListBox의 경우 List표현을 하기 위해 FrameLayout과는 다른 구조를 가진다. 

이러한 UI들은 각각의 추가 기능 및 특징을 가지는데 아래와 같다.

<img style="display: block; margin-left: auto; margin-right: auto;"  src="https://msh0411.github.io/assets/AndroidGameLibrary_17.jpg" alt="drawing" width="700"/>

UIButtom의 경우 버튼의 재사용시간을 표현할 수 있는 기능도 제공하고 있다. 정해진 재사용시간 동안 버튼의 입력처리가 정지되며, 재사용시간 게이지가 표현된다. 이 기능은 선택적으로 사용이 가능하며, 사용하지 않을 때는 일반적인 버튼처리가 된다.
UIListBox는 일정한 형식이 정해진 것이 아닌, 사용자에 맞게 수정이 가능하다. 예를 들어 리스트 하나에 표현되는 정보가 랭킹순위, 이름, 하트버튼 이라고 할 때, 이 내용이 고정적으로 적용되는 것이 아니라, 추가되는 리스트의 내용을 필요에 맞게 변경해서 사용할 수 있다. 이러한 기능은 따로 추가되는 기능이라기보다는 Layout Class의 특성으로 인한 자연적인 현상이다. UI자체도 하나의 Layout이기 때문에 다양한 UI 및 Layout 속성을 부여할 수 있다.
UIStaticText의 String 출력 기능은 정해진 크기로 출력이 가능하며, Layout의 크기에 맞게 크기가 자동적으로 설정되게 할 수도 있다. 게임상에서 출력되는 UI의 크기는 해상도 및 비율에 따라 크기와 위치가 다소 차이가 있을 수 있지만, 자동적으로 설정을 해주기 때문에 추가적인 계산을 할 필요가 없다. UIStaticText도 UI의 하나이기 때문에 UI가 가지는 특성을 모두 계승한다.
UIProgressBar로 표현되는 게이지바의 크기는 해당 Layout의 위치 및 크기 설정에 따라 달라지며, 방향 및 색상의 설정이 가능하다.

- - -

 * **Manager**  
<img style="display: block; margin-left: auto; margin-right: auto;"  src="https://msh0411.github.io/assets/AndroidGameLibrary_18.jpg" alt="drawing" width="500"/>

Engine Library에는 위에서 일부 소개된 바와 같이 Manager가 존재한다. 다양한 기능이나 사용자 편의를 위해서 존재하며, 각각의 Manager는 사용되는 Resource를 관리하거나 UI의 통합관리 또는 Rendering처리의 관리 등을 맡고 있다. 
주로 사용하게 될 Manager는 ResourceManager로 Assets에서의 데이터를 읽어 들이거나 Sound와 같은 데이터를 사용할 때 사용된다. 또한 읽어 들인 Texture의 포인터를 얻기 위해서 사용되기도 한다.
이러한 Manager들은 AppManager에서 통합적으로 관리되며, App에서 관리하게 될 정보들을 담당하고 있다. 
각각의 Manager들은 AppManager에서 사용되고 있지만, 각각이 독립적인 Manager이기 때문에 독자적인 사용이 가능하다.

<img style="display: block; margin-left: auto; margin-right: auto;"  src="https://msh0411.github.io/assets/AndroidGameLibrary_19.jpg" alt="drawing" width="200"/>

Engine Library는 GLGame을 생성하여 사용하게 되는데, GLGame에서 모든 기능을 통합해서 사용할 수 있다. 앞에서 설명한 Input, FileIO 등과 같이 Engine Library에서 제공되는 다양한 기능들을 모두 가지고 있다. 
또한 GLGame은 Activity를 상속하기 때문에 Activity의 역할을 하게 되며 별도의 Activity를 선언하지 않아도 된다. 
Screen은 실질적인 구현을 위한 공간이며, Engine Library에서 제공 하는 기능들을 사용하여 구현할 수 있다.
기본적으로 Update와 Draw, TouchEvent, KeyEvent 등의 처리를 하게 되며 Android의 LifeCycle에 맞추어 관리된다.
App의 중지나 일시정지, 재시작 등은 해당 함수 부분에서 처리를 구현하면 되며, Resource 관리는 ResourceManager에서 자동적으로 처리를 하기 때문에 별도의 관리를 필요로 하지 않는다.
