# VivaSEO Architecture Documentation

## Table of Contents

1. [System Overview](#system-overview)
2. [Technology Stack](#technology-stack)
3. [Architecture Patterns](#architecture-patterns)
4. [Project Structure](#project-structure)
5. [Database Schema](#database-schema)
6. [Service Layer](#service-layer)
7. [Frontend Architecture](#frontend-architecture)
8. [API Design](#api-design)
9. [Authentication & Authorization](#authentication--authorization)
10. [Task Queue System](#task-queue-system)
11. [External Integrations](#external-integrations)
12. [Security Architecture](#security-architecture)
13. [Performance Considerations](#performance-considerations)

---

## System Overview

VivaSEO is built as a modern monorepo application following a microservices-inspired architecture with clear separation between frontend and backend concerns.

### High-Level Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                         Frontend                             │
│  Next.js 15 App Router + React 19 + TypeScript              │
│  Port: 3000 (dev) / 3003 (prod)                             │
└──────────────────┬──────────────────────────────────────────┘
                   │ REST API (JWT Auth)
                   ↓
┌─────────────────────────────────────────────────────────────┐
│                      Backend API                             │
│  Django 5.2.6 + Django REST Framework                       │
│  Port: 8000 (dev) / 8002 (prod)                             │
└──┬────────┬──────────┬──────────┬────────────────────────┬──┘
   │        │          │          │                        │
   ↓        ↓          ↓          ↓                        ↓
┌──────┐ ┌──────┐  ┌────────┐ ┌──────┐              ┌──────────┐
│ DB   │ │Redis │  │Celery  │ │Celery│              │ Crawl4AI │
│ PG16 │ │  7   │  │Worker  │ │ Beat │              │ Service  │
└──────┘ └──────┘  └────────┘ └──────┘              └──────────┘
   │
   └── External Services: Stripe, OpenAI, Cloudinary, Resend
```

### Service Communication

- **Frontend ↔ Backend**: REST API over HTTP/HTTPS
- **Backend ↔ Database**: PostgreSQL connection pool
- **Backend ↔ Redis**: Celery broker and result backend
- **Backend ↔ Crawl4AI**: HTTP API for SEO crawling
- **Backend ↔ External APIs**: HTTP/HTTPS (Stripe, OpenAI, etc.)

---

## Technology Stack

### Backend Stack

#### Core Framework
```python
Django 5.2.6                      # Web framework
djangorestframework 3.16.1        # REST API
djangorestframework-simplejwt 5.5.1  # JWT auth
drf-spectacular 0.28.0            # OpenAPI/Swagger
unfold 0.65.0                     # Admin interface
```

#### Database & Caching
```python
psycopg 3.2.10                    # PostgreSQL adapter
redis 6.4.0                       # Redis client
```

#### Task Queue
```python
celery 5.3.0                      # Distributed task queue
celery-beat 2.8.0                 # Periodic tasks
```

#### External Services
```python
stripe 11.4.0                     # Payment processing
openai 1.59.9                     # AI content generation
cloudinary 1.42.0                 # File storage
resend 2.6.0                      # Email delivery
crawl4ai 0.7.3                    # Web crawling
```

#### Development & Testing
```python
pytest-django 4.9.0               # Test framework
factory-boy 3.3.1                 # Test fixtures
ruff 0.9.1                        # Linting
```

### Frontend Stack

#### Core Framework
```json
"next": "15.2.4"                  // React framework
"react": "19.0.0"                 // UI library
"typescript": "5.7.2"             // Type safety
```

#### Authentication & State
```json
"next-auth": "4.24.11"            // Authentication
"react-hook-form": "7.54.2"       // Form handling
"zod": "3.24.1"                   // Schema validation
```

#### Styling
```json
"tailwindcss": "3.4.17"           // Utility-first CSS
"tailwind-merge": "2.6.0"         // Class merging
"clsx": "2.1.1"                   // Class composition
```

#### Development Tools
```json
"@biomejs/biome": "1.9.4"         // Linter & formatter
"openapi-typescript-codegen": "0.29.0"  // Type generation
```

### Infrastructure

- **Containerization**: Docker 24+, Docker Compose
- **Database**: PostgreSQL 16
- **Cache/Broker**: Redis 7
- **Web Server**: Gunicorn (production)
- **Static Files**: WhiteNoise

---

## Architecture Patterns

### Backend Patterns

#### 1. MVT (Model-View-Template) with REST

Django's MVT pattern extended with DRF for API-first architecture:

```
┌─────────┐      ┌──────────┐      ┌─────────────┐      ┌──────────┐
│ Request │─────▶│ ViewSet  │─────▶│  Serializer │─────▶│  Model   │
│  (API)  │      │ (Router) │      │(Validation) │      │   (ORM)  │
└─────────┘      └──────────┘      └─────────────┘      └──────────┘
                       │                                       │
                       ↓                                       ↓
                 ┌──────────┐                           ┌──────────┐
                 │ Service  │                           │ Database │
                 │  Layer   │                           │   (PG)   │
                 └──────────┘                           └──────────┘
```

**Key Components:**
- **Models**: Database schema and ORM
- **ViewSets**: API endpoints and routing
- **Serializers**: Data validation and transformation
- **Services**: Business logic layer

#### 2. Service Layer Pattern

Business logic is separated into dedicated service modules:

```python
# backend/api/services/
audit_service.py          # SEO audit logic
email_service.py          # Email sending
pdf_service.py            # Report generation
stripe_service.py         # Payment processing
ai_service.py             # AI content generation
webhook_handlers.py       # External webhooks
```

**Benefits:**
- Separation of concerns
- Reusable business logic
- Easier testing
- Clear dependencies

**Example:**
```python
# api/apis/audit_viewset.py
from api.services.audit_service import AuditService

class AuditViewSet(ViewSet):
    @action(detail=False, methods=['post'])
    def start(self, request):
        audit = AuditService.create_audit(
            user=request.user,
            url=request.data['url']
        )
        return Response(AuditSerializer(audit).data)
```

#### 3. Task Queue Pattern

Long-running operations are handled asynchronously:

```python
# Synchronous request
POST /api/audits/start/
  ↓
# Create audit record
audit = SEOScan.objects.create(status='pending')
  ↓
# Queue background task
perform_seo_audit_task.delay(audit.id)
  ↓
# Immediate response
return Response({'id': audit.id, 'status': 'pending'})

# Background processing
[Celery Worker] → Crawl site → Analyze → Generate report → Send email
```

#### 4. API-First Design

All backend functionality exposed via REST API:

- OpenAPI/Swagger schema
- Type-safe client generation
- Consistent response format
- Comprehensive error handling

### Frontend Patterns

#### 1. Server Components First

Next.js 15 App Router with React Server Components:

```tsx
// Default: Server Component
export default async function DashboardPage() {
  const session = await getServerSession(authOptions)
  const data = await fetchDashboardData(session)

  return <DashboardView data={data} />
}

// Client Component when needed
'use client'
export function InteractiveChart({ data }) {
  const [filter, setFilter] = useState('week')
  // ... client-side interactivity
}
```

#### 2. Server Actions Pattern

Form submissions and mutations via server actions:

```tsx
// app/actions/audit-actions.ts
'use server'

export async function startAudit(data: FormData) {
  const session = await getServerSession()
  const client = await getApiClient(session)

  const result = await client.audits.auditsStartCreate({
    url: data.get('url'),
    scan_type: 'manual'
  })

  revalidatePath('/dashboard')
  return { success: true, audit: result }
}

// In component
<form action={startAudit}>
  <input name="url" />
  <button type="submit">Start Audit</button>
</form>
```

#### 3. API Client Pattern

Type-safe API client generated from OpenAPI schema:

```typescript
// lib/api.ts
import { ApiClient } from '@frontend/types/api'

export async function getApiClient(session?: Session) {
  return new ApiClient({
    BASE: process.env.NEXT_PUBLIC_API_URL,
    HEADERS: session ? {
      Authorization: `Bearer ${session.accessToken}`
    } : undefined
  })
}

// Usage
const client = await getApiClient(session)
const audits = await client.audits.auditsList()
```

#### 4. Monorepo Pattern

```
frontend/
├── apps/
│   └── web/                    # Main Next.js app
│       ├── app/               # App Router pages
│       ├── actions/           # Server actions
│       ├── components/        # React components
│       └── lib/              # Utilities
│
└── packages/
    ├── types/                 # Shared TypeScript types
    └── ui/                   # Shared UI components
```

**Benefits:**
- Code sharing between apps
- Centralized type definitions
- Reusable UI components
- Single dependency lock file

---

## Project Structure

### Backend Structure

```
backend/
├── api/                        # Main Django app
│   ├── apis/                  # ViewSets (REST endpoints)
│   │   ├── analytics_viewset.py
│   │   ├── audit_viewset.py
│   │   ├── notification_viewset.py
│   │   ├── subscription_viewset.py
│   │   ├── support_ticket_viewset.py
│   │   ├── user_viewset.py
│   │   ├── website_viewset.py
│   │   └── webhook_viewset.py
│   │
│   ├── services/              # Business logic
│   │   ├── audit_service.py
│   │   ├── email_service.py
│   │   ├── pdf_service.py
│   │   ├── stripe_service.py
│   │   ├── ai_service.py
│   │   └── webhook_handlers.py
│   │
│   ├── management/            # Django commands
│   │   └── commands/
│   │       ├── create_subscription_plans.py
│   │       └── test_email.py
│   │
│   ├── migrations/            # Database migrations
│   ├── tests/                # Test suite
│   │   ├── conftest.py
│   │   ├── factories.py
│   │   ├── fixtures.py
│   │   └── test_api.py
│   │
│   ├── models.py             # Database models
│   ├── serializers.py        # DRF serializers
│   ├── urls.py              # URL routing
│   ├── admin.py             # Admin configuration
│   ├── tasks.py             # Celery tasks
│   ├── celery.py            # Celery configuration
│   └── settings.py          # Django settings
│
├── templates/                 # Email templates
│   ├── emails/
│   │   ├── audit_complete.html
│   │   ├── password_reset.html
│   │   └── welcome.html
│   └── pdf/
│       └── seo_report.html
│
├── staticfiles/              # Static assets
├── media/                    # User uploads
├── pyproject.toml           # UV dependencies
├── requirements.txt         # Frozen dependencies
└── Dockerfile              # Container configuration
```

### Frontend Structure

```
frontend/
├── apps/
│   └── web/                   # Main Next.js app
│       ├── app/              # App Router
│       │   ├── (auth)/       # Auth layout group
│       │   │   ├── login/
│       │   │   ├── register/
│       │   │   └── layout.tsx
│       │   │
│       │   ├── (dashboard)/  # Dashboard layout group
│       │   │   ├── dashboard/
│       │   │   ├── websites/
│       │   │   ├── audits/
│       │   │   ├── subscription/
│       │   │   └── layout.tsx
│       │   │
│       │   ├── (marketing)/  # Marketing layout group
│       │   │   ├── page.tsx
│       │   │   ├── pricing/
│       │   │   └── layout.tsx
│       │   │
│       │   └── api/          # API routes
│       │       └── auth/     # NextAuth
│       │
│       ├── actions/          # Server actions
│       │   ├── auth-actions.ts
│       │   ├── audit-actions.ts
│       │   ├── subscription-actions.ts
│       │   └── website-actions.ts
│       │
│       ├── components/       # React components
│       │   ├── ui/          # Base UI components
│       │   ├── forms/       # Form components
│       │   ├── dashboard/   # Dashboard components
│       │   └── layouts/     # Layout components
│       │
│       ├── lib/             # Utilities
│       │   ├── api.ts       # API client
│       │   ├── auth.ts      # Auth helpers
│       │   ├── utils.ts     # General utilities
│       │   └── constants.ts
│       │
│       ├── contexts/        # React contexts
│       │   └── theme-context.tsx
│       │
│       ├── providers/       # Context providers
│       │   └── providers.tsx
│       │
│       └── public/          # Static assets
│
└── packages/
    ├── types/               # Generated API types
    │   └── api/
    │       └── index.ts
    │
    └── ui/                 # Shared components
        └── components/
```

---

## Database Schema

### Entity Relationship Diagram

```
User ──────────────┬─────────── ScanConfiguration (1:1)
  │                │
  ├─(1:N)──────────┼─────────── Website
  │                │               │
  ├─(1:N)──────────┼───────────────└─(1:N)─── SEOScan
  │                │                              │
  ├─(1:N)──────────┼──────────────────────────────└─(1:1)─── SEOReport
  │                │
  ├─(1:N)──────────┼─────────── Subscription
  │                │               │
  ├─(1:N)──────────┼───────────────├─(N:1)─── SubscriptionPlan
  │                │               │
  ├─(1:N)──────────┼───────────────└─(1:N)─── Payment
  │                │
  ├─(1:N)──────────┼─────────── Notification
  ├─(1:N)──────────┼─────────── AIContentGeneration
  ├─(1:N)──────────┼─────────── SupportTicket
  └─(1:1)──────────└─────────── StripeCustomer
```

### Core Models

#### User & Authentication

**User** (extends Django AbstractUser)
```python
- id: UUID (PK)
- username: String (unique)
- email: EmailField (unique)
- password: Hashed
- first_name: String
- last_name: String
- trial_used: Boolean (default: False)
- free_usage: Integer (default: 5)
- is_active: Boolean
- is_staff: Boolean
- is_superuser: Boolean
- created_at: DateTime
- modified_at: DateTime
```

**ScanConfiguration** (1:1 with User)
```python
- id: AutoField (PK)
- user: ForeignKey(User, unique=True)
- enable_scheduled_audits: Boolean (default: False)
- schedule_frequency: String (choices: daily, weekly, bi_weekly, monthly)
- schedule_time: TimeField (default: 00:00)
- notification_email: EmailField (optional)
- enable_email_notifications: Boolean (default: True)
- created_at: DateTime
- modified_at: DateTime
```

**PasswordResetToken**
```python
- id: AutoField (PK)
- user: ForeignKey(User)
- token: CharField(6) (numeric code)
- created_at: DateTime
- expires_at: DateTime
```

#### Subscriptions & Billing

**SubscriptionPlan**
```python
- id: AutoField (PK)
- name: String (e.g., "Standard", "Premium")
- description: Text
- price_monthly: Decimal
- price_yearly: Decimal
- stripe_price_id_monthly: String
- stripe_price_id_yearly: String
- features: JSONField (list of features)
- max_websites: Integer (null = unlimited)
- max_scans_per_month: Integer (null = unlimited)
- max_ai_credits: Integer (null = unlimited)
- is_active: Boolean
- sort_order: Integer
- created_at: DateTime
- modified_at: DateTime
```

**Subscription**
```python
- id: AutoField (PK)
- user: ForeignKey(User)
- plan: ForeignKey(SubscriptionPlan)
- stripe_subscription_id: String (unique)
- status: String (choices: active, canceled, past_due, trialing)
- billing_cycle: String (choices: monthly, yearly)
- current_period_start: DateTime
- current_period_end: DateTime
- cancel_at_period_end: Boolean
- trial_end: DateTime (nullable)
- created_at: DateTime
- modified_at: DateTime
```

**Payment**
```python
- id: AutoField (PK)
- user: ForeignKey(User)
- subscription: ForeignKey(Subscription, nullable)
- stripe_payment_intent_id: String (unique)
- amount: Decimal
- currency: String (default: "USD")
- status: String (choices: pending, succeeded, failed)
- payment_method: String
- invoice_url: URLField (nullable)
- created_at: DateTime
```

**StripeCustomer**
```python
- id: AutoField (PK)
- user: OneToOneField(User)
- stripe_customer_id: String (unique)
- created_at: DateTime
- modified_at: DateTime
```

**PaymentEvent** (Webhook logs)
```python
- id: AutoField (PK)
- event_id: String (unique, Stripe event ID)
- event_type: String
- payload: JSONField
- processed: Boolean
- created_at: DateTime
```

#### SEO Operations

**Website**
```python
- id: AutoField (PK)
- user: ForeignKey(User)
- url: URLField
- name: String
- description: Text (nullable)
- is_active: Boolean (default: True)
- created_at: DateTime
- modified_at: DateTime

Unique constraint: (user, url)
```

**SEOScan**
```python
- id: AutoField (PK)
- website: ForeignKey(Website)
- user: ForeignKey(User)
- url: URLField
- scan_type: String (choices: manual, scheduled, api, initial)
- status: String (choices: pending, processing, completed, failed)
- progress: Integer (0-100)
- progress_step: String (nullable)
- error_message: Text (nullable)
- started_at: DateTime (nullable)
- completed_at: DateTime (nullable)
- created_at: DateTime
- modified_at: DateTime
```

**SEOReport**
```python
- id: AutoField (PK)
- scan: OneToOneField(SEOScan)
-
# Raw Data
- raw_html: TextField (nullable)
- raw_metadata: JSONField (nullable)
-
# On-Page SEO
- title: String (nullable)
- title_score: Integer (0-100)
- meta_description: Text (nullable)
- meta_description_score: Integer (0-100)
- h1_tags: JSONField (list)
- h1_score: Integer (0-100)
- h2_tags: JSONField (list)
- canonical_url: URLField (nullable)
-
# Social
- og_title: String (nullable)
- og_description: Text (nullable)
- og_image: URLField (nullable)
- twitter_title: String (nullable)
- twitter_description: Text (nullable)
- twitter_image: URLField (nullable)
-
# Performance
- lighthouse_score: Integer (0-100, nullable)
- performance_score: Integer (0-100, nullable)
- accessibility_score: Integer (0-100, nullable)
- best_practices_score: Integer (0-100, nullable)
- seo_score: Integer (0-100, nullable)
-
# Technical
- has_robots_txt: Boolean
- robots_txt_url: URLField (nullable)
- has_sitemap: Boolean
- sitemap_url: URLField (nullable)
- has_ssl: Boolean
-
# Images
- images: JSONField (list of image analysis)
- total_images: Integer
- images_without_alt: Integer
-
# Structured Data
- structured_data: JSONField (nullable)
-
# Overall
- overall_score: Integer (0-100)
- recommendations: JSONField (list of suggestions)
-
# PDF Report
- pdf_url: URLField (nullable, Cloudinary URL)
- pdf_generated_at: DateTime (nullable)
- pdf_download_count: Integer (default: 0)
-
- created_at: DateTime
- modified_at: DateTime
```

#### AI Content

**AIContentGeneration**
```python
- id: AutoField (PK)
- user: ForeignKey(User)
- content_type: String (choices: meta_title, meta_description, h1,
                         content_rewrite, blog_post, alt_text, faq)
- input_text: Text
- output_text: Text
- language: String (choices: en, nl)
- tokens_used: Integer
- cost: Decimal (nullable)
- model: String (e.g., "gpt-3.5-turbo")
- created_at: DateTime
```

#### Communications

**Notification**
```python
- id: AutoField (PK)
- user: ForeignKey(User)
- notification_type: String (choices: scan_complete, scan_failed,
                              subscription_renewed, payment_failed, etc.)
- title: String
- message: Text
- priority: String (choices: low, medium, high)
- is_read: Boolean (default: False)
- is_archived: Boolean (default: False)
- action_url: URLField (nullable)
- metadata: JSONField (nullable)
- expires_at: DateTime (nullable)
- created_at: DateTime
```

**EmailTemplate**
```python
- id: AutoField (PK)
- template_name: String (unique)
- subject_en: String
- subject_nl: String
- body_html_en: TextField
- body_html_nl: TextField
- variables: JSONField (list of available template variables)
- is_active: Boolean
- created_at: DateTime
- modified_at: DateTime
```

**EmailLog**
```python
- id: AutoField (PK)
- user: ForeignKey(User, nullable)
- to_email: EmailField
- from_email: EmailField
- subject: String
- template_name: String (nullable)
- status: String (choices: sent, failed, pending)
- error_message: Text (nullable)
- sent_at: DateTime (nullable)
- created_at: DateTime
```

**SupportTicket**
```python
- id: AutoField (PK)
- user: ForeignKey(User)
- subject: String
- message: Text
- category: String (choices: technical, billing, feature_request, other)
- status: String (choices: open, in_progress, resolved, closed)
- priority: String (choices: low, medium, high)
- admin_notes: Text (nullable)
- resolved_at: DateTime (nullable)
- created_at: DateTime
- modified_at: DateTime
```

#### System

**SystemConfiguration**
```python
- id: AutoField (PK)
- key: String (unique)
- value: JSONField
- description: Text
- is_active: Boolean
- created_at: DateTime
- modified_at: DateTime
```

**UsageAnalytics**
```python
- id: AutoField (PK)
- date: DateField (unique)
- total_scans: Integer (default: 0)
- successful_scans: Integer (default: 0)
- failed_scans: Integer (default: 0)
- new_users: Integer (default: 0)
- active_subscriptions: Integer (default: 0)
- total_revenue: Decimal (default: 0)
- ai_tokens_used: Integer (default: 0)
- created_at: DateTime
```

**CMSContent** (Multi-language CMS)
```python
- id: AutoField (PK)
- page: String (e.g., "home", "pricing", "about")
- section: String
- content_key: String
- content_en: TextField
- content_nl: TextField
- content_type: String (choices: text, html, json)
- is_active: Boolean
- created_at: DateTime
- modified_at: DateTime

Unique constraint: (page, section, content_key)
```

---

## Service Layer

### Audit Service (`audit_service.py`)

**Purpose**: Orchestrate SEO audits

**Key Methods**:
```python
class AuditService:
    @staticmethod
    def create_audit(user, url, website=None, scan_type='manual') -> SEOScan

    @staticmethod
    def perform_audit(scan_id: int) -> SEOReport
        # 1. Crawl website
        # 2. Analyze on-page SEO
        # 3. Check technical SEO
        # 4. Measure performance
        # 5. Generate recommendations
        # 6. Create report
        # 7. Trigger PDF generation
        # 8. Send notification

    @staticmethod
    def cancel_audit(scan_id: int) -> bool

    @staticmethod
    def get_audit_progress(scan_id: int) -> dict
```

### Email Service (`email_service.py`)

**Purpose**: Handle all email communications

**Key Methods**:
```python
class EmailService:
    @staticmethod
    def send_template_email(to_email, template_name, context, language='en')
        # 1. Load template
        # 2. Render with context
        # 3. Send via Resend API
        # 4. Log email

    @staticmethod
    def send_audit_complete(user, audit)

    @staticmethod
    def send_password_reset(user, token)

    @staticmethod
    def send_subscription_renewal(user, subscription)
```

### PDF Service (`pdf_service.py`)

**Purpose**: Generate and manage PDF reports

**Key Methods**:
```python
class PDFService:
    @staticmethod
    def generate_seo_report(report_id: int, language='en') -> str
        # 1. Load report data
        # 2. Render HTML template
        # 3. Convert to PDF
        # 4. Upload to Cloudinary
        # 5. Return URL

    @staticmethod
    def increment_download_count(report_id: int)
```

### Stripe Service (`stripe_service.py`)

**Purpose**: Manage subscriptions and payments

**Key Methods**:
```python
class StripeService:
    @staticmethod
    def create_customer(user) -> StripeCustomer

    @staticmethod
    def create_checkout_session(user, plan_id, billing_cycle) -> str
        # Returns checkout URL

    @staticmethod
    def handle_subscription_created(event_data)

    @staticmethod
    def handle_subscription_updated(event_data)

    @staticmethod
    def handle_payment_succeeded(event_data)

    @staticmethod
    def cancel_subscription(subscription_id: str)
```

### AI Service (`ai_service.py`)

**Purpose**: AI content generation

**Key Methods**:
```python
class AIService:
    @staticmethod
    def generate_content(content_type, input_text, language='en') -> dict
        # Returns: {content, tokens_used, cost}

    @staticmethod
    def generate_meta_title(url, current_title, language='en')

    @staticmethod
    def generate_meta_description(url, current_desc, language='en')

    @staticmethod
    def generate_blog_post(topic, keywords, language='en')

    @staticmethod
    def calculate_cost(tokens: int, model: str) -> Decimal
```

### Webhook Handlers (`webhook_handlers.py`)

**Purpose**: Process external webhooks

**Key Methods**:
```python
class WebhookHandler:
    @staticmethod
    def handle_stripe_webhook(payload, signature) -> dict
        # 1. Verify signature
        # 2. Route to appropriate handler
        # 3. Log event
        # 4. Return status
```

---

## Frontend Architecture

### Type Generation Flow

```
Backend (Django)
  ↓
OpenAPI Schema (/api/schema/)
  ↓
openapi-typescript-codegen
  ↓
TypeScript Types (packages/types/api/)
  ↓
Frontend Components & Actions
```

**Command**: `pnpm openapi:generate`

### Server Actions Pattern

All mutations go through server actions:

```tsx
// actions/audit-actions.ts
'use server'

import { getServerSession } from 'next-auth'
import { getApiClient } from '@/lib/api'
import { revalidatePath } from 'next/cache'

export async function startAudit(formData: FormData) {
  const session = await getServerSession(authOptions)
  if (!session) {
    return { error: 'Unauthorized' }
  }

  const client = await getApiClient(session)

  try {
    const result = await client.audits.auditsStartCreate({
      url: formData.get('url') as string,
      website_id: formData.get('website_id') as string,
      scan_type: 'manual'
    })

    revalidatePath('/dashboard/audits')
    return { success: true, audit: result }
  } catch (error) {
    return { error: error.message }
  }
}
```

### API Client Usage

```tsx
// lib/api.ts
import { ApiClient, type Session } from '@frontend/types/api'

export async function getApiClient(session?: Session) {
  const baseUrl = process.env.NEXT_PUBLIC_API_URL || 'http://localhost:8000'

  return new ApiClient({
    BASE: baseUrl,
    HEADERS: session ? {
      Authorization: `Bearer ${session.accessToken}`
    } : undefined,
    CREDENTIALS: 'include'
  })
}

// Usage in Server Component
const session = await getServerSession()
const client = await getApiClient(session)
const audits = await client.audits.auditsList()

// Usage in Server Action
'use server'
export async function getAudits() {
  const session = await getServerSession()
  const client = await getApiClient(session)
  return await client.audits.auditsList()
}
```

---

## API Design

### RESTful Principles

- Resource-based URLs: `/api/audits/`, `/api/websites/`
- HTTP methods: GET, POST, PATCH, DELETE
- Status codes: 200, 201, 400, 401, 403, 404, 500
- JSON request/response bodies

### Response Format

**Success Response**:
```json
{
  "id": 123,
  "url": "https://example.com",
  "status": "completed",
  "created_at": "2025-01-19T10:00:00Z"
}
```

**Error Response**:
```json
{
  "detail": "Error message",
  "code": "error_code"
}
```

**List Response**:
```json
{
  "count": 100,
  "next": "http://api/audits/?page=2",
  "previous": null,
  "results": [...]
}
```

### Authentication

**JWT Token Flow**:
```
1. POST /api/token/ {username, password}
   → {access: "...", refresh: "..."}

2. Include in requests:
   Authorization: Bearer <access_token>

3. Refresh when expired:
   POST /api/token/refresh/ {refresh: "..."}
   → {access: "..."}
```

---

## Authentication & Authorization

### Backend JWT Implementation

```python
# settings.py
SIMPLE_JWT = {
    'ACCESS_TOKEN_LIFETIME': timedelta(hours=1),
    'REFRESH_TOKEN_LIFETIME': timedelta(days=7),
    'ROTATE_REFRESH_TOKENS': True,
    'BLACKLIST_AFTER_ROTATION': True,
    'ALGORITHM': 'HS256',
    'SIGNING_KEY': SECRET_KEY,
}
```

### Frontend NextAuth Configuration

```typescript
// app/api/auth/[...nextauth]/route.ts
import NextAuth from 'next-auth'
import CredentialsProvider from 'next-auth/providers/credentials'

export const authOptions = {
  providers: [
    CredentialsProvider({
      async authorize(credentials) {
        const res = await fetch(`${process.env.API_URL}/api/token/`, {
          method: 'POST',
          headers: { 'Content-Type': 'application/json' },
          body: JSON.stringify({
            username: credentials.username,
            password: credentials.password,
          }),
        })

        const user = await res.json()
        if (res.ok && user) {
          return user
        }
        return null
      }
    })
  ],
  callbacks: {
    async jwt({ token, user }) {
      if (user) {
        token.accessToken = user.access
        token.refreshToken = user.refresh
      }
      return token
    },
    async session({ session, token }) {
      session.accessToken = token.accessToken
      session.refreshToken = token.refreshToken
      return session
    }
  },
  session: {
    strategy: 'jwt'
  }
}
```

---

## Task Queue System

### Celery Configuration

```python
# backend/api/celery.py
from celery import Celery
from celery.schedules import crontab

app = Celery('api')
app.config_from_object('django.conf:settings', namespace='CELERY')
app.autodiscover_tasks()

# Scheduled tasks
app.conf.beat_schedule = {
    'run-scheduled-audits-daily': {
        'task': 'api.tasks.run_scheduled_audits',
        'schedule': crontab(hour=0, minute=0),
    },
}
```

### Task Definitions

```python
# backend/api/tasks.py
from celery import shared_task

@shared_task(bind=True, max_retries=3)
def perform_seo_audit_task(self, scan_id):
    try:
        AuditService.perform_audit(scan_id)
    except Exception as exc:
        raise self.retry(exc=exc, countdown=60)

@shared_task
def generate_pdf_report_task(report_id, language='en'):
    PDFService.generate_seo_report(report_id, language)

@shared_task
def send_email_task(to_email, template_name, context, language='en'):
    EmailService.send_template_email(to_email, template_name, context, language)

@shared_task
def run_scheduled_audits():
    # Find all users with scheduled audits enabled
    # Create and queue audit tasks
    pass
```

---

## External Integrations

### Integration Overview

| Service | Purpose | Documentation |
|---------|---------|---------------|
| Stripe | Payments & Subscriptions | [stripe.com/docs](https://stripe.com/docs) |
| OpenAI | AI Content Generation | [platform.openai.com/docs](https://platform.openai.com/docs) |
| Cloudinary | File Storage & CDN | [cloudinary.com/documentation](https://cloudinary.com/documentation) |
| Resend | Email Delivery | [resend.com/docs](https://resend.com/docs) |
| Crawl4AI | Web Crawling | Internal service |
| PageSpeed | Performance Metrics | [developers.google.com/speed](https://developers.google.com/speed) |

### Environment Configuration

All API keys stored in environment variables:
- `STRIPE_SECRET_KEY`
- `OPENAI_API_KEY`
- `CLOUDINARY_API_KEY`
- `RESEND_API_KEY`
- `LIGHTHOUSE_API_KEY`

---

## Security Architecture

### Security Layers

1. **Network Security**
   - HTTPS enforcement
   - CORS configuration
   - Rate limiting (recommended)

2. **Authentication Security**
   - JWT with short expiration
   - Secure password hashing (Django default)
   - Password reset with expiring tokens

3. **Authorization Security**
   - DRF permissions on all endpoints
   - Object-level permissions
   - User ownership validation

4. **Data Security**
   - SQL injection protection (ORM)
   - XSS protection (React escaping)
   - CSRF protection

5. **API Security**
   - Input validation (serializers)
   - Output sanitization
   - Error message sanitization

---

## Performance Considerations

### Backend Optimization

- **Database**: Connection pooling, query optimization
- **Caching**: Redis ready for implementation
- **Static Files**: WhiteNoise for efficient serving
- **Media Files**: Cloudinary CDN
- **Async Processing**: Celery for long operations

### Frontend Optimization

- **SSR/SSG**: Next.js optimizations
- **Code Splitting**: Automatic with Next.js
- **Image Optimization**: next/image
- **Font Optimization**: next/font
- **Lazy Loading**: React.lazy for heavy components

### Monitoring Points

- Database query count & time
- API response times
- Celery task queue length
- Memory usage
- Error rates

---

**Next**: See [API.md](./API.md) for complete API reference
