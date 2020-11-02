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

## 3. Grid Card Component

- **Grid Card Component 만들기**

  - `ES7 React/Redux/GraphQL/React-Native snippets` 익스텐션 설치
  - 한줄 ROW `24사이즈` = `4개 컬럼 x 6사이즈`

```js
// LandingPage.js
import GridCards from '../commons/GridCards'
import { Row } from 'antd'

function LandingPage() {
    ...
    useEffect(() => {
        ...
        fetch(endpoint)
        .then(response => response.json())
        .then(response => {
            console.log(response)
            setMovies([...response.results])
            setMainMovieImage(response.results[0])
        })

    }, [])

    return (

        <div style={{ width: '100%', margin: '0' }}>
            ...

              {/* Movie Grid Cards */}
              <Row gutter={[16, 16]} >
                  {Movies && Movies.map((movie, index) => (
                      <React.Fragment key={index}>
                          <GridCards
                              image={movie.poster_path ?
                                  `${IMAGE_BASE_URL}w500${movie.poster_path}` : null}
                              movieId={movie.id}
                              movieName={movie.original_title}
                          />
                      </React.Fragment>
                  ))}
              </Row>

            </div>
            ...
        </div>
    )
}

// components/views/commons/GridCard.js
import React from 'react'
import { Col } from 'antd'

function GridCards(props) {

    return (
        <Col lg={6} md={8} xs={24}>
            <div style={{ position: 'relative' }}>
                <a href={`/movie/${props.movieId}`} >
                    <img
                    style={{ width: '100%', height: '320px' }}
                    src={props.image}
                    alt={props.movieName} />
                </a>
            </div>
        </Col>
    )
}

export default GridCards
```

## 4. Load More Button 만들기

- **Load More Function 만들기**

```js
// LandingPage.js
function LandingPage() {
    ...
    const [CurrentPage, setCurrentPage] = useState(0)

    useEffect(() => {
        const endpoint = `${API_URL}movie/popular?api_key=${API_KEY}&language=en-US&page=1`;
        fetchMovies(endpoint)
    }, [])

    const fetchMovies = (endpoint) => {
        fetch(endpoint)
            .then(response => response.json())
            .then(response => {
                console.log(response)
                setMovies([...Movies, ...response.results])
                setMainMovieImage(response.results[0])
                setCurrentPage(response.page)
            })
    }

    const loadMoreItems = () => {
        const endpoint = `${API_URL}movie/popular?api_key=${API_KEY}&language=en-US&page=${CurrentPage + 1}`;
        fetchMovies(endpoint)
    }

    return (

        <div style={{ width: '100%', margin: '0' }}>
          ...

          <div style={{ display: 'flex', justifyContent: 'center' }}>
              <button onClick={loadMoreItems}> Load More</button>
          </div>

        </div>
    )
}
```

## 5. Movie Detail 페이지 만들기

- **특정 영화에 해당하는 자세한 정보를 가져오기**
  - `props.match.params.movieId`
- **무비 APi에서 가져온 정보를 State에다가 집어 넣기**
- **전체적 Template 간단히 만들기**

