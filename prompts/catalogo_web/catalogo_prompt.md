# PROMPT PARA DESARROLLO DEL CATÁLOGO WEB - PLATAFORMA MAYBEL

Necesito que me ayudes a diseñar y desarrollar el **Catálogo Web** para la Plataforma Maybel, un sistema de gestión de productos para el mercado cubano.

---

## 📋 CONTEXTO DEL PROYECTO

### ¿Qué es Plataforma Maybel?
Es un ecosistema digital para gestión y promoción de productos físicos (electrodomésticos, muebles, vehículos, etc.) orientado al mercado cubano. El sistema consta de:

1. **API Central** - Backend que maneja toda la lógica de negocio y base de datos
2. **Sistema Master** - Aplicación administrativa interna (NO es parte de este desarrollo)
3. **Catálogo Web** - **LO QUE NECESITO QUE DESARROLLES** - Sitio público para visualizar productos

### Características del mercado cubano:
- ❌ **NO hay pagos en línea** (no existe pasarela de pago)
- ❌ **NO hay carrito de compras** (no se compra desde el sitio)
- ✅ **El catálogo es solo informativo/consultivo**
- ✅ Los clientes ven productos y contactan por **WhatsApp, Telegram o teléfono**
- ✅ Se manejan **múltiples monedas** (USD, CUP, EUR, MLC, etc.)

---

## 🎯 OBJETIVO DEL CATÁLOGO WEB

### ¿Qué ES el catálogo?
- Sitio web público para que clientes **naveguen y vean productos disponibles**
- Muestra **categorías, productos, variantes (colores), precios y stock**
- Proporciona **información de contacto** para que clientes pregunten/compren
- Es **responsive** (móvil, tablet, desktop)
- Es **rápido y simple** de navegar

### ¿Qué NO ES el catálogo?
- ❌ NO es una tienda online (no hay checkout)
- ❌ NO procesa pagos
- ❌ NO tiene login de usuarios (es público)
- ❌ NO tiene carrito de compras
- ❌ NO permite hacer pedidos desde el sitio

---

## 🏗️ ARQUITECTURA TÉCNICA

### Backend (YA EXISTE - Solo consumir)
- **Base URL de la API:** `https://api.maybel.cu/v1`
- El catálogo **SOLO consume** datos de la API mediante GET requests
- **NO requiere autenticación** (los endpoints públicos están abiertos)

### Endpoints disponibles para el catálogo:

```
GET /categories
GET /categories/{id}
GET /products?category_id={uuid}&page=1&limit=20
GET /products/{id}
GET /exchange-rates/current
```

### Stack tecnológico sugerido:
- **Frontend:** React, Vue.js o Next.js (tú decides lo mejor)
- **Styling:** Tailwind CSS (preferido) o CSS/SCSS
- **Fetching:** Axios o Fetch API
- **Hosting:** Vercel, Netlify o servidor estático

---

## 🎨 REQUISITOS DE DISEÑO Y UX

### Principios de diseño:
1. **Mobile-first** - La mayoría de usuarios en Cuba navegan desde móviles
2. **Carga rápida** - Conexión a internet puede ser lenta
3. **Imágenes optimizadas** - Usar lazy loading y thumbnails
4. **Diseño limpio y claro** - Usuarios deben encontrar productos fácilmente
5. **Botones de contacto visibles** - WhatsApp/Telegram siempre accesibles

### Paleta de colores sugerida:
- Usa colores **profesionales pero accesibles**
- Considera el contexto cubano (evita diseños muy corporativos/fríos)
- Sugerencia: Tonos cálidos que transmitan confianza

### Tipografía:
- **Clara y legible** en pantallas pequeñas
- Tamaños accesibles (mínimo 16px para texto normal)

---

## 📐 ESTRUCTURA DE NAVEGACIÓN

### Páginas principales:

```
1. Página de inicio (/)
   ├── Banner/Hero con mensaje de bienvenida
   ├── Categorías destacadas
   ├── Productos destacados/nuevos
   └── Información de contacto

2. Listado de categorías (/categorias)
   ├── Todas las categorías principales
   └── Subcategorías de cada una

3. Productos por categoría (/categoria/{id})
   ├── Breadcrumb (Inicio > Vehículos > Bicicletas)
   ├── Filtros (precio, disponibilidad)
   ├── Grid de productos
   └── Paginación

4. Detalle de producto (/producto/{id})
   ├── Galería de imágenes
   ├── Nombre y descripción
   ├── Selector de variantes (colores)
   ├── Precios en todas las monedas
   ├── Disponibilidad por variante
   ├── Botones de contacto
   └── Productos relacionados

5. Búsqueda (/buscar?q=texto)
   └── Resultados de búsqueda

6. Contacto (/contacto)
   └── Información de contacto y redes sociales
```

