---
title: "[ReactJS] 생활코딩 - state, props 개념, event 만들기"
categories:
  - Javascript
  - reactJS
tags:
  - javascript
  - react
---
# 1. **State란 무엇인가**

컴포넌트를 생긴대로만 쓰면 너무 아쉬움.

컴포넌트의 기본적인 동작을 바꾸고 싶을 때 props를 사용함으로서 정보를 조작할 수 있게 됨.

컴포넌트 내부적으로 사용되는 것들이 State임.

즉, Props는 외부에서 사용하는 방식, State는 내부에서 사용되는 시스템

Props와 State가 엄격하게 구분된 것이 좋은 코드이다!

# 2. State 초기화에 필요한 Constructor

기존에 만든 App이라는 클래스는 뭔가 조금 아쉽다.

props는 항상 WEB, world wide web! ~~ 으로 동일한데 매번 바꿔줄 수는 없을까?

그래서 필요한 것이 State를 초기화하는 작업이다.

이 작업은 항상 render 메소드 이전에 들어가는 constructor 메소드에서 실행된다.

코드를 보자.

```jsx
class App extends React.Component{
  constructor(props){
//컴포넌트가 실행될 때 render라는 함수보다 먼저 실행돼서 컴포넌트를 초기화시켜주고 싶은 코드는 컨스트럭터 안에다 적는다.
    super(props);
  }
  render(){
    return(
      <div className="App">
        <Subject title="WEB" sub="world wide web!"></Subject>
        <TOC></TOC>
        <Content title="HTML" desc="HTML is HyperTextMarkup Language."></Content>
      </div>
    );
  }
}
```

**constructor안에서 컴포넌트에 들어가는 props를 초기화하여 값을 제어할 수 있다.**

 

```jsx
class App extends React.Component{
  constructor(props ){
    super(props);
    this.state = {
      Subject:{title:'WEB', sub:'wordl wide web!'};
    }
  }
  render(){
    return(
      <div className="App">
        <Subject title={this.state.subject.title}
        sub={this.state.subject.sub}>
        </Subject>
        <TOC></TOC>
        <Content title="HTML" desc="HTML is HyperTextMarkup Language."></Content>
      </div>
    );
  }
}
```

constructor안에서 state를 초기화시켜준 후 Subject의 props로 넣기 위해 새로운 오브젝트를 만들어줬다.

그리고 적용은 Subject 태그 내 props에 오브젝트를 꺼내는 문법과 동일하게 작성 후 {}안에 넣어준다.

이렇게 간단하게 최초의 state를 만들어 보았다.

# 3. State 활용하여 동적인 컴포넌트 만들어보기

내가 원하는 것은 단순히 스테이트만 만드는 것이 아니라

스테이트를 활용하여 다양한 컴포넌트의 props를 로딩하는 것이다.

현재 앱에서 TOC.js는 조금 아쉽다. 내용빼고 엘리먼트들이 똑같이 li > a 의 형식이라 중복을 제거할 수 있을 것 같다. 이럴 때 다음과 같이 스테이트로 똑똑하게 만들 수 있다.

**① App 클래스 내 컨스트럭터에 contents 오브젝트를 다음과 같이 생성한다.**

**② TOC의 props로 data를 `this.state.contents`로 지정한다.**

**③ TOC.js에서 또 다시 컨스트럭터를 만들어 반복문 처리한다.**

/src/App.js > App

```jsx
class App extends React.Component{
  constructor(props ){
    super(props);
    this.state = {
      subject:{title:'WEB', sub:'wordl wide web!'},
      contents:[
        {id:1, title:"HTML", desc:"HTML is for information"},
        {id:2, title:"CSS", desc:"CSS is for design"},
        {id:3, title:"Javascript", desc:"Javascript is for interaction"}
      ]
    }
  }
  render(){
    return(
      <div className="App">
        <Subject title={this.state.subject.title}
        sub={this.state.subject.sub}>
        </Subject>
        <TOC data={this.state.contents}></TOC>
        <Content title="HTML" desc="HTML is HyperTextMarkup Language."></Content>
      </div>
    );
  }
}
```

