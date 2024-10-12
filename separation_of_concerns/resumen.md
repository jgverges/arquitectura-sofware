# Separación de preocupaciones

 (Separation of Concerns) aplicado al desarrollo web con React y TypeScript.



### Etapa 1: Introducción a la Separación de Preocupaciones

**Definición:**
La separación de preocupaciones es una práctica que permite dividir un programa en distintas secciones, cada una encargada de una única tarea o responsabilidad. Esto facilita la mantenibilidad y la comprensión del código.

**Ejemplo de Separación de Preocupaciones:**
Supongamos que estamos creando una aplicación sencilla que muestra una lista de tareas. Aquí veremos cómo se puede separar la lógica de presentación de la lógica de negocio.

#### Ejemplo de Código

1. **Código sin separación de preocupaciones:**

```javascript
// App.js
import React, { useState } from 'react';

const App = () => {
  const [tasks, setTasks] = useState([]);
  const [taskInput, setTaskInput] = useState('');

  const addTask = () => {
    setTasks([...tasks, taskInput]);
    setTaskInput('');
  };

  return (
    <div>
      <h1>Tareas</h1>
      <input 
        type="text" 
        value={taskInput} 
        onChange={(e) => setTaskInput(e.target.value)} 
      />
      <button onClick={addTask}>Agregar Tarea</button>
      <ul>
        {tasks.map((task, index) => (
          <li key={index}>{task}</li>
        ))}
      </ul>
    </div>
  );
};

export default App;
```

En este código, la lógica de presentación y la lógica de negocio están mezcladas en el mismo componente.

 La lógica para manejar la entrada de tareas y la lista de tareas se encuentra en el mismo componente, lo que dificulta su mantenimiento.


2. **Código con separación de preocupaciones:**

Aquí dividimos el código en componentes diferentes:

```javascript
// TaskList.js
import React from 'react';

const TaskList = ({ tasks }) => (
  <ul>
    {tasks.map((task, index) => (
      <li key={index}>{task}</li>
    ))}
  </ul>
);

export default TaskList;

// TaskInput.js
import React, { useState } from 'react';

const TaskInput = ({ addTask }) => {
  const [taskInput, setTaskInput] = useState('');

  const handleAddTask = () => {
    addTask(taskInput);
    setTaskInput('');
  };

  return (
    <div>
      <input 
        type="text" 
        value={taskInput} 
        onChange={(e) => setTaskInput(e.target.value)} 
      />
      <button onClick={handleAddTask}>Agregar Tarea</button>
    </div>
  );
};

export default TaskInput;

// App.js
import React, { useState } from 'react';
import TaskInput from './TaskInput';
import TaskList from './TaskList';

const App = () => {
  const [tasks, setTasks] = useState([]);

  const addTask = (task) => {
    setTasks([...tasks, task]);
  };

  return (
    <div>
      <h1>Tareas</h1>
      <TaskInput addTask={addTask} />
      <TaskList tasks={tasks} />
    </div>
  );
};

export default App;
```
¿Cómo mejoró el segundo ejemplo la estructura del código?

Al separar TaskInput y TaskList, cada componente tiene una única responsabilidad, lo que hace que el código sea más claro y más fácil de mantener.

En este ejemplo, hemos separado las responsabilidades:
- `TaskInput` se encarga de la entrada del usuario.
- `TaskList` se encarga de la presentación de las tareas.
- `App` maneja la lógica de negocio y la comunicación entre componentes.
ocupaciones y explica brevemente cómo lo hace.

¡Claro! Vamos a profundizar en ejemplos más complejos que ilustren la separación de preocupaciones en una aplicación React utilizando TypeScript. Imaginemos una aplicación de gestión de tareas con funcionalidad de filtrado y edición de tareas.

## Ejemplo Complejo: Aplicación de Gestión de Tareas

#### Estructura de la Aplicación

1. **`App.tsx`**: Componente principal que gestiona el estado y la lógica de negocio.
2. **`TaskList.tsx`**: Componente que muestra la lista de tareas.
3. **`TaskItem.tsx`**: Componente que representa cada tarea individual.
4. **`TaskInput.tsx`**: Componente para agregar nuevas tareas.
5. **`TaskFilter.tsx`**: Componente para filtrar tareas (por estado: todas, completadas, pendientes).

### Código de Ejemplo

#### 1. `App.tsx`

