# 네트워크 (Network)

## 1. 네트워크 사용 개요

### ✏️ 1-1. 네트워크 사용 시 고려사항

- 네트워크 환경 조사 → 기기의 네트워크 모듈 확인
- 사용 가능한 네트워크 환경 선택 → 기기의 네트워크 사용 가능 여부 확인
- 네트워크 환경 변화 처리 → 사용 중 확인
- 네트워크 사용 → 모듈에 따른 확인 사항

### ✏️ 1-2. 네트워크 환경 조사

- ***ConnectivityManager*** 사용
- 주요 메소드
    1. getAllNetworkInfo() : 기기가 제공하는 모든 Network 타입 조사
    2. getActiveNetworkInfo() : 활성화 상태의 Network 타입 조사
    3. getNetworkInfo(int type) : 특정 Network 타입 조사
- 필요 퍼미션 (앱이 시스템 기능 사용 시 권한 요청)
    
    → android.permission.ACCESS_NETWORK_STATE (AndroidManifest.xml에 추가)
    

---

## 2. ConnectivityManager 사용

### ✏️  2-1. 네트워크 환경 조사 예시

```java
private static final String DEBUG_TAG = "NetworkStatusExample";
...
ConnectivityManager connMgr 
						= (ConnectivityManager) getSystemService(Context.CONNECTIVITY_SERVICE);
boolean isWifiConn = false;
boolean isMobileConn = false;
for (Network network : connMgr.getAllNetworks()) {  // 네트워크 모듈 자체를 반환
	NetworkInfo networkInfo = connMgr.getNetworkInfo(network);  // 모듈의 정보를 얻음
	if (networkInfo.getType() == ConnectivityManager.TYPE_WIFI)  // wifi 통신
		isWifiConn = networkInfo.isConnected();
	if (networkInfo.getType() == ConnectivityManager.TYPE_MOBILE)  // data 통신
		isMobileConn = networkInfo.isConnected();
}

// NetworkInfo 클래스 : 네트워크 타입 정보를 표현하는 클래스 (현재 사용 가능한 네트워크 확인) 
```

### ✏️  2-2. 네트워크 사용 가능 확인 예

```java
Public boolean isOnline() {  // wifi or data 알 수 없음 (추가로 구현)
	ConnectivityManager connMgr 
						= (ConnectivityManager) getSystemService(Context.CONNECTIVITY_SERVICE);
	NetworkInfo networkInfo = connMgr.getActiveNetworkInfo();
	return (networkInfo != null && networkInfo.isConnected());  
}
```

 *ConnectivityManager.getActivieNetworkInfo()*

         : 현재 활성화 상태의 네트워크 정보를 Network 객체로 반환

---

## 3.  HTTP 통신

### ✏️  3-1. HTTP 기반의 네트워크 활용

- JAVA 기반의 네트워크 관련 API 이용
- 필요 permission (AndroidManifest.xml)
    1. ***android.permission.INTERNET***
    2. ***android.permission.ACCESS_NETWORK_STATE***
- 절차 : HTTP 연결 확보 (open) → 요청 설정 → 요청 처리 → close

### ✏️  3-2. HTTP 연결 확립 (open)

- URL 클래스를 사용하여 접속할 서버 url 설정

```java
URL url = new Url("http://cs.dongduk.ac.kr");
```

- URLConnection (또는 HttpUrlConnection) 클래스를 사용하여 서버와 연결 확보

```java
HttpsURLConnection urlConnection = (HttpsURLConnection) url.openConnection();
```

### ✏️  3-3. HTTP 요청 설정 & 처리

- HTTP 요청 설정
    
    
    | HttpURLConnection 메소드 | 역할 |
    | --- | --- |
    | setConnectTimeout (int timeout) | 1/1000 단위로 연결 제한 시간 설정, 0일 경우 무한 대기 |
    | setReadTimeout (int timeout) | 1/1000 단위로 읽기 제한 시간 설정, 0일 경우 무한 대기 |
    | setUseCaches (boolean newValue) | 캐시 사용 여부 지정 |
    | setDoInput (boolean newValue) | 입력을 받을 것인지를 지정 (inputStream) |
    | setDoOutput (boolean newValue) | 출력을 할 것인지 지정 (outputStream) |
    | setRequestProperty (String field, String newValue) | 요청 헤더에 값을 설정 |
    | addRequestProperty (String filed, String newValue) | 요청 헤더에 값 추가 |
- HTTP 요청 처리
    
    
    | HttpUrlConnection 메소드 | 역할 |
    | --- | --- |
    | void setRequestMethod (String method) | GET 또는 POST 설정, 디폴트는 POST |
    | void connect() | 통신 연결 (네트워크 트래픽 발생)  → 생략하면 getResponseCode에서 처리 |
    | int getResponseCode() | 서버에 요청을 전달하고 응답 결과를 받음   |
    | getInputStream() | 서버에서 전달 받은 데이터를 inputStream 타입으로 변환 |
    | disconnect() | 서버와의 연결 종료 |
    
    *✔️*  **getResponseCode 응답 결과**
    
    - 정상적으로 요청 전달 : HTTP_OK (200)
    - URL을 발견할 수 없음 : HTTP_NOT_FOUND (404)
    - 인증 실패 : HTTP_UNAUTHORIZED (401)
    - 서비스 사용 불가 : HTTP_UNAVAILABLE (503)
