# PROMPT PARA DESARROLLO DE LA APLICACIÓN MASTER - PLATAFORMA MAYBEL

Necesito que me ayudes a diseñar y desarrollar la **Aplicación Master** para la Plataforma Maybel, el sistema administrativo completo para gestión de catálogo, inventario, ventas y análisis.

---

## 📋 CONTEXTO DEL PROYECTO

### ¿Qué es Plataforma Maybel?
Es un ecosistema digital para gestión y promoción de productos físicos (electrodomésticos, muebles, vehículos, etc.) orientado al mercado cubano. El sistema consta de:

1. **API Central (Backend)** - YA ESTÁ IMPLEMENTADA ✅
2. **Sistema Master** - **LO QUE NECESITO QUE DESARROLLES** - Aplicación administrativa
3. **Catálogo Web** - Sitio público (separado)

### Características del contexto cubano:
- ❌ **NO hay pagos en línea**
- ✅ Se manejan **10 monedas diferentes** con tasas de cambio variables
- ✅ Sistema de **reservas** antes de ventas finales
- ✅ Ventas por **WhatsApp, Telegram y presencial**
- ✅ **Gestión completa de inventario** con variantes (colores)
- ✅ **Analíticas avanzadas** para toma de decisiones

---

## 🎯 OBJETIVO DE LA APLICACIÓN MASTER

### ¿Qué ES el Master?

El Master es la **aplicación administrativa completa** que permite:

1. ✅ **Gestionar catálogo completo:**
   - Crear, editar y eliminar categorías
   - Crear, editar y eliminar productos
   - Subir y organizar imágenes
   - Gestionar variantes (colores) y stock

2. ✅ **Gestionar operaciones comerciales:**
   - Registrar clientes
   - Crear reservas
   - Confirmar ventas desde reservas
   - Registrar ventas directas
   - Ajustar inventario

3. ✅ **Gestionar tasas de cambio:**
   - Actualizar tasas semanalmente
   - Ver historial de tasas

4. ✅ **Visualizar y analizar datos:**
   - Dashboard con KPIs principales
   - Reportes de ventas
   - Productos más vendidos
   - Stock crítico
   - Comparación de precios y márgenes

5. ✅ **Control de acceso:**
   - Login/logout
   - Roles: admin y vendedor
   - Permisos diferenciados

### ¿Qué NO es el Master?
- ❌ NO es de acceso público (solo usuarios autorizados)
- ❌ NO es un punto de venta (POS) físico
- ❌ NO procesa pagos

---

## 🏗️ ARQUITECTURA TÉCNICA

### Backend (YA EXISTE)
- **Base URL de la API:** `https://api.maybel.cu/v1` (o tu URL)
- El Master **CONSUME** la API mediante llamadas HTTP
- **Requiere autenticación JWT** para todos los endpoints

### Endpoints disponibles (RESUMEN):

```
AUTENTICACIÓN:
POST /auth/login
POST /auth/logout
GET /auth/me

CATEGORÍAS:
GET /categories                          (Lista con query param parent_id para jerarquía)
GET /categories/{id}                     (Obtener una categoría por ID)
POST /categories
PUT /categories/{id}
DELETE /categories/{id}

💡 NOTA IMPORTANTE SOBRE CATEGORÍAS:
   - Usar parent_id=null para obtener categorías principales
   - Usar parent_id={id} para obtener subcategorías de una categoría
   - Los productos se asignan a subcategorías (nivel 2)
   - Al filtrar productos por categoría principal, la API 
     incluye automáticamente todos los productos de sus subcategorías

PRODUCTOS:
GET /products
GET /products/{id}
POST /products
PUT /products/{id}
DELETE /products/{id}

VARIANTES:
GET /products/{id}/variants
POST /products/{id}/variants
PUT /variants/{id}
POST /variants/{id}/stock/adjust

IMÁGENES:
POST /products/{id}/images
PUT /products/{id}/images/reorder
DELETE /images/{id}

TASAS DE CAMBIO:
GET /exchange-rates/current
POST /exchange-rates
GET /exchange-rates/history

CLIENTES:
GET /customers
POST /customers
PUT /customers/{id}                       (Actualizar datos de cliente)
GET /customers/{id}/purchases

RESERVAS:
GET /reservations
POST /reservations
POST /reservations/{id}/confirm
POST /reservations/{id}/cancel

VENTAS:
GET /sales
POST /sales
GET /sales/{id}

ANALÍTICAS:
GET /analytics/dashboard
GET /analytics/top-products
GET /analytics/sales-by-category
GET /analytics/price-comparison
GET /analytics/low-stock
```

### Stack tecnológico sugerido:
- **Framework:** React, Vue.js, Next.js, Angular (tú decides)
- **Styling:** Tailwind CSS + shadcn/ui o Material-UI o Ant Design
- **State Management:** Context API, Redux, Zustand, Pinia
- **Routing:** React Router / Vue Router / Next.js routing
- **Fetching:** Axios con interceptors para JWT
- **Forms:** React Hook Form / Formik / VeeValidate
- **Tables:** TanStack Table / AG Grid
- **Charts:** Recharts / Chart.js / ApexCharts
- **Date Picker:** react-datepicker / VueDatePicker

---

## 🎨 DISEÑO Y UX

### Principios de diseño:
1. **Eficiencia operativa** - Flujos rápidos para uso diario
2. **Información clara** - Datos importantes siempre visibles
3. **Prevención de errores** - Validaciones y confirmaciones
4. **Responsive** - Funcional en desktop, tablet y móvil
5. **Accesible** - Contraste adecuado, navegación por teclado

