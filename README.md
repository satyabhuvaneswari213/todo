# todo
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Smart To-Do App</title>
<style>
body {
  margin: 0;
  font-family: 'Segoe UI', sans-serif;
  background: linear-gradient(135deg, #f6d365, #fda085, #f5576c, #5f9cff);
  background-size: 300% 300%;
  animation: gradientMove 10s ease infinite;
  color: white;
  height: 100vh;
  overflow: hidden;
}

@keyframes gradientMove {
  0% { background-position: 0% 50%; }
  50% { background-position: 100% 50%; }
  100% { background-position: 0% 50%; }
}

#splash {
  position: fixed;
  width: 100%;
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  font-size: 2.5rem;
  background: inherit;
  z-index: 10;
}

.container {
  width: 100%;
  height: 100vh;
  display: flex;
  flex-direction: column;
}

h1 {
  text-align: center;
  margin: 10px 0;
}

.input-section {
  display: flex;
  flex-wrap: wrap;
  gap: 10px;
  padding: 15px;
  background: rgba(255,255,255,0.15);
  backdrop-filter: blur(12px);
}

input, select {
  padding: 12px;
  border-radius: 12px;
  border: none;
  outline: none;
  background: rgba(255,255,255,0.9);
  color: #333;
  font-weight: bold;
}

button {
  cursor: pointer;
  padding: 12px 15px;
  border-radius: 12px;
  border: none;
  background: linear-gradient(45deg, #ff7eb3, #ff758c);
  color: white;
  font-weight: bold;
}

button:hover { transform: scale(1.08); }

.categories {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 15px;
  padding: 15px;
}

.cat-box {
  display: flex;
  align-items: center;
  justify-content: center;
  height: 120px;
  border-radius: 20px;
  cursor: pointer;
  font-weight: bold;
  color: #333;
  transition: 0.3s;
}

.cat-box:hover { transform: scale(1.08); }

.all { background: #f0f0f0; }
.study { background: #d0f0ff; }
.shopping { background: #ffe4d6; }
.today { background: #fff3b0; }
.movie { background: #e4d9ff; }
.birthday { background: #ffd6e0; }
.health { background: #d6ffe0; }
.work { background: #d6e4ff; }
.personal { background: #ffeedd; }

#taskList {
  flex: 1;
  overflow-y: auto;
  padding: 10px;
}

.task {
  background: rgba(255,255,255,0.15);
  padding: 15px;
  margin: 8px 0;
  border-radius: 15px;
  display: flex;
  justify-content: space-between;
}
</style>
</head>
<body>

<div id="splash">My Smart To-Do App</div>

<div class="container" id="main" style="display:none">
  <h1>Smart To-Do List</h1>

  <div class="input-section">
    <input type="text" id="taskInput" placeholder="Enter task" required>
    <input type="date" id="taskDate" required>
    <input type="time" id="taskTime" required>

    <select id="priority" required>
      <option value="">Priority</option>
      <option value="High">🔴 High</option>
      <option value="Medium">🟡 Medium</option>
      <option value="Low">🟢 Low</option>
    </select>

    <select id="category" required>
      <option value="">Category</option>
      <option value="📚 Study">📚 Study</option>
      <option value="🛒 Shopping">🛒 Shopping</option>
      <option value="📅 Today">📅 Today</option>
      <option value="🎬 Movie">🎬 Movie</option>
      <option value="🎂 Birthday">🎂 Birthday</option>
      <option value="💪 Health">💪 Health</option>
      <option value="💼 Work">💼 Work</option>
      <option value="🏠 Personal">🏠 Personal</option>
    </select>

    <button onclick="addTask()">Add</button>
    <button onclick="startVoice()">Voice</button>
  </div>

  <div class="categories">
    <div class="cat-box all" onclick="filterCategory('All')">🌈 All</div>
    <div class="cat-box study" onclick="filterCategory('📚 Study')">📚 Study</div>
    <div class="cat-box shopping" onclick="filterCategory('🛒 Shopping')">🛒 Shopping</div>
    <div class="cat-box today" onclick="filterCategory('📅 Today')">📅 Today</div>
    <div class="cat-box movie" onclick="filterCategory('🎬 Movie')">🎬 Movie</div>
    <div class="cat-box birthday" onclick="filterCategory('🎂 Birthday')">🎂 Birthday</div>
    <div class="cat-box health" onclick="filterCategory('💪 Health')">💪 Health</div>
    <div class="cat-box work" onclick="filterCategory('💼 Work')">💼 Work</div>
    <div class="cat-box personal" onclick="filterCategory('🏠 Personal')">🏠 Personal</div>
  </div>

  <div id="taskList"></div>
</div>

<script>
setTimeout(() => {
  document.getElementById('splash').style.display = 'none';
  document.getElementById('main').style.display = 'flex';
}, 2000);

let tasks = [];
let currentCategory = 'All';

function addTask() {
  const text = document.getElementById('taskInput').value;
  const date = document.getElementById('taskDate').value;
  const time = document.getElementById('taskTime').value;
  const category = document.getElementById('category').value;
  const priority = document.getElementById('priority').value;

  if (!text || !date || !time || !category || !priority) {
    alert('Please fill all fields');
    return;
  }

  tasks.push({ text, date, time, category, priority });
  renderTasks();
  scheduleReminder(text, time);

  document.getElementById('taskInput').value = '';
  document.getElementById('taskDate').value = '';
  document.getElementById('taskTime').value = '';
  document.getElementById('category').value = '';
  document.getElementById('priority').value = '';
}

function filterCategory(cat) {
  currentCategory = cat;
  renderTasks();
}

function renderTasks() {
  const list = document.getElementById('taskList');
  list.innerHTML = '';

  const filtered = currentCategory === 'All'
    ? tasks
    : tasks.filter(t => t.category === currentCategory);

  filtered.forEach((task, index) => {
    list.innerHTML += `
      <div class="task">
        <div>
          <strong>${task.text}</strong><br>
          ${task.category} | ${task.priority}<br>
          ${task.date} ${task.time}
        </div>
        <div>
          <button onclick="editTask(${index})">Edit</button>
          <button onclick="deleteTask(${index})">Delete</button>
        </div>
      </div>
    `;
  });
}

function deleteTask(index) {
  tasks.splice(index, 1);
  renderTasks();
}

function editTask(index) {
  const newTask = prompt('Edit task:', tasks[index].text);
  if (newTask) {
    tasks[index].text = newTask;
    renderTasks();
  }
}

function scheduleReminder(task, time) {
  if (!time) return;

  const now = new Date();
  const [hours, minutes] = time.split(':');
  const reminderTime = new Date();
  reminderTime.setHours(hours, minutes, 0);

  const diff = reminderTime - now;

  if (diff > 0) {
    setTimeout(() => {
      alert('Reminder: ' + task);
    }, diff);
  }
}

function startVoice() {
  const recognition = new (window.SpeechRecognition || window.webkitSpeechRecognition)();
  recognition.start();

  recognition.onresult = function(event) {
    document.getElementById('taskInput').value = event.results[0][0].transcript;
  };
}
</script>

</body>
</html>
