# 대화상자 (Dialog)

## ☝️  대화상자

> 사용자에게 정보를 전달하거나 선택을 입력 받는 용도
> 
> 
> > Toast와 달리 정보 전달의 경우에도 확인 필요 → 확인 버튼
> > 
- ***Dialog Class***
    - 안드로이드의 기본 대화상자 클래스
    - 대화상자의 모든 기능을 제공하지만 세세한 제어 필요
        
        → 효율적인 사용을 위해 AlertDialog Class 사용
        
- ***AlertDialog Class***
    - 기본 대화상자보다 간단하게 사용할 수 있음
    - 간단한 설정을 통해 대화상자 생성
    - 내부 클래스인 **AlertDialog.Builder 클래스를 활용**하여 대화상자 객체 생성
- DatePickerDialog 또는 TimePickerDialog

---

## 1. 대화상자의 사용

### ✏️ 1-1. AlertDialog의 사용

- 다이얼로그 생성 및 표시
    
    ```java
    AlertDialog.Builder builder = new AlertDialog.Builder(this);
    
    // 각 메소드의 반환 값은 builder 자신이므로 연속 호출 가능
    // builder 대신 new AlertDialog.Builder(this)로 객체 만들면서 연속 호출도 가능
    /* 
    		builder.setTitle("대화상자 제목")
    					.setMessage("대화상자 메시지")
    					.setIcon(R.drawable.ic_launcher)
    					.setPositiveButton("확인버튼", null)
    					.setNeutralButton("대기버튼", null)
    					.setNegativeButton("취소버튼", null);
    */
    builder.setTitle("대화상자 제목");
    builder.setMessage("대화상자 메시지");
    builder.setIcon(R.drawable.ic_launcher);
    builder.setPositiveButton("확인버튼", null); // 이벤트 핸들러 부분이 Null -> 눌러도 동작X
    builder.setNeutralButton("대기버튼", null);
    builder.setNegativeButton("취소버튼", null);
    
    // Dialog dlg = builder.create(); // 대화상자 생성, 표시X
    // dlg.show();
    
    builder.show();  // 대화상자 표시
    ```
    

---

## 2. 대화상자의 이벤트 처리

### ✏️ 2-1. AlertDialog의 버튼

- **대화상자 버튼 클릭** 시 이벤트 리스너
    
    ```java
    builder.setPositiveButton("확인버튼", new DialogInterface.OnClickListener() {
      @Override
      public void onClick(DialogInterface dialog, int which) {
    		// 클릭 시 동작
      }
    });
    ```
    
    - 리스너 인수에 null 을 넣을 경우 버튼 클릭 시 대화상자 닫힘
- **Back 버튼 클릭** 시 대화상자 종료하기
    
    ```java
    // cancelable을 false로 하면 외부 클릭 & back 눌러도 대화상자 닫히지 X
    AlertDialog.Builder setCancelable (boolean cancelable)
    ```
    
- 대화상자 **외부 클릭** 시 대화상자
    - Builder에서 Dialog 객체를 생성한 후 호출
        
        ```java
        // builder가 갖고 있지 X -> dialog 객체 구현 후 사용
        // cancel을 false로 하면 외부 클릭해도 닫히지 X (back 클릭 시는 닫힘)
        void Dialog setCanceledOnTouchOutSide (Boolean cancel)
        ```
        

### ✏️ 2-2. 대화상자 처리 시 유의점

- **Modal 대화상자** = 대화상자 끝내야만 다른 것 동작 가능
    - 대화상자를 표시하면 대화상자가 닫히기 까지 프로그램은 실행 대기
        
        → 대화상자를 닫은 이후에 동작
        
    - 파일 열기 등의 일반적인 대화상자 방식
- **Modeless 대화상자** = 대화상자 끝나지 않아도 다른 것 동작 가능
    - Android의 대화상자는 실행 흐름을 막지 않으므로 사용 시 실행 흐름을 잘 고려해야 함
        
        (이벤트 핸들러 내에서 제어할 수 있도록 구현해야함)
        
    - 대화상자를 표시하여도 프로그램은 계속 실행
        
        → 대화상자를 닫지 않아도 동작 가능
        
    - 찾기 대화상자와 같은 대화상자 방식

---

## 3. 목록 표시 대화상자

### ✏️ 3-1. 단순 목록

```java
AlertDialog.Builder builder = new AlertDialog.Builder(this);

builder.setTitle("대화상자 제목")
// setMessage() 대신 setItems 사용
			.setItems(R.array.foods, new DialogInterface.OnClickListener() {
					@Override 
					public void onClick(DialogInterface dialog, int which) {
							String[] foods = getResources().getStringArray(R.array.foods);  // XML resource의 배열 가져오기
							Toast.makeText(MainActivity.this, foods[which], Toast.LENGTH_SHORT).show();
					}
      });
```

### ✏️ 3-2. 단일 선택 목록

```java
AlertDialog.Builder builder = new AlertDialog.Builder(this);

builder.setTitle("대화상자 제목")
// selectedIndex : 현재 선택한 항목이 아닌 이전 선택 항목 index가 저장됨
			.setSingleChoiceItems(foodsArray, selectedIndex, new DialogInterface.OnClickListener() {         
					@Override          
					public void onClick(DialogInterface dialog, int which) {  // which = 현재 선택한 항목의 index
							selectedIndex = which;  // 선택 항목의 index를 멤버 변수에 저장 (안하면 원래 값 09이 유지됨)
							
							Toast.makeText (MainActivity.this, foodsArray[selectedIndex],  // selectedIndex를 멤버변수로
											Toast.LENGTH_SHORT).show();
					 }
       });
```

### ✏️ 3-3. 다중 선택 목록

```java
AlertDialog.Builder builder = new AlertDialog.Builder(this);

builder.setTitle("대화상자 제목")
// selectedItems : boolean 타입으로 멤버변수 선언
				.setMultiChoiceItems(foodsArray, selectedItems, new DialogInterface.OnMultiChoiceClickListener() { 
								@Override   
								public void onClick(DialogInterface dialog, int which, boolean isChecked) {          
										// 항목 체크 여부를 배열 index에 저장          
										selectedItems[which] = isChecked;
										 
										// 선택된 항목들 출력
										String result = "선택: ";
										
										for (int i = 0; i < selectedItems.length; i++) {
													if (selectedItems[i])  // 배열 index 위치의 체크 여부 판단
													result += foodsArray[i];
										}
								    Toast.makeText(MainActivity.this, result, Toast.LENGTH_SHORT).show();
								}
				});
```

---

## 4. 커스텀 대화상자의 작성

- 대화상자에 보여줄 Layout XML 작성
- Layout을 Inflation 한 후 대화상자에 설정