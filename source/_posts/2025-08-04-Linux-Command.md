---
title: "Basic Linux Command"
date: 2025-08-04 00:00:00 +0900
categories: [Tech, Linux]
tags: [basic, linux, command, study]
---

# Linux Command

리눅스 명령어
- CLI를 통한 다양한 리눅스 쉘 명령어
- 해킹 공부에 가장 기초적이며 필수적인 기술
- 매일 매일 사용하다보면 저절로 외워짐

명령어 종류
- 기본 명령어
- 파일 및 디렉토리
- 스크립팅과 자동화
- 시스템 및 네트워킹

옵션과 플래그
- 명령어 뒤에 하이픈(-)으로 시작하는 무언가.
	- 옵션 : 인자를 추가로 받는 경우
	- 플래그 : 인자를 받지 않고 단독으로 쓰이는 경우

---

### 기본 명령어
- sudo : root 권한 사용
- hostname : 현재 접속한 호스트장치 이름
- whoami :  현재 접속한 사용자 이름
- pwd : 현재 작업중인 디렉터리 ( print working directory )
- cd '경로' : 작업 디렉터리 변경 ( change directory )
	- 띄어쓰기 
		- 백슬래시( \ )를 이용
		- 쌍따옴표를 이용
- id : 현재 사용자의 고유 아이디를 보여줌
- group 'user name' : 사용자가 속해 있는 그룹을 알려준다.
	- 'user name' : whoami로 확인 가능
- uname : 현재 접속중인 시스템의 정보를 알려준다. 
	- -a : 자세하게 보여줌 ( all 약어 )
	- -r : 커널의 버전을 알려줌 
- man 'command' : 명령에 대해서 설명해줌. ( manual 약어 )
- clear : 터미널 창을 깨끗하게 지워줌

### 읽기 명령어
- ls : 디렉터리 내용을 열거함. ( list 의 약어 )
	- -l 
	- -a : 숨김 파일까지 확인
- cat 'file name' : 지정한 파일 내용을 출력해줌 ( concatenate-'연결하다'의 약어 )
- less 'file name' : 파일이 클 경우에 사용하고, 파일 내부를 들여다 보듯 출력해줌.
- head 'file name' : 1~10번째 줄까지만 출력을 해줌.
	- -n 'number' : 숫자만큼 출력
- tail 'file name' : 맨 마지막 줄부터 -10번째 줄까지만 출력을 해줌.
	- -n 'number' : 숫자만큼 출력

### 쓰기 명령어
- touch 'file name' : 지정한 file name으로 파일을 생성
- echo '내용' > 'file name' : 작성한 내용을 지정한 file name으로 파일을 만들고 작성.
	- echo '내용' : 문자열 출력
- cp '복사할 파일' 'file' : 복사할 파일을 복사
	- file : 이때 file은 경로로 작성할 수도 있고 아닐 수 있는데, 마지막은 꼭 복사한 파일의 이름을 지정해줘야한다.
- mv '옮길 파일' 'file' : 옮길 파일을 옮김. 응용으로 이름 변경
	- file : 이때 file은 옮길 경로를 작성하며, 그냥 파일 이름만 사용할 경우 작업 중인 디렉터리에 이름만 바뀐 채 이동 된다. ( 이름 변경 효과 )
- mkdir 'directory name' : 디렉터리 생성
- rm 'file name' : 파일 삭제
	- -r : 폴더 삭제 시 사용. 왜냐하면 폴더 내부에 또 다른 파일이 있을 수 있으니 그것까지 지우는 옵션이다. ( recursive의 약어 )
- chmod '부여할 권한' 'file name' : 파일의 권한을 변경한다.
	- '부여할 권한' : r w x 각각 read write excution 으로 읽기, 쓰기, 실행 권한을 가르킨다. r = 4, w = 2, x = 1 로도 표기 가능하다.
- base64 'file name' : file 안의 내용을 base64로 인코딩 한다.
	- -d : 디코딩 한다.

### chmod 퍼미션 - 개념
- r( read ) : 4
- w( write ) : 2
- x( excution ) : 1
644 => rw- | r-- | r--
    소유자 | 그룹 | 일반 사용자

### 파일 & 디렉토리 명령어
- find '찾을 경로' '옵션' '옵션 값' : 파일이나 디렉터리를 찾을 때 사용
	- '찾을 경로' : 보통은 현재 디렉토리에서 찾기 위해 ' . '을 쓰며, 컴퓨터 전체를 뒤져보는거면 ' / '를 쓰기도 한다.
	- '옵션', '옵션 값'
		- -type : 찾을 파일의 타입을 지정
			- f : file
			- d : direcroty
		- -name : 찾을 파일 이름을 지정
			- "file name" : ' * '과 같은 와일드 카드나 정규표현식이 사용되기도 함.
		- -exec 'command'; : 꼭 세미콜론을 붙여야하며, 결과 값은 " { } " 를 사용하면 가져올 수 있다.
			- 예시 : find . -name "groot8-flag.txt" -exec cp {} . \;
- grep '찾을 문자열' 'file name' : 파일 내부에 특정 문자열을 검색할 때 사용
- file 'file name' : 파일 형식 및 파일에 대한 정보를 확인할 때 사용
- strings 'file name' : 파일 내부에 문자열들 중 읽을 수 있는 문자열만 출력해줌.
- diff 'option' 'file 1' 'file 2' : file1과 file2의 차이점을 보여줌. ( different 의 약어 )

### 아카이브 (압축파일)
- 7z e 'file name' : 7-zip 형태의 아카이브 파일을 압축 해제할 때 사용
- unzip 'file name' : zip 형태의 아카이브 파일을 압축 해제할 때 사용
- gzip -d 'file name' : gzip 형태의 아카이브 파일을 압축 해제할 때 사용
	- 주의할 점은 gzip의 경우 확장자가 ' ~.gz ' 의 형태인지 확인 후 사용. 아닐시 mv 명령으로 확장자를 변경해주고 사용할 것.
- tar -xvf 'file name' : tar 형태의 아카이브 파일을 압축 해제할 때 사용 