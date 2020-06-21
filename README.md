스마트 모바일 프로그래밍 ASSA 최종 보고서
===================================

목차   
-----

### 1.소개
>#### 1-1 주제선정이유
>#### 1-2 앱 개발중 사용한 기능
>#### 1-3 앱 개발로 얻는효과

### 2. 기능 구현
>#### 2-1 로그인 및 회원가입
>>##### 2-1-1 서버구축
>>##### 2-1-2 안드로이드 스튜디오와 서버연결
>>##### 2-1-3 카카오 로그인
>>##### 2-1-4 원형 프로필
>#### 2-2 Google Map
>>##### 2-2-1 지도상 내위치 표시
>>##### 2-2-2 지도상 대결상대 표시
>>##### 2-2-3 대결 가능 거리 설정
>#### 2-3 Unity를 이용한 게임
>>##### 2-3-1 고스톱 알고리즘
>>>###### 2-3-1-1 Player
>>>###### 2-3-1-2 AI
>>##### 2-3-2 특수효과
>>>###### 2-3-2-1 효과음
>>>###### 2-3-2-2 카드 효과(Card Hitting)
>>>###### 2-3-2-3 보간 함수   




<hr/>   

## 1. 소개

### 1-1 주제선정이유
고스톱은 사행성 게임으로만 불려왔다. 하지만 게임이란 것은 전 연령이 즐길 수 있어야 진정한 게임이라 생각한다. 그렇기에 어린 연령층도 우리나라의 전통 놀이 중 하나인 고스톱을 즐기게 하기 위해 개발했다. 또한 게임만 하는 것이 아닌 위치에 따라 거리를 측정하며 진행하여 나날히 증가하는 비만율에 도움이 되고자 운동과 게임 두 가지를 잡는 방향으로 개발하여 게임을 하며 흥미를 느끼고 걷기를 하게 되게끔 하였다.   
![주제선정이유](https://user-images.githubusercontent.com/62593452/85231705-799f3f00-b434-11ea-9621-898667310757.png)   

### 1-2 앱 개발중 사용한 기능
지도, 카카오 서버를 이용한 간편로그인, 유니티를 이용한 게임 개발   

### 1-3 앱 개발로 얻는효과
연령에 구애받지 않는 고스톱앱의 개발로 인해 어린 연령층의 사용자도 우리나라 전통 놀이인 고스톱을 배울 수 있고 고스톱에 사용되는 화투는 알츠하이머 환자에게 일종의 두뇌 게임, 인지 훈련으로 실행되기도 하며 환자의 기억력 훈련에 도움이 된다고 한다.   
<hr/>

## 2. 기능 구현

### 2-1 로그인 및 회원가입
로그인 및 회원가입시 데이터저장을 위한 서버를 생성해야한다.
>#### 2-1-1 서버구축
무료 서버 호스팅 사이트인 dothome을 이용하여 ASSA 서버를 생성한다.
![assa서버](https://user-images.githubusercontent.com/62593452/85231105-781f4800-b42f-11ea-89a4-71158ffcdc17.PNG)

MySQL 관리 웹페이지로 들어가 회원가입시 기입하는 정보를 저장하는 테이블 생성한다.
![sql테이블](https://user-images.githubusercontent.com/62593452/85231107-79507500-b42f-11ea-92d9-59a3e70dccef.PNG)

>#### 2-1-2 안드로이드 스튜디오와 서버연결
Volley API 를 이용한 HTTP 통신을 위해 build.gradle파일에 implementation해준다.
<pre>
<code>
implementation 'com.android.volley:volley:1.1.1'
</code>
</pre>
안드로이드 스튜디오에서 서버로 정보를 전송하기위한 Login.php / Register.php 파일을 생성한다.   
##### Login.php
<pre>
<code>

    $con = mysqli_connect("localhost", "assa", "tkdaud1!", "assa");
    mysqli_query($con,'SET NAMES utf8');

    $userID = $_POST["userID"];
    $userPassword = $_POST["userPassword"];
    
    $statement = mysqli_prepare($con, "SELECT * FROM USER WHERE userID = ? AND userPassword = ?");
    mysqli_stmt_bind_param($statement, "ss", $userID, $userPassword);
    mysqli_stmt_execute($statement);


    mysqli_stmt_store_result($statement);
    mysqli_stmt_bind_result($statement, $userID, $userPassword, $userName, $userAge);

    $response = array();
    $response["success"] = false;
 
    while(mysqli_stmt_fetch($statement)) {
        $response["success"] = true;
        $response["userID"] = $userID;
        $response["userPassword"] = $userPassword;
        $response["userName"] = $userName;
        $response["userAge"] = $userAge;        
    }

    echo json_encode($response);

</code>
</pre>

##### Register.php
<pre>
<code>
    $con = mysqli_connect("localhost", "assa", "tkdaud1!", "assa");
    mysqli_query($con,'SET NAMES utf8');

    $userID = $_POST["userID"];
    $userPassword = $_POST["userPassword"];
    $userName = $_POST["userName"];
    $userAge = $_POST["userAge"];

    $statement = mysqli_prepare($con, "INSERT INTO USER VALUES (?,?,?,?)");
    mysqli_stmt_bind_param($statement, "sssi", $userID, $userPassword, $userName, $userAge);
    mysqli_stmt_execute($statement);


    $response = array();
    $response["success"] = true;
 
   
    echo json_encode($response);
</code>
</pre>

2개의 파일을 FileZilla를 이용하여 html폴더에 추가해준다.    
![fillzilla](https://user-images.githubusercontent.com/62593452/85231104-76558480-b42f-11ea-8bfa-6faaf3dd6ac2.PNG)   


>#### 2-1-3 카카오 로그인
카카오톡은 대한민국에서 가장 큰 sns로 우리나라의 국민 전부가 쓰고 있다고 해도 과언이 아닌 어플로, 카카오 로그인을 통하여 간편하게 어플을 즐길 수 있도록 하였다. 
카카오에 APP등록하기   
[kakao app등록 링크](https://developers.kakao.com/)   
![카카오톡등록](https://user-images.githubusercontent.com/62593452/85231709-7c019900-b434-11ea-98f0-0b163564188c.png)   
Buildgradle(app)파일에 카카오 로그인을 위한 sdk, url을 추가해준다.
<pre>
<code>
dependencies {
    implementation 'com.kakao.sdk:usermgmt:1.29.0'
}

</code>
</pre>
<pre>
<code>
allprojects {
    repositories {

        flatDir {
            dirs 'libs'
        }
        google()
        jcenter()
        maven { url 'https://devrepo.kakao.com/nexus/content/groups/public/' }
    }
}
</code>
</pre>
manifest파일에 카카오 로그인에 필요한 인터넷연결을 permission하고 AppKey와 네이티브 키를 입력한다.
<pre>
<code>
uses-permission android:name="android.permission.INTERNET"
</pre>
</code>
<pre>
<code>
meta-data
    android:name="com.kakao.sdk.AppKey"
    android:value="네이티브 키"
</pre>
</code>

카카오 로그인을 하기위해 해시 키를 생성 후 카카오 디벨로퍼에 입력해준다.
##### 해시 키 생성
<pre>
<code>
private void getAppKeyHash() {
    try {
        PackageInfo info = getPackageManager().getPackageInfo(getPackageName(), PackageManager.GET_SIGNATURES);
        for (Signature signature : info.signatures) {
            MessageDigest md;
            md = MessageDigest.getInstance("SHA");
            md.update(signature.toByteArray());
            String something = new String(Base64.encode(md.digest(), 0));
            Log.e("Hash key", something);
        }
    } catch (Exception e) {
        // TODO Auto-generated catch block
        Log.e("name not found", e.toString());
    }
}
</code>
</pre>
##### logcat창에 나오는 HashKey
![해시키](https://user-images.githubusercontent.com/62593452/85231710-7c9a2f80-b434-11ea-8124-d118791b0e7b.png)    
##### 해시 키 등록
![해시키등록](https://user-images.githubusercontent.com/62593452/85231707-7ad06c00-b434-11ea-8227-65cfb3187d6c.png)     

>#### 2-1-4 원형 프로필
원형 프로필을 만들기 위해서는 Buildgradle(app)에 지정을 해준다.
<pre>
<code>
annotationProcessor 'com.github.bumptech.glide:compiler:4.8.0'
implementation 'de.hdodenhof:circleimageview:2.2.0'
</code>
</pre>   
layout 설정 후 string변수를 이용하여 LoginActivity에서 보낸 정보를 받아온다.
<pre>
<code>
CircleImageView ivProfile = findViewById(R.id.ivProfile);

   Intent intent = getIntent();

    strProfile = intent.getStringExtra("profile");

Glide.with(this).load(strProfile).into(ivProfile);
</code>
</pre>
##### 원형프로필
![원형프로필](https://user-images.githubusercontent.com/62593452/85231708-7c019900-b434-11ea-8394-b2ee8c21b71c.png)   




>### 2-2 Google Map
>>#### 2-2-1 지도상 내위치 표시
>>#### 2-2-2 지도상 대결상대 표시
>>#### 2-2-3 대결 가능 거리 설정

>### 2-3 Unity를 이용한 게임
>>#### 2-3-1 고스톱 알고리즘
>>>##### 2-3-1-1 Player
>>>##### 2-3-1-2 AI

>>#### 2-3-2 특수효과
>>>##### 2-3-2-1 효과음
효과음이 필요한 오브젝트가 많아 오브젝트마다 효과음을 넣으면 비효율적이어서 효과음 오브젝트를 만들어서 효과음이 필요한 부분에 각자 코드로 적용시켜 효율적인 효과음 재생이 가능하다.
>>>##### 2-3-2-2 카드 효과(Card Hitting)
전체적인 카드의 움직임은 앱을 구동시키는 기기의 성능에 따라 움직임이 자연스러워 보이기도 하고 부자연스러워 보이기도 하기에 최대한 성능에 구애받지 않는 퍼포먼스를 보여주기 위해 Time.time을 이용해 시간이 흘러가는것에 따라 카드의 움직임을 조절가능하게 하였다.
>>>##### 2-3-2-3 보간 함수
각종 보간함수를 통해 오브젝트를 부드럽게 이동시키거나 회전 가능하게 하였다. 보간함수 같은 경우는


