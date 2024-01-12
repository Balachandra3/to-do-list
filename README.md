# to-do-list
simple to do list application to work
java script:
const express = require('express');
const mongoose = require('mongoose');
const bodyParser = require('body-parser');

const app = express();
const PORT = process.env.PORT || 3000;

mongoose.connect('mongodb://localhost:27017/todo-list', { useNewUrlParser: true, useUnifiedTopology: true });

const todoSchema = new mongoose.Schema({
  task: String,
});

const Todo = mongoose.model('Todo', todoSchema);

app.use(bodyParser.urlencoded({ extended: true }));
app.use(express.static('public'));
app.set('view engine', 'ejs');

app.get('/', (req, res) => {
  Todo.find({}, (err, todos) => {
    if (err) {
      console.log(err);
    } else {
      res.render('index', { todos: todos });
    }
  });
});

app.post('/add', (req, res) => {
  const task = req.body.task;
  const newTodo = new Todo({
    task: task,
  });

  newTodo.save();
  res.redirect('/');
});

app.post('/delete', (req, res) => {
  const taskId = req.body.checkbox;

  Todo.findByIdAndRemove(taskId, (err) => {
    if (err) {
      console.log(err);
    } else {
      console.log('Task removed');
      res.redirect('/');
    }
  });
});

app.listen(PORT, () => {
  console.log(`Server is running on port ${PORT}`);
});
HTML:
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>To-Do List</title>
  <link rel="stylesheet" href="styles.css">
</head>
<body>
  <div id="container">
    <h1>To-Do List</h1>
    <form action="/add" method="post">
      <input type="text" name="task" placeholder="Add a new task..." required>
      <button type="submit">Add</button>
    </form>
    <ul>
      <% todos.forEach(function(todo){ %>
        <li>
          <span><%= todo.task %></span>
          <form action="/delete" method="post">
            <input type="checkbox" name="checkbox" value="<%= todo._id %>">
            <button type="submit">Delete</button>
          </form>
        </li>
      <% }); %>
    </ul>
  </div>
</body>
</html>

CSS:
body {
  font-family: 'Arial', sans-serif;
  background-color: #f4f4f4;
  margin: 0;
  padding: 0;
}

#container {
  max-width: 600px;
  margin: 50px auto;
  background-color: #fff;
  padding: 20px;
  border-radius: 5px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
}

h1 {
  text-align: center;
  color: #333;
}

form {
  display: flex;
  margin-top: 20px;
}

input[type="text"] {
  flex: 1;
  padding: 10px;
}

button {
  padding: 10px;
  background-color: #28a745;
  color: #fff;
  border: none;
  cursor: pointer;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: flex;
  justify-content: space-between;
  align-items: center;
  border-bottom: 1px solid #ddd;
  padding: 10px 0;
}