---

## 🛍️ COMPONENTES CLAVE Y FUNCIONALIDADES

### 1. PÁGINA DE INICIO

**Elementos:**
- **Hero/Banner:**
  - Título: "Bienvenido a Maybel"
  - Subtítulo: "Tu tienda de confianza en Cuba"
  - Buscador prominente
  - Botón CTA: "Ver productos"

- **Categorías destacadas:**
  - Grid de 4-6 categorías principales con imágenes
  - Al hacer clic, lleva a /categoria/{id}

- **Productos destacados:**
  - Carrusel o grid con 8-12 productos
  - Mostrar: imagen, nombre, precio (en 2-3 monedas principales), stock

- **Footer:**
  - Información de contacto (WhatsApp, Telegram, teléfono)
  - Horarios de atención
  - Enlaces rápidos
  - Redes sociales

---

### 2. LISTADO DE PRODUCTOS

**Estructura:**
```
┌─────────────────────────────────────────┐
│  Inicio > Vehículos > Bicicletas        │  ← Breadcrumb
├─────────────────────────────────────────┤
│  [Filtros]  │  Grid de Productos        │
│             │                           │
│  Precio:    │  ┌────┐ ┌────┐ ┌────┐   │
│  [ ] < $100 │  │Img │ │Img │ │Img │   │
│  [ ] $100+  │  │    │ │    │ │    │   │
│             │  └────┘ └────┘ └────┘   │
│  Stock:     │  Nombre   Nombre  Nombre  │
│  [x] Dispo. │  $150     $200    $180    │
│             │  Stock: 5 Stock: 2 Stock:10│
│             │                           │
│             │  ┌────┐ ┌────┐ ┌────┐   │
│             │  ...más productos...      │
└─────────────────────────────────────────┘
```

**Características:**
- Grid responsive (1 col en móvil, 2-3 en tablet, 3-4 en desktop)
- Cada tarjeta de producto muestra:
  - Imagen principal
  - Nombre del producto
  - Precio en **USD y CUP** (mínimo)
  - Indicador de stock ("Disponible" / "Stock bajo" / "Agotado")
  - Botón "Ver detalles"

- **Filtros (sidebar o dropdown en móvil):**
  - Por rango de precio
  - Por disponibilidad
  - Ordenar por: Precio (menor/mayor), Nombre (A-Z)

- **Paginación:**
  - 20 productos por página
  - Botones: « Anterior | 1 2 3 ... | Siguiente »

---

### 3. DETALLE DE PRODUCTO

**Layout sugerido:**

```
┌─────────────────────────────────────────────────────┐
│  Inicio > Vehículos > Bicicletas > MTB Aro 26       │
├──────────────────────┬──────────────────────────────┤
│                      │                              │
│  ┌────────────────┐  │  Bicicleta MTB Aro 26       │
│  │                │  │  ────────────────────────    │
│  │  Imagen        │  │                              │
│  │  Principal     │  │  Descripción detallada...    │
│  │                │  │  Cuadro de aluminio...       │
│  └────────────────┘  │                              │
│                      │  ⚙️ Selecciona color:        │
│  [🔴] [🔵] [⚫]     │  ○ Rojo (5 disponibles)      │
│  Miniaturas         │  ○ Azul (3 disponibles)      │
│                      │  ○ Negro (Agotado)           │
│                      │                              │
│                      │  💰 Precios:                 │
│                      │  USD: $150.00                │
│                      │  CUP: $4,500.00              │
│                      │  EUR: €140.00                │
│                      │  [Ver más monedas ▼]         │
│                      │                              │
│                      │  📦 Stock: 8 unidades        │
│                      │                              │
│                      │  ────────────────────────    │
│                      │  [💬 Consultar por WhatsApp] │
│                      │  [📱 Consultar por Telegram] │
│                      │  [📞 Llamar ahora]           │
└──────────────────────┴──────────────────────────────┘

┌─────────────────────────────────────────────────────┐
│  Productos relacionados                             │
│  [Producto 1]  [Producto 2]  [Producto 3]           │
└─────────────────────────────────────────────────────┘
```

**Funcionalidad de variantes (colores):**
- Al seleccionar un color diferente:
  - Actualiza stock disponible
  - Puede cambiar imágenes (si ese color tiene imágenes específicas)
  - **NO cambia el precio** (precio es por producto, no por variante)