### Paleta de colores sugerida:
- **Primary:** Azul profesional (#2563eb)
- **Success:** Verde (#10b981)
- **Warning:** Amarillo/naranja (#f59e0b)
- **Danger:** Rojo (#ef4444)
- **Neutral:** Grises (#6b7280, #f3f4f6)

### Layout general:
```
┌─────────────────────────────────────────────────┐
│  Header: Logo | Usuario | Notificaciones | Logout│
├──────────┬──────────────────────────────────────┤
│          │                                      │
│ Sidebar  │  Contenido Principal                 │
│ Navegación│                                      │
│          │                                      │
│ - Home   │                                      │
│ - Productos│                                     │
│ - Ventas │                                      │
│ - Clientes│                                     │
│ - Analytics│                                     │
│ - Config │                                      │
│          │                                      │
└──────────┴──────────────────────────────────────┘
```

---

## 📐 ESTRUCTURA DE NAVEGACIÓN COMPLETA

### Menú principal (Sidebar):

```
🏠 Dashboard
   └── Vista general con KPIs

📦 Productos
   ├── Lista de productos
   ├── Crear producto
   ├── Editar producto
   └── Categorías

🛒 Operaciones
   ├── Reservas
   │   ├── Reservas activas
   │   ├── Crear reserva
   │   └── Historial
   └── Ventas
       ├── Registrar venta
       ├── Historial de ventas
       └── Detalle de venta

👥 Clientes
   ├── Lista de clientes
   ├── Crear cliente
   └── Perfil de cliente

💱 Tasas de Cambio
   ├── Tasas actuales
   ├── Actualizar tasas
   └── Historial

📊 Análisis
   ├── Dashboard general
   ├── Reportes de ventas
   ├── Productos más vendidos
   ├── Análisis por categoría
   └── Stock crítico

⚙️ Configuración
   ├── Mi perfil
   ├── Configuración general
   └── Usuarios (Fase 2 - Futuro)
```

---

## 🖥️ PANTALLAS Y FUNCIONALIDADES DETALLADAS

### 1. LOGIN

**Ubicación:** `/login` (única pantalla sin autenticación)

**Layout:**
```
┌─────────────────────────────────────┐
│                                     │
│         🏢 Logo Maybel              │
│                                     │
│    ┌─────────────────────┐         │
│    │ Sistema Master      │         │
│    │                     │         │
│    │ Usuario: [_______]  │         │
│    │ Contraseña: [____]  │         │
│    │                     │         │
│    │   [Iniciar Sesión]  │         │
│    │                     │         │
│    └─────────────────────┘         │
│                                     │
└─────────────────────────────────────┘
```

**Funcionalidad:**
```javascript
async function login(username, password) {
  const response = await fetch('API/auth/login', {
    method: 'POST',
    body: JSON.stringify({ username, password })
  });
  
  const { token, user } = await response.json();
  
  // Guardar token en localStorage
  localStorage.setItem('auth_token', token);
  localStorage.setItem('user', JSON.stringify(user));
  
  // Redirigir a dashboard
  navigate('/dashboard');
}
```

**Validaciones:**
- Campos requeridos
- Mostrar error si credenciales inválidas
- Loading state durante autenticación

---

### 2. DASHBOARD (Página de inicio)

**Ubicación:** `/dashboard`

**Layout:**
```
┌─────────────────────────────────────────────────────────┐
│  📊 Dashboard - Resumen General                         │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  ┌────────────┐ ┌────────────┐ ┌────────────┐         │
│  │ 💰 Ventas  │ │ 📦 Productos│ │ 👥 Clientes│         │
│  │ Mes Actual │ │ en Stock   │ │ Registrados│         │
│  │            │ │            │ │            │         │
│  │ $12,500    │ │    245     │ │    156     │         │
│  │ +15% vs mes│ │ -5 vs ayer │ │ +12 nuevos │         │
│  └────────────┘ └────────────┘ └────────────┘         │
│                                                         │
│  ┌────────────┐ ┌────────────┐ ┌────────────┐         │
│  │ 📋 Reservas│ │ ⚠️ Stock   │ │ 💱 Tasa CUP│         │
│  │ Activas    │ │ Crítico    │ │            │         │
│  │            │ │            │ │            │         │
│  │     8      │ │     12     │ │  0.033     │         │
│  │ [Ver todas]│ │ [Ver lista]│ │ [Actualizar│         │
│  └────────────┘ └────────────┘ └────────────┘         │
│                                                         │
│  ┌─────────────────────────────────────────────┐       │
│  │ 📈 Ventas por Moneda (Últimos 30 días)     │       │
│  │                                             │       │
│  │  CUP ████████████████████ 65%  $8,125 USD  │       │
│  │  USD ████████ 20%  $2,500 USD              │       │
│  │  EUR ████ 10%  $1,250 USD                  │       │
│  │  MLC ██ 5%  $625 USD                       │       │
│  │                                             │       │
│  └─────────────────────────────────────────────┘       │
│                                                         │
│  ┌─────────────────────────────────────────────┐       │
│  │ 🔥 Productos Más Vendidos                   │       │
│  │                                             │       │
│  │  1. Bicicleta MTB Roja      15 vendidas    │       │
│  │  2. Refrigerador LG         12 vendidas    │       │
│  │  3. Lavadora Samsung         8 vendidas    │       │
│  │  4. Ventilador de Pie        7 vendidas    │       │
│  │  5. Microondas Panasonic     6 vendidas    │       │
│  │                                             │       │
│  └─────────────────────────────────────────────┘       │
│                                                         │
│  ┌─────────────────────────────────────────────┐       │
│  │ 📅 Actividad Reciente                       │       │
│  │                                             │       │
│  │  • Venta confirmada: Bicicleta MTB (hace 2h│       │
│  │  • Nueva reserva: Refrigerador (hace 3h)   │       │
│  │  • Stock ajustado: Lavadora (+5) (hace 5h) │       │
│  │  • Nuevo producto: TV 55" (hace 1 día)     │       │
│  │                                             │       │
│  └─────────────────────────────────────────────┘       │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

**Datos a mostrar:**
- Total de ventas del mes en USD
- Comparación con mes anterior
- Productos en stock
- Stock crítico (alerta si hay productos con < 5 unidades)
- Reservas activas
- Tasa CUP actual
- Gráfico de ventas por moneda
- Top 5 productos más vendidos
- Actividad reciente (últimas 10 acciones)

**Consumo de API:**
```javascript
// Datos del dashboard
const dashboardData = await fetch('API/analytics/dashboard', {
  headers: { 'Authorization': `Bearer ${token}` }
});
```

---

### 3. PRODUCTOS - Lista

**Ubicación:** `/productos`

**Layout:**
```
┌─────────────────────────────────────────────────────────┐
│  📦 Gestión de Productos                                │
│                                                         │
│  🔍 FILTROS:                                            │
│  ┌─────────────────────────────────────────────────┐   │
│  │ [🔍 Buscar producto por nombre...]             │   │
│  │                                                 │   │
│  │ Categoría:          Subcategoría:              │   │
│  │ [Todas ▼]          [Todas ▼]                    │   │
│  │  Vehículos          Bicicletas                  │   │
│  │  Electrodomésticos  Motos                       │   │
│  │  Muebles            Accesorios                  │   │
│  │                                                 │   │
│  │ Estado: [Todos ▼]  Stock: [Todos ▼]            │   │
│  │                                                 │   │
│  │              [Limpiar] [Aplicar]  [+ Nuevo]     │   │
│  └─────────────────────────────────────────────────┘   │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  Mostrando: Vehículos > Bicicletas (8 productos)        │
│                                                         │
│  ┌──────┬───────────────┬──────────────┬────────┬─────┐│
│  │ Img  │ Nombre        │ Categoría    │ Stock  │Precio││
│  ├──────┼───────────────┼──────────────┼────────┼─────┤│
│  │ [🖼️]│ Bicicleta MTB │ Vehículos >  │   8   │$150 ││
│  │      │ Aro 26        │ Bicicletas   │   🟢   │     ││
│  │      │ [Editar] [❌]│              │        │     ││
│  ├──────┼───────────────┼──────────────┼────────┼─────┤│
│  │ [🖼️]│ Bicicleta     │ Vehículos >  │   5   │$180 ││
│  │      │ Urbana 29"    │ Bicicletas   │   🟢   │     ││
│  │      │ [Editar] [❌]│              │        │     ││
│  ├──────┼───────────────┼──────────────┼────────┼─────┤│
│  │ [🖼️]│ Mountain Bike │ Vehículos >  │   2   │$220 ││
│  │      │ Pro 27.5      │ Bicicletas   │   🟡   │     ││
│  │      │ [Editar] [❌]│              │        │     ││
│  └──────┴───────────────┴──────────────┴────────┴─────┘│
│                                                         │
│  [◀ Anterior]  1  2  3  [Siguiente ▶]                  │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

**Funcionalidades:**
- ✅ **Buscar** por nombre de producto
- ✅ **Filtrar por CATEGORÍA** (nivel 1 de jerarquía)
- ✅ **Filtrar por SUBCATEGORÍA** (nivel 2 de jerarquía)
- ✅ **Filtro en cascada**: Al seleccionar categoría, se cargan automáticamente sus subcategorías en el segundo dropdown
- ✅ **Filtrar por estado** (activo/inactivo)
- ✅ **Filtrar por stock** (todos / disponible / stock bajo / agotado)
- ✅ **Ver** detalles al hacer clic en fila
- ✅ **Editar** producto (redirige a `/productos/editar/{id}`)
- ✅ **Eliminar** producto (confirmación modal)
- ✅ **Indicadores de stock:**
  - 🟢 Verde: >= 5 unidades
  - 🟡 Amarillo: 1-4 unidades
  - 🔴 Rojo: 0 unidades
- ✅ **Mostrar jerarquía completa** en cada fila (Categoría > Subcategoría)

**Consumo de API:**
```javascript
// 1. Cargar categorías principales (parent_id = null)
const categories = await fetch(
  'API/categories?parent_id=null',
  { headers: { 'Authorization': `Bearer ${token}` } }
);

// 2. Al seleccionar categoría, cargar sus subcategorías
const subcategories = await fetch(
  `API/categories?parent_id=${selectedCategoryId}`,
  { headers: { 'Authorization': `Bearer ${token}` } }
);

// 3. Filtrar productos por categoría O subcategoría
const products = await fetch(
  `API/products?page=1&limit=20
   ${selectedSubcategory 
     ? `&category_id=${selectedSubcategory}` 
     : selectedCategory 
     ? `&category_id=${selectedCategory}` 
     : ''}
   &search=${searchTerm}
   &active=${activeFilter}`,
  { headers: { 'Authorization': `Bearer ${token}` } }
);
```

**Lógica de filtro jerárquico:**
```javascript
function ProductFilters({ onFilterChange }) {
  const [categories, setCategories] = useState([]);
  const [subcategories, setSubcategories] = useState([]);
  const [selectedCategory, setSelectedCategory] = useState('');
  const [selectedSubcategory, setSelectedSubcategory] = useState('');
  
  // Cargar categorías principales al montar
  useEffect(() => {
    fetch('API/categories?parent_id=null')
      .then(res => res.json())
      .then(data => setCategories(data.data));
  }, []);
  
  // Cargar subcategorías al cambiar categoría
  useEffect(() => {
    if (selectedCategory) {
      fetch(`API/categories?parent_id=${selectedCategory}`)
        .then(res => res.json())
        .then(data => setSubcategories(data.data));
    } else {
      setSubcategories([]);
      setSelectedSubcategory('');
    }
  }, [selectedCategory]);
  
  // Notificar cambios de filtro
  useEffect(() => {
    // Si hay subcategoría seleccionada, filtrar por ella
    // Si no, filtrar por categoría
    // Si ninguna, mostrar todos
    const categoryId = selectedSubcategory || selectedCategory || null;
    onFilterChange({ categoryId });
  }, [selectedCategory, selectedSubcategory]);
  
  return (
    <div className="filters">
      {/* Categoría principal */}
      <Select
        value={selectedCategory}
        onChange={(value) => {
          setSelectedCategory(value);
          setSelectedSubcategory(''); // Reset subcategoría
        }}
        options={[
          { value: '', label: 'Todas las categorías' },
          ...categories.map(cat => ({ 
            value: cat.id, 
            label: cat.name 
          }))
        ]}
      />
      
      {/* Subcategoría (solo si hay categoría seleccionada) */}
      {selectedCategory && subcategories.length > 0 && (
        <Select
          value={selectedSubcategory}
          onChange={setSelectedSubcategory}
          options={[
            { value: '', label: 'Todas las subcategorías' },
            ...subcategories.map(sub => ({ 
              value: sub.id, 
              label: sub.name 
            }))
          ]}
        />
      )}
    </div>
  );
}
```

---

### 4. PRODUCTOS - Crear/Editar

**Ubicación:** `/productos/nuevo` o `/productos/editar/{id}`

**Layout (Tabs):**
```
┌─────────────────────────────────────────────────────────┐
│  📝 [Crear / Editar] Producto                           │
│                                                         │
│  [Información] [Variantes] [Imágenes]                   │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  TAB 1: INFORMACIÓN BÁSICA                              │
│                                                         │
│  Nombre del producto *                                  │
│  [________________________________]                     │
│                                                         │
│  Descripción *                                          │
│  [________________________________]                     │
│  [________________________________]                     │
│  [________________________________]                     │
│                                                         │
│  Categoría *                                            │
│  [Seleccionar categoría ▼]                              │
│                                                         │
│  ────────────────────────────────                       │
│  💰 PRECIOS EN TODAS LAS MONEDAS                        │
│  ────────────────────────────────                       │
│                                                         │
│  USD (Precio base) *        EUR                         │
│  [_________]                [_________]                 │
│                                                         │
│  CUP                        MLC                         │
│  [_________]                [_________]                 │
│                                                         │
│  Peso Mexicano              Dólar Canadiense            │
│  [_________]                [_________]                 │
│                                                         │
│  Real Brasileño             Zelle                       │
│  [_________]                [_________]                 │
│                                                         │
│  Transferencia CUP          Tarjeta Clásica CUP         │
│  [_________]                [_________]                 │
│                                                         │
│  💡 Sugerencia: Usa el precio USD como base.            │
│      Los demás precios se calcularán automáticamente    │
│      con las tasas actuales si los dejas vacíos.        │
│                                                         │
│  [📊 Calcular precios automáticamente]                  │
│                                                         │
│                                   [Cancelar] [Guardar]  │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

**TAB 2: VARIANTES (Colores)**
```
┌─────────────────────────────────────────────────────────┐
│  🎨 Variantes del Producto                              │
│                                                         │
│  [+ Agregar Variante]                                   │
│                                                         │
│  ┌──────────┬─────────┬─────────┬────────────────┐     │
│  │ Color    │ SKU     │ Stock   │ Acciones       │     │
│  ├──────────┼─────────┼─────────┼────────────────┤     │
│  │ 🔴 Rojo  │ MTB-RED │    5    │ [✏️] [➕] [🗑️]│     │
│  │ 🔵 Azul  │ MTB-BLUE│    3    │ [✏️] [➕] [🗑️]│     │
│  │ ⚫ Negro │ MTB-BLK │    0    │ [✏️] [➕] [🗑️]│     │
│  └──────────┴─────────┴─────────┴────────────────┘     │
│                                                         │
│  ✏️ = Editar   ➕ = Ajustar stock   🗑️ = Eliminar      │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

**Modal: Agregar variante**
```
┌─────────────────────────────┐
│  Agregar Variante           │
├─────────────────────────────┤
│                             │
│  Color *                    │
│  [_____________]            │
│                             │
│  SKU (opcional)             │
│  [_____________]            │
│                             │
│  Descripción                │
│  [_____________]            │
│                             │
│  Stock inicial *            │
│  [_______] unidades         │
│                             │
│       [Cancelar] [Guardar]  │
└─────────────────────────────┘
```

**TAB 3: IMÁGENES**
```
┌─────────────────────────────────────────────────────────┐
│  🖼️ Imágenes del Producto                               │
│                                                         │
│  [📤 Subir imágenes]                                    │
│                                                         │
│  Arrastra para reordenar:                               │
│                                                         │
│  ┌──────┐ ┌──────┐ ┌──────┐ ┌──────┐                  │
│  │ [🖼️] │ │ [🖼️] │ │ [🖼️] │ │ [🖼️] │                  │
│  │      │ │      │ │      │ │      │                  │
│  │ Img1 │ │ Img2 │ │ Img3 │ │ Img4 │                  │
│  │ [❌] │ │ [❌] │ │ [❌] │ │ [❌] │                  │
│  └──────┘ └──────┘ └──────┘ └──────┘                  │
│     1        2        3        4                        │
│                                                         │
│  💡 La primera imagen es la imagen principal            │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

**Funcionalidades:**

**1. Calcular precios automáticamente:**
```javascript
async function calculatePrices(priceUSD) {
  // Obtener tasas actuales
  const rates = await fetch('API/exchange-rates/current');
  
  // Calcular todos los precios
  return {
    price_eur: priceUSD / rates.rate_eur,
    price_cup: priceUSD / rates.rate_cup,
    price_mlc: priceUSD / rates.rate_mlc,
    // ... etc
  };
}
```

**2. Subir imágenes:**
```javascript
async function uploadImage(file, productId) {
  // Subir archivo al servidor (file storage)
  const formData = new FormData();
  formData.append('file', file);
  
  const uploadResponse = await fetch('/upload', {
    method: 'POST',
    body: formData
  });
  
  const { file_path } = await uploadResponse.json();
  
  // Registrar en API
  await fetch(`API/products/${productId}/images`, {
    method: 'POST',
    headers: { 'Authorization': `Bearer ${token}` },
    body: JSON.stringify({ file_path, position: 1 })
  });
}
```

**3. Crear producto completo (flujo):**
```javascript
async function createProduct(productData) {
  // 1. Crear producto
  const product = await fetch('API/products', {
    method: 'POST',
    headers: { 'Authorization': `Bearer ${token}` },
    body: JSON.stringify(productData)
  });
  
  const { id: productId } = await product.json();
  
  // 2. Crear variantes
  for (const variant of variants) {
    await fetch(`API/products/${productId}/variants`, {
      method: 'POST',
      headers: { 'Authorization': `Bearer ${token}` },
      body: JSON.stringify(variant)
    });
  }
  
  // 3. Subir imágenes
  for (const image of images) {
    await uploadImage(image, productId);
  }
  
  // 4. Redirigir a lista
  navigate('/productos');
}
```

**Validaciones:**
- Nombre: requerido, máx 150 caracteres
- Categoría: requerida
- Precio USD: requerido, > 0
- Al menos 1 variante con stock inicial

---

### 5. RESERVAS - Lista

**Ubicación:** `/reservas`

**Layout:**
```
┌─────────────────────────────────────────────────────────┐
│  📋 Gestión de Reservas                                 │
│                                                         │
│  [Estado: Todas ▼] [Cliente ▼] [+ Nueva Reserva]       │
│                                                         │
│  TABS: [Activas (8)] [Confirmadas] [Canceladas] [Todas]│
├─────────────────────────────────────────────────────────┤
│                                                         │
│  ┌────────────────────────────────────────────────────┐ │
│  │ RESERVA #RES-2024-00123                           │ │
│  │ ────────────────────────────────────────────────  │ │
│  │                                                   │ │
│  │ Cliente: Fernando García                          │ │
│  │ Teléfono: 53001234                                │ │
│  │                                                   │ │
│  │ Producto: Bicicleta MTB Aro 26                    │ │
│  │ Variante: Rojo                                    │ │
│  │ Cantidad: 2 unidades                              │ │
│  │                                                   │ │
│  │ Reservado: 17 Dic 2024, 10:30am                   │ │
│  │ Vence: 24 Dic 2024                                │ │
│  │                                                   │ │
│  │ Estado: 🟢 ACTIVA                                 │ │
│  │                                                   │ │
│  │ [✅ Confirmar Venta] [❌ Cancelar] [👁️ Detalles]│ │
│  └────────────────────────────────────────────────────┘ │
│                                                         │
│  ┌────────────────────────────────────────────────────┐ │
│  │ RESERVA #RES-2024-00124                           │ │
│  │ ────────────────────────────────────────────────  │ │
│  │ ...                                               │ │
│  └────────────────────────────────────────────────────┘ │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

**Funcionalidades:**
- ✅ Filtrar por estado (activas, confirmadas, canceladas)
- ✅ Buscar por cliente
- ✅ Ver detalles completos
- ✅ **Confirmar venta** (modal con datos de venta)
- ✅ **Cancelar** reserva (con confirmación)
- ✅ Alertas de expiración (resaltar si vence en < 24h)

**Consumo de API:**
```javascript
const reservations = await fetch(
  'API/reservations?status=ACTIVE',
  { headers: { 'Authorization': `Bearer ${token}` } }
);
```

---

### 6. RESERVAS - Crear Nueva

**Ubicación:** `/reservas/nueva`

**Layout:**
```
┌─────────────────────────────────────────────────────────┐
│  📝 Nueva Reserva                                       │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  PASO 1: Seleccionar Cliente                            │
│  ───────────────────────────────────                    │
│                                                         │
│  [Buscar cliente existente...] o [➕ Nuevo Cliente]     │
│                                                         │
│  ┌───────────────────────────────────────────┐         │
│  │ ✓ Fernando García                         │         │
│  │   Tel: 53001234                           │         │
│  │   Calle 23 #456, Centro Habana            │         │
│  └───────────────────────────────────────────┘         │
│                                                         │
│  PASO 2: Seleccionar Producto                           │
│  ───────────────────────────────────                    │
│                                                         │
│  [Buscar producto...]                                   │
│                                                         │
│  ┌───────────────────────────────────────────┐         │
│  │ ✓ Bicicleta MTB Aro 26                    │         │
│  │   Categoría: Bicicletas                   │         │
│  │   Precio: $150 USD / $4,500 CUP           │         │
│  └───────────────────────────────────────────┘         │
│                                                         │
│  PASO 3: Seleccionar Variante y Cantidad               │
│  ───────────────────────────────────                    │
│                                                         │
│  Color: ○ Rojo (5 disp.)  ● Azul (3 disp.)  ○ Negro (0)│
│                                                         │
│  Cantidad: [🔻] 2 [🔺] unidades                         │
│                                                         │
│  Stock disponible: 3 - 2 reservada = 1 restante        │
│                                                         │
│  PASO 4: Detalles Adicionales                          │
│  ───────────────────────────────────                    │
│                                                         │
│  Fecha de vencimiento (opcional)                        │
│  [📅 24/12/2024]                                        │
│                                                         │
│  Notas                                                  │
│  [________________________________]                     │
│  [________________________________]                     │
│                                                         │
│                               [Cancelar] [Crear Reserva]│
│                                                         │
└─────────────────────────────────────────────────────────┘
```

**Validaciones:**
- Cliente: requerido
- Producto: requerido
- Variante: requerida
- Cantidad: > 0 y <= stock disponible
- Fecha vencimiento: opcional, > hoy

**Flujo:**
```javascript
async function createReservation(data) {
  // Validar stock disponible
  const variant = await fetch(`API/variants/${data.variant_id}`);
  const available = variant.stock - variant.reserved;
  
  if (data.quantity > available) {
    alert('Stock insuficiente');
    return;
  }
  
  // Crear reserva
  await fetch('API/reservations', {
    method: 'POST',
    headers: { 'Authorization': `Bearer ${token}` },
    body: JSON.stringify(data)
  });
  
  navigate('/reservas');
}
```

---

### 7. RESERVAS - Confirmar Venta (Modal)

**Modal al hacer clic en "Confirmar Venta":**
```
┌─────────────────────────────────────────────┐
│  ✅ Confirmar Venta                         │
├─────────────────────────────────────────────┤
│                                             │
│  Reserva: #RES-2024-00123                   │
│  Cliente: Fernando García                   │
│  Producto: Bicicleta MTB Roja               │
│  Cantidad: 2 unidades                       │
│  ─────────────────────────────────────────  │
│                                             │
│  DATOS DE LA VENTA:                         │
│                                             │
│  Moneda usada en la venta *                 │
│  [CUP ▼] USD, EUR, CUP, MLC, MXN...         │
│                                             │
│  Precio unitario (en CUP) *                 │
│  [4500.00]                                  │
│                                             │
│  ℹ️ Conversión automática a USD:            │
│     Tasa actual: 1 CUP = 0.033 USD          │
│     Precio unit. USD: $148.50               │
│     Total USD: $297.00                      │
│                                             │
│  Notas adicionales                          │
│  [___________________________]              │
│  [___________________________]              │
│                                             │
│  ⚠️ Esta acción:                            │
│  • Creará una venta definitiva              │
│  • Descontará el stock permanentemente      │
│  • Marcará la reserva como confirmada       │
│                                             │
│           [Cancelar] [✅ Confirmar Venta]   │
│                                             │
└─────────────────────────────────────────────┘
```

**Flujo:**
```javascript
async function confirmReservation(reservationId, saleData) {
  // Confirmar reserva (la API hace todo automáticamente)
  await fetch(`API/reservations/${reservationId}/confirm`, {
    method: 'POST',
    headers: { 'Authorization': `Bearer ${token}` },
    body: JSON.stringify({
      currency_used: saleData.currency,
      unit_price_currency: saleData.price,
      notes: saleData.notes
    })
  });
  
  // Mostrar éxito
  toast.success('¡Venta confirmada exitosamente!');
  
  // Actualizar lista
  refreshReservations();
}
```

---

### 8. VENTAS - Registrar Venta Directa

**Ubicación:** `/ventas/nueva`

**Layout (idéntico a crear reserva + confirmar):**
```
┌─────────────────────────────────────────────────────────┐
│  💰 Registrar Venta Directa                             │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  1. Cliente                                             │
│  [Buscar o crear cliente...]                            │
│                                                         │
│  2. Producto y Variante                                 │
│  [Buscar producto...]                                   │
│  Color: ○ Rojo ● Azul ○ Negro                           │
│                                                         │
│  3. Cantidad                                            │
│  [🔻] 1 [🔺] unidades                                   │
│  Stock disponible: 3                                    │
│                                                         │
│  4. Datos de Pago                                       │
│  Moneda: [CUP ▼]                                        │
│  Precio unitario: [4500.00]                             │
│                                                         │
│  💡 Total en USD: $148.50                               │
│                                                         │
│  5. Notas                                               │
│  [________________________________]                     │
│                                                         │
│                          [Cancelar] [Registrar Venta]   │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

**Consumo de API:**
```javascript
await fetch('API/sales', {
  method: 'POST',
  headers: { 'Authorization': `Bearer ${token}` },
  body: JSON.stringify({
    variant_id,
    customer_id,
    quantity,
    currency_used,
    unit_price_currency,
    notes
  })
});
```

---

### 9. VENTAS - Historial

**Ubicación:** `/ventas`

**Layout:**
```
┌─────────────────────────────────────────────────────────┐
│  💰 Historial de Ventas                                 │
│                                                         │
│  🔍 FILTROS:                                            │
│  ┌─────────────────────────────────────────────────┐   │
│  │ Período:                                        │   │
│  │ Del: [📅 01/12/2024]  Al: [📅 31/12/2024]      │   │
│  │                                                 │   │
│  │ Categoría:                                      │   │
│  │ [Todas las categorías ▼]                        │   │
│  │   ├─ Vehículos                                  │   │
│  │   ├─ Electrodomésticos                          │   │
│  │   └─ Muebles                                    │   │
│  │                                                 │   │
│  │ Subcategoría: (aparece al seleccionar categoría)│   │
│  │ [Todas las subcategorías ▼]                     │   │
│  │   ├─ Bicicletas                                 │   │
│  │   ├─ Motos                                      │   │
│  │   └─ Accesorios                                 │   │
│  │                                                 │   │
│  │ Moneda: [Todas ▼]  Cliente: [Buscar cliente...]│   │
│  │                                                 │   │
│  │                    [Limpiar] [Aplicar Filtros]  │   │
│  └─────────────────────────────────────────────────┘   │
│                                                         │
│  📊 Resumen: 45 ventas | Total: $12,500 USD             │
│     Vehículos > Bicicletas: 15 ventas ($2,250)          │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  ┌──────┬────────┬─────────────────┬────────┬────────┐ │
│  │ Fecha│ Cliente│ Producto        │ Cant.  │ Total  │ │
│  ├──────┼────────┼─────────────────┼────────┼────────┤ │
│  │17 Dic│Fernando│🚲 Bicicleta MTB │   2    │ $297   │ │
│  │10:30 │García  │   Roja          │        │9000 CUP│ │
│  │      │        │   Vehículos >   │        │        │ │
│  │      │        │   Bicicletas    │        │        │ │
│  │      │        │   [Ver detalles]│        │        │ │
│  ├──────┼────────┼─────────────────┼────────┼────────┤ │
│  │17 Dic│María   │❄️ Refrigerador  │   1    │ $800   │ │
│  │09:15 │López   │   LG 18p        │        │800 USD │ │
│  │      │        │   Electro. >    │        │        │ │
│  │      │        │   Refrigeración │        │        │ │
│  │      │        │   [Ver detalles]│        │        │ │
│  └──────┴────────┴─────────────────┴────────┴────────┘ │
│                                                         │
│  [Exportar a Excel] [Exportar a PDF]                    │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

**Funcionalidades:**
- ✅ Filtrar por rango de fechas
- ✅ **Filtrar por CATEGORÍA** (jerarquía nivel 1)
- ✅ **Filtrar por SUBCATEGORÍA** (jerarquía nivel 2)
- ✅ Filtro en cascada: al seleccionar categoría, se cargan sus subcategorías
- ✅ Filtrar por moneda
- ✅ Buscar por cliente
- ✅ Ver detalle completo de cada venta
- ✅ Exportar reportes
- ✅ Mostrar jerarquía completa en cada fila (Categoría > Subcategoría)

**Consumo de API:**
```javascript
// 1. Obtener categorías para el filtro (dropdown)
const categories = await fetch('API/categories?parent_id=null', {
  headers: { 'Authorization': `Bearer ${token}` }
});

// 2. Al seleccionar categoría, obtener subcategorías
const subcategories = await fetch(
  `API/categories?parent_id=${selectedCategoryId}`,
  { headers: { 'Authorization': `Bearer ${token}` } }
);

// 3. Obtener ventas con los filtros disponibles en la API
const sales = await fetch(
  `API/sales?start_date=2024-12-01&end_date=2024-12-31&currency=${currency}`,
  { headers: { 'Authorization': `Bearer ${token}` } }
);

// ⚠️ NOTA: Los filtros por categoría/subcategoría se aplican en el FRONTEND
// La API devuelve las ventas con el producto asociado, y el frontend
// filtra por la categoría del producto. Ejemplo:
const filteredSales = sales.data.filter(sale => {
  if (!selectedCategory) return true;
  // La venta incluye info del producto y su categoría
  return sale.product.category_id === selectedCategory || 
         sale.product.category.parent_id === selectedCategory;
});
```

**Lógica de filtros en cascada:**
```javascript
function CategorySubcategoryFilter() {
  const [categories, setCategories] = useState([]);
  const [subcategories, setSubcategories] = useState([]);
  const [selectedCategory, setSelectedCategory] = useState(null);
  const [selectedSubcategory, setSelectedSubcategory] = useState(null);
  
  // Al cambiar categoría, cargar subcategorías
  useEffect(() => {
    if (selectedCategory) {
      fetch(`API/categories?parent_id=${selectedCategory}`)
        .then(res => res.json())
        .then(data => setSubcategories(data.data));
    } else {
      setSubcategories([]);
      setSelectedSubcategory(null);
    }
  }, [selectedCategory]);
  
  return (
    <>
      <Select 
        value={selectedCategory}
        onChange={setSelectedCategory}
        options={categories}
        placeholder="Todas las categorías"
      />
      
      {selectedCategory && (
        <Select 
          value={selectedSubcategory}
          onChange={setSelectedSubcategory}
          options={subcategories}
          placeholder="Todas las subcategorías"
        />
      )}
    </>
  );
}
```

---

### 10. CLIENTES - Lista

**Ubicación:** `/clientes`

**Layout:**
```
┌─────────────────────────────────────────────────────────┐
│  👥 Gestión de Clientes                                 │
│                                                         │
│  [🔍 Buscar por nombre o teléfono...]  [+ Nuevo Cliente]│
├─────────────────────────────────────────────────────────┤
│                                                         │
│  ┌──────────────┬────────────┬──────────┬──────────┐   │
│  │ Nombre       │ Teléfono   │ Compras  │ Total    │   │
│  ├──────────────┼────────────┼──────────┼──────────┤   │
│  │ Fernando     │ 53001234   │    5     │ $1,250   │   │
│  │ García       │            │          │          │   │
│  │ [Ver perfil] │            │          │          │   │
│  ├──────────────┼────────────┼──────────┼──────────┤   │
│  │ María        │ 53005678   │    3     │ $2,100   │   │
│  │ López        │            │          │          │   │
│  │ [Ver perfil] │            │          │          │   │
│  └──────────────┴────────────┴──────────┴──────────┘   │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

---

### 11. CLIENTES - Perfil

**Ubicación:** `/clientes/{id}`

**Layout:**
```
┌─────────────────────────────────────────────────────────┐
│  👤 Perfil de Cliente                                   │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  Fernando García                            [Editar]    │
│  Tel: 53001234                                          │
│  Dirección: Calle 23 #456, Centro Habana                │
│                                                         │
│  ─────────────────────────────────────────────────────  │
│                                                         │
│  📊 ESTADÍSTICAS                                        │
│                                                         │
│  Total de compras: 5                                    │
│  Total gastado: $1,250 USD                              │
│  Cliente desde: Octubre 2024                            │
│                                                         │
│  ─────────────────────────────────────────────────────  │
│                                                         │
│  📦 HISTORIAL DE COMPRAS                                │
│                                                         │
│  ┌─────────┬────────────────────┬────────┬─────────┐   │
│  │ Fecha   │ Producto           │ Cant.  │ Total   │   │
│  ├─────────┼────────────────────┼────────┼─────────┤   │
│  │ 17 Dic  │ Bicicleta MTB Roja │   2    │ $297    │   │
│  │ 10 Dic  │ Ventilador de Pie  │   1    │ $45     │   │
│  │ 05 Dic  │ Lavadora Samsung   │   1    │ $600    │   │
│  └─────────┴────────────────────┴────────┴─────────┘   │
│                                                         │
│  ─────────────────────────────────────────────────────  │
│                                                         │
│  📋 RESERVAS ACTIVAS (1)                                │
│                                                         │
│  • Refrigerador LG - 1 unidad - Vence: 20/12           │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

**Consumo de API:**
```javascript
const customerData = await fetch(`API/customers/${id}/purchases`, {
  headers: { 'Authorization': `Bearer ${token}` }
});
```

---

### 12. TASAS DE CAMBIO - Gestión

**Ubicación:** `/tasas`

**Layout:**
```
┌─────────────────────────────────────────────────────────┐
│  💱 Tasas de Cambio                                     │
│                                                         │
│  Última actualización: 15 Dic 2024, 10:00am             │
│  Por: admin                                             │
│                                                         │
│  [🔄 Actualizar Tasas]  [📊 Ver Historial]              │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  TASAS ACTUALES (respecto a USD)                        │
│  ─────────────────────────────────────                  │
│                                                         │
│  ┌──────────────────────┬──────────────┬──────────┐    │
│  │ Moneda               │ Tasa         │ Ejemplo  │    │
│  ├──────────────────────┼──────────────┼──────────┤    │
│  │ 🇺🇸 USD             │ 1.000000     │ 1 = $1   │    │
│  │ 🇪🇺 Euro            │ 1.080000     │ 1 = $1.08│    │
│  │ 🇨🇺 CUP             │ 0.033000     │ 1 = $0.03│    │
│  │ 💵 MLC              │ 1.000000     │ 1 = $1   │    │
│  │ 🇲🇽 Peso Mexicano   │ 0.059000     │ 1 = $0.06│    │
│  │ 🇨🇦 Dólar Canadiense│ 0.740000     │ 1 = $0.74│    │
│  │ 🇧🇷 Real Brasileño  │ 0.200000     │ 1 = $0.20│    │
│  │ 💸 Zelle            │ 1.000000     │ 1 = $1   │    │
│  │ 💳 Transfer. CUP    │ 0.031000     │ 1 = $0.03│    │
│  │ 💳 Tarjeta CUP      │ 0.030000     │ 1 = $0.03│    │
│  └──────────────────────┴──────────────┴──────────┘    │
│                                                         │
│  ℹ️ Estas tasas se usan para convertir todas las        │
│     ventas a USD y calcular ganancias reales.           │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

**Modal: Actualizar tasas**
```
┌─────────────────────────────────────────────┐
│  🔄 Actualizar Tasas de Cambio              │
├─────────────────────────────────────────────┤
│                                             │
│  💡 Ingresa las nuevas tasas respecto a USD │
│                                             │
│  Euro:                 [1.10_____]          │
│  CUP:                  [0.030____]          │
│  MLC:                  [1.00_____]          │
│  Peso Mexicano:        [0.058____]          │
│  Dólar Canadiense:     [0.75_____]          │
│  Real Brasileño:       [0.19_____]          │
│  Zelle:                [1.00_____]          │
│  Transferencia CUP:    [0.029____]          │
│  Tarjeta Clásica CUP:  [0.028____]          │
│                                             │
│  Notas (opcional):                          │
│  [Actualización semanal - 22 Dic 2024]      │
│                                             │
│  ⚠️ Esto cerrará las tasas anteriores y     │
│     creará un nuevo registro histórico.     │
│                                             │
│          [Cancelar] [💾 Guardar Tasas]      │
│                                             │
└─────────────────────────────────────────────┘
```

**Consumo de API:**
```javascript
// Obtener tasas actuales
const rates = await fetch('API/exchange-rates/current', {
  headers: { 'Authorization': `Bearer ${token}` }
});

// Actualizar tasas
await fetch('API/exchange-rates', {
  method: 'POST',
  headers: { 'Authorization': `Bearer ${token}` },
  body: JSON.stringify({
    rate_eur: 1.10,
    rate_cup: 0.030,
    rate_mlc: 1.00,
    rate_mxn: 0.058,
    rate_cad: 0.75,
    rate_brl: 0.19,
    rate_zelle: 1.00,
    rate_cup_transferencia: 0.029,
    rate_cup_tarjeta: 0.028,
    notes: 'Actualización semanal'
  })
});
```

---

### 13. ANÁLISIS - Reportes Avanzados

**Ubicación:** `/analisis`

**Layout:**
```
┌─────────────────────────────────────────────────────────┐
│  📊 Análisis y Reportes                                 │
│                                                         │
│  Período: [Del: 01/12/2024] [Al: 31/12/2024]           │
│                                                         │
│  TABS: [General] [Por Categoría] [Por Moneda] [Stock]  │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  TAB: GENERAL                                           │
│                                                         │
│  📈 Ventas por Día                                      │
│  ┌────────────────────────────────────────────────┐    │
│  │     (Gráfico de línea)                         │    │
│  │  $                                              │    │
│  │ 800│                      ●                     │    │
│  │ 600│        ●        ●                          │    │
│  │ 400│   ●                      ●                 │    │
│  │ 200│                                ●           │    │
│  │    └────────────────────────────────────       │    │
│  │     1  5  10  15  20  25  30  Dic              │    │
│  └────────────────────────────────────────────────┘    │
│                                                         │
│  🏆 Top 10 Productos Más Vendidos                       │
│  ┌────────────────────────────────────────────────┐    │
│  │  1. Bicicleta MTB Roja      ████████ 15 vend.  │    │
│  │  2. Refrigerador LG         ██████ 12 vend.    │    │
│  │  3. Lavadora Samsung        ████ 8 vend.       │    │
│  │  ...                                            │    │
│  └────────────────────────────────────────────────┘    │
│                                                         │
│  ⚠️ Productos con Stock Crítico (< 5 unidades)          │
│  ┌────────────────────────────────────────────────┐    │
│  │  • Refrigerador LG Gris - 2 unidades            │    │
│  │  • Bicicleta MTB Negra - 0 unidades (Agotado)  │    │
│  │  • Ventilador 16" - 3 unidades                  │    │
│  └────────────────────────────────────────────────┘    │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

**TAB: Por Categoría**
```
┌─────────────────────────────────────────────────────────┐
│  📂 Ventas por Categoría y Subcategoría                 │
│                                                         │
│  Ver: ○ Solo Categorías  ● Categorías + Subcategorías  │
│                                                         │
│  ┌────────────────────────────────────────────────┐    │
│  │        (Gráfico de barras jerárquico)          │    │
│  │                                                 │    │
│  │  Vehículos ████████████████████████ $8,500     │    │
│  │    └─ Bicicletas ████████████ $4,200           │    │
│  │    └─ Motos ████████ $3,100                    │    │
│  │    └─ Accesorios ████ $1,200                   │    │
│  │                                                 │    │
│  │  Electrodomésticos ████████████ $4,000         │    │
│  │    └─ Refrigeración █████ $2,000               │    │
│  │    └─ Lavado ████ $1,500                       │    │
│  │    └─ Cocina ██ $500                           │    │
│  │                                                 │    │
│  └────────────────────────────────────────────────┘    │
│                                                         │
│  Detalle Expandido:                                     │
│  ┌────────────────────┬──────────┬──────────────┐      │
│  │ Categoría          │ Ventas   │ Revenue USD  │      │
│  ├────────────────────┼──────────┼──────────────┤      │
│  │ 📁 Vehículos       │    35    │  $8,500      │      │
│  │   ├─ Bicicletas    │    20    │  $4,200      │      │
│  │   ├─ Motos         │    12    │  $3,100      │      │
│  │   └─ Accesorios    │     3    │  $1,200      │      │
│  ├────────────────────┼──────────┼──────────────┤      │
│  │ 📁 Electrodomésticos│   10    │  $4,000      │      │
│  │   ├─ Refrigeración │     5    │  $2,000      │      │
│  │   ├─ Lavado        │     3    │  $1,500      │      │
│  │   └─ Cocina        │     2    │  $500        │      │
│  └────────────────────┴──────────┴──────────────┘      │
│                                                         │
│  💡 Click en una categoría para ver solo sus subcategorías│
│                                                         │
└─────────────────────────────────────────────────────────┘
```

**Funcionalidad interactiva:**
```javascript
function CategoryAnalytics() {
  const [viewMode, setViewMode] = useState('hierarchy'); // 'flat' o 'hierarchy'
  const [expandedCategories, setExpandedCategories] = useState([]);
  
  // Obtener datos jerárquicos
  const salesData = await fetch(
    'API/analytics/sales-by-category?include_subcategories=true',
    { headers: { 'Authorization': `Bearer ${token}` } }
  );
  
  // Response esperado:
  // {
  //   "Vehículos": {
  //     "total": 8500,
  //     "count": 35,
  //     "subcategories": {
  //       "Bicicletas": { "total": 4200, "count": 20 },
  //       "Motos": { "total": 3100, "count": 12 }
  //     }
  //   }
  // }
  
  return (
    <div>
      {/* Toggle para vista plana vs jerárquica */}
      <RadioGroup value={viewMode} onChange={setViewMode}>
        <Radio value="flat">Solo Categorías</Radio>
        <Radio value="hierarchy">Categorías + Subcategorías</Radio>
      </RadioGroup>
      
      {/* Gráfico y tabla */}
      {viewMode === 'hierarchy' ? (
        <HierarchicalChart data={salesData} />
      ) : (
        <FlatChart data={salesData} />
      )}
    </div>
  );
}
```

**Consumo de API:**
```javascript
// Obtener ventas agrupadas jerárquicamente
const analytics = await fetch(
  'API/analytics/sales-by-category?include_subcategories=true&start_date=...&end_date=...',
  { headers: { 'Authorization': `Bearer ${token}` } }
);

// Response esperado con jerarquía:
{
  "success": true,
  "data": [
    {
      "category": {
        "id": "uuid-vehiculos",
        "name": "Vehículos"
      },
      "total_sales": 35,
      "revenue_usd": 8500.00,
      "percentage": 68.0,
      "subcategories": [
        {
          "category": {
            "id": "uuid-bicicletas",
            "name": "Bicicletas",
            "parent_id": "uuid-vehiculos"
          },
          "total_sales": 20,
          "revenue_usd": 4200.00,
          "percentage": 49.4
        },
        {
          "category": {
            "id": "uuid-motos",
            "name": "Motos",
            "parent_id": "uuid-vehiculos"
          },
          "total_sales": 12,
          "revenue_usd": 3100.00,
          "percentage": 36.5
        }
      ]
    }
  ]
}
```

---

## 🔐 AUTENTICACIÓN Y SEGURIDAD

### 1. Manejo de Token JWT

**Guardar token al hacer login:**
```javascript
localStorage.setItem('auth_token', token);
localStorage.setItem('user', JSON.stringify(user));
```

**Interceptor global para todas las peticiones:**
```javascript
// Con Axios
axios.interceptors.request.use(config => {
  const token = localStorage.getItem('auth_token');
  if (token) {
    config.headers.Authorization = `Bearer ${token}`;
  }
  return config;
});

// Manejar 401 (token expirado)
axios.interceptors.response.use(
  response => response,
  error => {
    if (error.response?.status === 401) {
      localStorage.removeItem('auth_token');
      localStorage.removeItem('user');
      window.location.href = '/login';
    }
    return Promise.reject(error);
  }
);
```

### 2. Rutas Protegidas

**React Router ejemplo:**
```javascript
function ProtectedRoute({ children, requiredRole }) {
  const token = localStorage.getItem('auth_token');
  const user = JSON.parse(localStorage.getItem('user') || '{}');
  
  if (!token) {
    return <Navigate to="/login" />;
  }
  
  if (requiredRole && user.role !== requiredRole) {
    return <Navigate to="/dashboard" />;
  }
  
  return children;
}

// Uso:
<Route path="/configuracion/usuarios" element={
  <ProtectedRoute requiredRole="admin">
    <UsersPage />
  </ProtectedRoute>
} />
```

### 3. Permisos por Rol

**admin:**
- Acceso completo
- Puede eliminar categorías/productos
- Puede actualizar tasas de cambio
- Puede gestionar usuarios

**vendedor:**
- Puede ver productos
- Puede crear reservas
- Puede confirmar ventas
- **NO puede** eliminar categorías/productos
- **NO puede** actualizar tasas
- **NO puede** gestionar usuarios

**UI condicional:**
```javascript
const user = JSON.parse(localStorage.getItem('user'));

{user.role === 'admin' && (
  <Button onClick={handleDelete}>Eliminar</Button>
)}
```

---

---

## 📂 MANEJO DE JERARQUÍA DE CATEGORÍAS (CRÍTICO)

### Concepto de jerarquía:

```
Categoría Principal (parent_id = NULL)
  ├── Subcategoría 1 (parent_id = categoría_principal_id)
  ├── Subcategoría 2 (parent_id = categoría_principal_id)
  └── Subcategoría 3 (parent_id = categoría_principal_id)
```

**Ejemplo real:**
```
Vehículos (parent_id = NULL)
  ├── Bicicletas (parent_id = vehiculos_id)
  ├── Motos (parent_id = vehiculos_id)
  └── Accesorios (parent_id = vehiculos_id)

Electrodomésticos (parent_id = NULL)
  ├── Refrigeración (parent_id = electro_id)
  ├── Lavado (parent_id = electro_id)
  └── Cocina (parent_id = electro_id)
```

### Reglas importantes:

1. **Un producto SIEMPRE pertenece a UNA categoría** (puede ser principal o subcategoría)
2. **Las categorías pueden tener máximo 2 niveles** (principal → subcategoría)
3. **Al filtrar por categoría principal**, mostrar todos los productos de esa categoría Y sus subcategorías
4. **Al filtrar por subcategoría**, mostrar solo productos de esa subcategoría específica

### Implementación en filtros:

```javascript
function CategoryFilter() {
  const [categories, setCategories] = useState([]);      // Categorías principales
  const [subcategories, setSubcategories] = useState([]); // Subcategorías de la seleccionada
  const [selectedCategory, setSelectedCategory] = useState(null);
  const [selectedSubcategory, setSelectedSubcategory] = useState(null);
  
  // 1. Cargar categorías principales al inicio
  useEffect(() => {
    fetch('API/categories?parent_id=null')
      .then(res => res.json())
      .then(data => setCategories(data.data));
  }, []);
  
  // 2. Cargar subcategorías cuando se selecciona una categoría
  useEffect(() => {
    if (selectedCategory) {
      fetch(`API/categories?parent_id=${selectedCategory}`)
        .then(res => res.json())
        .then(data => setSubcategories(data.data));
    } else {
      setSubcategories([]);
      setSelectedSubcategory(null);
    }
  }, [selectedCategory]);
  
  // 3. Determinar qué ID usar para el filtro
  const getFilterCategoryId = () => {
    // Prioridad: Subcategoría > Categoría > null
    return selectedSubcategory || selectedCategory || null;
  };
  
  // 4. Aplicar filtro
  const handleApplyFilter = () => {
    const categoryId = getFilterCategoryId();
    
    // La API se encarga de:
    // - Si es subcategoría: filtrar solo por ella
    // - Si es categoría principal: filtrar por ella Y todas sus subcategorías
    fetchFilteredData(categoryId);
  };
  
  return (
    <div>
      {/* Dropdown 1: Categorías principales */}
      <Select
        placeholder="Todas las categorías"
        value={selectedCategory}
        onChange={(value) => {
          setSelectedCategory(value);
          setSelectedSubcategory(null); // Reset
        }}
        options={categories}
      />
      
      {/* Dropdown 2: Subcategorías (solo si hay categoría seleccionada) */}
      {selectedCategory && subcategories.length > 0 && (
        <Select
          placeholder="Todas las subcategorías"
          value={selectedSubcategory}
          onChange={setSelectedSubcategory}
          options={subcategories}
        />
      )}
      
      {/* Indicador visual de lo que está filtrado */}
      {selectedCategory && (
        <Badge>
          {selectedSubcategory 
            ? `${getCategoryName(selectedCategory)} > ${getCategoryName(selectedSubcategory)}`
            : `${getCategoryName(selectedCategory)} (todas las subcategorías)`
          }
        </Badge>
      )}
    </div>
  );
}
```

### Mostrar jerarquía en tablas/listados:

```javascript
// En cada fila, mostrar la jerarquía completa
function ProductRow({ product }) {
  return (
    <tr>
      <td>{product.name}</td>
      <td>
        {/* Mostrar jerarquía completa */}
        <div className="text-sm text-gray-600">
          {product.category_hierarchy}
          {/* Ejemplo: "Vehículos > Bicicletas" */}
        </div>
      </td>
      <td>{product.stock}</td>
      <td>{product.price}</td>
    </tr>
  );
}
```

### Breadcrumbs para navegación:

```javascript
function CategoryBreadcrumb({ categoryId, subcategoryId }) {
  const [category, setCategory] = useState(null);
  const [subcategory, setSubcategory] = useState(null);
  
  useEffect(() => {
    if (categoryId) {
      fetch(`API/categories/${categoryId}`)
        .then(res => res.json())
        .then(data => setCategory(data.data));
    }
    
    if (subcategoryId) {
      fetch(`API/categories/${subcategoryId}`)
        .then(res => res.json())
        .then(data => setSubcategory(data.data));
    }
  }, [categoryId, subcategoryId]);
  
  return (
    <nav className="breadcrumb">
      <a href="/productos">Todos los productos</a>
      {category && (
        <>
          <span> &gt; </span>
          <a href={`/productos?category=${category.id}`}>
            {category.name}
          </a>
        </>
      )}
      {subcategory && (
        <>
          <span> &gt; </span>
          <span className="current">{subcategory.name}</span>
        </>
      )}
    </nav>
  );
}
```

### API debe devolver jerarquía en respuestas:

**Ejemplo en GET /products/{id}:**
```json
{
  "id": "uuid",
  "name": "Bicicleta MTB Aro 26",
  "category": {
    "id": "uuid-bicicletas",
    "name": "Bicicletas",
    "parent_id": "uuid-vehiculos",
    "parent": {
      "id": "uuid-vehiculos",
      "name": "Vehículos"
    }
  },
  "category_hierarchy": "Vehículos > Bicicletas"
}
```

Esta estructura permite:
1. ✅ Mostrar la jerarquía completa en la UI
2. ✅ Generar breadcrumbs automáticamente
3. ✅ Filtrar correctamente en cualquier pantalla
4. ✅ Mantener consistencia en toda la aplicación

---

## 🎨 COMPONENTES REUTILIZABLES

### 1. StockBadge
```jsx
function StockBadge({ quantity }) {
  const getColor = () => {
    if (quantity === 0) return 'bg-red-100 text-red-800';
    if (quantity < 5) return 'bg-yellow-100 text-yellow-800';
    return 'bg-green-100 text-green-800';
  };
  
  const getText = () => {
    if (quantity === 0) return 'Agotado';
    if (quantity < 5) return `${quantity} unidades`;
    return 'Disponible';
  };
  
  return (
    <span className={`px-2 py-1 rounded-full text-xs ${getColor()}`}>
      {getText()}
    </span>
  );
}
```

### 2. PriceDisplay
```jsx
function PriceDisplay({ prices, highlight = 'usd' }) {
  return (
    <div className="space-y-1">
      <div className="font-bold text-lg">
        ${prices.usd.toFixed(2)} USD
      </div>
      <div className="text-sm text-gray-600">
        ${prices.cup.toFixed(2)} CUP
      </div>
    </div>
  );
}
```

### 3. ConfirmDialog
```jsx
function ConfirmDialog({ isOpen, title, message, onConfirm, onCancel }) {
  if (!isOpen) return null;
  
  return (
    <div className="modal-overlay">
      <div className="modal-content">
        <h3>{title}</h3>
        <p>{message}</p>
        <div className="flex gap-2">
          <button onClick={onCancel}>Cancelar</button>
          <button onClick={onConfirm} className="btn-danger">
            Confirmar
          </button>
        </div>
      </div>
    </div>
  );
}
```

### 4. DataTable
```jsx
function DataTable({ columns, data, onRowClick }) {
  return (
    <table className="w-full">
      <thead>
        <tr>
          {columns.map(col => (
            <th key={col.key}>{col.label}</th>
          ))}
        </tr>
      </thead>
      <tbody>
        {data.map(row => (
          <tr key={row.id} onClick={() => onRowClick(row)}>
            {columns.map(col => (
              <td key={col.key}>
                {col.render ? col.render(row) : row[col.key]}
              </td>
            ))}
          </tr>
        ))}
      </tbody>
    </table>
  );
}
```

---

## 📱 RESPONSIVE DESIGN

### Breakpoints:
- **Desktop:** > 1024px - Sidebar fijo, layout completo
- **Tablet:** 768px - 1024px - Sidebar colapsable
- **Móvil:** < 768px - Menú hamburguesa, diseño vertical

### Adaptaciones móviles:
- Tablas se convierten en cards verticales
- Formularios apilados
- Botones más grandes (mínimo 44x44px)
- Menú hamburguesa en vez de sidebar

---

## ⚡ OPTIMIZACIONES

### 1. Loading States
```jsx
function ProductList() {
  const [loading, setLoading] = useState(true);
  const [products, setProducts] = useState([]);
  
  useEffect(() => {
    fetchProducts().then(data => {
      setProducts(data);
      setLoading(false);
    });
  }, []);
  
  if (loading) return <Spinner />;
  
  return <ProductGrid products={products} />;
}
```

### 2. Error Handling
```javascript
try {
  const response = await fetch('API/products');
  const data = await response.json();
  
  if (!data.success) {
    throw new Error(data.error.message);
  }
  
  return data.data;
} catch (error) {
  toast.error(error.message || 'Error al cargar productos');
}
```

### 3. Debounce en búsquedas
```javascript
import { debounce } from 'lodash';

const debouncedSearch = debounce((query) => {
  fetchProducts({ search: query });
}, 300);

<input onChange={(e) => debouncedSearch(e.target.value)} />
```

---

## 🎯 ENTREGABLES ESPERADOS

Por favor, genera:

1. **Estructura completa del proyecto** (carpetas y archivos)
2. **Todas las pantallas principales** (Dashboard, Productos, Ventas, etc.)
3. **Componentes reutilizables** (Tablas, Modales, Badges, etc.)
4. **Servicios de API** (con manejo de errores y autenticación)
5. **Manejo de estado** (Context o Redux según elijas)
6. **Rutas protegidas** con autenticación
7. **Diseño responsive** funcional
8. **README.md** con instrucciones de setup
9. **Archivo .env.example** con variables necesarias

---

## 🚀 STACK TECNOLÓGICO

Mi preferencia es:
- **Framework:** [React / Vue / Next.js - especifica]
- **Styling:** Tailwind CSS + shadcn/ui (o especifica otro)
- **State:** Context API / Zustand / Redux
- **Forms:** React Hook Form / VeeValidate
- **Charts:** Recharts / Chart.js
- **Icons:** Lucide React / Heroicons

**Si prefieres otro stack, especifícalo.**

---

## 📦 ESTRUCTURA SUGERIDA (React)

```
master-app/
├── public/
├── src/
│   ├── components/
│   │   ├── common/
│   │   │   ├── Button.jsx
│   │   │   ├── Modal.jsx
│   │   │   ├── DataTable.jsx
│   │   │   ├── StockBadge.jsx
│   │   │   └── PriceDisplay.jsx
│   │   ├── layout/
│   │   │   ├── Sidebar.jsx
│   │   │   ├── Header.jsx
│   │   │   └── Layout.jsx
│   │   └── forms/
│   │       ├── ProductForm.jsx
│   │       ├── ReservationForm.jsx
│   │       └── SaleForm.jsx
│   ├── pages/
│   │   ├── Login.jsx
│   │   ├── Dashboard.jsx
│   │   ├── Products/
│   │   │   ├── ProductList.jsx
│   │   │   ├── ProductCreate.jsx
│   │   │   └── ProductEdit.jsx
│   │   ├── Reservations/
│   │   ├── Sales/
│   │   ├── Customers/
│   │   ├── ExchangeRates/
│   │   └── Analytics/
│   ├── services/
│   │   ├── api.js
│   │   ├── authService.js
│   │   ├── productService.js
│   │   ├── saleService.js
│   │   └── analyticsService.js
│   ├── context/
│   │   └── AuthContext.jsx
│   ├── hooks/
│   │   ├── useAuth.js
│   │   └── useApi.js
│   ├── utils/
│   │   ├── formatters.js
│   │   └── validators.js
│   ├── App.jsx
│   └── main.jsx
├── .env.example
├── package.json
└── README.md
```

---

## 💾 VARIABLES DE ENTORNO

```env
VITE_API_BASE_URL=https://api.maybel.cu/v1
VITE_FILE_STORAGE_URL=https://maybel.cu/uploads
```

---

## 🚦 PRIORIDADES DE DESARROLLO

### Fase 1 (MVP):
1. Login/Logout
2. Dashboard básico
3. Lista de productos
4. Crear/editar producto simple
5. Ver stock
6. Gestión de variantes
7. Subida de imágenes
8. Crear reservas
9. Confirmar ventas
10. Clientes
11. Historial de ventas
12. Tasas de cambio
13. Analíticas básicas

### Fase 2:
14. Analíticas avanzadas
15. Exportar reportes
16. Optimizaciones UI/UX

