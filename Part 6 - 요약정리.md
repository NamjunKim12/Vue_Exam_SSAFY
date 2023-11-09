# Part 6 - 요약정리

# Routing

> Routing : 네트워크에서 경로를 선택하는 프로세스

    - 웹 어플리케이션에서 다른 페이지 간의 전환과 경로를 관리하는 기술

>

- SSR (Server Side Rendering) : 서버가 하나의 완벽한 페이지를 전송
- CSR (Client Side Rendering) : 브라우저의 클라이언트가 여러 컴포넌트를 렌더링함. (부품 갈아낌) (페이지는 1개이지만, 마치 여러 페이지를 사용하는 것처럼 보임) —> 우린 이거 선택!

### 기본 개념

- `<RouterLink>` : <a>태그 렌더링. - 페이지를 다시 로드하지 않고 URL을 변경
- `<RouterView>` : 컴포넌트를 표시하는 위치

```jsx
// App.vue

<template>
	<header>
		<nav>
			<RouterLink to="/"> Home </RouterLink>
			<RouterLink :to="{ name: 'about' }"> About </RouterLink>
		</nav>
	</header>

	<RouterView />
</template>
```

### 경로 지정하는 법

- index.js 파일 : 컴포넌트의 경로, 이름 지정
- `path: '/user/:id'` : 매개변수를 사용한 동적 경로 매칭

```jsx
// router 폴더 > index.js 파일

import HomeView from '../view/HomeView.vue'
import UserView from '../view/UserView.vue'

const router = createRouter({
	routes : [
		{
			path: '/',
			name: 'home',
			component: HomeView
		},
		{
			path: '/user/:id', // 매개변수를 사용한 동적 경로 매칭
			name: 'user',
			component: UserView
		},

		...
	]
})
```

### 매개변수를 컴포넌트에서 참조하는 법 2가지

- `{{ $route.params.id }}` (권장 X)
- `const userId = ref(route.params.id)` : Composition API 방식 (권장 O)

```jsx
// UserView.vue

<template>
	<div>
		<h1> UserView </h1>
		<h2> {{ $route.params.id }} </h2>
		<h2> {{ userId }} </h2>
	</div>
</template>

<script setup>

import { ref } from 'vue'
import { useRoute } from 'vue-router'

const route = useRoute()
const userId = ref(route.params.id)

</script>
```

### 다른 URL로 이동하는 메서드 2개

- `router.push()` : stack에 push하므로 뒤로가기 가능 (다른 위치로 이동하기)
  (RouterLink를 클릭했을 때 내부적으로 호출되는 메서드)
- `router.replace()` : stack push 안하므로 뒤로가기 불가능 (현재 위치 바꾸기)

```jsx
// UserView.vue

<template>
	<div>
		<button @click="goHome"> 홈으로 </button>
	</div>
</template>

<script setup>

import { useRouter } from 'vue-router'

const router = useRouter()

const goHome = function() {
	// rouer.push('/home')
	// router.push({name: 'home'})
	router.replace({name: 'home'})
}

</script>
```

### `router.push()` 경로 쓰는 방법 4가지

```jsx
// 기본
router.push("/users/eduardo");

// object 형태로 씀
router.push({ path: "/users/eduardo" });

// param 씀
router.push({ name: "user", params: { username: "eduardo" } });

// query 씀. 결과: /register?plan=private
router.push({ path: "/register", query: { plan: "private" } });
```

# Navigation Guard

1. Globally (전역 가드) : 어플리케이션 전역에서 동작

   - `router.beforeEach` : ‘to’ URL Route 객체로 이동

   ex) 로그인 안돼있으면 → `return false` → LoginView 컴포넌트로 못감

   로그인 돼있고, 이동하는 주소 이름이 login이면 → `return { name: 'login' }` → LoginView 컴포넌트로 감

```jsx
// index.js

router.beforeEach((to, form) => {
  const isAuthenticated = false;

  if (!isAuthenticated && to.name !== "login") {
    return { name: "login" };
    // return false도 가능
  }
});
```

2. Per-route (라우터 가드) : 특정 route에서만 동작

   - `router.beforeEnter()` : 이 router에 왔을때 실행

   ex) 로그인 돼있으면 → LoginView 말고 HomeView로 가

```jsx
// index.js

{
	path: '/login',
	name: 'login',
	component: LoginView,
	beforeEnter: (to, from) => {
		if (!isAuthenticated && to.name !== "login") {
			return { name: 'home' }
		}
	}
},
```

3. In-component (컴포넌트 가드) : 특정 컴포넌트 내에서만 동작

   - `onBeforeRouteLeave` : 현재 route에서 떠날 때 실행

   ex) UserView 떠날 때 팝업창 출력

```jsx
// UserView.vue

import { onBeforeRouteLeave } from "vue-router";

onBeforeRouteLeave((to, from) => {
  const answer = window.confirm("정말 떠나실 건가요?");
  if (answer === false) {
    return false;
  }
});
```

- `onBeforeRouteUpdate` : 같은 라우트 내에서 업데이트 될때 실행

  ex) UserVIew 페이지에서 다른 id를 가진 User의 UserView 페이지로 이동 (버튼 누르면 userId가 100으로 update됨)

  (이거 안하면 userId 변경 안됨 - 컴포넌트가 재사용됐기 때문)

```jsx
// UserView.vue

<button @click="routeUpdate">100번 유저 페이지</button>

// UserView.vue

import { onBeforeRouteLeave, onBeforeRouteUpdate } from 'vue-router'

const routeUpdate = function () {
router.push({ name: 'user' , params : { id: 100 } })

onBeforeRouteUpdate((to, from) => {
userId.value = to.params.id
})
```
