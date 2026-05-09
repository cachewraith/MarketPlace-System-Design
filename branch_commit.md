# Professional Git Branch Names and Commit Messages

## Branch Rule

Every branch name must end with:

```bash
-yyyymmdd
```

Final branch format:

```bash
<type>/<short-description>-yyyymmdd
```

Examples:

```bash
feature/user-login-20260509
fix/cart-total-calculation-20260509
refactor/payment-service-20260509
docs/api-documentation-20260509
chore/docker-setup-20260509
release/v1.0.0-20260509
hotfix/payment-webhook-duplicate-20260509
```

---

# Branch Types

```bash
feature/   # New feature
fix/       # Bug fix
hotfix/    # Urgent production fix
refactor/  # Improve code without changing behavior
docs/      # Documentation only
test/      # Testing related
chore/     # Config, package, setup, maintenance
release/   # Prepare release version
security/  # Security improvement
perf/      # Performance improvement
```

---

# Commit Message Rule

Use this professional commit format:

```bash
xxx(xxxx): xxxxxxxxxxxxxx
```

Real format:

```bash
<type>(<scope>): <message>
```

Examples:

```bash
feat(auth): add user login endpoint
fix(cart): correct cart total calculation
refactor(payment): extract payment logic into service
docs(api): update API documentation
test(order): add order lifecycle tests
chore(docker): configure Docker environment
security(auth): add login rate limiting
perf(product): optimize product query performance
```

---

# Commit Types

```bash
feat       # New feature
fix        # Bug fix
refactor   # Code cleanup without changing behavior
docs       # Documentation changes
style      # Code formatting only
test       # Add or update tests
chore      # Config, package, setup, maintenance
perf       # Performance improvement
security   # Security improvement
build      # Build system or dependency changes
ci         # CI/CD configuration changes
revert     # Revert previous commit
```

---

# Commit Scope Examples

```bash
auth
user
profile
admin
role
permission
vendor
store
product
category
cart
checkout
coupon
order
payment
webhook
notification
audit
api
database
migration
seed
docker
ci
config
deps
ui
navigation
theme
screen
component
service
```

---

# Branch Name Examples

## Authentication Branches

```bash
feature/user-login-20260509
feature/user-registration-20260509
feature/email-verification-20260509
feature/password-reset-20260509
feature/social-login-google-20260509
feature/social-login-facebook-20260509
fix/login-validation-error-20260509
fix/email-verification-token-expired-20260509
refactor/auth-service-20260509
test/auth-feature-tests-20260509
security/login-rate-limiting-20260509
```

## User Profile Branches

```bash
feature/user-profile-20260509
feature/update-profile-20260509
feature/upload-avatar-20260509
feature/change-password-20260509
fix/profile-image-upload-20260509
fix/profile-validation-error-20260509
refactor/user-controller-20260509
test/user-profile-tests-20260509
```

## Admin Branches

```bash
feature/admin-dashboard-20260509
feature/admin-user-management-20260509
feature/role-permission-management-20260509
feature/admin-vendor-approval-20260509
fix/admin-user-filter-20260509
fix/admin-permission-check-20260509
refactor/admin-layout-20260509
test/admin-management-tests-20260509
```

## Product Branches

```bash
feature/product-management-20260509
feature/product-catalog-20260509
feature/product-variants-20260509
feature/product-images-20260509
feature/product-search-20260509
feature/product-filtering-20260509
feature/category-management-20260509
feature/nested-categories-20260509
fix/product-price-calculation-20260509
fix/product-stock-update-20260509
refactor/product-service-20260509
test/product-feature-tests-20260509
perf/product-query-optimization-20260509
```

## Cart And Checkout Branches

```bash
feature/shopping-cart-20260509
feature/add-to-cart-20260509
feature/cart-item-quantity-20260509
feature/checkout-flow-20260509
feature/coupon-validation-20260509
feature/shipping-fee-calculation-20260509
feature/tax-calculation-20260509
fix/cart-total-calculation-20260509
fix/coupon-discount-bug-20260509
refactor/cart-service-20260509
test/cart-checkout-tests-20260509
```

## Order Branches

```bash
feature/order-management-20260509
feature/order-status-history-20260509
feature/order-tracking-20260509
feature/order-invoice-20260509
feature/order-cancellation-20260509
fix/order-payment-status-20260509
fix/order-stock-deduction-20260509
refactor/order-service-20260509
test/order-lifecycle-tests-20260509
```

## Payment Branches

