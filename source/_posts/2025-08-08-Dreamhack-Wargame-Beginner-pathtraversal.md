---
title: "🔒 (B) pathtraversal"
date: 2025-08-08 00:00:00 +0900
categories: [Dreamhack, Wargame]
tags: [dreamhack, wargame, beginner, pathtraversal, ssrf]
published: true
password: "ahdkeyrkt12#"
abstract: "해당글은 비밀글 입니다."
message: "비밀번호를 입력하세요."
---

## Dreamhack / Wargame

---

#### Difficulty Level : Beginner  
#### Title : pathtraversal

---

![ssrf1](/assets/img/dreamhack/2025-08-08-17-11-21.png)

```python
#!/usr/bin/python3
from flask import Flask, request, render_template, abort
from functools import wraps
import requests
import os, json

users = {
    '0': {
        'userid': 'guest',
        'level': 1,
        'password': 'guest'
    },
    '1': {
        'userid': 'admin',
        'level': 9999,
        'password': 'admin'
    }
}

def internal_api(func):
    @wraps(func)
    def decorated_view(*args, **kwargs):
        if request.remote_addr == '127.0.0.1':
            return func(*args, **kwargs)
        else:
            abort(401)
    return decorated_view

app = Flask(__name__)
app.secret_key = os.urandom(32)
API_HOST = 'http://127.0.0.1:8000'

try:
    FLAG = open('./flag.txt', 'r').read() # Flag is here!!
except:
    FLAG = '[**FLAG**]'

@app.route('/')
def index():
    return render_template('index.html')

@app.route('/get_info', methods=['GET', 'POST'])
def get_info():
    if request.method == 'GET':
        return render_template('get_info.html')
    elif request.method == 'POST':
        userid = request.form.get('userid', '')
        info = requests.get(f'{API_HOST}/api/user/{userid}').text
        return render_template('get_info.html', info=info)

@app.route('/api')
@internal_api
def api():
    return '/user/<uid>, /flag'

@app.route('/api/user/<uid>')
@internal_api
def get_flag(uid):
    try:
        info = users[uid]
    except:
        info = {}
    return json.dumps(info)

@app.route('/api/flag')
@internal_api
def flag():
    return FLAG

application = app # app.run(host='0.0.0.0', port=8000)
# Dockerfile
#     ENTRYPOINT ["uwsgi", "--socket", "0.0.0.0:8000", "--protocol=http", "--threads", "4", "--wsgi-file", "app.py"]
```

소스 코드를 보면

```python
info = requests.get(f'{API_HOST}/api/user/{userid}').text
# API_HOST = http://127.0.0.1:8000
```

외부 라우트 /get_info 가 입력받은 userid로 서버가 직접 내부 API를 호출하는 것을 볼 수 있는데,

```python
if request.remote_addr == '127.0.0.1': ... else abort(401)
```

내부 API는 @internal_api로 `127.0.0.1` 만 허용한다.

즉, 직접 `/api/flag` 에 접근하려고하면 401 에러를 내뱉지만,  
`/get_info` 를 통해 서버가 접근하면 flag를 읽을 수 있다.

`/get_info` 페이지에서 "guest"라고 입력한 뒤 요청을 보내고 패킷을 잡아봤다.

![ssrf2](/assets/img/dreamhack/2025-08-08-17-09-53.png)

body에 `userid=guest` 가 날아가는 것을 볼 수 있는데, 

/api/user/{userid} 이런식으로 처리되니  
입력 값을 guest가 아닌 `../flag` 로 바꾸어 전송하면 

/api ~~/user/..~~ /flag  
-> `/api/flag`

가 될 것이다.

전송해보면?

![ssrf3](/assets/img/dreamhack/2025-08-08-17-10-19.png)

이렇게 플래그가 출력된다.