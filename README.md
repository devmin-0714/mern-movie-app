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
export const API_KEY = ''
export const IMAGE_BASE_URL = 'http://image.tmdb.org/t/p'
```

## 2. Landing Page

- **전체적인 Template를 간단하게 만들기**

- **Movie API에서 가져온 모든 데이터를 STATE에 넣기**

- **MainImage Component를 만들기**

```js
// LandingPage.js
import React, { useEffect, useState } from 'react'
import { API_URL, API_KEY, IMAGE_BASE_URL } from '../../Config'
import { FaCode } from 'react-icons/fa'
import MainImage from './Sections/MainImage'

function LandingPage() {
  const [Movies, setMovies] = useState([])
  const [MainMovieImage, setMainMovieImage] = useState(null)

  useEffect(() => {
    const endpoint = `${API_URL}movie/popular?api_key=${API_KEY}&language=en-US&page=1`
    fetch(endpoint)
      .then((response) => response.json())
      .then((response) => {
        console.log(response)
        setMovies([response.results])
        setMainMovieImage(response.results[0])
      })
  }, [])

  return (
    <div style={{ width: '100%', margin: '0' }}>
      {/* Main Image */}
      {MainMovieImage && (
        <MainImage
          image={`${IMAGE_BASE_URL}w1280${MainMovieImage.backdrop_path}`}
          title={MainMovieImage.original_title}
          text={MainMovieImage.overview}
        />
      )}

      <div style={{ width: '85%', margin: '1rem auto' }}>
        <h2>Movies by latest</h2>
        <hr />

        {/* Movie Grid Cards */}
      </div>

      <div style={{ display: 'flex', justifyContent: 'center' }}>
        <button> Load More</button>
      </div>
    </div>
  )
}

export default LandingPage

// LandingPage/Sections/MainImage.js
import React from 'react'

function MainImage(props) {
  return (
    <div
      style={{
        background: `linear-gradient(to bottom, rgba(0,0,0,0)
        39%,rgba(0,0,0,0)
        41%,rgba(0,0,0,0.65)
        100%),
        url('${props.image}'), #1c1c1c`,
        height: '500px',
        backgroundSize: '100%, cover',
        backgroundPosition: 'center, center',
        width: '100%',
        position: 'relative',
      }}
    >
      <div>
        <div
          style={{
            position: 'absolute',
            maxWidth: '500px',
            bottom: '2rem',
            marginLeft: '2rem',
          }}
        >
          <h2 style={{ color: 'white' }}> {props.title} </h2>
          <p style={{ color: 'white', fontSize: '1rem' }}> {props.text}</p>
        </div>
      </div>
    </div>
  )
}

export default MainImage
```
