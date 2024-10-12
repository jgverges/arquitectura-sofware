
### Ejemplo: Aplicación de Gestión de Usuarios

 Aplicación React con TypeScript que incluya el manejo de APIs. 
 Crearemos una aplicación de gestión de usuarios que permite listar, agregar, editar y eliminar usuarios utilizando una API simulada.


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