```typescript
import React, { useState } from 'react';
import TaskInput from './TaskInput';
import TaskList from './TaskList';
import TaskFilter from './TaskFilter';
import { Task } from './types';

const App: React.FC = () => {
  const [tasks, setTasks] = useState<Task[]>([]);
  const [filter, setFilter] = useState<string>('all');

  const addTask = (taskText: string) => {
    const newTask: Task = { id: Date.now(), text: taskText, completed: false };
    setTasks([...tasks, newTask]);
  };

  const toggleTaskCompletion = (taskId: number) => {
    setTasks(tasks.map(task => 
      task.id === taskId ? { ...task, completed: !task.completed } : task
    ));
  };

  const filteredTasks = tasks.filter(task => {
    if (filter === 'completed') return task.completed;
    if (filter === 'pending') return !task.completed;
    return true; // 'all'
  });

  return (
    <div>
      <h1>Gestión de Tareas</h1>
      <TaskInput addTask={addTask} />
      <TaskFilter setFilter={setFilter} />
      <TaskList tasks={filteredTasks} toggleTaskCompletion={toggleTaskCompletion} />
    </div>
  );
};

export default App;
```

#### 2. `TaskList.tsx`

```typescript
import React from 'react';
import TaskItem from './TaskItem';
import { Task } from './types';

interface TaskListProps {
  tasks: Task[];
  toggleTaskCompletion: (taskId: number) => void;
}

const TaskList: React.FC<TaskListProps> = ({ tasks, toggleTaskCompletion }) => (
  <ul>
    {tasks.map(task => (
      <TaskItem key={task.id} task={task} toggleTaskCompletion={toggleTaskCompletion} />
    ))}
  </ul>
);

export default TaskList;
```

#### 3. `TaskItem.tsx`

```typescript
import React from 'react';
import { Task } from './types';

interface TaskItemProps {
  task: Task;
  toggleTaskCompletion: (taskId: number) => void;
}

const TaskItem: React.FC<TaskItemProps> = ({ task, toggleTaskCompletion }) => (
  <li>
    <input 
      type="checkbox" 
      checked={task.completed} 
      onChange={() => toggleTaskCompletion(task.id)} 
    />
    {task.text}
  </li>
);

export default TaskItem;
```

#### 4. `TaskInput.tsx`

```typescript
import React, { useState } from 'react';

interface TaskInputProps {
  addTask: (taskText: string) => void;
}

const TaskInput: React.FC<TaskInputProps> = ({ addTask }) => {
  const [taskInput, setTaskInput] = useState('');

  const handleAddTask = () => {
    if (taskInput.trim()) {
      addTask(taskInput);
      setTaskInput('');
    }
  };

  return (
    <div>
      <input 
        type="text" 
        value={taskInput} 
        onChange={(e) => setTaskInput(e.target.value)} 
        placeholder="Nueva tarea" 
      />
      <button onClick={handleAddTask}>Agregar Tarea</button>
    </div>
  );
};

export default TaskInput;
```

#### 5. `TaskFilter.tsx`

```typescript
import React from 'react';

interface TaskFilterProps {
  setFilter: (filter: string) => void;
}

const TaskFilter: React.FC<TaskFilterProps> = ({ setFilter }) => (
  <div>
    <button onClick={() => setFilter('all')}>Todas</button>
    <button onClick={() => setFilter('completed')}>Completadas</button>
    <button onClick={() => setFilter('pending')}>Pendientes</button>
  </div>
);

export default TaskFilter;
```

#### 6. `types.ts`

```typescript
export interface Task {
  id: number;
  text: string;
  completed: boolean;
}
```

### Análisis del Ejemplo

- **Separación de Preocupaciones:** Cada componente tiene una única responsabilidad. `App.tsx` maneja el estado global y la lógica de negocio, mientras que los componentes secundarios se encargan de su propia parte de la UI.
  
- **Componentes Reutilizables:** Los componentes como `TaskItem` y `TaskList` pueden ser reutilizados fácilmente en otras partes de la aplicación o en diferentes proyectos.

- **Mantenibilidad:** Si necesitas agregar más funcionalidades (como editar o eliminar tareas), puedes hacerlo sin afectar otros componentes. Por ejemplo, podrías crear un nuevo componente `TaskEdit`.

### Preguntas Concretas

1. **¿Qué responsabilidades tiene el componente `App.tsx` en esta aplicación?**
   - Respuesta: Maneja el estado de las tareas, la lógica para agregar y completar tareas, y la lógica para filtrar las tareas.

