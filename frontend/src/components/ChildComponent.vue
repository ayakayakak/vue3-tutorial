<script setup>
/* 子コンポーネントへのプロパティ引き渡し */
const props = defineProps({
  msg: String
})

/* 子コンポーネントから親コンポーネントへのイベントの発行 */
// 発行されるイベントを宣言
const emit = defineEmits(['response'])
// 引数つきで発行。第一引数はイベント名、追加の引数はイベントリスナーに渡される。
emit('response', 'hello from child')
</script>

<template>
  <div>
    <h2>{{ msg || 'No props passed yet' }}</h2>
    <slot />
  </div>
</template>