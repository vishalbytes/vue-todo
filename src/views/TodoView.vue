<script setup>
import { computed, ref, watch } from "vue";
import { uid } from "uid";
import { Icon } from '@iconify/vue';
import TodoCreator from "../components/TodoCreator.vue";
import TodoItem from "../components/TodoItem.vue";

const todoList = ref([]);

watch(todoList, () => {
  setTodolistToLocalStorage();
}, {
  deep: true,
})
const createTodo = (todo) => {
    todoList.value.push({
        id: uid(),
        todo,
        isCompleted: null,
        isEditing: null,
    })
}
const toggleComplete = (todoIndex) => {
  todoList.value[todoIndex].isCompleted = !todoList.value[todoIndex].isCompleted;
}

const editTodo = (todoIndex) => {
  todoList.value[todoIndex].isEditing = !todoList.value[todoIndex].isEditing;
}

const updateTodo = (newValue, todoIndex) => {
  todoList.value[todoIndex].todo = newValue;
}

const deleteTodo = (todoIndex) => {
  todoList.value.splice(todoIndex, 1);
}

const setTodolistToLocalStorage = () => {
  localStorage.setItem('todo-list', JSON.stringify(todoList.value));
}

const getTodoList = () => {
  const savedTodo = JSON.parse(localStorage.getItem('todo-list'));
  if (savedTodo) {
    todoList.value = savedTodo;
  }
}
getTodoList();

const isAllTodosCompleted = computed(() => {
  return todoList.value.every((todo) => todo.isCompleted );
})
</script>
<template>
    <main>
        <h1> Create Todo</h1>
        <TodoCreator @create-todo="createTodo"/>
        <ul class="todo-list" v-if="todoList.length > 0">
          <TodoItem 
          v-for="(todo, index) in todoList"
          :todo="todo"
          :index="index"
          @toggle-complete="toggleComplete"
          @edit-todo="editTodo"
          @update-todo="updateTodo"
          @delete-todo="deleteTodo"/>
        </ul>
        <p class="todo-msg" v-else>
          <Icon icon="noto-v1:sad-but-relieved-face" color="#41b080" width="22" />
          You have no todo's to complete! Add one!
        </p>
        <p v-if="isAllTodosCompleted && todoList.length > 0">
          <Icon icon="noto-v1:party-popper" />
          <span>You have completed all your todos!</span>
        </p>
    </main>
</template>

<style scoped lang="scss">
main {
  display: flex;
  flex-direction: column;
  max-width: 500px;
  width: 100%;
  margin: 0 auto;
  padding: 40px 16px;

  h1 {
    margin-bottom: 16px;
    text-align: center;
  }
  .todo-list {
    display: flex;
    flex-direction: column;
    list-style: none;
    margin-top: 24px;
    gap: 20px;
  }

  .todo-msg {
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 8px;
    margin-top: 24px;
  }
}
</style>