2. **¿Cómo se implementa la funcionalidad de filtrado en esta aplicación?**
   - Respuesta: Se utiliza un estado `filter` en `App.tsx` para determinar qué tareas mostrar, y se filtran antes de pasarlas a `TaskList`.

3. **¿Por qué es beneficioso separar los componentes en este ejemplo?**
   - Respuesta: Facilita la comprensión y el mantenimiento del código, permite reutilizar componentes y hace que cada componente sea más fácil de probar.

4. **¿Qué pasaría si la lógica de filtrado estuviera dentro de `TaskList` en lugar de `App.tsx`?**
   - Respuesta: Se complicaría la gestión del estado, y `TaskList` tendría múltiples responsabilidades, lo que dificultaría su reutilización y mantenimiento.

### Ejercicio

**Implementación:**
- Agrega una nueva funcionalidad que permita editar una tarea existente. Crea un componente `TaskEdit` que se muestre al hacer clic en una tarea. El componente debe permitir al usuario editar el texto de la tarea.

**Reflexión:**
- Escribe sobre cómo la separación de preocupaciones facilita la implementación de nuevas funcionalidades en esta aplicación. 

Si tienes más preguntas o quieres que profundice en algún aspecto, ¡hazmelo saber!

## Ejemplo con llamadas a API

¡Claro! Vamos a desarrollar un ejemplo más complejo de una aplicación React con TypeScript que incluya el manejo de APIs. Crearemos una aplicación de gestión de usuarios que permite listar, agregar, editar y eliminar usuarios utilizando una API simulada.

### Ejemplo Completo: Aplicación de Gestión de Usuarios

#### Funcionalidades de la Aplicación
1. Listar usuarios desde una API.
2. Agregar un nuevo usuario.
3. Editar un usuario existente.
4. Eliminar un usuario.

#### Estructura de la Aplicación
1. **`App.tsx`**: Componente principal que gestiona el estado y la lógica de negocio.
2. **`UserList.tsx`**: Componente que muestra la lista de usuarios.
3. **`UserItem.tsx`**: Componente que representa cada usuario individual.
4. **`UserForm.tsx`**: Componente para agregar y editar usuarios.
5. **`api.ts`**: Archivo para manejar las llamadas a la API.
6. **`types.ts`**: Definición de tipos de usuario.

### Código de Ejemplo

#### 1. `api.ts`
Aquí gestionamos las llamadas a la API simulada.

```typescript
// api.ts
const API_URL = 'https://jsonplaceholder.typicode.com/users';

export const fetchUsers = async () => {
  const response = await fetch(API_URL);
  return response.json();
};

export const createUser = async (user: { name: string; email: string }) => {
  const response = await fetch(API_URL, {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(user),
  });
  return response.json();
};

export const updateUser = async (id: number, user: { name: string; email: string }) => {
  const response = await fetch(`${API_URL}/${id}`, {
    method: 'PUT',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(user),
  });
  return response.json();
};

export const deleteUser = async (id: number) => {
  await fetch(`${API_URL}/${id}`, { method: 'DELETE' });
};
```

#### 2. `types.ts`
Definimos el tipo `User`.

```typescript
// types.ts
export interface User {
  id: number;
  name: string;
  email: string;
}
```

#### 3. `App.tsx`
El componente principal que gestiona la lógica y el estado.

```typescript
import React, { useEffect, useState } from 'react';
import { fetchUsers, createUser, updateUser, deleteUser } from './api';
import UserList from './UserList';
import UserForm from './UserForm';
import { User } from './types';

const App: React.FC = () => {
  const [users, setUsers] = useState<User[]>([]);
  const [editingUser, setEditingUser] = useState<User | null>(null);

  useEffect(() => {
    const loadUsers = async () => {
      const usersData = await fetchUsers();
      setUsers(usersData);
    };
    loadUsers();
  }, []);

  const handleCreateUser = async (user: { name: string; email: string }) => {
    const newUser = await createUser(user);
    setUsers([...users, newUser]);
  };

  const handleUpdateUser = async (user: User) => {
    const updatedUser = await updateUser(user.id, user);
    setUsers(users.map(u => (u.id === user.id ? updatedUser : u)));
    setEditingUser(null);
  };

  const handleDeleteUser = async (id: number) => {
    await deleteUser(id);
    setUsers(users.filter(user => user.id !== id));
  };

  return (
    <div>
      <h1>Gestión de Usuarios</h1>
      <UserForm onSubmit={editingUser ? handleUpdateUser : handleCreateUser} user={editingUser} />
      <UserList users={users} onDelete={handleDeleteUser} onEdit={setEditingUser} />
    </div>
  );
};

export default App;
```

