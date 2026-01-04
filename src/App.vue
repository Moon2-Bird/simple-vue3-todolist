<script setup>
import { ref, computed, watch, onMounted } from 'vue'
import { ElButton, ElInput } from 'element-plus' // 手动引入按钮组件
import 'element-plus/es/components/button/style/css' // 引入对应样式

// 这里是逻辑层 (JS)
// 数据：
const title = ref('我的待办清单')
// 你写代码是比一定要记住每一个人api，但你要知道有这个东西，在遇到需要它的功能时能想到它，然后去查找他的具体写法
// 1.在浏览器本地存储中找到名为“参数”的这一项
const savedData = localStorage.getItem('my_todo_data')
const todos = ref(savedData ? JSON.parse(savedData) : [])
// 2.监听todos数据向，当发生会改变时将新的值传入本地存储，注意字符串的JSON格式的转换
watch(todos, (newVal) => {
  // 只要 todos 改变，就将其转为字符串存入本地
  localStorage.setItem('my_todo_data', JSON.stringify(newVal))
}, { deep: true })
// 1.找一个变量存储输入的内容
const userInput = ref('')

const remainTodos = computed(() => {
  return todos.value.filter(item => !item.done).length
})

// 方法：
let addTask = () => {
  // 这一步时为了不让用户添加空的事件，trim可以去掉数据中的空格
  if (userInput.value.trim() === '') return
  // ref定义的变量获取它的值时要.value
  todos.value.push({ id: Date.now(), text: userInput.value, done: false })
  // 添加执行完之后将表单内容置为空
  userInput.value = ""
}

const deleteTask = (targetId) => {
  // 通过过滤器筛选掉被删除的数组，数组过滤的方法要掌握
  todos.value = todos.value.filter(item => item.id !== targetId)
}
</script>

<template>
  <div class="todo-container">
    <!-- 1.标题 -->
    <h1>{{ title }}</h1>
    <!-- 2.将变量与输入的内容绑定 -->
    <div class="input-section">
      <!-- 通过给输入框添加事件@keyup.enter="addTask"可以按下回车触发添加事件的函数，更加简便 -->
      <el-input v-model="userInput" placeholder="请输入任务..." size="large" clearable @keyup.enter="addTask"></el-input>

      <el-button type="primary" size="default" @click="addTask">添加</el-button>

    </div>

    <ul class="todo-list">
      <li v-for="t in todos" :key="t.id">
        <!-- v-model绑定状态，与t.done双向绑定 -->
        <input type="checkbox" v-model="t.done">
        <!-- 动态绑定样式 -->
        <span :class="{ 'completed': t.done }">{{ t.text }}</span>
        <el-button type="primary" size="default" @click="deleteTask(t.id)">删除</el-button>

      </li>
    </ul>

    <div class="footer">
      <span>还有{{ remainTodos }}件事没做</span>
    </div>
  </div>
</template>

<style>
/* 这里是样式层 (CSS) */
.todo-container {
  max-width: 400px;
  margin: 50px auto;
  font-family: Arial, sans-serif;
}

.input-section {
  display: flex;
  gap: 10px;
}

.inputbox {
  flex: 1;
  padding: 8px;
}

.todo-list {
  margin-top: 20px;
  list-style: none;
  padding: 0;
}

li {
  border-bottom: 1px solid #eee;
  padding: 10px 0;
  display: flex;
  justify-content: space-between;
}

.completed {
  text-decoration: line-through;
  /* 删除线 */
  color: #999;
  /* 变灰色 */
}
</style>