<template>
  <div class="app-container">
    <header class="header">
      <h1>📝할 일 목록 정리</h1> <!-- 제목 -->
    </header>

    <main class="main">
      <div class="card">
        <!-- 필터 -->
        <div class="filter-group">
          <button @click="filterMode = 'all'" :class="{ active: filterMode === 'all' }">전체</button>
          <button @click="filterMode = 'active'" :class="{ active: filterMode === 'active' }">미완료</button>
          <button @click="filterMode = 'completed'" :class="{ active: filterMode === 'completed' }">완료</button>
        </div>

        <!-- 입력 창 -->
        <div class="input-group">
          <input v-model="newTodo" @keyup.enter="addTodo" placeholder="할 일을 입력하세요" />
          <button @click="addTodo">추가</button>
        </div>

        <!-- 할 일 목록 -->
        <ul class="todo-list">
          <li v-for="(todo, index) in filteredTodos" :key="index">
            <template v-if="todo.editing">
              <input
                v-model="todo.text"
                @keyup.enter="saveEdit(todo)"
                @keyup.esc="cancelEdit(todo)"
                @blur="saveEdit(todo)"
                class="edit-input"
              />
            </template>
            <template v-else>
              <div style="flex: 1; display: flex; justify-content: space-between; align-items: center;">
                <span :class="{ completed: todo.completed }" @click="toggleTodo(index)">
                  {{ todo.text }}
                </span>
                <button class="edit-btn" @click="editTodo(todo)">✎</button> <!--수정 버튼 그림-->
              </div>
            </template>
            <button class="delete-btn" @click="removeTodo(index)">🗑</button> <!--삭제 버튼 그림 -->
          </li>
        </ul>
      </div>
    </main>

    <footer class="footer">© 2025 MyTodoApp</footer>
  </div>
</template>

<script setup>
import { ref, watch, onMounted, computed } from 'vue'

const STORAGE_KEY = 'vue-todo-list'
const newTodo = ref('')
const todos = ref([])
const filterMode = ref('all') // all, completed, active

onMounted(() => {
  const saved = localStorage.getItem(STORAGE_KEY)
  if (saved) todos.value = JSON.parse(saved)
})

watch(todos, (val) => {
  localStorage.setItem(STORAGE_KEY, JSON.stringify(val))
}, { deep: true })

const filteredTodos = computed(() => {
  if (filterMode.value === 'completed') {
    return todos.value.filter(todo => todo.completed)
  } else if (filterMode.value === 'active') {
    return todos.value.filter(todo => !todo.completed)
  }
  return todos.value
})

const addTodo = () => {
  if (newTodo.value.trim()) {
    todos.value.push({
      text: newTodo.value,
      completed: false,
      editing: false
    })
    newTodo.value = ''
  }
}

const removeTodo = (index) => todos.value.splice(index, 1)
const toggleTodo = (index) => todos.value[index].completed = !todos.value[index].completed
const editTodo = (todo) => { todo.editing = true }
const saveEdit = (todo) => {
  todo.text = todo.text.trim() || '내용 없음'
  todo.editing = false
}
const cancelEdit = (todo) => { todo.editing = false }
</script>

<!--스타일 넣는 곳 -->
<style scoped>
:root {
  --bg-color: #2f3136;
  --card-color: #36393f;
  --text-color: #dcddde;
  --text-muted: #999;
  --accent-color: #5865f2;
  --accent-hover: #4752c4;
  --input-bg: #40444b;
  --delete-color: #ed4245;
}

body, html {
  margin: 0;
  padding: 0;
  font-family: 'Segoe UI', system-ui, sans-serif;
  background-color: var(--bg-color);
  color: var(--text-color);
}

.app-container {
  display: flex;
  flex-direction: column;
  min-height: 100vh;
  background-color: var(--bg-color);
}

.header {
  background-color: var(--card-color);
  color: var(--text-color);
  padding: 20px;
  text-align: center;
  font-size: 24px;
  border-bottom: 1px solid #000;
}

.main {
  flex: 1;
  display: flex;
  justify-content: center;
  padding: 30px 20px;
}

.card {
  width: 100%;
  max-width: 500px;
  background: var(--card-color);
  border-radius: 12px;
  padding: 24px;
  box-shadow: 0 0 12px rgba(0, 0, 0, 0.3);
}

.input-group {
  display: flex;
  gap: 10px;
  margin-bottom: 24px;
}

input {
  flex: 1;
  padding: 10px 14px;
  font-size: 16px;
  border: none;
  border-radius: 8px;
  background-color: var(--input-bg);
  color: var(--text-color);
}

input::placeholder {
  color: #aaa;
}

button {
  background-color: var(--accent-color);
  color: black;
  border: none;
  border-radius: 8px;
  padding: 10px 16px;
  font-size: 16px;
  cursor: pointer;
  transition: background-color 0.2s;
}

button:hover {
  background-color: var(--accent-hover);
}

button.active {
  background-color: var(--accent-hover);
  font-weight: bold;
}

.todo-list {
  list-style: none;
  padding: 0;
  margin: 0;
}

.todo-list li {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 12px 0;
  border-bottom: 1px solid #444;
}

.completed {
  text-decoration: line-through;
  color: var(--text-muted);
}

.delete-btn {
  background: none;
  border: none;
  cursor: pointer;
  font-size: 18px;
  color: var(--delete-color);
}

.delete-btn:hover {
  opacity: 0.8;
}

.edit-btn {
  background: none;
  border: none;
  cursor: pointer;
  font-size: 16px;
  margin-left: 8px;
  color: #000;
}

.edit-btn:hover {
  opacity: 0.7;
}

.footer {
  text-align: center;
  padding: 20px;
  font-size: 14px;
  color: #666;
}

.edit-input {
  flex: 1;
  font-size: 16px;
  padding: 8px 10px;
  border-radius: 8px;
  background-color: var(--input-bg);
  color: var(--text-color);
  border: 1px solid #666;
}

.filter-group {
  display: flex;
  justify-content: center;
  gap: 10px;
  margin-bottom: 20px;
}
</style>
