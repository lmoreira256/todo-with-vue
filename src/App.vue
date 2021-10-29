<template>
    <div id="app">
        <h1>Tarefas</h1>
        <TasksProgress :progress="progress" />
        <NewTask @taskAdded="addTask" />
        <TaskGrid
            :tasks="tasks"
            @taskDeleted="deleteTask"
            @taskStateChanged="toggleTaskState"
        />
    </div>
</template>

<script>
import TaskGrid from './components/TaskGrid.vue'
import NewTask from './components/NewTask.vue'
import TasksProgress from './components/TasksProgress.vue'

export default {
    components: { TaskGrid, NewTask, TasksProgress },
    data() {
        return {
            tasks: [],
        }
    },
    computed: {
        progress() {
            const total = this.tasks.length
            const done = this.tasks.filter((t) => !t.pending).length

            return Math.round((done / total) * 100) || 0
        },
    },
    watch: {
        tasks: {
            deep: true,
            handler() {
                localStorage.setItem('tasks', JSON.stringify(this.tasks))
            },
        },
    },
    methods: {
        addTask(task) {
            if (task.name == '' && task.name.length <= 0) {
                return
            }

            const sameName = (t) => t.name === task.name
            const reallyNew = this.tasks.filter(sameName).length == 0

            if (reallyNew) {
                this.tasks.push({
                    name: task.name,
                    pending: task.pending || true,
                })
            }
        },
        deleteTask(taskId) {
            this.tasks.splice(taskId, 1)
        },
        toggleTaskState(taskId) {
            this.tasks[taskId].pending = !this.tasks[taskId].pending
        },
    },
    created() {
        const json = localStorage.getItem('tasks')
        const array = JSON.parse(json)

        this.tasks = Array.isArray(array) ? array : []
    },
}
</script>

<style>
body {
    margin: 0;
    font-family: 'Lato', sans-serif;
    background: linear-gradient(to right, rgb(22, 34, 42), rgb(58, 96, 115));
    color: #fff;
}

#app {
    display: flex;
    flex: 1;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    height: 100vh;
}

#app h1 {
    margin-bottom: 5px;
    font-weight: 300;
    font-size: 3rem;
}
</style>
