# 안드로이드 이벤트(Event)
## ☝️ Event-Driven Programming
> 프로그램의 실행 흐름이 사용자의 동작과 센서 입출력 등 이벤트에 따라 결정되는 프로그래밍 방식이다.  
<br>
대부분의 프로그래밍 방식으로, GUI 프로그램에서 채택되었다.

- 이벤트 발생 & 전달 과정  
  1. 발생한 이벤트를 운영체제에 전달한다.
  2. 운영체제에서 이벤트를 처리할 앱에 전달한다.  
    이 때, 앱에서 처리할 수 있는 이벤트 핸들러(Event Handler)가 있으면 수행하고 없다면 무시한다.
    
<br>

--------------------------------------------------------------------

## 1. Android 이벤트 처리(이벤트 핸들러 구현) 방식
### ✏️ **1-1. 상속 메소드 재정의** 
> 콜백 메소드를 사용하여 View가 현재 갖고 있는 이벤트 처리 메소드를 상속 받아 현재 사용하는 위젯 등에서 재정의한다.
- **View의 대표적 이벤트 처리 메소드**  
```java
// 모든 View는 이벤트 핸들러를 가지고 있다.(내용이 없어서 수행되지 않음)
// 각각의 View를 상속받아 재정의하여 내용을 구현한다.
boolean onTouchEvent(MotionEvent event)
boolean onKeyDown(int keyCode, keyEvent event)
boolean onkeyUp(int keyCode, keyEvent event)
boolean onTrackballEvent(MotionEvent event)
```
  
- **고려사항**  
  1. 개발자가 Custom View를 작성할 때, 즉 상속을 구현한 경우에만 사용 가능하다.
  2. 이벤트에 대한 메소드 전부가 정의되어 있지는 않다.  
  <br>

- **Android Studio 기능 활용**  
_코드의 View 클래스 내부에서 Ctrl + O_  
 => _[Select Method to Override/Implement]_   
 => _해당 메소드 선택_

 ### ✏️ **1-2. Listener Interface 구현** 
 > *가장 기본적인 UI 이벤트 처리 방법*으로, 이벤트 처리 Listener을 구현하여 이벤트 처리가 필요한 View에 등록하는 방식이다.
 - **대부분의 UI 이벤트에 대한 Interface가 준비**
 하나의 인터페이스 당 하나의 구현 메소드가 존재한다.  

인터페이스명 | 구현 메소드 
--- | --- 
OnTouchListener | boolean onTouch(View v, MotionEvent event)
OnKeyListener | boolean onKey(View v, int keyCode, KeyEvent event)
OnClickListener | void onClick(View v)
OnLongClickListener | boolean onLongClick(View v)

```java
public class MainActivity extends AppoCompatActivity {
    @Override
    protected void onCreate(Bundle savedInstanceState){...}

    // 메소드 선언 <- 운영체제(시스템) 호출 = method callback
    @Override
    public boolean onTouchEvent(MotionEvent event) {
        Toast.makeText(this, "Touch event!", Toast.LENGTH_SHORT).show();
        return true;
    }
}
```

- **적용 방법**  
  1. 처리하고자 하는 UI 이벤트에 해당하는 **_Interface를 구현한 후, 인터페이스 구현 클래스를 작성_** 한다.
  2. **_구현한 클래스에서 객체를 생성_** 한다.
  3. 생성한 객체를 이벤트 처리가 **_필요한 View에 등록_** 한다.  
  <br>

- **Listener Interface 구현 유형**  
1. 별도의 리스너 인터페이스를 구현한 클래스 작성
2. Activity가 직접 인터페이스 구현
3. Custom View 생성 시 뷰에 리스너 인터페이스 구현
4. 익명 내부 클래스 구현 (인터페이스 객체 직접 생성)
5. 익병 내부 클래스 임시 객체로 구현  

