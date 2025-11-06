# Local Understanding - Techtonic Studios
## nxtbn E-Commerce Platform - Complete Codebase Documentation

**Generated on:** November 6, 2025  
**Repository:** Ecommerce-website by DEMONNN69  
**Branch:** main

---

## 📋 Table of Contents
1. [Project Overview](#project-overview)
2. [Technology Stack](#technology-stack)
3. [Architecture](#architecture)
4. [Core Django Applications](#core-django-applications)
5. [Database Models](#database-models)
6. [API Structure](#api-structure)
7. [Authentication & Authorization](#authentication--authorization)
8. [Business Logic & Workflows](#business-logic--workflows)
9. [Configuration](#configuration)
10. [Development Setup](#development-setup)
11. [Testing](#testing)
12. [Deployment](#deployment)

---

## 🎯 Project Overview

**nxtbn** (Next Billion Native Commerce) is an enterprise-grade, open-source e-commerce CMS platform built for scalability and flexibility.

### Key Information
- **Version:** 3.0.0
- **License:** BSD-3-Clause
- **Primary Language:** Python
- **Framework:** Django 4.2
- **Architecture Type:** Headless CMS
- **Repository:** https://github.com/nxtbn-com/nxtbn

### Core Value Propositions
- Multi-language & Multi-currency support
- Cloud-native & Platform-independent
- Pluggable architecture for extensibility
- Git-friendly modular design
- Support for physical products, digital downloads, and services
- Subscription and one-time payment models
- SEO-optimized
- Built by industry veterans

---

## 💻 Technology Stack

### Backend
- **Framework:** Django 4.2.11
- **Python:** 3.10 (supports 3.8 - 3.12)
- **API Frameworks:**
  - Django REST Framework 3.15.2
  - Graphene Django (GraphQL)
- **Authentication:** 
  - django-allauth 0.63.3
  - Custom JWT implementation
- **Database:**
  - PostgreSQL (Production) - psycopg2-binary 2.9.9
  - SQLite3 (Development/Testing)

### Task Queue & Caching
- **Task Queue:** Celery with django-celery-beat 2.6.0
- **Message Broker:** Redis
- **Caching:**
  - Redis (default cache)
  - Memcached (pymemcache 4.0.0)

### Storage & File Management
- **Storage Backend:** django-storages 1.14.3 (AWS S3 support)
- **Image Processing:** Pillow 10.3.0
- **Static Files:** whitenoise

### Payment Integration
- **Gateway:** Stripe (extensible plugin system)
- **Money Handling:** py-money 0.5.0 (prevents floating-point errors)

### Additional Libraries
- **CORS:** django-cors-headers
- **Countries:** django-countries
- **Filtering:** django-filter 24.2
- **Environment:** python-dotenv
- **Testing:** factory-boy 3.3.0, faker
- **API Documentation:** drf-yasg 1.21.7

### Development Tools
- **Package Manager:** Pipenv
- **Code Formatting:** Black (mentioned in CONTRIBUTING.md)
- **Linting:** flake8
- **Extensions:** django-extensions 3.2.3

### Web Server
- **Production:** Gunicorn
- **Container:** Docker

---

## 🏗️ Architecture

### Architectural Pattern
**Headless CMS with Dual API Approach**

```
┌─────────────────────────────────────────────────────────┐
│                    Frontend Layer                        │
│         (React, Vue, Mobile Apps, etc.)                 │
└─────────────────────┬───────────────────────────────────┘
                      │
      ┌───────────────┴────────────────┐
      │                                │
┌─────▼──────┐              ┌─────────▼─────┐
│  GraphQL   │              │   REST API    │
│            │              │               │
│ Storefront │              │  Storefront   │
│   Admin    │              │   Dashboard   │
└─────┬──────┘              └───────┬───────┘
      │                             │
      └──────────┬──────────────────┘
                 │
        ┌────────▼────────┐
        │  Django Backend │
        │                 │
        │  - Core Logic   │
        │  - Models       │
        │  - Business     │
        └────────┬────────┘
                 │
    ┌────────────┼────────────┐
    │            │            │
┌───▼───┐   ┌───▼────┐  ┌───▼────┐
│  DB   │   │ Redis  │  │   S3   │
│ PgSQL │   │ Celery │  │ Media  │
└───────┘   └────────┘  └────────┘
```

### API Endpoints Structure

#### GraphQL Endpoints
- `/graphql/` - Storefront GraphQL API (customer-facing)
- `/admin-graphql/` - Admin Dashboard GraphQL API (management)

#### REST Endpoints Pattern
```
/{app_name}/storefront/api/  - Customer-facing REST APIs
/{app_name}/dashboard/api/   - Admin/Management REST APIs
```

**Example REST Endpoints:**
```
/user/storefront/api/          /user/dashboard/api/
/product/storefront/api/       /product/dashboard/api/
/order/storefront/api/         /order/dashboard/api/
/cart/storefront/api/          /cart/dashboard/api/
/payment/storefront/api/       /payment/dashboard/api/
```

### Design Patterns

#### Abstract Base Models
```python
AbstractBaseModel           # created_at, last_modified
AbstractUUIDModel          # UUID alias field
AbstractBaseUUIDModel      # UUID + timestamps
PublishableModel           # Draft/Published/Archived workflow
AbstractSEOModel           # meta_title, meta_description
AbstractAddressModels      # Standard address fields
AbstractTranslationModel   # Multi-language support
MonetaryMixin              # Money field validation
```

#### Plugin Architecture
- **Base Directory:** `nxtbn/plugins/sources/`
- **Plugin Types:**
  - Payment Processor
  - Currency Backend
  - SMS Service
  - Email Service
  - Invoice Generator
  - General Purpose

**Plugin Loading:**
```python
INSTALLED_PLUGINS = {
    "stripe": 'nxtbn.plugins.sources.nxt_stripe.StripeGateway',
}
```

---

## 📦 Core Django Applications

### Application Structure

| App Name | Purpose | Key Models |
|----------|---------|------------|
| **core** | Base functionality, middleware, utilities | Site configuration, Currency |
| **users** | User management & authentication | User (custom), Permissions |
| **product** | Product catalog management | Product, ProductVariant, Category, Collection, Supplier |
| **order** | Order processing & management | Order, OrderLineItem, Address, ReturnRequest |
| **cart** | Shopping cart functionality | Cart, CartItem |
| **payment** | Payment processing | Payment, Refund, PaymentTransaction |
| **warehouse** | Inventory & stock management | Warehouse, Stock, StockReservation, StockTransfer |
| **shipping** | Shipping methods & rates | ShippingMethod, ShippingRate |
| **tax** | Tax calculation | TaxClass, TaxRate |
| **discount** | Promotions & discounts | PromoCode, PromoCodeCustomer |
| **gift_card** | Gift card management | GiftCard |
| **filemanager** | Media file handling | Image, Document |
| **invoice** | Invoice generation | Invoice |
| **plugins** | Plugin management | PluginConfiguration |
| **seo** | SEO optimization | SeoMetadata |
| **post** | Blog/Content management | Post |
| **purchase** | Purchase orders (B2B) | PurchaseOrder |
| **checkout** | Checkout process | CheckoutSession |

### Middleware Stack
```python
'django.middleware.security.SecurityMiddleware',
'corsheaders.middleware.CorsMiddleware',
'django.contrib.sessions.middleware.SessionMiddleware',
'whitenoise.middleware.WhiteNoiseMiddleware',
'nxtbn.core.currency_middleware.CurrencyMiddleware',  # Custom
'django.middleware.locale.LocaleMiddleware',
'django.middleware.common.CommonMiddleware',
'django.middleware.csrf.CsrfViewMiddleware',
'django.contrib.auth.middleware.AuthenticationMiddleware',
'django.contrib.messages.middleware.MessageMiddleware',
'django.middleware.clickjacking.XFrameOptionsMiddleware',
'allauth.account.middleware.AccountMiddleware',
```

---

## 🗄️ Database Models

### Core Models

#### User Model (`users.User`)
```python
class User(AbstractUser):
    role = CharField(max_length=255, default='Store Admin')
    is_store_admin = BooleanField(default=False)
    is_store_staff = BooleanField(default=False)
    avatar = ImageField(upload_to='avatars/')
    phone_number = CharField(max_length=255)
    
    # Computed properties:
    - total_spent()
    - total_order_count()
    - total_cancelled_order_count()
```

#### Product Models (`product.models`)

**Product:**
```python
- name, slug, description
- category (ForeignKey)
- supplier (ForeignKey)
- tax_class (ForeignKey)
- is_taxable (Boolean)
- meta_title, meta_description (SEO)
- Publishable fields (status, is_live, published_date)
```

**ProductVariant:**
```python
- product (ForeignKey)
- name, sku
- price_amount (BigInteger - stored in subunits)
- currency
- compare_at_price_amount
- cost_per_unit_amount
- track_inventory (Boolean)
- weight, weight_unit
- dimensions (length, width, height)
- images (ManyToMany)
- stock_status (IN_STOCK, OUT_OF_STOCK, etc.)
```

**Category:**
```python
- name, slug, description
- parent (Self-referential ForeignKey)
- Max depth: 3 levels
- Methods: has_sub(), get_family_tree()
```

**Collection:**
```python
- name, slug, description
- created_by, last_modified_by (User)
- SEO fields
```

#### Order Models (`order.models`)

**Order:**
```python
- order_number (unique identifier)
- customer (ForeignKey to User)
- status (OrderStatus choices)
- authorization_status (OrderAuthorizationStatus)
- charge_status (OrderChargeStatus)
- stock_reservation_status (OrderStockReservationStatus)
- total_amount (BigInteger in subunits)
- currency
- payment_terms (PaymentTerms choices)
- shipping_address (ForeignKey to Address)
- billing_address (ForeignKey to Address)
- promo_code (ForeignKey)
- shipping_method (ForeignKey)
```

**OrderLineItem:**
```python
- order (ForeignKey)
- variant (ForeignKey to ProductVariant)
- quantity (PositiveInteger)
- price_per_unit (BigInteger)
- tax_amount
- discount_amount
- subtotal
```

**Address:**
```python
- user (ForeignKey, nullable)
- first_name, last_name
- phone_number, email
- street_address, city, state, country, postal_code
- address_type (DSA, DBA, SA, BA, DSA_DBA)
- Validation: Prevents duplicate default addresses
```

#### Cart Models (`cart.models`)

**Cart:**
```python
- user (OneToOne)
- alias (UUID)
```

**CartItem:**
```python
- cart (ForeignKey)
- variant (ForeignKey)
- quantity (PositiveInteger, min=1)
```

#### Payment Models (`payment.models`)

**Payment:**
```python
- user (ForeignKey)
- order (ForeignKey)
- payment_method (PaymentMethod choices)
- transaction_id (unique)
- payment_status (PaymentStatus choices)
- is_successful (Boolean)
- currency
- payment_amount (BigInteger in subunits)
- gateway_response_raw (JSONField)
- paid_at (DateTime)
- payment_plugin_id (CharField)
- gateway_name (CharField)
```

**Refund:**
```python
- payment (ForeignKey)
- order (ForeignKey)
- amount (BigInteger)
- reason (TextField)
- refund_status (RefundStatus choices)
- refund_method (RefundMethod choices)
```

#### Warehouse Models (`warehouse.models`)

**Warehouse:**
```python
- name (unique)
- location
- is_default (Boolean, only one can be default)
```

**Stock:**
```python
- warehouse (ForeignKey)
- product_variant (ForeignKey)
- quantity (Integer)
- reserved (Integer)
- incoming (Integer)
- Method: available_for_new_order()
- Unique together: (warehouse, product_variant)
```

**StockReservation:**
```python
- stock (ForeignKey)
- quantity (PositiveInteger)
- purpose (CharField)
- order_line (ForeignKey to OrderLineItem)
```

**StockTransfer:**
```python
- from_warehouse (ForeignKey)
- to_warehouse (ForeignKey)
- status (StockMovementStatus)
- created_by (ForeignKey to User)
```

#### Shipping Models (`shipping.models`)

**ShippingMethod:**
```python
- name
- description
- carrier (e.g., 'FedEx', 'DHL')
```

**ShippingRate:**
```python
- shipping_method (ForeignKey)
- country (CountryField)
- region, city (optional)
- weight_min, weight_max (Decimal)
- rate (Decimal)
- incremental_rate (Decimal)
- currency
```

#### Tax Models (`tax.models`)

**TaxClass:**
```python
- name (unique)
- Examples: VAT, GST, Sales Tax
```

**TaxRate:**
```python
- country (CountryField)
- state (optional)
- rate (Decimal, percentage)
- tax_class (ForeignKey)
- is_active (Boolean)
```

#### Discount Models (`discount.models`)

**PromoCode:**
```python
- code (unique, auto-uppercase)
- description
- code_type (PERCENTAGE, FIXED_AMOUNT, etc.)
- value (Decimal)
- expiration_date
- is_active
- specific_customers (ManyToMany)
- min_purchase_amount
- min_purchase_period
- applicable_products (ManyToMany)
- redemption_limit
- new_customers_only
- usage_limit_per_customer
- Validation methods: is_valid(), is_valid_customer(), etc.
```

---

## 🔌 API Structure

### GraphQL Schema

#### Admin Schema (`nxtbn.admin_schema`)
```python
class Query(
    ProductQuery,
    AdminOrderQuery,
    AdminCoreQuery,
    WarehouseQuery,
    AdminCartQuery,
    UserAdminQuery,
    PurchaseQuery,
    AdminPaymentQuery,
    ImageQuery
):
    pass

class Mutation(
    AdminUserMutation,
    ProductMutation,
    CoreMutation,
    AdminOrderMutation
):
    pass
```

#### Storefront Schema (`nxtbn.storefront_schema`)
```python
class Query(
    ProductQuery,
    CartQuery
):
    pass

class Mutation(
    CartMutation,
    OrderMutation,
    UserMutation,
    OrderQuery  # Mixed in
):
    pass
```

### GraphQL Configuration
```python
GRAPHENE = {
    'SCHEMA': 'nxtbn.admin_schema.admin_schema',
    'MIDDLEWARE': [
        'graphene_django.debug.DjangoDebugMiddleware',
        'nxtbn.users.auth_middleware.MaxQueryDepthMiddleware',
        'nxtbn.users.auth_middleware.NXTBNGraphQLAuthenticationMiddleware',
    ],
    'RELAY_CONNECTION_MAX_LIMIT': 100,
    'RELAY_CONNECTION_ENFORCE_FIRST_OR_LAST': True,
}
```

### REST Framework Configuration
```python
REST_FRAMEWORK = {
    'DEFAULT_PERMISSION_CLASSES': [
        'rest_framework.permissions.IsAuthenticated'
    ],
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'nxtbn.users.authentication.JWTAuthentication',
        'rest_framework.authentication.SessionAuthentication',
    ],
    'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.LimitOffsetPagination',
    'PAGE_SIZE': 20,
}
```

### API Documentation
- **Tool:** drf-yasg (Swagger/OpenAPI)
- **Endpoints:**
  - Dashboard API docs
  - Storefront API docs
- **Authentication:** Token-based (format: `Token <YOUR_TOKEN>`)

---

## 🔐 Authentication & Authorization

### Authentication Methods

#### 1. JWT (Custom Implementation)
```python
NXTBN_JWT_SETTINGS = {
    'SECRET_KEY': JWT_SECRET_KEY,
    'ALGORITHM': 'HS256',
    'ACCESS_TOKEN_EXPIRATION': timedelta(hours=1),
    'REFRESH_TOKEN_EXPIRATION': timedelta(days=1),
    'ACCESS_TOKEN_COOKIE_NAME': 'access_token',
    'REFRESH_TOKEN_COOKIE_NAME': 'refresh_token',
}
```

**Authentication Class:** `nxtbn.users.authentication.JWTAuthentication`

#### 2. Session Authentication
- Standard Django session-based auth
- Used alongside JWT for flexibility

#### 3. Social Authentication (django-allauth)
```python
AUTHENTICATION_BACKENDS = [
    'django.contrib.auth.backends.ModelBackend',
    'allauth.account.auth_backends.AuthenticationBackend',
]

# Allauth Configuration
ACCOUNT_EMAIL_VERIFICATION = 'optional'
ACCOUNT_EMAIL_REQUIRED = True
ACCOUNT_AUTHENTICATION_METHOD = 'email'
ACCOUNT_USERNAME_REQUIRED = False
ACCOUNT_CONFIRM_EMAIL_ON_GET = True
ACCOUNT_LOGIN_ON_EMAIL_CONFIRMATION = True
ACCOUNT_UNIQUE_EMAIL = True

# Custom Adapters
ACCOUNT_ADAPTER = "nxtbn.users.auth_adapters.CustomAccountAdapter"
SOCIALACCOUNT_ADAPTER = "nxtbn.users.auth_adapters.CustomSocialAccountAdapter"
```

### Authorization System

#### User Roles
```python
class UserRole(models.TextChoices):
    STORE_ADMIN = "STORE_ADMIN"
    STORE_STAFF = "STORE_STAFF"
    CUSTOMER = "CUSTOMER"
```

#### Custom Permissions (Enum-based)
Defined in `nxtbn.core.enum_perms.PermissionsEnum`:
- `CAN_READ_CUSTOMER`
- `CAN_UPDATE_CUSTOMER`
- `CAN_INITIATE_PAYMENT_REFUND`
- `CAN_RECEIVE_TRANSFERRED_STOCK`
- `CAN_MARK_STOCK_TRANSFER_AS_COMPLETED`
- And many more...

#### Permission Check Pattern
```python
class Meta:
    permissions = [
        (PermissionsEnum.PERMISSION_NAME, "Description"),
    ]
```

### Security Configuration
```python
# HTTPS & Cookies
SECURE_SSL_REDIRECT = env_var("SECURE_SSL_REDIRECT", default=False)
SESSION_COOKIE_SECURE = True
CSRF_COOKIE_SECURE = True
SESSION_COOKIE_SAMESITE = 'None'
CSRF_COOKIE_SAMESITE = 'None'
SESSION_COOKIE_HTTPONLY = True
CSRF_COOKIE_HTTPONLY = False

# CORS
CORS_ALLOW_CREDENTIALS = True
CORS_ORIGIN_ALLOW_ALL = False
CORS_ALLOWED_ORIGINS = ['http://localhost:3000']  # Configurable
```

---

## ⚙️ Business Logic & Workflows

### Order Lifecycle

```
┌──────────┐     ┌──────────┐     ┌──────────┐     ┌──────────┐
│ PENDING  │ --> │ APPROVED │ --> │  PACKED  │ --> │ SHIPPED  │
└──────────┘     └──────────┘     └──────────┘     └──────────┘
                                                           │
                                                           v
                                                    ┌──────────┐
                                                    │DELIVERED │
                                                    └──────────┘
     ┌──────────────┐                                    │
     │  CANCELLED   │                                    │
     └──────────────┘                                    v
                                              ┌────────────────────┐
     ┌──────────────┐                         │ PENDING_RETURN     │
     │   RETURNED   │ <---------------------- └────────────────────┘
     └──────────────┘
```

**OrderStatus Choices:**
- `PENDING` - Order placed, awaiting approval
- `APPROVED` - Order approved, ready for packing
- `PACKED` - Order packed, ready for shipment
- `SHIPPED` - Order shipped to customer
- `DELIVERED` - Order delivered
- `CANCELLED` - Order cancelled
- `PENDING_RETURN` - Return requested
- `RETURNED` - Order returned

### Payment Authorization & Charge Flow

```
Order Created
    │
    v
┌─────────────────────────────────┐
│   Authorization Status          │
├─────────────────────────────────┤
│ NONE -> PARTIAL -> FULL         │
└─────────────────────────────────┘
    │
    v
┌─────────────────────────────────┐
│   Charge Status                 │
├─────────────────────────────────┤
│ DUE -> PARTIAL -> FULL          │
│              -> OVERCHARGED     │
└─────────────────────────────────┘
```

**Payment Flow:**
1. **Authorization**: Funds are held but not captured
   - `NONE` - No authorization
   - `PARTIAL` - Partially authorized
   - `FULL` - Fully authorized

2. **Capture/Charge**: Funds are actually transferred
   - `DUE` - Payment due
   - `PARTIAL` - Partially paid
   - `FULL` - Fully paid
   - `OVERCHARGED` - Overpaid

**Payment Status:**
- `AUTHORIZED` - Approved but not captured
- `CAPTURED` - Payment completed
- `FAILED` - Payment failed
- `REFUNDED` - Fully refunded
- `PARTIALLY_REFUNDED` - Partially refunded
- `CANCELED` - Payment cancelled
- `UNDER_REVIEW` - Being reviewed
- `PENDING` - Awaiting processing

### Stock Management Workflow

```
Product Variant
    │
    v
┌──────────────────────────────────┐
│  Warehouse Stock                 │
├──────────────────────────────────┤
│  quantity: 100                   │
│  reserved: 10  (orders pending)  │
│  incoming: 20  (purchase orders) │
│  available: 90 (qty - reserved)  │
└──────────────────────────────────┘
    │
    ├─> Stock Reservation (Order placed)
    │
    ├─> Stock Transfer (Between warehouses)
    │
    └─> Stock Adjustment (Manual/Auto)
```

**Stock Reservation Purposes:**
- Pending Orders
- Blocked Stock (QC, special allocation)
- Pre-booked Stock (promised to customer)

**Configuration:**
```python
RESERVE_STOCK_ON_ORDER = True
VALIDATE_STOCK_ON_ORDER = True
```

### Return & Refund Workflow

```
Customer Request Return
    │
    v
┌──────────────┐
│  REQUESTED   │ --approval--> APPROVED
└──────────────┘                  │
       │                          │
   rejection                   shipping
       │                          │
       v                          v
   REJECTED               SHIPPED_BY_CUSTOMER
                                  │
                                  v
                          ┌──────────────────┐
                          │ RECEIVED_BY_STORE│
                          └──────────────────┘
                                  │
                          ┌───────┴────────┐
                          │                │
                       APPROVED        REJECTED
                          │                │
                          v                v
                    REFUNDED         REJECTED
```

**Return Reasons:**
- `DEFECTIVE` - Product defective
- `WRONG_ITEM` - Wrong item received
- `NOT_AS_DESCRIBED` - Product doesn't match description
- `CHANGED_MIND` - Customer changed mind
- `SIZE_FIT` - Wrong size/fit
- `QUALITY` - Quality issues
- `ARRIVED_LATE` - Late delivery
- `OTHER` - Other reasons

### Discount Application Logic

```python
# PromoCode validation chain
is_valid() checks:
    ├─> is_active (Boolean)
    ├─> is_valid_customer(user) - specific customers only?
    ├─> is_valid_product(products) - specific products only?
    ├─> is_valid_min_purchase(user) - minimum purchase met?
    ├─> is_valid_redemption_limit(user) - global limit reached?
    └─> is_valid_usage_limit_per_customer(user) - user limit reached?
```

**Promo Code Types:**
- `PERCENTAGE` - Discount percentage
- `FIXED_AMOUNT` - Fixed discount amount
- `BUY_X_GET_Y` - Buy X get Y free
- `FREE_SHIPPING` - Free shipping

### Currency Handling

**Money Storage Strategy:**
- All monetary values stored in **subunits** (cents, paise, etc.)
- Prevents floating-point arithmetic errors
- Uses `py-money` library for currency operations

**Example:**
```python
# $10.00 stored as 1000 (cents)
# ₹100.00 stored as 10000 (paise)

# Conversion helpers
def payment_amount_in_units(self):
    precision = get_currency_precision(self.currency)
    return self.payment_amount / (10 ** precision)

def humanize_payment_amount(self, locale='en_US'):
    return format_currency(
        self.payment_amount_in_units(),
        self.currency,
        locale=locale
    )
```

**Multi-Currency Configuration:**
```python
BASE_CURRENCY = "USD"
ALLOWED_CURRENCIES = []  # List of allowed currencies
IS_MULTI_CURRENCY = False
```

---

## ⚡ Configuration

### Environment Variables

#### Essential Variables
```bash
# Debug & Development
DEBUG=True
DEVELOPMENT_SERVER=True  # Custom flag for staging
SECRET_KEY=your-secret-key
JWT_SECRET_KEY=your-jwt-secret  # Falls back to SECRET_KEY

# Database (PostgreSQL)
DATABASE_NAME=nxtbn_db
DATABASE_USER=postgres
DATABASE_PASSWORD=password
DATABASE_HOST=localhost
DATABASE_PORT=5432

# CORS
CORS_ALLOWED_ORIGINS=http://localhost:3000,https://yourdomain.com

# Security
ALLOWED_HOSTS=*  # Comma-separated list
SECURE_SSL_REDIRECT=False

# AWS S3 Storage
AWS_ACCESS_KEY_ID=your-access-key
AWS_SECRET_ACCESS_KEY=your-secret-key
AWS_STORAGE_BUCKET_NAME=your-bucket
AWS_STORAGE_REGION=us-east-1
AWS_S3_CUSTOM_DOMAIN=cdn.yourdomain.com
AWS_DEFAULT_ACL=public-read
AWS_AUTO_CREATE_BUCKET=True

# Email (SMTP)
EMAIL_HOST=smtp.gmail.com
EMAIL_HOST_USER=your-email@gmail.com
EMAIL_HOST_PASSWORD=your-password
EMAIL_PORT=587
EMAIL_USE_TLS=True
DEFAULT_FROM_EMAIL=noreply@yourdomain.com

# Redis
REDIS_URL=redis://redis:6379/1

# Celery
CELERY_TASK_ALWAYS_EAGER=False  # True for testing

# Memcached
MEMCACHE_LOCATION=127.0.0.1:11211

# Currency
BASE_CURRENCY=USD
ALLOWED_CURRENCIES=USD,EUR,GBP,INR
IS_MULTI_CURRENCY=False

# Other
STORE_URL=http://localhost:8000
IMAGE_COMPRESS_MAX=200  # KB
ACCOUNT_EMAIL_VERIFICATION=optional
```

### Settings File Structure

**Location:** `nxtbn/settings.py`

**Key Sections:**
1. **Environment Variable Loading** (python-dotenv)
2. **Security Settings**
3. **Installed Apps** (Django + Local + Helper apps)
4. **Middleware Configuration**
5. **Database Configuration**
6. **Authentication Backends**
7. **REST Framework Settings**
8. **GraphQL Settings**
9. **Email Configuration**
10. **Celery Configuration**
11. **Cache Configuration**
12. **Static/Media Files**
13. **AWS S3 Configuration**
14. **Custom nxtbn Settings**

### URL Configuration

**Main URLs:** `nxtbn/urls.py`

```python
urlpatterns = [
    path('django-admin/', admin.site.urls),
    path('', include('nxtbn.home.urls')),
    path('', include('nxtbn.seo.urls')),
    
    # GraphQL
    path("graphql/", GraphQLView.as_view(graphiql=True, schema=storefront_schema)),
    path('admin-graphql/', GraphQLView.as_view(graphiql=True, schema=admin_schema)),
    
    # Product URLs
    path('product/', include('nxtbn.product.urls')),
    
    # Allauth
    path('accounts/', include('allauth.urls')),
    
    # REST API URLs (pattern: {app}/{audience}/api/)
    path('user/storefront/api/', include('...')),
    path('user/dashboard/api/', include('...')),
    # ... (repeated for each app)
]
```

### Celery Configuration

**File:** `nxtbn/celery.py`

```python
app = Celery('nxtbn')
app.config_from_object('django.conf:settings', namespace='CELERY')
app.autodiscover_tasks()

# Settings
CELERY_BROKER_URL = REDIS_URL
CELERY_RESULT_BACKEND = REDIS_URL
CELERY_ACCEPT_CONTENT = ['application/json']
CELERY_TASK_SERIALIZER = 'json'
CELERY_RESULT_SERIALIZER = 'json'
CELERY_TIMEZONE = 'UTC'
```

**Commands:**
```bash
# Start worker
celery -A nxtbn worker --loglevel=info

# Start beat scheduler
celery -A nxtbn beat --loglevel=info
```

---

## 🚀 Development Setup

### Prerequisites
- Python 3.8 - 3.12
- PostgreSQL (optional, SQLite for dev)
- Redis (for caching and Celery)
- Memcached (optional)
- Node.js & npm (for frontend assets)

### Installation Steps

#### 1. Clone Repository
```bash
git clone https://github.com/nxtbn-com/nxtbn.git
cd nxtbn
```

#### 2. Install Dependencies (Pipenv)
```bash
pip install pipenv
pipenv install
pipenv shell
```

#### 3. Environment Configuration
```bash
cp env.example .env
# Edit .env file with your configurations
```

#### 4. Database Setup
```bash
# Run migrations
python manage.py migrate

# Create superuser
python manage.py createsuperuser
```

#### 5. Collect Static Files
```bash
python manage.py collectstatic --noinput
```

#### 6. Run Development Server
```bash
python manage.py runserver
```

**Access Points:**
- **Application:** http://localhost:8000
- **Admin Panel:** http://localhost:8000/django-admin/
- **GraphQL (Storefront):** http://localhost:8000/graphql/
- **GraphQL (Admin):** http://localhost:8000/admin-graphql/
- **API Docs:** http://localhost:8000/api/docs/

#### 7. Run Celery (Optional)
```bash
# Terminal 1: Worker
celery -A nxtbn worker --loglevel=info

# Terminal 2: Beat Scheduler
celery -A nxtbn beat --loglevel=info
```

### Docker Setup

#### Quick Start (Docker)
```bash
# Using docker-compose
docker-compose up

# Using docker run
docker run --name nxtbn_backend \
  -p 8000:8000 \
  -v static_data:/backend/staticfiles \
  -v $(pwd)/dist/media:/backend/media \
  nxtbn/nxtbn:latest
```

#### Build Custom Image
```bash
docker build -t nxtbn/nxtbn:latest .
```

### Project Structure
```
nxtbn/
├── manage.py
├── Pipfile
├── Pipfile.lock
├── package.json
├── docker-compose.yml
├── Dockerfile
├── env.example
├── db.sqlite3
├── nxtbn/
│   ├── settings.py
│   ├── urls.py
│   ├── wsgi.py
│   ├── asgi.py
│   ├── celery.py
│   ├── admin_schema.py
│   ├── storefront_schema.py
│   ├── core/
│   ├── users/
│   ├── product/
│   ├── order/
│   ├── cart/
│   ├── payment/
│   ├── warehouse/
│   ├── shipping/
│   ├── tax/
│   ├── discount/
│   ├── plugins/
│   └── ... (other apps)
├── static/
├── media/
├── templates/
├── scripts/
│   └── entrypoint.sh
└── nxtbn.plugins.sources/
```

---

## 🧪 Testing

### Test Framework
- **Primary:** Django's built-in unittest
- **Fixtures:** Factory Boy
- **Fake Data:** Faker
- **Coverage:** coverage.py

### Running Tests

#### All Tests
```bash
python manage.py test
```

#### Specific App Tests
```bash
python manage.py test nxtbn.product.tests
```

#### Specific Test File
```bash
python manage.py test nxtbn.product.tests.test_models
```

#### With Coverage
```bash
coverage run --source='.' manage.py test
coverage report
coverage html  # Generate HTML report
```

### Test Configuration
```python
# In settings.py
if sys.argv[1] == 'test':
    DATABASES['default'] = {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
    }
```

### Package.json Scripts
```json
{
  "scripts": {
    "test": "pytest",
    "lint": "flake8 .",
    "prepush": "npm run lint && python3 manage.py tests",
    "precommit": "python3 manage.py check"
  }
}
```

### Testing Guidelines
- Write tests for new features
- Maintain existing test coverage
- Use Factory Boy for test data
- Mock external API calls
- Test edge cases and error handling

---

## 🌐 Deployment

### Production Checklist

#### Security
- [ ] Set `DEBUG=False`
- [ ] Configure `ALLOWED_HOSTS`
- [ ] Set strong `SECRET_KEY` and `JWT_SECRET_KEY`
- [ ] Enable `SECURE_SSL_REDIRECT=True`
- [ ] Configure proper CORS origins
- [ ] Set secure cookie settings
- [ ] Enable HTTPS

#### Database
- [ ] Use PostgreSQL (not SQLite)
- [ ] Configure database backups
- [ ] Set up connection pooling
- [ ] Secure database credentials

#### Static & Media Files
- [ ] Configure S3 or CDN for media files
- [ ] Run `collectstatic`
- [ ] Enable Whitenoise for static files
- [ ] Configure proper cache headers

#### Caching & Performance
- [ ] Set up Redis for caching
- [ ] Configure Memcached (optional)
- [ ] Enable query optimization
- [ ] Set up database indexes

#### Email
- [ ] Configure SMTP settings
- [ ] Set proper `DEFAULT_FROM_EMAIL`
- [ ] Test email delivery

#### Background Tasks
- [ ] Set up Celery workers
- [ ] Configure Celery beat for scheduled tasks
- [ ] Monitor task queue

#### Monitoring
- [ ] Set up error tracking (Sentry, etc.)
- [ ] Configure logging
- [ ] Set up performance monitoring
- [ ] Health check endpoints

### Docker Production Deployment

#### Using Docker Image
```bash
docker pull nxtbn/nxtbn:latest

docker run -d \
  --name nxtbn_production \
  -p 80:8000 \
  -e DEBUG=False \
  -e SECRET_KEY=your-production-secret \
  -e DATABASE_NAME=prod_db \
  -e DATABASE_USER=prod_user \
  -e DATABASE_PASSWORD=secure_password \
  -e DATABASE_HOST=db.example.com \
  -e REDIS_URL=redis://redis:6379/0 \
  -v /var/www/media:/backend/media \
  nxtbn/nxtbn:latest
```

#### Docker Compose Production
```yaml
version: '3.7'

services:
  backend:
    image: nxtbn/nxtbn:latest
    ports:
      - "8000:8000"
    environment:
      - DEBUG=False
      - DATABASE_HOST=postgres
      - REDIS_URL=redis://redis:6379/0
    volumes:
      - static_data:/backend/staticfiles
      - media_data:/backend/media
    depends_on:
      - postgres
      - redis

  postgres:
    image: postgres:14
    environment:
      POSTGRES_DB: nxtbn
      POSTGRES_USER: nxtbn
      POSTGRES_PASSWORD: secure_password
    volumes:
      - postgres_data:/var/lib/postgresql/data

  redis:
    image: redis:7-alpine
    volumes:
      - redis_data:/data

  celery_worker:
    image: nxtbn/nxtbn:latest
    command: celery -A nxtbn worker --loglevel=info
    depends_on:
      - postgres
      - redis

  celery_beat:
    image: nxtbn/nxtbn:latest
    command: celery -A nxtbn beat --loglevel=info
    depends_on:
      - postgres
      - redis

volumes:
  static_data:
  media_data:
  postgres_data:
  redis_data:
```

### Traditional Deployment (No Docker)

#### 1. Install System Dependencies
```bash
sudo apt update
sudo apt install python3-pip python3-venv postgresql redis-server nginx
```

#### 2. Set Up Application
```bash
cd /var/www/nxtbn
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

#### 3. Configure Gunicorn
```bash
gunicorn nxtbn.wsgi:application \
  --bind 0.0.0.0:8000 \
  --workers 4 \
  --threads 2 \
  --timeout 60
```

#### 4. Nginx Configuration
```nginx
server {
    listen 80;
    server_name yourdomain.com;

    location /static/ {
        alias /var/www/nxtbn/staticfiles/;
    }

    location /media/ {
        alias /var/www/nxtbn/media/;
    }

    location / {
        proxy_pass http://127.0.0.1:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

#### 5. Systemd Service
```ini
[Unit]
Description=nxtbn gunicorn daemon
After=network.target

[Service]
User=www-data
Group=www-data
WorkingDirectory=/var/www/nxtbn
ExecStart=/var/www/nxtbn/venv/bin/gunicorn \
  --workers 4 \
  --bind unix:/var/www/nxtbn/nxtbn.sock \
  nxtbn.wsgi:application

[Install]
WantedBy=multi-user.target
```

### Cloud Platform Deployment

#### AWS Deployment
- **Compute:** EC2, ECS, or EKS
- **Database:** RDS (PostgreSQL)
- **Cache:** ElastiCache (Redis)
- **Storage:** S3 for media files
- **CDN:** CloudFront
- **Load Balancer:** ALB

#### Heroku Deployment
```bash
heroku create nxtbn-app
heroku addons:create heroku-postgresql:hobby-dev
heroku addons:create heroku-redis:hobby-dev
git push heroku main
heroku run python manage.py migrate
```

#### DigitalOcean App Platform
- Use `Dockerfile` for deployment
- Add PostgreSQL and Redis databases
- Configure environment variables
- Set up spaces for media files

---

## 📊 Key Enumerations & Constants

### Order Status Flow
```python
class OrderStatus(models.TextChoices):
    PENDING = "PENDING"
    APPROVED = "APPROVED"
    PACKED = "PACKED"
    SHIPPED = "SHIPPED"
    DELIVERED = "DELIVERED"
    CANCELLED = "CANCELLED"
    PENDING_RETURN = "PENDING_RETURN"
    RETURNED = "RETURNED"
```

### Payment Methods
```python
class PaymentMethod(models.TextChoices):
    CREDIT_CARD = "CREDIT_CARD"
    PAYPAL = "PAYPAL"
    BANK_TRANSFER = "BANK_TRANSFER"
    CASH_ON_DELIVERY = "CASH_ON_DELIVERY"
    GIFT_CARD = "GIFT_CARD"
    DIRECT_DEBIT = "DIRECT_DEBIT"
    CASH_IN_STORE = "CASH_IN_STORE"
    MFS = "MFS"  # Mobile Financial Services
    CARRIER_BILLING = "CARRIER_BILLING"
    CRYPTOCURRENCY = "CRYPTOCURRENCY"
    BUY_NOW_PAY_LATER = "BUY_NOW_PAY_LATER"
    E_WALLET = "E_WALLET"
    BANK_TRANSFER_INSTANT = "BANK_TRANSFER_INSTANT"
    POSTAL_MONEY_ORDER = "POSTAL_MONEY_ORDER"
    CHEQUE = "CHEQUE"
    OTHER = "OTHER"
```

### Currency Types
Over 160+ currencies supported. Major ones:
```python
class CurrencyTypes(models.TextChoices):
    USD = "USD"  # United States Dollar
    EUR = "EUR"  # Euro
    GBP = "GBP"  # British Pound
    JPY = "JPY"  # Japanese Yen
    AUD = "AUD"  # Australian Dollar
    CAD = "CAD"  # Canadian Dollar
    INR = "INR"  # Indian Rupee
    CNY = "CNY"  # Chinese Yuan
    # ... and 150+ more
```

### Stock Status
```python
class StockStatus(models.TextChoices):
    IN_STOCK = "IN_STOCK"
    OUT_OF_STOCK = "OUT_OF_STOCK"
    PREORDER = "PREORDER"
    LOW_STOCK = "LOW_STOCK"
    DISCONTINUED = "DISCONTINUED"
```

### Publishable Status
```python
class PublishableStatus(models.TextChoices):
    DRAFT = "DRAFT"
    PUBLISHED = "PUBLISHED"
    ARCHIVED = "ARCHIVED"
```

---

## 🔧 Utilities & Helper Functions

### Money Handling Utilities

**File:** `nxtbn/core/utils.py`

```python
def build_currency_amount(amount, currency):
    """Convert amount to subunit based on currency precision"""
    precision = get_currency_precision(currency)
    return int(amount * (10 ** precision))

def to_currency_unit(subunit_amount, currency):
    """Convert subunit to standard unit"""
    precision = get_currency_precision(currency)
    return subunit_amount / (10 ** precision)
```

### Monetary Mixin

**File:** `nxtbn/core/mixin.py`

```python
class MonetaryMixin:
    """
    Mixin for models with monetary fields.
    Validates currency fields based on money_validator_map.
    """
    money_validator_map = {}
    
    def validate_amount(self):
        # Validates monetary fields
        # Checks currency field exists
        # Verifies amount type (unit/subunit)
        pass
```

### Custom Middleware

**Currency Middleware:** `nxtbn/core/currency_middleware.py`
- Dynamically determines currency based on request
- Sets currency in request context
- Supports multi-currency operations

### Signal Handlers

**File:** `nxtbn/core/receivers.py`
- Post-save signals for cache invalidation
- Pre-delete signals for cleanup
- Order status change notifications

---

## 📱 Frontend Integration

### API Integration Points

#### For React/Vue/Angular Frontend

**Authentication Flow:**
```javascript
// 1. Login
POST /user/storefront/api/login/
{
  "email": "user@example.com",
  "password": "password123"
}

// Response includes JWT tokens in cookies or response body
```

**GraphQL Queries:**
```graphql
# Get Products
query {
  products(first: 10) {
    edges {
      node {
        id
        name
        slug
        variants {
          id
          sku
          priceAmount
          currency
        }
      }
    }
  }
}
```

**REST API Calls:**
```javascript
// Get Cart
GET /cart/storefront/api/

// Add to Cart
POST /cart/storefront/api/items/
{
  "variant_id": "uuid",
  "quantity": 2
}

// Create Order
POST /order/storefront/api/create/
{
  "shipping_address": {...},
  "payment_method": "CREDIT_CARD"
}
```

### Headless CMS Benefits
- Complete frontend flexibility
- Use any JavaScript framework
- Mobile app integration (React Native, Flutter)
- Multiple storefronts from one backend
- Progressive Web App (PWA) friendly

---

## 🤝 Contributing

### Guidelines

**From CONTRIBUTING.md:**

1. **Fork & Branch**
   ```bash
   git checkout -b feature/add-login
   ```

2. **Code Style**
   - Follow PEP 8
   - Use Black formatter
   - Add docstrings
   - Comment complex logic

3. **Commit Messages**
   - `feat:` - New features
   - `fix:` - Bug fixes
   - `docs:` - Documentation
   - `style:` - Formatting
   - `refactor:` - Code restructuring
   - `test:` - Adding tests

4. **Testing**
   ```bash
   python manage.py test
   python manage.py test nxtbn.product.tests
   ```

5. **Pull Request**
   - Clear description
   - Link related issues
   - Pass all tests
   - Respond to feedback

### Best Practices
- Check existing issues first
- Maintain consistency with existing code
- Test thoroughly
- Update documentation
- Be responsive to reviews

---

## 📚 Additional Resources

### Documentation Links
- **Main Documentation:** https://docs.nxtbn.com/
- **GitHub Repository:** https://github.com/nxtbn-com/nxtbn
- **Community Slack:** https://join.slack.com/t/nxtbn/...
- **Website:** https://www.nxtbn.com/

### Technology Documentation
- **Django:** https://docs.djangoproject.com/
- **Django REST Framework:** https://www.django-rest-framework.org/
- **Graphene-Django:** https://docs.graphene-python.org/
- **Celery:** https://docs.celeryproject.org/
- **Stripe:** https://stripe.com/docs/api

### Support
- **Email:** info@nxtbn.com
- **LinkedIn:** https://www.linkedin.com/company/nxtbn
- **Twitter/X:** https://x.com/nxtbn_com
- **Facebook:** https://www.facebook.com/nxtbncms/

---

## 🎓 Learning Path

### For New Developers

**Week 1: Setup & Basics**
1. Set up local development environment
2. Explore Django admin panel
3. Understand project structure
4. Read through main models

**Week 2: API Exploration**
1. Test GraphQL queries in GraphiQL
2. Explore REST API endpoints
3. Understand authentication flow
4. Try CRUD operations

**Week 3: Business Logic**
1. Study order creation process
2. Understand payment flow
3. Explore inventory management
4. Learn discount application

**Week 4: Advanced Topics**
1. Plugin architecture
2. Multi-currency handling
3. Celery tasks
4. Performance optimization

### Common Tasks

**Add New Product:**
1. Create Product instance
2. Add ProductVariant(s)
3. Upload images
4. Set pricing
5. Configure inventory
6. Publish

**Process Order:**
1. Customer adds to cart
2. Proceeds to checkout
3. Creates order
4. Payment authorization
5. Stock reservation
6. Payment capture
7. Order fulfillment
8. Shipping
9. Delivery confirmation

**Handle Return:**
1. Customer requests return
2. Admin approves/rejects
3. Customer ships back
4. Admin receives & inspects
5. Refund processing
6. Stock adjustment

---

## 🔍 Troubleshooting

### Common Issues

#### Database Connection Error
```bash
# Check PostgreSQL is running
sudo systemctl status postgresql

# Verify DATABASE_* environment variables
echo $DATABASE_NAME
```

#### Redis Connection Error
```bash
# Check Redis is running
sudo systemctl status redis

# Test connection
redis-cli ping  # Should return PONG
```

#### Static Files Not Loading
```bash
# Collect static files
python manage.py collectstatic --noinput

# Check STATIC_ROOT and STATIC_URL settings
```

#### Celery Tasks Not Running
```bash
# Ensure Redis is accessible
# Check Celery worker is running
celery -A nxtbn worker --loglevel=info

# Check Celery beat for scheduled tasks
celery -A nxtbn beat --loglevel=info
```

#### JWT Token Issues
```bash
# Verify JWT_SECRET_KEY is set
# Check token expiration settings
# Ensure correct Authorization header format: "Bearer <token>"
```

---

## 📝 Quick Reference

### Management Commands
```bash
# Database
python manage.py makemigrations
python manage.py migrate
python manage.py dbshell

# Users
python manage.py createsuperuser
python manage.py changepassword <username>

# Static Files
python manage.py collectstatic
python manage.py findstatic <filename>

# Development
python manage.py runserver
python manage.py shell
python manage.py check

# Testing
python manage.py test
python manage.py test --keepdb  # Keep test database

# Custom Commands (if any)
python manage.py seed_data  # Seed sample data
```

### Important File Locations
```
Configuration:  nxtbn/settings.py
URLs:           nxtbn/urls.py
Models:         nxtbn/{app}/models.py
Views:          nxtbn/{app}/views.py
Serializers:    nxtbn/{app}/api/{audience}/serializers.py
GraphQL Schema: nxtbn/admin_schema.py, nxtbn/storefront_schema.py
Celery:         nxtbn/celery.py
Middleware:     nxtbn/core/currency_middleware.py
```

### Environment Files
```
.env              - Main environment configuration
env.example       - Example environment file
Pipfile           - Python dependencies
Pipfile.lock      - Locked dependencies
package.json      - Node.js scripts
docker-compose.yml - Docker services
Dockerfile        - Container build instructions
```

---

## 📊 Database Schema Overview

### Entity Relationship Summary

```
User (1) ----< (Many) Order
User (1) ----< (1) Cart
User (1) ----< (Many) Address
User (1) ----< (Many) Payment

Product (1) ----< (Many) ProductVariant
Product (Many) ----< (Many) Category
Product (Many) ----< (Many) Collection

Order (1) ----< (Many) OrderLineItem
Order (1) ----< (1) Payment
Order (Many) ----< (1) Address (shipping)
Order (Many) ----< (1) Address (billing)

ProductVariant (1) ----< (Many) Stock
ProductVariant (1) ----< (Many) CartItem
ProductVariant (1) ----< (Many) OrderLineItem

Warehouse (1) ----< (Many) Stock
Stock (1) ----< (Many) StockReservation
```

---

## ✅ Final Notes

### Project Strengths
✅ Well-structured Django application  
✅ Dual API approach (REST + GraphQL)  
✅ Comprehensive e-commerce features  
✅ Production-ready with Docker support  
✅ Extensible plugin architecture  
✅ Strong currency/money handling  
✅ Multi-tenant capabilities  
✅ Active development & community  

### Areas for Custom Development
- Custom payment gateway plugins
- Additional shipping providers
- Custom tax calculation rules
- Advanced analytics & reporting
- AI-powered product recommendations
- Custom theme system
- Advanced inventory rules

### Security Considerations
- Always use HTTPS in production
- Rotate SECRET_KEY regularly
- Implement rate limiting
- Monitor for SQL injection
- Regular security audits
- Keep dependencies updated
- Use environment variables for secrets

---

**Document Version:** 1.0  
**Last Updated:** November 6, 2025  
**Prepared for:** Techtonic Studios  
**Platform Version:** nxtbn 3.0.0

---

*This document provides a comprehensive understanding of the nxtbn e-commerce platform codebase. For the most up-to-date information, always refer to the official documentation at https://docs.nxtbn.com/*
