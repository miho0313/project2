<template>
    <li>
      <span :class="{ completed: todo.completed }" @click="$emit('toggle')">
        {{ todo.text }}
      </span>
      <button @click="$emit('remove')">삭제</button>
    </li>
  </template>
  
  <script setup>
  defineProps({
    todo: Object
  });
  
  defineEmits(['toggle', 'remove']);
  </script>
  
  <style scoped>
  .completed {
    text-decoration: line-through;
    color: gray;
  }
  </style>
  