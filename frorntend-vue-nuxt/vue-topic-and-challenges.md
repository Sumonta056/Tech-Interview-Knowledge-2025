# Vue Topic & Challenges!

<details>

<summary>How to validate props in Vue Components with Default Value?</summary>

```vue
<script setup lang="ts">
defineProps({
  type: {
    type: String,
    default: 'default',
    validator: (value: string) =>
      ['primary', 'ghost', 'dashed', 'link', 'text', 'default'].includes(value),
  },
})
</script>

<template>
  <button>Button</button>
</template>

```

</details>

