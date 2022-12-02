# App Data와 File

## 1. **Android의 데이터 저장 유형**

- 기기 저장소에 파일 저장
    - `내부 파일 저장소` : 해당 App 전용, 내부 캐시 파일 등
    - `외부 파일 저장소` : 공유 데이터 (캡처한 사진, 또는 다운로드 파일 등)
- 공유 기본 설정 (Shared Preferences)
    
    → 여러 Activity가 공유하고자 할 때
    
    - 기본 데이터를 key-value의 쌍 형태로 저장
    - 데이터를 많이 저장하지 않고 기본 자료형을 영구적으로 저장할 필요가 있을 때 사용
    - 사용자 인증 비밀번호, 앱 설정 정보 등
- 데이터베이스의 사용
    - SQLite, ROOM(SQLite의 효율적인 사용을 위한 프레임워크)
    

---

## 2. **내부 저장소 파일 사용**

> 앱 전용 파일 사용 시 적용
> 
- **사용 전 용량 확인이 필요할 경우**
    
    → `getFreeSpace()` 사용 (용량 부족 시 IOException 발생)
    
- **파일 생성 및 준비**
    - 기존 파일이 존재할 경우 파일 준비, 없을 경우 생성
    - `getFilesDir()` : 앱 전용 위치의 경로 *(직접 파일 접근)*
        
        1. File이 아닌 Stream으로 읽어올 경우 `Context.openFileInput(...)` 사용
        
        2. Stream으로 저장할 경우 Context.openFileOutput(...) 사용
        
    - `getCacheDir()` : 앱 전용 캐쉬 위치에 파일 생성 (cache 폴더는 자동 생성)
        
        *File file = new File(context.getFilesDir(), filename);*
        
    - 생성 위치 확인 (Device File Explorer 사용)
        1. 일반 파일 : data/data/패키지명/files/filename
        2. 캐시 파일 : data/data/패키지명/cache/filename

### **✏️ 2-1.** 파일 쓰기

🔎  방법 1) 직접 파일 접근

```java
// 내부 저장소에 파일 생성
File saveFile = new File(context.getFilesDir(), fileName);
// file용 output 스트림 생성
FileOutputStream fos = new FileOutputStream(saveFile);
 
// 문자열을 파일에 기록
// String data = “file contents!”;
// fos.write(data.getBytes());
 
// 비트맵 이미지를 파일에 기록, Bitmap의 크기가 클 경우 quality 조정
bitmap.compress(Bitmap.CompressFormat.JPEG, 100, fos);
 
fos.flush();
fos.close();
```

🔎 방법 2) 기본 지정 파일 접근

```java
String filename = “myfile”;
String fileContents = “Hello world!”;
FileOutputStream outputStream;

try {
outputStream = openFileOutput(filename, Context.MODE_PRIVATE);
outputStream.write(fileContents.getBytes());
outputStream.close();
} catch (Exception e) {
e.printStackTrace();
}
```

### **✏️ 2-2.** 파일 읽기

```java
// 문자열 경로 반환 -> data/data/project_name/files/filename
String path = context.getFilesDir().getPath + “/“ + fileName;

// 문자열일 경우의 읽기
File readFile = new File(path);
FileReader fileReader = new FileReader(readFile);
BufferedReader br = new BufferedReader(fileReader);
String line = “”;
while((line = br.readLine()) != null) {
Log.i(TAG, line);
}
br.close();
 
// 비트맵일 경우의 읽기
Bitmap bitmap = BitmapFactory.decodeFile(path); // 이미지 용량 확인 필요
```

### **✏️ 2-3.** 파일 삭제

- 파일 삭제

```java
File file = new File(context.getFilesDir() + “/” + fileName);
boolean  result = file.delete();
```

- 경로 내 모든 파일 삭제

```java
File internalStoragefiles= context.getFilesDir();  // 경로만 지정
File[] files = internalStoragefiles.listFiles();  // 파일 목록
for (File target : files) {
target.delete();
}
```

---

## 3. **외부 저장소 파일 사용**

> 외부 메모리 or 내부에 있는 별도 파티션
> 
> 
> SD 카드 등 외부 저장소에 앱 전용 또는 공유 목적으로 파일 사용 시 적용
> 
- 퍼미션 추가
    - READ_EXTERNAL_STORAGE(*읽기 전용*), WRITE_EXTERNAL_STORAGE(*쓰기 전용*)
    - Android 4.4 이후 앱 전용 외부저장소 디렉토리 사용 시 퍼미션 불필요
