---
title: "🔒 blog settings"
date: 2025-07-07
categories: ["< settings >", Hexo]
tags: []
password: "hexo settings"
abstract: "이 글은 비밀번호가 필요합니다."
message: "비밀번호를 입력하세요."
---

# 1. Hexo 블로그 생성 및 로컬에서 열기

### Windows

```bash
# Node.js와 Git 설치 (필수)
winget install OpenJS.NodeJS
winget install Git.Git

# Hexo 설치
npm install -g hexo-cli

# 블로그 생성
hexo init my-blog
cd my-blog
npm install

# 로컬 서버 실행
hexo clean && hexo g && hexo s
```

→ 브라우저에서 http://localhost:4000 접속.

### macOS

```bash
# Node.js와 Git 설치
brew install node git

# Hexo 설치
npm install -g hexo-cli

# 블로그 생성
hexo init my-blog
cd my-blog
npm install

# 로컬 서버 실행
hexo clean && hexo g && hexo s
```

→ 브라우저에서 http://localhost:4000 접속.

<br>

# 2. 블로그 초기 설정

### __config.yml

```yaml
title: 블로그 제목
subtitle: 부제목
description: 설명
author: 작성자
language: en
timezone: Asia/Seoul

url: https://USERNAME.github.io
permalink: :year/:month/:day/:title/
theme: vivia

future: false
```

<br>

# 3. vivia 테마 설치 및 적용

```bash
cd my-blog
git clone https://github.com/Saicaca/hexo-theme-vivia themes/vivia
```

`/themes/vivia/example_config.vivia.yml`

해당 파일을 루트 폴더로 옮기고, `_config.vivia.yml` 로 이름 변경

내부 설정은 다음과 같이 해줬음.

```yml
# Hexo Configuration
## Docs: https://hexo.io/docs/configuration.html
## Source: https://github.com/hexojs/hexo/

# Site
title: m0nk3ygod's Blog
subtitle: 'Security Blog'
description: 'm0nk3ygod room'
keywords:
author: Seungwon Lee
language: en
timezone: 'Asia/Seoul'

# URL
## Set your site url here. For example, if you use GitHub Page, set url as 'https://username.github.io/project'
url: https://m0nk3ygod.github.io
permalink: :year/:month/:day/:title/
permalink_defaults:
pretty_urls:
  trailing_index: true # Set to false to remove trailing 'index.html' from permalinks
  trailing_html: true # Set to false to remove trailing '.html' from permalinks

# Directory
source_dir: source
public_dir: public
tag_dir: tags
archive_dir: archives
category_dir: categories
code_dir: downloads/code
i18n_dir: :lang
skip_render:

# Writing
new_post_name: :title.md # File name of new posts
default_layout: post
titlecase: false # Transform title into titlecase
external_link:
  enable: true # Open external links in new tab
  field: site # Apply to the whole site
  exclude: ''
filename_case: 0
render_drafts: false
post_asset_folder: false
relative_link: false
future: false
syntax_highlighter: highlight.js
highlight:
  line_number: true
  auto_detect: false
  tab_replace: ''
  wrap: true
  hljs: false
prismjs:
  preprocess: true
  line_number: true
  tab_replace: ''

# Home page setting
# path: Root path for your blogs index page. (default = '')
# per_page: Posts displayed per page. (0 = disable pagination)
# order_by: Posts order. (Order by date descending by default)
index_generator:
  path: ''
  per_page: 10
  order_by: -date

# Category & Tag
default_category: uncategorized
category_map:
tag_map:

# Metadata elements
## https://developer.mozilla.org/en-US/docs/Web/HTML/Element/meta
meta_generator: true

# Date / Time format
## Hexo uses Moment.js to parse and display date
## You can customize the date format as defined in
## http://momentjs.com/docs/#/displaying/format/
date_format: YYYY-MM-DD
time_format: HH:mm:ss
## updated_option supports 'mtime', 'date', 'empty'
updated_option: 'mtime'

# Pagination
## Set per_page to 0 to disable pagination
per_page: 10
pagination_dir: page

# Include / Exclude file(s)
## include:/exclude: options only apply to the 'source/' folder
include:
exclude:
ignore:

# Extensions
## Plugins: https://hexo.io/plugins/
## Themes: https://hexo.io/themes/
theme: vivia # landscape

# Deployment
## Docs: https://hexo.io/docs/one-command-deployment
deploy:
  type: git
  repo: https://github.com/m0nk3ygod/m0nk3ygod.github.io.git
  branch: main

plugins:
  - hexo-blog-encrypt

symbols_count_time:
  symbols: true
  time: true
  total_symbols: true
  total_time: true
  awl: 4      # average word length
  wpm: 275    # words per minute

markdown:
  render:
    html: true
    breaks: false
    linkify: true
    typographer: true
  plugins:
    - markdown-it-emoji
    - markdown-it-footnote       # [^1] 각주
    - markdown-it-deflist        # 정의 목록: term \n : desc
    - markdown-it-task-lists     # - [ ] 체크박스
    - markdown-it-attrs          # {#id .class} 같은 속성 부여
    - markdown-it-anchor         # 헤딩 앵커
    - markdown-it-toc-done-right # [TOC] 목차
    - markdown-it-container      # :::note 같은 컨테이너

marked:
  gfm: true
  breaks: false
  pedantic: false
  smartLists: true
  smartypants: true
```