**Galería de imágenes:**
- Imagen principal grande
- Miniaturas abajo (scroll horizontal en móvil)
- Al hacer clic en miniatura, cambia la principal
- Opción de zoom (opcional pero recomendado)

**Precios:**
- Mostrar por defecto: USD y CUP (las más usadas)
- Botón "Ver más monedas" despliega: EUR, MLC, MXN, CAD, BRL, Zelle, Transferencia CUP, Tarjeta CUP
- Los precios vienen directamente del endpoint `/products/{id}`

**Botones de contacto:**
- **WhatsApp:** Enlace a `https://wa.me/53XXXXXXXX?text=Hola, me interesa el producto: {nombre_producto}`
- **Telegram:** Enlace a `https://t.me/usuario_telegram`
- **Teléfono:** `tel:+53XXXXXXXX`

Estos botones deben ser **MUY VISIBLES** y estar siempre accesibles (sticky en móvil si es posible).

---

### 4. BARRA DE NAVEGACIÓN (NAVBAR)

**Elementos:**
- **Logo** (izquierda): Enlace a /
- **Buscador** (centro en desktop, icono en móvil)
- **Menú de categorías** (dropdown)
- **Iconos de contacto** rápido (WhatsApp, Telegram)

**Versión móvil:**
- Menú hamburguesa
- Buscador en la parte superior cuando se abre el menú

---

### 5. BUSCADOR

**Funcionalidad:**
- Input con icono de lupa
- Búsqueda en tiempo real (opcional: sugerencias mientras escribes)
- Al enviar: Redirige a `/buscar?q={texto}`
- Busca en: nombres de productos y descripciones

**Página de resultados:**
- Mismo formato que listado de productos
- Mensaje: "Resultados para: {término}"
- Si no hay resultados: "No encontramos productos con '{término}'. Te sugerimos..."

---

### 6. INDICADORES DE STOCK

**Estados posibles:**
- **✅ Disponible:** stock >= 5 → Badge verde "Disponible"
- **⚠️ Stock bajo:** stock 1-4 → Badge amarillo "Quedan X unidades"
- **❌ Agotado:** stock = 0 → Badge rojo "Agotado"

En productos con variantes, mostrar el estado de la variante seleccionada.

---

## 📱 RESPONSIVE DESIGN

### Breakpoints sugeridos:
- **Móvil:** < 640px
- **Tablet:** 640px - 1024px
- **Desktop:** > 1024px

### Ajustes por dispositivo:

**Móvil:**
- Navegación hamburguesa
- Grid de productos: 1 columna
- Galería de imágenes: swipe horizontal
- Botones de contacto sticky al final de la página
- Precios apilados verticalmente

**Tablet:**
- Grid de productos: 2 columnas
- Navbar completa con categorías visibles

**Desktop:**
- Grid de productos: 3-4 columnas
- Sidebar de filtros visible
- Galería con miniaturas a un lado

---

## 🔄 CONSUMO DE LA API

### Ejemplo de código para consumir productos:

```javascript
// Obtener productos de una categoría
async function getProducts(categoryId, page = 1) {
  const response = await fetch(
    `https://api.maybel.cu/v1/products?category_id=${categoryId}&page=${page}&limit=20`
  );
  const data = await response.json();
  return data;
}

