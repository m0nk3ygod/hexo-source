---
title: "🔒 (B) Path Finder"
date: 2025-08-01 00:00:00 +0900
categories: [Dreamhack, Wargame]
tags: [dreamhack, wargame, beginner, path, system hacking, linux, c]
published: true
password: "ahdkeyrkt12#"
abstract: "해당글은 비밀글 입니다."
message: "비밀번호를 입력하세요."
---

## Dreamhack / Wargame

---

#### Difficulty Level : Beginner  
#### Title : Path Finder

---

![pathFinder1](/assets/img/dreamhack/pathFinder1.png)

우선 Description을 토대로 ssh 명령을 사용해 해당 문제에 접속했다.

![pathFinder2](/assets/img/dreamhack/pathFinder2.png)

내부에 뭐가 있는지 보면

![pathFinder3](/assets/img/dreamhack/pathFinder3.png)

flag 파일과 chal 파일이 있다.

chal 의 권한은 -rwxr-sr-x , SGID가 설정되어 있고  
flag 의 권한은 -rw-r-S--- , SGID가 설정되어 있지만 실행 권한은 없는 상태이다. (s S 차이)

두 파일 다 소유자는 root, 그룹은 pwned로 되어있다.

whoami로 사용자를 확인해보면,

![pathFinder4](/assets/img/dreamhack/pathFinder4.png)

pwn인 것을 알 수 있다.

chal에 실행 권한이 있는 것을 확인했으니 실행을 한 번 해보았다.

![pathFinder5](/assets/img/dreamhack/pathFinder5.png)

clear 명령이 사용된 것처럼 터미널이 깨끗해지고 Tada~가 출력이 되는 것 이외에 별다른 동작은 찾아 볼 수 없었다.

<br>

해당 문제는 chal 파일을 제공하므로 다운로드하여 ida로 열어보았다.

그리고 main 함수를 디컴파일 해보니

![pathFinder6](/assets/img/dreamhack/pathFinder6.png)

setresgid() 함수로 권한을 설정하고 있는 것 같았다

setresgid() 안에서 쓰인 값은 0x3E9, 10진수로 1001이다.

이는

![pathFinder7](/assets/img/dreamhack/pathFinder7.png)

pwned의 그룹 권한으로 설정한다는 것을 의미한다.

system() 함수가 해당 권한으로 clear 명령을 실행한 다음,  
puts() 함수가 Tada~ 를 출력하고 종료하는 흐름으로 되어있었다.

<br>

해당 문제는 문제 이름인 "Path Finder"에서 힌트를 얻을 수 있었다.

바로 환경 변수를 이용하는 것.  
clear만 적으면 간단하게 화면이 지워지는 이유는 해당 동작을 하는 파일이 환경 변수에 등록되어 있기 때문이다.

export 명령을 사용하면 환경 변수를 확인하거나 설정할 수 있다.

<br>

export 명령을 사용해보면,

![pathFinder8](/assets/img/dreamhack/pathFinder8.png)

/bin 폴더를 확인할 수 있고, 해당 폴더에 들어가 확인해보면 clear 명령도 들어있는 것을 볼 수 있다.  
그래서 clear라고 터미널에 입력하든, /bin/clear라고 입력하든 똑같은 동작을 하게 된다.

이걸 이용해서 /bin/sh 를 clear로 위장시키고,  
chal이 실행될 때 pwned 권한으로 clear(/bin/sh)을 실행시켜서 shell을 얻어낸 뒤 flag를 읽는 시나리오를 생각해봤다.

<br>

우선 /bin/sh 를 현재 폴더에 clear 라는 이름으로 복사해준다.
`cp /bin/sh ./clear`

이후 환경 변수를 현재 폴더로 수정해서 clear라는 명령을 현재 폴더에서 찾게 만든다.
`export PATH="./"`

그리고 chal 파일을 실행시켜주면?

![pathFinder9](/assets/img/dreamhack/pathFinder9.png)

다음과 같이 쉘이 실행된 것을 볼 수 있다.

<br>

export 명령을 사용해서 다시 원래 환경 변수 경로를 넣어서 기존 명령을 쓸 수 있게 한 뒤

![pathFinder10](/assets/img/dreamhack/pathFinder10.png)

cat 명령으로 flag를 읽으면 된다.

![pathFinder11](/assets/img/dreamhack/pathFinder11.png)
