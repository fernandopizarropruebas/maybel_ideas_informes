# PROMPT PARA DESARROLLO DE LA API - PLATAFORMA MAYBEL

Necesito que me ayudes a diseñar y desarrollar la **API REST** para la Plataforma Maybel, un sistema de gestión de productos para el mercado cubano.

---

## 📋 CONTEXTO DEL PROYECTO

### ¿Qué es Plataforma Maybel?
Es un ecosistema digital para gestión y promoción de productos físicos (electrodomésticos, muebles, vehículos, etc.) orientado al mercado cubano. El sistema consta de:

1. **API Central (Backend)** - **LO QUE NECESITO QUE DESARROLLES** - Núcleo del sistema
2. **Sistema Master** - Aplicación administrativa que consume la API (cliente)
3. **Catálogo Web** - Sitio público que consume la API (cliente)

### Características del contexto cubano:
- ❌ **NO hay pagos en línea** 
- ✅ Se manejan **10 monedas diferentes** con tasas de cambio variables
- ✅ Sistema de **reservas** antes de ventas finales
- ✅ Ventas por **WhatsApp, Telegram y presencial**
- ✅ **Gestión de inventario** con variantes (colores)
- ✅ **Analíticas avanzadas** para toma de decisiones

---

## 🎯 OBJETIVO DE LA API

### ¿Qué debe hacer la API?

La API es el **único punto de acceso a la base de datos** y debe:

1. ✅ Gestionar **categorías** jerárquicas (categoría → subcategoría)
2. ✅ Gestionar **productos** con precios en 10 monedas
3. ✅ Gestionar **variantes** (colores, versiones) con stock independiente
4. ✅ Gestionar **imágenes** de productos (rutas, no archivos binarios)
5. ✅ Gestionar **tasas de cambio** con historial
6. ✅ Gestionar **clientes** y su historial
7. ✅ Gestionar **reservas** (activas, confirmadas, canceladas)
8. ✅ Registrar **ventas** con conversión automática de monedas
9. ✅ Registrar **movimientos de inventario** para auditoría
10. ✅ Proveer **analíticas** (dashboards, reportes, estadísticas)
11. ✅ **Autenticación JWT** para usuarios del Master
12. ✅ **Separación de endpoints** públicos (catálogo) y privados (master)

---

## 🗄️ MODELO DE DATOS COMPLETO

### Base de datos: PostgreSQL