// Response esperado:
{
  "success": true,
  "data": [
    {
      "id": "uuid",
      "name": "Bicicleta MTB Aro 26",
      "description": "Bicicleta de montaña...",
      "category": {
        "id": "uuid-cat",
        "name": "Bicicletas"
      },
      "prices": {
        "usd": 150.00,
        "cup": 4500.00,
        "eur": 140.00,
        // ... más monedas
      },
      "total_stock": 8,
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

### Ejemplo para detalle de producto:

```javascript
// Obtener detalle completo
async function getProductDetail(productId) {
  const response = await fetch(
    `https://api.maybel.cu/v1/products/${productId}`
  );
  const data = await response.json();
  return data.data;
}

// Response esperado:
{
  "id": "uuid",
  "name": "Bicicleta MTB Aro 26",
  "description": "Descripción completa...",
  "category": { /* ... */ },
  "prices": { /* todos los precios */ },
  "images": [
    {
      "id": "uuid-img",
      "url": "/uploads/products/uuid/1.webp",
      "position": 1
    },
    {
      "id": "uuid-img-2",
      "url": "/uploads/products/uuid/2.webp",
      "position": 2
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
      "id": "uuid-var-2",
      "color": "Azul",
      "stock": 3,
      "active": true
    },
    {
      "id": "uuid-var-3",
      "color": "Negro",
      "stock": 0,
      "active": true
    }
  ],
  "total_stock": 8
}
```

---

## 🎨 ELEMENTOS VISUALES ESPECÍFICOS

### Tarjeta de producto (componente reutilizable):

```jsx
<ProductCard>
  <Image src={product.main_image} alt={product.name} />
  <ProductName>{product.name}</ProductName>
  <PriceContainer>
    <Price currency="USD">${product.prices.usd}</Price>
    <Price currency="CUP">${product.prices.cup} CUP</Price>
  </PriceContainer>
  <StockBadge status={getStockStatus(product.total_stock)}>
    {product.total_stock > 0 ? 'Disponible' : 'Agotado'}
  </StockBadge>
  <ViewDetailsButton href={`/producto/${product.id}`}>
    Ver detalles
  </ViewDetailsButton>
</ProductCard>
```

### Selector de variantes:

```jsx
<VariantSelector>
  <Label>Selecciona color:</Label>
  <VariantOptions>
    {variants.map(variant => (
      <VariantOption 
        key={variant.id}
        selected={selectedVariant === variant.id}
        disabled={variant.stock === 0}
        onClick={() => setSelectedVariant(variant.id)}
      >
        <ColorCircle color={variant.color} />
        <VariantName>{variant.color}</VariantName>
        <Stock>({variant.stock} disponibles)</Stock>
      </VariantOption>
    ))}
  </VariantOptions>
</VariantSelector>
```

---

## ⚡ OPTIMIZACIONES IMPORTANTES

### Performance:
1. **Lazy loading de imágenes** - Cargar solo cuando sean visibles
2. **Paginación** - Nunca cargar más de 20 productos a la vez
3. **Cache de categorías** - Las categorías cambian poco, cachear en localStorage
4. **Thumbnails** - Usar versión pequeña de imágenes en listados

### SEO básico:
- Títulos descriptivos en cada página
- Meta descriptions con palabras clave
- URLs amigables (/categoria/bicicletas en vez de /cat?id=123)
- Alt text en todas las imágenes

### Accesibilidad:
- Contraste adecuado en textos
- Botones con tamaño mínimo de 44x44px (táctil)
- Labels en inputs
- Navegación por teclado funcional

---

## 🚫 RESTRICCIONES Y COSAS A EVITAR

❌ **NO implementar:**
- Sistema de login/registro
- Carrito de compras
- Proceso de checkout
- Pasarela de pago
- Wishlist/favoritos (a menos que sea solo local con localStorage)
- Sistema de comentarios/reseñas

❌ **NO consumir endpoints que requieran autenticación:**
- POST, PUT, DELETE de la API
- Endpoints de /auth/, /sales/, /reservations/, etc.

✅ **SÍ puedes agregar (opcional):**
- Comparador de productos (cliente)
- Filtros avanzados
- Compartir en redes sociales
- Modo oscuro
- Selector de moneda preferida (guardar en localStorage)

---

## 📞 INFORMACIÓN DE CONTACTO (PLACEHOLDER)

Estos datos son ejemplos, deben ser configurables:

- **WhatsApp:** +53 5555-5555
- **Telegram:** @MaybelCuba
- **Teléfono:** +53 7 555-5555
- **Email:** contacto@maybel.cu
- **Dirección:** La Habana, Cuba
- **Horario:** Lunes a Sábado 9:00 - 18:00

---

## 🎯 ENTREGABLES ESPERADOS

Por favor, genera:

1. **Estructura del proyecto completo** (carpetas y archivos)
2. **Código de los componentes principales:**
   - Navbar
   - ProductCard
   - ProductDetail
   - CategoryList
   - Footer
3. **Páginas principales:**
   - Home
   - Listado de productos
   - Detalle de producto
4. **Servicios/API calls** para consumir los endpoints
5. **Estilos responsive** (Tailwind o CSS)
6. **Instrucciones de instalación y ejecución**

---

## 🚀 TECNOLOGÍA SUGERIDA

Mi preferencia es:
- **Framework:** [React / Vue / Next.js - especifica cuál prefieres]
- **Styling:** Tailwind CSS
- **Routing:** React Router / Vue Router / Next.js routing
- **State Management:** Context API / Pinia / Zustand (solo si es necesario)

Pero siéntete libre de sugerir el stack que consideres más adecuado para este proyecto.

---

## ❓ PREGUNTAS FINALES

Antes de empezar, ¿necesitas alguna aclaración sobre:
- La estructura de datos de la API?
- Los flujos de navegación?
- El diseño visual esperado?
- Funcionalidades específicas?

Si todo está claro, **procede a generar el código completo del catálogo web** siguiendo todas las especificaciones anteriores.

¡Gracias!