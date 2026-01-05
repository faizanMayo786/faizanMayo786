# Technical & Architecture Disclosure

## Purpose

This document aims to provide a clear and shared understanding of the system's architecture, technology choices, and operational setup.

---

## 1. Supabase Usage & Data Access

### 1.1 Database Communication

**How does the application communicate with Supabase?**

- **A combination of both**:
  - Direct client-side access via Flutter SDK (`supabase_flutter`) for most database operations
  - Edge Functions for sensitive operations (OTP sending/verification, demo user creation)

**Which Supabase services are currently used and how?**

- ✅ **PostgreSQL database**: Direct queries via Flutter SDK (`Supabase.instance.client.from('table')`)
- ✅ **Auth**: Supabase Auth for user sessions (`Supabase.instance.client.auth`)
- ✅ **Realtime**: WebSocket subscriptions for chat messages and notifications (`StreamBuilder` with Realtime)
- ✅ **Storage**: File storage for profile images (`profile-pictures` bucket) and driver documents (`driver-docs` bucket)
- ✅ **Edge Functions**:
  - `send-otp-whatsapp` - OTP delivery via WhatsApp
  - `verify-otp` - OTP verification and user creation
  - `create-demo-user` - Demo account creation
  - `send-ride-reminders` - Scheduled notifications (via Cron)

### 1.2 Security & Access Control

**Is Row Level Security (RLS) enabled on all relevant tables?**

- ❌ No, RLS is not enabled. Due to fast migration phase, RLS was avoided. Access control is handled at application level.

**Are access policies reviewed and tested for each role?**

- N/A - RLS is not enabled. Access control is managed through application logic and Edge Functions.

**Where is business logic and validation primarily handled?**

- **Combination approach**:
  - **Edge Functions**: Authentication (OTP), user creation, sensitive operations
  - **Backend services**: Access control and data validation via Edge Functions
  - **Frontend**: Input validation, UI state management, client-side checks

**Are Supabase service-role keys used anywhere in the system?**

- ✅ Yes, service-role keys are used:
  - **Where**: Edge Functions only (for OTP sending/verification, demo user creation)
  - **Storage**: Stored in Edge Function environment variables (server-side only)
  - **Security**: Never exposed in client-side code. Only used in Deno Edge Functions which run server-side

### 1.3 Data Ownership & Portability

**Who owns the Supabase project and its billing account?**

- Moaz Arishe - Client

**Can the database be accessed using standard PostgreSQL credentials?**

- ✅ Yes, Supabase provides direct PostgreSQL connection. Connection string available in Supabase dashboard under Settings > Database.

**Are backups available outside of Supabase if needed?**

- Supabase provides automatic daily backups. Manual exports can be performed via Supabase dashboard or pg_dump using PostgreSQL connection string.

**Are there any notable vendor dependencies beyond Supabase-managed services?**

- No major vendor lock-in. Database is standard PostgreSQL and can be migrated. Edge Functions use Deno (standard runtime).

---

## 2. Backend Architecture

### 2.1 Backend Layer

**Is there a dedicated backend layer?**

- **No** - No traditional backend server. Architecture uses:
  - Supabase Edge Functions (Deno/TypeScript) for serverless backend logic
  - Direct client-to-database access via Flutter SDK

**Where is the core business logic implemented?**

- **Supabase Edge Functions**: Authentication, OTP handling, user creation
- **Flutter application**: Ride management, booking logic, chat, notifications
- **Database**: Data storage and relationships

**Any known trade-offs or limitations of this setup?**

- **Trade-offs**:
  - No RLS means access control must be handled in application code
  - Edge Functions have execution time limits
  - Direct database access requires careful input validation
- **Benefits**:
  - Faster development and deployment
  - Lower infrastructure costs
  - Scalable serverless architecture

---

## 3. Mobile Application (Flutter)

### 3.1 Environments

**Are separate environments in place?**

No

**How is environment configuration handled within the app?**

- Environment variables loaded from `.env` file using `flutter_dotenv` package
- Variables: `SUPABASE_URL`, `SUPABASE_ANON_KEY`, `PUSHY_API_KEY`, `GOOGLE_MAPS_API_KEY`
- `.env` file is gitignored, `.env.example` provided as template

### 3.2 Secrets & Configuration

**How are API keys and sensitive configuration values managed?**

- Stored in `.env` file (gitignored)
- Loaded at runtime via `flutter_dotenv`
- Some keys (Google Maps API key) are embedded in `AndroidManifest.xml` and `Info.plist` for platform-specific requirements

**Are any secrets embedded in the mobile application?**

