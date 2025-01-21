
# 1. **Arquitectura Monolítica (Single Page Application - SPA)**

#### Descripción:
En una **arquitectura monolítica** para una **Single Page Application (SPA)**, toda la aplicación se construye como una única página web que se carga una vez en el navegador y luego actualiza dinámicamente el contenido sin realizar una nueva carga de página completa.

- En una SPA, se utilizan librerías de enrutamiento como **React Router** para manejar las rutas dentro de la aplicación, en lugar de cargar nuevas páginas.
- La lógica del enrutamiento es gestionada completamente por el frontend, sin interacción con el servidor (más allá de las llamadas a la API).
- El estado de la aplicación se maneja dentro del cliente, usualmente con **React's state** o utilizando **Redux**, **Context API** o **Zustand**.

#### Características:
- **Rendimiento rápido**: Después de la carga inicial, las transiciones entre páginas son instantáneas porque solo se actualiza la vista.
- **Sin recarga de página**: Solo se recargan las partes necesarias de la UI, lo que mejora la experiencia del usuario.
- **Enrutamiento en el cliente**: El enrutamiento de páginas es gestionado completamente en el cliente (normalmente mediante `React Router` o similar).
- **Estado centralizado**: Los datos generalmente se mantienen en el cliente utilizando hooks de React o librerías de estado global como **Redux**.

#### Cuándo usarla:
- **Proyectos pequeños y medianos** con pocas rutas o interacciones.
- **Aplicaciones interactivas** donde la recarga de página no es necesaria y se desea una experiencia de usuario fluida.
- **Aplicaciones sin necesidad de SEO avanzado** (aunque puedes añadir SEO con técnicas como SSR o SSG si es necesario).

#### Ejemplo de Proyecto - "To-Do List":

Vamos a crear una pequeña **To-Do List** usando una arquitectura SPA para ilustrar cómo se organiza el código en una aplicación monolítica.

#### Estructura del Proyecto:
```
src/
├── components/          # Componentes reutilizables
│   ├── TodoItem.tsx     # Representa cada tarea
│   └── TodoList.tsx     # Muestra la lista de tareas
├── pages/               # Páginas principales
│   └── Home.tsx         # Página de inicio
├── App.tsx              # Componente principal que gestiona las rutas
├── App.css              # Estilos globales
└── index.tsx            # Punto de entrada de la aplicación
```

#### **index.tsx** - Punto de Entrada
Este archivo es el punto de inicio de la aplicación, donde se renderiza el componente raíz de React.

```tsx
// src/index.tsx
import React from "react";
import ReactDOM from "react-dom";
import App from "./App";
import "./App.css"; // Estilos globales

ReactDOM.render(<App />, document.getElementById("root"));
```

#### **App.tsx** - Componente Principal
Aquí es donde configuramos **React Router** para manejar las rutas.

```tsx
// src/App.tsx
import React from "react";
import { BrowserRouter as Router, Route, Switch } from "react-router-dom";
import Home from "./pages/Home";

const App = () => {
  return (
    <Router>
      <div>
        <h1>Todo List Application</h1>
        <Switch>
          <Route path="/" exact component={Home} />
        </Switch>
      </div>
    </Router>
  );
};

export default App;
```

#### **Home.tsx** - Página de Inicio
En esta página, renderizamos un formulario para agregar tareas y una lista de tareas.

```tsx
// src/pages/Home.tsx
import React, { useState } from "react";
import TodoList from "../components/TodoList";

const Home = () => {
  const [task, setTask] = useState("");
  const [todos, setTodos] = useState<string[]>([]);

  const addTodo = () => {
    if (task.trim() !== "") {
      setTodos([...todos, task]);
      setTask(""); // Limpiar el campo de texto
    }
  };

  return (
    <div>
      <h2>My To-Do List</h2>
      <input
        type="text"
        value={task}
        onChange={(e) => setTask(e.target.value)}
        placeholder="Add a new task"
      />
      <button onClick={addTodo}>Add Task</button>
      <TodoList todos={todos} />
    </div>
  );
};

export default Home;
```

#### **TodoList.tsx** - Componente para Mostrar las Tareas
Este componente recibe la lista de tareas como una propiedad y las muestra.

```tsx
// src/components/TodoList.tsx
import React from "react";

interface TodoListProps {
  todos: string[];
}

const TodoList: React.FC<TodoListProps> = ({ todos }) => {
  return (
    <ul>
      {todos.map((todo, index) => (
        <li key={index}>{todo}</li>
      ))}
    </ul>
  );
};

export default TodoList;
```

#### **App.css** - Estilos Globales
```css
/* src/App.css */
body {
  font-family: Arial, sans-serif;
}

h1 {
  color: #333;
}

input {
  margin-right: 10px;
  padding: 5px;
}

button {
  padding: 5px 10px;
}
```

---

### **Flujo de Funcionamiento de la SPA**:
1. **Cargar la página**: Cuando el usuario accede a la página, React carga la aplicación en una sola solicitud HTTP. El enrutador (`React Router`) gestiona las rutas.
2. **Navegar entre páginas**: El usuario puede navegar entre las diferentes vistas o componentes de la aplicación sin que se recargue la página. Esto se logra gracias a la navegación interna gestionada por `React Router`.
3. **Actualizar el estado**: Al agregar una nueva tarea, el estado de la aplicación se actualiza mediante `useState`, lo que causa que la UI se vuelva a renderizar solo con los cambios.
4. **Interacciones rápidas**: La UI se actualiza rápidamente sin necesidad de recargar la página completa, lo que mejora la experiencia del usuario.

---

#### Ventajas de la Arquitectura SPA:
- **Rendimiento**: La interfaz de usuario se carga una vez y luego solo se actualizan las partes necesarias de la aplicación.
- **Experiencia de usuario fluida**: El usuario no experimenta tiempos de carga entre cambios de vistas.
- **Desarrollo sencillo**: Es fácil de implementar cuando la aplicación no tiene muchas rutas o lógica compleja.

#### Desventajas:
- **SEO limitado**: Las aplicaciones SPA tienen dificultades con el SEO porque el contenido no se renderiza de inmediato. Aunque puedes añadir soporte de SEO mediante técnicas como SSR o pre-rendering (Next.js).
- **Mayor uso de memoria**: Como la aplicación está cargada en su totalidad en el cliente, puede consumir más memoria.
- **Tiempo de carga inicial más largo**: El primer `load` de la página puede ser más largo debido a que todo el contenido se carga en una sola vez.

---

### **Conclusión**:
La **arquitectura SPA** es adecuada para aplicaciones simples a medianas con pocas rutas y donde la interactividad es una prioridad. Si bien no es la mejor opción para aplicaciones donde el SEO es crítico, se puede mejorar con **técnicas de SEO** o utilizando **Next.js** para renderizado en el servidor (SSR). Además, es perfecta para interfaces de usuario rápidas y con cambios dinámicos.

Si estás listo para pasar a la siguiente arquitectura o necesitas más detalles de alguna parte de la SPA, ¡házmelo saber!