```sql
-- ============================================
-- CATEGORÍAS
-- ============================================
CREATE TABLE categories (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    name VARCHAR(100) NOT NULL,
    description TEXT,
    parent_id UUID REFERENCES categories(id),
    active BOOLEAN NOT NULL DEFAULT TRUE,
    created_at TIMESTAMP NOT NULL DEFAULT NOW()
);

-- ============================================
-- PRODUCTOS
-- ============================================
CREATE TABLE products (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    name VARCHAR(150) NOT NULL,
    description TEXT,
    category_id UUID NOT NULL REFERENCES categories(id),
    -- Precios en todas las monedas
    price_usd NUMERIC(10,2) NOT NULL,
    price_eur NUMERIC(10,2),
    price_cup NUMERIC(10,2),
    price_mlc NUMERIC(10,2),
    price_mxn NUMERIC(10,2),
    price_cad NUMERIC(10,2),
    price_brl NUMERIC(10,2),
    price_zelle NUMERIC(10,2),
    price_transfer_cup NUMERIC(10,2),
    price_clasic_usd NUMERIC(10,2),
    active BOOLEAN NOT NULL DEFAULT TRUE,
    created_at TIMESTAMP NOT NULL DEFAULT NOW(),
    updated_at TIMESTAMP
);

-- ============================================
-- IMÁGENES
-- ============================================
CREATE TABLE product_images (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    product_id UUID NOT NULL REFERENCES products(id) ON DELETE CASCADE,
    file_path TEXT NOT NULL,
    position INTEGER NOT NULL DEFAULT 1,
    active BOOLEAN NOT NULL DEFAULT TRUE,
    created_at TIMESTAMP NOT NULL DEFAULT NOW()
);

-- ============================================
-- VARIANTES (Colores, versiones)
-- ============================================
CREATE TABLE product_variants (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    product_id UUID NOT NULL REFERENCES products(id) ON DELETE CASCADE,
    color VARCHAR(50) NOT NULL,
    sku VARCHAR(100),
    description TEXT,
    active BOOLEAN NOT NULL DEFAULT TRUE,
    created_at TIMESTAMP NOT NULL DEFAULT NOW()
);

-- ============================================
-- STOCK POR VARIANTE
-- ============================================
CREATE TABLE variant_stock (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    variant_id UUID NOT NULL REFERENCES product_variants(id) ON DELETE CASCADE,
    quantity INTEGER NOT NULL DEFAULT 0,
    updated_at TIMESTAMP NOT NULL DEFAULT NOW(),
    updated_by UUID REFERENCES users(id)
);

-- ============================================
-- TASAS DE CAMBIO
-- ============================================
CREATE TABLE exchange_rates (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    rate_eur NUMERIC(10,6) NOT NULL,
    rate_cup NUMERIC(10,6) NOT NULL,
    rate_mlc NUMERIC(10,6) NOT NULL,
    rate_mxn NUMERIC(10,6) NOT NULL,
    rate_cad NUMERIC(10,6) NOT NULL,
    rate_brl NUMERIC(10,6) NOT NULL,
    rate_zelle NUMERIC(10,6) NOT NULL,
    rate_transfer_cup NUMERIC(10,6) NOT NULL,
    rate_clasic_usd NUMERIC(10,6) NOT NULL,
    valid_from TIMESTAMP NOT NULL DEFAULT NOW(),
    valid_to TIMESTAMP,
    created_by UUID REFERENCES users(id),
    notes TEXT
);

-- ============================================
-- CLIENTES
-- ============================================
CREATE TABLE customers ( 
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(), 
    name VARCHAR(200) NOT NULL, 
    last_name VARCHAR(200) NOT NULL,
    phone VARCHAR(20) NOT NULL, 
    address TEXT, 
    created_at TIMESTAMP NOT NULL DEFAULT NOW(), 
    updated_at TIMESTAMP
);

-- ============================================
-- RESERVAS
-- ============================================
CREATE TABLE reservations (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    variant_id UUID NOT NULL REFERENCES product_variants(id),
    customer_id UUID NOT NULL REFERENCES customers(id),
    quantity INTEGER NOT NULL,
    reserved_at TIMESTAMP NOT NULL DEFAULT NOW(),
    expires_at TIMESTAMP,
    status VARCHAR(30) NOT NULL,
    created_by UUID REFERENCES users(id),
    notes TEXT,
    CHECK (status IN ('ACTIVE', 'CONFIRMED', 'CANCELLED', 'EXPIRED'))
);

-- ============================================
-- VENTAS
-- ============================================
CREATE TABLE sales (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    variant_id UUID NOT NULL REFERENCES product_variants(id),
    customer_id UUID NOT NULL REFERENCES customers(id),
    reservation_id UUID REFERENCES reservations(id),
    quantity INTEGER NOT NULL,
    currency_used VARCHAR(10) NOT NULL,
    unit_price_currency NUMERIC(10,2) NOT NULL,
    unit_price_usd NUMERIC(10,2) NOT NULL,
    total_price_currency NUMERIC(10,2) NOT NULL,
    total_price_usd NUMERIC(10,2) NOT NULL,
    exchange_rate_used NUMERIC(10,6) NOT NULL,
    sold_at TIMESTAMP NOT NULL DEFAULT NOW(),
    sold_by UUID REFERENCES users(id),
    notes TEXT,
    CHECK (currency_used IN ('USD', 'EUR', 'CUP', 'MLC', 'MXN', 'CAD', 'BRL', 'ZELLE', 'TRANSFER_CUP', 'CARD_CUP'))
);

-- ============================================
-- MOVIMIENTOS DE INVENTARIO
-- ============================================
CREATE TABLE inventory_movements (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    variant_id UUID NOT NULL REFERENCES product_variants(id),
    movement_type VARCHAR(20) NOT NULL,
    quantity INTEGER NOT NULL,
    reason TEXT,
    created_at TIMESTAMP NOT NULL DEFAULT NOW(),
    created_by UUID REFERENCES users(id),
    CHECK (movement_type IN ('ENTRADA', 'SALIDA', 'AJUSTE', 'DEVOLUCION', 'PERDIDA'))
);

-- ============================================
-- USUARIOS
-- ============================================
CREATE TABLE users (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    username VARCHAR(50) UNIQUE NOT NULL,
    full_name VARCHAR(150),
    password_hash VARCHAR(255) NOT NULL,
    role VARCHAR(30) NOT NULL,
    active BOOLEAN NOT NULL DEFAULT TRUE,
    created_at TIMESTAMP NOT NULL DEFAULT NOW(),
    last_login TIMESTAMP,
    CHECK (role IN ('admin', 'vendedor'))
);

-- ============================================
-- ÍNDICES
-- ============================================
CREATE INDEX idx_products_category ON products(category_id);
CREATE INDEX idx_products_active ON products(active);
CREATE INDEX idx_variants_product ON product_variants(product_id);
CREATE INDEX idx_stock_variant ON variant_stock(variant_id);
CREATE INDEX idx_exchange_rates_current ON exchange_rates(valid_from) WHERE valid_to IS NULL;
CREATE INDEX idx_reservations_customer ON reservations(customer_id);
CREATE INDEX idx_reservations_variant ON reservations(variant_id);
CREATE INDEX idx_reservations_status ON reservations(status);
CREATE INDEX idx_sales_customer ON sales(customer_id);
CREATE INDEX idx_sales_variant ON sales(variant_id);
CREATE INDEX idx_sales_date ON sales(sold_at);
CREATE INDEX idx_sales_currency ON sales(currency_used);
CREATE INDEX idx_inventory_variant ON inventory_movements(variant_id);
CREATE INDEX idx_customers_phone ON customers(phone);
```

