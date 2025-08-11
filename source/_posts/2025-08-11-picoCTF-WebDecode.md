---
title: "(Easy) WebDecode"
date: 2025-08-11 00:00:00 +0900
categories: [picoCTF, Web Exploitation]
tags: [picoctf, WebDecode, web hacking, write-up]
---

# picoCTF / Web Exploitation

---

#### Difficulty Level : Easy
#### Title : WebDecode

---

![webdecode figure1](/assets/img/picoCTF/2025-08-06-09-34-47.png)

> [Description]  
> 웹 검사 도구(Web Inspector) 사용법을 알고 있나요?

Description 을 읽어보면 "Web Inspector"의 사용법을 아냐고 물어보고 있다.

<br>
### Web Inspector

웹 검사 도구란, 웹사이트의 구조와 동작을 분석하거나 수정해볼 수 있는 도구로,  
개발자 도구를 열어서 해당 도구를 사용해볼 수 있다.

개발자 도구를 여는 단축키는 F12 이다.

<br>
이제 문제에 접속해보자!

![webdecode figure2](/assets/img/picoCTF/2025-08-06-09-59-19.png)

접속해보면 약간 정신없어보이는(?) 페이지를 볼 수 있다.

<br>
해당 페이지는 총 3개의 페이지로 이동할 수 있었는데,  

`HOME`, `ABOUT`, `CONTACT`  

이렇게 3개이다.

다른 페이지도 구경해보자.

![webdecode figure3](/assets/img/picoCTF/2025-08-06-10-09-51.png)  
(ABOUT)

![webdecode figure4](/assets/img/picoCTF/2025-08-06-10-10-07.png)
(CONTACT)

어...

일단 도움이 되는 정보는 없는 것 같다. (응원 메세지만 가득하다.)

<br>
그렇다면 이제 웹 검사 도구를 한번 써보자.

F12를 눌러 개발자 도구를 열어준다.  
(또는 페이지 빈곳을 우클릭한 뒤, "검사"를 클릭해준다.)

그리고 Elements 탭에 들어가면, 해당 페이지의 HTML 구조를 볼 수 있다.

![webdecode figure5](/assets/img/picoCTF/2025-08-06-10-14-58.png)

먼저 `HOME` 페이지의 HTML 구조는 어떤게 있나 살펴봤는데...

![webdecode figure6](/assets/img/picoCTF/2025-08-06-10-20-01.png)

딱히 얻을만한 정보는 없었다...

<br>
다음으로 `ABOUT` 페이지를 조사해봤는데,

![webdecode figure7](/assets/img/picoCTF/2025-08-06-10-22-53.png)

??!?!?!?!

section 태그의 notify_true 라는 속성을 보니

![webdecode figure8](/assets/img/picoCTF/2025-08-06-10-35-29.png)

누가 봐도 수상한, 정상적이지 않은 값이 들어있다.

생김새를 보아하니 Base64로 인코딩되어 있는 것 같은데,

바로 디코딩 사이트를 이용해 디코딩 해보았다.

![webdecode figure9](/assets/img/picoCTF/2025-08-06-10-39-37.png)

결과는...?

![webdecode figure10](/assets/img/picoCTF/2025-08-06-10-39-48.png)

이렇게 플래그를 얻을 수 있었다.

<br>

flag : ~~picoCTF{web_succ3ssfully_d3c0ded_02cdcb59}~~



