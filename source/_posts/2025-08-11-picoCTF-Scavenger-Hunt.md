---
title: "(Easy) Scavenger Hunt"
date: 2025-08-11 00:00:00 +0900
categories: [picoCTF, Web Exploitation]
tags: [picoctf, scavenger hunt, web hacking, write-up]
---

# picoCTF / Web Exploitation

---

#### Difficulty Level : Easy
#### Title : Scavenger Hunt

---

![sh figure1](/assets/img/picoCTF/2025-08-07-16-58-47.png)

> [Description]  
> 이 사이트 http://mercury.picoctf.net:39491/ 주변에 흥미로운 정보가 숨겨져 있어요. 찾을 수 있겠나요?

Description 만 읽어봤을 때는 사이트 내부에 플래그가 숨겨져 있나보다. (당연히...)

바로 사이트에 접속해봤다.

![sh figure2](/assets/img/picoCTF/2025-08-07-17-00-53.png)

사이트에 접속하면 이런 모습을 볼 수 있는데,  
`How`라는 글자를 클릭하면 위 사진처럼 나오고, 

`What`이라는 글자를 클릭하면

![sh figure3](/assets/img/picoCTF/2025-08-07-17-02-29.png)

이런게 출력된다.

<br>
이것말고는 딱히 다른 기능은 없어보이니 바로 개발자도구를 켜서 정보들을 얻어보자.  
(개발자 도구 - F12 or 페이지 빈 곳 우클릭 - 검사)

![sh figure4](/assets/img/picoCTF/2025-08-07-17-05-36.png)

`Elements` 탭에서 HTML 구조를 확인하고 있었는데...

![sh figure5](/assets/img/picoCTF/2025-08-07-17-05-50.png)

주석에 "first part of the flag" 라고 하며 플래그 포맷 시작 부분이 있는 것을 발견했다.

아무래도 사이트 곳곳에 몇개의 파트로 플래그가 쪼개져서 숨겨져있고,  

이걸 전부 찾아 조합하면 되는 것 같다.

<br>
그래서 숨겨져있는 곳이 어디일까 생각하며 HTML 구조를 쭉 봤는데,

![sh figure6](/assets/img/picoCTF/2025-08-07-17-10-42.png)

`mycss.css`, `myjs.js` 와 같이 사용자가 정의한 것처럼 보이는 파일들을 불러와 사용하고 있는 것을 볼 수 있었고,

주소창에 해당 파일들을 입력해 접근해봤다.

<br>
먼저 `mycss.css`부터 확인해 봤다.

`http://mercury.picoctf.net:39491/mycss.css`

![sh figure7](/assets/img/picoCTF/2025-08-07-17-13-45.png)

내용을 확인해보면, 맨 마지막줄에

![sh figure7](/assets/img/picoCTF/2025-08-07-17-14-06.png)

이렇게 2번째 플래그 조각을 찾을 수 있었다.

<br>
그래서 바로 `myjs.js` 파일도 확인해봤다.

`http://mercury.picoctf.net:39491/myjs.js`

![sh figure8](/assets/img/picoCTF/2025-08-07-17-15-00.png)

여기는 플래그가 따로 보이지 않았다...

하지만

![sh figure8](/assets/img/picoCTF/2025-08-07-17-15-20.png)

> 구글이 저의 웹사이트를 검색 결과에 노출하지 않게 하려면 어떻게 해야 할까요?

라는 주석이 있었다.

### robots.txt

`robots.txt` 파일이란, 웹사이트의 루트 디렉토리에 위치하는 **텍스트 파일**로,  
검색 엔진의 **웹 크롤러(로봇)**에게 해당 사이트의 어떤 페이지를 크롤링(수집)할 수 있고,  
어떤 페이지는 수집하지 말아야 하는지 지시하는 역할을 한다.

자세한 내용은 공식 문서 참고  
(https://developers.google.com/search/docs/crawling-indexing/robots/create-robots-txt?hl=ko)

<br>
해당 주석을 통해 `robots.txt` 파일이 존재한다는 힌트를 얻었고,  

바로 `robots.txt` 파일에 접근해봤다.

![sh figure9](/assets/img/picoCTF/2025-08-07-17-22-20.png)

확인해보니, 3번째 플래그 조각을 찾을 수 있었다.

그리고 또하나의 주석을 확인할 수 있었는데,

![sh figure10](/assets/img/picoCTF/2025-08-07-17-23-20.png)

> 이건 아파치 서버인 것 같아요… 다음 플래그에 접근할 수 있겠나요?

아파치 서버라는 힌트와 함께 플래그에 "접근" 할 수 있는지 물어보고 있다.

아마 아파치 웹 서버에서만 사용되는 파일에 있다는 말인 것 같고,  
접근이라는 말을 들었을 때 `.htaccess` 파일이 생각났다.

### .htaccess
.htaccess (Hypertext Access) 는 **Apache 웹 서버**에서 디렉터리 단위로 접근 제어 설정을 할 수 있도록 해주는 파일이다.

- 특정 디렉터리 접근 차단
- 기본 인덱스 페이지 지정
- 리다이렉션 설정
- 비밀번호 보호 설정

등등...

아무튼 아파치 웹 서버라면 해당 파일이 존재할 것이니 접근해보면,

`http://mercury.picoctf.net:39491/.htaccess`

![sh figure11](/assets/img/picoCTF/2025-08-07-19-21-20.png)

4번째 플래그 조각도 찾을 수 있었다.

<br>
내부에 또 주석이 보이는데,

![sh figure12](/assets/img/picoCTF/2025-08-07-19-25-12.png)

> 난 맥에서 웹사이트 만드는 걸 정말 좋아해요. 거기에 많은 정보를 저장할 수도 있어서 좋아요.

이 사람은 맥(MacOS)를 이용해서 해당 사이트를 개발한 것 같다.

그리고 저장을 굳이 "Store"라고 언급한 것을 볼 수 있는데, (심지어 S는 대문자로 표기함.)

이것들을 조합하면 `.DS_Store` 라는 파일을 떠올릴 수 있다.

### .DS_Store
.DS_Store (Desktop Services Store) 는 macOS에서 디렉터리를 열면 자동으로 생성되는 숨김 파일이다.

Finder (Windows의 익스플로러 같은 앱) 가 해당 폴더의 **아이콘 위치, 보기 설정** 등 메타 데이터를 저장하기 위해 만들게 된다.

웹 사이트를 작업할 때, 맥 사용자가 이 파일을 실수로 서버에 업로드 하는 경우가 종종 있다.

<br>
그래서 이 파일도 접근해보면,

`http://mercury.picoctf.net:39491/.DS_Store`

![sh figure13](/assets/img/picoCTF/2025-08-07-19-32-26.png)

마지막 5번째 플래그 조각도 찾을 수 있다!

이것들을 전부 조합하면 플래그를 얻을 수 있다.

<br>

flag : ~~picoCTF{th4ts_4_l0t_0f_pl4c3s_2_lO0k_f7ce8828}~~


