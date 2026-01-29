# API REST - Plataforma Maybel
## Documentación Completa de Endpoints

**Base URL:** `https://api.maybel.cu/v1`

---

## 🔐 **1. AUTENTICACIÓN**

### 1.1 Login
```http
POST /auth/login
```

**Acceso:** Público (solo para usuarios del Master)

**Request:**
```json
{
  "username": "admin",
  "password": "password123"
}
```

**Response:**
```json
{
  "success": true,
  "data": {
    "user_id": "uuid",
    "username": "admin",
    "full_name": "Juan Pérez",
    "role": "admin",
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
  }
}
```

**Errores:**
- `401`: Credenciales inválidas
- `403`: Usuario desactivado

---

### 1.2 Logout
```http
POST /auth/logout
```

**Headers:** `Authorization: Bearer {token}`

**Response:**
```json
{
  "success": true,
  "message": "Sesión cerrada exitosamente"
}
```

---

### 1.3 Verificar sesión
```http
GET /auth/me
```

**Headers:** `Authorization: Bearer {token}`

**Response:**
```json
{
  "success": true,
  "data": {
    "user_id": "uuid",
    "username": "admin",
    "role": "admin",
    "active": true
  }
}
```

---

## 📂 **2. CATEGORÍAS**

### 2.1 Listar todas las categorías
```http
GET /categories
```

**Acceso:** Master + Catálogo

**Query params:**
- `active=true` (opcional): Filtrar solo activas
- `parent_id=uuid` (opcional): Subcategorías de una categoría

**Response:**
```json
{
  "success": true,
  "data": [
    {
      "id": "uuid",
      "name": "Vehículos",
      "description": "Todo tipo de vehículos",
      "parent_id": null,
      "active": true,
      "created_at": "2024-12-01T10:00:00Z",
      "subcategories": [
        {
          "id": "uuid-2",
          "name": "Bicicletas",
          "parent_id": "uuid",
          "active": true
        }
      ]
    }
  ]
}
```

---

### 2.2 Obtener categoría específica
```http
GET /categories/{id}
```

**Acceso:** Master + Catálogo

**Response:**
```json
{
  "success": true,
  "data": {
    "id": "uuid",
    "name": "Bicicletas",
    "description": "Bicicletas de montaña y urbanas",
    "parent_id": "uuid-parent",
    "active": true,
    "parent": {
      "id": "uuid-parent",
      "name": "Vehículos"
    },
    "product_count": 25
  }
}
```

---

### 2.3 Crear categoría
```http
POST /categories
```

**Acceso:** Solo Master (admin)

**Headers:** `Authorization: Bearer {token}`

**Request:**
```json
{
  "name": "Electrodomésticos",
  "description": "Equipos para el hogar",
  "parent_id": null
}
```

**Response:**
```json
{
  "success": true,
  "data": {
    "id": "uuid-new",
    "name": "Electrodomésticos",
    "description": "Equipos para el hogar",
    "parent_id": null,
    "active": true,
    "created_at": "2024-12-17T15:30:00Z"
  }
}
```

**Validaciones:**
- `name`: Requerido, máximo 100 caracteres
- `parent_id`: Debe existir en la BD si se proporciona

---

### 2.4 Actualizar categoría
```http
PUT /categories/{id}
```

**Acceso:** Solo Master (admin)

**Request:**
```json
{
  "name": "Electrodomésticos Modernos",
  "description": "Equipos de última generación",
  "active": true
}
```

**Response:**
```json
{
  "success": true,
  "data": {
    "id": "uuid",
    "name": "Electrodomésticos Modernos",
    "description": "Equipos de última generación",
    "active": true,
    "updated_at": "2024-12-17T16:00:00Z"
  }
}
```

---

### 2.5 Eliminar categoría (soft delete)
```http
DELETE /categories/{id}
```

**Acceso:** Solo Master (admin)

**Response:**
```json
{
  "success": true,
  "message": "Categoría desactivada exitosamente"
}
```

**Validaciones:**
- No se puede eliminar si tiene productos activos asociados

---

## 🛍️ **3. PRODUCTOS**