```js
// App.js
import MovieDetail from './views/MovieDetail/MovieDetail'
function App() {
  return (
    <Suspense fallback={<div>Loading...</div>}>
      <NavBar />
      <div style={{ paddingTop: '69px', minHeight: 'calc(100vh - 80px)' }}>
        <Switch>
          <Route
            exact
            path="/movie/:movieId"
            component={Auth(MovieDetail, null)}
          />
        </Switch>
      </div>
      <Footer />
    </Suspense>
  )
}

// MovieDetail.js
import React, { useEffect, useState } from 'react'
import { API_URL, API_KEY, IMAGE_BASE_URL } from '../../Config'
import MainImage from '../../views/LandingPage/Sections/MainImage'
import MovieInfo from './Sections/MovieInfo'

function MovieDetail(props) {
  let movieId = props.match.params.movieId
  const [Movie, setMovie] = useState([])

  useEffect(() => {
    let endpointInfo = `${API_URL}movie/${movieId}?api_key=${API_KEY}`

    fetch(endpointInfo)
      .then((response) => response.json())
      .then((response) => {
        console.log(response)
        setMovie(response)
      })
  }, [])

  return (
    <div>
      {/* Header */}
      <MainImage
        image={`${IMAGE_BASE_URL}w1280${Movie.backdrop_path}`}
        title={Movie.original_title}
        text={Movie.overview}
      />

      {/* Body */}
      <div style={{ width: '85%', margin: '1rem auto' }}>
        {/* Movie Info */}
        <MovieInfo movie={Movie} />

        <br />

        {/* Actors Grid*/}
        <div
          style={{ display: 'flex', justifyContent: 'center', margin: '2rem' }}
        >
          <button> Toggle Actor View </button>
        </div>
      </div>
    </div>
  )
}

export default MovieDetail

// MovieDetail/Sections/MovieInfo.js
import React from 'react'
import { Descriptions, Badge } from 'antd'

function MovieInfo(props) {
  let { movie } = props

  return (
    <Descriptions title="Movie Info" bordered>
      <Descriptions.Item label="Title">
        {movie.original_title}
      </Descriptions.Item>
      <Descriptions.Item label="release_date">
        {movie.release_date}
      </Descriptions.Item>
      <Descriptions.Item label="revenue">{movie.revenue}</Descriptions.Item>
      <Descriptions.Item label="runtime">{movie.runtime}</Descriptions.Item>
      <Descriptions.Item label="vote_average" span={2}>
        {movie.vote_average}
      </Descriptions.Item>
      <Descriptions.Item label="vote_count">
        {movie.vote_count}
      </Descriptions.Item>
      <Descriptions.Item label="status">{movie.status}</Descriptions.Item>
      <Descriptions.Item label="popularity">
        {movie.popularity}
      </Descriptions.Item>
    </Descriptions>
  )
}

export default MovieInfo
```

## 6. 영화 출연진들 가져오기

- **영화에 나오는 Crews Information을 가져오기**
- **가져온 Crew 정보를 State에 넣기**
- **State에 보관된 Data들을 화면에 보여주기**

```js
// MovieDetail.js
import GridCards from '../commons/GridCards'
import { Row } from 'antd'

function MovieDetail(props) {
    ...
    const [Casts, setCasts] = useState([])
    const [ActorToggle, setActorToggle] = useState(false)

    useEffect(() => {
        ...

        let endpointCrew = `${API_URL}movie/${movieId}/credits?api_key=${API_KEY}`

        ...

        fetch(endpointCrew)
            .then(response => response.json())
            .then(response => {
                setCasts(response.cast)
            })

    }, [])

    const toggleActorView = () => {
        setActorToggle(!ActorToggle)
    }

    return (
        <div>
            ...

              {/* Actors Grid*/}
              <div style={{ display: 'flex', justifyContent: 'center', margin: '2rem' }}>
                  <button onClick={toggleActorView}> Toggle Actor View </button>
              </div>

              {ActorToggle &&
                  <Row gutter={[16, 16]} >
                      {Casts && Casts.map((cast, index) => (
                          <React.Fragment key={index}>
                              <GridCards
                                  image={cast.profile_path ?
                                      `${IMAGE_BASE_URL}w500${cast.profile_path}` : null}
                                  characterName={cast.name}
                              />
                          </React.Fragment>
                      ))}
                  </Row>
              }

            </div>

        </div>
    )
}

// LandingPage.js
import React, { useEffect, useState } from 'react'
import { API_URL, API_KEY, IMAGE_BASE_URL } from '../../Config'
import MainImage from './Sections/MainImage'
import GridCards from '../commons/GridCards'
import { Row } from 'antd'

function LandingPage() {
    ...
    return (

      ...

        {/* Movie Grid Cards */}
        <Row gutter={[16, 16]} >

            {Movies && Movies.map((movie, index) => (
                <React.Fragment key={index}>
                    <GridCards
                        landingPage
                        image={movie.poster_path ?
                            `${IMAGE_BASE_URL}w500${movie.poster_path}` : null}
                        movieId={movie.id}
                        movieName={movie.original_title}
                    />
                </React.Fragment>
            ))}

        </Row>

      ...

    )
}

// views/commons/GridCards.js
import React from 'react'
import { Col } from 'antd'

function GridCards(props) {

    if (props.landingPage) {
        return (
            <Col lg={6} md={8} xs={24}>
                <div style={{ position: 'relative' }}>
                    <a href={`/movie/${props.movieId}`} >
                        <img style={{ width: '100%', height: '320px' }} src={props.image} alt={props.movieName} />
                    </a>
                </div>
            </Col>
        )
    } else {
        return (
            <Col lg={6} md={8} xs={24}>
                <div style={{ position: 'relative' }}>
                    <img style={{ width: '100%', height: '320px' }} src={props.image} alt={props.characterName} />
                </div>
            </Col>
        )
    }
}

export default GridCards
```

