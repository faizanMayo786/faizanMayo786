# StudyApply: Production Readiness Audit

**Prepared for:** Oratile Maroga (CEO, StudyApply)  
**Prepared by:** Faizan Elahi (CTO-level review)  
**Date:** February 11, 2026  
**Reference:** Meeting minutes (Feb 11, 2026), instructions.txt, Security-First Specs

---

## 1. Executive Summary

The app is **feature-complete** for MVP (Study + Apply modes, staff portal, messaging). To reach **production level** with the agreed scope (bug fixes, push notifications, application-system hardening), the codebase needs:

- **Bug fixes:** 2 items (calendar crash, splash icon, application form load).
- **New feature:** Push notifications (event reminders, new message alerts, scholarship alerts).
- **Security & integrity:** Backend-enforced application rules (“Texas bulldog”), audit logs, Firestore/Storage tightening, App Check.

**Estimated total: 38–52 hours** (development only; excludes app store submission).  
Breakdown: Bugs 4–6h | Notifications 10–14h | Application backend & rules 16–22h | Security baseline & testing 8–10h.

---

## 2. Scope vs Current State (from Meeting & Instructions)

| Scope item | Current state | Gap |
|------------|---------------|-----|
| **Calendar crash** (new event) | Calendar screen creates event via direct `Firestore.add(ev.toMap())`; possible null/async/mounted issues | Fix crash: use `CalendarRepository.createEvent`, add guards, ensure `date`/timestamps and checklist serialization are safe |
| **Splash icon** before loading | Rive splash only; no StudyApply logo/icon before Rive loads | Add or update splash asset so logo appears before/during load |
| **Application form blank on edit** | `_loadExistingFromFirestore()` loads identity/academics/guardian; possible race or unmounted apply of `_pending*` | Ensure load runs and applies to controllers; defer UI until data applied or show loading state |
| **Push notifications** | None | FCM + backend triggers: event reminders, new message alerts, scholarship alerts (preference-based) |
| **Application rules (1 per program/intake, max 3 per uni/intake)** | Partially client-side only; direct Firestore writes allowed | **All create/submit/withdraw/status via Cloud Functions**; Firestore deny client writes for applications; locks & counters in backend |
| **Backend as source of truth** | Applications: create/save/submit are **client-side** (Firestore `users/{uid}/applications` writable by user) | Move create draft, submit, withdraw, status updates to Cloud Functions; server-side validation only |
| **Audit logs** | None | Log create, submit, withdraw, status change, doc attach, messaging (who, what, when, before/after, uniId) |
| **App Check / rate limits** | Not implemented | Enable App Check; rate limit create/submit/messaging/uploads |
| **Email/phone verification** | Email optional; no phone OTP | Require email verification before submissions; recommend phone OTP before first submit |

---

## 3. Architecture & Design Pattern Assessment (CTO View)

### 3.1 What’s in place

- **Flutter:** Single app with clear split: **Study** (StudyScreens) vs **Apply** (ApplyScreens); shared `lib/` (models, providers, repositories, services, utilities).
- **State:** Riverpod (StreamProvider, StateProvider, Provider). Appropriate for Firebase streams and UI state.
- **Data layer:** Repositories (e.g. `ApplicationsRepository`, `CalendarRepository`, `MessageRepo`) abstract Firestore; providers expose streams/single-shot data. No use-case/interactor layer (acceptable for current size).
- **Models:** Strong domain models (`Application`, `CalendarEvent`, `AppUser`, etc.) with `fromDoc` / `toMap`; enums for status.
- **Staff portal:** Separate Flutter app (`staff_portal/`) with go_router; shares Firebase project; Cloud Functions used for messaging only.
- **Backend:** Firebase (Auth, Firestore, Storage, Cloud Functions). Functions currently only for messaging (`sendStaffMessage`, `sendStudentMessage`, `sendStaffMessageBulk`).

### 3.2 Gaps and risks

