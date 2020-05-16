---
title: "[ReactJS]기초 개념 및 환경 세팅"
categories:
  - Javascript
  - reactJS
tags:
  - javascript
  - react
---

# JSX 특징

1. 컴포넌트를 렌더링할 때에는 꼭 <⁯div></div> 같은 하나의 container 엘리먼트 안에 포함시켜야 한다.
2. JSX안에 JS를 표현할 때는 {}으로 감싸면 됨.
3. if Else문 사용 불가. 대신 삼항연산자 문법으로. 1==1 ? 'True':'False'
4. JSX내에서 스타일 설정시, string 형식 X, 키:값 형태의 객체로 사용. 키는 무조건 카멜케이스
ex)
let style = {
color:'#fff',
backgroundColor:'#000'
}
5. 주석은 {*/ /*}, 주석은 컨테이너 엘리먼트 안에 작성되어야 함
6. 프롭타입스 지정은 필수는 아니지만 유지보수를 위해 사용하자
7. state는 유동적인 데이터, constructor를 통하여 초기값 설정이 필수(this.state = {}).
값을 수정할 경우에는 this.setState를 사용하여 안전하게 수정해야 함. 렌더링 이후에는 this.state 절대 금지

# 리액트 환경 설정

- 깃, node js, mogo DB

`npm install -g webpack webpack-dev-server`

# 프로젝스 생성

- 폴더 생성 후 노드 시작

`mkdir react-fundamentals`

`cd react-fundamentals`

노드 프로젝트를 시작하는 명령어: `npm init`

npm init을 하게 되면 터미널에서 먼저 프로젝트 이름, 버전, 설명 등의 설정을 할 수 있다. 안할거면 enter를 누르고 원하는 값을 설정하면 package.json 파일이 생기는데 방금 입력한 설정값들이 저장되어 있다.

```html
{
  "name": "react-tutorial",
  "version": "1.0.0",
  "description": "react-tutorial",
  "main": "index.js",
  "scripts": {
    "test": "test"
  },
  "author": "",
  "license": "ISC"
}
```

# Dependency 및 Plugin 설치

- React 설치:  `npm intall --save react react-dom`

`--save` : packages.json에 정보를 저장해줌. 다음과 같이 추가 됨.

```html
"dependencies": {
    "react": "^16.13.1",
    "react-dom": "^16.13.1"
  }
```

- 개발에 필요한 모듈 설치(개발 의존 모듈)

`npm install --save-dev babel-core babel-loader babel-preset-es2015 babel-preset-react`

babel: ES6 문법을 프로젝트에서 사용할 수 있게 해주는 문법

`npm install --save-dev react-hot-loader webpack webpack-dev-server`

- 세팅이 끝난 이후 packages.json에 추가된 것들

```html
"devDependencies": {
    "babel-core": "^6.26.3",
    "babel-loader": "^8.1.0",
    "babel-preset-es2015": "^6.24.1",
    "babel-preset-react": "^6.24.1",
    "react-hot-loader": "^4.12.21",
    "webpack": "^4.43.0",
    "webpack-dev-server": "^3.11.0"
  }
```

- 프로젝트 폴더에 노드 모듈이 설치되어 있음

# webpack 설정하기

- webpack.config.js 생성 `touch webpack.config.js`
- 다음과 같이 기본 설정을 해준다.

```jsx
var webpack = require('webpack'); //웹팩 불러오기(require) = import명령과 동일

module.exports = {
    entry: './src/index.js', //번들링해주고 싶은 파일들을 선언함

    output: { //번들링 후 생성될 파일에 대한 정보를 명시함
        path: __dirname + '/public/',
        filename: 'bundle.js'
    },

    devServer: { //개발서버 설정
        hot: true, //수정할 때마다 리로딩을 함
        inline: true,
        host: '0.0.0.0',
        port: 4000,
        contentBase: __dirname + '/public/' //index 파일의 위치
    },

    module: {
            rules: [
                {
                    test: /\.js$/,
                    loader: 'babel-loader',
                    exclude: /node_modules/,
                    query: {
                        cacheDirectory: true,
                        presets: ['es2015', 'react'] //less sass 로더를 사용할 수도 있음
                    }
                }
            ]
        },

    plugins: [
        new webpack.HotModuleReplacementPlugin() //수정된 파일만 리로딩하는 기능
    ],

    resolve: {
        extensions: ['.js', '.jsx']
    }
};
```

[https://haviyj.tistory.com/17](https://haviyj.tistory.com/17)