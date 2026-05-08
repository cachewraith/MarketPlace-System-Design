# HushStore Marketplace Backend Database Design

## 1. Project Overview

This document defines the finalized database design for a Laravel backend marketplace system.

The system supports:

- Customer authentication
- Social login providers such as Google, Facebook, GitHub, Apple, etc.
- Role and permission management
- Vendor onboarding
- Store management
- Nested categories
- Product catalog
- Product variants
- Product images
- Inventory tracking
- Cart and checkout
- Multi-vendor orders
- Payments and payment webhooks
- Coupons
- Reviews
- Wishlists
- Notifications
- Vendor payouts
- Audit logs

The project is designed for a real-world multi-vendor e-commerce platform similar to Shopee, Lazada, or Amazon Marketplace.

---

## 2. Main System Flow

```txt
User registers or logs in
→ User can login with email/password or social provider
→ User can apply to become a vendor
→ Admin reviews vendor application
→ Vendor is approved
→ Vendor creates store
→ Vendor creates products and variants
→ Customer adds products to cart
→ Customer checks out
→ System creates order
→ Customer pays
→ Payment webhook confirms payment
→ Order status changes
→ Vendor packs and ships items
→ Customer receives order
→ Order is completed
→ Vendor receives payout
```

---

## 3. Recommended Laravel Packages

```bash
composer require laravel/sanctum
composer require spatie/laravel-permission
composer require spatie/laravel-model-states
composer require spatie/laravel-activitylog
composer require kalnoy/nestedset
composer require laravel/scout
composer require meilisearch/meilisearch-php
composer require spatie/laravel-medialibrary
```

### Package Purpose

| Package | Purpose |
|---|---|
| Laravel Sanctum | API token authentication |
| Spatie Permission | Roles and permissions |
| Spatie Model States | Vendor/application/order state management |
| Spatie Activitylog | Audit logs |
| Kalnoy Nested Set | Nested categories |
| Laravel Scout | Search abstraction |
| Meilisearch | Product full-text search engine |
| Spatie Medialibrary | Product/store/vendor image uploads |

---

# 4. Database Tables Summary

## Core Tables

```txt
users
roles
permissions
model_has_roles
model_has_permissions
role_has_permissions
```

## Vendor & Store Tables

```txt
vendors
stores
addresses
```

## Product Catalog Tables

```txt
categories
products
product_variants
product_variant_options
product_images
```

## Inventory Tables

```txt
inventories
inventory_movements
```

## Cart Tables

```txt
carts
cart_items
```

## Order Tables

```txt
orders
store_orders
order_items
order_status_histories
```

## Payment Tables

```txt
payments
payment_webhook_events
```

## Coupon Tables

```txt
coupons
coupon_usages
```

## Customer Interaction Tables

```txt
reviews
wishlists
notifications
```

## Vendor Finance Tables

```txt
payouts
payout_items
```

## System Log Tables

```txt
audit_logs
```

---

# 5. Core Authentication Tables

---

## 5.1 `users`

### Purpose

The `users` table stores every account in the system.

A user can be:

- Customer
- Vendor
- Admin
- Super admin
- Support staff

Do not create separate login tables for customers, vendors, and admins. Keep authentication centralized in `users`, then use roles and permissions.

### Important Note About Social Login

You requested:

```txt
provider
provider_id
```

These columns are used for social login.

Example:

```txt
provider = google
provider_id = 112233445566778899
```

This means the user logged in using Google, and `provider_id` is the unique ID from Google.

### Columns

| Column | Type | Nullable | Purpose |
|---|---:|---:|---|
| id | bigint | no | Primary key |
| name | string | no | User display name |
| email | string | yes | User email address |
| email_verified_at | timestamp | yes | Email verification time |
| password | string | yes | Hashed password |
| provider | string | yes | Login provider: google, facebook, github, apple, email |
| provider_id | string | yes | Unique user ID from social provider |
| phone | string | yes | User phone number |
| avatar | string | yes | Profile image URL/path |
| status | string | no | active, blocked, suspended, pending |
| last_login_at | timestamp | yes | Last login time |
| remember_token | string | yes | Laravel remember token |
| created_at | timestamp | no | Created time |
| updated_at | timestamp | no | Updated time |
| deleted_at | timestamp | yes | Soft delete |

### Recommended Indexes

```txt
unique(email)
index(provider, provider_id)
index(status)
```

### Recommended Unique Rule

For social login:

```txt
unique(provider, provider_id)
```

But be careful: both fields can be nullable.

For email login:

```txt
unique(email)
```

### Example Data

```txt
id: 1
name: "Brondi"
email: "brondi@example.com"
provider: "google"
provider_id: "1099288288272727"
status: "active"
```

### Explanation

A user may register with email and password or social login.

For normal email login:

```txt
provider = email
provider_id = null
password = hashed password
```

For Google login:

```txt
provider = google
provider_id = Google account ID
password = null
```

### Relationships

```txt
User has one Vendor
User has many Addresses
User has many Orders
User has many Reviews
User has many Wishlists
User has many Carts
User has many AuditLogs
```

---

## 5.2 `roles`

### Purpose

Stores role names for access control.

Use `spatie/laravel-permission`.

### Columns

| Column | Type | Nullable | Purpose |
|---|---:|---:|---|
| id | bigint | no | Primary key |
| name | string | no | Role name |
| guard_name | string | no | Usually `web` or `api` |
| created_at | timestamp | yes | Created time |
| updated_at | timestamp | yes | Updated time |

