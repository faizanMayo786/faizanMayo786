# Technical & Architecture Disclosure

## Section 1: Supabase Usage & Data Access

### 1.1 Database Communication

**Question: How does the application communicate with Supabase?**

**Answer: Direct client-side access via Flutter SDK**

The application uses **direct client-side access** through the Supabase Flutter SDK (`supabase_flutter: ^2.10.3`). The Flutter app communicates directly with Supabase services without an intermediate custom backend API.

**Implementation Details:**

1. **Initialization** (in `lib/main.dart`):

   ```dart
   await Supabase.initialize(
     url: rawUrl,      // From .env: SUPABASE_URL
     anonKey: rawAnonKey  // From .env: SUPABASE_ANON_KEY
   );
   ```

2. **Service Layer Pattern**:

   - All services access Supabase via singleton: `Supabase.instance.client`
   - Services include: `AuthService`, `RideService`, `ChatService`, `UserService`, `NotificationService`, `ConfigService`
   - Example: `final _supa = Supabase.instance.client;`

3. **Direct Database Queries**:

   - Services perform direct SQL queries using Supabase client methods:
     - `.from('table_name').select()`
     - `.from('table_name').insert()`
     - `.from('table_name').update()`
     - `.from('table_name').delete()`

4. **Edge Functions for Sensitive Operations**:
   - OTP sending/verification: `/send-otp-whatsapp`, `/verify-otp`
   - Demo user creation: `/create-demo-user`
   - Admin user creation: `/create-admin-user`
   - Ride reminders: `/send-ride-reminders` (scheduled via Supabase Cron)
   - Edge Functions use Service Role Key (never exposed to client)

**Architecture Pattern:**

```
Flutter App → Supabase Client SDK → Supabase Services
                ↓
        ┌───────┴────────┐
        │                │
   Direct DB Access   Edge Functions
   (Anon Key)        (Service Role Key)
```

---

### 1.2 Which Supabase Services Are Currently Used and How?

**Answer: Multiple Supabase services are actively used:**

#### ✅ **PostgreSQL Database**

- **Usage**: Primary data storage for all application data
- **Tables Used**:
  - `profiles` - User profiles (riders/drivers)
  - `rides` - Ride offers created by drivers
  - `bookings` - Seat bookings by passengers
  - `chats` - Chat conversations
  - `messages` - Chat messages (Base64 encoded)
  - `driver_documents` - Driver verification documents
  - `notifications` - In-app notifications
  - `otps` - Temporary OTP codes (hashed)
  - `device_tokens` - Push notification tokens
  - `app_config` - Remote configuration
  - `ratings` - Driver ratings
  - `admin_users` - Admin panel users
- **Access Pattern**: Direct queries via Supabase client with Anon Key

#### ✅ **Supabase Auth**

- **Usage**: User authentication and session management
- **Features Used**:
  - Phone-based authentication (via Edge Functions)
  - Email/password for admin panel
  - Session token management
  - User metadata storage
- **Implementation**:
  ```dart
  final _supa = Supabase.instance.client;
  User? get currentUser => _supa.auth.currentUser;
  Stream<AuthState> get authStateChanges => _supa.auth.onAuthStateChange;
  ```
- **Auth Flow**: OTP sent via WhatsApp → Verified via Edge Function → Auth user created → Client signs in with credentials

#### ✅ **Supabase Realtime**

- **Usage**: Real-time data subscriptions for live updates
- **Features Used**:
  - Real-time notifications stream
  - Chat message updates
  - Configuration updates (via RealtimeChannel)
- **Implementation Examples**:

  ```dart
  // Notifications stream
  _db.from('notifications')
     .stream(primaryKey: ['id'])
     .map((rows) => ...)

  // Config updates via RealtimeChannel
  RealtimeChannel? _channel;
  _channel = _supa.channel('app_config_changes')
  ```

#### ✅ **Supabase Storage**

