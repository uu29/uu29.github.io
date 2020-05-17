---
title: "[JS]강의록-객체지향 프로그래밍(생활코딩)"
categories:
  - Javascript
  - reactJS
tags:
  - javascript
  - 객체지향언어
---
# 객체란?

서로 연관된 변수와 함수를 그룹핑하고 이름을 붙인 것

# method

객체에 소속되어있는 함수를 메소드라고 칭함

# constructor

new를 붙여서 객체를 생성하는 생성자 함수constructor를 사용하면 좋은 점: 객체를 원하는대로 찍어내고 재생성할 수 있다.

constructor가 하는 일 2가지

1. 객체를 만든다
2. 객체의 초기 상태(prototype)를 세팅한다

```jsx
function Person(name, first, second, third){
    this.name=name,
    this.first=first,
    this.second=second,
    this.third=third,
    this.sum=function(){
        return this.first+this.second+this.third;
    }
};

console.log('Person()', Person()); //아무것도 리턴하지 않으므로 undefined
console.log('new Person()', new Person()); //new를 붙이면 객체를 생성함. 객체를 생성하는 생성자 > 생성자 함수. 객체를 찍어내는 공장 = constructor function

var kim = new Person('kim', 10, 20, 30);
var lee = new Person('lee', 10, 10, 10);
console.log("kim.sum()", kim.sum());
console.log("lee.sum()", lee.sum());
```

# prototype

prototype: 원형. 자바스크립트를 지탱하는 기반

— 프로토타입을 사용하지 않은 함수(함수 내 메소드를 사용한 경우)

```jsx
function Person(name, first, second, third){
    this.name=name,
    this.first=first,
    this.second=second,
    this.third=third,
    this.sum=function(){ //함수 내 메소드
        return this.first+this.second+this.third;
    }
};
var kim = new Person('kim', 10, 20, 30);
var lee = new Person('lee', 10, 10, 10);
```

프로토타입을 사용하지 않고 생성자 함수 안에서 메소드를 사용하면 생성자 함수를 정의할 때 마다 메소드를 실행하는 비효율이 발생한다.

— 프로토타입을 사용한 함수(함수 바깥에 프로토타입으로 메소드를 선언)

```jsx
function Person(name, first, second, third){
    this.name=name;
    this.first=first;
    this.second=second;
    this.third=third;
};
Person.prototype.sum = function(){ //Person 함수의 원형으로서 sum 메소드 선언
    return 'prototype : '+(this.first+this.second+this.third);
};
console.log("kim.sum()", kim.sum());
console.log("lee.sum()", lee.sum());
```

— 특정 변수에만 다른 메소드를 적용하고 싶을 때는

```jsx
kim.sum = function(){
    return 'this : '+(this.first+this.second+this.third);
};
console.log("kim.sum()", kim.sum());
console.log("lee.sum()", lee.sum());
```

프로토타입을 사용하여 메소드를 선언하면 생성자 함수를 사용할 때마다 메소드를 호출하지 않아 성능이 개선되고 특정 변수에 다른 메소드를 적용하고 싶으면 해당 변수 같은 이름의 메소드를 선언해주면 된다. (자기 자신에 정의된 객체에서 메소드를 먼저 때문에)

# class

constructor를 사용하기 위해 도입된 ECMA script 6의 새로운 문법

(tip: 원래 이해하기 어려운 것이므로 처음부터 너무 알려고 하지 말 것)

### class에서의 constructor

```jsx
class Person{ //객체를 만드는 동작
    constructor(){ //객체가 만들어지기 직전에 실행되도록 약속되어 있는 함수 => 'constructor'라고 함(매번 실행되는 메소드)
        console.log('constructor');
    }
}

var kim = new Person();
console.log('kim', kim); //컨스트럭터를 지정하였으니 console.log('constructor')를 찍지 않아도 터미널 창에 찍힐 것이다.
```

### class에서 prototype 선언하기

```jsx
class Person{
    constructor(name, first, second){
        this.name = name;
        this.first = first;
        this.second = second;
    }
}

var kim = new Person('kim', 10, 20);
console.log('kim', kim);
```

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/808a5829-919e-4b4b-92d4-93bfcdc3760a/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/808a5829-919e-4b4b-92d4-93bfcdc3760a/Untitled.png)

결과

### class에서 method 선언하기

'function'이라고 칭할 필요 없이 바로 메소드명을 쓴다.

```jsx
class Person{
    constructor(name, first, second){
        this.name = name;
        this.first = first;
        this.second = second;
    }
    sum(){ //같은 클래스에 속하는 모든 객체가 공유하는 메소드
        return 'prototype : '+(this.first+this.second);
    }
}

var kim = new Person('kim', 10, 20);
kim.sum = function(){
    return 'this : '+(this.first+this.second);
}
var lee = new Person('lee', 10, 10);
console.log('kim.sum()', kim.sum()); //kim이라는 객체가 sum이라는 객체를 가지고 있는지 우선 확인해보고
console.log('lee.sum()', lee.sum()); //없다면 클래스 내의 'sum'으로 정의된 메소드를 찾는다
```

# class에서의 상속

두 개 이상의 클래스 내의 메소드가 반복될 때 먼저 정의된 클래스에서 상속을 받아 그대로 사용할 수 있음

상속을 사용하면:

- 중복되는 코드 제거
- 유지보수의 편의성 증대

— 상속하기 전

