<template>
  <div class="table">
    <div>
      <input v-model="title" placeholder="Введите заголовок задачи" />
      <button @click.prevent="addTask">Добавить задачу</button>
    </div>
  </div>
  <div>
    <h1>Задачи</h1>
    <div class="table__container" v-for="(task,index) in tasks" :key="task.id">
      <div class="table__item">
      <p>Number: {{index + 1}}</p>
      <p>Title: {{task.title}}</p> 
      </div>
        <div>
        <button @click="deleteTask(task.id)">удалить</button>
        <button>редактировать</button>
      </div>
    </div> 
  </div>
</template>

<script>
import axios from "axios";
export default {
  data() {
    return {
      tasks: [],
      title: "",
    };
  },
  methods: {
    async addTasks() {
      const response = await axios.get("http://localhost:8080/index.php");
      this.tasks = response.data.tasks;
    },
    async addTask() {
      await axios.post("http://localhost:8080/index.php", {
        title: this.title,
      });
      this.title = "";
      this.addTasks();
    },
    async deleteTask(id) {
      await axios.delete(`http://localhost:8080/index.php`,{
        data: {
          id: id,
        },
      }); 
      this.addTasks();
    },
  },
  mounted() {
    this.addTasks();
  },
};
</script>

<style scoped>
.table__container{
  display: flex;  
  border: 1px solid teal; 
  margin: 10px;
  align-items: center;
  justify-content: space-between;
  padding: 15px;
}
button { 
  border: 1px solid teal; 
  cursor: pointer;
  background-color: white;
  margin-inline: 5px;
  padding: 10px;
}
button:hover{
  background-color: teal;
  color: white;
}
.table__item{
  display: flex;
  flex-direction: column;
  gap: 10px;
}
</style>