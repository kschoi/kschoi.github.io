---
title: "react-router 이해하기"
date: 2020-05-04 08:26:28 -0400
categories: react
---

## Companion Libraries

- react-router : 코어 라이브러리 (직접 설치하지 않음)
- react-router-dom : dom-based apps 네비게이션
- react-router-native : react-native apps 네비게이션
- react-router-redux : Redux와 React Router 바인딩

## Component

- Router
- BrowserRouter: TLD(Top Level Domation, .com, .net)나 포트 뒤에 오는 path로 사용함
- HashRouter: /# 뒤에 오는 것을 path로 사용함
- MemoryRouter: 네비게이션을 트래킹하기 위해 url을 변경하지 않음

## history object

BrowserRouter는 history 객체를 자동으로 생성하고 컴포넌트 단위로 history 객체를 컨트롤하기 때문에 커스텀 작업이 어렵다. 컴포넌트 바깥에서 history 객체를 컨트롤하기 위해서는(ex. action creator) BrowserRouter 대신 Router를 사용하고, BrowserHistory를 별도 생성하여 관리해야 한다.

### history.js

```jsx
import { createBrowserHistory } from 'history';

export default createBrowserHistory();
```

### App.js

```jsx
...
import history from '../history';

const App = () => {
  return (
    <div className="container">
      <Router **history={history}**>
        <div>
          <Header></Header>
					<Switch>
	          <Route path="/" exact component={MainComponent} />
						...
					</Switch>
        </div>
      </Router>
    </div>
  );
};
```

### actions/index.js

```jsx
import history from '../history';

export const createStream = formValues => async (dispatch, getState) => {
  const { userId } = getState().auth;
  const response = await streams.post('/streams', { ...formValues, userId });

  dispatch({
    type: CREATE_STREAM,
    payload: response.data,
  });

  // 액션 크리에이터 내부에서 HTTP POST가 끝난 후 루트로 이동하기에 적합한 위치
  **history.push('/');**
};
```


<style type="text/css">
@media (min-width: 64em) {
  .archive pre,
	.archive p {
		font-size: 0.84em;
	}
}
@media (min-width: 80em) {
  .archive pre,
	.archive p {
		font-size: 0.72em;
	}
}
</style>