### Example Roles

```txt
super_admin
admin
vendor
customer
support
```

---

## 5.3 `permissions`

### Purpose

Stores system permissions.

### Columns

| Column | Type | Nullable | Purpose |
|---|---:|---:|---|
| id | bigint | no | Primary key |
| name | string | no | Permission name |
| guard_name | string | no | Usually `web` or `api` |
| created_at | timestamp | yes | Created time |
| updated_at | timestamp | yes | Updated time |

### Example Permissions

```txt
manage_users
approve_vendors
manage_stores
manage_products
manage_orders
manage_payments
manage_payouts
view_reports
```

---

## 5.4 Spatie Pivot Tables

Spatie package creates these tables:

```txt
model_has_roles
model_has_permissions
role_has_permissions
```

### Purpose

They connect users to roles and permissions.

Example:

```txt
User Brondi has role vendor
Vendor role has permission manage_products
```

---

# 6. Vendor & Store Tables

---

## 6.1 `vendors`

### Purpose

The `vendors` table stores vendor business profiles.

A user becomes a vendor only after submitting a vendor application and receiving admin approval.

### Columns

| Column | Type | Nullable | Purpose |
|---|---:|---:|---|
| id | bigint | no | Primary key |
| user_id | bigint | no | Owner user |
| business_name | string | no | Legal or public business name |
| business_email | string | yes | Business email |
| business_phone | string | yes | Business phone |
| business_type | string | yes | individual, company, organization |
| tax_number | string | yes | Tax/VAT number |
| id_card_front | string | yes | ID document image |
| id_card_back | string | yes | ID document image |
| status | string | no | Vendor application state |
| approved_at | timestamp | yes | Approval time |
| approved_by | bigint | yes | Admin user who approved |
| rejected_reason | text | yes | Reason for rejection |
| suspended_reason | text | yes | Reason for suspension |
| created_at | timestamp | no | Created time |
| updated_at | timestamp | no | Updated time |
| deleted_at | timestamp | yes | Soft delete |

### Status Values

Use `spatie/laravel-model-states`.

```txt
draft
submitted
under_review
approved
rejected
suspended
```

### Relationships

```txt
vendors.user_id → users.id
vendors.approved_by → users.id
```

### Explanation

When a customer wants to sell products, they submit vendor information.

The vendor flow:

```txt
draft
→ submitted
→ under_review
→ approved
```

If the vendor is bad or violates rules:

```txt
approved
→ suspended
```

---

## 6.2 `stores`

### Purpose

The `stores` table stores vendor shops.

A vendor can have one or many stores.

### Columns

| Column | Type | Nullable | Purpose |
|---|---:|---:|---|
| id | bigint | no | Primary key |
| vendor_id | bigint | no | Store owner |
| name | string | no | Store name |
| slug | string | no | Store URL slug |
| description | text | yes | Store description |
| logo | string | yes | Store logo |
| banner | string | yes | Store banner |
| phone | string | yes | Store phone |
| email | string | yes | Store email |
| address_id | bigint | yes | Store address |
| status | string | no | Store status |
| is_verified | boolean | no | Whether store is verified |
| activated_at | timestamp | yes | Activation time |
| created_at | timestamp | no | Created time |
| updated_at | timestamp | no | Updated time |
| deleted_at | timestamp | yes | Soft delete |

### Store Status Values

```txt
pending
active
inactive
suspended
closed
```

### Relationships

```txt
stores.vendor_id → vendors.id
stores.address_id → addresses.id
```

### Explanation

A vendor application can be approved, but the store may still be inactive until setup is complete.

Example:

```txt
Vendor approved
→ Store created
→ Store status pending
→ Admin/vendor activates store
→ Store status active
```

---

## 6.3 `addresses`

### Purpose

Stores reusable addresses for users, stores, orders, vendors, billing, and shipping.

### Columns

| Column | Type | Nullable | Purpose |
|---|---:|---:|---|
| id | bigint | no | Primary key |
| user_id | bigint | yes | Owner user |
| addressable_type | string | yes | Polymorphic owner type |
| addressable_id | bigint | yes | Polymorphic owner ID |
| type | string | no | shipping, billing, store, vendor |
| name | string | yes | Receiver/contact name |
| phone | string | yes | Receiver/contact phone |
| country | string | no | Country |
| province | string | yes | Province |
| city | string | yes | City |
| district | string | yes | District |
| commune | string | yes | Commune |
| village | string | yes | Village |
| street | string | yes | Street details |
| postal_code | string | yes | Postal code |
| latitude | decimal | yes | Latitude |
| longitude | decimal | yes | Longitude |
| is_default | boolean | no | Default address |
| created_at | timestamp | no | Created time |
| updated_at | timestamp | no | Updated time |
| deleted_at | timestamp | yes | Soft delete |

### Explanation

One address table is cleaner than creating many tables like:

```txt
user_addresses
store_addresses
order_addresses
vendor_addresses
```

Use one flexible table.

### Example Address Types

```txt
shipping
billing
store
vendor
```

---

# 7. Category & Product Catalog Tables

---

## 7.1 `categories`

### Purpose

Stores product categories.

Use `kalnoy/nestedset` for parent-child categories.

### Example Category Tree

