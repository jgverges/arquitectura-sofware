
### Ejemplo: Aplicación de Gestión de Tareas

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
