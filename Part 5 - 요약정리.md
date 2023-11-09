# Props

**동일한 데이터**가 **동일한 페이지**에서 여러번 출력되는 경우,
이를 **공통된 부모 컴포넌트**에서 한번에 관리하는데 사용
<br> **부모 컴포넌트**에서 **자식 컴포넌트**로 데이터를 전달하는데 사용되는 속성
<br><br>
=> 부모는 자식에게 **데이터를 전달**하며(_**pass props**_),
자식은 자신에게 **일어난 일을 부모에게 알림**(_**emit event**_)

---

## 특징

- 모든 props는 자식 속성과 부모 속성 사이에 **하향식 단방향 바인딩**을 형성!
- 부모 속성이 업데이트 되면, 자식으로 흐르지만 그 반대는 불가
  = 자식 컴포넌트 내부에서 props 변경 불가능!
  = 부모 컴포넌트가 업데이트 될 때마다 자식 컴포넌트의 모든 props가 최신 값으로 업데이트 됨

  > why?
  > 하위 컴포넌트가 부모를 업데이트 해서 데이터 흐름이 복잡해지는 것 방지

- _**Props Name Casing**_
  - **`camelCase`** <br>
    : 자식 컴포넌트에서 선언할 때<br>
    : 템플릿에서 참조해서 사용할 때<br>
  - **`kebab-case`**<br>
    : 부모 컴포넌트에서 자식 컴포넌트로 전달할 때
    <br>**(camelCase도 가능하지만, HTML 속성 표기법과 동일하게 kebab-case로 표기할 것을 권장)**
- _**Static Props & Dynamic Props**_
  : 기본적으로 정적 props를 사용
  : but, **자식에서 손자 컴포넌트로 props 해주는 것처럼 원하면 v-bind를 사용하여 동적으로 할당된 props를 사용할 수 있다**

```html
<!-- Parent.vue -->
<script>
  import { ref } from "vue";
  const name = ref("Alice");
</script>

<template>
  <ParentChild
    my-msg="message"
    :dynamic-props="name"
  />
  <!-- dynamic-props : props 이름 / name : 동적으로 보낸 props 값-->
</template>
```

---

# prop 설정법

**1.부모 컴포넌트에선 prop를 보내주고, 2.자식 컴포넌트에서 사용하기 위해선 선언(`defineProp`)해주어야 함**

### 1. 부모 컴포넌트에서 prop 보내주기

```html
<!--Parent.vue-->
<template>
  <div>
    <ParentChild my-msg="message" />
    <!-- my-msg: prop 이름 / "message": 값 -->
  </div>
</template>
```

---

### 2. 자식 컴포넌트에서 prop 선언하기

1. **문자열 배열을 사용한 선언**

```html
<!--ParentChild.vue-->
<script setup>
  defineProps(["myMsg"]);
</script>
```

2. **객체를 사용한 선언 (권장)**

```html
<!-- ParentChild.vue -->
<script setup>
  defineProps({
    // 객체의 키 값(myMsg) : 부모로부터 받은 props의 이름
    // 객체의 value 값 : 데이터 타입에 해당하는 생성자 함수(String, Number, Object..)
    myMsg: String,
  });
</script>
```

> _**객체 선언 문법을 권장하는 이유**_
> : 타입 지정을 통해

- 가독성 향상
- 개발자 실수 미리 방지 (콘솔 경고)
- **유효성 검사** 가능

---

# Props 사용법

### 1. 템플릿에서 반응형 변수와 동일한 방식으로 사용

```html
<!-- ParentChile.vue -->
<div>
  <p>{{myMsg}}</p>
</div>
```

---

### 2. Props를 객체로 반환하므로 필요한 경우 JS에서 접근도 가능

```html
<script setup>
  const props = defineProps({ myMsg: String });
  console.log(props); // {myMsg:'message'}
  console.log(props.myMsg); // 'message'
</script>
```

---

---

# $Emit()

: 자식 컴포넌트가 이벤트를 발생시켜 부모 컴포넌트로 데이터를 전달하는 역할의 메서드

: `$` = vue 인스턴스나 컴포넌트 내에서 제공되는 전역 속성이나 메서드를 식별하기 위한 접두어

- 구조
  `$emit(event,...args)`
  : event = 이벤트 이름
  : args = 추가 인자

---

## emit 과정

1. 자식 컴포넌트에서 해당 이벤트가 발생할 때 직접 **사용자 정의 이벤트를 발신**

```html
<button @click="$emit('someEvent')">클릭</button>
```

2. 부모 컴포넌트에서 **`v-on`을 사용하여 수신**

```html
<ParentComp @some-event="someCallback" />
<!-- ParentComp라는 자식 컴포넌트로부터 someEvent가 호출되면 
부모 컴포넌트에서 선언한 someCallback 함수를 실행하겠다 -->
```

---

## event 주고 받는 법

### 1. `'$emit'` 사용

- ParentChild에서 someEvent라는 이름의 사용자 정의 이벤트를 발신

```html
<!-- ParentChild.vue -->
<button @click="$emit('someEvent')">클릭</button>
```

- ParentChild의 부모 Parent는 `v-on`을 사용하여 발신된 이벤트를 수신
- 수신 후 처리할 로직 및 콜백함수 호출

```html
<!-- Parent.vue -->
<template>
  <ParentChild @some-event="someCallback" />
</template>

<script>
  const someCallback = function () {
    // ParentChild에서 emit을 보냈을 때 실행할 로직 작성
  };
</script>
```

### 2. `'defineEmits()'` 사용

- `defineEmits()`을 사용하여 명시적으로 발신할 이벤트를 선언할 수 있음
- `<script>`에서 `$emit`메서드를 접근할 수 없기 때문에, `defineEmits()`는 `$emit`대신 사용할 수 있는 동등한 함수를 반환

```html
<!-- ParentChild -->
<script>
  // emit이라는 변수에 emit 사용자 정의 이벤트를 할당
  const emit = defineEmits(["someEvent", "myFocus"]);
  // 버튼 클릭 시 사용할 이벤트 함수 생성
  const buttonClick = function () {
    // buttonClick 함수가 실행되면 'someEvent'라는 emit을 실행해라
    emit("someEvent");
    // emit시 추가인자도 같이 전달해서 보낼 수 있다
    emit("someEvent", 1, 2, 3); //추가 인자 = 1,2,3
  };
</script>

<template>
  <button @click="buttonClick">클릭</button>
</template>
```

- Parent에서 수신하기

```html
<!-- Parent.vue -->
<template>
	<ParentChild @some-event="someCallback" @emit-args="getNumbers">
</template>

<script>
	const getNumbers = function(...args){
  		console.log(args)
  		// [1,2,3]
  		console.log(`ParentChild가 전달한 추가 인자 ${args}를 수신했어요`)
  		// ParentChild가 전달한 추가 인자 1,2,3를 수신했어요
}
</script>
```

---

### props와 마찬가지로 name casing이 발생한다

- **`camelCase`**
  : 자식 컴포넌트에서 선언 및 발신 시

- **`kebab-case`**
  : 부모 컴포넌트에서 수신 시

---
