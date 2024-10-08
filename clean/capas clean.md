La arquitectura CLEAN (o arquitectura limpia) es un enfoque de diseño de software que promueve la separación de preocupaciones y facilita la mantenibilidad, escalabilidad y testabilidad del código. Se organiza en capas, cada una con responsabilidades bien definidas. Las principales capas son:

1. **Capa de Entidades**
2. **Capa de Casos de Uso**
3. **Capa de Interfaces**
4. **Capa de Infraestructura**

A continuación, se describe cada capa con ejemplos en TypeScript y se explica la relación entre ellas, así como escenarios donde se cumple o no se cumple la arquitectura CLEAN.

### 1. Capa de Entidades

**Descripción**: Esta capa contiene las reglas de negocio y las entidades del dominio. Se enfoca en representar conceptos centrales y sus comportamientos, sin depender de detalles externos.

**Ejemplo en TypeScript**:

```typescript
// Entity.ts
class User {
    constructor(public id: number, public name: string, public email: string) {}

    changeEmail(newEmail: string) {
        this.email = newEmail;
    }
}
```

**Cumple**: La entidad `User` no depende de ningún detalle de implementación externo, como bases de datos o servicios.

**No cumple**: Si la entidad tiene lógica para conectarse a una base de datos o lógica de presentación.

### 2. Capa de Casos de Uso

**Descripción**: Define las operaciones que el sistema puede realizar. Esta capa orquesta la lógica de negocio utilizando entidades y maneja la interacción entre ellas.

**Ejemplo en TypeScript**:

```typescript
// UserService.ts
class UserService {
    constructor(private userRepository: UserRepository) {}

    async changeUserEmail(userId: number, newEmail: string) {
        const user = await this.userRepository.findById(userId);
        user.changeEmail(newEmail);
        await this.userRepository.save(user);
    }
}
```

**Cumple**: `UserService` utiliza la entidad `User` y el repositorio para realizar la operación sin preocuparse por detalles de la implementación.

**No cumple**: Si `UserService` intenta implementar la lógica de acceso a datos directamente.

### 3. Capa de Interfaces

**Descripción**: Define cómo interactúan los casos de uso con el exterior. Incluye controladores, vistas o cualquier interfaz que el sistema exponga.

**Ejemplo en TypeScript**:

```typescript
// UserController.ts
class UserController {
    constructor(private userService: UserService) {}

    async changeEmail(req: Request, res: Response) {
        const { userId, newEmail } = req.body;
        await this.userService.changeUserEmail(userId, newEmail);
        res.status(200).send('Email changed successfully');
    }
}
```

**Cumple**: `UserController` maneja la solicitud del cliente y llama al caso de uso correspondiente.

**No cumple**: Si `UserController` implementa la lógica de negocio o acceso a datos.

### 4. Capa de Infraestructura

**Descripción**: Maneja detalles técnicos como la persistencia de datos, sistemas externos y otros componentes de bajo nivel.

**Ejemplo en TypeScript**:

```typescript
// UserRepository.ts
class UserRepository {
    private users: User[] = [];

    async findById(id: number): Promise<User | undefined> {
        return this.users.find(user => user.id === id);
    }

    async save(user: User) {
        const index = this.users.findIndex(u => u.id === user.id);
        if (index === -1) {
            this.users.push(user);
        } else {
            this.users[index] = user;
        }
    }
}
```

**Cumple**: `UserRepository` se encarga de la lógica de almacenamiento sin afectar otras capas.

**No cumple**: Si `UserRepository` contiene lógica de negocio o detalles sobre cómo interactuar con otras capas.

### Relaciones entre capas

- **Entidades** son utilizadas por los **Casos de Uso**. Los casos de uso orquestan la lógica del negocio, usando entidades para realizar operaciones.
- **Casos de Uso** son invocados por las **Interfaces**. Estas últimas representan la entrada del sistema (ej. API REST).
- **Infraestructura** provee la implementación necesaria para que los casos de uso interactúen con sistemas externos (ej. base de datos).

### Ejemplos de incumplimiento

1. **Ejemplo de incumplimiento**: Si el `UserService` incluye lógica de acceso a datos directamente (en lugar de usar `UserRepository`),
2.  se crea una dependencia entre la lógica de negocio y la infraestructura. Esto hace que el código sea más difícil de probar y mantener.