### 3.1 Listar productos
```http
GET /products
```

**Acceso:** Master + Catálogo

**Query params:**
- `category_id=uuid` (opcional)
- `active=true` (opcional)
- `search=texto` (opcional): Buscar por nombre
- `page=1` (opcional)
- `limit=20` (opcional)

**Response:**
```json
{
  "success": true,
  "data": [
    {
      "id": "uuid",
      "name": "Bicicleta MTB Aro 26",
      "description": "Bicicleta de montaña resistente",
      "category": {
        "id": "uuid-cat",
        "name": "Bicicletas"
      },
      "prices": {
        "usd": 150.00,
        "cup": 4500.00,
        "eur": 140.00,
        "mlc": 150.00
      },
      "total_stock": 10,
      "variants_count": 3,
      "main_image": "/uploads/products/uuid/1.webp",
      "active": true
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 45,
    "total_pages": 3
  }
}
```

---

### 3.2 Obtener producto detallado
```http
GET /products/{id}
```

**Acceso:** Master + Catálogo

**Response:**
```json
{
  "success": true,
  "data": {
    "id": "uuid",
    "name": "Bicicleta MTB Aro 26",
    "description": "Bicicleta de montaña con 21 velocidades...",
    "category": {
      "id": "uuid-cat",
      "name": "Bicicletas",
      "parent": {
        "id": "uuid-parent",
        "name": "Vehículos"
      }
    },
    "prices": {
      "usd": 150.00,
      "eur": 140.00,
      "cup": 4500.00,
      "mlc": 150.00,
      "mxn": 2550.00,
      "cad": 203.00,
      "brl": 750.00,
      "zelle": 150.00,
      "transfer_cup": 4800.00,
      "card_cup": 5000.00
    },
    "images": [
      {
        "id": "uuid-img",
        "url": "/uploads/products/uuid/1.webp",
        "position": 1,
        "active": true
      }
    ],
    "variants": [
      {
        "id": "uuid-var",
        "color": "Rojo",
        "sku": "MTB-26-RED",
        "stock": 5,
        "active": true
      },
      {
        "id": "uuid-var2",
        "color": "Azul",
        "sku": "MTB-26-BLUE",
        "stock": 3,
        "active": true
      }
    ],
    "total_stock": 8,
    "active": true,
    "created_at": "2024-11-15T10:00:00Z"
  }
}
```

---

### 3.3 Crear producto
```http
POST /products
```

**Acceso:** Solo Master

**Request:**
```json
{
  "name": "Refrigerador LG 18 pies",
  "description": "Refrigerador de 2 puertas con congelador...",
  "category_id": "uuid-cat",
  "prices": {
    "usd": 800.00,
    "eur": 750.00,
    "cup": 24000.00,
    "mlc": 800.00,
    "mxn": 13600.00,
    "cad": 1080.00,
    "brl": 4000.00,
    "zelle": 800.00,
    "transfer_cup": 25600.00,
    "card_cup": 26666.00
  }
}
```

**Response:**
```json
{
  "success": true,
  "data": {
    "id": "uuid-new",
    "name": "Refrigerador LG 18 pies",
    "category_id": "uuid-cat",
    "prices": { /* ... */ },
    "active": true,
    "created_at": "2024-12-17T15:45:00Z"
  }
}
```

**Validaciones:**
- `name`: Requerido, máximo 150 caracteres
- `category_id`: Debe existir
- `prices.usd`: Requerido
- Otras monedas son opcionales

---

### 3.4 Actualizar producto
```http
PUT /products/{id}
```

**Acceso:** Solo Master

**Request:**
```json
{
  "name": "Refrigerador LG 18 pies - Nuevo Modelo",
  "description": "Actualización con nueva tecnología...",
  "prices": {
    "usd": 850.00,
    "cup": 25500.00
  }
}
```

**Response:**
```json
{
  "success": true,
  "data": {
    "id": "uuid",
    "name": "Refrigerador LG 18 pies - Nuevo Modelo",
    "prices": { /* ... */ },
    "updated_at": "2024-12-17T16:00:00Z"
  }
}
```

---