```bash
feature/payment-gateway-20260509
feature/aba-payway-payment-20260509
feature/bakong-khqr-payment-20260509
feature/payment-webhook-20260509
feature/payment-idempotency-20260509
fix/payment-callback-validation-20260509
fix/webhook-duplicate-processing-20260509
refactor/payment-service-20260509
test/payment-webhook-tests-20260509
security/payment-webhook-signature-20260509
```

## API Branches

```bash
feature/api-authentication-20260509
feature/api-product-endpoints-20260509
feature/api-order-endpoints-20260509
feature/api-response-format-20260509
feature/api-pagination-20260509
fix/api-validation-error-20260509
fix/api-pagination-response-20260509
refactor/api-resources-20260509
docs/api-documentation-20260509
test/api-feature-tests-20260509
```

## React Native / Frontend Branches

```bash
feature/auth-screens-20260509
feature/home-screen-20260509
feature/product-list-screen-20260509
feature/product-detail-screen-20260509
feature/cart-screen-20260509
feature/checkout-screen-20260509
feature/profile-screen-20260509
feature/bottom-tab-navigation-20260509
feature/onboarding-flow-20260509
feature/app-theme-20260509
feature/mock-data-20260509
fix/mobile-layout-overflow-20260509
fix/navigation-back-button-20260509
refactor/components-structure-20260509
refactor/screen-layouts-20260509
test/component-tests-20260509
```

## DevOps / Config Branches

```bash
chore/docker-setup-20260509
chore/env-configuration-20260509
chore/github-actions-20260509
chore/ci-cd-pipeline-20260509
chore/update-dependencies-20260509
chore/eslint-prettier-config-20260509
fix/docker-build-error-20260509
fix/deployment-config-20260509
docs/deployment-guide-20260509
```

## Release Branches

```bash
release/v1.0.0-20260509
release/v1.1.0-20260509
release/v1.2.0-20260509
release/v2.0.0-20260509
hotfix/v1.0.1-login-bug-20260509
hotfix/v1.0.2-payment-webhook-20260509
hotfix/v1.0.3-cart-total-20260509
```

---

# Commit Message Examples

## Authentication Commits

```bash
feat(auth): add user login endpoint
feat(auth): add user registration endpoint
feat(auth): add email verification flow
feat(auth): implement password reset
feat(auth): add Google social login
feat(auth): add Facebook social login
fix(auth): validate login credentials correctly
fix(auth): prevent duplicate email registration
fix(auth): resolve expired verification token issue
refactor(auth): move auth logic into service class
test(auth): add authentication feature tests
security(auth): add rate limiting to login endpoint
```

## User Profile Commits

```bash
feat(profile): add user profile API
feat(profile): allow users to update profile
feat(profile): add avatar upload support
feat(profile): implement change password feature
fix(profile): resolve profile image upload issue
fix(profile): validate profile update request
refactor(profile): clean user profile controller
test(profile): add user profile tests
```

## Admin Commits

```bash
feat(admin): create admin dashboard API
feat(admin): add admin user management
feat(role): implement role management
feat(permission): implement permission management
feat(vendor): add vendor approval workflow
fix(admin): resolve admin user filter issue
fix(permission): correct admin permission check
refactor(admin): clean admin dashboard logic
test(admin): add admin management tests
security(admin): restrict admin routes by permission
```

## Product Commits

```bash
feat(product): create product management API
feat(product): add product catalog endpoint
feat(product): add product variant support
feat(product): implement product image upload
feat(product): add product search
feat(product): add product filtering
feat(category): implement nested categories
fix(product): correct product price calculation
fix(product): prevent negative product stock
refactor(product): move product logic into service class
test(product): add product creation tests
perf(product): optimize product query performance
```

## Cart And Checkout Commits

```bash
feat(cart): implement shopping cart
feat(cart): add item to cart
feat(cart): update cart item quantity
feat(checkout): add checkout flow
feat(coupon): add coupon validation
feat(checkout): calculate shipping fee
feat(checkout): calculate tax amount
fix(cart): correct cart total calculation
fix(coupon): resolve coupon discount bug
refactor(cart): move cart logic into service class
test(cart): add cart calculation tests
```

## Order Commits

```bash
feat(order): add order management API
feat(order): add order status history
feat(order): implement order tracking
feat(order): generate order invoice
feat(order): add order cancellation
fix(order): resolve order payment status issue
fix(order): prevent incorrect stock deduction
refactor(order): clean order service logic
test(order): add order lifecycle tests
```

## Payment Commits