```txt
Electronics
├── Phones
│   ├── Samsung
│   └── iPhone
└── Laptops

Fashion
├── Men
└── Women
```

### Columns

| Column | Type | Nullable | Purpose |
|---|---:|---:|---|
| id | bigint | no | Primary key |
| name | string | no | Category name |
| slug | string | no | Category URL slug |
| description | text | yes | Category description |
| image | string | yes | Category image |
| parent_id | bigint | yes | Parent category |
| _lft | integer | no | Nested set left value |
| _rgt | integer | no | Nested set right value |
| depth | integer | yes | Category depth |
| is_active | boolean | no | Category visibility |
| created_at | timestamp | no | Created time |
| updated_at | timestamp | no | Updated time |
| deleted_at | timestamp | yes | Soft delete |

### Relationships

```txt
categories.parent_id → categories.id
products.category_id → categories.id
```

### Explanation

Nested set is better than simple parent-child when you need fast category tree queries.

Example:

```txt
Get all children of Electronics
Get all products under Electronics including Phones and Laptops
```

---

## 7.2 `products`

### Purpose

Stores main product information.

A product belongs to one store and one category.

### Columns

| Column | Type | Nullable | Purpose |
|---|---:|---:|---|
| id | bigint | no | Primary key |
| store_id | bigint | no | Store selling product |
| category_id | bigint | no | Product category |
| name | string | no | Product name |
| slug | string | no | Product URL slug |
| description | longText | yes | Full product description |
| short_description | text | yes | Short product summary |
| sku | string | yes | Main product SKU |
| status | string | no | Product status |
| base_price | decimal | no | Default product price |
| sale_price | decimal | yes | Discount price |
| cost_price | decimal | yes | Vendor cost price |
| rating_avg | decimal | no | Average rating |
| rating_count | integer | no | Number of ratings |
| is_featured | boolean | no | Featured product |
| published_at | timestamp | yes | Publish time |
| created_at | timestamp | no | Created time |
| updated_at | timestamp | no | Updated time |
| deleted_at | timestamp | yes | Soft delete |

### Product Status Values

```txt
draft
pending_review
published
rejected
archived
out_of_stock
```

### Relationships

```txt
products.store_id → stores.id
products.category_id → categories.id
products has many product_variants
products has many product_images
products has many reviews
```

### Explanation

Products should be soft deleted because old orders may still reference them.

Do not permanently delete products that were already ordered.

---

## 7.3 `product_variants`

### Purpose

Stores product variations like size, color, storage, model, etc.

Example:

```txt
Product: iPhone 15

Variants:
- Black / 128GB
- Black / 256GB
- Blue / 128GB
```

### Columns

| Column | Type | Nullable | Purpose |
|---|---:|---:|---|
| id | bigint | no | Primary key |
| product_id | bigint | no | Parent product |
| sku | string | no | Variant SKU |
| name | string | yes | Variant display name |
| price | decimal | no | Variant price |
| sale_price | decimal | yes | Variant sale price |
| cost_price | decimal | yes | Vendor cost price |
| barcode | string | yes | Barcode |
| weight | decimal | yes | Weight for shipping |
| width | decimal | yes | Package width |
| height | decimal | yes | Package height |
| length | decimal | yes | Package length |
| is_default | boolean | no | Default selected variant |
| status | string | no | active, inactive, out_of_stock |
| created_at | timestamp | no | Created time |
| updated_at | timestamp | no | Updated time |
| deleted_at | timestamp | yes | Soft delete |

### Relationships

```txt
product_variants.product_id → products.id
product_variants has one inventory
product_variants has many product_variant_options
```

### Explanation

Use variants when product has multiple purchasable options.

Example:

```txt
T-Shirt
- Size M / Black
- Size L / Black
- Size M / White
```

Each variant can have its own price and stock.

---

## 7.4 `product_variant_options`

### Purpose

Stores flexible options for each product variant.

### Columns

| Column | Type | Nullable | Purpose |
|---|---:|---:|---|
| id | bigint | no | Primary key |
| product_variant_id | bigint | no | Variant ID |
| name | string | no | Option name |
| value | string | no | Option value |
| created_at | timestamp | no | Created time |
| updated_at | timestamp | no | Updated time |

### Example Data

```txt
product_variant_id: 1
name: color
value: black

product_variant_id: 1
name: storage
value: 128GB
```

### Explanation

This is better than adding fixed columns like:

```txt
color
size
storage
ram
```

Because different products need different options.

---

## 7.5 `product_images`

### Purpose

Stores product and variant images.

### Columns

| Column | Type | Nullable | Purpose |
|---|---:|---:|---|
| id | bigint | no | Primary key |
| product_id | bigint | no | Parent product |
| product_variant_id | bigint | yes | Specific variant image |
| path | string | no | Image path |
| thumbnail_path | string | yes | Thumbnail path |
| alt_text | string | yes | SEO/accessibility text |
| sort_order | integer | no | Image order |
| is_primary | boolean | no | Main image |
| created_at | timestamp | no | Created time |
| updated_at | timestamp | no | Updated time |

### Explanation

Some images belong to the product, some belong to a variant.

Example:

```txt
Product image:
- iPhone 15 all colors

Variant image:
- iPhone 15 Black
- iPhone 15 Blue
```

---

# 8. Inventory Tables

---

## 8.1 `inventories`

### Purpose

Stores stock quantity per product variant.

