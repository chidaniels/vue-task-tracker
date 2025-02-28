<template>
    <form @submit="onSubmit" class="add-form">
      <div class="form-control">
        <label>Task</label>
        <input 
          type="text" 
          v-model="text" 
          name="text"
          placeholder="Add Task"
        />
      </div>
      <div class="form-control">
        <label>Day & Time</label>
        <input 
          type="text" 
          v-model="day" 
          name="day"
          placeholder="Add Day & Time"
        />
      </div>
      <div class="form-control">
        <label>Category</label>
        <select v-model="category" name="category">
          <option v-for="cat in categories" :key="cat" :value="cat">
            {{ cat }}
          </option>
        </select>
      </div>
      <div class="form-control">
        <label>Priority</label>
        <select v-model="priority" name="priority">
          <option value="low">Low</option>
          <option value="medium">Medium</option>
          <option value="high">High</option>
        </select>
      </div>
      <div class="form-control form-control-check">
        <label>Set Reminder</label>
        <input type="checkbox" v-model="reminder" name="reminder"/>
      </div>
      <input type="submit" value="Save Task" class="btn btn-block"/>
    </form>
  </template>
  
  <script>
  export default {
    name: 'AddTask',
    props: {
      categories: {
        type: Array,
        required: true
      }
    },
    data() {
      return {
        text: '',
        day: '',
        reminder: false,
        category: 'Other',
        priority: 'medium'
      }
    },
    methods: {
      onSubmit(e) {
        e.preventDefault()
  
        if (!this.text) {
          alert('Please enter a task')
          return
        }
        
        const newTask = {
          id: Math.floor(Math.random() * 100000),
          text: this.text,
          day: this.day,
          reminder: this.reminder,
          category: this.category,
          priority: this.priority,
          createdAt: new Date().toISOString()
        }
  
        this.$emit('add-task', newTask)
  
        this.text = ''
        this.day = ''
        this.reminder = false
        this.category = 'Other'
        this.priority = 'medium'
      }
    }
  }
  </script>
  
  <style scoped>
  .add-form {
    margin-bottom: 40px;
  }
  .form-control {
    margin: 20px 0;
  }
  .form-control label {
    display: block;
  }
  .form-control input, 
  .form-control select {
    width: 100%;
    height: 40px;
    margin: 5px 0;
    padding: 3px 7px;
    font-size: 17px;
    border: 1px solid #ddd;
    border-radius: 5px;
  }
  .form-control-check {
    display: flex;
    align-items: center;
    justify-content: space-between;
  }
  .form-control-check label {
    flex: 1;
  }
  .form-control-check input {
    flex: 2;
    height: 20px;
  }
  </style>