---

## 🔌 ENDPOINTS COMPLETOS (38 endpoints)

### **Base URL:** `/api/v1`

### **1. AUTENTICACIÓN (3 endpoints)**

#### 1.1 Login
```
POST /auth/login
Acceso: Público
Body: { "username": "admin", "password": "pass123" }
Response: { "token": "jwt_token", "user": {...} }
```

#### 1.2 Logout
```
POST /auth/logout
Acceso: Autenticado
Headers: Authorization: Bearer {token}
```

#### 1.3 Verificar sesión
```
GET /auth/me
Acceso: Autenticado
Response: Datos del usuario actual
```

---

### **2. CATEGORÍAS (5 endpoints)**

#### 2.1 Listar categorías
```
GET /categories
Acceso: Público (Catálogo) + Master
Query: ?active=true&parent_id=uuid
Response: Lista de categorías con subcategorías
```

#### 2.2 Obtener categoría
```
GET /categories/{id}
Acceso: Público + Master
Response: Detalle de categoría con productos count
```

#### 2.3 Crear categoría
```
POST /categories
Acceso: Solo Master (admin)
Body: { "name": "Vehículos", "description": "...", "parent_id": null }
```

#### 2.4 Actualizar categoría
```
PUT /categories/{id}
Acceso: Solo Master (admin)
Body: { "name": "...", "active": true }
```

#### 2.5 Eliminar categoría (soft delete)
```
DELETE /categories/{id}
Acceso: Solo Master (admin)
Validación: No puede tener productos activos
```

---

### **3. PRODUCTOS (5 endpoints)**

#### 3.1 Listar productos
```
GET /products
Acceso: Público + Master
Query: ?category_id=uuid&active=true&search=texto&page=1&limit=20
Response: Lista paginada con: {data: [...], pagination: {...}}
Incluye: nombre, precios (USD, CUP, EUR mínimo), stock total, imagen principal
```

#### 3.2 Detalle de producto
```
GET /products/{id}
Acceso: Público + Master
Response: Producto completo con:
  - Todos los precios
  - Todas las imágenes
  - Todas las variantes con stock
  - Categoría y jerarquía completa
```

#### 3.3 Crear producto
```
POST /products
Acceso: Solo Master
Body: {
  "name": "Bicicleta MTB",
  "description": "...",
  "category_id": "uuid",
  "prices": {
    "usd": 150.00,
    "cup": 4500.00,
    "eur": 140.00,
    ...
  }
}
Validaciones:
  - name: requerido, máx 150 chars
  - category_id: debe existir
  - price_usd: requerido, > 0
```

#### 3.4 Actualizar producto
```
PUT /products/{id}
Acceso: Solo Master
Body: Campos a actualizar
```

#### 3.5 Eliminar producto
```
DELETE /products/{id}
Acceso: Solo Master (admin)
Soft delete: active = false
```

---

### **4. VARIANTES (4 endpoints)**

#### 4.1 Listar variantes de producto
```
GET /products/{product_id}/variants
Acceso: Público + Master
Response: Array de variantes con stock
```

