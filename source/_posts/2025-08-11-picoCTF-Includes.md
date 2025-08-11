---
title: "(Easy) Includes"
date: 2025-08-11 00:00:00 +0900
categories: [picoCTF, Web Exploitation]
tags: [picoctf, includes, web hacking, write-up]
---

# picoCTF / Web Exploitation

---

#### Difficulty Level : Easy
#### Title : Incldues

---

![includes figure1](/assets/img/picoCTF/2025-08-06-10-43-38.png)

> [Description]  
> 플래그를 얻을 수 있겠나요?

Description 에서 뭔가 힌트 얻을게 없어보여서 바로 문제에 접속해보았다.

접속해보니

![includes figure2](/assets/img/picoCTF/2025-08-06-10-46-37.png)

이런 페이지가 나오는데, 해석해보면

> 인클루드(Include)에 대하여
>
> 많은 프로그래밍 언어나 기타 컴퓨터 파일에는 include(때로는 copy 또는 import)라고 불리는 지시문이 있습니다.
> 이 지시문은 두 번째 파일의 내용을 원래 파일에 삽입하도록 합니다.
>
> 이렇게 포함되는 파일을 copybook 또는 헤더 파일(header file)이라고 부릅니다.
> 주로 프로그램 데이터의 물리적 레이아웃, 절차적 코드의 일부, 또는 선행 선언(forward declaration)을 정의하는 데 사용되며,
> 캡슐화(encapsulation)와 코드 재사용을 촉진합니다.
>
> 출처: 위키백과 “Include directive” 문서

Include에 대한 설명이 나와있다.

<br>
밑에 `Say hello` 버튼도 있길래 눌러봤다.

![includes figure3](/assets/img/picoCTF/2025-08-06-10-49-37.png)

> 이 코드는 다른 파일에 작성되어 있다!

누르면 요런게 뜬다.

include 관련해서 부가적인 설명을 위해 존재하는 버튼인 것 같다.

<br>
가장 먼저 개발자 도구로 해당 페이지가 어떤 구조를 가지고 동작하는지 살펴봤다.  
(개발자 도구 단축키 F12 or 페이지 우클릭 - 검사)

![includes figure4](/assets/img/picoCTF/2025-08-06-10-55-51.png)

button 태그를 보면

![includes figure5](/assets/img/picoCTF/2025-08-06-10-57-03.png)

onclick 되었을 때, `greetings();` 라는 함수가 실행되도록 되어있다.

<br>
그리고 body 태그의 시작 부분을 보면

![includes figure6](/assets/img/picoCTF/2025-08-06-11-01-32.png)

`script.js`라는 파일을 불러와서 실행하도록 하고 있다.

<br>
정리해보면, 

`script.js` 라는 외부 자바스크립트 파일을 가져와 실행시키고 있고,  

`greetings()` 라는 함수는 그 파일에 정의되어, 해당 페이지에서 사용되고 있는 듯 하다.

<br>
태그 속성값을 보니 `script.js` 파일의 경로는 현재 위치한 페이지 소스 경로와 같다.  
(src = "./script.js" 라고 볼 수 있음)

주소창에 파일명을 입력해 내용을 볼 수 있는지 확인해보자.

`http://saturn.picoctf.net:51201/script.js`

![includes figure7](/assets/img/picoCTF/2025-08-06-11-11-49.png)

역시 `greetings()` 함수의 동작이 정의되어 있었고, 

`Say hello` 버튼을 눌렀을 때 나왔던 문구도 확인할 수 있었다.

<br>
그리고 바로 아래 주석으로 무언가 적혀있는데...

![icludes figure8](/assets/img/picoCTF/2025-08-06-11-13-29.png)

중괄호로 끝나는 모습을 보니 플래그의 일부분으로 보인다!

다른 곳에 플래그의 일부분이 또 존재함을 예상할 수 있다.

<br>
그래서 다시 원래 페이지로 돌아와 코드를 봤는데,

![includes figure9](/assets/img/picoCTF/2025-08-06-11-15-10.png)

`script.js` 말고도 외부 파일을 끌어와 쓰는 부분이 한 곳 더 있다. 

바로...

![includes figure10](/assets/img/picoCTF/2025-08-06-11-16-56.png)

head 태그 내부에 있는 link 태그 부분이다.

<br>
해당 페이지의 디자인을 담당하는 css코드도 `style.css` 파일을 불러와 사용하고 있다.

이것도 주소창에 입력해서 확인해보자.

`http://saturn.picoctf.net:51201/style.css`

![includes figure11](/assets/img/picoCTF/2025-08-06-11-20-21.png)

`style.css` 파일 내부에서 앞부분의 플래그를 얻을 수 있었고,

두 플래그를 이어붙여서 완성된 플래그를 얻을 수 있다!

<br>

flag : ~~picoCTF{1nclu51v17y_1of2_f7w_2of2_b8f4b022}~~