### Columns

| Column | Type | Nullable | Purpose |
|---|---:|---:|---|
| id | bigint | no | Primary key |
| product_variant_id | bigint | no | Variant |
| store_id | bigint | no | Store |
| quantity | integer | no | Total stock |
| reserved_quantity | integer | no | Reserved stock |
| low_stock_threshold | integer | no | Low stock warning level |
| created_at | timestamp | no | Created time |
| updated_at | timestamp | no | Updated time |

### Important Formula

```txt
available_stock = quantity - reserved_quantity
```

### Explanation

`quantity` is the real total stock.

`reserved_quantity` means stock currently locked by checkout/payment process.

Example:

```txt
quantity = 10
reserved_quantity = 2
available_stock = 8
```

---

## 8.2 `inventory_movements`

### Purpose

Stores every inventory change.

This table is very important for debugging and audit history.

### Columns

| Column | Type | Nullable | Purpose |
|---|---:|---:|---|
| id | bigint | no | Primary key |
| inventory_id | bigint | no | Related inventory |
| type | string | no | Movement type |
| quantity | integer | no | Changed quantity |
| before_quantity | integer | no | Quantity before change |
| after_quantity | integer | no | Quantity after change |
| reason | string | yes | Human-readable reason |
| reference_type | string | yes | Polymorphic reference type |
| reference_id | bigint | yes | Polymorphic reference ID |
| created_by | bigint | yes | User who made change |
| created_at | timestamp | no | Created time |

### Movement Types

```txt
stock_in
stock_out
reserved
released
sold
returned
adjustment
```

### Example Flow

```txt
Vendor adds stock
→ type = stock_in

Customer checkout reserves stock
→ type = reserved

Payment succeeds
→ type = sold

Payment fails
→ type = released
```

### Explanation

Never update stock without history.

Without `inventory_movements`, you cannot know why stock changed.

---

# 9. Cart Tables

---

## 9.1 `carts`

### Purpose

Stores shopping carts.

Supports both logged-in users and guests.

### Columns

| Column | Type | Nullable | Purpose |
|---|---:|---:|---|
| id | bigint | no | Primary key |
| user_id | bigint | yes | Cart owner |
| session_id | string | yes | Guest cart session |
| status | string | no | Cart status |
| created_at | timestamp | no | Created time |
| updated_at | timestamp | no | Updated time |
| deleted_at | timestamp | yes | Soft delete |

### Cart Status Values

```txt
active
converted
abandoned
```

### Explanation

A guest user may add products to cart before login.

So `user_id` is nullable.

When the guest logs in, you can merge guest cart into user cart.

---

## 9.2 `cart_items`

### Purpose

Stores items inside cart.

### Columns

| Column | Type | Nullable | Purpose |
|---|---:|---:|---|
| id | bigint | no | Primary key |
| cart_id | bigint | no | Parent cart |
| product_id | bigint | no | Product |
| product_variant_id | bigint | no | Selected variant |
| quantity | integer | no | Quantity |
| unit_price | decimal | no | Price when added |
| created_at | timestamp | no | Created time |
| updated_at | timestamp | no | Updated time |

### Explanation

Cart item should reference the variant, not only the product.

Because users buy a specific variant.

Example:

```txt
Product: T-Shirt
Variant: Black / Size M
Quantity: 2
```

---

# 10. Order Tables

---

## 10.1 `orders`

### Purpose

Stores main customer order.

One order can contain products from many stores.

### Columns

| Column | Type | Nullable | Purpose |
|---|---:|---:|---|
| id | bigint | no | Primary key |
| user_id | bigint | no | Customer |
| order_number | string | no | Public order number |
| status | string | no | Main order status |
| payment_status | string | no | Payment state |
| subtotal | decimal | no | Items total |
| discount_total | decimal | no | Total discount |
| shipping_fee | decimal | no | Shipping fee |
| tax_total | decimal | no | Tax total |
| grand_total | decimal | no | Final total |
| currency | string | no | Currency code |
| coupon_id | bigint | yes | Applied coupon |
| shipping_address_id | bigint | yes | Shipping address snapshot |
| billing_address_id | bigint | yes | Billing address snapshot |
| notes | text | yes | Customer note |
| placed_at | timestamp | yes | Order placement time |
| paid_at | timestamp | yes | Payment success time |
| completed_at | timestamp | yes | Order completion time |
| cancelled_at | timestamp | yes | Cancellation time |
| created_at | timestamp | no | Created time |
| updated_at | timestamp | no | Updated time |
| deleted_at | timestamp | yes | Soft delete |

### Order Status Values

```txt
pending
paid
packed
shipped
delivered
completed
cancelled
refunded
```

### Payment Status Values

```txt
unpaid
pending
paid
failed
refunded
partially_refunded
```

### Explanation

`orders` is the parent order.

Example:

```txt
Customer buys:
- Product A from Store 1
- Product B from Store 2

System creates:
- 1 order
- 2 store_orders
```

---

## 10.2 `store_orders`

### Purpose

Stores per-store order groups inside one main order.

This is very important for multi-vendor marketplace systems.

### Columns

