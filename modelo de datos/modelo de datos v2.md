Creado por Claude code en el proyecto maybel-un ecosistema de negocio Iniciando desarrollo: API y modelo de datos del proyecto
-- ============================================
-- MODELO DE DATOS DEFINITIVO - PLATAFORMA MAYBEL
-- Sistema de catálogo, inventario y ventas multimoneda
-- ============================================

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
    -- Precios en todas las monedas (solo precio actual)
    price_usd NUMERIC(10,2) NOT NULL,
    price_eur NUMERIC(10,2),
    price_cup NUMERIC(10,2),
    price_mlc NUMERIC(10,2),
    price_mxn NUMERIC(10,2),
    price_canad NUMERIC(10,2),
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
-- VARIANTES (Colores, versiones, etc.)
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
    -- Tasas respecto al USD (USD siempre es 1.0)
    rate_eur NUMERIC(10,6) NOT NULL,
    rate_cup NUMERIC(10,6) NOT NULL,
    rate_mlc NUMERIC(10,6) NOT NULL,
    rate_mxn NUMERIC(10,6) NOT NULL,
    rate_cad NUMERIC(10,6) NOT NULL,
    rate_brl NUMERIC(10,6) NOT NULL,
    rate_zelle NUMERIC(10,6) NOT NULL,
    rate_transfer_cup NUMERIC(10,6) NOT NULL,
    rate_card_cup NUMERIC(10,6) NOT NULL,
    valid_from TIMESTAMP NOT NULL DEFAULT NOW(),
    valid_to TIMESTAMP, -- NULL = tasas actuales
    created_by UUID REFERENCES users(id),
    notes TEXT
);

-- ============================================
-- CLIENTES(esto es para las reservas y tal)
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
    -- Información de moneda y precios
    currency_used VARCHAR(10) NOT NULL,
    unit_price_currency NUMERIC(10,2) NOT NULL,
    unit_price_usd NUMERIC(10,2) NOT NULL,
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
-- ÍNDICES PARA PERFORMANCE
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

-- ============================================
-- DATOS DE EJEMPLO - TASAS INICIALES
-- ============================================
-- Descomentar para insertar tasas iniciales:
/*
INSERT INTO exchange_rates (
    rate_eur, rate_cup, rate_mlc, rate_mxn, rate_cad, 
    rate_brl, rate_zelle, rate_transfer_cup, rate_card_cup, notes
) VALUES (
    1.08, 0.033, 1.00, 0.059, 0.74, 
    0.20, 1.00, 0.031, 0.030, 
    'Tasas iniciales - Diciembre 2024'
);
*/