/src/components > TOC

```jsx
class TOC extends React.Component {
    render() {
        var lists = [];
        var data = this.props.data;
        var i = 0;
        while(i<data.length){
        lists.push(<li key={data[i].id}><a href={"/content/"+data[i].id}>{data[i].title}</a></li>);
        i++;
        }
        return (
            <nav>
                <ul>
                    {lists}
                </ul>
            </nav>
        );
    }
}
```

이 때 화면에 props를 가지고 있는 반복되는 엘리먼트에는 key라는 고유의 값을 설정해야 한다. 이 것이 jsx의 규칙이다. 설정하지 않으면 콘솔창에 다음과 같은 에러가 뜰 것이다.

`Warning: Each child in a list should have a unique "key" prop.`

간단하게 props로 key를 추가하고 id를 값으로 지정하여 해결한다.

# 4. 이벤트 걸어 동적인 앱 만들기

리액트에서는 props나 state 값이 바뀌면 state 값을 가지고 있는 렌더 메소드가 다시 호출된다.

그러면서 렌더 메소드 하위에 있는 컴포넌트도 다시 호출된다.

즉, 화면이 재렌더링 된다는 뜻이다.

이것을 이용하면 설정값에 따라 데이터가 변하는 동적인 앱을 만들 수 있다.

**① 먼저 this.state에 'mode'라는 변수를 만들어 설정값을 컨트롤하기로 한다. default 값은 'welcome'이다.**

`**mode:'welcome'**`

**② App 컴포넌트를 리턴하기 전에 _title, _desc라는 변수를 만들어 if문을 걸어준다.**

완성된 코드는 다음과 같다.

```jsx
class App extends React.Component{
  constructor(props ){
    super(props);
    this.state = {
      mode:'welcome',
      subject:{title:'WEB', sub:'wordl wide web!'},
      welcome:{title:'Welcome', desc:'Hello, React!!!'},
      contents:[
        {id:1, title:"HTML", desc:"HTML is for information"},
        {id:2, title:"CSS", desc:"CSS is for design"},
        {id:3, title:"Javascript", desc:"Javascript is for interaction"}
      ]
    }
  }
  render(){
    var _title, _desc = null;
    if(this.state.mode === 'welcome'){
      _title = this.state.welcome.title;
      _desc = this.state.welcome.desc;
    } else if(this.state.mode === 'read'){
      _title = this.state.contents[0].title;
      _desc = this.state.contents[0].desc;
    }
    return(
      <div className="App">
        <Subject title={this.state.subject.title}
        sub={this.state.subject.sub}>
        </Subject>
        <TOC data={this.state.contents}></TOC>
        <Content title={_title} desc={_desc}></Content>
      </div>
    );
  }
}
```

이제 로컬을 열어보면 디폴트 값인 "welcome"의 설정으로 "Hello, React!!!"가 보일 것이다.

이제 자바스크립트로 이벤트를 적용하여 홈(WEB)을 클릭하면 mode가 "welcome"으로, 메뉴를 클릭하면  mode가 "read"로 바뀌게만 하면 된다.

먼저 쉽게 가기 위해 Subject 컴포넌트를 오리지날 코드로 대체해본다.

src/App.js > render

```jsx
<div className="App">
        <header>
          <h1><a href="/" onClick={function(){
          })}>{this.state.subject.title}</a></h1>
          {this.state.subject.sub}
        </header>
        <TOC data={this.state.contents}></TOC>
        <Content title={_title} desc={_desc}></Content>
      </div>
```

 

a 태그의 온클릭 이벤트로 함수를 만들어주는데 여기서 자바스크립트 문법과 약간 다르다. 카멜케이스를 사용하여 onclick이 아닌 onClick이라고 써줘야 한다.

a 태그의 function안에서 이제까지 배운대로 이벤트를 걸어보자면 이렇게 만들 것이다.

`this.state.mode = "welcome"`

하지만 이렇게 하면 두 가지의 문제가 생긴다.

**① this를 인식할 수 없다.**