#### 4.2 Crear variante
```
POST /products/{product_id}/variants
Acceso: Solo Master
Body: {
  "color": "Rojo",
  "sku": "MTB-26-RED",
  "description": "Cuadro rojo brillante",
  "initial_stock": 10
}
Lógica:
  1. Crear en product_variants
  2. Crear registro en variant_stock con quantity = initial_stock
  3. Registrar en inventory_movements (ENTRADA)
```

#### 4.3 Actualizar variante
```
PUT /variants/{id}
Acceso: Solo Master
Body: { "color": "Rojo Mate", "description": "..." }
```

#### 4.4 Ajustar stock
```
POST /variants/{id}/stock/adjust
Acceso: Solo Master
Body: { "adjustment": 5, "reason": "Nueva mercancía" }
Lógica:
  1. Actualizar variant_stock.quantity
  2. Registrar en inventory_movements
```

---

### **5. IMÁGENES (3 endpoints)**

#### 5.1 Registrar imagen
```
POST /products/{product_id}/images
Acceso: Solo Master
Body: { "file_path": "/uploads/products/uuid/1.webp", "position": 1 }
Nota: El archivo YA fue subido por el Master al file storage
La API solo registra la ruta en la BD
```

#### 5.2 Reordenar imágenes
```
PUT /products/{product_id}/images/reorder
Acceso: Solo Master
Body: {
  "images": [
    { "id": "uuid-1", "position": 1 },
    { "id": "uuid-2", "position": 2 }
  ]
}
```

#### 5.3 Eliminar imagen
```
DELETE /images/{id}
Acceso: Solo Master
Soft delete: active = false
```

---

### **6. TASAS DE CAMBIO (3 endpoints)**

#### 6.1 Obtener tasas actuales
```
GET /exchange-rates/current
Acceso: Público + Master
Response: {
  "rates": {
    "usd": 1.0,
    "eur": 1.08,
    "cup": 0.033,
    ...
  },
  "valid_from": "2024-12-17T10:00:00Z"
}
```

#### 6.2 Crear nuevas tasas
```
POST /exchange-rates
Acceso: Solo Master (admin)
Body: {
  "rates": {
    "eur": 1.10,
    "cup": 0.030,
    ...
  },
  "notes": "Actualización semanal"
}
Lógica:
  1. Cerrar tasas anteriores: UPDATE ... SET valid_to = NOW() WHERE valid_to IS NULL
  2. Insertar nuevas tasas
```

#### 6.3 Historial de tasas
```
GET /exchange-rates/history
Acceso: Solo Master
Query: ?limit=10
Response: Array de tasas históricas
```

---

### **7. CLIENTES (3 endpoints)**

#### 7.1 Listar clientes
```
GET /customers
Acceso: Solo Master
Query: ?search=texto&page=1&limit=20
Response: Clientes con stats (total_purchases, total_spent_usd)
```

#### 7.2 Crear cliente
```
POST /customers
Acceso: Solo Master
Body: {
  "name": "Fernando",
  "last_name": "García",
  "phone": "53001234",
  "address": "Calle 23 #456"
}
```

#### 7.3 Historial de cliente
```
GET /customers/{id}/purchases
Acceso: Solo Master
Response: {
  "customer": {...},
  "reservations": [...],
  "sales": [...],
  "stats": {...}
}
```

---

### **8. RESERVAS (4 endpoints)**

#### 8.1 Crear reserva
```
POST /reservations
Acceso: Solo Master
Body: {
  "variant_id": "uuid",
  "customer_id": "uuid",
  "quantity": 2,
  "expires_at": "2024-12-24T00:00:00Z",
  "notes": "Cliente llamó por WhatsApp"
}
Validaciones:
  - Verificar stock disponible: 
    variant_stock.quantity - SUM(reservas ACTIVE) >= quantity
```

#### 8.2 Listar reservas
```
GET /reservations
Acceso: Solo Master
Query: ?status=ACTIVE&customer_id=uuid
Response: Lista de reservas con detalles de producto y cliente
```