🔎 방법 1  - **_별도의 리스너 인터페이스를 구현한 클래스 작성_**
```java
public class MainActivity extends AppoCompatActivity {

    Button button1;

    @Override
    protected void onCreate(Bundle savedInstanceState){
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        button1 = findViewById(R.id.button1);

        // 클래스의 객체 생성
        MyClick myClick = new MyClick();

        // button1과 연결
        button1.setOnclickListener(myClick);
    }

}

// 별도의 리스너 인터페이스를 상속 받은 MyClick 클래스 생성
class MyClick implements View.OnClickListener { 
    @Override
    public void onCLick(View v) {
        Toast.makeText(MainActivity.this, "방법 1", Toast.LENGTH_SHORT).show();
    }
}
```
🔎 방법 2  - **_Activity가 직접 인터페이스 구현_**   
```java
public class MainActivity extends AppoCompatActivity {

    Button button2;

    @Override
    protected void onCreate(Bundle savedInstanceState){
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        button2 = findViewById(R.id.button2);

        // button2와 연결
        button2.setOnClickListener(this); // this == MainActivity
    }

    // onClick 메소드 재정의
    @Override
    public void onClick(View v) {
        Toast.makeText(MainActivity.this, "방법 2", Toast.LENGTH_SHORT).show();
    }

}
```
🔎 방법 3  - **_Custom View 생성 시 뷰에 리스너 인터페이스 구현_** 
```java
// MyView(Custom View)에 리스너 인터페이스 구현
class MyView extends View implements View.onTouchListener {
    public MyView(Context context) {...}

    @Override
    public void onDraw(Canvas canvas) {...}

    @Override
    public boolean onTouch(Veiw v, MotionEvent event) {
        Toast.makeText(MainActivity.this, "방법 3", Toast.LENGTH_SHORT).show();
        return true; 
        // 이벤트 처리 완료 시 ture, 처리를 계속해야 할 경우 false (상위로 전달)
    }
} 

public class MainActivity extends AppoCompatActivity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        MyView view = new MyView(this); // this == MainActivity
        view.setOnTouchListener(view);
        setContentView(view);
    }
    ... 
}
```
🔎 방법 4  - **_익명 내부 클래스 구현 (인터페이스 객체 직접 생성)_**  
```java
public class MainActivity extends AppoCompatActivity {

    Button button3;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        button3 = findViewById(R.id.button3);
        // 익명 클래스의 객체 사용
        button3.setOnClickListener(myClickListener);
    }
    
    // View.OnClickListener myClickListener = new View.OnClickListener(); => 인터페이스라 불가능
    View.OnCLickListener myClickListener = new View.onClickListener() {
        @Override
        public void onClick(View v) {
            Toast.makeText(MainActivity.this, "방법 4", Toast.LENGTH_SHORT).show();
        }
    }
}
```
🔎 방법 5  - **_익병 내부 클래스 임시 객체로 구현_**  
```java
public class MainActivity extends AppoCompatActivity {

    Button button4;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        button4 = findViewById(R.id.button4);

        // 익명 클래스의 임시 객체 사용
        button3.setOnClickListener(new View.onClickListener() { // 모두 매개변수로 (== 임시객체)
            @Override
            public void onClick(View v) {
                Toast.makeText(MainActivity.this, "방법 5", Toast.LENGTH_SHORT).show();
            }
        });
    }
}
```

 ### ✏️ **1-3. 위젯 이벤트 처리** 
 > View의 XML 속성으로 이벤트 처리를 지정한다. 이벤트를 처리하는 뷰(위젯)의 XML onClick 속성에 이벤트를 처리할 메소드 명을 등록한다.

<br>

--------------------------------------------------------------------
## 2. 이벤트 핸들러의 우선 순위
### ✏️ 2-1. 하나의 이벤트에 대하여 여러 이벤트 리스너를 작성할 경우
  1. 구현한 이벤트 리스너가 있을 경우 우선 수행한다.
  2. 뷰 자체의 콜백 메소드를 구현하였을 경우 수행한다. (커스텀 뷰 사용 시)
  3. 액티비티에 콜백 메소드를 구현하였을 경우 수행한다.

### ✏️ 2-2. 이벤트 리스너의 반환값 설정
- **true를 반환할 경우** : 추가적인 이벤트 처리 진행을 생략한다.
- **false를 반환할 경우** : 다음 순위의 이벤트 리스너가 이벤트를 진행한다.

<br>

--------------------------------------------------------------------
## 3. 외부 변수 접근
### ✏️ 3-1. 이벤트 핸들러는 이벤트 발생 시점에 실행
> 외부의 지역 변수 사용 시 접근 범위 문제가 발생할 수 있다.
```java
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);

    // onCreate 끝나면 없어지므로 접근 범위의 문제가 발생한다.
    LinearLayout layout = (LinearLayout)findViewById(R.id.LinearLayout);
    TextView textView = (TextView)findViewById(R.id.text_view); 

    layout.setOnTouchListener(new View.OnTouchListener() {
        @Override
        public boolean onTouch(View view, MotionEvent motionEvent) {
            if (motionEvent.getAction() == MotionEvent.ACTION_DOWN) {
                textView.setText("Touched"); // 여기서 에러가 발생한다. 접근 범위 문제가 발생한다.
                return true;
            }
            return false;
        }
    })
}
```
🔎 해결책 1 - **멤버 변수로 선언**한다.
- 내부에서의 접근 위치/시점의 제한이 없어진다.
- 멤버 변수가 불필요하게 많아지지 않도록 주의한다.
```java
TextView textView; // 멤버 변수로 선언

@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);
    
    LinearLayout layout = (LinearLayout)findViewById(R.id.Linear_layout);
    textView = (TextView)findViewById(R.id.text_view);

    layout.setOnTouchListener(new View.onTouchListener() {
        @Override
        if (motionEvent.getAction() == MotionEvent.ACTION_DOWN) {
            textView.setText("Touched"); // 가능
            return true;
        }
        return false;
    })
}
```
🔎 해결책 2 - **상수로 선언**한다.
- 상수로 선언 시 앱 종료 시까지 유지된다.
- 멤버 변수로 선언할 필요가 없을 경우 적용한다.
```java
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);
    
    LinearLayout layout = (LinearLayout)findViewById(R.id.Linear_layout);
    // 상수로 선언
    final TextView textView = (TextView)findViewById(R.id.text_view);

    layout.setOnTouchListener(new View.onTouchListener() {
        @Override
        if (motionEvent.getAction() == MotionEvent.ACTION_DOWN) {
            textView.setText("Touched"); // 가능
            return true;
        }
        return false;
    })
}
```