| Column | Type | Nullable | Purpose |
|---|---:|---:|---|
| id | bigint | no | Primary key |
| order_id | bigint | no | Parent order |
| store_id | bigint | no | Store |
| vendor_id | bigint | no | Vendor |
| status | string | no | Store-specific order status |
| subtotal | decimal | no | Store item total |
| discount_total | decimal | no | Store discount total |
| shipping_fee | decimal | no | Store shipping fee |
| tax_total | decimal | no | Store tax total |
| commission_total | decimal | no | Platform commission |
| payout_total | decimal | no | Vendor earning |
| packed_at | timestamp | yes | Packed time |
| shipped_at | timestamp | yes | Shipped time |
| delivered_at | timestamp | yes | Delivered time |
| completed_at | timestamp | yes | Completed time |
| cancelled_at | timestamp | yes | Cancelled time |
| created_at | timestamp | no | Created time |
| updated_at | timestamp | no | Updated time |

### Store Order Status Values

```txt
pending
paid
packed
shipped
delivered
completed
cancelled
refunded
```

### Explanation

This solves a real marketplace problem.

One customer order can have many vendors.

Example:

```txt
Order #1001
├── Store Order #A from Store 1
│   ├── Product 1
│   └── Product 2
└── Store Order #B from Store 2
    └── Product 3
```

Each vendor can manage only their own `store_order`.

---

## 10.3 `order_items`

### Purpose

Stores purchased items.

Each order item belongs to a `store_order`.

### Columns

| Column | Type | Nullable | Purpose |
|---|---:|---:|---|
| id | bigint | no | Primary key |
| order_id | bigint | no | Main order |
| store_order_id | bigint | no | Store order |
| store_id | bigint | no | Store |
| vendor_id | bigint | no | Vendor |
| product_id | bigint | yes | Product reference |
| product_variant_id | bigint | yes | Variant reference |
| product_name | string | no | Product snapshot name |
| variant_name | string | yes | Variant snapshot name |
| sku | string | yes | SKU snapshot |
| quantity | integer | no | Purchased quantity |
| unit_price | decimal | no | Unit price |
| discount_total | decimal | no | Discount amount |
| tax_total | decimal | no | Tax amount |
| total | decimal | no | Final line total |
| created_at | timestamp | no | Created time |
| updated_at | timestamp | no | Updated time |

### Explanation

Order items should store product snapshot data.

Do not only rely on product relationship.

Why?

Because after checkout, vendor may change:

```txt
product name
price
variant name
SKU
```

Old order must still show the original purchased data.

---

## 10.4 `order_status_histories`

### Purpose

Stores order status change history.

### Columns

| Column | Type | Nullable | Purpose |
|---|---:|---:|---|
| id | bigint | no | Primary key |
| order_id | bigint | no | Related order |
| store_order_id | bigint | yes | Related store order |
| from_status | string | yes | Previous status |
| to_status | string | no | New status |
| note | text | yes | Status note |
| changed_by | bigint | yes | User who changed status |
| created_at | timestamp | no | Created time |

### Explanation

This table is useful for tracking.

Example:

```txt
pending → paid
paid → packed
packed → shipped
shipped → delivered
delivered → completed
```

Can be used for customer order tracking timeline.

---

# 11. Payment Tables

---

## 11.1 `payments`

### Purpose

Stores payment transactions.

Supports multiple gateways.

Example gateways:

```txt
ABA PayWay
Bakong KHQR
Wing
Cash on Delivery
Mock Sandbox
```

### Columns

| Column | Type | Nullable | Purpose |
|---|---:|---:|---|
| id | bigint | no | Primary key |
| order_id | bigint | no | Related order |
| payment_method | string | no | khqr, card, cod, wallet |
| gateway | string | no | aba_payway, bakong_khqr, wing, mock |
| transaction_id | string | yes | Internal/external transaction ID |
| external_reference | string | yes | Gateway reference |
| amount | decimal | no | Paid amount |
| currency | string | no | Currency |
| status | string | no | Payment status |
| paid_at | timestamp | yes | Paid time |
| failed_at | timestamp | yes | Failed time |
| raw_response | json | yes | Gateway response |
| created_at | timestamp | no | Created time |
| updated_at | timestamp | no | Updated time |

### Payment Status Values

```txt
pending
processing
paid
failed
cancelled
refunded
```

### Explanation

Every payment attempt should be saved.

If payment fails, keep the failed payment record for debugging.

---

## 11.2 `payment_webhook_events`

### Purpose

Stores payment gateway webhook events.

This prevents duplicate processing.

### Columns

| Column | Type | Nullable | Purpose |
|---|---:|---:|---|
| id | bigint | no | Primary key |
| gateway | string | no | Payment gateway |
| event_id | string | yes | Gateway event ID |
| idempotency_key | string | no | Unique processing key |
| payload | json | no | Raw webhook payload |
| status | string | no | received, processed, failed |
| processed_at | timestamp | yes | Processing time |
| created_at | timestamp | no | Created time |
| updated_at | timestamp | no | Updated time |

### Recommended Indexes

```txt
unique(idempotency_key)
index(gateway, event_id)
```

### Explanation

Payment gateways may send the same webhook multiple times.

Without idempotency, your system may mark the same order as paid twice.

Flow:

```txt
Webhook received
→ Save event using idempotency_key
→ If already exists, ignore
→ If new, process payment
→ Mark webhook as processed
```

---

# 12. Coupon Tables

---

## 12.1 `coupons`

### Purpose

Stores coupon and discount codes.

### Columns

