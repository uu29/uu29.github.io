---
title: "[JS]이벤트리스너 load vs DOMContentLoaded"
categories:
  - Javascript
tags:
  - 이벤트리스너
  - DOM tree
  - javascript
---

# Thinking

자바스크립트는 프로그래밍 언어다. 프로그램은 순서대로 실행된다. 로직을 짤 때도 가장 첫번째로 생각해야할 것이 순서다. 디버깅을 할 때도 마찬가지다. 아무리 봐도 문법에 맞게 짠 것 같은데 대체 안되는 원인이 뭐지, 생각하면 순서를 먼저 살펴야 한다.

# Debugging

몇 일에 걸쳐 나를 삽질하게 만든 것도 순서였다. 챗봇으로 깃플 라이브러리를 이용하고 있었는데 특정 페이지에서만 보이고 다른 페이지에서는 숨기게 하고 싶었다. 오픈소스 같은 경우 혼자 하다 막힐 때는 무조건 문의하면 된다. 내 어이없는 질문에도 항상 친절하게 답변해준 깃플(+부트페이에 감사를 전한다.)은 이번에도 window.Gitple('hide')라는 코드를 쓰면 된다고 가르쳐줬다. 깃플이 실행되고 있는 상태에서 깃플을 숨기고 싶은 페이지에 해당 코드를 써보았지만 되지 않았다. 한참 고민하다 깃플을 아예 인식하지 못하는 것은 아닐까 하고 콘솔창에 찍어봤더니 역시나 undefined가 떴다. undefined가 뜨는 이유는 두 가지. 정의하지 않았거나 나중에 정의했거나. 분명 정의했는데? 하는 순간 순서를 봐야 한다. 깃플 실행코드를 뜯어보니 attachevent load도 있는 듯 하고 onload도 보이는 듯 하다.

"아! 돔(DOM)이 먼저 로드되고 깃플이 실행되는거면 DOMContentLoaded를 써야겠다!"

라고 생각하고

```jsx
function gitpleHide(){
	window.Gitple('hide');
}
document.addEventListener('DOMConetentLoaded', gitpleHide, false);
```

대충 이렇게 짜보았다.

그러나 아무리 해봐도 페이지를 로드하면 깃플이 깜빡하면서 생겼다.

도대체 뭐가 문제일까... 하고 깃플 실행 코드를 다시 한번 봤더니 깃플은 돔이 아닌 모든 html이 렌더링되고 난 후 맨 마지막에 생기는 듯 하다.

혹시.. 

설마..

하는 마음으로 윈도우가 로드되고 난 후 코드를 짜기로 했다.

```jsx
window.addEventListenr('load', gitpleHide, false);
```

역시나 성공!

깃플이 로드된 시점에서는 깃플 객체를 인지할 수 있게 되어 hide명령도 가능해졌다.

사실 따지고 보면 제이쿼리에서 쓰이는 $(document).ready()와 window.onload, DOMContentLoaded는 엄연히 다르지만 급할 때 쓰기에는 비슷해서 별 생각을 않고 썼었다.

이 참에 제대로 배우자! 하는 생각에 정리했다.

# 뭐가 더 빨리 실행될까?window.addEventListener('load') vs document.addEventListener('DOMContentLoaded')

정답부터 말하면 DOMContentLoaded가 더 빠르다.

DOMContentLoaded는 HTML + DOM tree + javascript가 모두 실행되었을 때 동작한다.

load는 HTML + DOM tree + javascript + css + images 등 모든 파일을 다 읽고 나서 동작한다.

깃플이 실행되지 않았던 이유는 깃플 자체가 window.onload 시점에 생성되는데 그 전에 DOM이 생성되자 마자 깃플 hide 명령어를 내렸기 때문이다.

# DOM이 뭐야 대체?

DOM(Document Object Model, 문서 객체 모델). 사실 객체가 뭔지도 제대로 모르는 나인데... 돔은 오죽하겠나. 찾아보니 대충 이러하다.

웹 문서는 본래 텍스트로 이루어져 있다. 이것을 브라우저에 보여주려면 브라우저가 이해할 수 있는 구조로 메모리에 올려야 하는데 이렇게 메모리에 적재하는 것이 DOM이다. 텍스트를 각각의 속성별로 나누어 객체로 만들고 부자 관계를 트리 구조로 구성한 것이다. DOM은 자바스크립트를 통해 동적으로 변경할 수 있으며 렌더링에 반영된다고 한다.

![JS%20load%20vs%20DOMContentLoaded%20999ee1bbde074961b94473fcc6d7fbef/Untitled.png](JS%20load%20vs%20DOMContentLoaded%20999ee1bbde074961b94473fcc6d7fbef/Untitled.png)

돔트리는 네 종류의 노드로 구성된다.

# DOM 로딩 순서

문서(Document) 노드 > 요소(Element) 노드 > 어트리뷰트(Attribute) 노드 > 텍스트(Text) 노드

[Node](https://www.notion.so/aa65c6efb1894867b4c6091fbbd0a984)

그래서 자바스크립트로 엘리먼트를 하나 만드려면 createElement로 Element Node를 생성하고 createTextNode로 텍스트를 생성한 후 생성한 엘리먼트 노드에에 텍스트 노드를 끼워넣는(appendChild)것이었다. 

# DOM 조작하기

[DOM | PoiemaWeb](https://poiemaweb.com/js-dom)

document.querySelector/document.getElementbyId, innerHTML/textContent의 차이와 왜 후자를 쓰는 것이 더 안전하고 좋은지 자세하게 설명되어 있다.

# 정리하자면

[함수 작동 순서](https://www.notion.so/94a2ed25cbc9421589f7104f12fa36bb)

그렇다면 제이쿼리의 $(document).ready(function(){})은 어디와 같을까? 바로 DOMContentLoaded다. 일반적으로 많이 쓰이는 시점인데 그 이유는 load보다 더 빨리 실행되어서다. 하지만 애초에 자바스크립트를 <body>태그 시작점에 쓰는 것이 아닌 </body> 직전에(바디 태그가 끝나기 직전)에 쓴다면 이러한 코드들을 쓸 일이 거의 없겠다.

[문서의 로드시점 - onload, DOMContentLoaded](https://webdir.tistory.com/515)