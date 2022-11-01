# 커스텀 뷰(Custom View)
## ☝️ Custom View란?
<br>
미리 만들어 놓은 뷰가 아닌 개발자가 직접 모양 및 기능 등을 정의하여 만든 뷰이다. 커스텀 뷰는 View 클래스를 상속받아 필요한 메소드를 재정의함으로써 만들 수 있다.
<br><br>
이 때, XML 에서 직접 작성한 View를 사용하기 위해서는 View의 생성자를 모두 재정의 해야한다.  
  
```java
class MyView extends View {
// XML에서 다른 View와 함께 사용하고자 할 때는 반드시 모든 생성자를 재정의
    public MyView(Context context) { // 기본 생성자 (필수)
    	super(context);
    }
    
    public MyView(Context context, AttributeSet attrs) {..}
    
    public MyView(Context context, AttributeSet attrs, int defStyleAttr) {..}
    
    // 그리기 메소드 (뷰가 화면에 그려져야 할 때 호출)
   	public void onDraw(Canvas canvas) {
    	// 그리기 코드
        ...
    }
}
```
<br>

--------------------------------------------------------------------

## 1. Custom View - 그리기 예제
- **MyView.java**
```java
Public class MyView extends View {
    // 되도록 3개의 생성자 모두 정의
	public MyView(Context context) { // 기본 생성자 (필수)
        super(context);
    }

    public MyView(Context context, @Nullable AttributeSet attrs) {
        super(context, attrs);
    }

    public MyView(Context context, @Nullable AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
    }

    // onDraw 재정의    
	public void onDraw(Canvas canvas) { // 그림이 그려지는 영역
		canvas.drawColor(Color.LTGRAY);
		Paint pnt = new Paint(); // 그림을 그리는 도구
		pnt.setColor(Color.BLUE);
		canvas.drawCircle(100, 100, 80, pnt); // 캔버스에 지정한 위치와 도구로 그림을 그림
	}
}
```
- **activity_main.xml**
```xml
<!-- 반드시 Full Package를 포함한 클래스 명으로 기록해야 한다. -->
<ddwu.com.mobile.exam.customViewtest.MyView 
    android:id="@+id/myView"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    />
```

<br>

--------------------------------------------------------------------

## 2. Custom View 그리기
### ✏️ 2-1. **CustomView 사용 시의 그리기 절차**
1. View 클래스 상속 후 생성자, onDraw() 재정의한다.  
_(on이 붙은 메소드는 개발자가 정의하고, 시스템이 호출하는 메소드)_  
2. onDraw()에 그릴 내용 지정한다.
    - canvas에 어떠한 형식으로 그릴지 paint 객체로 지정한다.
    - 매개변수로 전달 받은 canvas의 메소드를 사용하여 그리기 수행한다.
3. CustomView 객체를 생성한다.
4. 화면을 다시 그릴 필요가 있을 때 **_customView.invalidate() 호출한다._**  
    - 화면을 무효화 한 후 onDraw()가 호출되어 화면이 다시 그려진다.  

![customView](./img/customView.jpg)  

### ✏️ 2-2. **Canvas 클래스**
> 그림을 그리는 영역을 담당하는 클래스이다.
- Canvas의 메소드를 사용하여 화면에 그리기를 지정한다.
- View 클래스이 onDraw() 메소드를 매개변수로 전달한다.  
_(전달받은 Canvas 객체를 수정하여 그려지는 모양을 변경한다.)_  
- 기본 도형 출력  
  - **Canvas의 멤버 메소드를 호출한다.**  
  _(이 때, 오버로딩 되어 있으므로 매개변수 확인이 필요하다.)_
- Canvas의 메소드  
ex) canvas.drawCircle(100, 100, 80, pnt);
```java
// 점 그리기
void drawPoint (float x, float y, Paint paint)
// 선 그리기
void drawLine (float startX, float startY, float stopX, float stopY, Paint paint)
// 원 그리기
void drawCircle (float cx, float cy, float radius, Paint paint)
// 사각형 그리기
void drawRect (float left, float top, float right, float bottom, Paint paint)
// 텍스트 그리기
void drawText (String text, float x, float y, Paint paint)
```  

### ✏️ 2-3. **Paint**
> Canvas에 그림을 그리기 위해 사용하는 펜 역할
- **그리기에 대한 속성 정보를 지정**한다.
- 선 경계를 부드럽게 출력하는 방법  
  1. paint.setAntiAlias(true)
  2. new Paint(Paint.ANTI_ALIAS_FLAG)
- 색상 변경 방법  
  1. 투명도 지정 RGB : paint.setARGB (int a, int r, int g, int b)
  2. 색상만 지정 : paint.setColor(int color)  
  _ex)_  _Paint pnt = new Paint();_  
  _pnt.setColor(Color.BLUE);_
  
<br>

--------------------------------------------------------------------

## 3. 간단한 Sound & 진동 기능 사용법
### ✏️ 3-1. **간단한 Sound 출력**
- SoundPool : 짧은 소리 출력용
```java
// res 폴더에 raw 폴더 생성 후 sound 파일을 복사하여 준비 (소문자만 가능)
SoundPool soundPool = new SoundPool(1, AudioManager.STREAM_MUSIC, 0);

// 메모리 공간에 실제 소리를 로딩
int sound = soundPool.load(this, R.raw.dingdong, 1); 

soundPool.play(sound, 1, 1, 0, 0, 1);
```
_❗️ 모두 onClick에 넣으면 매번 메모리에 올려야하기 때문에 소리가 출력되지 않을 가능성이 높다.  
✔️ SoundPool 객체 생성과 메모리 공간에 실제 소리를 로딩하는 부분은 onCreate에 작성하고, **play 부분만 onClick에 작성한다.**_

- AudioManager : 노래와 긴 소리와 같은 시스템 기본 사운드 출력용  
시스템 서비스로부터 AudioManager를 획득하여 사용한다.
```java
AudioManger audioManger = (AudioManager)getSystemService(AUDIO_SERVICE);
audioManager.playSoundEffect(AudioManger.FX_KEYPRESS_STANDARD);
```  
### ✏️ 3-2. **진동 기능의 사용**
- 기기의 진동 기능을 활용하여 진동을 발생시킨다.
```java
Vibrator vibrator = (Vibrator)getSystemService(Context.VIBRATOR_SERVICE); // onCreate에 작성

// onClick에 작성
vibrator.vibrate(500); // 1번 진동
vibrator.vibrate(new long[] {100, 50, 200, 50}, 0); // 여러번 진동

vibrator.cancel(); // 진동 중지
```
- Permission 설정  
AndroidManifest.xml에 진동 기능에 관한 permission을 추가하여야 한다.
```xml
<uses-sdk
    android:minSdkVersion="14"
    android:targetSdkVersion="16" />

<uses-permission android:name="android.permission.VIBRATE" />
```