### 3.5 Eliminar producto (soft delete)
```http
DELETE /products/{id}
```

**Acceso:** Solo Master (admin)

**Response:**
```json
{
  "success": true,
  "message": "Producto desactivado exitosamente"
}
```

---

## 🎨 **4. VARIANTES DE PRODUCTOS**

### 4.1 Listar variantes de un producto
```http
GET /products/{product_id}/variants
```

**Acceso:** Master + Catálogo

**Response:**
```json
{
  "success": true,
  "data": [
    {
      "id": "uuid-var",
      "product_id": "uuid-prod",
      "color": "Rojo",
      "sku": "MTB-26-RED",
      "description": "Cuadro rojo brillante",
      "stock": 5,
      "active": true
    }
  ]
}
```

---

### 4.2 Crear variante
```http
POST /products/{product_id}/variants
```

**Acceso:** Solo Master

**Request:**
```json
{
  "color": "Negro",
  "sku": "MTB-26-BLACK",
  "description": "Cuadro negro mate",
  "initial_stock": 10
}
```

**Response:**
```json
{
  "success": true,
  "data": {
    "id": "uuid-new-var",
    "product_id": "uuid-prod",
    "color": "Negro",
    "sku": "MTB-26-BLACK",
    "stock": 10,
    "active": true,
    "created_at": "2024-12-17T15:50:00Z"
  }
}
```

**Lógica:**
1. Crea la variante en `product_variants`
2. Crea el registro de stock en `variant_stock` con `quantity = initial_stock`
3. Registra el movimiento en `inventory_movements` (ENTRADA)

---

### 4.3 Actualizar variante
```http
PUT /variants/{id}
```

**Acceso:** Solo Master

**Request:**
```json
{
  "color": "Negro Mate",
  "description": "Acabado mate premium"
}
```

**Response:**
```json
{
  "success": true,
  "data": {
    "id": "uuid-var",
    "color": "Negro Mate",
    "description": "Acabado mate premium",
    "updated_at": "2024-12-17T16:00:00Z"
  }
}
```

---

### 4.4 Ajustar stock de variante
```http
POST /variants/{id}/stock/adjust
```

**Acceso:** Solo Master

**Request:**
```json
{
  "adjustment": 5,
  "reason": "Recepción de nueva mercancía"
}
```

**Response:**
```json
{
  "success": true,
  "data": {
    "variant_id": "uuid-var",
    "previous_stock": 10,
    "new_stock": 15,
    "adjustment": 5
  }
}
```

**Lógica:**
1. Actualiza `variant_stock.quantity`
2. Registra en `inventory_movements`

---

## 🖼️ **5. IMÁGENES**

### 5.1 Subir imagen
```http
POST /products/{product_id}/images
```

**Acceso:** Solo Master

**Headers:** 
- `Content-Type: multipart/form-data`

**Request (form-data):**
- `file`: archivo de imagen
- `position`: número de orden (1, 2, 3...)

**Response:**
```json
{
  "success": true,
  "data": {
    "id": "uuid-img",
    "product_id": "uuid-prod",
    "file_path": "/uploads/products/uuid-prod/1.webp",
    "position": 1,
    "active": true
  }
}
```

**Lógica:**
1. Master guarda el archivo en `/uploads/products/{product_id}/`
2. Notifica a la API la ruta del archivo
3. API registra en `product_images`

---

### 5.2 Reordenar imágenes
```http
PUT /products/{product_id}/images/reorder
```

**Acceso:** Solo Master

**Request:**
```json
{
  "images": [
    { "id": "uuid-img-3", "position": 1 },
    { "id": "uuid-img-1", "position": 2 },
    { "id": "uuid-img-2", "position": 3 }
  ]
}
```

**Response:**
```json
{
  "success": true,
  "message": "Imágenes reordenadas exitosamente"
}
```

---

### 5.3 Eliminar imagen
```http
DELETE /images/{id}
```

**Acceso:** Solo Master

**Response:**
```json
{
  "success": true,
  "message": "Imagen eliminada exitosamente"
}
```

**Lógica:**
1. Marca como `active = false` en BD
2. El archivo físico se puede eliminar después

---

