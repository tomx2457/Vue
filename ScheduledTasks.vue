<template>
    <div>
      <h2>Scheduled Tasks</h2>
      <p>Tasks are running in the background...</p>
      <form @submit.prevent="addTask">
        <div>
          <label for="taskName">Task Name:</label>
          <input type="text" id="taskName" v-model="newTask.name" required />
        </div>
        <div>
          <label for="taskType">Task Type:</label>
          <select id="taskType" v-model="newTask.type" required>
            <option value="daily">Daily</option>
            <option value="regular">Regular</option>
          </select>
        </div>
        <div v-if="newTask.type === 'regular'">
          <label for="taskInterval">Interval (ms):</label>
          <input type="number" id="taskInterval" v-model="newTask.interval" required />
        </div>
        <button type="submit">Add Task</button>
      </form>
      <ul>
        <li v-for="task in tasks" :key="task.name">{{ task.name }} - {{ task.type }}</li>
      </ul>
    </div>
  </template>
  
  <script>
  export default {
    name: 'ScheduledTasks',
    data() {
      return {
        tasks: [],
        intervalIds: [],
        newTask: {
          name: '',
          type: 'regular',
          interval: 3600000, // Default to 1 hour in milliseconds
        },
      };
    },
    methods: {
      performTask(task) {
        console.log(`Performing task: ${task.name} at`, new Date().toLocaleTimeString());
        // Add your task logic here
      },
      scheduleTask(task) {
        if (task.type === 'daily') {
          const now = new Date();
          const nextMidnight = new Date(now.getFullYear(), now.getMonth(), now.getDate() + 1, 0, 0, 0);
          const timeUntilMidnight = nextMidnight - now;
          setTimeout(() => {
            this.performTask(task);
            const intervalId = setInterval(() => this.performTask(task), 24 * 60 * 60 * 1000);
            this.intervalIds.push(intervalId);
          }, timeUntilMidnight);
        } else if (task.type === 'regular') {
          const intervalId = setInterval(() => this.performTask(task), task.interval);
          this.intervalIds.push(intervalId);
        }
      },
      addTask() {
        const task = { ...this.newTask };
        this.tasks.push(task);
        this.scheduleTask(task);
        this.newTask.name = '';
        this.newTask.type = 'regular';
        this.newTask.interval = 3600000; // Reset to 1 hour
      },
      clearTasks() {
        this.intervalIds.forEach(id => clearInterval(id));
        this.intervalIds = [];
      },
    },
    mounted() {
      // Example of adding a task on mount
      this.addTask();
    },
    beforeUnmount() {
      this.clearTasks();
    },
  };
  </script>
  
  <style scoped>
  h2 {
    color: green;
  }
  form {
    margin-bottom: 20px;
  }
  form div {
    margin-bottom: 10px;
  }
  </style>
  