- ⚠️ Yes, some API keys are embedded:
  - Google Maps API key in `AndroidManifest.xml` and iOS configuration
  - Supabase Anon Key (public, safe to expose)
  - Pushy API key in `.env` (not in compiled app if using `.env`)

**How are keys updated or rotated when needed?**

- Update `.env` file and rebuild app
- For production: Update keys in Supabase dashboard, then update `.env` and redeploy
- Google Maps key: Update in platform-specific config files and rebuild

---

## 4. Admin / Dashboard Application

### 4.1 Frontend

**Technology used:**

- **Next.js 14.2.16** (React framework with SSR/SSG)
- **React 18.3.1** (UI library)
- **TypeScript 5.x** (Type-safe JavaScript)
- **Tailwind CSS 3.4.17** (Styling)
- **Radix UI** (Component library)
- **Recharts** (Charts)

**Hosting provider:**

- Vercel

**Authentication and authorization approach:**

- Supabase Auth with email/password
- Role-based access via `admin_users` table (`super_admin`, `admin`, `moderator`)
- Auth guard middleware for protected routes

### 4.2 Backend & Permissions

**Does it connect directly to Supabase or through a custom API?**

- ✅ Direct connection to Supabase via Supabase Client SDK

**How do admin permissions differ from standard user permissions?**

- Admins have elevated access via `admin_users` table check
- Can view all users, rides, bookings, chats
- Can block/unblock users, approve/reject driver verifications
- Can access analytics and system configuration

**Is there logging or traceability for admin actions?**

- Admin actions are logged in database (blocking logs, verification reviews)
- Specific logging implementation details in `btareqi-admin/README.md`

---

## 5. Third-Party Services

| Service Name        | Purpose                                           | Environment | Pricing Model |
| ------------------- | ------------------------------------------------- | ----------- | ------------- |
| **Supabase**        | Database, Auth, Realtime, Storage, Edge Functions | Production  | Usage-based   |
| **Google Maps API** | Geocoding, Places                                 | Production  | Pay-as-you-go |
| **Pushy SDK**       | Push Notifications (iOS/Android)                  | Production  | Subscription  |
| **WhatsApp API**    | OTP Delivery                                      | Production  | Per-message   |

**Note**: Exact pricing and billing account owners to be confirmed with project owner.

---

## 6. Infrastructure & Deployment

**Hosting providers used:**

- **Supabase**: Database, Auth, Storage, Edge Functions
- **Mobile Apps**: App Store (iOS), Play Store (Android)
- **Admin Panel**: Vercel

**Monitoring tools in place:**

- Supabase Dashboard (built-in monitoring)
- Edge Function logs available in Supabase dashboard

**Logging strategy:**

- Application logs: Console/debug logs in Flutter app
- Edge Function logs: Available in Supabase dashboard
- Database logs: Supabase built-in logging

---

## 7. App Store & Play Store Readiness

### 7.1 Publishing Accounts

**Apple App Store account owner:**

- Moaz Arishe

**Google Play Console account owner:**

- Moaz Arishe

**Access level currently granted to the client:**

- Owner

### 7.2 Release Process

**Versioning strategy:**

- Semantic versioning: `MAJOR.MINOR.PATCH+BUILD_NUMBER`
- Current version: `1.0.1+6`
- Version stored in `pubspec.yaml`

---

## 8. Source Code & Repository Access

**Repository information:**

- **Mobile App Repository**:
  - Platform: GitHub
  - Access level: Moaz Arishe & Faizan Elahi (Reckap Solutions - Co Founder)

- **Admin Panel Repository**:
  - Same repository as mobile app
  - Access level: Same as main repository

---

## 9. Documentation

**Available documentation:**

- ✅ **Architecture diagrams**: Available in `README.md` (Mermaid diagrams)
- ✅ **Database schema**: Available in `README.md` (Section: Database Schema)
- ✅ **API documentation**: Edge Functions documented in `README.md` (Section: Supabase Edge Functions)
- ✅ **Environment variables**: Listed in `README.md` and `.env.example` files
- ✅ **Admin access steps**: Documented in `btareqi-admin/README.md`

**Detailed documentation locations:**

- **Main README**: `/README.md` (2574 lines) - Complete Flutter app documentation
- **Admin Panel README**: `/btareqi-admin/README.md` (1592 lines) - Complete admin panel documentation

**Operational notes or runbooks:**

- Troubleshooting guides in both README files
- Build & deployment instructions documented
- Setup & installation guides included

---

**Note**: For detailed technical documentation, architecture diagrams, database schemas, API specifications, and operational procedures, please refer to the comprehensive README.md files in the codebase:

- Main application: `README.md` (2574 lines)
- Admin panel: `btareqi-admin/README.md` (1592 lines)