| Column | Type | Nullable | Purpose |
|---|---:|---:|---|
| id | bigint | no | Primary key |
| code | string | no | Coupon code |
| name | string | yes | Coupon name |
| description | text | yes | Description |
| type | string | no | fixed, percentage, free_shipping |
| value | decimal | no | Discount value |
| min_order_amount | decimal | yes | Minimum order amount |
| max_discount_amount | decimal | yes | Maximum discount |
| usage_limit | integer | yes | Total usage limit |
| usage_limit_per_user | integer | yes | Per-user usage limit |
| used_count | integer | no | Total used count |
| starts_at | timestamp | yes | Start date |
| expires_at | timestamp | yes | Expiry date |
| is_active | boolean | no | Active status |
| created_at | timestamp | no | Created time |
| updated_at | timestamp | no | Updated time |
| deleted_at | timestamp | yes | Soft delete |

### Coupon Types

```txt
fixed
percentage
free_shipping
```

### Example

```txt
code = WELCOME10
type = percentage
value = 10
max_discount_amount = 5.00
```

Means:

```txt
10% discount, maximum discount $5
```

---

## 12.2 `coupon_usages`

### Purpose

Tracks coupon usage by user and order.

### Columns

| Column | Type | Nullable | Purpose |
|---|---:|---:|---|
| id | bigint | no | Primary key |
| coupon_id | bigint | no | Coupon |
| user_id | bigint | no | User |
| order_id | bigint | no | Order |
| discount_amount | decimal | no | Applied discount |
| used_at | timestamp | no | Used time |
| created_at | timestamp | no | Created time |
| updated_at | timestamp | no | Updated time |

### Explanation

This prevents abuse.

Example:

```txt
Coupon usage limit per user = 1
User already used coupon
→ cannot use again
```

---

# 13. Reviews & Wishlist Tables

---

## 13.1 `reviews`

### Purpose

Stores product reviews.

A user should only review a product after buying it.

### Columns

| Column | Type | Nullable | Purpose |
|---|---:|---:|---|
| id | bigint | no | Primary key |
| user_id | bigint | no | Reviewer |
| product_id | bigint | no | Reviewed product |
| order_item_id | bigint | yes | Purchased item proof |
| rating | tinyInteger | no | Rating 1 to 5 |
| comment | text | yes | Review comment |
| status | string | no | Review moderation status |
| created_at | timestamp | no | Created time |
| updated_at | timestamp | no | Updated time |
| deleted_at | timestamp | yes | Soft delete |

### Review Status Values

```txt
pending
approved
rejected
hidden
```

### Explanation

`order_item_id` proves the user really bought the product.

This prevents fake reviews.

---

## 13.2 `wishlists`

### Purpose

Stores products saved by users.

### Columns

| Column | Type | Nullable | Purpose |
|---|---:|---:|---|
| id | bigint | no | Primary key |
| user_id | bigint | no | User |
| product_id | bigint | no | Saved product |
| created_at | timestamp | no | Created time |
| updated_at | timestamp | no | Updated time |

### Recommended Constraint

```txt
unique(user_id, product_id)
```

### Explanation

A user should not be able to save the same product twice.

---

# 14. Notification Table

---

## 14.1 `notifications`

### Purpose

Laravel default notification table.

Used for user, vendor, and admin notifications.

### Columns

| Column | Type | Nullable | Purpose |
|---|---:|---:|---|
| id | uuid | no | Primary key |
| type | string | no | Notification class |
| notifiable_type | string | no | Model type |
| notifiable_id | bigint | no | Model ID |
| data | text/json | no | Notification payload |
| read_at | timestamp | yes | Read time |
| created_at | timestamp | no | Created time |
| updated_at | timestamp | no | Updated time |

### Notification Examples

```txt
Vendor approved
Order paid
Order shipped
Payment failed
Low stock alert
Payout processed
```

---

# 15. Payout Tables

---

## 15.1 `payouts`

### Purpose

Stores vendor payout records.

After orders are completed, vendors can receive payout.

### Columns

| Column | Type | Nullable | Purpose |
|---|---:|---:|---|
| id | bigint | no | Primary key |
| vendor_id | bigint | no | Vendor |
| store_id | bigint | yes | Store |
| amount | decimal | no | Gross amount |
| commission_amount | decimal | no | Platform commission |
| net_amount | decimal | no | Vendor payout amount |
| status | string | no | Payout status |
| payment_method | string | yes | Bank, KHQR, Wing, ABA, etc. |
| transaction_reference | string | yes | Payment reference |
| requested_at | timestamp | yes | Request time |
| approved_at | timestamp | yes | Approval time |
| processed_at | timestamp | yes | Processed time |
| paid_at | timestamp | yes | Paid time |
| rejected_at | timestamp | yes | Rejection time |
| rejected_reason | text | yes | Rejection reason |
| created_at | timestamp | no | Created time |
| updated_at | timestamp | no | Updated time |

### Payout Status Values

```txt
pending
approved
processing
paid
rejected
failed
```

### Explanation

Vendor payout should not be calculated randomly.

It should be connected to completed order items.

---

## 15.2 `payout_items`

### Purpose

Connects payout to order items.

### Columns

| Column | Type | Nullable | Purpose |
|---|---:|---:|---|
| id | bigint | no | Primary key |
| payout_id | bigint | no | Payout |
| order_item_id | bigint | no | Order item |
| amount | decimal | no | Gross item amount |
| commission_amount | decimal | no | Platform commission |
| net_amount | decimal | no | Vendor net amount |
| created_at | timestamp | no | Created time |
| updated_at | timestamp | no | Updated time |