| Area | Assessment | Risk |
|------|------------|------|
| **Application writes** | Create/save/submit are in client (repository + `application_detail_screen`). Firestore rules allow `users/{uid}/**` full read/write. | **High:** Client can bypass repo logic; no server-side duplicate or cap enforcement. |
| **Single source of application creation** | Two paths: (1) `ApplicationsRepository.createApplication` (deterministic id `uniId__programId`, counter on **create**); (2) `application_detail_screen` uses `appsRef.add()` when no `_applicationDocId` (random id, no duplicate/cap check). | **High:** Inconsistent model; counter incremented on draft create vs spec “only submitted count”; intake not in doc id. |
| **Firestore rules** | `users/{uid}/{docPath=**}` allow full read, write. No per-collection deny for `applications`. `institutionApplications` is staff read + verification-only update. | **High:** Students can write any field (e.g. status) and create unlimited applications. |
| **Idempotency** | No idempotency keys on submit; double-tap or retry can cause duplicate submits if not constrained by backend. | **Medium.** |
| **Auditability** | No audit log collection or logging of sensitive actions. | **Medium.** |
| **Calendar** | Screen bypasses `CalendarRepository.createEvent` and uses inline `add(ev.toMap())`; crash likely from async/mounted or data shape. | **Low:** Fix by using repository + guards. |
| **Splash** | Rive-only; no static logo/icon before Rive. | **Low.** |

### 3.3 Recommendations (architecture)

1. **Application lifecycle:** All application create (draft), submit, withdraw, and status changes must go through **Cloud Functions only**. Firestore rules: deny client writes to `users/{uid}/applications` (and to any institution-side application mirror). Keep client read for own applications.
2. **Single creation path:** Remove `appsRef.add()` from `application_detail_screen`. New applications must be created via Cloud Function (after program/university/intake selection); function returns application id; detail screen only loads/updates that doc through backend or via function that updates draft.
3. **Data model alignment with spec:**  
   - **UniProgramLock:** server-side lock doc (e.g. `locks/{studentUid}_{uniId}_{programId}_{intakeYear}_{intakeTerm}`) created in same transaction as draft.  
   - **UniApplicationCounter:** per student, per university, per intake; increment only on **submit** (not on create).  
   - Application doc id can remain random or be derived; uniqueness enforced by lock + transaction.
4. **Repositories:** Keep repositories for **reads** and for calling Cloud Functions (e.g. `createDraftApplication`, `submitApplication`). Writes to applications collection only via Functions.
5. **Staff portal:** Continue using custom claims + `staffHasUni(uniId)`; ensure Functions validate staff’s `uniId` for every application/message access.

---

## 4. Work Breakdown (Mapped to Specs)

### 4.1 Bug fixes (4–6 h)

| # | Task | Spec / area | Files / components | Est. h |
|---|------|-------------|--------------------|--------|
| B1 | Fix calendar crash when creating new event | Meeting | `calendar_screen.dart`, `calendar_repository.dart`, `calendar_event.dart` | 2–3 |
| B2 | Splash: show StudyApply logo/icon before or during load | Meeting | `rive_splash_gate.dart`, `flutter_native_splash` / assets, Android/iOS splash config | 1–2 |
| B3 | Application form: load existing application data into form (no blank form on edit) | Instructions | `application_detail_screen.dart` (`_loadExistingFromFirestore`, initState, `_pending*` apply) | 1 |

### 4.2 Push notifications (10–14 h)

| # | Task | Spec / area | Files / components | Est. h |
|---|------|-------------|--------------------|--------|
| N1 | FCM integration in Flutter app (token, foreground/background handling) | Meeting | New service, `main.dart`, Android/iOS config | 2–3 |
| N2 | Cloud Functions: triggers for event reminders (e.g. calendar event N minutes before) | Meeting | `functions/` (new), Firestore or scheduled | 2–3 |
| N3 | New message alerts (on new message in thread) | Meeting | `functions/` (Firestore trigger or call path), FCM send | 2 |
| N4 | Scholarship/financial-aid alerts based on user preferences | Meeting | `functions/`, user prefs in Firestore, FCM | 2–3 |
| N5 | Backend: store FCM tokens per user; cleanup on logout | – | Firestore `users/{uid}/fcmTokens` or similar, Functions | 1–2 |

