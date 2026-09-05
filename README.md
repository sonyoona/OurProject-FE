# OurProject — Frontend

코드와 에러 로그를 함께 올려 질문하는 **개발자용 게시판**의 프론트엔드입니다.
일반 본문과 별개로 **코드 블록·에러 로그를 각각 분리된 필드로** 다루는 것이 일반 게시판과 다른 점입니다.

백엔드 저장소 → [OurProject-BE](https://github.com/sonyoona/OurProject-BE) (Spring Boot)

<br>

## 담당한 부분

3인 팀 프로젝트이며, 프론트엔드 화면과 API 연동을 맡았습니다.

| 영역 | 내용 |
|---|---|
| 게시판 | 전체 조회 · 상세 조회 · 글 작성 · 수정 · 삭제 |
| 카테고리 | 카테고리 선택에 따른 목록 부분 렌더링 |
| 댓글 | 댓글 작성 · 수정 · 삭제 및 게시글 상세 화면 연동 |
| 인증 | 로그인 · 회원가입 화면 구현 |
| 상태 관리 | Redux로 로그인 사용자 정보를 전역 공유하도록 구조 변경 |

<br>

## 기술 스택

| 구분 | 사용 기술 |
|---|---|
| 프레임워크 | React 19 |
| 빌드 도구 | Vite 6 |
| 라우팅 | React Router 7 |
| 상태 관리 | Redux Toolkit, React Redux |
| UI | React Bootstrap |
| 알림 | SweetAlert2 |

<br>

## 화면 구성

라우트는 `App.jsx`에 정의되어 있습니다.

| 경로 | 화면 | 컴포넌트 |
|---|---|---|
| `/` | 게시글 목록 (메인) | `BoardListView` |
| `/detail/:articleId` | 게시글 상세 + 댓글 | `ArticleDetailView` |
| `/add` | 글쓰기 | `AddArticle` |
| `/editarticle/:boardId` | 글 수정 | `EditArticle` |
| `/login` | 로그인 | `LoginForm` |
| `/signup` | 회원가입 | `SignupForm` |

<br>

## 프로젝트 구조

```
src/
├── App.jsx                    # 라우트 정의
├── main.jsx                   # 진입점, Redux Provider 연결
├── services/
│   └── ApiClient.jsx          # 백엔드 REST API 호출을 한 곳에 모은 클래스
├── store/
│   ├── store.js               # Redux store
│   └── userSlice.js           # 로그인 사용자 상태 (setUser / clearUser)
└── clientview/
    ├── board/                 # 게시판 — 목록, 상세, 작성, 수정, 댓글
    │   ├── BoardListView.jsx
    │   ├── ArticleDetailView.jsx
    │   ├── AddArticle.jsx
    │   ├── EditArticle.jsx
    │   ├── Article.jsx
    │   ├── BoardHeader.jsx
    │   └── CommentList.jsx
    └── login/                 # 로그인, 회원가입
        ├── LoginForm.jsx
        └── SignupForm.jsx
```

### 설계에서 신경 쓴 부분

**1. API 호출을 `ApiClient` 한 곳으로 모았습니다.**

각 컴포넌트가 직접 `fetch`를 부르면 서버 주소나 엔드포인트가 바뀔 때마다 모든 화면을 찾아다녀야 합니다.
호출을 전부 정적 메서드로 모아두어, 주소 변경은 `SERVER_URL` 한 줄만 고치면 되도록 했습니다.

**2. 로그인 정보를 Redux로 끌어올렸습니다.**

처음에는 로그인 사용자 정보를 props로 내려보냈는데, 게시글·댓글·헤더가 모두 `userId`를 필요로 하면서
중간 컴포넌트들이 쓰지도 않는 값을 전달만 하는 상태가 됐습니다.
`userSlice`로 옮겨 필요한 컴포넌트가 직접 꺼내 쓰도록 바꿨습니다.

<br>

## API 연동

백엔드는 기본적으로 `http://localhost:8080`으로 가정합니다. (`src/services/ApiClient.jsx`의 `SERVER_URL`)

| 기능 | 메서드 | 엔드포인트 |
|---|---|---|
| 게시글 목록 | `GET` | `/board/article/list` |
| 게시글 상세 | `GET` | `/board/article/list/{articleId}` |
| 글 작성 | `POST` | `/board/article/{userId}` |
| 글 수정 | `PUT` | `/board/article/{articleId}` |
| 글 삭제 | `DELETE` | `/board/article/{articleId}` |
| 댓글 목록 | `GET` | `/board/comment/list/{boardId}` |
| 댓글 작성 | `POST` | `/board/comment/{articleId}/{userId}` |
| 댓글 수정 · 삭제 | `PUT` `DELETE` | `/board/comment/{commentId}` |
| 좋아요 조회 · 등록 · 취소 | `GET` `POST` `DELETE` | `/board/good/...` |
| 회원가입 · 조회 | `POST` `GET` | `/board/user`, `/board/user/{userId}` |

게시글은 본문(`content`) 외에 **`codeContent`(코드)** 와 **`errorContent`(에러 로그)** 를 따로 가지며,
댓글도 같은 방식으로 `comment`와 `codeComment`를 분리해 주고받습니다.

<br>

## 실행 방법

```bash
# 1. 의존성 설치
npm install
npm install @reduxjs/toolkit bootstrap   # package.json에 누락되어 별도 설치 필요

# 2. 백엔드 기동 (localhost:8080)
#    https://github.com/sonyoona/OurProject-BE 참고

# 3. 개발 서버 실행
npm run dev
```

> `@reduxjs/toolkit`(`store/userSlice.js`)과 `bootstrap`(`App.jsx`의 CSS import)이
> 코드에서는 쓰이지만 `package.json`에는 빠져 있습니다. 위 명령으로 함께 설치해야 실행됩니다.
