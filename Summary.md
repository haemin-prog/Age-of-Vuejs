Summary.md
참고 URL : <https://joshua1988.github.io/vue-camp/vue/instance.html>

## Vue는 무엇인가?

MVVM 패턴의 뷰모델(View Model) 레이어에 해당하는 화면(View)단 라이브러리

> [View] DOM -> [ViewModel] DOM Listener -> [Model] Plain JavaScript Objects  
> [View] DOM <- [ViewModel] Data Bindings <- [Model] Plain JavaScript Objects

### Object.defineProperty()

객체의 동작을 재 정의하는 API

```js
Object.defineProperty ( 대상 객체, 객체의 속성, { 
    // 정의할 내용 
})
```

### Immediately-Invoked Function Expression (즉시 실행 코드)

함수정의->함수실행의 과정을 거치지 않고, 함수를 괄호()로 래핑해 실행

```js
(function(){
    // 실행할 내용
}());
```

#### 함수 호이스팅 (Function Hositing)

- js는 함수 선언의 위치와 상관없이 어디서든 호출 가능
- 코드를 실행하기 전에 var 과 function을 스코프 제일 위로 옮겨 VO(variable object)에 저장
- 함수가 너무 많으면 VO에 너무 많은 코드가 저장돼 응답속도가 ↓
  cf. IIFE를 사용하면 변수의 유효범위를 정의할 수 있음

## Vue 인스턴스

인스턴스를 생성하면 Root Component가 생성

#### Console Code

- vm
  > Vue {\_uid: 0, \_isVue: true, $options: {…}, \_renderProxy: Proxy, \_self: Vue, …}
- vm.\_data
  > {**ob**: Observer}
        message: "hello world"
        __ob__: Observer {value: {…}, dep: Dep, vmCount: 1}
        get message: ƒ reactiveGetter()
        set message: ƒ reactiveSetter(newVal)
        __proto__: Object
- vm.\_data.message
  > hello world

#### 왜 View를 생성자 함수의 형태로 찍어내냐!

- 매번 정의하지 않고, 정의된 함수를 가져다 쓸 수 있기 때문에 = 재사용성
- 인스턴스에서 사용할수 있는 속성, API를 끌어다 쓰면 된다.

## Vue 컴포넌트

화면의 영역을 구분해 개발하는 것

- 재사용성, 개발 생산성 ↑

### 등록 방식

- 전역 컴포넌트 / 지역 컴포넌트(객체 생성하면서 안에 components로 선언)
- 차이 : 특정 인스턴스 하단에 어떤 컴포넌트가 등록되어있는지 확인 가능하기 때문에 가독성 좋음
- (Usually) 전역 컴포넌트는 플러그인이나 라이브러리만 사용
- 지역 컴포넌트는, 같은 이름의 인스턴스를 여러개
  생성하더라도 반복해서 생성해야 함 (인스턴스는 같은 이름으로 여러 개 생성 가능)

## 컴포넌트 통신 방식

뷰 컴포넌트는 각각 고유한 데이터 유효 범위를 가짐

    상위 컴포넌트 -> props(데이터) 전달 -> 하위 컴포넌트 -> 이벤트 발생 -> 상위 컴포넌트(반복)

- 컴포넌트 간 데이터를 주고 받기 위해선 규칙을 따라야 함
- 상위에서 하위로는 데이터를 내려줌 (프롭스 속성)
- 하위에서 상위로는 이벤트를 올려줌 (이벤트 발생)

### 컴포넌트 통신 규칙이 필요한 이유

컴포넌트의 변화에 따라 다른 컴포넌트가 유기적으로 변화할 때, 그로 인한 버그를 추적하기 어려움

- 데이터의 흐름을 추적할 수 있다.
- 데이터는 항상 내려오고, 이벤트는 항상 올라간다.

```html
<div id="app">
  //<app-header
    v-bind:프롭스
    속성이름="상위컴포넌트의 데이터 이름"
  ></app-header>
  <app-header v-bind:propsdata="message"></app-header>
</div>
var appHeader = { template : '
<h1>header</h1>
', props: ['propsdata'] }
```

## props 속성의 특징

Root의 message가 바뀌면 app-header의 props도 바뀐다.
console을 이용해 myData.message를 바꿨을 때, 화면에 적용되어 나타남

```html
// data binding form : {{ }}
    <div id="app">
        <app-header v-bind:propsdata ="message"></app-header>
    </div>
    <script>
        var appHeader = {
            //template : '<h1>header</h1>',
            template: '<h1>{{ propsdata }}</h1>',
            props: ['propsdata']
        }
        // HM Vue.js 플러그인 없이 확인하기 위해
        var myData = {
            message: 'hi'
        }
        new Vue({
            el: '#app',
            components: {
                'app-header' : appHeader
            },
            data: myData
        })
    </script>
```

