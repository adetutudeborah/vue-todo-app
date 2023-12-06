<script setup>
  import { ref, onMounted, computed, watch } from 'vue'

    const todos = ref([])

    const input_content = ref('')

    const editingTodo = ref(null)


    const todo_list = computed(() => todos.value.sort((a,b) =>{
      return a.createdAt - b.createdAt
    }))

    watch(todos, (newVal) => {
      localStorage.setItem('todos', JSON.stringify(newVal))
    }, {
      deep: true
    })

    const addTodo = () => {
      if (input_content.value.trim() === '') {
        return
      }

      todos.value.push({
        content: input_content.value,
        done: false,
        editable: false,
        createdAt: new Date().getTime()
      })

      input_content.value = '';
    }

    const editTodo = (todo) => {

      editingTodo.value = todo
      input_content.value = todo.content
      todo.editable = !todo.editable
    }

    const updateTodo = () => {
      if (editingTodo.value) {
        editingTodo.value.content = input_content.value
        editingTodo.value.editable = false
        editingTodo.value = null
        input_content.value = ''
      }
    }

    const cancelEdit = () => {

      if (editingTodo.value) {
        editingTodo.value.editable = false
        editingTodo.value = null
        input_content.value = ''
      }
    }

    const removeTodo = (todo) => {
      todos.value = todos.value.filter((t) => t !== todo)
    } 

   onMounted(() => {
      todos.value = JSON.parse(localStorage.getItem('todos')) || []
    })

</script>

<template>

	<div class="app">

          <section class="create-todo"> 

               <h1>TODO APP</h1>

              <h3>What's on your todo list?</h3>

              <form id="new-todo-form" @submit.prevent="addTodo">

                    <input 
                      type="text" 
                      name="content" 
                      id="content" 
                      placeholder="e.g. write an article"
                      autocomplete="off"
                      v-model="input_content" 
                    />
                  
                  <input type="submit" value="Add todo" />
              </form>

          </section>

           <section class="todo-list">
                <h3>TODO LIST</h3>

                <div class="list" id="todo-list">

                  <div v-for="todo in todo_list" :key="todo.createdAt" :class="`todo-item ${todo.done && 'done'}`">

                      <div class="checkbox">
                        <input type="checkbox" v-model="todo.done" />
                      </div>

                      <div class="todo-content" v-if="!todo.editable">{{ todo.content }}</div>

                      <div class="actions" v-if="!todo.editable">
                        <button class="edit" @click="editTodo(todo)">Edit</button>
                        <button class="delete" @click="removeTodo(todo)">Delete</button>
                      </div>

                      <div class="edit-todo" v-if="editingTodo === todo">
                        <input type="text" v-model="input_content" @keyup.enter="updateTodo" @keyup.esc="cancelEdit" />

                        <button class="save" @click="updateTodo">Save</button>
                        <button class="cancel" @click="cancelEdit">Cancel</button>                 
                      </div>

                  </div>

                </div>

                <h4 v-if="todos.length === 0">Empty list.</h4>

           </section>


    </div>
</template>

<style scoped>

.create-todo form {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.create-todo input[type="text"] {
  width: 100%;
	font-size: 1rem;
	padding: 0.8rem 1.2rem;
	background-color: #FFF;
	border-radius: 0.5rem;
	margin-bottom: 1.5rem;
  flex: 1; 
  margin-right: 10px; 
}

.create-todo input[type="submit"] {
  color: #ffff;
	border-radius: 8px;
  border: 1px solid transparent;
  padding: 0.8em 1.8em;
  font-size: 1em;
  font-weight: 700;
  font-family: inherit;
  background-color: #1a1a1a;
  background-color: #454545;
  cursor: pointer;
  margin-top: -25px;
}

.todo-list .list {
	margin: 1rem 0;
} 

.todo-item {
	display: flex;
	align-items: center;
	border: 1px solid black;
	padding: 0.5rem 1rem;
	border-radius: 0.5rem;
	margin-bottom: 1rem;
  background-color: #454545;
}

.todo-item .checkbox {
	display: block;
	cursor: pointer;
}

.todo-content {
	flex: 1 1 0%;
  color: #fff;
	font-size: 1.2rem;
  font-weight: 600;
}

.todo-item.done .todo-content {
	text-decoration: line-through;
	color: #888;
}

.actions{
	display: flex;
	align-items: center;
}

.actions button {
	display: block;
	padding: 0.5rem;
	border-radius: 0.25rem;
  color: #000;
	background-color: #fff;
	cursor: pointer;
	transition: 0.2s ease-in-out;
}

.actions .edit {
	margin-right: 0.5rem;
	background-color: #ffff;
  padding: 0.5rem 1rem;
}

.actions .delete {
	background-color: #ffff;
}

.edit-todo {
  display: flex;
	align-items: center;
  justify-content: space-between;
}

.edit-todo input[type="text"] {
  padding: 0.5rem 2rem;
  margin: 0 4rem; 
  border-radius: 0.5rem;
  font-size: 1.1rem;
  font-weight: 500;
}

.edit-todo button {
	display: block;
	padding: 0.5rem;
	border-radius: 0.25rem;
  margin: 0 0.3rem;
  color: #000;
	background-color: #fff;
	cursor: pointer;
	transition: 0.2s ease-in-out;
}

</style>