### 4.3 Application system: backend enforcement (16–22 h)

| # | Task | Spec / area | Files / components | Est. h |
|---|------|-------------|--------------------|--------|
| A1 | Firestore data model: UniProgramLock, UniApplicationCounter (per student/uni/intake); application status enum aligned with spec | Instructions §2 | Firestore design doc, `application` model (if needed) | 1 |
| A2 | Cloud Functions: CreateDraftApplication (transaction: check lock, create lock + draft, no counter increment) | Instructions §3 | `functions/index.js` | 3–4 |
| A3 | Cloud Functions: SubmitApplication (transaction: validate draft, check counter < 3, increment counter, set status Submitted, lock fields) | Instructions §3 | `functions/index.js` | 3–4 |
| A4 | Cloud Functions: WithdrawApplication, UpdateApplicationStatus (staff-only status) | Instructions §3 | `functions/index.js` | 2 |
| A5 | Idempotency keys for submit (and optionally create); reject duplicate submit by idempotency key | Instructions §3 | `functions/index.js`, client | 1–2 |
| A6 | Firestore rules: deny client writes to `users/{uid}/applications`; allow read own; (optional) allow limited draft fields via rules if not all through Functions | Instructions §5 | `firestore.rules` | 1–2 |
| A7 | Client: replace direct application create/save/submit with calls to Cloud Functions; detail screen uses returned application id and only updates via Functions or restricted rules | Instructions §3 | `application_detail_screen.dart`, `applications_repository.dart`, providers | 3–4 |
| A8 | Mirror or sync application to `institutionApplications/{uniId}/applications` for staff (create/update on submit/status change) | Instructions | `functions/index.js`, staff_portal | 1–2 |

### 4.4 Security baseline (6–8 h)

| # | Task | Spec / area | Files / components | Est. h |
|---|------|-------------|--------------------|--------|
| S1 | Email verification required before submit (or before first application action) | Instructions §4, Baseline §1 | Auth flow, Cloud Function checks | 1 |
| S2 | Firebase App Check: enable and enforce on Firestore, Functions, Storage | Instructions §5, Baseline §5 | Firebase console, `firestore.rules`, Functions, client | 2 |
| S3 | Rate limiting: create draft, submit, messaging (per user/period) | Instructions §4, Baseline §4 | `functions/index.js` | 1–2 |
| S4 | Audit log: write audit events (create, submit, withdraw, status change, doc attach, message send) with who, what, when, before/after, uniId | Instructions §5, Baseline §6 | `functions/index.js`, Firestore collection `auditLog` or similar | 2 |

### 4.5 Verification & testing (2–4 h)

| # | Task | Spec / area | Est. h |
|---|------|-------------|--------|
| T1 | Manual + automated tests: duplicate apply same uni+program+intake fails; 4th submit same uni+intake fails; double-tap submit idempotent; two devices one succeeds; direct Firestore write blocked; staff Uni A cannot see Uni B | Instructions §7 | 2–4 |

---

## 5. Milestones & Acceptance Criteria

- **Milestone 1 – Bugs & splash (Complete when B1–B3 done, smoke test)**  
  - Calendar: create new event does not crash.  
  - Splash: logo/icon visible before or during load.  
  - Application: opening existing application shows saved data in form.

- **Milestone 2 – Notifications (Complete when N1–N5 done)**  
  - Event reminders fire before event time.  
  - New message in thread triggers push.  
  - Scholarship alerts (e.g. new financial aid matching prefs) trigger push.

