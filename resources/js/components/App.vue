<template>
    <div>

        <loading :active.sync="isLoading"
                 :is-full-page="fullPage">
        </loading>

        <table class="table">
            <thead>
            <tr>
                <th>Id</th>
                <th>Task Title</th>
                <th>Priority</th>
                <th>Action</th>
            </tr>
            </thead>
            <tbody>

            <task-component
                v-for="task in tasks"
                :key="task.id"
                :task="task"
                @delete="remove">
            </task-component>

            <tr>
                <td>
                    <input v-model="task.title" type="text" id="task" class="form-control">
                </td>
                <td>
                    <select v-model="task.priority" id="select" class="form-control">
                        <option>Low</option>
                        <option>Medium</option>
                        <option>High</option>
                    </select>
                </td>
                <td>
                    <button @click="store" class="btn btn-primary">Add</button>
                </td>
            </tr>
            </tbody>
        </table>

    </div>
</template>

<script>

    import TaskComponent from "./Task";
    import Loading from 'vue-loading-overlay';
    import 'vue-loading-overlay/dist/vue-loading.css';

    export default {
        name: "App",
        components: {
            TaskComponent,
            Loading
        },
        data() {
            return {
                isLoading: false,
                fullPage: true,
                tasks: [],
                task: {title: '', priority: ''}
            }
        },
        methods: {
            getTasks() {
                window.axios.get('/api/tasks').then(({data}) => {
                    data.forEach(task => {
                        this.tasks.push(task);
                    });
                });
            },
            store() {
                if (this.checkInputs()) {
                    this.isLoading = true;
                    window.axios.post('/api/tasks', this.task).then(savedTask => {
                        this.tasks.push(savedTask.data);
                        this.task.title = '';
                        this.task.priority = '';
                        this.isLoading = false;
                    });
                }
            },
            checkInputs() {
                if (this.task.title && this.task.priority) {
                    return true;
                }
            },
            remove(id) {
                this.isLoading = true;
                window.axios.delete(`/api/tasks/${id}`).then(() => {
                    let index = this.tasks.findIndex(task => task.id === id);
                    this.tasks.splice(index, 1);
                    this.isLoading = false;
                });
            }
        },
        created() {
            this.getTasks();
        }
    }
</script>

<style scoped>

</style>
