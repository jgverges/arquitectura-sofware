Ejemplo de cómo podría ser la estructura de archivos de un proyecto React utilizando la arquitectura hexagonal:

```
src/
│
├── adapters/                   # Adaptadores
│   ├── api/                    # Adaptadores para comunicarse con APIs externas
│   │   └── ProductAPI.js       # Adaptador para interactuar con la API de productos
│   ├── database/               # Adaptadores para interactuar con la base de datos
│   │   └── ProductDatabase.js  # Adaptador para interactuar con la base de datos de productos
│   └── mock/                   # Adaptadores de prueba con datos falsos
│       └── MockProductRepository.js
│
├── components/                 # Componentes de React
│   ├── ProductList.js          # Componente de lista de productos
│   └── ...
│
├── core/                       # Lógica de negocio (puertos)
│   ├── entities/               # Definición de entidades del dominio
│   │   └── Product.js          # Entidad Producto
│   ├── repositories/           # Interfaces para acceder a los datos (puertos)
│   │   └── ProductRepository.js  # Interfaz para operaciones CRUD de productos
│   └── usecases/               # Casos de uso de la aplicación
│       └── GetAllProducts.js   # Caso de uso para obtener todos los productos
│
└── index.js                    # Punto de entrada de la aplicación
```

En esta estructura:

- **Adaptadores (adapters)**: La carpeta `adapters` contiene adaptadores que se encargan de interactuar con recursos externos, como una API REST (`api`), una base de datos (`database`) o datos falsos para pruebas (`mock`).
- **Componentes (components)**: Aquí se encuentran todos los componentes de React, como `ProductList.js`, que utiliza los casos de uso y los datos proporcionados por los adaptadores.
- **Núcleo (core)**: Esta carpeta contiene la lógica de negocio de la aplicación, incluyendo las entidades del dominio en `entities`, las interfaces para acceder a los datos en `repositories` (los puertos) y los casos de uso en `usecases`.

Esta estructura separa claramente la lógica de negocio de los detalles de implementación, lo que facilita el mantenimiento, la prueba y la escalabilidad del proyecto. Además, permite intercambiar fácilmente los adaptadores concretos según sea necesario sin afectar al resto de la aplicación.
