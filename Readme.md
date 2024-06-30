# 📝 Todo Application

This is a simple yet functional Todo application built using React. It allows you to add, edit, complete, and delete todos, and persists them in `localStorage`.

## 🚀 Features

- **Add Todos**: Quickly add new tasks.
- **Edit Todos**: Modify tasks inline.
- **Toggle Completion**: Mark tasks as completed.
- **Delete Todos**: Remove tasks permanently.
- **Persist Todos**: Stores todos in `localStorage` for persistent data.

## 🛠️ Installation

1. **Clone the Repository**:
   ```bash
   git clone https://github.com/shyambabujayswal/todo-app.git
   ```
2. **Navigate to the Project Directory**:
   ```bash
   cd todo-app
   ```
3. **Install Dependencies**:
   ```bash
   npm install
   ```

## 🏃‍♂️ Running the Application

To start the development server:
```bash
npm start
```
Open [http://localhost:3000](http://localhost:3000) in your browser to view the app.

## 📂 File Structure

```
todo-app/
├── public/
├── src/
│   ├── components/
│   │   ├── TodoForm.js
│   │   └── TodoItem.js
│   ├── context/
│   │   ├── index.js
│   │   └── TodoContext.js
│   ├── App.css
│   ├── App.js
│   ├── index.css
│   └── index.js
└── package.json
```

- **`components/`**: Contains the `TodoForm` and `TodoItem` components.
- **`context/`**: Contains `index.js` for exports and `TodoContext.js` for state management.
- **`App.js`**: Main component where state and context are managed.

## 📋 Usage

1. **Add a Todo**: Enter a task in the input field and click `Add`.
2. **Edit a Todo**: Click the ✏️ button, modify the task, then click 📁 to save.
3. **Complete a Todo**: Click the checkbox to mark the task as completed.
4. **Delete a Todo**: Click ❌ to remove the task.

## 🧩 Code Overview

### `TodoForm.js`

Handles adding new todos. Uses `useState` to manage the input state and `useTodo` to add the todo.

```javascript
// src/components/TodoForm.js
import { useState } from 'react';
import { useTodo } from '../context/TodoContext';

function TodoForm() {
    const [todo, setTodo] = useState("");
    const { addTodo } = useTodo();

    const add = (e) => {
        e.preventDefault();
        if (!todo) return;
        addTodo({ todo, completed: false });
        setTodo("");
    };

    return (
        <form onSubmit={add} className="flex">
            <input
                type="text"
                placeholder="Write Todo..."
                value={todo}
                onChange={(e) => setTodo(e.target.value)}
            />
            <button type="submit">Add</button>
        </form>
    );
}

export default TodoForm;
```

### `TodoItem.js`

Handles displaying, editing, and deleting todos. Uses `useState` for editable state and `useTodo` for actions.

```javascript
// src/components/TodoItem.js
import { useState } from 'react';
import { useTodo } from '../context/TodoContext';

function TodoItem({ todo }) {
    const [isTodoEditable, setIsTodoEditable] = useState(false);
    const [todoMsg, setTodoMsg] = useState(todo.todo);
    const { updateTodo, deleteTodo, toggleComplete } = useTodo();

    return (
        <div>
            <input
                type="checkbox"
                checked={todo.completed}
                onChange={() => toggleComplete(todo.id)}
            />
            <input
                type="text"
                value={todoMsg}
                onChange={(e) => setTodoMsg(e.target.value)}
                readOnly={!isTodoEditable}
            />
            <button onClick={() => {
                if (todo.completed) return;
                if (isTodoEditable) {
                    updateTodo(todo.id, { ...todo, todo: todoMsg });
                } else {
                    setIsTodoEditable(!isTodoEditable);
                }
            }}>
                {isTodoEditable ? "Save" : "Edit"}
            </button>
            <button onClick={() => deleteTodo(todo.id)}>Delete</button>
        </div>
    );
}

export default TodoItem;
```

### `TodoContext.js`

Provides context for managing todos globally.

```javascript
// src/context/TodoContext.js
import { createContext, useContext } from 'react';

export const TodoContext = createContext({
    todos: [],
    addTodo: (todo) => {},
    updateTodo: (id, todo) => {},
    deleteTodo: (id) => {},
    toggleComplete: (id) => {},
});

export const useTodo = () => useContext(TodoContext);
export const TodoProvider = TodoContext.Provider;
```

### `index.js`

Exports `TodoContext` and `TodoProvider` for easier imports.

```javascript
// src/context/index.js
export { TodoContext, TodoProvider } from './TodoContext';
```

### `App.js`

Main component that integrates everything and manages the application state.

```javascript
// src/App.js
import { useState, useEffect } from 'react';
import { TodoProvider } from './context';
import TodoForm from './components/TodoForm';
import TodoItem from './components/TodoItem';
import './App.css';

function App() {
    const [todos, setTodos] = useState([]);

    const addTodo = (todo) => {
        setTodos((prev) => [{ id: Date.now(), ...todo }, ...prev]);
    };

    const updateTodo = (id, updatedTodo) => {
        setTodos((prev) => prev.map((todo) => (todo.id === id ? updatedTodo : todo)));
    };

    const deleteTodo = (id) => {
        setTodos((prev) => prev.filter((todo) => todo.id !== id));
    };

    const toggleComplete = (id) => {
        setTodos((prev) =>
            prev.map((todo) => (todo.id === id ? { ...todo, completed: !todo.completed } : todo))
        );
    };

    useEffect(() => {
        const storedTodos = JSON.parse(localStorage.getItem('todos'));
        if (storedTodos) setTodos(storedTodos);
    }, []);

    useEffect(() => {
        localStorage.setItem('todos', JSON.stringify(todos));
    }, [todos]);

    return (
        <TodoProvider value={{ todos, addTodo, updateTodo, deleteTodo, toggleComplete }}>
            <div>
                <h1>Manage Your Todos</h1>
                <TodoForm />
                <div>
                    {todos.map((todo) => (
                        <TodoItem key={todo.id} todo={todo} />
                    ))}
                </div>
            </div>
        </TodoProvider>
    );
}

export default App;
```

## 👨‍💻 Contributing

Feel free to submit pull requests or open issues for any bugs or feature requests.

## 📝 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.



