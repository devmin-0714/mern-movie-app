# react-movie-app

- 출처 : [John Ahn님 GitHub](https://github.com/jaewonhimnae)

---

## 0. 초기 설정

- [boiler-plate 클론](https://github.com/jaewonhimnae/boilerplate-mern-stack)

- `클라이언트`와 `서버`에 `Dependencies` 다운받기

  - `npm install`
  - `Server`은 **Root** 경로, `Client`는 **client폴더** 경로

- `server/config/dev.js` 파일 설정
  - `MongoDB` 로그인
  - 클러스터, 유저 아이디와 비밀번호 생성 후 `dev.js` 파일에 넣는다.

```js
// server/config/dev.js
module.exports = {
  mongoURI:
    'mongodb+srv://blackb0x0714:<password>@react-boiler-plate.ovbtd.mongodb.net/<dbname>?retryWrites=true&w=majority',
}
```

---

## 1. The MovieDB API

- **The MovieDB Website API_KEY 받기**
  - [TMDB](https://www.themoviedb.org/)
  - `Profile` -> `Settings` -> `API` -> `Request an API Key` -> `Developer` -> `API Key (v3 auth)`
  - 동일한 부분
    - `API_URL` : `https://api.themoviedb.org/3/`
  - 이미지는 어떻게 가져오나?
    - 동일한 UR 부분 : `https://image.tmdb.org/t/p`
    - 이미지 사이즈 : `/original`
    - 유니크한 이름 이미지 이름 : `/wwwemzK...svg`

```js
// components/Config.js
export const API_URL = 'https://api.themoviedb.org/3/'
export const IMAGE_BASE_URL = 'https://image.tmdb.org/t/p'
```