## 💱 **6. TASAS DE CAMBIO**

### 6.1 Obtener tasas actuales
```http
GET /exchange-rates/current
```

**Acceso:** Master + Catálogo

**Response:**
```json
{
  "success": true,
  "data": {
    "id": "uuid",
    "rates": {
      "usd": 1.000000,
      "eur": 1.080000,
      "cup": 0.033000,
      "mlc": 1.000000,
      "mxn": 0.059000,
      "cad": 0.740000,
      "brl": 0.200000,
      "zelle": 1.000000,
      "transfer_cup": 0.031000,
      "card_cup": 0.030000
    },
    "valid_from": "2024-12-15T10:00:00Z",
    "notes": "Actualización semanal"
  }
}
```

---

### 6.2 Crear nuevas tasas
```http
POST /exchange-rates
```

**Acceso:** Solo Master (admin)

**Request:**
```json
{
  "rates": {
    "eur": 1.10,
    "cup": 0.030,
    "mlc": 1.00,
    "mxn": 0.058,
    "cad": 0.75,
    "brl": 0.19,
    "zelle": 1.00,
    "transfer_cup": 0.029,
    "card_cup": 0.028
  },
  "notes": "Actualización semanal - 22 Diciembre 2024"
}
```

**Response:**
```json
{
  "success": true,
  "data": {
    "id": "uuid-new",
    "rates": { /* ... */ },
    "valid_from": "2024-12-22T10:00:00Z",
    "created_at": "2024-12-22T10:00:00Z"
  }
}
```

**Lógica:**
1. Cierra las tasas anteriores: `UPDATE exchange_rates SET valid_to = NOW() WHERE valid_to IS NULL`
2. Inserta las nuevas tasas

---

### 6.3 Historial de tasas
```http
GET /exchange-rates/history
```

**Acceso:** Master

**Query params:**
- `limit=10` (opcional)

**Response:**
```json
{
  "success": true,
  "data": [
    {
      "id": "uuid-3",
      "rates": { /* ... */ },
      "valid_from": "2024-12-22T10:00:00Z",
      "valid_to": null,
      "notes": "Tasas actuales"
    },
    {
      "id": "uuid-2",
      "rates": { /* ... */ },
      "valid_from": "2024-12-15T10:00:00Z",
      "valid_to": "2024-12-22T10:00:00Z",
      "notes": "Semana anterior"
    }
  ]
}
```

---

## 👥 **7. CLIENTES**

### 7.1 Listar clientes
```http
GET /customers
```

**Acceso:** Solo Master

**Query params:**
- `search=texto` (opcional): Buscar por nombre o teléfono
- `page=1` (opcional)
- `limit=20` (opcional)

**Response:**
```json
{
  "success": true,
  "data": [
    {
      "id": "uuid",
      "name": "Fernando",
      "last_name": "García",
      "phone": "53001234",
      "address": "Calle 23 #456, Centro Habana",
      "total_purchases": 5,
      "total_spent_usd": 1250.00,
      "created_at": "2024-10-01T10:00:00Z"
    }
  ]
}
```

---

### 7.2 Crear cliente
```http
POST /customers
```

**Acceso:** Solo Master

**Request:**
```json
{
  "name": "María",
  "last_name": "López",
  "phone": "53009876",
  "address": "Ave 10 de Octubre #123, Cerro"
}
```

**Response:**
```json
{
  "success": true,
  "data": {
    "id": "uuid-new",
    "name": "María",
    "last_name": "López",
    "phone": "53009876",
    "address": "Ave 10 de Octubre #123, Cerro",
    "created_at": "2024-12-17T16:00:00Z"
  }
}
```

---

### 7.3 Historial de compras de cliente
```http
GET /customers/{id}/purchases
```

**Acceso:** Solo Master