#### 8.3 Confirmar reserva (convertir en venta)
```
POST /reservations/{id}/confirm
Acceso: Solo Master
Body: {
  "currency_used": "CUP",
  "unit_price_currency": 4500.00,
  "notes": "Entrega en 3 días"
}
Lógica completa:
  1. Obtener tasas actuales
  2. Calcular conversión a USD:
     - rate = tasas.rate_{currency}
     - unit_price_usd = unit_price_currency × rate
     - total_price_usd = unit_price_usd × quantity
  3. Crear registro en sales con todos los datos
  4. Actualizar reserva: status = 'CONFIRMED'
  5. Descontar stock: variant_stock.quantity -= quantity
  6. Registrar en inventory_movements (SALIDA)
```

#### 8.4 Cancelar reserva
```
POST /reservations/{id}/cancel
Acceso: Solo Master
Body: { "reason": "Cliente desistió" }
Lógica:
  - Actualizar status = 'CANCELLED'
  - El stock se libera automáticamente (solo se cuentan ACTIVE)
```

---

### **9. VENTAS (3 endpoints)**

#### 9.1 Crear venta directa
```
POST /sales
Acceso: Solo Master
Body: {
  "variant_id": "uuid",
  "customer_id": "uuid",
  "quantity": 1,
  "currency_used": "EUR",
  "unit_price_currency": 140.00,
  "notes": "Venta en tienda"
}
Lógica IDÉNTICA a confirmar reserva:
  1. Obtener tasas actuales
  2. Calcular conversión a USD
  3. Crear en sales
  4. Descontar stock
  5. Registrar movimiento
```

#### 9.2 Listar ventas
```
GET /sales
Acceso: Solo Master
Query: ?start_date=2024-12-01&end_date=2024-12-31&currency=CUP&page=1
Response: {
  "data": [...],
  "summary": {
    "total_sales": 25,
    "total_usd": 5430.00
  }
}
```

#### 9.3 Detalle de venta
```
GET /sales/{id}
Acceso: Solo Master
Response: Venta completa con producto, cliente, vendedor, etc.
```

---

### **10. ANALÍTICAS (5 endpoints)**

#### 10.1 Dashboard general
```
GET /analytics/dashboard
Acceso: Solo Master
Query: ?start_date=2024-12-01&end_date=2024-12-31
Response: {
  "sales_summary": {
    "total_sales": 45,
    "total_usd": 12500.00,
    "average_sale_usd": 277.78
  },
  "by_currency": [...],
  "top_products": [...],
  "low_stock_alerts": [...],
  "active_reservations": 8
}
```

#### 10.2 Productos más vendidos
```
GET /analytics/top-products
Acceso: Solo Master
Query: ?limit=10&start_date=...&end_date=...
Response: Productos ordenados por unidades vendidas y revenue
```

#### 10.3 Ventas por categoría
```
GET /analytics/sales-by-category
Acceso: Solo Master
Response: Categorías con total de ventas y porcentajes
```

#### 10.4 Comparación de precios
```
GET /analytics/price-comparison
Acceso: Solo Master
Query: ?product_id=uuid
Response: Comparación entre precio base y precios reales de venta por moneda
Útil para ver márgenes de ganancia/pérdida
```

#### 10.5 Stock crítico
```
GET /analytics/low-stock
Acceso: Solo Master
Query: ?threshold=5
Response: Variantes con stock bajo
Cálculo: stock_disponible = quantity - SUM(reservas ACTIVE)
```

---

## 🔐 AUTENTICACIÓN Y AUTORIZACIÓN

### Sistema JWT

**Login:**
1. Usuario envía username + password
2. API verifica en BD (password_hash con bcrypt)
3. Si es válido, genera JWT token con payload:
```json
{
  "user_id": "uuid",
  "username": "admin",
  "role": "admin",
  "iat": 1234567890,
  "exp": 1234654290
}
```
4. Devuelve token al cliente
5. Actualiza `users.last_login`

**Endpoints protegidos:**
- Middleware que verifica token en header `Authorization: Bearer {token}`
- Decodifica JWT y extrae user_id y role
- Verifica que el usuario sigue activo

**Roles:**
- `admin`: Acceso total
- `vendedor`: No puede eliminar categorías/productos, ni crear tasas de cambio

---

## 🛡️ VALIDACIONES IMPORTANTES

### Al crear producto:
```javascript
validations = {
  name: { required: true, maxLength: 150 },
  category_id: { required: true, exists: 'categories' },
  price_usd: { required: true, min: 0.01 },
  // Otras monedas son opcionales
}
```