컴포넌트 안에 있는 함수에서 this를 쓰면 컴포넌트 자기 자신을 가리키지 않고 undefined가 되기 때문이다.

이에 대한 해결로 function 바로 뒤에 this를 바인딩해준다.

`onClick={function(){ }.bind(this)}`

그러면 이제 this를 자기 자신으로 인식할 수 있다.

하지만 이 상태로 a태그를 클릭해보면 아무 변화도 일어나지 않는다. 두번째 문제가 생긴다.

**② state 값이 바뀌지 않는다.**

리액트는 스테이트 값이 바뀌었다는 것을 인지하지 못한다.

그래서 해주는 것이 setState이다. 다음과 같이 바꿔보자.

`this.setState({mode: "welcome"})`

제대로된 이벤트를 완성했다!

풀 코드는 다음과 같다.

```jsx
<header>
          <h1><a href="/" onClick={function(e){
            console.log(e);
            e.preventDefault();//a 태그의 기본적인 동작 방법을 막는다. 
						//>> a의 기본 동작인 페이지 리로드가 되지 않음.
            this.setState({
              mode: 'welcome'
            });
          }.bind(this)}>{this.state.subject.title}</a></h1>
          {this.state.subject.sub}
        </header>
```

# 5. bind(this)가 하는 일

렌더 안에서 this는 렌더 메소드가 속해있는 컴포넌트 자체를 가리키는데

렌더 내의 함수 안에 있는 this는 undefined다. 이 this를 묶어주기 위해 bind()라는 내장함수를 사용한다.

예시를 한번 보자.

```jsx
var obj = {name:'BTS'};
function bindTest(){
	console.log(this.name);
}
bindTest();
// >> undefined

var bindTest2 = bindTest.bind(obj);
bindTest2();
// >> BTS
```

객체를 파라미터로 넣어주지 않고 bind로 함수를 bindTest2라는 복제함수를 만들고 그 인자로 obj를 넣어줬더니 bindTest 함수 내에서 this를 인식할 수 있게 되었다.

# 6. setState를 쓰는 이유

constructor(생성자 함수) 내에서는 this.state를 써도 된다.

하지만 그 이후에 실행되는 함수에서는 this.state를 쓸 수 없다. 왜일까?

동적인 state 값을 바꿀 때는 절대로 this.state를 쓰면 안된다.

그 이유는 리액트 입장에서는 값을 몰래 바꾼 것이 되기 때문이다.

>> 항상 state 값이 바뀌면 setState로 해준다.

&& constructor에서는 this.state로 편하게 쓰자

# 7. 컴포넌트 이벤트 만들기

이제 바깥쪽으로 옮겼던 코드는 비활성화를 시키고 이전의 Subject 컴포넌트를 살려보자.

/src/App.js > render

```jsx
<Subject title={this.state.subject.title}
        sub={this.state.subject.sub}
        onChangePage={function(e){
          this.setState({
            mode: 'welcome'
          })
        }.bind(this)} //onChangePage라는 이벤트를 만들어 a 태그 클릭 시 호출하도록 만든다.
        >
```

onChangePage라는 props를 만들어 Subject.js에서 이벤트로 사용할 준비를 한다. this를 인식할 수 있도록 `.bind(this)`는 필수다.

그렇다면 Subject.js는 어떻게 할까?

/src/components/Subject.js > Subject > render

```jsx
<header>
          <h1><a href="/" onClick={function(e){
            e.preventDefault();
            this.props.onChangePage();
          }.bind(this)}>{this.props.title}</a></h1>
          {this.props.sub}
        </header>
```

먼저 `event.preventDefault()`로 페이지 리로드를 방지해주고

`this.props.onChangePage();`로 App.js에서 만들었던 이벤트를 실행시킨다.

이렇게 하고 실행하면 또 다시 this를 인식할 수 없다는 에러가 나온다.

여기에서도 반드시 `.bind(this)`를 해줘야 한다.

여기까지 잘 따라왔다!!

그러면 다음 포스팅에서는 남은 이벤트를 마저 해보자.