**Response:**
```json
{
  "success": true,
  "data": {
    "customer": {
      "id": "uuid",
      "name": "Fernando García",
      "phone": "53001234"
    },
    "reservations": [
      {
        "id": "uuid-res",
        "product": "Bicicleta MTB",
        "variant": "Rojo",
        "quantity": 2,
        "status": "CONFIRMED",
        "reserved_at": "2024-12-10T10:00:00Z"
      }
    ],
    "sales": [
      {
        "id": "uuid-sale",
        "product": "Bicicleta MTB",
        "variant": "Rojo",
        "quantity": 2,
        "total_usd": 300.00,
        "currency_used": "CUP",
        "sold_at": "2024-12-11T15:00:00Z"
      }
    ],
    "stats": {
      "total_purchases": 3,
      "total_spent_usd": 850.00
    }
  }
}
```

---

## 📦 **8. RESERVAS**

### 8.1 Crear reserva
```http
POST /reservations
```

**Acceso:** Solo Master

**Request:**
```json
{
  "variant_id": "uuid-var",
  "customer_id": "uuid-customer",
  "quantity": 2,
  "expires_at": "2024-12-24T10:00:00Z",
  "notes": "Cliente llamó por WhatsApp"
}
```

**Response:**
```json
{
  "success": true,
  "data": {
    "id": "uuid-new-res",
    "variant_id": "uuid-var",
    "customer": {
      "id": "uuid-customer",
      "name": "Fernando García"
    },
    "product": {
      "name": "Bicicleta MTB",
      "variant": "Rojo"
    },
    "quantity": 2,
    "status": "ACTIVE",
    "reserved_at": "2024-12-17T16:00:00Z",
    "expires_at": "2024-12-24T10:00:00Z"
  }
}
```

**Validaciones:**
- Verificar que hay stock disponible: `variant_stock.quantity - SUM(reservas activas) >= cantidad solicitada`

---

### 8.2 Listar reservas
```http
GET /reservations
```

**Acceso:** Solo Master

**Query params:**
- `status=ACTIVE` (opcional): Filtrar por estado
- `customer_id=uuid` (opcional)

**Response:**
```json
{
  "success": true,
  "data": [
    {
      "id": "uuid-res",
      "product": {
        "name": "Bicicleta MTB",
        "variant": "Rojo"
      },
      "customer": {
        "name": "Fernando García",
        "phone": "53001234"
      },
      "quantity": 2,
      "status": "ACTIVE",
      "reserved_at": "2024-12-17T16:00:00Z",
      "expires_at": "2024-12-24T10:00:00Z"
    }
  ]
}
```

---

### 8.3 Confirmar reserva (convertir en venta)
```http
POST /reservations/{id}/confirm
```

**Acceso:** Solo Master

**Request:**
```json
{
  "currency_used": "CUP",
  "unit_price_currency": 4500.00,
  "notes": "Entrega en 3 días"
}
```

**Response:**
```json
{
  "success": true,
  "data": {
    "reservation_id": "uuid-res",
    "sale_id": "uuid-new-sale",
    "message": "Reserva confirmada y venta registrada"
  }
}
```

**Lógica:**
1. Obtener tasas actuales de cambio
2. Calcular conversión a USD
3. Crear registro en `sales`
4. Actualizar reserva: `status = 'CONFIRMED'`
5. Descontar stock en `variant_stock`
6. Registrar movimiento en `inventory_movements`

---

### 8.4 Cancelar reserva
```http
POST /reservations/{id}/cancel
```

**Acceso:** Solo Master

**Request:**
```json
{
  "reason": "Cliente decidió no comprar"
}
```

**Response:**
```json
{
  "success": true,
  "message": "Reserva cancelada exitosamente"
}
```

**Lógica:**
1. Actualizar reserva: `status = 'CANCELLED'`
2. Stock queda liberado automáticamente (ya que solo se cuentan reservas ACTIVE)

---

## 💰 **9. VENTAS**

### 9.1 Crear venta directa (sin reserva previa)
```http
POST /sales
```

**Acceso:** Solo Master

**Request:**
```json
{
  "variant_id": "uuid-var",
  "customer_id": "uuid-customer",
  "quantity": 1,
  "currency_used": "EUR",
  "unit_price_currency": 140.00,
  "notes": "Venta directa en la tienda"
}
```

