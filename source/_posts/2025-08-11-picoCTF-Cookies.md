---
title: "(Easy) Cookies"
date: 2025-08-11 00:00:00 +0900
categories: ["< picoCTF >" , Web Exploitation]
tags: [picoctf, cookies, web hacking, write-up]
---

# picoCTF / Web Exploitation

---

#### Difficulty Level : Easy
#### Title : Cookies

---

![cookies figure1](/assets/img/picoCTF/2025-08-06-11-27-59.png)

> [Description]  
> 누가 쿠키를 안 좋아하겠어요?  
> 가장 맛있는(?) 쿠키를 찾아보세요

보아하니 먹는 쿠키는 아닌거 같고,  
웹 브라우저의 HTTP 쿠키를 의미하는 것 같다.

우선 문제에 접속해봤다.

![cookies figure2](/assets/img/picoCTF/2025-08-06-11-36-36.png)

뭔가 입력 받을 수 있는 입력폼과,  
폼 전송 역할을 할 것 같은 `Search` 버튼도 보인다.

입력폼에 "snickerdoodle" 이라는 단어가 보여서

똑같이 입력해봤더니

![cookies figure3](/assets/img/picoCTF/2025-08-06-11-39-25.png)

> 그건 쿠키에요! 하지만 별로 특별하진 않네요...

해당 문구와 함께 "I love snickerdoodle cookies!" 라는 문장을 보여준다.

아무래도 특별한 쿠키를 찾으면 되는 것 같다.

<br>
쿠키 문제인 것 같으니 개발자 도구를 열어 쿠키가 설정되어있는지, 어떻게 설정되어 있는지 확인해 봤다.  
(개발자 도구 단축키는 F12 or 페이지 빈 곳 우클릭 - 검사)

개발자 도구를 열고 `Application` 탭으로 이동하면 쿠키를 확인할 수 있다.

![cookies figure4](/assets/img/picoCTF/2025-08-06-11-43-45.png)

`name` 이라는 쿠키가 있었고,  
값은 `0` 으로 되어있었다.

요 값을 1로 바꾸고 새로고침해서 결과를 봤는데,

![cookies figure5](/assets/img/picoCTF/2025-08-06-11-46-36.png)

> 그건 쿠키에요! 하지만 별로 특별하진 않네요...

해당 문구와 함께 "snickerdoodle" 이었던게 "chocolate chip" 으로 바뀌었다.

<br>

2도 넣어봤다.

![cookies figure6](/assets/img/picoCTF/2025-08-06-11-51-20.png)

<br>

이것을 통해 알 수 있는 점은,

- 쿠키 이름을 적으면 "I love \[cookie name\] cookies!" 가 출력된다.
- 쿠키 별로 값이 다르다. (고유 쿠키값을 가진다)

<br>

지금까지 확인된 쿠키  

snickerdoodle - 0  
chocolate chip - 1  
oatmeal raisin - 2  

<br>

이제 숫자를 1씩 증가시켜보며 특별한 쿠키를 찾으면 된다!!!

...

근데 노가다 하기는 싫다...

그래서 `Burp Suite` 의 `Intruder` 기능을 이용해서 `Sniper Attack`, 자동화 공격을 진행해볼 수 있다.

![cookies figure7](/assets/img/picoCTF/2025-08-06-12-19-42.png)

우선 사진과 같이 Burp Suite 의 Proxy 서버 브라우저를 열어 다시 문제에 접속해줬다.

그리고 쿠키 생성을 위해 "snickerdoodle" 을 입력했다.

![cookies figure8](/assets/img/picoCTF/2025-08-06-12-59-26.png)

`Proxy - HTTP history` 탭에서 통신한 내용을 확인해보니

![cookies figure9](/assets/img/picoCTF/2025-08-06-13-01-29.png)

![cookies figure10](/assets/img/picoCTF/2025-08-06-13-02-41.png)

서버에 요청할 때 `Cookie: name=0` 형태로 쿠키를 설정해 요청을 보내고 있음을 확인할 수 있었다.

<br>

이제 `0`을 1씩 증가시켜보면서 특별한 쿠키를 찾아보자.

`BurpSuite - Proxy - HTTP history` 탭에서 `Request` 영역을 우클릭하고,  
`Send to Intruder` 를 선택한다.

![cookies figure11](/assets/img/picoCTF/2025-08-06-13-05-46.png)

그리고 `BurpSuite - Intruder` 탭에 들어가보면

![cookies figure12](/assets/img/picoCTF/2025-08-06-13-38-05.png)

Target과 요청 양식이 그대로 받아져온 것을 확인할 수 있다.

<br>

### 사용법

우선 바꿀 바라미터 값을 드래그해서 **블록** 처리 해준다.

![cookies figure13](/assets/img/picoCTF/2025-08-06-13-40-53.png)

이후 `Add§` 버튼을 누르게 되면,

![cookies figure14](/assets/img/picoCTF/2025-08-06-13-41-53.png)

이렇게 바뀐다.

이것은 자동화 대상의 파라미터로 지정했음을 의미한다.

<br>

이제 우측 `Payloads` 탭을 보자.

Payload type을 보면 `Simple list` 로 설정되어 있을텐데,  
이걸 `Numbers` 로 바꿔주자.

![cookies figure15](/assets/img/picoCTF/2025-08-06-13-45-01.png)

그러면 사진과 같이 바뀌게 되는데,  
`Payload configuration` 의 `Number range` 부분을 수정해줄 것이다.

- Type : 순차적 또는 무작위 (Sequential 선택)
- From : 시작할 숫자 (0)
- To : 종료할 숫자 (ex. 30)
- Step : 증가할 숫자 단위 (1)

![cookies figure16](/assets/img/picoCTF/2025-08-06-13-51-29.png)

이렇게 설정하고, 주황색 버튼 (Start Attack) 을 누르면?

![cookies figure17](/assets/img/picoCTF/2025-08-06-13-53-01.png)

사진과 같이 설정한 Payload 대로 파라미터를 수정하며 요청을 보내기 시작한다.

<br>

공격 결과들을 쭉 보고 있는데

![cookies figure18](/assets/img/picoCTF/2025-08-06-14-00-29.png)

이상하게 쿠기 값을 `18` 로 주었을 때 응답 길이가 짧은 것을 볼 수 있었다.  
(Length를 보면 다 1900대 인데 혼자 1200대임...)

해당 요청에 대한 응답 결과를 쭉 확인해보니...

![cookies figure19](/assets/img/picoCTF/2025-08-06-14-02-08.png)

![cookies figure20](/assets/img/picoCTF/2025-08-06-14-02-32.png)
(쿠키 값 변경 후 새로고침도 해본 모습)

플래그를 찾을 수 있었다.

<br>

flag : ~~picoCTF{3v3ry1_l0v3s_c00k135_bb3b3535}~~