- **Usage**: File storage for images and documents
- **Buckets Used**:
  - `profile-pictures` - User profile avatars
  - `driver-docs` - Driver verification documents (ID, license, vehicle registration)
  - `chat-images` - Chat message images
- **Implementation**:
  ```dart
  _db.storage
    .from('profile-pictures')
    .upload(objectPath, file, fileOptions: FileOptions(...))
  ```
- **Image Compression**: Images compressed to max 1MB before upload

#### ✅ **Supabase Edge Functions**

- **Usage**: Serverless backend logic for sensitive operations
- **Functions Deployed**:

  1. **`send-otp-whatsapp`**: Sends OTP via WhatsApp API

     - Hashes OTP with SHA-256
     - Stores hash in `otps` table
     - Rate limiting (60-second cooldown)

  2. **`verify-otp`**: Verifies OTP and creates/authenticates user

     - Verifies OTP hash
     - Creates Supabase Auth user if not exists
     - Creates/updates profile
     - Returns credentials for client sign-in

  3. **`create-demo-user`**: Creates demo accounts for testing

     - Creates Auth user and profile
     - Pre-configured credentials

  4. **`create-admin-user`**: Creates admin panel users

  5. **`send-ride-reminders`**: Scheduled notifications (via Supabase Cron)
     - Queries upcoming rides
     - Sends push notifications

- **Authentication**: All Edge Functions use Service Role Key (bypasses RLS)
- **Runtime**: Deno (TypeScript)

---

### 1.3 Security & Access Control

#### **Question: Is Row Level Security (RLS) enabled on all relevant tables?**

**Answer: RLS is mentioned in documentation but actual policies are not visible in codebase**

**Current Status:**

- README.md mentions RLS should be configured
- Example RLS policy provided in README for admin access:
  ```sql
  CREATE POLICY "Admins can read all profiles"
  ON profiles FOR SELECT
  TO authenticated
  USING (
    EXISTS (
      SELECT 1 FROM admin_users
      WHERE admin_users.id = auth.uid()
      AND admin_users.is_active = true
    )
  );
  ```
- **However**, no actual RLS policy files found in the codebase
- The codebase contains `firestore.rules` (Firebase Firestore rules) but this is a Supabase project, so these rules are likely legacy/unused

**Recommendation:**

- RLS policies should be reviewed and verified in Supabase Dashboard
- All tables should have appropriate RLS policies enabled
- Policies should enforce:
  - Users can only access their own data
  - Drivers can manage their own rides
  - Passengers can view available rides
  - Chat participants can only access their conversations
  - Admin users have elevated permissions

---

#### **Question: Are access policies reviewed and tested for each role?**

**Answer: Cannot confirm from codebase - requires Supabase Dashboard review**

**Current State:**

- No test files found for RLS policies
- No documentation of role-based access testing
- README mentions RLS but doesn't provide comprehensive policy documentation

**Roles in System:**

1. **Rider** (`role: 'rider'` in profiles table)
2. **Driver** (`role: 'driver'` in profiles table)
3. **Admin** (in `admin_users` table with roles: `super_admin`, `admin`, `moderator`)

**Recommended Testing:**

- Test RLS policies for each role
- Verify users cannot access other users' data
- Verify drivers can only manage their own rides
- Verify admins have appropriate access
- Test edge cases (blocked users, inactive accounts)

---

#### **Question: Where is business logic and validation primarily handled?**

**Answer: Combination of Backend Services (Edge Functions) and Client-side Services**

#### **1. Backend Services (Edge Functions) - Primary for Sensitive Operations**

**Location**: Supabase Edge Functions (Deno/TypeScript)

**Handles:**

- ✅ **OTP Generation & Verification**
  - OTP hashing (SHA-256 with phone-specific salt)
  - OTP expiration (5 minutes)
  - Rate limiting (60-second cooldown)
  - Failed attempt tracking