## event emit

이벤트가 발생 했을 때, 하위 컴포넌트에서 상위 컴포넌트로 이벤트를 올리는 것

- 문법
  > 이벤트 속성 함수 이름: function(){
        this.$emit('이벤트 이름');
  }
  > <app-header v-on:하위 컴포넌트에서 발생한 이벤트 이름 = "상위 컴포넌트의 메서드 이름"></app-header>

```html
<body>
    <div id="app">
        <!-- <app-header v-on:하위 컴포넌트에서 발생한 이벤트 이름 = "상위컴포넌트의 메서드 이름"></app-header> -->
        <app-header v-on:pass = "logText"></app-header>
    </div>
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
        var appHeader = {
            //template: '<button>Click Me</button>'
            // 버튼을 클릭했을 때 이벤트를 실행시키는 방법
            template: '<button v-on:click="passEvent">Click Me</button>',
            methods:{
                passEvent: function(){
                    this.$emit('pass');
                    // this.$emit은 Vue에서 제공하는 API
                    // this.$emit을 이용해 pass라는 이벤트가 발생
                    // tag에서 pass라는 이벤트가 올라오는 것을 잡아줘야 함
                    // event Log는 Vue 개발자 플러그인에서 event tab에서 확인 가능
                }
            }
        }
        new Vue({
            el: '#app',
            components: {
                'app-header': appHeader
            },
            methods: {
                logText: function(){
                    console.log('This is Log');
                }
            }
        })
    </script>
</body>
```

## 같은 컴포넌트 레벨 간의 통신 방법

같은 컴포넌트 레벨 간 직접 통신은 가능하지 X

- 하위 -> 상위 컴포넌트로 event 전달,
- 다시 상위 -> 하위로 props 전달하는 과정을 통해 통신

```html
<body>
    <div id="app">
        <app-header v-bind:propsdata="num"></app-header>
        <!-- ' v-bind:프롭스 속성 이름 = "상위컴포넌트의 데이터 이름" '-->
        <!-- 을 이용해 상위 컴포넌트의 데이터(num)를 하위 컴포넌트의 프롭스(propsdata)로 전달 -->
        <app-content v-on:pass="deliverNum"></app-content>
        <!-- ' v-on:하위 이벤트 이름="상위 컴포넌트 메소드 이름" ' -->
        <!-- 을 이용해 하위 컴포넌트에서 발생하는 이벤트(pass)를 받아 상위컴포넌트의 method(deliverNum) 을 실행 -->
    </div>
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
        var appHeader = {
            template: '<div>{{propsdata}}</div>', // {{}}를 이용해 props data를 header에 표시
            props: ['propsdata']
        }
        var appContent = {
            //template: '<div>content</div>'
            template: '<div>content <button v-on:click = "passNum">pass</button></div>',
            methods: {
                passNum : function(){
                    this.$emit('pass', 10);
                    // 이벤트 이름 pass, 첫번째 인자를 10으로 넘겨줌
                }
            }
        }
        // new Vue가 Root
        new Vue({
            el: '#app',
            components: {
                'app-header' : appHeader,
                'app-content': appContent
            },
            data: {
                num: 0
            },
            methods: {
                deliverNum: function(value){
                    this.num = value;
                    console.log(this.num);
                }
            }
        })
    </script>
</body>
```

## 뷰 라우터 (Vue Router)

뷰 라우터는 싱글 페이지 어플리케이션을 구현할때 사용하는 라이브러리
페이지 이동과 관련된 기능을 구현 할 수 있음

### CDN 방식

Vue Router Script : `<script src="https://unpkg.com/vue-router/dist/vue-router.js"></script>`

### 뷰 라우터 등록

뷰 라우터를 등록하게 되면, 뷰 인스턴스에 $route 가 잡히게 됨

```js
// 라우터 인스턴스 생성
var router = new VueRouter({
    // 라우터 옵션
})
// 인스턴스에 라우터 인스턴스를 등록
new Vue({
    router: router
})
```

### 뷰 라우터 옵션

- routes : 라우팅 할 URL과 컴포넌트 값 지정
- mode : URL의 해쉬 값 제거 속성

### router view

routes 속성에 따라 컴포넌트가 화면에 표시 됨, 이때 표시 되는 지점>  
`<router-view></router-view>`

### router link

화면에서 링크를 클릭해 페이지를 이동 할 수 있도록 하는  
`<router-link>` > `<router-link to="이동할 URL"></router-link>`

## Axios(액시오스)

