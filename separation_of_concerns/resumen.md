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

En este ejemplo, hemos separado las responsabilidades:
- `TaskInput` se encarga de la entrada del usuario.
- `TaskList` se encarga de la presentación de las tareas.
- `App` maneja la lógica de negocio y la comunicación entre componentes.

### 1.2. Preguntas Concretas

1. **¿Qué se entiende por separación de preocupaciones en programación?**
   - Respuesta: Es la práctica de dividir un programa en distintas secciones, cada una encargada de una tarea específica.

2. **¿Cuáles son las ventajas de aplicar la separación de preocupaciones en el código?**
   - Respuesta: Mejora la mantenibilidad, facilita la comprensión del código y permite el desarrollo y la prueba de componentes de manera independiente.

3. **En el primer ejemplo, ¿qué elementos de la lógica de negocio y la lógica de presentación están mezclados?**
   - Respuesta: La lógica para manejar la entrada de tareas y la lista de tareas se encuentra en el mismo componente, lo que dificulta su mantenimiento.

4. **¿Cómo mejoró el segundo ejemplo la estructura del código?**
   - Respuesta: Al separar `TaskInput` y `TaskList`, cada componente tiene una única responsabilidad, lo que hace que el código sea más claro y más fácil de mantener.

### Ejercicio

**Reflexión:** Escribe un breve párrafo sobre cómo aplicarías la separación de preocupaciones en un proyecto que hayas desarrollado. ¿Qué componentes separarías y por qué?

Esto debería darte una buena base sobre la separación de preocupaciones. Si tienes alguna pregunta o quieres profundizar en algún aspecto, ¡déjamelo saber!

#### 2.1. Componentes en React
- **Componentes Presentacionales vs. Contenedores:** Aprende la diferencia entre componentes que manejan la UI y aquellos que manejan la lógica de negocio.

#### 2.2. Recursos
- Tutorial: Realiza un tutorial sobre la creación de componentes en React, enfocado en la separación de preocupaciones.
  
#### 2.3. Gráfico
- Diagrama de componentes: Crea un diagrama que muestre cómo se comunican los componentes presentacionales y contenedores.

#### 2.4. Ejercicio
- **Práctica:** Crea una pequeña aplicación React que tenga al menos dos componentes: uno presentacional y otro contenedor.

### Etapa 3: Introducción a TypeScript

**Objetivo:** Familiarizarte con TypeScript y su rol en la separación de preocupaciones.

#### 3.1. Tipos en TypeScript
- **Definición de tipos:** Entiende cómo TypeScript ayuda a mantener la lógica de negocio clara y separada de la presentación.

#### 3.2. Recursos
- Curso básico de TypeScript: Encuentra un curso gratuito en línea.

#### 3.3. Ejercicio
- **Práctica:** Convierte el proyecto de la etapa anterior a TypeScript, definiendo tipos claros para las props y estados.

### Etapa 4: Patrones de Diseño

**Objetivo:** Explorar patrones de diseño que fomentan la separación de preocupaciones.

#### 4.1. Patrones Comunes
- **Ejemplos:** MVP (Model-View-Presenter), MVVM (Model-View-ViewModel), Redux.

#### 4.2. Recursos
- Lectura sobre patrones de diseño: Encuentra información sobre patrones de diseño en el desarrollo web.

#### 4.3. Ejercicio
- **Estudio de Caso:** Analiza un proyecto que use Redux. Describe cómo se logra la separación de preocupaciones.

### Etapa 5: Proyecto Final

**Objetivo:** Integrar todos los conceptos aprendidos en un proyecto completo.

#### 5.1. Desarrollo de una Aplicación Completa
- Crea una aplicación utilizando React y TypeScript, aplicando todos los principios de separación de preocupaciones.

#### 5.2. Recursos
- Plantillas y ejemplos de proyectos en GitHub.

#### 5.3. Cuestionario Final
1. ¿Qué es la separación de preocupaciones?
2. Nombra dos beneficios de aplicar este concepto en desarrollo.
3. ¿Cuál es la diferencia entre un componente presentacional y uno contenedor?
4. Explica cómo TypeScript ayuda en la separación de preocupaciones.
5. Nombra un patrón de diseño que fomente la separación de preocupaciones y explica brevemente cómo lo hace.

### Conclusión

Al final de este proceso, deberías tener una comprensión sólida de cómo aplicar la separación de preocupaciones en el desarrollo web con React y TypeScript. Puedes ajustar las etapas según tu ritmo de aprendizaje y explorar más recursos según sea necesario. ¡Buena suerte en tu aprendizaje!
