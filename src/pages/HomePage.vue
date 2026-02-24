<template>
  <div class="header">
    <div class="header__container">
      <h1>Task Manager</h1>
    </div>
  </div>

  <div class="add">
    <h2>Добавить новую задачу</h2>
    <div class="add__title">
      <input v-model="title" placeholder="Введите заголовок задачи" />
      <button @click.prevent="addTask">Добавить</button>
    </div>
    <div class="add__select">
      <select v-model="categories_id">
        <option
          v-for="category in categories"
          :key="category.id"
          :value="category.id"
        >
          {{ category.title }}
        </option>
      </select>
    </div>
  </div>

  <div class="tasks">
    <h3>Мои задачи</h3>

    <div v-for="(task, index) in tasks" :key="task.id" class="tasks-items">
      
      <div v-if="editId === task.id" class="tasks__buttons">
        <input v-model="editTitle" />
        <button @click="updateTask(task.id)" class="tasks__buttons--modif">
          ✅
        </button>
        <button @click="cancelEdit" class="tasks__buttons--modif">❌</button>
      </div>

      <div v-else class="tasks__title">
        <p>{{ index + 1 + ". " + task.task_title }}</p>
      </div>

      <div class="tasks__category">
        <span>
          {{ task.category_title }}
        </span>
      </div>

      <div class="tasks__buttons">
        <button @click="startEdit(task)" class="tasks__buttons--modif">
          ✏️
        </button>
        <button @click="deleteTask(task.id)" class="tasks__buttons--modif">
          ❌
        </button>
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
      categories: [],
      title: "",
      categories_id: null,
      editId: null,
      editTitle: "",
    };
  },
  methods: {
    startEdit(task) {
      this.editId = task.id;
      this.editTitle = task.task_title;
      this.categories_id = task.categories_id;
    },

    cancelEdit() {
      this.editId = null;
      this.editTitle = "";
      this.categories_id = null;
    },

    async updateTask(id) {
      await axios.put(`http://localhost:8080/index.php`, {
        id: id,
        title: this.editTitle,
        categories_id: this.categories_id,
      });
      this.editId = null;
      this.editTitle = "";
      this.addTasks();
    },

    async addTasks() {
      const response = await axios.get("http://localhost:8080/index.php");
      this.tasks = response.data.tasks;
    },

    async addCategories() {
      const response = await axios.get("http://localhost:8080/categories.php");
      this.categories = response.data.categories;
    },

    async addTask() {
      await axios.post("http://localhost:8080/index.php", {
        title: this.title,
        categories_id: this.categories_id,
      });
      this.clearForm();
      this.addTasks();
    },

    async deleteTask(id) {
      if (confirm("Вы уверены, что хотите удалить эту задачу?")) {
        await axios.delete(`http://localhost:8080/index.php`, {
          data: {
            id: id,
          },
        });
        this.addTasks();
      }
    },

    clearForm() {
      this.title = "";
      this.categories_id = null;
    },
  },

  mounted() {
    this.addTasks();
    this.addCategories();
  },
};
</script>

<style scoped>
p {
  color: hsl(0, 3%, 40%);
}
input {
  outline: none;
}
.header {
  padding: 40px 100px;
}
.header__container {
  max-width: 1000px;
  margin: 0 auto;
  padding: 20px 40px;
  background-color: #223a5e;
  border-radius: 4px;
  color: white;
}

/* start add */
.add {
  max-width: 980px;
  margin: 0 auto;
  padding: 10px 20px;
  display: flex;
  flex-direction: column;
  gap: 18px;
  margin-bottom: 20px;
}
.add__title {
  display: flex;
  gap: 14px;
}
.add__title input {
  width: 100%;
  padding: 10px 20px;
  font-size: 14px;
  border: 1px solid #ccc;
}
.add__select select {
  border: none;
  outline: none;
  font-size: 14px;
  border: 1px solid #ccc;
}
.add__title button {
  border-radius: 4px;
  border: none;
  cursor: pointer;
  background-color: #f1780e;
  color: white;
  padding: 10px 30px;
}
/* end add */

.tasks {
  max-width: 980px;
  margin: 0 auto;
  padding: 10px 20px;
  display: flex;
  flex-direction: column;
  gap: 12px;
}
.tasks-items {
  display: flex;
  justify-content: space-between;
  align-items: center;
  background-color: white;
  padding: 16px 24px;
  border: 1px solid #ccc;
  border-radius: 4px; 
}
.tasks__title {
 
}
.tasks__category {
  padding: 5px 10px;
  border-radius: 12px;
  background-color: #48aff3;
  color: white;    
}
.tasks__buttons {
  display: flex;
  justify-content: center;
  gap: 10px; 
}
  
.tasks__buttons--modif {
  background-color: white;
  border: none;
  cursor: pointer;
}
</style>