## 7. Favorite 버튼 만들기

- **Favorite Model 만들기**
  - `userFrom`, `movieId`, `movieTitle`, `movieImage`, `movieRunTime`
  - 모델 `User`의 `ObjectId`만 가지고 있으면 `User`의 모든 정보를 가져올 수 있다.

```js
// Server/models/Favorite.js
const mongoose = require('mongoose')
const Schema = mongoose.Schema

const favoriteSchema = mongoose.Schema(
  {
    userFrom: {
      // 모델 `User`의 `ObjectId`만 가지고 있으면 `User`의 모든 정보를 가져올 수 있다.
      type: Schema.Types.ObjectId,
      ref: 'User',
    },
    movieId: {
      type: String,
    },
    movieTitle: {
      type: String,
    },
    moviePost: {
      type: String,
    },
    movieRunTime: {
      type: String,
    },
  },
  { timestamps: true }
)

const Favorite = mongoose.model('Favorite', favoriteSchema)

module.exports = { Favorite }
```

- **Favorite Button UI 만들기**
- **얼마나 많은 사람이 이 영화를 Favorite리스트에 넣었는지 그 숫자 정보 얻기**
- **내가 이 영화를 이미 Favorite리스트에 넣었는지 아닌지 정보 얻기**
- **데이터를 화면에 보여주기**
  - `프론트` --`req.body`-> `서버` : `body-parser` 이용
  - `프론트` <-`response.data`-- `서버`

