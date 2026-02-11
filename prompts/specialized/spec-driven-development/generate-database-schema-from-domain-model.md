---
type: specialized
complexity: intermediate
context: technical
output_format: code
interaction_mode: single-shot
production_ready: experimental
tags: [sdd, database, schema, sql, data-model, migrations]
---

# Generate Database Schema from Domain Model

## Purpose
Transform domain models into production-ready database schemas with tables, indexes, constraints, and migration scripts.

## Context
Use this prompt when:
- Converting entity models into database tables
- Need optimized schema for relational databases
- Creating database migrations from scratch
- Ensuring data integrity with constraints

---

## Prompt

You are a database architect specializing in schema design. Generate a complete database schema from domain models with indexes, constraints, and migrations.

**INPUT**:
- Domain entities with attributes and relationships
- Database type (PostgreSQL, MySQL, etc.)
- Optional: Performance requirements, data volume estimates

**GENERATE**:

1. **Table Definitions**:
   - Column names and data types
   - Primary keys (UUIDs vs auto-increment)
   - Foreign keys with referential actions
   - NOT NULL and DEFAULT constraints
   - CHECK constraints for validation

2. **Indexes**:
   - Primary key indexes
   - Foreign key indexes (for JOIN performance)
   - Unique indexes
   - Composite indexes for common queries
   - Partial indexes for filtered queries

3. **Relationships**:
   - One-to-one (foreign key + unique constraint)
   - One-to-many (foreign key)
   - Many-to-many (junction table)
   - Self-referential relationships

4. **Data Integrity**:
   - Referential integrity (ON DELETE/UPDATE actions)
   - Domain validation (CHECK constraints)
   - Uniqueness constraints
   - Default values and auto-generation

5. **Performance Optimizations**:
   - Index selection based on query patterns
   - Denormalization considerations
   - Partitioning strategy (if needed)

6. **Migration Scripts**:
   - UP migration (create schema)
   - DOWN migration (rollback)
   - Seed data (if applicable)

**OUTPUT FORMAT**:
```sql
-- Migration: [001_initial_schema.sql]
-- Description: [What this migration does]
-- Database: [PostgreSQL 15 / MySQL 8 / etc.]

-- ============================================================
-- TABLES
-- ============================================================

CREATE TABLE [table_name] (
  [column_name] [DATA_TYPE] [CONSTRAINTS],
  [column_name2] [DATA_TYPE] [CONSTRAINTS],

  -- Primary Key
  PRIMARY KEY ([column]),

  -- Foreign Keys
  FOREIGN KEY ([column]) REFERENCES [other_table]([column])
    ON DELETE [CASCADE|SET NULL|RESTRICT]
    ON UPDATE [CASCADE|RESTRICT],

  -- Check Constraints
  CONSTRAINT [constraint_name] CHECK ([condition]),

  -- Unique Constraints
  UNIQUE ([column1], [column2])
);

-- ============================================================
-- INDEXES
-- ============================================================

-- Index for [purpose]
CREATE INDEX [idx_name] ON [table]([column]);

-- Composite index for [query pattern]
CREATE INDEX [idx_name] ON [table]([column1], [column2]);

-- Unique index for [uniqueness constraint]
CREATE UNIQUE INDEX [idx_name] ON [table]([column]);

-- Partial index for [filtered queries]
CREATE INDEX [idx_name] ON [table]([column]) WHERE [condition];

-- ============================================================
-- COMMENTS (Documentation)
-- ============================================================

COMMENT ON TABLE [table] IS '[Description of table purpose]';
COMMENT ON COLUMN [table].[column] IS '[Description of column]';

-- ============================================================
-- ROLLBACK (DOWN MIGRATION)
-- ============================================================

DROP TABLE IF EXISTS [table] CASCADE;
```

**CONSTRAINTS**:
- Use appropriate data types (UUID, TIMESTAMP, DECIMAL for money)
- Index all foreign keys for JOIN performance
- Use ON DELETE CASCADE carefully (prefer SET NULL or RESTRICT)
- Add CHECK constraints for business rules
- Document complex logic with comments
- Consider query patterns when creating indexes

---

## Example