- HTTP 요청
    
    ```java
    private String downloadUrl(URL url) thorws IOException {
    	InputStream stream = null;
    	HttpsURLConnection conn = null;
    	String result = null;
    
    	try {
    		conn = (HttpsUrlConnection) url.openConnection(); // 서버 연결 설정 - MalformedURLException
    		conn.setReadTimeout(3000);  // 읽기 타임아웃 지정 - SocketTimeoutException
    		conn.setConnectTimeout(3000);  // 연결 타임아웃 지정 - SocketTimeoutException
    		conn.setRequestMethod("GET");  // 연겳 방식 지정
    		conn.setDoInput(true);  // 서버 응답 지정 - default
    
    		conn.connect();  // 통신 링크 열기 - 트래픽 발생
    		int responseCode = conn.getResponseCode();  // 서버 전송 및 응답 결과 수신
    		if (responseCode != HttpsURLConnection.HTTP_OK) {
    			throw new IOException("HTTP error code: " + responseCode);
    		}
    
    		stream = conn.getInputStream();  // 응답 결과 스트림 확인
    
    		if (stream != null) {
    			result = readStream(stream, 500);  // 응답 결과 스트림을 문자열로 변환 - readStream 구현 필요
    		}
    
    	} finally {
    		if (stream != null)
    			stream.close();
    		if (conn != null)
    			conn.disconnect();
    	}
    
    	return result;
    }
    ```
    

### ✏️  3-4. InputStream의 변환

- InputStream → **String**
    
    ```java
    public String readStream(Inputstream stream) {  // stream = byte 모음
    	StringBuilder result = new StringBuilder();
    
    	try {
    		InputStreamReader inputStreamReader = new InputStreamReader (stream);
    		BufferedReader bufferedReader = new BufferedReader(inputStreamReader);
    
    		String readLine = bufferedReader.readLine();  // 한 줄을 문자열로
    
    		while (readLine != null) {
    			result.append(readLine + "\n");
    			readLine = bufferedReader.readLine();
    		}
    
    		bufferedReader.close();  // close() 작업 필요
    	} catch (IOException e) {
    		e.printStackTrace();
    	}
    
    	return result.toString();
    }
    ```
    
- InputSteram → **이미지**
    
    ```java
    InputStream is = null;
    ...
    Bitmap bitmap = BitmapFactory.decodeStream(is);  // size, 해상도 조절 필요할 수 있음
    ImageView imageView = (ImageView) findViewById(R.id.image_view);
    imageView.setImageBitmap(bitmap);
    ```
    

---

## 4. 권한 오류 처리

> 메인 스레드에서의 네트워크 사용 금지 (3.0 버전부터)
> 

### ✏️  4-1. 해결 방법

- 테스트나 임시로 네트워크 확인해볼 때만 사용
- Thread 또는 AsyncTask 등을 사용하여 별도의 스레드에서 네트워크 기능을 구현하는 방법으로 교체 필요

```java
StrictMode.ThreadPolicy pol = new StrictMode.ThreadPolicy.Builder().permitNetwork().build();
StrictMode.setThreadPolicy(pol);
```

---

## 5. HTTP 통신을 이용한 매개변수 전송

### ✏️ 5-1. GET 방식

- **앱 실행 중 URL 구성 시 매개변수(쿼리) 추가**
    
    → 프로토콜://호스트주소[:포트번호]/[경로]/[파일]?{쿼리}
    
    ex) *http://www.kobils.or.kr/kobisopenapi/...searchDailyBoxOfficeList.xml?key=...&targetDT=...*
    
- 쿼리 구성
    1. `?param_name=param_value&...`
    2. HashMap<String, String>으로 구성하기 용이

### ✏️ 5-2. POST 방식

- 서버로의 데이터 전송 절차
    1. `URL.openConnection()`을 사용하여 HttpsURLConnection 객체 획득
    2. setDoOutput(true) 및 기타 업로드 설정
    3. key=value 형태로 전송 값 구성 후 `getOutputStream()`을 사용하여 전송 값 준비
    4. HttpURLConnection의 `getResponseCode()`를 통해 전송 및 응답 확인
    5. `getInputStream()`을 통해 서버의 응답 확인
    6. HttpsURLConnection을 `disconnect()`
- HttpsURLConnection 객체 획득 및 기본 설정
    
    ```java
    URL url new URL("인터넷 주소");
    HttpsURLConnection conn = (HttpsURLConnection) url.openConnection();  // 연결
    
    conn.setDefaultUseCaches(false);
    conn.setDoInput(true);  // 서버 읽기 모드 지정
    conn.setDoOutput(true); // 서버 쓰기 모드 지정
    conn.setRequestMethod("POST"); // 전송 방식 POST (디폴트 GET)
    conn.setRequestProperty("content_type", "application/x-www-form-urlencoded:charset=EUC-KR");
    	// 폼 처리 지정
    ```
    
- 전송 값 및 출력 스트림 준비
    
    ```java
    String strParams = "send_data=test";  // 전송 값 (key=value)
    
    // 출력 스트림 준비
    OutputStreamWriter outStream
    	= new OutputStreamWriter (conn.getOutputStream(), "EUC-KR");
    BufferedWriter writer = new BufferedWriter(outStream);  // String -> stream
    writer.write(strParams);  // 출력 스트림에 전송 값 추가
    writer.flush();  // 서버 전송
    ```