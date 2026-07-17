# Football Shoe Shop Database

A MySQL database system for managing a football shoe retail store. Built as a course project for **IT3290 — Database Lab** at Hanoi University of Science and Technology (HUST).

## Team Members

| Name | Student ID | Role |
|------|-----------|------|
| Nguyễn Thành Trung | 202417056 | Schema design, tables, triggers, procedures |
| Trần Xuân Phong | 202417002 | Data seeding |
| Đỗ Lê Thanh Tùng | 202417070 | Reports, slides, presentation |

> All members contributed to brainstorming and planning.

## Problem Statement

A football shoe store needs to manage:
- **Product catalog** — shoes organized by brand, stud type, color, and size variants
- **Inventory** — real-time stock tracking per variant
- **Orders** — customer purchases with status tracking
- **Payments** — multiple payment methods with status monitoring
- **Reviews** — customer ratings and feedback

## Entity-Relationship Diagram

![ERD](path/to/erd.png) <!-- TODO: add ERD image -->

### Entities & Relationships

| Entity A | Entity B | Type | Description |
|----------|----------|------|-------------|
| Customers | Orders | 1–N | One customer can place many orders |
| Orders | Payments | 1–1 | Each order has one payment |
| Orders | OrderDetails | 1–N | One order contains many line items |
| Variants | OrderDetails | 1–N | One variant appears in many order lines |
| Products | Reviews | 1–N | One product can have many reviews |
| Products | Variants | 1–N | One product has many variants |
| Customers | Reviews | 1–N | One customer can write many reviews |
| Brands | Products | 1–N | One brand makes many products |
| PaymentMethods | Payments | 1–N | One method used in many payments |

## Database Schema

```sql
brands           (brand_id PK, brand_name UNIQUE)
customers        (customer_id PK, first_name, last_name, email, phone, address,
                  is_member, created_at, date_of_birth)
products         (product_id PK, product_name, description, retail_price,
                  created_at, brand_id FK)
variants         (variant_id PK, product_id FK, stud_type, color, size,
                  stock_quantity, UNIQUE(product_id, color, size, stud_type))
orders           (order_id PK, customer_id FK, order_date, total_amount,
                  estimated_delivery_date, discount_rate, tax_rate, shipping_fee,
                  order_status, updated_at, subtotal)
order_details    (order_id PK+FK, variant_id PK+FK, quantity, unit_price)
payments         (payment_id PK, order_id FK, payment_method_id FK,
                  payment_status, paid_at, amount)
payment_methods  (payment_method_id PK, method_name UNIQUE)
reviews          (review_id PK, customer_id FK, product_id FK, rating_star,
                  comment, created_at, UNIQUE(customer_id, product_id))
```

## Project Structure

```
football_shoe_shop_db/
├── init.sql          # Full database dump (schema + data + routines)
└── README.md
```

## Getting Started

### Prerequisites

- MySQL 8.0+ or MariaDB 10.5+

### Setup

```bash
mysql -u root -p < init.sql
```

This creates the `football_shoe_shop` database with all tables, sample data, views, procedures, and triggers.

### Sample Data

| Table | Approx. Rows |
|-------|-------------|
| brands | 9 |
| products | 57 |
| variants | ~1,700 |
| customers | 200 |
| orders | 900 |
| payments | 900 |
| reviews | 400+ |

## Features

### Views

| View | Purpose |
|------|---------|
| `product_variant_view` | Full product info with brand, variants, and stock |
| `order_detail_view` | Order line items with customer and product details |
| `order_summary_view` | Customer-level order summaries |
| `payment_view` | Payment status with method name |
| `review_view` | Reviews with customer and product info |
| `vw_top_5_best_selling_products` | Top 5 products by sales volume |

### Stored Procedures

| Procedure | Description |
|-----------|-------------|
| `get_or_create_customer` | Look up an existing customer or create a new one |
| `guest_create_payment` | Create a payment record for an order |
| `update_order_totals` | Recalculate subtotal and total with discount, tax, shipping |
| `update_stock_quantity` | Bulk update variant stock levels |

### Triggers

| Trigger | Event | Action |
|---------|-------|--------|
| `trg_order_details_after_insert` | AFTER INSERT on `order_details` | Deduct stock & update order total |
| `trg_order_details_after_update` | AFTER UPDATE on `order_details` | Adjust stock & recalculate total |
| `trg_order_details_after_delete` | AFTER DELETE on `order_details` | Restore stock & recalculate total |
| `trg_orders_before_update` | BEFORE UPDATE on `orders` | Recalculate total when discount/tax/shipping changes |
| `trg_orders_after_update_status` | AFTER UPDATE on `orders` | Restore stock on order cancellation |
| `trg_payments_after_update` | AFTER UPDATE on `payments` | Cancel order if payment fails |

### Indexes

Indexes are created on all primary keys, foreign keys, and frequently queried columns (`email`, `phone`, `product_name`, `brand_name`, `order_date`, `payment_status`, etc.) for query performance.

### User Perspectives

**Guest (Customer):**
- Browse and search products by name, brand, price range, stud type
- View top sellers, highest rated, new arrivals, in-stock products
- Track orders and payment status
- Read and write product reviews

**Staff (Admin):**
- Top 5 best-selling products and top customers
- Revenue reports (total, by product, by month, by year)
- Orders by status, payment method analytics
- Average product ratings and total reviews
- Highest-value orders and biggest spenders

## Technologies

- **Database:** MySQL 8.4
- **Language:** SQL (DML, DDL, PL/SQL-style routines)

## Future Work

- Integrate with a backend application (Node.js/Python/Java)
- Build a web frontend for the shop
- Add user authentication and role-based access control
- Implement a recommendation engine based on reviews and purchase history

## License

This project is created for educational purposes as part of the IT3290 Database Lab course at HUST.
