---
title: "(Easy) where are the robots"
date: 2025-08-11 00:00:00 +0900
categories: ["< picoCTF >" , Web Exploitation]
tags: [picoctf, where are the robots, web hacking, write-up]
---

# picoCTF / Web Exploitation

---

#### Difficulty Level : Easy
#### Title : where are the robots

---

![water figure1](/assets/img/picoCTF/2025-08-08-00-14-53.png)

> [Description]  
> 당신은 로봇들을 찾을 수 있나요?

Description을 보면 로봇이라는 말이 나온다.

로봇을 언급하는거보니 `robots.txt` 를 이용해야함을 어느정도 유추해 볼 수 있다.

### robots.txt

`robots.txt` 파일이란, 웹사이트의 루트 디렉토리에 위치하는 **텍스트 파일**로,  
검색 엔진의 **웹 크롤러(로봇)**에게 해당 사이트의 어떤 페이지를 크롤링(수집)할 수 있고,  
어떤 페이지는 수집하지 말아야 하는지 지시하는 역할을 한다.

자세한 내용은 공식 문서 참고  
(https://developers.google.com/search/docs/crawling-indexing/robots/create-robots-txt?hl=ko)

<br>

우선 문제에 접속해봤다.

![water figure2](/assets/img/picoCTF/2025-08-08-00-33-06.png)

ㄷㄷㄷ 무섭다...

로봇이 어디있냐고 물어보고 있다.

주소창에 `robots.txt` 를 입력해 해당 파일이 존재하는지 확인해봤더니

`https://jupiter.challenges.picoctf.org/problem/56830/robots.txt`

![water figure3](/assets/img/picoCTF/2025-08-08-00-34-28.png)

잘 있는 것을 볼 수 있다.

그리고

![water figure4](/assets/img/picoCTF/2025-08-08-00-35-52.png)

`/1bb4c.html` 파일이 크롤링 되지 않도록 Disallow 되어있는 것을 볼 수 있다.

<br>

크롤링만 되지 않을 뿐, 접근은 가능할 수 있으니 해당 경로를 주소창에 입력해봤다.

`https://jupiter.challenges.picoctf.org/problem/56830/1bb4c.html`

![water figure5](/assets/img/picoCTF/2025-08-08-00-38-07.png)

해당 파일을 확인해보니, 플래그를 얻을 수 있었다.

<br>

flag : ~~picoCTF{ca1cu1at1ng_Mach1n3s_1bb4c}~~