- **Milestone 3 – Application backend (Complete when A1–A8 done + tests pass)**  
  - Duplicate apply same uni+program+intake fails (server).  
  - 4th submission to same university same intake fails (server).  
  - Double-tap submit does not duplicate (idempotent).  
  - Two devices submitting same draft: only one succeeds.  
  - Direct Firestore write to applications fails (rules).  
  - Staff Uni A cannot view/edit Uni B applicants.

- **Milestone 4 – Security baseline (Complete when S1–S4 done)**  
  - Email verification required before submit (or first application action).  
  - App Check enabled and enforced.  
  - Rate limits applied; audit log has required events.

---

## 6. Test Plan

### 6.1 Automated (where possible)

- **Application rules:**  
  - Call CreateDraftApplication twice same (student, uni, program, intake) → second fails.  
  - Submit 4 times to same uni same intake → 4th fails.  
  - Submit with same idempotency key twice → second returns success but no duplicate.
- **Firestore rules:**  
  - Client SDK: write to `users/{uid}/applications` → denied (after rule change).  
  - Staff token with uniId A: read `institutionApplications/uniB/...` → denied.

### 6.2 Manual scenarios

- Calendar: add event (task/deadline/exam/reminder) with and without checklist → no crash; event appears.  
- Splash: cold start → logo/icon visible, then Rive, then app.  
- Application: create draft (via function), edit form, save draft, load again → form populated.  
- Application: submit once; try duplicate submit (double-tap / second device) → single submitted application.  
- Application: submit 3 to same uni same intake → success; 4th → rejected.  
- Notifications: create event with reminder → receive push; send message to student → student gets push.  
- Staff: login as Uni A staff → see only Uni A applicants; cannot open Uni B application.

---

## 7. Risk List (What May Break When Tightening)

| Risk | Mitigation |
|------|------------|
| **Application list/detail flow** | Detail screen currently assumes it can read/write Firestore directly; switching to Functions may require loading states and error handling for “create draft” and “submit” calls. |
| **Existing applications** | Existing docs may not have lock/counter docs; migration or one-time backfill for counter/lock for existing data; new rules only apply to new writes. |
| **Staff portal** | Depends on `institutionApplications` and possibly custom claims; ensure mirroring from Functions keeps staff portal in sync and claims are set (e.g. `setStaffClaims`). |
| **Calendar** | Switching to `CalendarRepository.createEvent` must match current Firestore schema (e.g. `date`, `startTime`, `endTime`, `checklist`). |
| **Firestore rules** | Denying `users/{uid}/applications` write will break current save/submit until client uses only Functions. Deploy rules only after Functions and client are updated. |

---

## 8. Hour Estimate Summary

| Category | Low (h) | High (h) |
|----------|---------|----------|
| Bug fixes (B1–B3) | 4 | 6 |
| Push notifications (N1–N5) | 10 | 14 |
| Application backend (A1–A8) | 16 | 22 |
| Security baseline (S1–S4) | 6 | 8 |
| Verification & testing (T1) | 2 | 4 |
| **Total (development only)** | **38** | **52** |

- **Recommended planning:** 40–48 hours (1–2 weeks full-time equivalent).  
- **Excluded:** App store submission, ongoing maintenance, phone OTP integration (recommended later), full POPIA/compliance deep dive.

---

## 9. Dependency & Ordering

1. **First:** Bug fixes (B1–B3) and splash (B2) – unblock testing and demos.  
2. **Parallel-capable:** Push notifications (N1–N5) vs application backend design (A1, A2–A5).  
3. **After Functions A2–A5:** Client switch to Functions (A7), then Firestore rules (A6).  
4. **Before production:** App Check (S2), rate limits (S3), audit log (S4), email verification (S1), then full test plan (T1).

---

## 10. Document Control

- This audit is based on the Feb 11, 2026 meeting minutes and the instructions + security/application specs provided.  
- Estimates assume one developer familiar with the codebase; scope creep or new requirements will increase hours.  
- “Production ready” here means: bugs fixed, notifications working, application rules enforced server-side, and baseline security (App Check, rate limits, audit) in place.
