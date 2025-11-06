# Vercel v0 — Frontend Requirements (Customer-side)

Generated: November 6, 2025

This document describes the frontend (customer-side) pages and the backend endpoints the frontend must consume for Vercel deployment (v0). The frontend will be built in Next.js and will connect to this repository's backend.

Important: We want exact powerlook website style design.

## 1. Overview
- Frontend framework: Next.js (React)
- Hosting: Vercel (v0)
- Backend: This project (Django) — REST and GraphQL endpoints described below
- Purpose: Customer-facing storefront only (no admin UI)

## 2. Backend endpoints (required)
The frontend must support both REST and GraphQL where appropriate.

A. GraphQL (primary product and storefront interactions)
- Storefront GraphQL: `POST /graphql/` (GraphiQL available on backend)
  - Queries: products, collections, categories, product variants, cart, customer, orders (storefront queries)
  - Mutations: add to cart, remove from cart, update cart item, create order, customer signup/login/token refresh, address management

B. REST API (supporting endpoints)
Use REST for simple operations or where existing serializers are provided.
- Products: `/product/storefront/api/` (list, detail, search, filters)
- Cart: `/cart/storefront/api/` (get cart, add item, update item, remove item)
- Users: `/user/storefront/api/` (signup, login, profile, addresses)
- Orders: `/order/storefront/api/` (create order, order history, order detail)
- File Manager (images): `/filemanager/storefront/api/` (upload/display media if needed)
- Payment: `/payment/storefront/api/` (initiate payment, confirm payment status)
- Shipping: `/shipping/storefront/api/` (shipping methods & rates)
- Tax: `/tax/storefront/api/` (tax calculation endpoints if exposed)
- SEO: `/seo/storefront/api/` (page metadata if needed)

C. Auth / Tokens
- Login / Signup: REST or GraphQL as implemented in backend (check `nxtbn.users.storefront_mutation` and `nxtbn.users.api.storefront`)
- JWT: frontend should store access/refresh tokens in secure HttpOnly cookies if backend sets them; otherwise follow backend auth flow.

D. Media & Static
- Media served from backend `MEDIA_URL` or S3 (check backend config). Frontend should load images using absolute URLs returned by APIs.

## 3. Pages required (customer-side)
Build these pages in Next.js (pages/app routes). Each page should mirror the structure and UX behavior of the Powerlook website exactly.

1. Home page (`/`) — hero banner, featured collections, featured products, promotional blocks
2. Category listing (`/category/[slug]`) — product grid, filters (price, brand, attributes), sorting, pagination
3. Collection listing (`/collection/[slug]`) — similar to category
4. Product detail page (`/product/[slug]`) — images gallery, thumbnails, price, variants, add-to-cart, description, specifications, reviews, related products
5. Search results (`/search?q=...`) — search bar, suggestions, results list with filters
6. Cart (`/cart`) — cart items, quantity controls, coupon code input, subtotal, proceed to checkout
7. Checkout (`/checkout`) — shipping address form, shipping method selection, payment method selection, order review, place order
8. Order confirmation (`/order/[order_number]/confirmation`) — order details, estimated delivery
9. My Account (`/account`) — profile overview
10. My Orders (`/account/orders`) — past orders list and order detail pages
11. Address book (`/account/addresses`) — add/edit addresses
12. Login / Signup (`/account/login`, `/account/signup`) — auth forms (if using GraphQL mutations, call those)
13. Wishlist (`/wishlist`) — optional but include if backend supports it
14. Contact / About / Policy pages (`/contact`, `/about`, `/terms`, `/privacy`) — static pages
15. Static content pages and CMS-driven pages (`/[slug]`) — fetch via SEO or CMS endpoints if available

## 4. UI & UX Notes (explicit requirement)
- The frontend design and user experience must be exactly like www.powerlook.in.
- No alternative suggestions or deviations: We want exact powerlook website style design.

## 5. Integration details
- Authentication: use backend-provided auth flows (JWT or session) as implemented. Prefer secure HttpOnly cookies when backend sets them.
- Cart persistence: use backend cart endpoint for authenticated users; for anonymous users, maintain local cart and sync after login.
- Prices & currency: use values returned by backend APIs (prices are stored in subunits). Format amounts on frontend to the currency returned.
- Images: use image URLs from backend/filemanager or S3. Implement responsive image loading.
- GraphQL vs REST: prefer GraphQL for product/catalog interactions when available; fallback to REST endpoints listed above.

## 6. Deployment checklist for Vercel (v0)
- Create Next.js project in separate repo or subfolder.
- Configure environment variables for API base URL (e.g., NEXT_PUBLIC_API_URL = `https://your-backend.example.com`)
- Ensure CORS on backend allows Vercel origin (update `CORS_ALLOWED_ORIGINS` in backend env)
- Set secure cookies and sameSite policies as backend requires

## 7. Files/Routes mapping (quick)
- `/` → Home
- `/category/[slug]` → Category listing
- `/collection/[slug]` → Collection listing
- `/product/[slug]` → Product detail
- `/search` → Search
- `/cart` → Cart
- `/checkout` → Checkout
- `/account/*` → Account area (login, orders, addresses)
- `/order/[order_number]/confirmation` → Order confirmation
- `/wishlist` → Wishlist
- `/contact`, `/about`, `/terms`, `/privacy` → Static pages


---

If you want, I can now scaffold a minimal Next.js project with the route files and example API calls wired to the backend endpoints listed above. (No UI styling beyond the required structure will be added here—styling must match Powerlook exactly as requested.)
