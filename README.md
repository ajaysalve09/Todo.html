# Create a To-Do List HTML file
todo_html_content = """<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Floating To-Do List</title>
    <script src="https://kit.fontawesome.com/a076d05399.js" crossorigin="anonymous"></script>
    <style>
        /* Floating To-Do Icon */
        .todo-btn {
            position: fixed;
            left: 15px;
            bottom: 15px;
            background-color: darkorange;
            color: white;
            border: none;
            width: 45px;
            height: 45px;
            border-radius: 50%;
            font-size: 18px;
            cursor: pointer;
            z-index: 9999;
            display: flex;
            align-items: center;
            justify-content: center;
            box-shadow: 0px 3px 8px rgba(0, 0, 0, 0.3);
            transition: transform 0.2s ease-in-out;
        }

        .todo-btn:hover {
            transform: scale(1.1);
        }

        .todo-btn i {
            font-size: 20px;
        }

        /* Background Blur Effect */
        .blur-overlay {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0, 0, 0, 0.3);
            backdrop-filter: blur(4px);
            z-index: 9997;
            display: none;
        }

        /* To-Do Modal */
        .todo-modal {
            display: none;
            position: fixed;
            left: 10px;
            bottom: 70px;
            width: 280px;
            height: 400px;
            background: white;
            border: 1px solid black;
            z-index: 9998;
            box-shadow: 0px 3px 8px rgba(0, 0, 0, 0.2);
        }

        .todo-header {
            padding: 8px;
            background: darkorange;
            color: white;
            text-align: center;
            font-weight: bold;
            font-size: 16px;
        }

        .todo-close {
            float: right;
            cursor: pointer;
            font-size: 18px;
            border-radius: 1em;
        }

        .todo-body {
            padding: 8px;
        }

        .todo-input-container {
            display: flex;
            align-items: center;
            gap: 5px;
            border-radius: 0.5em;
        }

        .todo-input {
            width: 75%;
            padding: 8px;
            font-size: 13px;
            border: 1px solid black;
            border-radius: 0.5em;
        }

        .add-btn {
            width: 18%;
            padding: 8px;
            background: black;
            color: white;
            border: none;
            font-size: 14px;
            cursor: pointer;
            border-radius: 1em;
        }

        .todo-container {
            margin-top: 8px;
            max-height: 360px;
            overflow-y: auto;
        }

        .task {
            background: white;
            border: 1px solid black;
            padding: 8px;
            border-radius: 0.5em;
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 5px;
            position: relative;
        }

        .task.completed .task-text {
            text-decoration: line-through;
            opacity: 0.6;
        }

        .task-text {
            flex-grow: 1;
            margin-left: 8px;
            font-size: 14px;
        }

        .task-buttons {
            display: flex;
            gap: 10px;
            position: absolute;
            bottom: 3px;
            right: 3px;
            border-radius: 1em;
        }

        .check-btn {
            width: 20px;
            height: 20px;
            border-radius: 50%;
            background: white;
            border: 1px solid black;
            cursor: pointer;
            display: flex;
            align-items: center;
            justify-content: center;
            transition: all 0.3s ease-in-out;
        }

        .check-btn i {
            color: transparent;
            font-size: 14px;
        }

        .completed .check-btn {
            background: darkorange;
            border: 1px solid darkorange;
        }

        .completed .check-btn i {
            color: white;
        }

        .edit-btn, .delete-btn {
            background: transparent;
            border: none;
            cursor: pointer;
            font-size: 16px;
            transition: color 0.3s ease-in-out;
            color: darkorange;
        }

        .edit-btn:hover, .delete-btn:hover {
            color: black;
        }
    </style>
</head>
<body>
    <button class="todo-btn" onclick="toggleTodo()">
        <i class="fas fa-tasks"></i>
    </button>

    <div class="blur-overlay" id="blur-overlay" onclick="toggleTodo()"></div>

    <div class="todo-modal" id="todo">
        <div class="todo-header">
            To-Do List <span class="todo-close" onclick="toggleTodo()">✖</span>
        </div>
        <div class="todo-body">
            <div class="todo-input-container">
                <input type="text" id="todo-input" class="todo-input" placeholder="Add a task..." />
                <button class="add-btn" onclick="addTask()">+</button>
            </div>
            <div id="todo-container" class="todo-container"></div>
        </div>
    </div>

    <script>
        function toggleTodo() {
            var todo = document.getElementById("todo");
            var blurOverlay = document.getElementById("blur-overlay");

            if (todo.style.display === "none" || todo.style.display === "") {
                todo.style.display = "block";
                blurOverlay.style.display = "block";
                loadTasks();
            } else {
                todo.style.display = "none";
                blurOverlay.style.display = "none";
            }
        }

        function addTask() {
            var input = document.getElementById("todo-input");
            var taskText = input.value.trim();
            if (taskText === "") return;

            var tasks = JSON.parse(localStorage.getItem("tasks")) || [];
            tasks.push({ text: taskText, completed: false });
            localStorage.setItem("tasks", JSON.stringify(tasks));

            input.value = "";
            loadTasks();
        }

        function loadTasks() {
            var todoContainer = document.getElementById("todo-container");
            todoContainer.innerHTML = "";

            var tasks = JSON.parse(localStorage.getItem("tasks")) || [];
            tasks.forEach((task, index) => {
                var taskElement = document.createElement("div");
                taskElement.classList.add("task");
                if (task.completed) taskElement.classList.add("completed");

                taskElement.innerHTML = `
                    <button class="check-btn" onclick="toggleComplete(${index})">
                        <i class="fas fa-check"></i>
                    </button>
                    <span class="task-text">${task.text}</span>
                    <div class="task-buttons">
                        <button class="edit-btn" onclick="editTask(${index})">
                            <i class="fas fa-edit"></i>
                        </button>
                        <button class="delete-btn" onclick="deleteTask(${index})">
                            <i class="fas fa-trash-alt"></i>
                        </button>
                    </div>
                `;
                todoContainer.appendChild(taskElement);
            });
        }

        window.onload = function () {
            loadTasks();
        };
    </script>
</body>
</html>
"""

# Save the HTML content to a file
file_path = "/mnt/data/todo.html"
with open(file_path, "w") as file:
    file.write(todo_html_content)

file_path