### Al crear reserva:
```javascript
// 1. Verificar que variant existe y está activo
// 2. Calcular stock disponible:
const availableStock = await db.query(`
  SELECT vs.quantity - COALESCE(SUM(r.quantity), 0) as available
  FROM variant_stock vs
  LEFT JOIN reservations r ON r.variant_id = vs.variant_id AND r.status = 'ACTIVE'
  WHERE vs.variant_id = $1
  GROUP BY vs.quantity
`, [variant_id]);

if (availableStock < requested_quantity) {
  throw new Error('Stock insuficiente');
}
```

### Al crear/confirmar venta:
```javascript
// 1. Obtener tasas actuales
const rates = await db.query(`
  SELECT * FROM exchange_rates WHERE valid_to IS NULL
`);

// 2. Obtener tasa de la moneda usada
const rate = rates[`rate_${currency_used.toLowerCase()}`];

// 3. Calcular precios
const unit_price_usd = unit_price_currency * rate;
const total_price_usd = unit_price_usd * quantity;

// 4. Crear venta con todos los datos
await db.query(`
  INSERT INTO sales (
    variant_id, customer_id, quantity,
    currency_used, unit_price_currency, unit_price_usd,
    total_price_currency, total_price_usd, exchange_rate_used,
    sold_by
  ) VALUES (...)
`);

// 5. Descontar stock
await db.query(`
  UPDATE variant_stock 
  SET quantity = quantity - $1 
  WHERE variant_id = $2
`, [quantity, variant_id]);

// 6. Registrar movimiento
await db.query(`
  INSERT INTO inventory_movements (
    variant_id, movement_type, quantity, reason, created_by
  ) VALUES ($1, 'SALIDA', -$2, 'Venta confirmada', $3)
`, [variant_id, quantity, user_id]);
```

---

## 📊 RESPONSES ESTÁNDAR

### Éxito:
```json
{
  "success": true,
  "data": { /* ... */ }
}
```

### Error de validación:
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

### Error de negocio:
```json
{
  "success": false,
  "error": {
    "code": "INSUFFICIENT_STOCK",
    "message": "Stock insuficiente para esta reserva",
    "details": {
      "requested": 5,
      "available": 2
    }
  }
}
```

### Códigos HTTP:
- `200 OK`: Éxito en GET, PUT
- `201 Created`: Éxito en POST
- `204 No Content`: Éxito en DELETE
- `400 Bad Request`: Error de validación
- `401 Unauthorized`: Sin token o token inválido
- `403 Forbidden`: Sin permisos
- `404 Not Found`: Recurso no existe
- `409 Conflict`: Conflicto de negocio (ej: stock insuficiente)
- `500 Internal Server Error`: Error del servidor

---

## 🚀 STACK TECNOLÓGICO SUGERIDO

Mi preferencia es:
- **Lenguaje:** [Python / Node.js / Go - especifica]
- **Framework:** 
  - Python: FastAPI o Django REST Framework
  - Node.js: Express.js + TypeScript
  - Go: Gin
- **ORM/Query Builder:**
  - Python: SQLAlchemy o Django ORM
  - Node.js: Prisma o TypeORM
  - Go: GORM
- **Base de datos:** PostgreSQL (ya definido)
- **Autenticación:** JWT (PyJWT / jsonwebtoken / jwt-go)
- **Validación:** Pydantic / Joi / go-playground/validator
- **Documentación:** OpenAPI/Swagger automático

**Si prefieres otro stack, especifícalo.**

---

## 📁 ESTRUCTURA DEL PROYECTO ESPERADA

### Ejemplo para FastAPI (Python):
```
api/
├── main.py                 # Entry point
├── config.py              # Configuración (DB, JWT secret, etc.)
├── database.py            # Conexión a PostgreSQL
├── requirements.txt       # Dependencias
│
├── models/                # Modelos SQLAlchemy
│   ├── __init__.py
│   ├── category.py
│   ├── product.py
│   ├── variant.py
│   ├── customer.py
│   ├── reservation.py
│   ├── sale.py
│   ├── user.py
│   └── exchange_rate.py
│
├── schemas/               # Pydantic schemas (validación)
│   ├── __init__.py
│   ├── category.py
│   ├── product.py
│   ├── sale.py
│   └── ...
│
├── routers/               # Endpoints organizados
│   ├── __init__.py
│   ├── auth.py           # POST /auth/login, /auth/logout
│   ├── categories.py     # CRUD categorías
│   ├── products.py       # CRUD productos
│   ├── variants.py       # CRUD variantes
│   ├── customers.py      # CRUD clientes
│   ├── reservations.py   # Gestión reservas
│   ├── sales.py          # Gestión ventas
│   ├── analytics.py      # Endpoints de analíticas
│   └── exchange_rates.py # Tasas de cambio
│
├── services/              # Lógica de negocio
│   ├── __init__.py
│   ├── auth_service.py
│   ├── product_service.py
│   ├── sale_service.py   # Lógica de conversión de monedas
│   └── stock_service.py  # Cálculos de stock disponible
│
├── middleware/
│   ├── __init__.py
│   └── auth.py           # Verificación JWT
│
└── utils/
    ├── __init__.py
    ├── security.py       # Hash passwords, generar JWT
    └── exceptions.py     # Excepciones custom
```