```js
// MovieDetail.js
import Favorite from '../MovieDetail/Sections/Favorite'

function MovieDetail(props) {

  return (
      {/* Body */}
      <div style={{ display: 'flex', justifyContent: 'flex-end' }}>
         <Favorite movieInfo={Movie} movieId={movieId} userFrom={localStorage.getItem('userId')} />
      </div>
  )
}

// MovieDetail/Sections/Favorite.js
import React, { useEffect, useState } from 'react'
import axios from 'axios'

function Favorite(props) {

    const movieId = props.movieId
    const userFrom = props.userFrom
    const movieTitle = props.movieInfo.title
    const moviePost = props.movieInfo.backdrop_path
    const movieRunTime = props.movieInfo.runtime

    const [FavoriteNumber, setFavoriteNumber] = useState(0)
    const [Favorited, setFavorited] = useState(false)

    useEffect(() => {

        let variables = {
            movieId,
            userFrom
        }

        axios.post('/api/favorite/favoriteNumber', variables)
            .then(response => {
                if (response.data.success) {
                    console.log(response)
                    console.log(response.data)
                    setFavoriteNumber(response.data.favoriteNumber)
                } else {
                    alert('숫자 정보를 가져오는데 실패 했습니다.')
                }
            })

        axios.post('/api/favorite/favorited', variables)
            .then(response => {
                if (response.data.success) {
                    setFavorited(response.data.favorited)
                } else {
                    alert('정보를 가져오는데 실패 했습니다.')
                }
            })

    }, [])

    return (
        <div>
            <button>{Favorited ? " Not Favorite" : "Add to Favorite "} {FavoriteNumber} </button>
        </div>
    )
}

export default Favorite

// server/index.js
app.use('/api/favorite', require('./routes/favorite'))

// server/routes/favorite.js
const express = require('express')
const router = express.Router()
const { Favorite } = require('../models/Favorite')

router.post('/favoriteNumber', (req, res) => {

    // mongoDB에서 favorite 숫자를 가져오기
    Favorite.find({ "movieId": req.body.movieId })
        .exec((err, info) => {
            if (err) return res.status(400).send(err)
            // 그다음에 프론트에 다시 숫자 정보를 보내주기
            res.status(200).json({ success: true, favoriteNumber: info.length })
        })
})

router.post('/favorited', (req, res) => {

    // 내가 이 영화를 Favorite 리스트에 넣었는지 정보를 DB 에서 가져오기
    Favorite.find({ "movieId": req.body.movieId, "userFrom": req.body.userFrom })
        .exec((err, info) => {
            if (err) return res.status(400).send(err)

            // 그다음에 프론트에 다시 숫자 정보를 보내주기
            let result = false;
            if (info.length !== 0) {
                result = true
            }

            res.status(200).json({ success: true, favorited: result })
        })
})

module.exports = router
```

## 8. Favorite 리스트에 추가 삭제

- **특정 영화를 Favorite 리스트에 넣는 기능 만들기**
- **특정 영화를 Favorite 리스트에서 빼는 기능 만들기**

```js
// MovieDetail/Sections/Favorite.js
import { Button } from 'antd'

function Favorite(props) {

    ...
    const [FavoriteNumber, setFavoriteNumber] = useState(0)
    const [Favorited, setFavorited] = useState(false)

    let variables = {
        movieId: movieId,
        userFrom: userFrom,
        movieTitle: movieTitle,
        moviePost: moviePost,
        movieRunTime: movieRunTime
    }

    useEffect(() => {
        ...
    }, [])

    const onClickFavorite = () => {

        if (Favorited) {
            axios.post('/api/favorite/removeFromFavorite', variables)
                .then(response => {
                    if (response.data.success) {
                        setFavoriteNumber(FavoriteNumber - 1)
                        setFavorited(!Favorited)
                    } else {
                        alert('Favorite 리스트에서 지우는 걸 실패했습니다.')
                    }
                })

        } else {
            axios.post('/api/favorite/addToFavorite', variables)
                .then(response => {
                    if (response.data.success) {
                        setFavoriteNumber(FavoriteNumber + 1)
                        setFavorited(!Favorited)
                    } else {
                        alert('Favorite 리스트에서 추가하는 걸 실패했습니다.')
                    }
                })
        }
    }

    return (
        <div>
            <Button onClick={onClickFavorite}>{Favorited ? " Not Favorite" : "Add to Favorite "} {FavoriteNumber} </Button>
        </div>
    )
}

// server/routes/favorite.js
router.post('/removeFromFavorite', (req, res) => {

    Favorite.findOneAndDelete({ movieId: req.body.movieId, userFrom: req.body.userFrom })
        .exec((err, doc) => {
            if (err) return res.status(400).send(err)
            res.status(200).json({ success: true, doc })
        })
})

router.post('/addToFavorite', (req, res) => {

    const favorite = new Favorite(req.body)

    favorite.save((err, doc) => {
        if (err) return res.status(400).send(err)
        return res.status(200).json({ success: true })
    })
})
```