**Input Domain Model**:
```
Entities:
1. User
   - id (UUID)
   - email (unique, required)
   - name (required)
   - age (18-120)
   - created_at, updated_at

2. Order
   - id (UUID)
   - user_id (FK → User)
   - status (pending, completed, cancelled)
   - total_amount (decimal)
   - created_at, updated_at

3. OrderItem
   - id (UUID)
   - order_id (FK → Order)
   - product_id (FK → Product)
   - quantity (positive integer)
   - unit_price (decimal)

4. Product
   - id (UUID)
   - name (required, unique)
   - price (decimal, positive)
   - stock (integer, non-negative)
   - created_at, updated_at

Relationships:
- User 1:N Orders
- Order 1:N OrderItems
- Product 1:N OrderItems (many orders can have same product)
```

**Output**:

```sql
-- ============================================================
-- Migration: 001_create_ecommerce_schema.sql
-- Description: Initial schema for e-commerce platform
-- Database: PostgreSQL 15
-- Author: Schema Generator
-- Date: 2024-01-20
-- ============================================================

-- Enable UUID extension (PostgreSQL)
CREATE EXTENSION IF NOT EXISTS "pgcrypto";

-- ============================================================
-- TABLES
-- ============================================================

-- -------------------------------------------------------------
-- Table: users
-- Purpose: Store user account information
-- -------------------------------------------------------------
CREATE TABLE users (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  email VARCHAR(254) NOT NULL,
  name VARCHAR(100) NOT NULL,
  age INTEGER NOT NULL,
  created_at TIMESTAMP NOT NULL DEFAULT NOW(),
  updated_at TIMESTAMP NOT NULL DEFAULT NOW(),

  -- Constraints
  CONSTRAINT users_email_unique UNIQUE (email),
  CONSTRAINT users_email_format CHECK (email ~* '^[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Z|a-z]{2,}$'),
  CONSTRAINT users_name_length CHECK (LENGTH(name) >= 2),
  CONSTRAINT users_age_range CHECK (age BETWEEN 18 AND 120)
);

-- -------------------------------------------------------------
-- Table: products
-- Purpose: Store product catalog
-- -------------------------------------------------------------
CREATE TABLE products (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  name VARCHAR(200) NOT NULL,
  price DECIMAL(10, 2) NOT NULL,
  stock INTEGER NOT NULL DEFAULT 0,
  created_at TIMESTAMP NOT NULL DEFAULT NOW(),
  updated_at TIMESTAMP NOT NULL DEFAULT NOW(),

  -- Constraints
  CONSTRAINT products_name_unique UNIQUE (name),
  CONSTRAINT products_price_positive CHECK (price > 0),
  CONSTRAINT products_stock_non_negative CHECK (stock >= 0)
);

-- -------------------------------------------------------------
-- Table: orders
-- Purpose: Store customer orders
-- -------------------------------------------------------------
CREATE TABLE orders (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID NOT NULL,
  status VARCHAR(20) NOT NULL DEFAULT 'pending',
  total_amount DECIMAL(12, 2) NOT NULL,
  created_at TIMESTAMP NOT NULL DEFAULT NOW(),
  updated_at TIMESTAMP NOT NULL DEFAULT NOW(),

  -- Foreign Keys
  CONSTRAINT orders_user_fk
    FOREIGN KEY (user_id) REFERENCES users(id)
    ON DELETE RESTRICT  -- Prevent deleting user with orders
    ON UPDATE CASCADE,

  -- Constraints
  CONSTRAINT orders_status_valid
    CHECK (status IN ('pending', 'completed', 'cancelled')),
  CONSTRAINT orders_total_non_negative
    CHECK (total_amount >= 0)
);

-- -------------------------------------------------------------
-- Table: order_items
-- Purpose: Store individual items within an order (junction table)
-- -------------------------------------------------------------
CREATE TABLE order_items (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  order_id UUID NOT NULL,
  product_id UUID NOT NULL,
  quantity INTEGER NOT NULL,
  unit_price DECIMAL(10, 2) NOT NULL,

  -- Foreign Keys
  CONSTRAINT order_items_order_fk
    FOREIGN KEY (order_id) REFERENCES orders(id)
    ON DELETE CASCADE  -- Delete items when order is deleted
    ON UPDATE CASCADE,

  CONSTRAINT order_items_product_fk
    FOREIGN KEY (product_id) REFERENCES products(id)
    ON DELETE RESTRICT  -- Prevent deleting product that's in orders
    ON UPDATE CASCADE,

  -- Constraints
  CONSTRAINT order_items_quantity_positive CHECK (quantity > 0),
  CONSTRAINT order_items_unit_price_positive CHECK (unit_price > 0),

  -- Unique constraint: prevent duplicate product in same order
  CONSTRAINT order_items_order_product_unique UNIQUE (order_id, product_id)
);

-- ============================================================
-- INDEXES
-- ============================================================

-- Users indexes
-- -------------------------------------------------------------
-- Index on email for login queries (covered by unique constraint)
-- No additional index needed due to UNIQUE constraint

-- Index on created_at for sorting recent users
CREATE INDEX idx_users_created_at ON users(created_at DESC);

-- Products indexes
-- -------------------------------------------------------------
-- Index on name for product search (covered by unique constraint)

-- Index on price for filtering/sorting by price
CREATE INDEX idx_products_price ON products(price);

-- Composite index for low stock alerts
CREATE INDEX idx_products_low_stock ON products(stock) WHERE stock < 10;

-- Orders indexes
-- -------------------------------------------------------------
-- Index on user_id for "user's orders" queries (foreign key)
CREATE INDEX idx_orders_user_id ON orders(user_id);

-- Composite index for user's recent orders
CREATE INDEX idx_orders_user_created ON orders(user_id, created_at DESC);

-- Index on status for filtering orders by status
CREATE INDEX idx_orders_status ON orders(status);

-- Partial index for pending orders (hot queries)
CREATE INDEX idx_orders_pending ON orders(created_at DESC) WHERE status = 'pending';

-- Order Items indexes
-- -------------------------------------------------------------
-- Index on order_id for fetching order items (foreign key)
CREATE INDEX idx_order_items_order_id ON order_items(order_id);

-- Index on product_id for product sales analysis (foreign key)
CREATE INDEX idx_order_items_product_id ON order_items(product_id);

-- ============================================================
-- TRIGGERS (For updated_at automation)
-- ============================================================

-- Function to update updated_at timestamp
CREATE OR REPLACE FUNCTION update_updated_at_column()
RETURNS TRIGGER AS $$
BEGIN
  NEW.updated_at = NOW();
  RETURN NEW;
END;
$$ LANGUAGE plpgsql;

-- Trigger for users table
CREATE TRIGGER users_updated_at_trigger
  BEFORE UPDATE ON users
  FOR EACH ROW
  EXECUTE FUNCTION update_updated_at_column();

-- Trigger for products table
CREATE TRIGGER products_updated_at_trigger
  BEFORE UPDATE ON products
  FOR EACH ROW
  EXECUTE FUNCTION update_updated_at_column();

-- Trigger for orders table
CREATE TRIGGER orders_updated_at_trigger
  BEFORE UPDATE ON orders
  FOR EACH ROW
  EXECUTE FUNCTION update_updated_at_column();

-- ============================================================
-- COMMENTS (Documentation)
-- ============================================================

-- Users table
COMMENT ON TABLE users IS 'User accounts for the e-commerce platform';
COMMENT ON COLUMN users.id IS 'Unique user identifier (UUID v4)';
COMMENT ON COLUMN users.email IS 'User email address (unique, used for login)';
COMMENT ON COLUMN users.age IS 'User age in years (must be 18-120)';

-- Products table
COMMENT ON TABLE products IS 'Product catalog with pricing and inventory';
COMMENT ON COLUMN products.id IS 'Unique product identifier (UUID v4)';
COMMENT ON COLUMN products.price IS 'Product price in USD (always positive)';
COMMENT ON COLUMN products.stock IS 'Available inventory quantity';

-- Orders table
COMMENT ON TABLE orders IS 'Customer orders with status tracking';
COMMENT ON COLUMN orders.id IS 'Unique order identifier (UUID v4)';
COMMENT ON COLUMN orders.user_id IS 'FK to users table (order owner)';
COMMENT ON COLUMN orders.status IS 'Order status: pending, completed, cancelled';
COMMENT ON COLUMN orders.total_amount IS 'Total order amount in USD';

-- Order Items table
COMMENT ON TABLE order_items IS 'Line items within an order (junction table)';
COMMENT ON COLUMN order_items.quantity IS 'Number of units ordered';
COMMENT ON COLUMN order_items.unit_price IS 'Price per unit at time of order (historical)';

-- ============================================================
-- SEED DATA (Optional)
-- ============================================================

-- Insert test users
INSERT INTO users (email, name, age) VALUES
  ('john.doe@example.com', 'John Doe', 30),
  ('jane.smith@example.com', 'Jane Smith', 28);

-- Insert test products
INSERT INTO products (name, price, stock) VALUES
  ('Laptop Pro 15"', 1299.99, 50),
  ('Wireless Mouse', 29.99, 200),
  ('USB-C Cable', 12.99, 500);

-- ============================================================
-- ROLLBACK (DOWN MIGRATION)
-- ============================================================

/*
-- To rollback this migration, run:

DROP TRIGGER IF EXISTS users_updated_at_trigger ON users;
DROP TRIGGER IF EXISTS products_updated_at_trigger ON products;
DROP TRIGGER IF EXISTS orders_updated_at_trigger ON orders;
DROP FUNCTION IF EXISTS update_updated_at_column();

DROP TABLE IF EXISTS order_items CASCADE;
DROP TABLE IF EXISTS orders CASCADE;
DROP TABLE IF EXISTS products CASCADE;
DROP TABLE IF EXISTS users CASCADE;

DROP EXTENSION IF EXISTS "pgcrypto";
*/

-- ============================================================
-- VERIFICATION QUERIES
-- ============================================================

-- Verify table creation
SELECT table_name, table_type
FROM information_schema.tables
WHERE table_schema = 'public'
  AND table_name IN ('users', 'products', 'orders', 'order_items');

-- Verify indexes
SELECT tablename, indexname, indexdef
FROM pg_indexes
WHERE schemaname = 'public'
  AND tablename IN ('users', 'products', 'orders', 'order_items')
ORDER BY tablename, indexname;

-- Verify foreign keys
SELECT
  tc.table_name,
  kcu.column_name,
  ccu.table_name AS foreign_table_name,
  ccu.column_name AS foreign_column_name,
  rc.delete_rule,
  rc.update_rule
FROM information_schema.table_constraints AS tc
JOIN information_schema.key_column_usage AS kcu
  ON tc.constraint_name = kcu.constraint_name
JOIN information_schema.constraint_column_usage AS ccu
  ON ccu.constraint_name = tc.constraint_name
JOIN information_schema.referential_constraints AS rc
  ON rc.constraint_name = tc.constraint_name
WHERE tc.constraint_type = 'FOREIGN KEY'
  AND tc.table_schema = 'public'
ORDER BY tc.table_name;

-- ============================================================
-- PERFORMANCE NOTES
-- ============================================================

/*
Index Strategy:
1. All foreign keys indexed for JOIN performance
2. Partial index on orders.status='pending' (hot queries)
3. Composite index on (user_id, created_at) for user order history
4. Low stock partial index for inventory alerts

Query Optimization:
- User login: O(1) via unique index on email
- User orders: Fast via composite index (user_id, created_at)
- Product search: O(1) via unique index on name
- Low stock alerts: Uses partial index (stock < 10)

Estimated Table Sizes (1M users):
- users: ~150 MB (1M rows)
- products: ~15 MB (100K rows)
- orders: ~1.5 GB (10M rows, avg 10 orders/user)
- order_items: ~3 GB (30M rows, avg 3 items/order)

Total: ~4.7 GB (without indexes)
With indexes: ~7 GB
*/

-- ============================================================
-- MAINTENANCE QUERIES
-- ============================================================

-- Check table sizes
SELECT
  schemaname,
  tablename,
  pg_size_pretty(pg_total_relation_size(schemaname||'.'||tablename)) AS size
FROM pg_tables
WHERE schemaname = 'public'
ORDER BY pg_total_relation_size(schemaname||'.'||tablename) DESC;

-- Check index usage
SELECT
  schemaname,
  tablename,
  indexname,
  idx_scan AS index_scans,
  idx_tup_read AS tuples_read,
  idx_tup_fetch AS tuples_fetched
FROM pg_stat_user_indexes
WHERE schemaname = 'public'
ORDER BY idx_scan DESC;

-- Analyze tables for query planner
ANALYZE users;
ANALYZE products;
ANALYZE orders;
ANALYZE order_items;
```

---

## Usage Notes

- **When to use**: After domain model finalized, before implementation
- **When NOT to use**: For NoSQL databases (use document structure instead)
- **Best practice**: Review with DBA before production deployment
- **Iteration**: Create new migration files for schema changes

---

## Quality Checklist

- [ ] All tables have primary keys
- [ ] Foreign keys defined with ON DELETE/UPDATE actions
- [ ] All foreign keys indexed
- [ ] CHECK constraints for business rules
- [ ] Appropriate data types (UUID, DECIMAL for money, TIMESTAMP)
- [ ] Default values where appropriate
- [ ] Indexes for common query patterns
- [ ] Unique constraints for business uniqueness
- [ ] Comments on tables and important columns
- [ ] Rollback script included
- [ ] Seed data for testing (if applicable)
