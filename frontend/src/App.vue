<script setup>
import { reactive, ref, computed, onMounted, watch } from 'vue'
import ChildComponent from './components/ChildComponent.vue'

/* 宣言的レンダリング */
// reactive() はオブジェクト（配列や Map や Set のような組み込み型も含む）に対してのみ動作
const counter = reactive({
  count: 0
})
console.log(counter.count) // 0
// ref() は、任意の値の型を取り、.value プロパティの下で内部の値を公開するオブジェクトを作成することができる
const message = ref('Hello World!')
console.log(message.value) // "Hello World!"
message.value = 'Changed'

/* 属性バインディング */
const titleClass = ref('title')

/* イベントリスナー */
const count = ref(0)
const increment = () => {
  // コンポーネントの状態を更新する
  count.value++
}

/* 双方向バインディング */
const text = ref('')

/* 条件付きレンダリング */
const awesome = ref(true)
const toggle = () => {
  awesome.value = !awesome.value
}

/* リストレンダリング */
// give each todo a unique id
let id = 0
const newTodo = ref('')
const todos = ref([
  { id: id++, text: 'Learn HTML' },
  { id: id++, text: 'Learn JavaScript' },
  { id: id++, text: 'Learn Vue' }
])
const addTodo = () => {
  todos.value.push({ id: id++, text: newTodo.value })
  newTodo.value = ''
}
const removeTodo = (todo) => {
  todos.value = todos.value.filter((t) => t !== todo)
}

/* 算出プロパティー */
const hideCompleted = ref(false)
const filteredTodos = computed(() => {
  return hideCompleted.value
    ? todos.value.filter((t) => !t.done)
    : todos.value
})

/* ライフサイクルとテンプレート参照 */
const p = ref(null)
onMounted(() => {
  p.value.textContent = 'mounted!'
})

/* ウォッチャー */
const todoId = ref(1)
const todoData = ref(null)
async function fetchData() {
  todoData.value = null
  const res = await fetch(
    `https://jsonplaceholder.typicode.com/todos/${todoId.value}`
  )
  todoData.value = await res.json()
}
fetchData()
watch(todoId, fetchData)

/* 子コンポーネントへのプロパティ引き渡し */
const greeting = ref('Hello from parent')

/* 子コンポーネントから親コンポーネントへのイベントの発行 */
const childMsg = ref('No child msg yet')

/* テンプレートフラグメントを スロット を経由して子コンポーネントへ渡す */
const slotMsg = ref('slot message from parent')
</script>

<template>
<div>
  <!-- テンプレートで message ref にアクセスする際、.valueは不要 -->
  <section>
    <h1>宣言的レンダリング</h1>
    <h2>{{ message }}</h2>
    <p>counter.count is: {{ counter.count }}</p>
  </section>

  <section>
    <h1>属性バインディング</h1>
    <h2 :class="titleClass">Make me red</h2>
  </section>

  <section>
    <h1>イベントリスナー</h1>
    <button @click="increment">count is: {{ count }}</button>
  </section>

  <section>
    <h1>双方向バインディング</h1>
    <input v-model="text" placeholder="Type here">
    <p>{{ text }}</p>
  </section>

  <section>
    <h1>条件付きレンダリング</h1>
    <button @click="toggle">toggle</button>
    <h2 v-if="awesome">Vue is awesome!</h2>
    <h2 v-else>Oh no 😢</h2>
  </section>

  <section>
    <h1>リストレンダリング / 算出プロパティー</h1>
    <form @submit.prevent="addTodo">
      <input v-model="newTodo">
      <button>Add Todo</button>    
    </form>
    <ul>
      <li v-for="todo in filteredTodos" :key="todo.id">
        <input type="checkbox" v-model="todo.done">
        <span :class="{ done: todo.done }">{{ todo.text }}</span>
        <button @click="removeTodo(todo)">X</button>
      </li>
    </ul>
    <button @click="hideCompleted = !hideCompleted">
      {{ hideCompleted ? 'Show all' : 'Hide completed' }}
    </button>
  </section>

  <section>
    <h1>ライフサイクルとテンプレート参照</h1>
    <p ref="p">hello</p>
  </section>

  <section>
    <h1>ウォッチャー</h1>
    <p>Todo id: {{ todoId }}</p>
    <button @click="todoId++">Fetch next todo</button>
    <p v-if="!todoData">Loading...</p>
    <pre v-else>{{ todoData }}</pre>
  </section>

  <section>
    <h1>子コンポーネントへのプロパティ引き渡し / 子コンポーネントから親コンポーネントへのイベントの発行</h1>
    <ChildComponent :msg="greeting" @response="(msg) => childMsg = msg" />
  </section>

  <section>
    <ChildComp>Slot Message: {{ slotMsg }}</ChildComp>
  </section>
</div>
</template>

<style scoped>
section + section {
  margin: 30px 0 0;
}
.title {
  color: red;
}
.done {
  text-decoration: line-through;
}
</style>
