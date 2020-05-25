---
title: "[ReactJS]생활코딩 - 컴포넌트 이벤트 만들기"
categories:
  - Javascript
  - reactJS
tags:
  - javascript
  - react
---
이제까지 만든 앱의 생김새다.

이제 원하는 건 HTML, CSS, Javascript의 리스트를 클릭했을 때 각각의 내용이 다르게 나오는 것.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/19e5ea8e-de10-4146-96dd-d3b9ae61eb48/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/19e5ea8e-de10-4146-96dd-d3b9ae61eb48/Untitled.png)

우선 배운대로 이벤트를 적용하여 미리 만들어놓은 mode의 변수를 "read"로 바꾸는 작업을 해보자.

/src/App.js > App > render

```jsx
<TOC
    data={this.state.contents}
    onClickList={function (e) {
    this.setState({mode: 'read'})
}.bind(this)}></TOC>
```

/src/components/TOC.js > TOC > render > while

```jsx
<a
    href={"/content/" + data[i].id}
    onClick={function (e) {
    e.preventDefault();
    this
        .props
        .onClickList();
}.bind(this)}>{data[i].title}</a>
```

이제 쉽게 완성하였다. 하지만 App 내 렌더 메소드 직후에 선언한 _title, _desc 변수를 조작하는 것이 문제다.

```jsx
if (this.state.mode === 'welcome') {
            _title = this.state.welcome.title;
            _desc = this.state.welcome.desc;
        } else if (this.state.mode === 'read') {
            _title = this.state.contents[0].title;
            _desc = this.state.contents[0].desc;
        }
```

지금 if문 안에서 `this.state.contents[0].{값}`으로 고정되어 있는 상태다.

이 값들을 어떻게 내가 클릭한 값에 해당하는 내용으로 바뀌게 만들 수 있을까?

방법은 여러가지다.

**① TOC 각 컴포넌트에 dataset을 id로 설정하는 방법**

/src/App.js > App > render

```jsx
onClickList={function (id) {
	this.setState({
  mode: 'read',
  selected_content_id: Number(id),
})
```

클릭할 때 마다 `selected_content_id`가 바뀌게 해주고 id를 a태그의 또 다른 속성으로 지정한다.

이 때 유용하게 쓰이는 것이 `data-`가 붙은 사용자 설정 attribute다.

data-id, data-key, data-isMobile 등으로 설정하면 나중에 어트리뷰트로 불러올 때 element.id, element.key, element.isMobile 등으로 불러올 수 있다.

적용하면 다음과 같다.

/src/components/TOC.js > TOC > render

```jsx
<a
    href={"/content/" + data[i].id}
    data-id={data[i].id}
    onClick={function (e) {
    e.preventDefault();
    this.props.onClickList(e.target.dataset.id);
}.bind(this)}>{data[i].title}</a>
```

**② 바인딩한 파라미터에 id를 추가로 넘기는 경우**

/src/components/TOC.js > TOC > render

```jsx
<li key={data[i].id}>
    <a
        href={"/content/" + data[i].id}
        onClick={function (id, e) {
        e.preventDefault();
        this.props.onClickList(id);
    }.bind(this, data[i].id)}>{data[i].title}</a>
</li>
```

이 때 주의할 점은 onClick의 익명함수의 첫번째 인자였던 e(event) 앞에 id가 추가된다는 것이다.

이렇게 이벤트를 이용하여 데이터를 불러오는(read) 것은 해냈다.

이제 남은 create, update, delete를 해보자.