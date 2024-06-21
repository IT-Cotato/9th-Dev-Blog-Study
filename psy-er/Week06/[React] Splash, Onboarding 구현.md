# [React] Splash, Onboarding 구현

## **[React] Splash, Onboarding 구현**

![https://blog.kakaocdn.net/dn/boLO4q/btsHJUY6CR2/K7Ds9n1PKR46LmV89vb6Q1/img.png](https://blog.kakaocdn.net/dn/boLO4q/btsHJUY6CR2/K7Ds9n1PKR46LmV89vb6Q1/img.png)

나는 분명 백엔드 직무를 희망하지만, 프런트엔드도 함께 공부하는 겸 어쩌다 보니 프로젝트에서 풀스택으로 개발 중이다.

리액트를 공부한 지 1달(?) 정도 지났는데 데이터 전달 방식도 생소하고, 컴포넌트 배치도 어렵지만, 백엔드에서 보낸 api를 프런트엔드에서 전달받아 어떻게 활용하는지 직접 구현해 보니까 나름 웹 프로그래밍 공부가 되는 것 같다.

프로젝트가 멋져 보이기 위해 실제 앱을 구동하는 것처럼 스플래시와 온보딩도 추가했다. 하지만 리액트 왕초보인 나는 이미 정해진 프레임에서 스플래시를 내가 생각한 방식대로 구현할 수 없었다.

**[문제상황]**

```
import React from "react";
import "./App.css";
import { Route, Routes, Navigate } from "react-router-dom";
import DiaryDetail from "./feat_diary/DiaryDetail";
import DiaryCalendar from "./feat_diary/DiaryCalendar";
import AddDiary from "./feat_diary/AddDiary";
import QuestionHome from "./feat_question/QuestionHome";
import Navbar from "./Navbar";
import ProfileList from "./feat_profile/ProfileList";
import ProfileDetail from "./feat_profile/ProfileDetail";
import Login from "./feat_login/Login";
import SignUp from "./feat_login/SignUp";
import Splash from "./feat_login/Splash";

function App() {
  return (
    <div className="app-wrapper">
    <div className="app-container">

    <Routes>
      {/* 일기 */}
      <Route path="/diary" element={<DiaryCalendar />} />
      <Route path="/diary/detail" element={<DiaryDetail />} />
      <Route path="/diary/add" element={<AddDiary />} />

      {/* 프로필(워드클라우드) */}
      <Route path="/profile" element={<ProfileList />} />
      <Route path="/profile/detail" element={<ProfileDetail />} />

      {/* 1일 1질문 */}
      <Route path="/question" element={<QuestionHome />} />

      {/* 로그인 */}
      <Route path="/login" element={<Login />} />

      {/* 회원가입 */}
      <Route path="/signup" element={<SignUp />} />

      {/* 스플래시 */}
      <Route path="/" element={<Navigate to="/splash" />} />
      <Route path="/splash" element={<Splash />} />

    </Routes>
    </div>

    <Navbar />
    </div>
  );
}

export default App;
```

처음에 App.js에 스플래시를 넣었더니 Navbar도 가려지지 않을뿐더러,

콘텐츠의 App.css가 모두 함께 적용되어 원하는 스플래시 화면이 나오지 못하였다.

**[해결방법]**

내가 생각한 해결방법으로는 App.js가 아닌 다른 컴포넌트를 새로 추가해서 index.js에 추가하고,

css를 분리시켜 순서대로 랜더링 하는 방식이었다. 따라서 Splash를 위한 Preview 컴포넌트를 생성하였고, 이를 index.js에 추가하였다.

**[시행착오 1 : 컴포넌트 추가]**

![https://blog.kakaocdn.net/dn/bChxG2/btsHKAyX1px/7klFnymC9MwzCY8aOo4CM1/img.png](https://blog.kakaocdn.net/dn/bChxG2/btsHKAyX1px/7klFnymC9MwzCY8aOo4CM1/img.png)

index.js에 Preview 컴포넌트를 추가했는데,

스플래시가 뜨지 않았고 스플레시 창이 뜬 이후 리다이렉트 되었던 로그인 창 역시 뜨지 않았다.

```
import React from 'react';
import ReactDOM from 'react-dom/client';
import './index.css';
import App from './App';
import Preview from './Preview';
import { BrowserRouter } from 'react-router-dom';

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(
  <React.StrictMode>
    <BrowserRouter>
      <Preview/>
      <App />
    </BrowserRouter>
  </React.StrictMode>
);
```

그래서 다른 방법을 시도해 보았다.

**[시행착오 2 : Preview를 최상위 컴포넌트로 설정]**

![https://blog.kakaocdn.net/dn/waCJJ/btsHMfNEB2R/4e0LKNHwg0h9027uQwfY0k/img.png](https://blog.kakaocdn.net/dn/waCJJ/btsHMfNEB2R/4e0LKNHwg0h9027uQwfY0k/img.png)

Preview 컴포넌트를 최상위 컴포넌트로 설정하였더니 App.js에 설정해 두었던 navbar를

스플래시 화면이 렌더링 되는 동안 안 보이는 데 성공하였다.

하지만 navbar의 css가 무너졌고, navbar 하이퍼링크도 제대로 동작하지 않았다.

```
import React from 'react';
import ReactDOM from 'react-dom/client';
import './index.css';
import Preview from './Preview';
import { BrowserRouter } from 'react-router-dom';

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(
  <React.StrictMode>
    <BrowserRouter>
      <Preview />
    </BrowserRouter>
  </React.StrictMode>
);
```

```
import React from "react";
import { Route, Routes, Navigate } from "react-router-dom";
import Splash from "./feat_login/Splash";
import App from "./App";// 추가: App 컴포넌트를 임포트합니다.import "./Preview.css";

function Preview() {
  return (
    <div className="app-wrapper">
      <div className="app-container">
        <Routes>
          {/* 스플래시 */}
          <Route path="/" element={<Navigate to="/splash" />} />
          <Route path="/splash" element={<Splash />} />

          {/* App 컴포넌트의 라우트 추가 */}
          <Route path="/app" element={<App />} />
        </Routes>
      </div>
    </div>
  );
}
export default Preview;
```

무엇이 문제일까 일주일 동안 고민한 끝에 드디어 해결책을 찾았다!

스플래시, 로그인, 회원가입 화면은 navbar가 보이지 않게 하고, 나머지 부분은 navbar가 보이도록 하기 위해서 App에 navbar를 넣지 말고 새로운 컴포넌트를 생성했다.

```
import React from "react";
import "./App.css";
import Navbar from "./Navbar";

const PageFirst = ({children}) => {
  return (
    <div className="app-wrapper">
      <div className="app-container">
        {children}
      </div>
      <Navbar />
    </div>
  )
}

export default PageFirst;
```

새로운 PageFirst 컴포넌트에서 받아온 값과 나브바를 함께 반환하는 방식으로 코드를 작성하면 나브바를 보이게 하고 싶은 컴포넌트의 반환 값에만 PageFirst로 감싸 나브바를 보이게 할 수 있다.

- PageFirst로 감싸 반환하여 navbar가 보이도록 하는 컴포넌트

```
  return (
    <PageFirst>
      <div>
      <Header title="일일문답" type="home" />
        <div className="todayInput">어떤 놀이가 제일 좋아?</div>
        <AddQuestion postQuestion={postQuestion}/>
        <div className="search">
          <Select
            value={sortOrder}
            onChange={handleSortChange}
            options={options}
            styles={customStyles}
            className="selectBox"
          />
          <SearchQuestion searchQuestion={searchQuestion}/> {/* 검색부분 */}
          <MSearchQuestion
            isOpen={modalOpen} onClose={handleCloseModal} /> {/* 모달부분 */}
        </div>
        <div className="questionList">
            {questionList}
        </div>
      </div>
    </PageFirst>
  );
}

export default QuestionHome;
```

- PageFirst로 감싸지 않아 navbar가 보이지 않도록 하는 컴포넌트

```
return (
        <Container component="main" maxWidth="xs" style={{marginTop: "8%"}} >
            <form noValidate onSubmit={handleSubmit}>
                <Grid container spacing={2}>
                    <Grid item xs={12}>
                        <Typography component="h1" variant="h5">
                            <div> 반갑습니다 ^_^ </div>
                            <div> 부모클래스입니다 </div>
                        </Typography>
                    </Grid>
                    <Grid item xs={12}>
                        <TextField
                            autoComplete="fname"
                            name="email"
                            variant="outlined"
                            required
                            fullWidth
                            id="email"
                            label="아이디"
                            autoFocus
                        />
                    </Grid>
                    <Grid item xs={12}>
                        <TextField
                            variant="outlined"
                            required
                            fullWidth
                            name = "password"
                            label="패스워드"
                            autoFocus
                            id="password"
                            autoComplete="current-password"
                        />
                    </Grid>
                    <Grid item xs={12}>
                        <TextField
                            variant="outlined"
                            required
                            fullWidth
                            name = "passwordverify"
                            label="패스워드 확인"
                            autoFocus
                            id="passwordverify"
                            autoComplete="current-password"
                        />
                    </Grid>
                    <Grid item xs={12}>
                        <TextField
                            variant="outlined"
                            required
                            fullWidth
                            name = "nickname"
                            label="닉네임"
                            autoFocus
                            id="nickname"
                            autoComplete="current-password"
                        />
                    </Grid>
                    <Grid item xs={12}>
                        <Button
                            type="submit"
                            fullWidth
                            variant="contained"
                            color="primary">
                            회원가입
                        </Button>
                    </Grid>
                </Grid>
                <Grid container justify="flex-end">
                    <Grid item>
                        <Link to="/login" variant="body2">
                            이미 계정이 있습니까? 로그인하세요.
                        </Link>
                    </Grid>
                </Grid>
            </form>
        </Container>
    );
```

![https://blog.kakaocdn.net/dn/bONjIs/btsH7ZLaawk/sGT19KX2TpjFbzUjJI9KIk/img.png](https://blog.kakaocdn.net/dn/bONjIs/btsH7ZLaawk/sGT19KX2TpjFbzUjJI9KIk/img.png)

원하는 대로 결과가 잘 나온 것을 확인할 수 있다.

온보딩도 같은 방식으로 추가하면 될 것같다.