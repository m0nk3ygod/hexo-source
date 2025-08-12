---
title: "(Easy) logon"
date: 2025-08-11 00:00:00 +0900
categories: [picoCTF, Web Exploitation]
tags: [picoctf, logon, web hacking, write-up]
---

# picoCTF / Web Exploitation

---

#### Difficulty Level : Easy
#### Title : logon

---

![logon figure1](/assets/img/picoCTF/2025-08-07-23-25-58.png)

> [Description]  
> 공장은 모든 사용자에게 무언가를 숨기고 있어요.  
> Joe라는 이름으로 로그인해서, 그들이 뭘 보고 있었는지 알아낼 수 있을까요?

음... Description 만 봤을 때는 무슨 말인지 모르겠다...

일단 한 번 접속해보자.

![logon figure2](/assets/img/picoCTF/2025-08-07-23-31-35.png)

유저명과 비밀번호를 입력할 수 있는 폼이 존재하고, `Sign in` 버튼을 통해 제출할 수 있는 것으로 보인다.

우측 상단에는 `Home`과 `Sign Out` 링크도 보인다.

<br>

일단 "Joe"에 이름으로 로그인하라고 했으니 로그인 해보았다.

비밀번호는 모르겠어서 그냥 joe라고 입력했는데...

![logon figure3](/assets/img/picoCTF/2025-08-07-23-36-09.png)  
(Username : joe | Password : joe)

> 성공: 로그인했어요! 하지만 플래그를 볼 수 있을지는 잘 모르겠네요.

로그인에 성공했다...!

근데 플래그는 볼 수 없다고 한다... (아마도 권한이 없는 듯 하다)

<br>

개발자 도구로 얻을 수 있는게 있을까 해서 한번 열어 보았다.  
(개발자 도구 - F12 or 페이지 빈 곳 우클릭 - 검사)

![logon figure4](/assets/img/picoCTF/2025-08-07-23-49-16.png)

권한과 관련있어보이니, 쿠키 쪽을 살펴보기로 했다.

<br>

`Application` 탭으로 이동해 쿠키를 봤는데,

![logon figure5](/assets/img/picoCTF/2025-08-08-00-02-55.png)

![logon figure6](/assets/img/picoCTF/2025-08-08-00-03-03.png)

?!?!?!?!

`admin` 이라는 쿠키가 있었고, `False` 라는 값이 할당되어 있었다.

이 녀석이 뭔가 admin의 권한 자격을 확인하고 있는 듯 했고,  

<br>

값을 `True`로 변경하고 새로고침 해보니

![logon figure7](/assets/img/picoCTF/2025-08-08-00-05-44.png)

플래그를 얻을 수 있었다.

<br>

flag : ~~picoCTF{th3_c0nsp1r4cy_l1v3s_6edb3f5f}~~