**Response:**
```json
{
  "success": true,
  "data": {
    "id": "uuid-new-sale",
    "variant": {
      "product_name": "Bicicleta MTB",
      "color": "Azul"
    },
    "customer": {
      "name": "María López"
    },
    "quantity": 1,
    "currency_used": "EUR",
    "unit_price_currency": 140.00,
    "unit_price_usd": 151.20,
    "total_price_currency": 140.00,
    "total_price_usd": 151.20,
    "exchange_rate_used": 1.08,
    "sold_at": "2024-12-17T16:30:00Z"
  }
}
```

**Lógica:**
1. Obtener tasa de cambio actual para la moneda usada
2. Calcular precios en USD:
   - `unit_price_usd = unit_price_currency × rate`
   - `total_price_usd = unit_price_usd × quantity`
3. Crear registro en `sales`
4. Descontar stock en `variant_stock`
5. Registrar movimiento en `inventory_movements`

---

### 9.2 Listar ventas
```http
GET /sales
```

**Acceso:** Solo Master

**Query params:**
- `start_date=2024-12-01` (opcional)
- `end_date=2024-12-31` (opcional)
- `currency=CUP` (opcional)
- `customer_id=uuid` (opcional)
- `page=1` (opcional)

**Response:**
```json
{
  "success": true,
  "data": [
    {
      "id": "uuid-sale",
      "product": {
        "name": "Bicicleta MTB",
        "variant": "Rojo"
      },
      "customer": {
        "name": "Fernando García"
      },
      "quantity": 2,
      "currency_used": "CUP",
      "total_price_currency": 9000.00,
      "total_price_usd": 297.00,
      "sold_at": "2024-12-17T16:30:00Z",
      "sold_by": "Juan Pérez (vendedor)"
    }
  ],
  "summary": {
    "total_sales": 25,
    "total_usd": 5430.00
  }
}
```

---

### 9.3 Detalle de venta
```http
GET /sales/{id}
```

**Acceso:** Solo Master

**Response:**
```json
{
  "success": true,
  "data": {
    "id": "uuid-sale",
    "reservation_id": "uuid-res",
    "product": {
      "id": "uuid-prod",
      "name": "Bicicleta MTB",
      "variant": {
        "id": "uuid-var",
        "color": "Rojo"
      }
    },
    "customer": {
      "id": "uuid-customer",
      "name": "Fernando",
      "last_name": "García",
      "phone": "53001234",
      "address": "Calle 23 #456"
    },
    "quantity": 2,
    "currency_used": "CUP",
    "unit_price_currency": 4500.00,
    "unit_price_usd": 148.50,
    "total_price_currency": 9000.00,
    "total_price_usd": 297.00,
    "exchange_rate_used": 0.033,
    "sold_at": "2024-12-17T16:30:00Z",
    "sold_by": {
      "id": "uuid-user",
      "username": "vendedor1",
      "full_name": "Juan Pérez"
    },
    "notes": "Entrega programada para el 20/12"
  }
}
```

---

## 📊 **10. ANALÍTICAS**

### 10.1 Dashboard general
```http
GET /analytics/dashboard
```

**Acceso:** Solo Master

**Query params:**
- `start_date=2024-12-01` (opcional)
- `end_date=2024-12-31` (opcional)

**Response:**
```json
{
  "success": true,
  "data": {
    "period": {
      "start": "2024-12-01",
      "end": "2024-12-31"
    },
    "sales_summary": {
      "total_sales": 45,
      "total_usd": 12500.00,
      "average_sale_usd": 277.78
    },
    "by_currency": [
      {
        "currency": "CUP",
        "count": 25,
        "total_currency": 375000.00,
        "total_usd": 11250.00
      },
      {
        "currency": "USD",
        "count": 15,
        "total_currency": 1250.00,
        "total_usd": 1250.00
      }
    ],
    "top_products": [
      {
        "product_id": "uuid",
        "name": "Bicicleta MTB",
        "units_sold": 15,
        "revenue_usd": 2250.00
      }
    ],
    "low_stock_alerts": [
      {
        "product": "Refrigerador LG",
        "variant": "Gris",
        "current_stock": 2
      }
    ],
    "active_reservations": 8
  }
}
```

---

### 10.2 Productos más vendidos
```http
GET /analytics/top-products
```

