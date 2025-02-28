<template>
  <div
    @dblclick="$emit('toggle-reminder', task.id)"
    :class="[
      task.reminder ? 'reminder' : '',
      'task',
      `priority-${task.priority || 'medium'}`,
    ]"
  >
    <h3>
      {{ task.text }}
      <i @click="$emit('delete-task', task.id)" class="fas fa-times"></i>
    </h3>
    <p>{{ task.day }}</p>
    <div class="task-meta">
      <span class="category-badge">{{ task.category || "Other" }}</span>
      <span v-if="task.createdAt" class="date-info">
        Added: {{ formatDate(task.createdAt) }}
      </span>
    </div>
  </div>
</template>

<script>
export default {
  name: "Task",
  props: {
    task: Object,
  },
  methods: {
    formatDate(dateString) {
      if (!dateString) return "";
      const date = new Date(dateString);
      return date.toLocaleDateString();
    },
  },
};
</script>

<style scoped>
.fas {
  color: red;
  cursor: pointer;
}
.task {
  background: #f4f4f4;
  margin: 5px;
  padding: 10px 20px;
  cursor: pointer;
  border-radius: 5px;
  position: relative;
}
.task.reminder {
  border-left: 5px solid green;
}
.task h3 {
  display: flex;
  align-items: center;
  justify-content: space-between;
}
.task-meta {
  display: flex;
  justify-content: space-between;
  margin-top: 10px;
  font-size: 0.8rem;
  color: #666;
}
.priority-high {
  border-left: 5px solid red;
}
.priority-medium {
  border-left: 5px solid orange;
}
.priority-low {
  border-left: 5px solid blue;
}
.task.reminder.priority-high {
  border-left: 5px solid red;
  border-right: 5px solid green;
}
.task.reminder.priority-medium {
  border-left: 5px solid orange;
  border-right: 5px solid green;
}
.task.reminder.priority-low {
  border-left: 5px solid blue;
  border-right: 5px solid green;
}
.category-badge {
  background-color: #eee;
  padding: 2px 8px;
  border-radius: 10px;
  font-size: 0.75rem;
}
.date-info {
  font-style: italic;
}
</style>