#### 4. `UserList.tsx`
Componente para mostrar la lista de usuarios.

```typescript
import React from 'react';
import UserItem from './UserItem';
import { User } from './types';

interface UserListProps {
  users: User[];
  onDelete: (id: number) => void;
  onEdit: (user: User) => void;
}

const UserList: React.FC<UserListProps> = ({ users, onDelete, onEdit }) => (
  <ul>
    {users.map(user => (
      <UserItem key={user.id} user={user} onDelete={onDelete} onEdit={onEdit} />
    ))}
  </ul>
);

export default UserList;
```

#### 5. `UserItem.tsx`
Componente que representa cada usuario individual.

```typescript
import React from 'react';
import { User } from './types';

interface UserItemProps {
  user: User;
  onDelete: (id: number) => void;
  onEdit: (user: User) => void;
}

const UserItem: React.FC<UserItemProps> = ({ user, onDelete, onEdit }) => (
  <li>
    {user.name} ({user.email})
    <button onClick={() => onEdit(user)}>Editar</button>
    <button onClick={() => onDelete(user.id)}>Eliminar</button>
  </li>
);

export default UserItem;
```

#### 6. `UserForm.tsx`
Componente para agregar y editar usuarios.

```typescript
import React, { useState, useEffect } from 'react';
import { User } from './types';

interface UserFormProps {
  onSubmit: (user: { name: string; email: string }) => void;
  user: User | null;
}

const UserForm: React.FC<UserFormProps> = ({ onSubmit, user }) => {
  const [name, setName] = useState('');
  const [email, setEmail] = useState('');

  useEffect(() => {
    if (user) {
      setName(user.name);
      setEmail(user.email);
    } else {
      setName('');
      setEmail('');
    }
  }, [user]);

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault();
    onSubmit({ name, email });
  };

  return (
    <form onSubmit={handleSubmit}>
      <input 
        type="text" 
        value={name} 
        onChange={(e) => setName(e.target.value)} 
        placeholder="Nombre" 
        required 
      />
      <input 
        type="email" 
        value={email} 
        onChange={(e) => setEmail(e.target.value)} 
        placeholder="Email" 
        required 
      />
      <button type="submit">{user ? 'Actualizar' : 'Agregar'}</button>
    </form>
  );
};

export default UserForm;
```

### Análisis del Ejemplo

- **Separación de Preocupaciones:** Cada componente tiene su propia responsabilidad. `App.tsx` se encarga de gestionar el estado y las interacciones con la API, mientras que `UserList`, `UserItem` y `UserForm` manejan la presentación de los datos.

- **Manejo de API:** Las funciones en `api.ts` gestionan todas las llamadas a la API, lo que permite cambiar la implementación de la API sin afectar los componentes de presentación.

- **Mantenibilidad y Escalabilidad:** Este enfoque facilita la adición de nuevas funcionalidades, como la búsqueda o la paginación de usuarios, sin complicar el código existente.

### Preguntas Concretas

1. **¿Cuál es la función del archivo `api.ts` en esta aplicación?**
   - Respuesta: Maneja las llamadas a la API para obtener, crear, actualizar y eliminar usuarios.

2. **¿Cómo se gestiona el estado de los usuarios en el componente `App.tsx`?**
   - Respuesta: El estado se gestiona mediante el hook `useState`, y se carga inicialmente desde la API con `useEffect`.

3. **¿Por qué es importante separar la lógica de negocio de la presentación en esta aplicación?**
   - Respuesta: Facilita la prueba, el mantenimiento y la reutilización de componentes, y permite realizar cambios en la lógica sin afectar la interfaz de usuario.

4. **¿Qué ocurriría si se mezclara la lógica de la API directamente en los componentes de presentación?**
   - Respuesta: Se dificultaría la mantenibilidad, ya que los componentes se volverían más complicados y menos reutilizables.

### Ejercicio

**Implementación:**
- Añade una funcionalidad de búsqueda que permita filtrar usuarios por nombre. Crea un componente `UserSearch` que maneje la entrada del usuario y actualice la lista mostrada.

**Reflexión:**
- Escribe sobre cómo la separación de preocupaciones facilita la implementación de nuevas funcionalidades en esta aplicación y cómo podrías extenderla aún más.

Si tienes más preguntas o deseas explorar más conceptos, ¡no dudes en preguntar!