뷰에서 권고하는 HTTP 통신 라이브러리  
Promise(js의 비동기 처리 패턴)기반의 HTTP 통신 라이브러리
상대적으로 다른 HTTP 통신 라이브러리에 비해 문서화가 잘 되어있음  
<https://github.com/axios/axios>

### Axios script

> `<script src="https://cdn.jsdelivr.net/npm/axios/dist/axios.min.js"></script>`

### function 내에서 get, then-catch 사용

```js
getData.function(){
    // url : 10개의 user 정보가 담긴 sample data
    axios.get('https://jsonplaceholder.typicode.com/users/')
    .then(function(response){
        // reponse 시 수행
        // 이 지점에서의 this는 Vue를 가르키지 않음
    })
    .then(function(error){
        // error 시 수행
    });
}
```

- cf. vue-resource  
  -> 현재 공식 Library는 X
- cf. Ajax  
  비 동기적인 웹 애플리케이션의 제작  
  대표적으로 jQuery.ajax()가 있음
- cf. 자바스크립트의 비동기 처리 패턴
  1. callback
  2. promise
  3. promise + generator
  4. async await
  5. 참고 url
  - js 비동기 처리와 콜백 함수
    <https://joshua1988.github.io/web-development/javascript/javascript-asynchronous-operation/>
  - js promise 이해하기
    <https://joshua1988.github.io/web-development/javascript/promise-for-beginners/>
  - js async와 awiat
    <https://joshua1988.github.io/web-development/javascript/js-async-await/>

### 웹 서비스에서 클라이언트와 서버와의 HTTP 통신 구조

> 브라우저(Client) --> **HTTP 요청(Request)** 송신 --> 서버(Server) -(백엔드 로직)-> DB  
> 브라우저(Client) <-- **HTTP 응답(Response)** 수신 <-- 서버(Server) <-(백엔드 로직)- DB

### 네트워크 패널

크롬 개발자 도구 -> Network

- General > Request URL: https://jsonplaceholder.typicode.com/users/  
  Request Method: GET  
  Status Code: 200 OK
  Remote Address: 104.21.9.189:443  
  Referrer Policy: strict-origin-when-cross-origin
  - Request URL : HTTP 요청 URL
  - Request Method : GET - 정보를 요청
  - Status : 200 - 200번대는 대부분 성공을 의미
- Request Header : 응답에 따른 부가적인 정보
- Response Header : 서버에서 보내는 부가적인 정보

cf. HTTP 프로토콜 참고 url <https://joshua1988.github.io/web-development/http-part1/>

## 뷰의 템플릿 문법

### 데이터 바인딩

{{   }} (Mustache Tag, 콧수염 괄호)를 이용해 Vue 인스턴스에서 정의한 속성을 화면에 표시하는 방법

```html
<div>{{ message }}</div>
new Vue ({
    data:{
        message: 'message'
    }
})
```

#### 디렉티브

화면 조작에 자주 사용되는 방식들을 모아 뷰 디렉티브(v-) 형태로 제공  
`<div> hello<span v-if="show">Vuejs</span></div>`

- v-bind
- v-if : if/else
- v-show

### 모르는 문법이 나왔을 때, 공식 문서를 보고 해결하는 방법
 [Vue.js Org](https://kr.vuejs.org/v2/guide/index.html)에서 검색
 
### methods 속성과 v-on 디렉티브를 이용한 키보드, 마우스 이벤트 처리 방법
클릭 이벤트 : ```v-on:click="메서드 이름"```  
키 입력 이벤트 : ```v-on:keyup="메서드 이름"```  
특정 키 입력 이벤트 : ```v-on:keyup.특정키="메서드 이름"```  

## 뷰의 템플릿 문법 : 실전
템플릿 내에 표현식이 많아지면 코드가 길어지고 유지보수가 어려움. 템플릿 문법을 Vue 속성을 이용하면 나이스하게 표현할 수 있음

### watch 속성
데이터의 변화에 따라 로직을 실행하는 속성

```
watch:{
    데이터 이름: function(){
        // 실행할 내용
    }
}
```

### computed 속성
```
computed:{
    메소드 이름: function(){
        // ...
        return ~ // 실행할 내용
    }
}
```

### watch vs computed 
[Computed Properties and Watchers-Eng](https://vuejs.org/v2/guide/computed.html#ad)
[Computed Properties and Watchers-Kor](https://kr.vuejs.org/v2/guide/computed.html)

watch는 데이터를 지정하고, 데이터가 바뀔때 실행되는 **명령형 프로그래밍** 방식,  
computed는 계산해야하는 데이터를 정의하는 **선언형 프로그래밍** 방식.