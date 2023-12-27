<template>
  <body>
  <div id="app" class="container mt-5">
    <div class="row">
      <div class="col-md-6">
        <div class="todo-list">
          <h1 class="mb-4">To-Do List</h1>

          <form @submit.prevent="addTask" class="mb-4">
            <div class="row">
              <div class="col-md-6">
                <div class="form-group">
                  <label for="task">Task:</label>
                  <input v-model="newTask.task" id="task" class="form-control" placeholder="Enter task" required>
                </div>
              </div>

              <div class="col-md-3">
                <div class="form-group">
                  <label for="priority">Priority:</label>
                  <select v-model="newTask.priority" class="form-control" required>
                    <option value="low">Low</option>
                    <option value="medium">Medium</option>
                    <option value="high">High</option>
                  </select>
                </div>
              </div>

              <div class="col-md-3">
                <div class="form-group">
                  <label for="dueDate">Due Date:</label>
                  <input type="date" v-model="newTask.dueDate" class="form-control">
                </div>
              </div>
            </div>

            <button type="submit" class="btn btn-primary mt-3">Add Task</button>
          </form>

          <div v-if="tasks.length > 0" class="task-list">
            <h2>Tasks:</h2>
            <div v-for="task in sortedTasks" :key="task.id" class="task" :class="{ 'done': task.done }">
              <p class="task-name">{{ task.task }}</p>
              <p class="priority" :class="priorityClass(task.priority)">{{ task.priority }}</p>
              <p class="due-date" :class="dueDateClass(task.dueDate)">{{ task.dueDate }}</p>
              <div class="action-buttons">
                <button @click="toggleDone(task.id)" class="btn btn-sm btn-success" v-if="!task.done">
                  Mark as Done
                </button>
                <button @click="deleteTask(task.id)" class="btn btn-sm btn-danger">
                  Delete
                </button>
              </div>
            </div>
          </div>

          <div v-else>
            <p>No tasks available. Add a new task above.</p>
          </div>
        </div>
      </div>
    </div>
  </div>
</body>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      newTask: {
        task: '',
        priority: 'medium',
        dueDate: ''
      },
      tasks: []
    };
  },
  computed: {
    sortedTasks() {
      return [...this.tasks]; // Avoid modifying the original array
    }
  },
  methods: {
    addTask() {
      if (this.newTask.task && this.newTask.priority) {
        this.tasks.push({
          id: Date.now(),
          task: this.newTask.task,
          priority: this.newTask.priority,
          dueDate: this.newTask.dueDate,
          done: false // Initially set as not done
        });
        this.sortTasks(); // Sort the tasks
        this.clearNewTask();
      } else {
        alert('Please enter both task and priority');
      }
    },
    sortTasks() {
      // Sort tasks by priority and then by due date
      this.tasks = this.tasks.sort((a, b) => {
        if (a.priority !== b.priority) {
          return a.priority.localeCompare(b.priority);
        } else if (a.dueDate && b.dueDate) {
          return new Date(a.dueDate) - new Date(b.dueDate);
        } else {
          return 0;
        }
      });
    },
    clearNewTask() {
      this.newTask = {
        task: '',
        priority: 'medium',
        dueDate: ''
      };
    },
    deleteTask(taskId) {
      this.tasks = this.tasks.filter(task => task.id !== taskId);
    },
    toggleDone(taskId) {
      const task = this.tasks.find(task => task.id === taskId);
      if (task) {
        task.done = !task.done;
      }
    },
    priorityClass(priority) {
      return {
        'low': priority === 'low',
        'medium': priority === 'medium',
        'high': priority === 'high'
      };
    },
    dueDateClass(dueDate) {
      return {
        'overdue': dueDate && new Date(dueDate) < new Date() // Highlight overdue tasks
      };
    }
  }
};
</script>

<style scoped>
/* Custom styles for the component */


html, body {
  font-family: 'Arial', sans-serif;
  background: linear-gradient(45deg, #2980B9, #6DD5FA, #ffffff); /* Gradient background */
  margin: 0;
  display: flex;
  align-items: center;
  justify-content: center;
  height: 100vh;
}
.todo-list {
  background-color: #fff;
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
}

h1, h2 {
  color: #007BFF;
  margin-bottom: 20px;
}

.task-list {
  margin-top: 20px;
}

.task {
  background-color: #fff;
  padding: 10px;
  margin-bottom: 10px;
  border: 1px solid #ddd;
  border-radius: 4px;
}

.task p {
  margin: 0;
}

.task-name {
  font-weight: bold;
}

.priority, .due-date {
  margin-top: 5px;
}

.low {
  color: #28a745; /* Green for Low priority */
}

.medium {
  color: #ffc107; /* Yellow for Medium priority */
}

.high {
  color: #dc3545; /* Red for High priority */
}

.overdue {
  color: #dc3545; /* Red for overdue due dates */
}

.done {
  text-decoration: line-through;
  color: #6c757d; /* Gray for done tasks */
}
</style>