**Acceso:** Solo Master

**Query params:**
- `limit=10` (opcional)
- `start_date=2024-12-01` (opcional)
- `end_date=2024-12-31` (opcional)

**Response:**
```json
{
  "success": true,
  "data": [
    {
      "product": {
        "id": "uuid",
        "name": "Bicicleta MTB"
      },
      "units_sold": 25,
      "revenue_usd": 3750.00,
      "revenue_by_currency": {
        "CUP": 112500.00,
        "USD": 0.00
      }
    }
  ]
}
```

---

### 10.3 Ventas por categoría
```http
GET /analytics/sales-by-category
```

**Acceso:** Solo Master

**Response:**
```json
{
  "success": true,
  "data": [
    {
      "category": {
        "id": "uuid",
        "name": "Vehículos"
      },
      "total_sales": 35,
      "revenue_usd": 8500.00,
      "percentage": 68.0
    },
    {
      "category": {
        "id": "uuid-2",
        "name": "Electrodomésticos"
      },
      "total_sales": 10,
      "revenue_usd": 4000.00,
      "percentage": 32.0
    }
  ]
}
```

---

### 10.4 Comparación de precios vs ventas
```http
GET /analytics/price-comparison
```

**Acceso:** Solo Master

**Query params:**
- `product_id=uuid` (opcional)

**Response:**
```json
{
  "success": true,
  "data": [
    {
      "product": "Bicicleta MTB",
      "variant": "Rojo",
      "base_price_usd": 150.00,
      "sales": [
        {
          "currency": "CUP",
          "count": 10,
          "avg_price_currency": 4500.00,
          "avg_price_usd": 148.50,
          "difference_usd": -1.50,
          "margin_percentage": -1.0
        },
        {
          "currency": "EUR",
          "count": 5,
          "avg_price_currency": 145.00,
          "avg_price_usd": 156.60,
          "difference_usd": 6.60,
          "margin_percentage": 4.4
        }
      ]
    }
  ]
}
```

---

### 10.5 Stock crítico
```http
GET /analytics/low-stock
```

**Acceso:** Solo Master

**Query params:**
- `threshold=5` (opcional): Cantidad mínima de alerta

**Response:**
```json
{
  "success": true,
  "data": [
    {
      "product": {
        "id": "uuid",
        "name": "Refrigerador LG 18 pies"
      },
      "variant": {
        "id": "uuid-var",
        "color": "Gris"
      },
      "current_stock": 2,
      "reserved_quantity": 1,
      "available_stock": 1,
      "alert_level": "critical"
    }
  ]
}
```

---

## 📝 **RESUMEN DE ENDPOINTS**

### Por módulo:
- **Autenticación:** 3 endpoints
- **Categorías:** 5 endpoints
- **Productos:** 5 endpoints
- **Variantes:** 4 endpoints
- **Imágenes:** 3 endpoints
- **Tasas de cambio:** 3 endpoints
- **Clientes:** 3 endpoints
- **Reservas:** 4 endpoints
- **Ventas:** 3 endpoints
- **Analíticas:** 5 endpoints

**Total:** 38 endpoints principales

---

## 🔒 **SEGURIDAD**

### Autenticación:
- Todos los endpoints del Master requieren token JWT
- El Catálogo solo accede a endpoints de lectura sin autenticación

### Roles:
- **admin:** Acceso completo
- **vendedor:** No puede eliminar categorías/productos ni ver ciertas analíticas

### Rate Limiting:
- Master: 100 requests/minuto
- Catálogo: 500 requests/minuto (lectura intensiva)

---

## 📦 **RESPONSES ESTÁNDAR**

### Éxito:
```json
{
  "success": true,
  "data": { /* ... */ }
}
```

### Error:
```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "El nombre del producto es requerido",
    "details": {
      "field": "name",
      "constraint": "required"
    }
  }
}
```

### Códigos HTTP:
- `200`: OK
- `201`: Created
- `400`: Bad Request (validación)
- `401`: Unauthorized (sin token)
- `403`: Forbidden (sin permisos)
- `404`: Not Found
- `500`: Internal Server Error