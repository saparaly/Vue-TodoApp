<script >
import TodoApp from './components/TodoApp.vue';

export default {
  // name: 'App',
  components: {
    TodoApp
  },

  data() {
    return {
      task: '',
      editedTask: null,
      availableStatuses: ['to-do', 'in-progress', 'finished'],
      tasks: [
        {
          name: 'task1',
          status: 'to-do'
        },
        {
          name: 'task2',
          status: 'in-progress'
        }
      ]
    }
  },
  // mounted() {
  //   if (localStorage.tasks) {
  //     this.tasks = localStorage.tasks
  //   }
  // },
  // watch: {
  //   tasks(newTasks){
  //     localStorage.tasks = JSON.parse(newTasks)
  //   }
  // },
  async mounted(){
    const data = await localStorage.getItem('tasks')
    if (data) {
      this.tasks = JSON.parse(data)
    }
  },
  methods: {
    submitTask(){
     if ( this.editedTask === null) {
      this.tasks.push({
        name: this.task,
        status: 'to-do'
      })
     } else {
      this.tasks[this.editedTask].name = this.task
      this.editedTask = null
     }
      this.task = ''
      localStorage.setItem('tasks', JSON.stringify(this.tasks))
    },
    deleteTask(index){
      this.tasks.splice(index, 1)
      localStorage.setItem('tasks', JSON.stringify(this.tasks))
    },
    editTask(index) {
      this.$refs.task.focus()
      this.task = this.tasks[index].name 
      this.editedTask = index
      this.task = ''
    },
    statusChange(index) {
      let newIndex = this.availableStatuses.indexOf(this.tasks[index].status)
      if (++newIndex > 2) newIndex = 0
      this.tasks[index].status = this.availableStatuses[newIndex]
      localStorage.setItem('tasks', JSON.stringify(this.tasks)) 
    }
  }
}
</script>

<template>
  <div class="container">
    <h1>Todo App</h1>
    <div class="create__task">
      <input type="text" placeholder="Enter task" required v-model="task" ref="task">
      <button @click="submitTask">submit</button>
    </div>

    <div class="table__tasks">
      <div class="tasks__header">
        <div class="tasks__item">Task</div>
        <div class="tasks__item">Status</div>
        <div class="tasks__item">Edit</div>
        <div class="tasks__item">Delete</div>
      </div>
      <div class="tasks__collection" v-for="(task, index) in tasks" :key="index">
        <div class="tasks__item">
          <span :class="{'finished': task.status === 'finished'}">
            {{task.name}}
          </span>
        </div>
        <div class="tasks__item"> 
          <span @click="statusChange(index)" class="status" :class="{'red': task.status === 'to-do', 'yellow': task.status === 'in-progress', 'green': task.status === 'finished'}">
            {{task.status}}
          </span>
        </div>
        <div class="tasks__item edit" @click="editTask(index)">Edit</div>
        <div class="tasks__item delete" @click="deleteTask(index)">Delete</div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.table__tasks {
  display: flex;
  flex-direction: column;
  padding: 10px;
  border: 1px solid #333;
}
.tasks__header .tasks__item{
  font-weight: bold;
}
.tasks__header, .tasks__collection {
  display: flex;
  align-items: center;
  /* justify-content: space-between; */
}
.tasks__item {
  border: 1px solid #333;
  padding: 10px;
  width: 100px;
}

.finished {
  text-decoration: line-through;
}
.tasks__item.delete, .tasks__item.edit{
  cursor: pointer;
}
.status{
  transition: all 0.15s ease-in-out;
  cursor: pointer;
}
.green {
  color: green;
}
.yellow {
  color: yellow;
}
.red{
  color: red;
}
</style>