---

## 🔍 LÓGICA DE NEGOCIO CRÍTICA

### 1. Cálculo de stock disponible

**Siempre que se consulte stock:**
```sql
-- Stock disponible de una variante
SELECT 
  vs.quantity - COALESCE(SUM(r.quantity), 0) as available_stock
FROM variant_stock vs
LEFT JOIN reservations r 
  ON r.variant_id = vs.variant_id 
  AND r.status = 'ACTIVE'
WHERE vs.variant_id = $1
GROUP BY vs.quantity;
```

### 2. Conversión de monedas en ventas

**Función reutilizable:**
```python
async def convert_to_usd(amount: float, currency: str) -> dict:
    # Obtener tasas actuales
    rates = await get_current_exchange_rates()
    
    # Obtener tasa específica
    rate_field = f"rate_{currency.lower()}"
    rate = getattr(rates, rate_field)
    
    # Calcular
    amount_usd = amount * rate
    
    return {
        "amount_currency": amount,
        "currency": currency,
        "amount_usd": amount_usd,
        "rate_used": rate
    }
```

### 3. Registro de movimientos de inventario

**Cada vez que cambia stock:**
```python
async def register_inventory_movement(
    variant_id: str,
    movement_type: str,
    quantity: int,
    reason: str,
    user_id: str
):
    await db.execute(
        """
        INSERT INTO inventory_movements (
            variant_id, movement_type, quantity, reason, created_by
        ) VALUES ($1, $2, $3, $4, $5)
        """,
        variant_id, movement_type, quantity, reason, user_id
    )
```

**Llamar después de:**
- Crear variante (ENTRADA)
- Ajustar stock (AJUSTE)
- Confirmar venta (SALIDA)
- Cancelar venta (DEVOLUCION)

---

## 🧪 CASOS DE PRUEBA IMPORTANTES

### Test: Crear reserva con stock insuficiente
```python
# Setup: Variante con 3 unidades disponibles
# Acción: Intentar reservar 5 unidades
# Resultado esperado: Error 409 "Stock insuficiente"
```

### Test: Confirmar reserva y verificar stock
```python
# Setup: Variante con 10 unidades, reserva de 3
# Acción: Confirmar reserva
# Verificaciones:
#   - Se creó venta
#   - Reserva status = 'CONFIRMED'
#   - Stock = 7 (10 - 3)
#   - Se registró movimiento SALIDA
```

### Test: Conversión de monedas en venta
```python
# Setup: Tasas: 1 CUP = 0.033 USD
# Acción: Vender 2 unidades a 4500 CUP c/u
# Verificaciones:
#   - unit_price_usd = 148.50
#   - total_price_usd = 297.00
#   - exchange_rate_used = 0.033
```

### Test: Cancelar reserva libera stock
```python
# Setup: Variante con 10 unidades, reserva activa de 3
# Stock disponible = 7
# Acción: Cancelar reserva
# Verificación: Stock disponible = 10
```

---

## 📖 DOCUMENTACIÓN API

### Genera automáticamente:
- **OpenAPI/Swagger UI** en `/docs`
- **ReDoc** en `/redoc`

Con:
- Todos los endpoints documentados
- Schemas de request/response
- Ejemplos de uso
- Códigos de error

---

## 🔒 SEGURIDAD

### 1. Hash de contraseñas
```python
import bcrypt

def hash_password(password: str) -> str:
    return bcrypt.hashpw(password.encode('utf-8'), bcrypt.gensalt()).decode('utf-8')

def verify_password(password: str, hashed: str) -> bool:
    return bcrypt.checkpw(password.encode('utf-8'), hashed.encode('utf-8'))
```

