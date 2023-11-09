### Template Syntax

1. Text Interpolation
2. Raw HTML
3. Attribute Bindings
4. Javascript Expressions

---

### Text Interpolation

가장 기본적인 형태 : {{msg}}

### Raw HTML

콧수염 구문은 데이터를 일반 텍스트로 해석함
=> HTML 을 출력하려면 v-html = "rawHtml" 사용해야함!

### Attribute Bindings

콧수염 구문은 HTML 속성 내에서 사용할 수 없기 때문에 v-bind를 사용
▪ HTML의 id 속성 값을 vue의 dynamicId 속성과 동기화 되도록 함
▪ 바인딩 값이 null 이나 undefind 인 경우 렌더링 요소가 제거됨

### JS expressions

Vue는 모든 데이터 바인딩 내에서 JavaScript 표현식의 모든 기능을 지원
▪ Vue 템플릿에서 JavaScript 표현식을 사용할 수 있는 위치
• 콧수염 구문 내부
• 모든 directive의 속성 값 (v-로 시작하는 특수 속성)
주의사항
: 각 바인딩에는 하나의 단일 표현식만 포함가능

---

### Directive 특징
: 단일 표현식(v-for, v-on 제외)
DOM 에 반응적으로 업데이트 적용

Modifiers
▪ . (dot) 로 표시되는 특수 접미사로, directive가 특별한 방식으로 바인딩 되어야 함을 나타냄
▪ 예를 들어 .prevent는 발생한 이벤트에서 event.preventDefault()를 호출하도록
v-on에 지시하는 modifier

v-bind 사용처
1. attribute bindings
2. class and style bindings

v-bind:href -> :href 사용 가넝!

### Event Handling

v-on:event = "handler" -> @event="handler" 사용 가넝

: Method Handlers 방식으로 대부분 사용
<button @click="update">

: 사용자 지정 인자 전달방식
<button @click="greeting('hello')">

  event Modifiers (stop, prevent, self)

<form @submit.prevent="onSubmit">

- Key Modifiers

<input @keyup.enter="onSubmit">

## Form Input (양방향 바인드)
### v-bind와 v-on을 함께 사용
<input :value="inputText1" @input="onInput">

### v-model 사용
사용자 입력 데이터와 반응형 변수를 실시간 동기화
text 뿐만 아니라 checkbox , radio, select 다 가능

예시 확인






