# 14. State Management

## 🌟 State Management(상태 관리)

- Vue 컴포넌트는 이미 반응형 상태를 관리하고 있음 (상태 === 데이터)

### 📌 컴포넌트 구조의 단순화

- 상태 (State) : 앱 구동에 필요한 기본 데이터
- 뷰 (View) :  상태를 선언적으로 매핑하여 시각화
- 기능 (Action) : 뷰에서 `사용자 입력`에 대해 `반응적으로 상태를 변경`할 수 있게 정의된 동작
- “단방향 데이터 흐름”의 간단한 표현

### 📌 상태 관리의 단순성이 무너지는 시점

 → 여러 컴포넌트가 상태를 공유할 때

1. `여러 뷰가 동일한 상태에 종속`되는 경우
    - 공유 상태를 공통 조상 컴포넌트로 끌어올린 다음 props로 전달하는 것
    - 하지만 계층 구조가 깊어질 경우 비효율적, 관리가 어려워짐
2. `서로 다른 뷰`의 `기능이 동일한 상태를 변경`시켜야 하는 경우
    - 발신(emit)된 이벤트를 통해 상태의 여러 복사본을 변경 및 동기화 하는 것
    - 마찬가지로 관리의 패턴이 깨지기 쉽고 유지 관리할 수 없는 코드가 됨

### 📌 해결책

- `각 컴포넌트의 공유 상태를 추출하여, 전역에서 참조할 수 있는 저장소에서 관리`
- 컴포넌트 트리는 `하나의 큰 "뷰"` 가 되고,
    
    컴포넌트는 트리 계층 구조에 관계없이 상태에 접근하거나 기능을 사용할 수 있음
    
- Vue의 공식 상태 관리 라이브러리 === `“Pinia"`

## 🌟 Pinia

- State management library
- 설치 → Vite 프로젝트 빌드 시 Pinia 라이브러리 추가
- Vue의 스토어 라이브러리로 컴포넌트/페이지 간에 상태를 공유가능

### 📌 구성요소

- Pinia는 `store 라는 저장소`를 가짐
- store는 `state = ref()`, `getters=conputed()`, `actions=function()`으로 이루어져 있음

### 1. store

- 중앙 저장소, 모든 컴포넌트가 공유하는 상태 및 기능 등이 작성됨

```jsx
 stores / counter.js
커다란 이 상태 자체가 store -> 중앙 저장소는 여러 개 생성 가능 

// counter라는 이름으로 객체들을 넣어서 반환해서 사용
export const useCounterStore = defineStore('counter', () => {
  // state(상태)
  const count = ref(0)
  // getter
  const doubleCount = computed(() => count.value * 2)
  // action(기능)
  function increment() {
    count.value++
  }

  // return해야 사용 가능
  return { count, doubleCount, increment }
})
```

### 2. state

- 반응형 데이터 (상태), `ref() === state`
- `store 인스턴스로 state에 접근하여 직접 읽고 쓸 수 있음`
- 만약 store에 state를 정의하지 않았다면 컴포넌트에서 새로 추가할 수 없음

```jsx
// App.vue

<template>
  <div>
    <h2>StoreTest</h2>
    <p> state : {{ store.count }}</p>
  </div>
</template>

<script setup>
import { useCounterStore } from '@/stores/counter.js'

const store = useCounterStore();

console.log(store.count);
</script>

<style scoped>

</style>
```

### 3. getters

- 계산된 값, `computed() === getters`
- store의 모든 getters를 state처럼 직접 접근할 수 있음

```jsx
<template>
  <div>
    <h2>StoreTest</h2>
    <p> state : {{  store.count }}</p>
    <p> getters : {{  store.doubleCount }}</p>
  </div>
</template>

<script setup>
import { useCounterStore } from '@/stores/counter.js'

const store = useCounterStore();

console.log(store.count);
</script>

<style scoped>

</style>
```

### 4. actions

- 메서드, `function() === actions`
- store의 모든 actions를 직접 접근 및 호출 할 수 있음
- getters와 달리 state 조작, 비동기, API 호출이나 다른 로직을 진행할 수 있음

```jsx
<template>
  <div>
    <h2>StoreTest</h2>
    <p> state : {{  store.count }}</p>
    <p> getters : {{  store.doubleCount }}</p>
    <button @click="store.increment()">증가</button>
    <button @click="increment">증가2</button>
  </div>
</template>

<script setup>
import { useCounterStore } from '@/stores/counter.js'`

const store = useCounterStore();
console.log(store.count);

const increment = function () {
  store.increment()
}
</script>

<style scoped>

</style>
```

### 5. plugin

- 어플리케이션의 상태 관리에 필요한 추가 기능을 제공하거나 확장하는 도구나 모듈
- 어플리케이션의 상태 관리를 더욱 간편하고 유연하게 만들어주며
    
    패키지 매니저로 설치 이후 별도 설정을 통해 추가됨
    
- 중앙저장소 → localStorage 자동 동기화 해주는 플러그인 예제로 사용

## 🌟 Pinia 실습

### 📌 todo 실습 설명

```jsx
📁 src
		ㄴ 📁 components
				ㄴ 📃 TodoForm.vue
				ㄴ 📃 TodoList.vue
				ㄴ 📃 TodoListItem.vue
		ㄴ 📁 stores
				ㄴ 📃 counter.js
				ㄴ 📃 todo.js
		ㄴ 📃 App.vue
		ㄴ 📃 main.js
```

```jsx
// 📃 TodoForm.vue

<template>
    <div>
        <h4>TodoForm</h4>
        <!-- 문자열 좌우에서 공백을 제거하는 함수가 trim() -->
        <input type="text" v-model.trim="todoText" @keyup.enter="createTodo">
        <button @click="createTodo">등록</button>
    </div>