### 2. JWT Token
```python
import jwt
from datetime import datetime, timedelta

def create_token(user_id: str, username: str, role: str) -> str:
    payload = {
        "user_id": user_id,
        "username": username,
        "role": role,
        "iat": datetime.utcnow(),
        "exp": datetime.utcnow() + timedelta(hours=24)
    }
    return jwt.encode(payload, SECRET_KEY, algorithm="HS256")

def decode_token(token: str) -> dict:
    return jwt.decode(token, SECRET_KEY, algorithms=["HS256"])
```

### 3. CORS
- Permitir solo dominios específicos:
  - `https://maybel.cu` (catálogo)
  - `https://admin.maybel.cu` (master)
  - `http://localhost:3000` (desarrollo)

### 4. Rate Limiting
- Master: 100 req/min
- Catálogo: 500 req/min
- Auth: 10 req/min (evitar brute force)

---

## ⚡ OPTIMIZACIONES

### 1. Caché
- Cachear `GET /categories` (cambian poco)
- Cachear `GET /exchange-rates/current` (5-10 min)
- Usar Redis para cache

### 2. Queries optimizadas
- Usar JOINs en vez de N+1 queries
- Seleccionar solo campos necesarios
- Usar índices (ya definidos en modelo)

### 3. Paginación
- Siempre paginar listas largas
- Límite máximo: 100 items por página
- Default: 20 items

---

## 🎯 ENTREGABLES ESPERADOS

Por favor, genera:

1. **Estructura completa del proyecto** (carpetas y archivos)
2. **Modelos de base de datos** (SQLAlchemy/Prisma/GORM)
3. **Todos los endpoints** implementados
4. **Schemas de validación** (Pydantic/Joi/validator)
5. **Lógica de negocio** (conversión monedas, stock, etc.)
6. **Middleware de autenticación** JWT
7. **Manejo de errores** centralizado
8. **Archivo de configuración** (.env.example)
9. **Archivo README.md** con instrucciones
10. **Script de migraciones** para crear tablas

---

## 🚦 PRIORIDADES

### Fase 1 (MVP):
1. ✅ Autenticación
2. ✅ CRUD Categorías
3. ✅ CRUD Productos
4. ✅ CRUD Variantes
5. ✅ Tasas de cambio
6. ✅ Endpoints públicos para catálogo

### Fase 2:
7. ✅ Clientes
8. ✅ Reservas
9. ✅ Ventas
10. ✅ Analíticas básicas

### Fase 3:
11. ✅ Analíticas avanzadas
12. ✅ Optimizaciones
13. ✅ Tests

---

## 💾 DATOS INICIALES (SEED)

### Usuario admin por defecto:
```sql
INSERT INTO users (username, full_name, password_hash, role)
VALUES ('admin', 'Administrador', '{hash_de_admin123}', 'admin');
```

### Tasas iniciales:
```sql
INSERT INTO exchange_rates (
    rate_eur, rate_cup, rate_mlc, rate_mxn, rate_cad, 
    rate_brl, rate_zelle, rate_transfer_cup, rate_card_cup
) VALUES (
    1.08, 0.033, 1.00, 0.059, 0.74, 
    0.20, 1.00, 0.031, 0.030
);
```

---

## ❓ ACLARACIONES FINALES

### ¿Los archivos de imágenes se suben a la API?
**NO**. El Master sube los archivos directamente al file storage del servidor (`/uploads/`). La API solo registra la ruta en la base de datos.

### ¿La API maneja paginación?
**SÍ**. Todos los listados devuelven:
```json
{
  "data": [...],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 100,
    "total_pages": 5
  }
}
```

### ¿Cómo se manejan las transacciones?
Usa **transacciones de base de datos** para operaciones críticas:
- Confirmar reserva (crear venta + actualizar reserva + descontar stock + registrar movimiento)
- Crear variante (crear variante + crear stock + registrar movimiento)

Si falla algún paso, hacer ROLLBACK de todo.

---

## 🚀 COMENZAR DESARROLLO

Si todo está claro, **procede a generar el código completo de la API** siguiendo todas las especificaciones anteriores.

Incluye:
1. Código funcional y completo
2. Comentarios explicativos en partes críticas
3. Manejo de errores robusto
4. Validaciones exhaustivas
5. README con setup instructions

¡Muchas gracias!