<script setup>
import { ref, onMounted, computed, watch } from 'vue'

const todos = ref([])
const name = ref('')
const inputContent = ref('')
const inputCategory = ref(null)

const sortedTodos = computed(() => todos.value.sort((a, b) => a.createdAt - b.createdAt))

watch([name, todos], () => {
    localStorage.setItem('name', name.value)
    localStorage.setItem('todos', JSON.stringify(todos.value))
}, { deep: true })

const addTodo = () => {
    if (!inputContent.value.trim() || !inputCategory.value) return
    todos.value.push({
        content: inputContent.value,
        category: inputCategory.value,
        done: false,
        editable: false,
        createdAt: Date.now()
    })
    inputContent.value = ''
    inputCategory.value = null
}

const removeTodo = (todo) => {
    todos.value = todos.value.filter((t) => t !== todo)
}

onMounted(() => {
    name.value = localStorage.getItem('name') || ''
    todos.value = JSON.parse(localStorage.getItem('todos')) || []
})
</script>

<style>
.app {
    display: flex;
    flex-direction: column;
    align-items: center;
}
.todo-list {
    margin-top: 20px;
    width: 90%;
}
.todo-item {
    display: flex;
    align-items: center;
    margin-bottom: 5px; 
    border-bottom: 2px solid #ccc;
    padding-bottom: 5px; 
}
.todo-item:last-child {
    border-bottom: none;
}
.todo-item.done {
    opacity: 0.5;
    transform: translateY(-15px) scale(0.95); 
}
.bubble {
    width: 15px; 
    height: 15px; 
    border-radius: 50%;
    margin-right: 5px;
}
.todo-content input {
    font-size: 14px; 
}
.actions button {
    font-size: 12px; 
}
</style>


<template>
<main class="app">
    <section class="greeting">
        <h2 class="title">
            <input type="text" id="name" placeholder="TaskMaster" v-model="name">
        </h2>
    </section>

    <section class="create-todo">
        <h3>BUAT LIST</h3>
        <form id="new-todo-form" @submit.prevent="addTodo">
            <h4>Apa yang ingin Anda list?</h4>
            <input 
                type="text" 
                name="content" 
                id="content" 
                placeholder="Tuliskan"
                v-model="inputContent" />
            
            <h4>Pilih Kategori</h4>
            <div class="options">
                <label v-for="(category, index) in ['business', 'personal']" :key="index">
                    <input 
                        type="radio" 
                        name="category" 
                        :id="`category${index}`" 
                        :value="category"
                        v-model="inputCategory" />
                    <span :class="`bubble ${category}`"></span>
                    <div>{{ category === 'business' ? 'Pribadi' : 'Umum' }}</div>
                </label>
            </div>
            <input type="submit" value="Simpan Kegiatan" />
        </form>
    </section>

    <section class="todo-list">
        <h3>LIST KEGIATAN</h3>
        <div class="list" id="todo-list">
            <div v-for="todo in sortedTodos" :key="todo.createdAt" :class="`todo-item ${todo.done && 'done'}`">
                <label>
                    <input type="checkbox" v-model="todo.done" />
                    <span :class="`bubble ${todo.category}`"></span>
                </label>
                <div class="todo-content">
                    <input type="text" v-model="todo.content" />
                </div>
                <div class="actions">
                    <button class="delete" @click="removeTodo(todo)">Hapus</button>
                </div>
            </div>
        </div>
    </section>
</main>
</template>