![blog figure1](/assets/img/blog/2025-08-10-03-19-08.png)

<br>

# 4. GitHub 레포지토리 생성 및 기초 설정

### 1. GitHub에서 새 레포 생성

- 레포 이름: USERNAME.github.io
- Public 선택
- README, .gitignore, License는 생성 안 함

### 2. Hexo 폴더에서 git 연결

```bash
git init
git branch -M main
git remote add origin https://github.com/USERNAME/USERNAME.github.io.git
```

### 3. 배포 플러그인 설치

```bash
npm install hexo-deployer-git --save
```

### 4. _config.yml 설정

```yml
deploy:
  type: git
  repo: https://github.com/USERNAME/USERNAME.github.io.git
  branch: main
```

### 5. 배포

```bash
hexo clean && hexo g && hexo d
```

<br>

# 4. Post 작성 시 지켜야할 Front-Matter

```markdown
---
title: 글 제목
date: 2025-08-10 10:00:00
categories: [카테고리]
tags: [태그1, 태그2]
---
본문 내용...
``` 

<br>

# 5. 비밀글 설정하는 법

### 1. 설치

```bash
npm install hexo-blog-encrypt --save
```

### 2. _config.yml 설정

```yml
plugins:
  - hexo-blog-encrypt
```

### 3. 글에 암호 적용

```yml
---
title: 비밀글 예시
date: 2025-08-10 12:00:00
password: 비밀번호123
abstract: 이 글은 비밀글입니다.
message: 비밀번호를 입력하세요.
tags:
---
여기는 비밀글 내용.
```

### 4. 빌드 및 배포

<br>

# 6. hexo 주요 명령어 정리

| 명령어 | 설명 |
|--------|------|
| `hexo init <폴더명>` | 새로운 Hexo 블로그 프로젝트 생성 |
| `hexo new "제목"` | 새 글 생성 (`source/_posts/`에 `.md` 파일 생성) |
| `hexo new page "페이지명"` | 새 페이지 생성 (`source/페이지명/index.md` 생성) |
| `hexo clean` | 캐시 및 이전 빌드 파일 삭제 |
| `hexo g` 또는 `hexo generate` | 정적 파일 생성 (`public/` 폴더에 출력) |
| `hexo s` 또는 `hexo server` | 로컬 서버 실행 (`http://localhost:4000`) |
| `hexo d` 또는 `hexo deploy` | 블로그 배포 (`_config.yml`의 deploy 설정 필요) |
| `hexo clean && hexo g && hexo s` | 캐시 삭제 → 정적 파일 생성 → 로컬 서버 실행 (연속 실행) |
| `hexo clean && hexo g && hexo d` | 캐시 삭제 → 정적 파일 생성 → 배포 (연속 실행) |

# 7. 캐시 강재 새로고침 설정

themes/vivia/layout/_partial/head.ejs 내부 헤드태그 안에 다음 을 삽입

```html
<script>
  (function () {
    // 1) 템플릿에서 주입되는 빌드 ID (Hexo 빌드 시각)
    window.__BUILD_ID__ = <%= +new Date(site.time || Date.now()) %>;
    const KEY = 'last_build_id';
    const last = Number(localStorage.getItem(KEY) || 0);
    const now  = Number(window.__BUILD_ID__ || Date.now());
    if (!last || last !== now) {
      // 2) 서비스워커/Cache Storage 비우기 (원점의 캐시만)
      Promise.resolve().then(async () => {
        if ('serviceWorker' in navigator) {
          const regs = await navigator.serviceWorker.getRegistrations();
          for (const r of regs) await r.unregister();
        }
        if ('caches' in window) {
          const keys = await caches.keys();
          await Promise.all(keys.map(k => caches.delete(k)));
        }
      }).then(() => {
        // 3) HTTP 캐시는 못 지우므로, 쿼리 파라미터로 강제 새 요청
        const url = new URL(location.href);
        url.searchParams.set('_r', String(now));
        localStorage.setItem(KEY, String(now));
        location.replace(url.toString()); // 1회만 새로고침
      }).catch(() => {
        localStorage.setItem(KEY, String(now));
      });
    }
  })();
  </script>
```