- ✅ **User Authentication**
  - Auth user creation/update
  - Profile creation/update
  - Session management
- ✅ **WhatsApp Integration**
  - OTP delivery via WhatsApp API
  - Multi-language message support
- ✅ **Scheduled Tasks**
  - Ride reminder notifications (via Supabase Cron)

**Why Backend:**

- Security: Service Role Key never exposed to client
- Sensitive operations: OTP hashing, user creation
- External API calls: WhatsApp integration

---

#### **2. Client-side Services (Flutter) - Primary for Business Logic**

**Location**: `lib/services/` directory

**Services:**

- `AuthService` - Authentication flow, OTP requests
- `RideService` - Ride search, booking, creation, status management
- `ChatService` - Messaging, image upload, message encryption (Base64)
- `UserService` - Profile management, document upload
- `NotificationService` - Push notifications, notification management
- `ConfigService` - Remote configuration fetching
- `RatingService` - Driver rating submission
- `ReportService` - User reporting functionality

**Handles:**

- ✅ **Data Validation**
  - Input validation (phone numbers, dates, prices)
  - Business rules (seat capacity, ride status transitions)
  - User permission checks
- ✅ **Business Logic**
  - Ride search with filters
  - Booking workflow (request → approval → confirmation)
  - Chat message encryption/decryption
  - Image compression before upload
  - Rating calculations
  - Notification translation application
- ✅ **State Management**
  - User session management
  - Cache management (user details cache)
  - Real-time subscriptions

**Example Validation in Client:**

```dart
// RideService - Seat booking validation
if (seats <= 0 || seats > availableSeats) {
  throw Exception('Invalid seat count');
}

// RatingService - Rating validation
if (stars < 1 || stars > 5) {
  throw ArgumentError('Stars must be between 1 and 5');
}
```

---

#### **3. Database (Constraints, Triggers) - Limited**

**Current State:**

- No SQL migration files found in codebase
- No database trigger definitions visible
- README mentions database schema but not constraints/triggers

**Potential Database-Level Logic:**

- Foreign key constraints (enforced by Supabase)
- Unique constraints (e.g., phone numbers in profiles)
- Timestamp defaults (`created_at`, `updated_at`)
- **Note**: Business logic validation should ideally be in database triggers, but this is not visible in codebase

---

### Summary: Business Logic Distribution

| Layer               | Primary Responsibilities                    | Examples                                      |
| ------------------- | ------------------------------------------- | --------------------------------------------- |
| **Edge Functions**  | Sensitive operations, external APIs         | OTP hashing, WhatsApp delivery, user creation |
| **Client Services** | Business logic, validation, data operations | Ride booking, chat messaging, profile updates |
| **Database**        | Data integrity, relationships               | Foreign keys, unique constraints, timestamps  |
| **Frontend (UI)**   | User input validation, UX                   | Form validation, error messages               |

---

## Recommendations

### 1. RLS Policy Review

- ✅ Audit all tables in Supabase Dashboard
- ✅ Verify RLS is enabled on all tables
- ✅ Review and document all RLS policies
- ✅ Test policies for each role (rider, driver, admin)
- ✅ Document policy testing procedures

### 2. Business Logic Audit

- ✅ Review client-side validation for security
- ✅ Consider moving critical validation to database triggers
- ✅ Document all business rules
- ✅ Add validation tests

### 3. Security Hardening

- ✅ Ensure Service Role Key is never exposed
- ✅ Review all direct database queries for SQL injection risks
- ✅ Verify input sanitization
- ✅ Review message encryption (currently Base64 - consider AES)

### 4. Documentation

- ✅ Document all RLS policies
- ✅ Document database constraints and triggers
- ✅ Create architecture diagrams showing data flow
- ✅ Document role-based access patterns

---

**Document Generated**: Based on codebase analysis  
**Last Updated**: Current date  
**Codebase Version**: 1.0.1+6
