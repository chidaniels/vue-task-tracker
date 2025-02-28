<template>
  <div class="container">
    <Header
      @toggle-add-task="toggleAddTask"
      title="Task Tracker"
      :showAddTask="showAddTask"
    />
    <div v-show="showAddTask">
      <AddTask @add-task="addTask" :categories="categories" />
    </div>
    <div class="filter-container">
      <input
        type="text"
        v-model="searchTerm"
        placeholder="Search tasks..."
        class="search-input"
      />
      <select v-model="categoryFilter" class="category-filter">
        <option value="">All Categories</option>
        <option
          v-for="category in categories"
          :key="category"
          :value="category"
        >
          {{ category }}
        </option>
      </select>
    </div>
    <Tasks
      @toggle-reminder="toggleReminder"
      @delete-task="deleteTask"
      :tasks="filteredTasks"
    />
    <Footer />
  </div>
</template>

<script>
import Header from "./components/Header";
import Footer from "./components/Footer";
import Tasks from "./components/Tasks";
import AddTask from "./components/AddTask";

const API_URL = "http://localhost:5000/tasks";

export default {
  name: "App",
  components: {
    Header,
    Footer,
    Tasks,
    AddTask,
  },
  data() {
    return {
      tasks: [],
      showAddTask: false,
      searchTerm: "",
      categoryFilter: "",
      categories: ["Work", "Personal", "Shopping", "Health", "Other"],
    };
  },
  computed: {
    filteredTasks() {
      return this.tasks.filter((task) => {
        // Filter by search term
        const matchesSearch =
          this.searchTerm === "" ||
          task.text.toLowerCase().includes(this.searchTerm.toLowerCase()) ||
          task.day.toLowerCase().includes(this.searchTerm.toLowerCase());

        // Filter by category
        const matchesCategory =
          this.categoryFilter === "" || task.category === this.categoryFilter;

        return matchesSearch && matchesCategory;
      });
    },
  },
  methods: {
    toggleAddTask() {
      this.showAddTask = !this.showAddTask;
    },
    async addTask(task) {
      try {
        const res = await fetch(API_URL, {
          method: "POST",
          headers: {
            "Content-Type": "application/json",
          },
          body: JSON.stringify(task),
        });

        if (!res.ok) {
          throw new Error("Server error when adding task");
        }

        const data = await res.json();
        this.tasks = [...this.tasks, data];
      } catch (error) {
        console.error("Error adding task:", error);
        // Fallback - add to local array if API fails
        this.tasks = [...this.tasks, task];
      }
    },
    async deleteTask(id) {
      if (confirm("Are you sure you want to delete this task?")) {
        try {
          const res = await fetch(`${API_URL}/${id}`, {
            method: "DELETE",
          });

          if (res.status === 200) {
            this.tasks = this.tasks.filter((task) => task.id !== id);
          } else {
            throw new Error("Error deleting task");
          }
        } catch (error) {
          console.error("Error deleting task:", error);
          // Fallback - delete from local array if API fails
          this.tasks = this.tasks.filter((task) => task.id !== id);
        }
      }
    },
    async toggleReminder(id) {
      try {
        const taskToToggle = this.tasks.find((task) => task.id === id);
        const updTask = {
          ...taskToToggle,
          reminder: !taskToToggle.reminder,
        };

        const res = await fetch(`${API_URL}/${id}`, {
          method: "PUT",
          headers: {
            "Content-Type": "application/json",
          },
          body: JSON.stringify(updTask),
        });

        if (!res.ok) {
          throw new Error("Server error when updating task");
        }

        const data = await res.json();
        this.tasks = this.tasks.map((task) =>
          task.id === id ? { ...task, reminder: data.reminder } : task
        );
      } catch (error) {
        console.error("Error toggling reminder:", error);
        // Fallback - update local array if API fails
        this.tasks = this.tasks.map((task) =>
          task.id === id ? { ...task, reminder: !task.reminder } : task
        );
      }
    },
    async fetchAllTasks() {
      try {
        const res = await fetch(API_URL);

        if (!res.ok) {
          throw new Error("Server error when fetching tasks");
        }

        const data = await res.json();
        return data;
      } catch (error) {
        console.error("Error fetching tasks:", error);
        // Return sample data if API fails
        return [
          {
            id: 1,
            text: "Doctors Appointment",
            day: "March 1st at 2:30pm",
            reminder: true,
            category: "Health",
          },
          {
            id: 2,
            text: "Meeting with boss",
            day: "March 3rd at 1:30pm",
            reminder: true,
            category: "Work",
          },
          {
            id: 3,
            text: "Food Shopping",
            day: "March 3rd at 11:00am",
            reminder: false,
            category: "Shopping",
          },
        ];
      }
    },
    async fetchTask(id) {
      try {
        const res = await fetch(`${API_URL}/${id}`);

        if (!res.ok) {
          throw new Error(`Server error when fetching task ${id}`);
        }

        const data = await res.json();
        return data;
      } catch (error) {
        console.error(`Error fetching task ${id}:`, error);
        // Find in local array if API fails
        return this.tasks.find((task) => task.id === id);
      }
    },
  },
  async created() {
    this.tasks = await this.fetchAllTasks();
  },
};
</script>

<style>
@import url("https://fonts.googleapis.com/css2?family=Poppins:wght@300;400&display=swap");

* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}
body {
  font-family: "Poppins", sans-serif;
}
.container {
  max-width: 500px;
  margin: 30px auto;
  overflow: auto;
  min-height: 300px;
  border: 1px solid steelblue;
  padding: 30px;
  border-radius: 5px;
}
.btn {
  display: inline-block;
  background: #000;
  color: #fff;
  border: none;
  padding: 10px 20px;
  margin: 5px;
  border-radius: 5px;
  cursor: pointer;
  text-decoration: none;
  font-size: 15px;
  font-family: inherit;
}
.btn:focus {
  outline: none;
}
.btn:active {
  transform: scale(0.98);
}
.btn-block {
  display: block;
  width: 100%;
}
.filter-container {
  display: flex;
  margin-bottom: 20px;
  gap: 10px;
}
.search-input,
.category-filter {
  padding: 8px;
  border: 1px solid #ddd;
  border-radius: 5px;
  flex: 1;
}
</style>
