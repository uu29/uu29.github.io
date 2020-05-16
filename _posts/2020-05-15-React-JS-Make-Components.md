---
title: "[ReactJS]시작하기, 컴포넌트 만들기"
categories:
  - Javascript
  - reactJS
tags:
  - javascript
  - react
---
# index.html 만들기

- 루트 경로에 public 폴더를 만들고 그 안에 index.html을 생성한다.

### /public/index.html

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>React App</title>
</head>
<body>
    <div id="root"></div>
    <script src="bundle.js"></script>
</body>
</html>
```

# HTML, JS 작성하기

### /src/components/App.js

```jsx
import React from 'react': //ES6 문법.
// var React = require('react'); 예전 문법

class App extends React.Component {
    render(){
        return (
            <h1>Hello React Skeleton</h1>
        );
    }
}

export default App; //클래스를 다른 곳에서도 불러와서 사용할 수 있도록. ES6 문법
// module.export = App; 예전 문법
```

### /src/index.js

```jsx
import React from 'react';
import ReactDOM from 'react-dom';
import App from './components/App';

const rootElement = document.getElementById('root');
ReactDOM.render(<App/>, rootElement);

if (module.hot){
    module.hot.accept();
} // new webpack.HotModuleReplacementPlugin()으로 수정된 부분만 리로드하고 싶을 때 같이 쓰는 함수
```

- package.json에 "scripts" 값에 데브서버 실행 명령어를 추가한다.

```jsx
"scripts": {
    "dev-server": "webpack-dev-server" //데브 서버 실행 명령어 설정
  }
```

- 터미널에서 명령어 실행

`npm run dev-server`