- 외부 저장소 사용 가능 확인
    
    ```java
    private boolean isExternalStorageWritable() {
    	return Environment.getExternalStorageState().equals(Environment.MEDIA_MOUNTED);
    }
    private boolean isExternalStorageReadable() {
    	return Environment.getExternalStorageState().equals(Environment.MEDIA_MOUNTED ||
    				Environment.getExternalStorageState().equals(Environment.MEDIA_MOUNTED_READ_ONLY);
    }
    ```
    

### **✏️ 3-1. 공용 위치에 저장할 경우**

> 모든 앱이 공통으로 접근할 수 있는 기본 디렉토리 *(ex. Download, DCIM, Movies, Music 등)*
> 
- MediaStore API 또는 ACTION_CREATE_DOCUMENT 이용
- Media Scanner 스캔 대상에서 제외하고자 할 때
    
    → .nomedia 파일(내용 없음)을 해당 디렉토리에 생성 
    

### **✏️ 3-2. 전용 위치에 저장할 경우**

- 필요 시 디렉토리 생성 후 사용 파일 입출력
- 지정된 위치에 파일의 유형별 기본 디렉토리를 필요에 따라 생성
    - `getExternalFilesDir(파일 유형)` 메소드로 접근
    - 경로 형식 예 : *루트/mnt/scdard/Android/data/패키지명/*
    - 파일 유형 예 : *Environment.DIRECTORY_PICTURES (Pictures 디렉토리)*
    - 미디어 파일 유형 별로 디렉토리를 필요에 따라 생성
- 외부 메모리에 디렉토리 생성 예
    
    ```java
    Public File getPrivateAlbumStorageDir(Context context, String albumName) {
    	File file = new File(context.getExternalFilesDir(
    		Environment.DIRECTORY_PICTURES), albumName);  // albumName : String형
    		// -> Android/data/패키지명/files/Pictures/myalbum
    		if (!file.mkdirs()) {
    			Log.e(LOG_TAG, “Directory not created”);
    	}
    	return file;
    }
    ```
    

---

## 4. **내부 저장소 vs 외부 저장소**

| 특성/분류 | 내부 저장소 파일 | 외부 저장소 파일 (앱 입장에서 외부) |
| --- | --- | --- |
| 종류 | 앱 전용 파일 or 캐시 파일 | 앱 지정 파일 or 공용 파일 |
| 사용가능성 | 항상 사용 가능 | 저장소 분리 등에 의해 사용 불가할 수 있으므로 확인 필요(mount 상태일 때만 사용 가능) |
| 접근성 | 파일을 생성한 앱에서만 사용 가능 (전용)  | 다른 앱 등에 의해서도 접근 가능 (공용) |
| 파일 유지 | 앱 삭제 시 함께 삭제됨 | 앱 삭제 시 별도로 지정한 경로가 아닐 경우 파일 남음 (단, getExternalFilesDir()에 위치한 디렉토리의 앱 파일은 함께 삭제됨) |
| 용도 | 앱 내부에서 자체적으로 사용하는 파일 | 다른 앱들과 공유할 필요가 있는 파일, 파일 크기 등의 이유로 외부의 전용 공간에 저장할 필요가 있는 파일 |
| 주요 접근 메소드 | getFreeSpace(), getTotalSpace(), getFilesDir(), getDir(), getCacheDir90, createTempFile(), deleteFile() | getExternalStorageState(), getExternalFilesDir() |
| 기타 사항 | 퍼미션 불필요 | 안드로이드 버전에 따라 퍼미션 추가 필요 |

---

## 5. **SharedPreferences 사용**

### **✏️ 5-1. SharedPrefernces 클래스**

- Key-value 형태의 데이터를 영구 저장할 때 사용
- Context.getSharedPreferences(“파일명”, mode)로 객체 획득
    - 여러 Activity에서 공유할 SharedPreferences 가 필요할 때 사용
    - 파일명 : preferences 값이 저장될 내부 파일명
    - mode : 파일의 접근 모드, 디폴트 0값 사용
    - Boolean, Float, Int, Long, String, String set으로 저장 가능
- Context.getPreferences() : 해당 Activity 전용의 SharedPreferences 가 필요할 때 사용

### **✏️ 5-2. 사용 시점**

- SharedPreferences 읽기 -> onCreate()
- SharedPreferences 쓰기 -> onPause() 또는 특정 이벤트 시점