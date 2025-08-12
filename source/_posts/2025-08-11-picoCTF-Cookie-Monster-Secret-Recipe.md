---
title: "(Easy) Cookie Monster Secret Recipe"
date: 2025-08-11 00:00:00 +0900
categories: [picoCTF, Web Exploitation]
tags: [picoctf, cookie monster secret recipe, web hacking, write-up]
---

# picoCTF / Web Exploitation

---

#### Difficulty Level : Easy
#### Title : Cookie Monster Secret Recipe

---

![CMSR figure1](/assets/img/picoCTF/2025-08-05-14-41-00.png)

> [Description]  
> 쿠키 몬스터가 자신의 최고 비밀 쿠키 레시피를 웹사이트 어딘가에 숨겨두었어요.  
> 당신은 열정 넘치는 쿠키 탐정!  
> 이 맛있는 비밀을 찾아낼 수 있을까요?  

Description에 쿠키라는 말이 반복되는 것을 보니 "Cookie/Session"과 관련있는 문제인가 보다.

링크되어 있는 `here`를 클릭해 문제에 접속해주자.

![CMSR figure2](/assets/img/picoCTF/2025-08-05-14-45-10.png)

접속하면 로그인 페이지가 뜨는데, CTF 로그인 국룰인 guest/guest 로 로그인을 시도해봤다.

![CMSR figure3](/assets/img/picoCTF/2025-08-05-14-46-41.png)

권한이 없다고 한다. (당연함.)

그리고 출력된 내용은 다음과 같다.

```
쿠키 몬스터가 말하길:  
“비밀번호 따윈 필요 없어. 쿠키만 있으면 돼!”

힌트:  
“최근에 쿠키(cookie) 확인해봤어?”
```

바로 개발자 도구에 들어가 쿠키를 확인해봤다.  
(단축키 : F12)

![CMSR figure4](/assets/img/picoCTF/2025-08-05-14-49-31.png)

들어가보니 `secret_recipe` 라는 이름의 쿠키를 찾을 수 있었다.

해당 쿠키는 `cGljb0NURntjMDBrMWVfbTBuc3Rlcl9sMHZlc19jMDBraWVzXzk2RjU4REFCfQ%3D%3D` 라는 값을 가지고 있었는데,  
뒤에 저 "%3D"은 "=" 기호를 의미한다.

"=" 기호 2개로 끝난다...?  
base64로 인코딩 된 값임을 유추할 수 있다.

해당 값을 base64 디코딩 사이트를 통해 디코딩 해보니...

![CMSR figure5](/assets/img/picoCTF/2025-08-05-14-54-44.png)

바로 플래그를 얻을 수 있었다.

<br>

flag : ~~picoCTF{c00k1e_m0nster_l0ves_c00kies_96F58DAB}~~