3. **Ejemplo de cumplimiento**: Al mantener las responsabilidades separadas (lógica de negocio en `UserService`,
lógica de acceso a datos en `UserRepository`, y lógica de presentación en `UserController`),
el sistema es más limpio y cada capa puede evolucionar de forma independiente.

### Conclusión

La arquitectura CLEAN es una poderosa guía para estructurar aplicaciones. Al seguir estos principios, 
no solo se mejora la mantenibilidad y escalabilidad del software, sino que también se facilita su prueba y evolución en el tiempo.

*******
La arquitectura CLEAN se relaciona estrechamente con los conceptos de capas de presentación, infraestructura, aplicación y dominio, que
son comunes en muchos enfoques arquitectónicos. A continuación, se explica cómo se interrelacionan estas capas:

### 1. **Capa de Dominio**

**Descripción**: Esta capa es equivalente a la **Capa de Entidades** en CLEAN. Contiene las reglas de negocio y las entidades del sistema. 
Se centra en el modelo del dominio y no debe depender de ninguna otra capa.

**Ejemplo**: En un sistema de gestión de usuarios, la entidad `User` representa el dominio. Contiene la lógica relacionada con los usuarios, 
como validaciones y comportamientos.

### 2. **Capa de Aplicación**

**Descripción**: Equivale a la **Capa de Casos de Uso** en CLEAN. Aquí se definen las operaciones del sistema y 
la lógica que orquesta el uso de las entidades del dominio. Esta capa interactúa con la capa de dominio y maneja las reglas específicas de aplicación.

**Ejemplo**: Un servicio como `UserService` que maneja operaciones como la creación de usuarios, cambio de email, etc.
Esta capa no debe contener detalles de implementación sobre cómo se manejan las entidades en la infraestructura.

### 3. **Capa de Presentación**

**Descripción**: Esta capa se relaciona con la **Capa de Interfaces** en CLEAN. Se encarga de la interacción del usuario con el sistema, 
incluyendo controladores, vistas y cualquier interfaz de usuario. Su responsabilidad es presentar la información al usuario y recoger entradas.

**Ejemplo**: Un `UserController` que maneja las solicitudes HTTP para cambiar el email de un usuario. Esta capa traduce las entradas 
del usuario en invocaciones a los casos de uso.

### 4. **Capa de Infraestructura**

**Descripción**: Corresponde a la Capa de Infraestructura en CLEAN. Esta capa contiene la implementación de detalles técnicos,
como acceso a bases de datos, sistemas de archivos, servicios externos, etc. Su objetivo es proporcionar los recursos necesarios para
que las capas superiores funcionen sin preocuparse por estos detalles.

**Ejemplo**: Un `UserRepository` que se encarga de la persistencia de usuarios, interactuando con una base de datos para guardar y recuperar entidades `User`.

### Relaciones entre las capas

1. **Dominio → Aplicación**: La capa de dominio proporciona las entidades y lógica de negocio a la capa de aplicación. La capa de aplicación
2.  utiliza estas entidades para ejecutar casos de uso específicos.

3. **Aplicación → Presentación**: La capa de aplicación es invocada por la capa de presentación. Cuando un usuario realiza una acción
4. (como enviar un formulario), la presentación llama a un caso de uso en la aplicación para procesar la solicitud.

5. **Aplicación → Infraestructura**: La capa de aplicación utiliza la infraestructura para persistir datos o interactuar con servicios
6.  externos. La infraestructura es responsable de la implementación concreta de estas interacciones.

7. **Presentación → Aplicación**: La capa de presentación no debe contener lógica de negocio ni de acceso a datos.
8.  Su única responsabilidad es interactuar
9.  con la capa de aplicación.

### Ejemplos de incumplimiento

1. **Incumplimiento**: Si la capa de presentación contiene lógica de negocio (por ejemplo, validaciones complejas o reglas de
2.  negocio en el controlador), se rompe la separación de responsabilidades, dificultando la prueba y el mantenimiento.

3. **Cumplimiento**: Mantener la lógica de negocio en la capa de aplicación, permitiendo que la capa de presentación se limite
4. a invocar los casos de uso y
5.  manejar la presentación, resulta en un código más limpio y fácil de mantener.


### Conclusión

Al seguir la separación de estas capas, se facilita la evolución del software, la prueba de componentes individuales y la 
comprensión del sistema en su conjunto. La arquitectura CLEAN, al definir claramente estas capas, ayuda a estructurar el
software de manera que se maximice su mantenibilidad y escalabilidad.