### Explanation

This lets you answer:

```txt
Which orders created this payout?
Which item generated this vendor earning?
How much commission did platform take?
```

---

# 16. Audit Log Table

---

## 16.1 `audit_logs`

### Purpose

Stores important system actions.

You can also use `spatie/laravel-activitylog`.

### Columns

| Column | Type | Nullable | Purpose |
|---|---:|---:|---|
| id | bigint | no | Primary key |
| user_id | bigint | yes | User who performed action |
| event | string | no | Event name |
| auditable_type | string | yes | Model type |
| auditable_id | bigint | yes | Model ID |
| old_values | json | yes | Previous values |
| new_values | json | yes | New values |
| ip_address | string | yes | Request IP |
| user_agent | text | yes | Browser/device info |
| created_at | timestamp | no | Created time |

### Audit Log Examples

```txt
Admin approved vendor
Vendor updated product price
Admin suspended store
Payment webhook processed
User changed password
Inventory adjusted manually
```

### Explanation

Audit logs help with:

```txt
Security
Debugging
Admin tracking
Fraud investigation
```

---

# 17. Important Backend Flows

---

## 17.1 Authentication Flow

### Email Login

```txt
User registers with email/password
→ Password is hashed
→ Email verification is sent
→ User verifies email
→ User logs in
→ Laravel Sanctum token is created
```

### Social Login

```txt
User clicks Login with Google
→ Frontend gets provider token
→ Backend verifies token with provider
→ Backend gets provider_id and email
→ Backend checks user by provider/provider_id
→ If user exists, login
→ If user does not exist, create user
→ Return Sanctum token
```

### User Social Login Data Example

```txt
provider = google
provider_id = 1234567890
email = brondi@gmail.com
password = null
```

---

## 17.2 Vendor Onboarding Flow

```txt
Customer applies as vendor
→ Create vendor record
→ status = submitted
→ Admin reviews documents
→ status = under_review
→ Admin approves
→ status = approved
→ User receives vendor role
→ Store can be activated
```

If rejected:

```txt
status = rejected
rejected_reason = "Invalid document"
```

---

## 17.3 Product Creation Flow

```txt
Vendor creates product
→ Product status = draft
→ Vendor adds variants
→ Vendor adds images
→ Vendor sets inventory
→ Vendor submits product
→ Product status = pending_review
→ Admin approves
→ Product status = published
→ Product searchable in Meilisearch
```

---

## 17.4 Cart Flow

```txt
Customer adds variant to cart
→ Check product is published
→ Check variant is active
→ Check available stock
→ Add or update cart item
→ Cart total is calculated
```

Important:

```txt
available_stock = quantity - reserved_quantity
```

---

## 17.5 Checkout Flow

```txt
Customer starts checkout
→ Validate cart items
→ Validate coupon
→ Calculate subtotal
→ Calculate discount
→ Calculate shipping fee
→ Calculate tax
→ Create order
→ Create store_orders grouped by store
→ Create order_items
→ Reserve inventory
→ Create payment record
```

---

## 17.6 Payment Flow

```txt
Customer pays with ABA/Bakong/Wing
→ Payment status = pending
→ Gateway sends webhook
→ System checks idempotency key
→ If webhook already processed, ignore
→ If new, process
→ Payment status = paid
→ Order payment_status = paid
→ Order status = paid
→ Inventory reserved becomes sold
→ Order status history created
```

---

## 17.7 Failed Payment Flow

```txt
Customer checkout created order
→ Inventory reserved
→ Payment fails
→ Payment status = failed
→ Order payment_status = failed
→ Reserved inventory released
→ Order can be cancelled or retried
```

---

## 17.8 Order Lifecycle Flow

```txt
pending
→ paid
→ packed
→ shipped
→ delivered
→ completed
```

### Status Explanation

| Status | Meaning |
|---|---|
| pending | Order created but not paid |
| paid | Payment confirmed |
| packed | Vendor packed items |
| shipped | Order sent to delivery |
| delivered | Customer received order |
| completed | Final successful order |
| cancelled | Order cancelled |
| refunded | Payment refunded |

---

## 17.9 Multi-Vendor Order Flow

Example cart:

```txt
Cart:
- Product A from Store 1
- Product B from Store 1
- Product C from Store 2
```

System creates:

```txt
orders
└── order #1001

store_orders
├── store_order #1 for Store 1
└── store_order #2 for Store 2

order_items
├── Product A → store_order #1
├── Product B → store_order #1
└── Product C → store_order #2
```

Why this is important:

```txt
Store 1 can ship first
Store 2 can ship later
Each store has its own payout
Each vendor sees only their own orders
```

---

## 17.10 Payout Flow

```txt
Order completed
→ System calculates vendor earning
→ Platform commission calculated
→ payout_items are created
→ payout is created
→ Admin approves payout
→ Admin pays vendor
→ payout status = paid
```

Example:

```txt
Order item total = $100
Platform commission = 10%
Vendor net = $90
```

---

# 18. Recommended Indexes

## `users`

```txt
unique(email)
unique(provider, provider_id)
index(status)
```

## `vendors`

```txt
index(user_id)
index(status)
index(approved_by)
```

## `stores`

```txt
unique(slug)
index(vendor_id)
index(status)
```

