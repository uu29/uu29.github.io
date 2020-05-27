---
title: "[ReactJS] 생활코딩 - Create"
categories:
  - Javascript
  - reactJS
tags:
  - javascript
  - react
---
생활코딩 수업 절정에 다다랐다.

최종 앱의 생김새는 이러하다.

create, update, delete를 누를 때 마다 mode가 바뀌고

그에 따라 데이터를 추가, 수정, 삭제할 수 있는 앱이다.

우선 모드부터 변경해보자.

# 1. mode 변경하기

  [things to do]

- TOC와 Content 컴포넌트 사이에 새로운 컴포넌트인 Control를 만든다.
- Control.js 파일에 필요한 엘리먼트를 만든다. (ul>li>a)
- Control의 각 엘리먼트를 클릭했을 때 mode가 각각 create, update, delete로 바뀌는 이벤트를 만든다.

코드는 다음과 같다.

/src/App.js > render > Control

```jsx
import Control from './components/Control';

```(생략)```

<Control onChangeMode={function(_mode){
    this.setState({mode:_mode})
}.bind(this)}></Control>
```

/src/components/Control.js

```jsx
import React from 'react';

class Control extends React.Component{
    render(){
      return (
        <ul>
          <li><a href="/create" onClick={function(e){
            e.preventDefault();
            this.props.onChangeMode('create');
          }.bind(this)}>create</a></li>
          <li><a href="/update" onClick={function(e){
            e.preventDefault();
            this.props.onChangeMode('update');
          }.bind(this)}>update</a></li>
          <li><input type="button" value="delete" onClick={function(e){
            e.preventDefault();
            this.props.onChangeMode('delete');
          }.bind(this)}></input></li>
        </ul>
      );
    }
  }

export default Control;
```

① 먼저 onChangeMode라는 메소드를 Control 컴포넌트의 props로 만들어준다.

똑같이 `.bind(this)`를 통해 this를 컴포넌트 자기 자신으로 받아주고 `setState`를 통해 mode를 바꿔준다.

이 때, 파라미터로 `_mode`라는 것을 받는데 나머지는 Control 컴포넌트 내에서 수행한다.

② a태그와 input태그에 onClick function으로 이벤트를 파라미터로 넘기는 익명함수를 만들어준다.

이전과 마찬가지로 `e.preventDefault()`를 해주어 a태그 고유의 속성인 페이지 리로드를 막아준다.

③ 익명함수 내에 Control 컴포넌트의 props였던 onChangeMode함수를 사용한다.

`this.props.onChangeMode('create')` : 이 때 컴포넌트에서 _mode 인자로 쓰여질 값을 잘 넣어주어야 한다.

여기까지 하면 엘리먼트들을 누를 때 마다 mode가 해당하는 내용으로 바뀌는 것을 확인할 수 있다.

# 2. mode에 따라서 하단 컨텐트 변경하기

src/App.js > App > render

```jsx
<div className="App">
    <Subject title={this.state.subject.title} sub={this.state.subject.sub} onChangePage={function (e) {
        this.setState({mode:'welcome'})
    }.bind(this)}
    ></Subject>
    <TOC
        data={this.state.contents}
        onClickList={function (id) {
        this.setState({
          mode: 'read',
          selected_content_id: Number(id),
        })
    }.bind(this)}></TOC>
    <Control onChangeMode={function(_mode){
        this.setState({mode:_mode})
    }.bind(this)}></Control>
    {_article}
</div>
```

`<Content>`가 있던 자리를 `{_article}`이라는 변수로 대체한 후 

렌더 메소드 시작단에서 먼저 `{_article}`을 정의해준다.

그리고 모드에 따라서 아티클을 다르게 정의해주면 된다!

src/App.js > App > render

```jsx
var _title,
    _desc,
    _article = null;
if (this.state.mode === 'welcome') {
    _title = this.state.welcome.title;
    _desc = this.state.welcome.desc;
    _article = <ReadContent title={_title} desc={_desc}></ReadContent>;
    
} else if (this.state.mode === 'read') {
  var i = 0;
  while(i<this.state.contents.length){
    var data = this.state.contents[i];
    if(data.id === this.state.selected_content_id){
      _title = data.title;
      _desc = data.desc;
      break;
    }
    i++;
  }
  _article = <ReadContent title={_title} desc={_desc}></ReadContent>;
}
```

(이 때, 좀 더 명확한 구분을 위해 읽기용의 콘텐트는 `<ReadContent>`로, 쓰기용의 콘텐트는 `<CreateContent>`로 다시 네이밍한다.)

같은 방식으로 `<CreateContent>`도 추가한다.

그 전에 임포트는 필수!

src/App.js > App > render

```jsx
import CreateContent from './components/CreateContent';

```생략```

else if (this.state.mode === 'create') {
    _article = <CreateContent></CreateContent>;
}
```

# 3. Create Form 완성하기

 우리가 만들 아주 간단한 Create 폼은 이렇게 생겼다.

친숙한 html로 input, textarea로 만들어준 뒤, `onSubmit`함수를 입혀서 작동되는지 테스트를 해본다. 

/src/components/CreateContent > CreateContent > render

```jsx
<article>
    <h2>Create</h2>
    <form action="/create_process" method="post"
    onSubmit={function(e){
        e.preventDefault();//폼 태그 고유의 페이지 전환 기능 블록
        alert('submit!');
    }.bind(this)}
    >
        <p>
            <input type="text" name="title" placeholder="title"></input>
        </p>
        <p>
            <textarea type="text" name="desc" placeholder="description"></textarea>
        </p>
        <p>
            <input type="submit"></input>
        </p>
    </form>
</article>
```

이렇게 하면 폼 제출을 눌렀을 때 페이지가 넘어가지 않으면서 정상적으로 alert 창이 뜬다.

이제 다음 포스팅에서는 데이터 추가를 해보자!