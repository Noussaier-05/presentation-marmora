# Marmora Project Documentation

> **Full-stack e-commerce platform for premium marble and natural stone**
>
> **Monorepo** containing a Laravel 12 backend API and an Angular 17 SPA frontend with Three.js 3D visualization, AI chatbot, social login, and B2B quote management.

---

## Table of Contents

1. [Architecture Overview](#1-architecture-overview)
2. [Project Structure](#2-project-structure)
3. [Backend (Laravel 12)](#3-backend-laravel-12)
4. [Frontend (Angular 17)](#4-frontend-angular-17)
5. [Database Schema](#5-database-schema)
6. [API Routes](#6-api-routes)
7. [Configuration](#7-configuration)
8. [Features](#8-features)
9. [Setup Guide](#9-setup-guide)
10. [Detailed Directory Tree](#10-detailed-directory-tree)

---

## 1. Architecture Overview

```
┌─────────────────────────────────────────────────────┐
│                    Frontend                          │
│            Angular 17 + Three.js                     │
│         Standalone Components + SSR                  │
│  ┌──────────┬──────────┬──────────┬──────────────┐  │
│  │ Catalog  │ 3D Viz   │ Chatbot  │  Admin Panel │  │
│  └──────────┴──────────┴──────────┴──────────────┘  │
└──────────────────────┬──────────────────────────────┘
                       │ HTTP (REST + Sanctum Tokens)
                       ▼
┌─────────────────────────────────────────────────────┐
│                    Backend                           │
│              Laravel 12 API                          │
│  ┌──────────┬──────────┬──────────┬──────────────┐  │
│  │ Products │ Orders   │ Quotes   │  AI / Chat   │  │
│  │ Users    │ Auth     │ Notifs   │  Admin       │  │
│  └──────────┴──────────┴──────────┴──────────────┘  │
└──────────────────────┬──────────────────────────────┘
                       │
         ┌─────────────┼─────────────┐
         ▼             ▼             ▼
      MySQL 8.0    Google Gemini  Pusher (realtime)
```

---

## 2. Project Structure

```
Marmora/
├── marmora_backend/          # Laravel 12 API (PHP 8.2+)
├── marmora_front/            # Angular 17 SPA (TypeScript)
├── UML/                      # PlantUML diagrams
├── Documentation files       # *.md design & setup docs
├── .github/workflows/        # CI pipeline
└── package.json              # Root (Three.js)
```

---

## 3. Backend (Laravel 12)

### 3.1 Tech Stack

| Component           | Technology           |
|---------------------|----------------------|
| Framework           | Laravel 12           |
| Language            | PHP 8.2+             |
| API Authentication  | Laravel Sanctum      |
| Social Auth         | Laravel Socialite    |
| AI Integration      | Google Gemini API    |
| Database            | MySQL 8.0+           |
| Real-time           | Pusher               |
| Testing             | PHPUnit              |

### 3.2 Directory Structure

```
marmora_backend/
├── app/
│   ├── Http/
│   │   ├── Controllers/
│   │   │   ├── Api/
│   │   │   │   ├── Auth/AuthController.php      # Register, login, social auth
│   │   │   │   ├── AccountController.php         # User profile management
│   │   │   │   ├── ProductController.php         # Public product catalog
│   │   │   │   ├── OrderController.php           # Customer orders
│   │   │   │   ├── QuoteController.php           # B2B quotes
│   │   │   │   ├── ContactController.php         # Contact form
│   │   │   │   ├── ChatController.php            # AI chatbot
│   │   │   │   ├── LanguageController.php        # Multi-language
│   │   │   │   ├── NotificationController.php    # Notifications
│   │   │   │   ├── CollectionVideoController.php # Videos
│   │   │   │   └── Admin/                        # Admin CRUD controllers
│   │   │   │       ├── AdminProductController.php
│   │   │   │       ├── AdminOrderController.php
│   │   │   │       ├── AdminQuoteController.php
│   │   │   │       ├── AdminUserController.php
│   │   │   │       ├── AdminContactController.php
│   │   │   │       ├── AdminSupportController.php
│   │   │   │       └── AdminCollectionVideoController.php
│   │   │   └── ChatController.php                # Support chat controller
│   │   ├── Middleware/
│   │   │   ├── AdminMiddleware.php               # Admin role guard
│   │   │   └── EnsureUserIsActive.php             # Active user check
│   │   └── Requests/Auth/                        # Validation rules
│   ├── Models/
│   │   ├── User.php                              # Users (admin, b2b, b2c)
│   │   ├── Product.php                           # Products with material taxonomy
│   │   ├── Category.php                          # Product categories
│   │   ├── ProductImage.php                      # Product media
│   │   ├── ProductSurfaceFinish.php              # Surface finish options
│   │   ├── ProductEdgeProfile.php                # Edge profile options
│   │   ├── Order.php                             # Customer orders
│   │   ├── OrderItem.php                         # Order line items
│   │   ├── Quote.php                             # B2B quotations
│   │   ├── QuoteItem.php                         # Quote line items
│   │   ├── Conversation.php                      # Chat conversations
│   │   ├── ConversationMessage.php               # Chat messages
│   │   ├── ContactMessage.php                    # Contact form messages
│   │   ├── CollectionVideo.php                   # Video collections
│   │   └── CompanyProfile.php                    # B2B company info
│   ├── Services/
│   │   ├── AIService.php                         # Google Gemini integration
│   │   └── SubtypeConfig.php                     # Product subtype rules
│   ├── Notifications/                            # 10 notification types
│   └── Events/
│       ├── OrderStatusUpdated.php
│       └── SupportMessageSent.php
├── config/
│   ├── app.php, auth.php, cors.php, database.php,
│   ├── sanctum.php, services.php, session.php, ...
├── database/
│   ├── migrations/                               # 37 migration files
│   └── seeders/                                  # Database seeders
├── routes/
│   ├── api.php                                   # All API routes
│   └── web.php
├── tests/                                        # PHPUnit tests
└── composer.json                                 # PHP dependencies
```

### 3.3 API Endpoints (routes/api.php)

#### Public Endpoints
| Method | URI | Description |
|--------|-----|-------------|
| GET | `/api/products` | List products (filtered, paginated) |
| GET | `/api/products/{product}` | Single product detail |
| GET | `/api/products/featured` | Featured products |
| GET | `/api/products/by-category/{category}` | Products by category |
| GET | `/api/categories` | All categories |
| GET | `/api/categories/tree` | Category tree |
| GET | `/api/surface-finishes` | Surface finishes |
| GET | `/api/subtypes` | Product subtypes |
| GET | `/api/subtypes/{subtype}/edge-profiles` | Edge profiles by subtype |
| GET | `/api/subtypes/{subtype}/dimensions` | Dimension config by subtype |
| GET | `/api/collection-videos` | Collection videos |
| POST | `/api/contact` | Submit contact form |
| POST | `/api/auth/register` | User registration |
| POST | `/api/auth/login` | User login |
| POST | `/api/auth/social/{provider}` | Social login (google/facebook) |

#### Protected Endpoints (auth required)
| Method | URI | Description |
|--------|-----|-------------|
| POST | `/api/auth/logout` | Logout |
| GET | `/api/auth/user` | Current user |
| GET | `/api/orders` | User's orders |
| POST | `/api/orders` | Create order |
| PUT | `/api/orders/{order}/cancel` | Cancel order |
| GET | `/api/orders/{order}/tracking` | Order tracking |
| GET | `/api/quotes` | User's quotes |
| POST | `/api/quotes` | Create quote |
| PUT | `/api/quotes/{quote}/accept` | Accept quote |
| PUT | `/api/quotes/{quote}/reject` | Reject quote |
| GET | `/api/account` | Account details |
| PUT | `/api/account` | Update account |
| GET | `/api/conversations` | Support conversations |
| POST | `/api/conversations` | Create conversation |
| POST | `/api/conversations/{conversation}/messages` | Send message |
| GET | `/api/notifications` | User notifications |
| GET | `/api/chat/context` | AI chat context |
| POST | `/api/chat/ask` | Ask AI assistant |
| GET | `/api/collection-videos/{video}` | Single video |

#### Admin Endpoints (admin auth required)
| Method | URI | Description |
|--------|-----|-------------|
| GET/POST/PUT/DELETE | `/api/admin/products` | Product CRUD |
| GET/POST/PUT/DELETE | `/api/admin/categories` | Category CRUD |
| GET/POST/PUT/DELETE | `/api/admin/orders` | Order management |
| GET/POST/PUT/DELETE | `/api/admin/quotes` | Quote management |
| GET/POST/PUT/DELETE | `/api/admin/users` | User management |
| GET/POST/PUT/DELETE | `/api/admin/collection-videos` | Video CRUD |
| GET/POST/PUT/DELETE | `/api/admin/contact-messages` | Contact messages |
| GET/POST/PUT/DELETE | `/api/admin/support` | Support management |
| PUT | `/api/admin/products/update-images-order` | Reorder images |

---

## 4. Frontend (Angular 17)

### 4.1 Tech Stack

| Component           | Technology               |
|---------------------|--------------------------|
| Framework           | Angular 17.3             |
| Language            | TypeScript (strict mode) |
| Rendering           | SSR with Express         |
| 3D Graphics         | Three.js (r184)          |
| Styling             | CSS custom properties    |
| Icons               | Font Awesome 6           |
| Real-time           | Pusher                   |
| State Management    | Angular Signals          |

### 4.2 Directory Structure

```
marmora_front/src/app/
├── app.component.ts/html/css       # Root component
├── app.config.ts                   # App providers
├── app.routes.ts                   # Route definitions
│
├── core/
│   ├── guards/auth.guard.ts        # Route guards
│   ├── interceptors/
│   │   ├── auth.interceptor.ts     # Bearer token injection
│   │   └── loading.interceptor.ts  # Loading indicator
│   ├── models/index.ts             # TypeScript interfaces
│   └── services/
│       ├── auth.service.ts         # Authentication
│       ├── social-auth.service.ts  # Google/Facebook OAuth
│       ├── product.service.ts      # Product catalog
│       ├── cart.service.ts         # Shopping cart
│       ├── account.service.ts      # User profile
│       ├── chat.service.ts         # AI chatbot
│       ├── admin.service.ts        # Admin API
│       ├── wishlist.service.ts     # Wishlist
│       ├── translate.service.ts    # i18n translations
│       ├── theme.service.ts        # Dark/light theme
│       ├── notification.service.ts # Notifications
│       ├── dropdown.service.ts     # Dropdown state
│       ├── loading.service.ts      # Loading state
│       ├── login-prompt.service.ts # Login prompt modal
│       ├── quote-drawer.service.ts # Quote drawer
│       ├── user-drawer.service.ts  # User drawer
│       └── collection-video.service.ts
│
├── pages/                          # Lazy-loaded page components
│   ├── landing/                    # Homepage
│   ├── catalog/                    # Product catalog + filters
│   ├── product-detail/             # Product detail + 3D viewer
│   ├── auth/                       # Login/Register + social auth
│   ├── account/                    # User dashboard
│   ├── admin/                      # Admin panel
│   ├── checkout/                   # Order checkout
│   ├── wishlist/                   # User wishlist
│   ├── about/                      # About page
│   └── contact/                    # Contact form
│
├── shared/                         # Reusable components
│   ├── navbar/                     # Main navigation
│   ├── marble-canvas/              # Three.js 3D viewer
│   ├── chatbot/                    # AI assistant UI
│   ├── cart-drawer/                # Cart sidebar
│   ├── wishlist-drawer/            # Wishlist sidebar
│   ├── quote-drawer/               # Quote sidebar
│   ├── user-drawer/                # User menu sidebar
│   ├── dimension-modal/            # Product customization
│   ├── order-tracking/             # Order status
│   ├── notification-dropdown/      # Notifications
│   ├── message-dropdown/           # Messages
│   ├── login-prompt/               # Login prompt modal
│   ├── accept-quote-modal/         # Quote acceptance
│   └── delete-account-modal/       # Account deletion
│
├── product-visualizer-component/   # Standalone 3D configurator
└── styles/drawer-animations.css    # Shared animations
```

### 4.3 Route Definitions (app.routes.ts)

| Path                | Guard  | Component       |
|---------------------|--------|-----------------|
| `/`                 | None   | Landing         |
| `/catalog/:catalogType` | None | Catalog     |
| `/products/:slug`   | None   | Product Detail  |
| `/auth/login`       | Guest  | Auth            |
| `/auth/register`    | Guest  | Auth            |
| `/account`          | Auth   | Account         |
| `/admin`            | Admin  | Admin           |
| `/checkout`         | Auth   | Checkout        |
| `/wishlist`         | Auth   | Wishlist        |
| `/about`            | None   | About           |
| `/contact`          | None   | Contact         |

---

## 5. Database Schema

**37 migration files** covering all entities:

### Core Tables
| Table | Key Columns |
|-------|-------------|
| `users` | id, name, email, password, role (admin/b2b/b2c), phone, google_id, facebook_id, avatar, preferred_language, is_active |
| `company_profiles` | id, user_id, company_name, tax_id, address, phone, website |
| `categories` | id, name, slug, description, parent_id, is_active, order |
| `products` | id, name, slug, description, price, family, subtype, application_space, engineered_brand, dimensions, thickness, stock, is_featured, is_active, category_id |
| `product_images` | id, product_id, image_path, alt_text, is_primary, order |
| `product_surface_finishes` | id, product_id, name, description, price_modifier |
| `product_edge_profiles` | id, name, description, applicable_subtypes |
| `orders` | id, user_id, status, total, shipping_address, payment_status, tracking_number |
| `order_items` | id, order_id, product_id, quantity, price, shape, thickness, subtype, edge_profile, surface_finish |
| `quotes` | id, user_id, status, total, notes, expires_at |
| `quote_items` | id, quote_id, product_id, quantity, price, shape |
| `conversations` | id, user_id, admin_id, subject, status |
| `conversation_messages` | id, conversation_id, user_id, message, is_admin |
| `contact_messages` | id, name, email, subject, message, is_read |
| `collection_videos` | id, title, url, description, is_active, order |
| `notifications` | id, type, data, read_at, notifiable_id/type |
| `personal_access_tokens` | id, tokenable_id/type, name, token, abilities, last_used_at |

---

## 6. Configuration

### 6.1 Backend (.env)
```
APP_NAME=Marmora
DB_CONNECTION=mysql
DB_DATABASE=marmora
SANCTUM_STATEFUL_DOMAINS=localhost:4200,localhost:8000,*.ngrok-free.dev
FRONTEND_URL=https://cruelty-divisible-mortician.ngrok-free.dev
GEMINI_API_KEY=AIzaSyCH1Es_ZBe8Fbf6yvHIAlUzD5tqcqx-DNM
GOOGLE_CLIENT_ID=326547831136-*.apps.googleusercontent.com
FACEBOOK_CLIENT_ID=1334462331907759
```

### 6.2 Frontend (environment.ts)
```typescript
apiUrl: 'http://localhost:8000/api',
googleClientId: '326547831136-*.apps.googleusercontent.com',
facebookAppId: '1334462331907759'
```

### 6.3 CORS Configuration
- Allowed origins: `FRONTEND_URL` env var + `*.ngrok-free.dev` + `localhost:\d+`
- All methods, all headers

### 6.4 Sanctum (API Auth)
- Stateful domains configured for SPA authentication
- Token-based for external/mobile access
- Guards: `web` (session), `sanctum` (token)

---

## 7. Features

| Feature | Implementation |
|---------|---------------|
| **Product Catalog** | Filtered, paginated, searchable catalog with material taxonomy (family, subtype, application) |
| **Product Customization** | Dimension input, surface finish, edge profile selection per product subtype |
| **3D Visualization** | Two Three.js components: marble-canvas (material viewer) + product-visualizer (full configurator) |
| **B2C Shopping** | Cart, checkout, order tracking |
| **B2B Quoting** | Company profiles, quote requests, admin review, accept/reject workflow |
| **Social Login** | Google One Tap + Facebook Login (OAuth2) |
| **AI Assistant** | Google Gemini 2.5 Flash chatbot with catalog-aware context |
| **Multi-language** | English, French, Arabic (i18n with RTL support) |
| **Admin Panel** | Full CRUD: products, orders, quotes, users, support, videos, contacts |
| **Notifications** | 10 notification types (new orders, quotes, support messages, etc.) |
| **Real-time** | Pusher integration for live updates |
| **Dark Theme** | Dark luxury theme (black `#0d0d0d`, cream `#f5f1e8`, gold `#c5a059`) |
| **Responsive** | Mobile-first design with breakpoints at 640px, 768px, 1024px |
| **SSR** | Angular server-side rendering with Express |

---

## 8. Setup Guide

### Prerequisites
- PHP 8.2+
- Composer
- Node.js 20+
- MySQL 8.0+
- Angular CLI 17+

### Backend Setup
```bash
cd marmora_backend
composer install
cp .env.example .env        # Configure DB, APIs
php artisan key:generate
php artisan migrate --seed
php artisan storage:link
php artisan serve            # http://localhost:8000
```

### Frontend Setup
```bash
cd marmora_front
npm install
ng serve                    # http://localhost:4200
```

### Build for Production
```bash
# Backend
php artisan optimize

# Frontend
ng build --configuration production
```

### CI/CD (GitHub Actions)
- **Frontend job**: Node 20, `npm ci`, `ng build`
- **Backend job**: PHP 8.2, Composer, PHPUnit tests
- Triggers: push, pull requests

---

## 9. Documentation Files

| File | Description |
|------|-------------|
| `README.md` | Complete project setup guide |
| `3D_Visualization_Documentation.md` | Three.js 3D visualization notes |
| `PRODUCT_DETAILS_DESIGN.md` | Luxury product detail page design spec |
| `SOCIAL_LOGIN_SETUP.md` | Google/Facebook OAuth configuration |
| `SOCIAL_LOGIN_COMPLETE_FIX.md` | Social login bug fixes |
| `FACEBOOK_APP_CONFIGURATION_GUIDE.md` | Facebook app settings |
| `FACEBOOK_LOGIN_HTTPS_FIX.md` | HTTPS workaround for Facebook login |

---

## 10. Detailed Directory Tree

```
Marmora/
│
├── .github/workflows/ci.yml
├── .gitignore
├── .vscode/extensions.json
├── README.md
├── package.json
├── package-lock.json
├── UML/use-case-diagram.puml
│
├── 3D_Visualization_Documentation.md
├── PRODUCT_DETAILS_DESIGN.md
├── SOCIAL_LOGIN_SETUP.md
├── SOCIAL_LOGIN_COMPLETE_FIX.md
├── FACEBOOK_APP_CONFIGURATION_GUIDE.md
├── FACEBOOK_LOGIN_HTTPS_FIX.md
│
├── marmora_backend/
│   ├── .env
│   ├── .env.example
│   ├── artisan
│   ├── composer.json
│   ├── composer.lock
│   ├── package.json
│   ├── vite.config.js
│   ├── README.md
│   │
│   ├── app/
│   │   ├── Events/
│   │   │   ├── OrderStatusUpdated.php
│   │   │   └── SupportMessageSent.php
│   │   ├── Exceptions/GeminiRateLimitException.php
│   │   ├── Http/
│   │   │   ├── Controllers/
│   │   │   │   ├── Controller.php
│   │   │   │   ├── ChatController.php
│   │   │   │   └── Api/
│   │   │   │       ├── Auth/AuthController.php
│   │   │   │       ├── AccountController.php
│   │   │   │       ├── ProductController.php
│   │   │   │       ├── OrderController.php
│   │   │   │       ├── QuoteController.php
│   │   │   │       ├── ContactController.php
│   │   │   │       ├── LanguageController.php
│   │   │   │       ├── NotificationController.php
│   │   │   │       ├── CollectionVideoController.php
│   │   │   │       └── Admin/
│   │   │   │           ├── AdminProductController.php
│   │   │   │           ├── AdminOrderController.php
│   │   │   │           ├── AdminQuoteController.php
│   │   │   │           ├── AdminUserController.php
│   │   │   │           ├── AdminContactController.php
│   │   │   │           ├── AdminSupportController.php
│   │   │   │           └── AdminCollectionVideoController.php
│   │   │   ├── Middleware/
│   │   │   │   ├── AdminMiddleware.php
│   │   │   │   └── EnsureUserIsActive.php
│   │   │   └── Requests/Auth/
│   │   │       ├── LoginRequest.php
│   │   │       ├── RegisterRequest.php
│   │   │       └── SocialLoginRequest.php
│   │   ├── Models/
│   │   │   ├── User.php
│   │   │   ├── Category.php
│   │   │   ├── Product.php
│   │   │   ├── ProductImage.php
│   │   │   ├── ProductSurfaceFinish.php
│   │   │   ├── ProductEdgeProfile.php
│   │   │   ├── Order.php
│   │   │   ├── OrderItem.php
│   │   │   ├── Quote.php
│   │   │   ├── QuoteItem.php
│   │   │   ├── Conversation.php
│   │   │   ├── ConversationMessage.php
│   │   │   ├── ContactMessage.php
│   │   │   ├── CollectionVideo.php
│   │   │   └── CompanyProfile.php
│   │   ├── Notifications/
│   │   │   ├── ContactMessageNotification.php
│   │   │   ├── NewOrderNotification.php
│   │   │   ├── NewQuoteNotification.php
│   │   │   ├── OrderStatusNotification.php
│   │   │   ├── QuoteConvertedNotification.php
│   │   │   ├── QuoteRejectedNotification.php
│   │   │   ├── QuoteReviewedNotification.php
│   │   │   ├── SupportMessageNotification.php
│   │   │   ├── SupportReplyNotification.php
│   │   │   └── SupportRequestNotification.php
│   │   ├── Providers/AppServiceProvider.php
│   │   └── Services/
│   │       ├── AIService.php
│   │       └── SubtypeConfig.php
│   │
│   ├── bootstrap/
│   ├── config/
│   │   ├── app.php
│   │   ├── auth.php
│   │   ├── broadcasting.php
│   │   ├── cache.php
│   │   ├── cors.php
│   │   ├── database.php
│   │   ├── filesystems.php
│   │   ├── logging.php
│   │   ├── mail.php
│   │   ├── queue.php
│   │   ├── sanctum.php
│   │   ├── services.php
│   │   └── session.php
│   │
│   ├── database/
│   │   ├── factories/UserFactory.php
│   │   ├── migrations/ (37 files)
│   │   └── seeders/
│   │       ├── DatabaseSeeder.php
│   │       ├── CollectionVideoSeeder.php
│   │       └── ProductTaxonomySeeder.php
│   │
│   ├── public/
│   │   ├── .htaccess
│   │   ├── index.php
│   │   └── storage/
│   │
│   ├── resources/views/
│   ├── routes/
│   │   ├── api.php
│   │   ├── console.php
│   │   └── web.php
│   │
│   ├── storage/
│   ├── tests/
│   │   ├── Feature/ExampleTest.php
│   │   ├── Unit/ExampleTest.php
│   │   └── TestCase.php
│   │
│   ├── vendor/
│   │
│   └── (utility scripts)
│       ├── _list_cats.php
│       ├── _list_products.php
│       ├── merge_backup.php
│       ├── merge_backup_resume.php
│       ├── tmp_admin_token.php
│       ├── tmp_check_tables.php
│       ├── tmp_gemini_test.py
│       └── tmp_repair_edge_profiles.php
│
├── marmora_front/
│   ├── .editorconfig
│   ├── .gitignore
│   ├── angular.json
│   ├── package.json
│   ├── package-lock.json
│   ├── server.ts
│   ├── tsconfig.json
│   ├── tsconfig.app.json
│   ├── tsconfig.spec.json
│   ├── README.md
│   │
│   ├── dist/
│   ├── node_modules/
│   │
│   └── src/
│       ├── favicon.ico
│       ├── index.html
│       ├── main.ts
│       ├── main.server.ts
│       ├── styles.css
│       │
│       ├── environments/
│       │   ├── environment.ts
│       │   └── environment.production.ts
│       │
│       ├── assets/
│       │   ├── logo/
│       │   ├── products/ (80+ product images + PBR textures)
│       │   ├── quarries/
│       │   ├── projets marbre/
│       │   │   ├── interior/
│       │   │   ├── revetement/
│       │   │   └── salle d'eau/
│       │   ├── carrara_marble_quarry_gltf/
│       │   │   ├── scene.gltf
│       │   │   ├── scene.bin
│       │   │   └── textures/
│       │   ├── marble_pillar/
│       │   ├── about/
│       │   ├── lobby.jpg
│       │   └── marmora-guide.md
│       │
│       └── app/
│           ├── app.component.ts
│           ├── app.component.html
│           ├── app.component.css
│           ├── app.component.spec.ts
│           ├── app.config.ts
│           ├── app.config.server.ts
│           ├── app.routes.ts
│           │
│           ├── core/
│           │   ├── guards/auth.guard.ts
│           │   ├── interceptors/
│           │   │   ├── auth.interceptor.ts
│           │   │   └── loading.interceptor.ts
│           │   ├── models/index.ts
│           │   └── services/
│           │       ├── account.service.ts
│           │       ├── admin.service.ts
│           │       ├── auth.service.ts
│           │       ├── cart.service.ts
│           │       ├── chat.service.ts
│           │       ├── collection-video.service.ts
│           │       ├── dropdown.service.ts
│           │       ├── loading.service.ts
│           │       ├── login-prompt.service.ts
│           │       ├── notification.service.ts
│           │       ├── product.service.ts
│           │       ├── quote-drawer.service.ts
│           │       ├── social-auth.service.ts
│           │       ├── theme.service.ts
│           │       ├── translate.service.ts
│           │       ├── user-drawer.service.ts
│           │       └── wishlist.service.ts
│           │
│           ├── pages/
│           │   ├── landing/
│           │   ├── catalog/
│           │   ├── product-detail/
│           │   ├── auth/
│           │   ├── account/
│           │   ├── admin/
│           │   │   └── admin-overlay/
│           │   ├── checkout/
│           │   ├── wishlist/
│           │   ├── about/
│           │   └── contact/
│           │
│           ├── shared/
│           │   ├── navbar/
│           │   ├── marble-canvas/
│           │   ├── chatbot/
│           │   ├── cart-drawer/
│           │   ├── wishlist-drawer/
│           │   ├── quote-drawer/
│           │   ├── user-drawer/
│           │   ├── dimension-modal/
│           │   ├── order-tracking/
│           │   ├── notification-dropdown/
│           │   ├── message-dropdown/
│           │   ├── login-prompt/
│           │   ├── accept-quote-modal/
│           │   └── delete-account-modal/
│           │
│           ├── product-visualizer-component/
│           └── styles/drawer-animations.css
```

---

*Generated on 2026-06-24*