## `categories`

```txt
unique(slug)
index(parent_id)
index(_lft, _rgt)
index(is_active)
```

## `products`

```txt
unique(slug)
index(store_id)
index(category_id)
index(status)
index(is_featured)
index(published_at)
```

## `product_variants`

```txt
unique(sku)
index(product_id)
index(status)
```

## `inventories`

```txt
unique(product_variant_id)
index(store_id)
```

## `orders`

```txt
unique(order_number)
index(user_id)
index(status)
index(payment_status)
index(created_at)
```

## `store_orders`

```txt
index(order_id)
index(store_id)
index(vendor_id)
index(status)
```

## `order_items`

```txt
index(order_id)
index(store_order_id)
index(product_id)
index(product_variant_id)
index(store_id)
index(vendor_id)
```

## `payments`

```txt
index(order_id)
index(status)
index(gateway)
index(transaction_id)
```

## `payment_webhook_events`

```txt
unique(idempotency_key)
index(gateway, event_id)
index(status)
```

## `wishlists`

```txt
unique(user_id, product_id)
```

---

# 19. Recommended Foreign Keys

```txt
vendors.user_id → users.id
vendors.approved_by → users.id

stores.vendor_id → vendors.id
stores.address_id → addresses.id

products.store_id → stores.id
products.category_id → categories.id

product_variants.product_id → products.id
product_images.product_id → products.id
product_images.product_variant_id → product_variants.id

inventories.product_variant_id → product_variants.id
inventories.store_id → stores.id

inventory_movements.inventory_id → inventories.id
inventory_movements.created_by → users.id

carts.user_id → users.id
cart_items.cart_id → carts.id
cart_items.product_id → products.id
cart_items.product_variant_id → product_variants.id

orders.user_id → users.id
orders.coupon_id → coupons.id
orders.shipping_address_id → addresses.id
orders.billing_address_id → addresses.id

store_orders.order_id → orders.id
store_orders.store_id → stores.id
store_orders.vendor_id → vendors.id

order_items.order_id → orders.id
order_items.store_order_id → store_orders.id
order_items.store_id → stores.id
order_items.vendor_id → vendors.id
order_items.product_id → products.id
order_items.product_variant_id → product_variants.id

order_status_histories.order_id → orders.id
order_status_histories.store_order_id → store_orders.id
order_status_histories.changed_by → users.id

payments.order_id → orders.id

coupon_usages.coupon_id → coupons.id
coupon_usages.user_id → users.id
coupon_usages.order_id → orders.id

reviews.user_id → users.id
reviews.product_id → products.id
reviews.order_item_id → order_items.id

wishlists.user_id → users.id
wishlists.product_id → products.id

payouts.vendor_id → vendors.id
payouts.store_id → stores.id

payout_items.payout_id → payouts.id
payout_items.order_item_id → order_items.id

audit_logs.user_id → users.id
```

---

# 20. Final ERD-style Relationship Map

```txt
User
├── Vendor
│   └── Store
│       ├── Products
│       │   ├── Product Variants
│       │   │   ├── Variant Options
│       │   │   └── Inventory
│       │   │       └── Inventory Movements
│       │   ├── Product Images
│       │   └── Reviews
│       └── Store Orders
│
├── Carts
│   └── Cart Items
│
├── Orders
│   ├── Store Orders
│   │   └── Order Items
│   ├── Payments
│   └── Order Status Histories
│
├── Addresses
├── Wishlists
├── Reviews
└── Notifications
```

---

# 21. Final Recommended Database List

```txt
users
roles
permissions
model_has_roles
model_has_permissions
role_has_permissions

vendors
stores
addresses

categories

products
product_variants
product_variant_options
product_images

inventories
inventory_movements

carts
cart_items

orders
store_orders
order_items
order_status_histories

payments
payment_webhook_events

coupons
coupon_usages

reviews
wishlists
notifications

payouts
payout_items

audit_logs
```

---

# 22. Final Backend Architecture Advice

Use this structure in Laravel:

```txt
app/
├── Actions/
├── DTOs/
├── Enums/
├── Events/
├── Exceptions/
├── Http/
│   ├── Controllers/
│   ├── Requests/
│   └── Resources/
├── Jobs/
├── Listeners/
├── Models/
├── Notifications/
├── Policies/
├── Services/
├── States/
└── Support/
```

## Recommended Service Classes

```txt
AuthService
SocialAuthService
VendorOnboardingService
StoreService
ProductService
InventoryService
CartService
CheckoutService
CouponService
PaymentService
PaymentWebhookService
OrderService
PayoutService
NotificationService
AuditLogService
```

## Recommended State Classes

```txt
VendorState
ProductState
OrderState
PaymentState
PayoutState
```

---

# 23. Final Notes

This database design is suitable for a production-level Laravel marketplace backend.

The most important parts are:

```txt
users.provider and users.provider_id for social login
vendors for seller applications
stores for marketplace shops
products and product_variants for flexible catalog
inventories and inventory_movements for stock tracking
orders and store_orders for multi-vendor checkout
payments and payment_webhook_events for safe payment processing
payouts and payout_items for vendor finance
audit_logs for security and tracking
```

Do not skip these tables:

```txt
store_orders
inventory_movements
payment_webhook_events
coupon_usages
payout_items
audit_logs
```

They make the system easier to scale, debug, and maintain.