</template>

<script setup>
import { ref } from 'vue'
import { useTodosStore } from '@/stores/todos';

const store = useTodosStore();

const todoText = ref('')

const createTodo = function() {
    // 가벼운 유효성 검사를 해보자! (공백, 스페이스만 입력한 거 배제)
    if(todoText.value) {
        store.addTodo(todoText.value)
        todoText.value = ""
    }
    else
        alert('공백은 등록 불가넝')
}
</script>

<style scoped></style>
```

```jsx
// 📃 TodoList.vue

<template>
    <div>
        <h4>TodoList</h4>
        <hr>
        <TodoListItem v-for="todo in store.todos" :key="todo.id"
        :todo="todo"/>
    </div>
</template>

<script setup>
import TodoListItem from './TodoListItem.vue';
import { useTodosStore } from '@/stores/todos.js';

const store = useTodosStore();

</script>

<style scoped>

</style>
```

```jsx
// 📃 TodoListItem.vue

<template>
    <div>
        <span class="click-cursor" @click="store.updateTodo(todo.id)" 
        :class="{ 'is-done': todo.isDone }">
            {{ todo.text }}
        </span>
        <button @click="store.deleteTodo(todo.id)">X</button>
    </div>
</template>

<script setup>
import { useTodosStore } from '@/stores/todos';

const store = useTodosStore();

defineProps({
    todo: Object,
})
</script>

<style scoped>
.is-done {
    text-decoration: line-through;
}
.click-cursor {
    cursor: pointer;
}
</style>
```

```jsx
// 📃 counter.js

import { ref, computed } from 'vue'
import { defineStore } from 'pinia'

// counter라는 이름으로 객체들을 넣어서 반환해서 사용
export const useCounterStore = defineStore('counter', () => {
  // state(상태)
  const count = ref(0)
  // getter
  const doubleCount = computed(() => count.value * 2)
  // action(기능)
  function increment() {
    count.value++
  }

  // return해야 사용 가능
  return { count, doubleCount, increment }
})
```

```jsx
// 📃 todo.js

import { ref, computed } from 'vue'
import { defineStore } from 'pinia'

export const useTodosStore = defineStore('todos', () => {
  let id = 0
  // state
  const todos = ref([
    { id: id++, text: '수업듣기', isDone: false },
    { id: id++, text: '점심먹기', isDone: false },
    { id: id++, text: '춤추기', isDone: false },
  ]);

  // actions
  const addTodo = function (todoText) {
    todos.value.push({
      id: id++,
      text: todoText,
      isDone: false,
    })
  }

  const deleteTodo = function (todoId) {
    const idx = todos.value.findIndex((todo) => {
      return todo.id === todoId
    })
    console.log(idx)
    // 배열에서 삭제
    todos.value.splice(idx, 1)
  }

  const updateTodo = function (todoId) {
    todos.value = todos.value.map((todo) => {
      // 전달 받은 todo의 id값을 활용해 선택된 todo와 동일 todo를 목록에서 검색
      if (todo.id === todoId) {
        // 일치하는 todo 데이터의 isDone 속성 값을 반대로 재할당 후 새로운 todo 목록 반환
        todo.isDone = !todo.isDone
      }
      return todo
    })
  }

  const doneTodosCount = computed (() => {
    return todos.value.filter((todo) => todo.isDone).length
  })

  return { todos, addTodo, deleteTodo, updateTodo, doneTodosCount }
}, { persist: true})
```

```jsx
// 📃 App.vue

<template>
  <div>
   <h2>Todo PJT</h2>
		<!-- state 직접 접근해서 읽기 가능 -->
   <p>완료 Todo : {{ store.doneTodosCount }}</p>
   <TodoForm />
   <TodoList />
  </div>
</template>

<script setup>
import { useTodosStore } from '@/stores/todos';
import TodoForm from './components/TodoForm.vue';
import TodoList from './components/TodoList.vue';

const store = useTodosStore();

</script>

<style scoped>

</style>
```

```jsx
// 📃 main.js
import { createApp } from 'vue'
import { createPinia } from 'pinia'
import piniaPluginPersistedstate from 'pinia-plugin-persistedstate'
import App from './App.vue'

const app = createApp(App)
const pinia = createPinia()

// createPinia()
pinia.use(piniaPluginPersistedstate)
// app.use(createPinia())
app.use(pinia)

app.mount('#app')
```

### 📌 Local Storage

- 브라우저 내에 `key-value 쌍을 저장`하는 웹 스토리지 객체

### 특징

- 페이지를 새로 고침하고 브라우저를 다시 실행해도 데이터가 유지
- 쿠키와 다르게 네트워크 요청 시 서버로 전송되지 않음
- 여러 탭이나 창 간에 데이터를 공유할 수 있음

### 사용목적

- 웹 어플리케이션에서 사용자 설정, 상태 정보, 캐시 데이터 등을 클라이언트 측에서 보관하여
    
    → 웹 사이트의 성능을 향상시키고 사용자 경험을 개선하기 위함
    

### 📌 pinia-plugin-persistedstate

- 웹 어플리케이션의 상태 (state)를 브라우저의 local storage나 session storage에 영구적으로 저장, 복원

```jsx
$ npm i pinia-plugin-persistedstate

// main.js (설치 및 등록)
import piniaPluginPersistedstate from 'pinia-plugin-persistedstate'

const app = creatApp(App)
const pinia = createPinia()

pinia.use(piniaPluginPersistedstate)
app.use(pinia)

```

```jsx
// stores/counter.js

export const userCounterStore = defineStore('counter', () => {
	...,
	return {todos, addTodo, deleteTodo, updateTodo, doenTodosCount }
}, {persist : true})
```