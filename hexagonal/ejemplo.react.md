Claro, aquí tienes una versión actualizada del ejemplo que incluye puertos y adaptadores:

### 1. Capa de Dominio:
En esta capa, definimos los puertos que serán implementados por los adaptadores en la capa de infraestructura.

```jsx
// domain/ports/ProductRepositoryPort.js
class ProductRepositoryPort {
  getAllProducts() {
    throw new Error('Method not implemented');
  }
}

export default ProductRepositoryPort;
```

### 2. Capa de Aplicación:
Esta capa contiene los casos de uso y depende de los puertos definidos en el dominio.

```jsx
// application/ProductService.js
class ProductService {
  constructor(productRepository) {
    this.productRepository = productRepository;
  }

  async getAllProducts() {
    return this.productRepository.getAllProducts();
  }
}

export default ProductService;
```

### 3. Capa de Infraestructura:
Aquí es donde implementamos los puertos definidos en la capa de dominio utilizando adaptadores concretos.

```jsx
// infrastructure/adapters/ProductRepositoryAdapter.js
import ProductRepositoryPort from '../../domain/ports/ProductRepositoryPort';

class ProductRepositoryAdapter extends ProductRepositoryPort {
  async getAllProducts() {
    // Aquí iría la lógica para obtener los productos de una base de datos o API externa
    return Promise.resolve([
      { id: 1, name: 'Camisa', price: 20 },
      { id: 2, name: 'Pantalón', price: 30 },
      { id: 3, name: 'Zapatos', price: 50 },
    ]);
  }
}

export default ProductRepositoryAdapter;
```

### Componentes React:
En la capa de presentación, utilizamos los servicios de la capa de aplicación a través de los puertos definidos en la capa de dominio.

```jsx
// components/ProductList.js
import React, { useEffect, useState } from 'react';
import ProductService from '../application/ProductService';
import ProductRepositoryAdapter from '../infrastructure/adapters/ProductRepositoryAdapter';

const productRepository = new ProductRepositoryAdapter();
const productService = new ProductService(productRepository);

const ProductList = () => {
  const [products, setProducts] = useState([]);

  useEffect(() => {
    productService.getAllProducts().then((data) => setProducts(data));
  }, []);

  return (
    <div>
      <h1>Lista de Productos</h1>
      <ul>
        {products.map((product) => (
          <li key={product.id}>
            {product.name} - ${product.price}
          </li>
        ))}
      </ul>
    </div>
  );
};

export default ProductList;
```

Con esta estructura, los puertos definidos en la capa de dominio actúan como contratos que deben ser implementados por los adaptadores en la capa de infraestructura. Esto permite la separación efectiva de las preocupaciones y facilita la prueba y el cambio de implementaciones sin afectar otras partes del sistema.
