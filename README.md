Code-ray XG PRO Community 진단 도구 산출물
=================================================

# 1. 대시보드 (DashBoard)  
![image](https://user-images.githubusercontent.com/49786050/73178897-8c4b6b80-4155-11ea-828c-8b8017879142.png)  

위 사진의 대시보드는 현재 자신의 서비스(어플리케이션) 분석 현황을 한 눈에 볼 수 있도록 나타낸다.  
대시보드에서는 원하는 분석 파일로 접근이 용이하다. 
또한, 좌측 상단의 '점검대상 변경'을 통하여 변경하고자 하는 파일로 변경이 가능하다.  

# 2. 프리셋 추가 

![image](https://user-images.githubusercontent.com/49786050/73179044-e5b39a80-4155-11ea-9134-1a814f467f03.png)  

우측 상단의 룰 관리를 누른 뒤, 좌측의 '프리셋 관리'에서 취약점 점검 룰을 새로만들기/수정이 가능하다.  

### 프리셋이란?  
소스코드 분석에 앞서 소스코드의 취약점 점검을 위한 점검항목을 선정하는 단계이다.  
![image](https://user-images.githubusercontent.com/49786050/73179300-6a9eb400-4156-11ea-8e26-05d65641a4c7.png)  
위의 사진 처럼 서비스 개발 언어에 맞는 XSS 취약점을 선택할 수 있으며, 좌측에는 솔루션이 제공이 된다.  

### 프리셋 추가 방법 
1. 프리셋 관리 선택  

2. 새로 만들기  

3. 자신의 개발 언어에 맞는 룰을 'SW개발보안 가이드'의 47개 항목을 선정하여 프리셋을 생성할 수 있도록 한다.  

### 개발 언어 셋업 방법 

![image](https://user-images.githubusercontent.com/49786050/73179546-fa446280-4156-11ea-94de-f9f1cc1ddf7b.png)  
#### 위의 사진과 같이 자신의 개발 언어에 맞는 언어를 선택하여 분석을 할 수 있도록 한다.  

![image](https://user-images.githubusercontent.com/49786050/73179612-1811c780-4157-11ea-86da-2d6fcccbb8c4.png)  
#### 추가한 프리셋을 프리셋 목록에서 선택하여 분석할 수 있도록 한다.  

# 3. 분석 진행
![image](https://user-images.githubusercontent.com/49786050/73179784-6f179c80-4157-11ea-8788-3259fa552852.png)  
위 사진의 가장 우측 '검사 시작'을 눌러 분석을 진행한다.  
![image](https://user-images.githubusercontent.com/49786050/73180047-da616e80-4157-11ea-9eda-8d2541f0de8c.png)  
분석 진행중인 것을 우측 하단에서 확인할 수 있다.  

# 4. 분석 결과
![image](https://user-images.githubusercontent.com/49786050/73180197-2f04e980-4158-11ea-9364-8e977e51b9cd.png)  
위 사진처럼 정적분석을 통하여 추출된 취약점을 높음/보통/낮음 형태로 확인 할 수 있다.  

소스코드 분석
==============
# 5. 사용자 하드디스크를 통한 쿠키 노출 / 보안속성 미적용을 통한 쿠키 정보 노출  
![image](https://user-images.githubusercontent.com/49786050/73180705-3678c280-4159-11ea-8458-8271fb0eda52.png)  
**90번째 라인**을 통해 setMaxAge의 값이 필요이상으로 높은것을 확인 할 수 있다.  
쿠키는 기본적으로 클라이언트의 디스크에 상주하기에 쿠키의 저장 기간이 길어질 수록 공격자에게는  
보다 많은 시간과 기회를 줄 수 있어, 이는 시스템 취약점과도 연결될 소지가 있다.  

#### 대응방안은 setMaxAge의 값을 사용자가 접속하고 있을때마다 갱신할 수 있도록  
#### 음수 값으로 -60x2로 설정 및 로그아웃 시 setMaxAge(0)으로 셋업하여 쿠키를 삭제한다.  

#### 분석 코드의 서비스는 http 프로토콜을 사용하고 있으나, 향후 https를 사용한다면 전송 간 쿠키 노출 방지를 위해  
#### setSecure(true);를 설정하여 https 통신일때만 쿠키를 전송하여, 쿠키를 보호한다.    

# 6. XSS 취약점
![image](https://user-images.githubusercontent.com/49786050/73182193-ee0ed400-415b-11ea-998f-c66719ea3e73.png)  
**27번째 라인**을 통해 스크립트 문이 서버에 저장되어 사용자의 정보유출 및 CSRF까지 유발할 소지가 있다.  
아이디/내용/비밀번호는 boardWritePro.jsp로 전달이 되어 처리되어진다.  
아래는 boardWritePro.jsp의 소스코드 일부이다.  
![image](https://user-images.githubusercontent.com/49786050/73182320-33330600-415c-11ea-9eb2-8f94ecffef93.png)  
위 사진의 **29~33번째 라인**은 적절한 입력값 검증 없이 게시글이 DB에 저장됨을 확인할 수 있다.  

#### 대응방안은 게시글의 입력값에 스크립트문이 들어오지 못하도록 replaceAll로 이스케이프 처리한다.  
#### ex) conent.replaceAll("<","&lt"); content.replaceAll(">","&gt");  
#### content.replaceAll("&","&amp"); content.replaceAll("\"","&quto");  

# 7. 위험한 형식의 파일 업로드
![image](https://user-images.githubusercontent.com/49786050/73183094-9d987600-415d-11ea-82d5-8644249dd6ae.png)  
분석도구에는 잡히지 않았으나 파일 업로드 시, 적절한 확장자/사이즈 검증 부재를 통해  
공격자는 스크립트 파일 삽입이 가능해져 시스템 내부 침투와 외부 연결이 가능할 소지가 있다.  

#### 대응방안은 업로드할 파일의 크기 제한/파일 실행여부/mime Type 확인/허용할 화이트 리스트 확장자를  
#### if문을 통해 검증하여 업로드 할 수 있도록 한다.  
#### ex) if(mr.getSize() > MAX_FILESIZE) { //ERROR }  
#### String file_name = mr.getOriginalFilename.toLowerCase();  
#### String[] array_file_Name = file_name.split(\\.);
#### if (array_file_Name[1].endsWith("doc") || array_file_Name[1].endsWith("hwp") || 등등) { //파일업로드 }  

# 8. 제거되지 않은 디버그 코드
![image](https://user-images.githubusercontent.com/49786050/73184011-16e49880-415f-11ea-8007-14f42ad517ab.png)  
개발 시 테스트를 위한 디버그 코드가 실제 배포시, 제거되지 않아 공격자에게 의도치 않은 정보 누출이 가능해진다.   

#### 대응방안은 디버그 코드를 제거한다.  

이외에도 ~~인증 시도 부재, 비밀번호 정책 부재, 전송간 암호화, 평문 저장 등 취약점들이 존재하였다~~  
##### 1- 인증 시도 부재는 5회이상 로그인 실패시, 계정 잠금/일정시간 잠금을 통해 패스워드 브루토 포싱을 방어한다.  
##### 2- 비밀번호 정책 부재는 숫자+영문자+특수문자 9자이상을 통해 패스워드 크랙의 시간을 늘어나게 한다.    
##### 3- 전송간 암호화는 전송 시 암호화 또는 HTTPS 프로토콜을 통하여 전송 간 암호화를 진행한다.  
##### 4- 평문 저장은 서버에서 저장 시 사용자의 비밀번호를 SHA-256~512, Bcrypt 등 해시 함수화하여 안전하게 저장한다.  

#### 취약점 분석을 하며 부족한 부분은 행자부 47개 취약점 가이드라인을 참고한다.
<https://www.kisa.or.kr/public/laws/laws3_View.jsp?cPage=6&mode=view&p_No=259&b_No=259&d_No=88&ST=T&SV=>  