```bash
feat(payment): add payment gateway service
feat(payment): integrate ABA PayWay payment
feat(payment): integrate Bakong KHQR payment
feat(webhook): add payment webhook endpoint
feat(payment): add payment idempotency handling
fix(payment): validate payment callback correctly
fix(webhook): prevent duplicate webhook processing
refactor(payment): extract payment gateway interface
test(webhook): add payment webhook tests
security(webhook): validate payment webhook signature
```

## API Commits

```bash
feat(api): add API authentication
feat(api): create product API endpoints
feat(api): create order API endpoints
feat(api): standardize API response format
feat(api): add API pagination support
fix(api): resolve API validation error response
fix(api): correct API pagination response
refactor(api): clean API resources
docs(api): add API documentation
test(api): add API feature tests
```

## Database Commits

```bash
feat(database): create users table
feat(database): create products table
feat(database): create orders table
feat(database): add indexes to product table
feat(database): add soft deletes to orders table
fix(migration): correct nullable columns
fix(seed): resolve duplicate seed data
refactor(migration): clean database schema structure
test(database): add database relationship tests
```

## React Native / Frontend Commits

```bash
feat(screen): create authentication screens
feat(screen): build home screen layout
feat(screen): add product list screen
feat(screen): implement product detail screen
feat(screen): create cart screen
feat(screen): add checkout screen
feat(screen): create profile screen
feat(navigation): configure bottom tab navigation
feat(onboarding): add onboarding flow
feat(theme): setup app theme system
feat(mock): add mock product data
fix(ui): resolve mobile layout overflow
fix(navigation): fix back button behavior
refactor(component): split large component into reusable components
refactor(screen): reorganize screen folders
test(component): add component tests
```

## DevOps / Config Commits

```bash
chore(docker): configure Docker environment
chore(env): add environment example file
ci(github): configure GitHub Actions workflow
chore(deps): update composer dependencies
chore(deps): update npm packages
chore(config): configure ESLint and Prettier
fix(docker): resolve Docker build error
fix(deployment): correct deployment configuration
docs(deployment): add deployment guide
```

## Security Commits

```bash
security(auth): add rate limiting to login endpoint
security(webhook): validate payment webhook signature
security(api): sanitize user input fields
security(admin): restrict admin routes by permission
security(auth): enforce password confirmation
security(webhook): prevent duplicate webhook processing
```

## Performance Commits

```bash
perf(product): optimize product query performance
perf(api): improve API response time
perf(category): cache category tree response
perf(image): optimize image loading
perf(order): reduce database queries in order list
```

---

# Real Git Workflow Example

## Create New Feature Branch

```bash
git checkout develop
git pull origin develop
git checkout -b feature/payment-webhook-20260509
```

## Make Commits

```bash
git add .
git commit -m "feat(webhook): add payment webhook endpoint"

git add .
git commit -m "security(webhook): validate payment webhook signature"

git add .
git commit -m "fix(webhook): prevent duplicate webhook processing"

git add .
git commit -m "test(webhook): add payment webhook tests"
```

## Push Branch

```bash
git push origin feature/payment-webhook-20260509
```

---

# Best Branch Examples

```bash
feature/sanctum-authentication-20260509
feature/vendor-onboarding-20260509
feature/product-catalog-20260509
feature/cart-checkout-20260509
feature/payment-integration-20260509
feature/payment-webhook-20260509
feature/order-lifecycle-20260509
feature/admin-dashboard-20260509
feature/role-permission-management-20260509
feature/search-filtering-20260509
feature/notification-system-20260509
feature/audit-logging-20260509
```

---

# Best Commit Examples

```bash
feat(auth): implement Sanctum authentication
feat(vendor): add vendor onboarding workflow
feat(product): create product catalog API
feat(cart): implement cart and checkout flow
feat(payment): integrate payment gateway service
feat(webhook): add payment webhook handling
feat(order): add order lifecycle state transitions
feat(permission): configure role and permission management
feat(search): add product search and filtering
feat(notification): implement notification system
feat(audit): implement audit logging
```

---

# Simple Rule To Remember

## Branch

```bash
feature/what-you-are-building-yyyymmdd
fix/what-you-are-fixing-yyyymmdd
refactor/what-you-are-cleaning-yyyymmdd
docs/what-you-are-documenting-yyyymmdd
chore/what-you-are-configuring-yyyymmdd
```

## Commit

```bash
feat(scope): what you added
fix(scope): what you fixed
refactor(scope): what you improved
docs(scope): what you documented
test(scope): what you tested
chore(scope): what you configured
security(scope): what you secured
perf(scope): what you optimized
```