```jsx
class Person{
    constructor(name, first, second){
        this.name = name;
        this.first = first;
        this.second = second;
    }
    sum(){
        return 'prototype : '+(this.first+this.second);
    }
}

class PersonPlus{
    constructor(name, first, second){
        this.name = name;
        this.first = first;
        this.second = second;
    }
    sum(){
        return 'prototype : '+(this.first+this.second);
    } //중복되는 부분
    avg(){
        return (this.first+this.second)/2;
    }
}
```

— 상속 후

`class {{클래스명}} extends {{상속받을 클래스명}}`

```jsx
class Person{
    constructor(name, first, second){
        this.name = name;
        this.first = first;
        this.second = second;
    }
    sum(){
        return 'prototype : '+(this.first+this.second);
    }
}

class PersonPlus extends Person{
    avg(){
        return (this.first+this.second)/2;
    }
}

var kim = new PersonPlus('kim', 10, 20); //Person을 상속받은 PersonPlus를 생성해줌
console.log("kim.sum()", kim.sum()); //Person 클래스에만 있는 메소드지만 실행됨
console.log("kim.avg()", kim.avg()); //Person을 상속받아 그대로 실행됨
```

# super

'부모 클래스에게 일을 시키고 부모 클래스가 하지 못한 일은 내가 한다'

상속의 단점 보완: 부모 클래스에서 일부를 변경하고 싶을 땐?

```jsx
class Person{
    constructor(name, first, second){
        this.name = name;
        this.first = first;
        this.second = second;
    }
    sum(){
        return (this.first+this.second);
    }
}

class PersonPlus extends Person{
    constructor(name, first, second, third){ //부모가 가지고 있는 컨스트럭터에 (나만 쓰고 싶은)변수를 추가하고 싶을 때
        super(name, first, second) //부모 클래스의 생성자 호출
        this.third = third;
    }
    sum(){
        return super.sum()+this.third; //부모가 가지고 있는 sum 클래스를 호출한다
    }
    avg(){
        return (this.first+this.second+this.third)/3;
    }
}

var kim = new PersonPlus('kim', 10, 20, 30);
console.log("kim.sum()", kim.sum());
console.log("kim.avg()", kim.avg());
```

### super의 두 가지 용법

- super()

부모 클래스에서 정의된 생성자 함수를 호출함. ()안에 호출할 프로토타입을 넣어준다.

- super

부모 클래스의 메소드를 통째로 호출한다. 오브젝트에서 키 꺼내듯

# 자바스크립트에서 객체지향은 어떻게 다른가?

— 클래스: 객체를 만드는 공장

— 객체: 클래스에서 만들어진 인스턴스(생산물)

- 주류 객체지향 언어에서는 상속을 어떻게 다루나?

서브 클래스가 수퍼 클래스의 기능을 물려받기 위해 수퍼 클래스의 자식이 되고 객체를 생성한다.

but 자바스크립트는 훨씬 더 자유롭고 복잡한데

주류 객체언어에서는 클래스가 상속을 받아 객체를 생성하지만 자바스크립트에서는 서브 객체가 수퍼 객체로부터 직접 상속을 받을 수 있다.

수퍼 객체에서 상속을 받다가 다른 객체로부터 상속을 받고 싶을 때는 프로토타입 링크(prototype link)를 사용한다.

이 때, 프로토타입 링크가 가리키는 오브젝트를 프로토타입 오브젝트(prototype object)라고 한다.

### 수작업으로 객체 상속해보기

1. 두 개의 관련 없는 객체를 만든다. 'superObj', 'subObj'는 아직까지 남남인 상태다.

```jsx
var superObj = {superVal:'super'}
var subObj = {subVal:'sub'}
```

2. 프로토타입 링크를 걸어 'subObj'의 원형을 만들어준다.

```jsx
subObj.__proto__ = superObj;
```

이 때, `__proto__`는 객체를 다른 객체의 자식으로 만들기 위해 링크를 걸어주는 문법이다.

3. 콘솔창에 다음과 같이 찍어보자.

```jsx
console.log('subObj.subVal => ', subObj.subVal);
console.log('subObj.superVal => ', subObj.superVal);
```

4. 분명 subObj에 superVal를 선언하지 않았음에도 콘솔창에 다음과 같이 찍힌다.

but __proto__는 자바스크립트 표준 문법이 아니다(오피셜이 아니라는 뜻). 하지만 거의 모든 브라우저에서 지원하고 있긴 한데... 문법도 이상하고 네이밍이 확 와닿지 않기 때문에 대신 사용할 만한 것이 나왔다.

# Object.create(): __proto__ 의 대체제

```jsx
var subObj = Object.create(superObj);//superObj를 부모로 하는 새로운 객체를 만듦
```

`var {{오브젝트명}} = Object.create({{상속받을 객체명}});`

표준화된 방법이자 권장되는 방법이다.

```jsx
var kim = {
    name: 'kim',
    first: 10,
    second: 20,
    sum: function(){
        return this.first+this.second
    }
}

//__proto__를 사용한 방법
var lee = {
    name: 'lee',
    first: 10,
    second: 10,
    avg: function(){
        return (this.first+this.second)/2;
    }
}
lee.__proto__ = kim;

//Object.create()를 사용한 방법
var lee = Object.create(kim);
lee.name = 'lee';
lee.first = 10;
lee.second = 10;
lee.avg = function(){
    return (this.first+this.second)/2;
}

console.log('kim.sum() : ', kim.sum());
console.log('lee.sum() : ', lee.sum());
console.log('lee.avg() : ', lee.avg());
```