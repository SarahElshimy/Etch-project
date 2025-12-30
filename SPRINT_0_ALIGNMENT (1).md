# Sprint 0: Bug Fixes & Code Alignment

**Version:** 1.9  
**Date:** December 24, 2025  
**Purpose:** Stabilization sprint focused on systemic bugs and refactor drivers before feature delivery  
**Developed by:** Technical Product Owner (Human)

---

## Executive Summary

Sprint 0 is a stabilization sprint to unblock Creator Activation and protect the platform from security, data integrity, and performance failures before new feature delivery.

- **Primary outcomes:** enforce backend security controls, standardize Firestore schema/indexing, add runtime write validation, fix list/place write-path integrity issues, and harden import + migration workflows.
- **Scope:** 18 in-scope bugs grouped into 10 Jira tasks (Core + Phase 1 prioritized).
- **Estimate:** 40 SP total (Fullstack: 32 SP, Mobile: 8 SP). Using 1 SP ≈ 1 dev-hour, this is ~5 dev-days.
- **Impact:** prevents data loss (U-30), resolves list/place corruption (U-15/U-26/U-24), stabilizes onboarding (U-21), fixes import reliability (U-22/U-23/U-35), and reduces query costs + latency (2/11/U-29).

---

## 📋 Jira Task List (Sprint 0)

Tasks are grouped by logical work unit. Multiple bugs may be resolved by a single task when they share the same root cause or code touchpoints.

### Task Groupings

| Task ID | Task Name | Linked Bugs | SP | Owner | Priority | Justification / Impact |
|---------|-----------|-------------|---:|:-----:|:--------:|------------------------|
| S0-01 | **Firebase Security Rules Refactor** | 1, 14 | 8 | Fullstack | 🔴 P0 | **Bugs Resolved:** Unauthorized data access, no rate limiting, abuse vectors. **Features Unlocked:** Safe multi-user operations for Lists (LI-05/06/07), Places (PL-11/12), and DM (NO-08/13). **PRD Impact:** Foundation for PV-01–PV-12 (Privacy controls), PR-10/11 (Follow/Message buttons). **Business Impact:** Prevents data breaches; eliminates cost spikes from abuse; required for App Store compliance. |
| S0-02 | **Firestore Schema Standardization** | 8, 13 | 5 | Fullstack | 🔴 P0 | **Bugs Resolved:** Schema drift causing missing places, broken near-me queries, inconsistent timestamps. **Features Unlocked:** HP-03/04/05 (Homepage carousels), SL-03/08 (Location filters), SP-03/10 (Place search by location). **PRD Impact:** Prerequisite for Phase 2 Discovery (search + location-based recommendations). **Business Impact:** Reduces query failures; enables geospatial features; cuts debugging time from schema inconsistencies. |
| S0-03 | **Data Validation Layer (Zod)** | 3 | 5 | Fullstack | 🔴 P0 | **Bugs Resolved:** Malformed coordinates, missing required fields, mixed timestamp formats causing silent failures. **Features Unlocked:** Reliable LI-08/09/10/11 (List CRUD), IM-02–08 (Import flows), PL-02/06 (Place details). **PRD Impact:** Supports data integrity for all Phase 1 Creator Activation features. **Business Impact:** Prevents downstream crashes; reduces support tickets from corrupted data; enables confident schema evolution. |
| S0-04 | **Query Optimization & Pagination** | 2, 11, U-29 | 8 | Fullstack | 🔴 P0 | **Bugs Resolved:** App slowness, high Firestore costs, search timeouts on large datasets. **Features Unlocked:** HP-07/08 (Pull-to-refresh, infinite scroll), SL-01–08 (List search), SP-01–11 (Place search), HP-10 (For You algorithm). **PRD Impact:** Required for Phase 2 Consumer Discovery at scale; directly enables performant Home/Search surfaces. **Business Impact:** Cuts Firebase bill by ~60%; improves app responsiveness (target <2s load); supports 10x user growth without re-architecture. |
| S0-05 | **List/Place Write Path Refactor** | U-15, U-26, U-24 | 5 | Mobile | 🔴 P0 | **Bugs Resolved:** Disappearing places, inability to add places to existing lists, crashes on list name edits. **Features Unlocked:** LI-08 (Create List), LI-09 (Add Place via Search), LI-11 (Edit List), LI-12 (Remove Place), LI-16 (Places Tab). **PRD Impact:** Core Phase 1 Creator Activation journey—creators cannot build quality lists without this fix. **Business Impact:** Eliminates #1 user-reported data loss issue; directly impacts creator retention and list quality. |
| S0-06 | **Import Pipeline Hardening** | U-22, U-23, U-35 | 3 | Fullstack | 🟠 P1 | **Bugs Resolved:** Google Maps URL parsing failures, missing items in imports, only first link processed from multi-link pastes. **Features Unlocked:** IM-02 (Google Maps Import), IM-07 (Save to Existing List), IM-08 (Create New List from Import), IM-11 (Text/Notes Import). **PRD Impact:** Import is the primary creator onboarding path—broken imports block creator activation. **Business Impact:** Increases import success rate from ~70% to >95%; reduces manual data entry friction; improves creator NPS. |
| S0-07 | **Data Migration & Recovery** | U-30 | 3 | Fullstack | 🔴 P0 | **Bugs Resolved:** Lists/places deleted or hidden after migration scripts run; mixed ID schemes causing data invisibility. **Features Unlocked:** Safe execution of future migrations; data recovery procedures. **PRD Impact:** Protects all existing user data—prerequisite for any production deployment. **Business Impact:** Prevents catastrophic data loss incidents; restores user trust; establishes migration safety protocols for ongoing development. |
| S0-08 | **Auth Flow Stabilization** | U-21 | 1 | Mobile | 🟠 P1 | **Bugs Resolved:** App crash/freeze after signup on iPhone due to blocking push token registration. **Features Unlocked:** ON-01 (Email Sign-up), ON-08 (Progressive Profiling), NO-09–13 (Push Notifications). **PRD Impact:** Broken signup funnel blocks all new user activation—critical for Phase 1 Creator Activation. **Business Impact:** Fixes complete signup drop-off on iOS; directly impacts new user conversion rate. |
| S0-09 | **Coordinate Validation** | U-10 | 1 | Mobile | 🟡 P2 | **Bugs Resolved:** Incorrect distance calculations, wrong map regions, invalid 0,0 coordinates polluting data. **Features Unlocked:** PL-03 (Map Pin), LI-17 (List Map Tab), SP-10 (Sort by Distance), HP-03 (Places For You). **PRD Impact:** Enables accurate location-based features for Phase 2 Discovery. **Business Impact:** Improves map UX reliability; prevents user confusion from wrong distance/location displays. |
| S0-10 | **API Key Security** | 9 | 1 | Mobile | 🟠 P1 | **Bugs Resolved:** Google API keys exposed in repo; no key restrictions configured. **Features Unlocked:** Safe production deployment; enables CI/CD with secrets management. **PRD Impact:** Required for any public release—security baseline for all phases. **Business Impact:** Prevents API key abuse/cost spikes; meets security audit requirements; enables safe open-source contribution model. |
| | **TOTAL** | **18 bugs** | **40 SP** | | | |

### Task Details

#### S0-01: Firebase Security Rules Refactor (8 SP)
**Linked Bugs:** 1 (Security Rules), 14 (Rate Limiting)
**Scope:**
- [ ] Audit and rewrite `firestore.rules` (ownership checks, field-level diffs)
- [ ] Audit and rewrite `storage.rules`
- [ ] Remove dev-only allowances and mock user patterns
- [ ] Add App Check enforcement
- [ ] Add per-user quotas for `import_requests`
- [ ] Test with Firebase Emulator

**Files:** `firestore.rules`, `storage.rules`, `firebase.json`

---

#### S0-02: Firestore Schema Standardization (5 SP)
**Linked Bugs:** 8 (No Schema), 13 (No Indexes)
**Scope:**
- [ ] Document canonical schema for `users`, `lists`, `places`, `import_requests`
- [ ] Resolve `locations[]` vs `locationIds[]` hybrid pattern
- [ ] Standardize timestamp strategy (Firestore serverTimestamp vs ISO strings)
- [ ] Deploy composite indexes via Firebase CLI
- [ ] Update TypeScript interfaces to match

**Files:** `src/types/index.ts`, `firestore.indexes.json`, `Web & Backend/shared/schema.ts`

---

#### S0-03: Data Validation Layer (5 SP)
**Linked Bugs:** 3 (No Validation)
**Scope:**
- [ ] Enforce Zod schemas at service boundaries (mobile + backend)
- [ ] Add validation to `createList`, `updateList`, `upsertPlace`, `requestImport`
- [ ] Reject malformed coordinates, missing required fields
- [ ] Add Firestore rules validation for required fields/types

**Files:** `src/services/firestoreService.ts`, `src/services/placesService.ts`, `src/services/importService.ts`, `Web & Backend/server/firebase-service.ts`, `Web & Backend/shared/schema.ts`

---

#### S0-04: Query Optimization & Pagination (8 SP)
**Linked Bugs:** 2 (High API Costs), 11 (Search Performance), U-29 (App Slow)
**Scope:**
- [ ] Add cursor-based pagination to `getAllLists`, `searchLists`, `searchPlaces`
- [ ] Replace client-side "scan & filter" with server-side indexed queries
- [ ] Batch place fetches using chunked `where('__name__','in',...)` (max 10)
- [ ] Add React Query/SWR caching layer
- [ ] Add query limits to all collection reads

**Files:** `src/services/firestoreService.ts`, `src/services/placesService.ts`, `src/screens/SearchScreen.tsx`, `src/screens/HomeScreenNew.tsx`

---

#### S0-05: List/Place Write Path Refactor (5 SP)
**Linked Bugs:** U-15 (Disappearing Places), U-26 (Cannot Add Places), U-24 (Edit Crash)
**Scope:**
- [ ] Make `locationIds` updates incremental (append, not rebuild)
- [ ] Separate metadata updates from place updates
- [ ] Enforce list hydration before enabling add-place
- [ ] Add transactional writes for list/place operations
- [ ] Fix Redux state sync issues

**Files:** `src/services/firestoreService.ts`, `src/screens/ListDetailScreen.tsx`, `src/components/AddPlaceModal.tsx`, `src/components/EditListModal.tsx`, `src/store/slices/listsSlice.ts`

---

#### S0-06: Import Pipeline Hardening (3 SP)
**Linked Bugs:** U-22 (URL Parsing), U-23 (Missing Items), U-35 (Multi-Link)
**Scope:**
- [ ] Extract/clean URLs before enqueueing (client + server)
- [ ] Parse multiple URLs from pasted text
- [ ] Handle partial place failures gracefully
- [ ] Add retry logic for geocoding/enrichment failures

**Files:** `src/components/ImportTypeModal.tsx`, `src/services/importService.ts`, `Web & Backend/server/firebase-poller.ts`, `Web & Backend/server/routes.ts`

---

#### S0-07: Data Migration & Recovery (3 SP)
**Linked Bugs:** U-30 (Data Deleted)
**Scope:**
- [ ] Audit cleanup scripts for destructive operations
- [ ] Stop filtering lists due to bad images (use fallback image)
- [ ] Fix numeric ID vs Firebase UID handling
- [ ] Create data verification/recovery procedure
- [ ] Ensure backups before running migration scripts

**Files:** `scripts/cleanInvalidPlaceIds.js`, `src/scripts/cleanupDuplicates.ts`, `src/utils/imageValidation.ts`

---

#### S0-08: Auth Flow Stabilization (1 SP)
**Linked Bugs:** U-21 (Crash After Signup)
**Scope:**
- [ ] Make push token registration fire-and-forget (remove `await`)
- [ ] Add timeout for notification permission prompts
- [ ] Defer permission prompts to post-onboarding

**Files:** `src/screens/auth/SignUpProfileScreen.tsx`, `src/services/pushNotificationService.ts`

---

#### S0-09: Coordinate Validation (1 SP)
**Linked Bugs:** U-10 (Wrong Distance)
**Scope:**
- [ ] Add coordinate validation in `upsertPlace`
- [ ] Reject or repair invalid `0,0` coordinates
- [ ] Update map region calculation to handle missing coords

**Files:** `src/services/placesService.ts`, `src/screens/ListDetailScreen.tsx`

---

#### S0-10: API Key Security (1 SP)
**Linked Bugs:** 9 (Keys Exposed)
**Scope:**
- [ ] Move API keys from `app.json` to EAS secrets
- [ ] Add Google API key restrictions (bundle/package)
- [ ] Remove keys from scripts

**Files:** `app.json`, `eas.json`, `scripts/*`

---

### Sprint 0 Execution Order

```
Week 1 (Days 1-3): Foundation
├── S0-01: Security Rules (Fullstack) ──────────────── 8 SP
├── S0-02: Schema Standardization (Fullstack) ──────── 5 SP
└── S0-10: API Key Security (Mobile) ───────────────── 1 SP
                        Subtotal: 14 SP

Week 1 (Days 4-5): Data Integrity
├── S0-03: Data Validation (Fullstack) ─────────────── 5 SP
├── S0-05: Write Path Refactor (Mobile) ────────────── 5 SP
└── S0-07: Migration & Recovery (Fullstack) ────────── 3 SP
                        Subtotal: 13 SP

Week 2 (Days 1-2): Performance & Polish
├── S0-04: Query Optimization (Fullstack) ──────────── 8 SP
├── S0-06: Import Hardening (Fullstack) ────────────── 3 SP
├── S0-08: Auth Stabilization (Mobile) ─────────────── 1 SP
└── S0-09: Coordinate Validation (Mobile) ──────────── 1 SP
                        Subtotal: 13 SP

                     TOTAL: 40 SP
```

### Jira Epic Structure

```
📁 Epic: Sprint 0 - Stabilization
├── 📋 S0-01: Firebase Security Rules Refactor [8 SP] [Fullstack]
│   ├── 🐛 Bug 1: Firebase Security Rules Not Enforced
│   └── 🐛 Bug 14: No Rate Limiting
├── 📋 S0-02: Firestore Schema Standardization [5 SP] [Fullstack]
│   ├── 🐛 Bug 8: No Defined NoSQL Schema
│   └── 🐛 Bug 13: No Firestore Indexes Defined
├── 📋 S0-03: Data Validation Layer [5 SP] [Fullstack]
│   └── 🐛 Bug 3: No Data Validation at Write Time
├── 📋 S0-04: Query Optimization & Pagination [8 SP] [Fullstack]
│   ├── 🐛 Bug 2: High API Costs
│   ├── 🐛 Bug 11: Search Performance
│   └── 🐛 U-29: Application is Very Slow
├── 📋 S0-05: List/Place Write Path Refactor [5 SP] [Mobile]
│   ├── 🐛 U-15: Disappearing Places
│   ├── 🐛 U-26: Cannot Add New Places
│   └── 🐛 U-24: App Crashes When Editing List
├── 📋 S0-06: Import Pipeline Hardening [3 SP] [Fullstack]
│   ├── 🐛 U-22: Google Map Links Not Parsing
│   ├── 🐛 U-23: Not All Items Imported
│   └── 🐛 U-35: Text Import Only First Link
├── 📋 S0-07: Data Migration & Recovery [3 SP] [Fullstack]
│   └── 🐛 U-30: All Lists/Places Deleted
├── 📋 S0-08: Auth Flow Stabilization [1 SP] [Mobile]
│   └── 🐛 U-21: App Crashes After Signup
├── 📋 S0-09: Coordinate Validation [1 SP] [Mobile]
│   └── 🐛 U-10: Location/Distance Wrong
└── 📋 S0-10: API Key Security [1 SP] [Mobile]
  └── 🐛 Bug 9: API Keys Exposed
```

### Owner Distribution

| Owner | Tasks | SP | % of Sprint |
|-------|------:|---:|:-----------:|
| Fullstack | 6 | 32 | 80% |
| Mobile | 4 | 8 | 20% |
| **Total** | **10** | **40** | 100% |

---

## 📐 Effort Estimation

| SP | Hours | Complexity | Notes |
|----|-------|------------|-------|
| 1 | 1 hr | Trivial | Config or single-line fix |
| 2 | 2 hrs | Simple | Small refactor or contained bug fix |
| 3 | 3 hrs | Small | Multi-file fix with a consistent pattern |
| 5 | 5 hrs | Medium | Cross-cutting change across modules |
| 8 | 1 day | Complex | Architectural decision + careful validation |

> **Assumption:** Mid-level dev team velocity; SP maps roughly to dev-hours. Testing and validation are included in task SP estimates.

---

## Scope Rules (Updated)

Sprint 0 includes only:
- **Major bugs** that break core journeys or cause data loss.
- **Critical issues that require refactors** (schema/service/query/security changes across multiple modules).

Sprint 0 explicitly excludes from **critical** classification:
- **UI-only** issues (layout, keyboard handling, visual overlaps, iconography).
- **Single-file / localized fixes** (quick patches, one-off parsing tweaks, isolated screen changes).

> UI-only or single-file issues can still be **Major** if they block a core journey, but they are not treated as **Critical**.

---

## Sprint 0 Backlog (In-Scope)

Estimates below use `MOBILE_APP_BUGS.md` SP estimates (1 SP = ~1 dev-hour) as the baseline.

> **Note:** Bug-level SP estimates below reflect the source tracker. The Sprint 0 delivery estimate is governed by the Jira task plan at the top of this document (40 SP).

### A) Critical Refactor Drivers (Systemic)

These items are considered **Critical** because they require cross-cutting refactors (data model, security rules, service-layer patterns, query/pagination architecture).

| Bug ID | Title | Phase | SP | Why It's Critical (Refactor Driver) |
|--------|-------|-------|---:|-------------------------------------|
| 1 | Firebase Security Rules Not Enforced | ⚪ Core | 21 | Security model + rules refactor across users/lists/places/DM/storage |
| 2 | High API Costs (Inefficient Queries) | 🔵 Phase 2 | 34 | Query/pagination/caching refactor across discovery/search/list fetching |
| 3 | No Data Validation at Write Time | ⚪ Core | 21 | Service-layer validation refactor (Zod/Yup) + safer write patterns |
| 8 | No Defined NoSQL Schema | ⚪ Core | 21 | Formal schema + migration (locations[] vs locationIds[], timestamps, consistency) |
| 11 | Search Performance on Large Datasets | 🔵 Phase 2 | 13 | Search architecture refactor (server-side/pagination/virtualization) |
| 13 | No Firestore Indexes Defined | ⚪ Core | 5 | Query/index strategy update to support refactors and remove perf bottlenecks |
| 14 | No Rate Limiting | ⚪ Core | 13 | Cost/safety refactor (rules/middleware/CF patterns) |
| U-15 | Disappearing Places | 🟢 Phase 1 | 13 | Data integrity refactor across list/place write/read + migration edge cases |
| U-23 | Not All Items Imported from Google Maps | 🟢 Phase 1 | 8 | Import pipeline refactor (retries, parsing, partial failures) |
| U-26 | Cannot Add New Places to Existing List | 🟢 Phase 1 | 8 | Shared list/place write path refactor + validation/transactions |
| U-29 | Application is Very Slow | 🔵 Phase 2 | 13 | Cross-app performance refactor (ties to #2/#11 + caching strategy) |
| U-30 | All Lists and Places Deleted After Migration | 🟢 Phase 1 | 21 | Migration/data recovery refactor + verification process |

**Subtotal:** 12 items (191 SP)

### B) Major Bugs (Localized / Single-Module)

These items are in Sprint 0 because they are **Major blockers**, but they are **not Critical** under the updated rule (UI-only or localized fixes).

| Bug ID | Title | Phase | SP | Why (Notes) |
|--------|-------|-------|---:|-----------------------------|
| U-21 | App Crashes After Creating New User (iPhone) | 🟢 Phase 1 | 1 | Remove `await` from push registration (1 line) |
| U-22 | Google Map Links Not Parsing Correctly | 🟢 Phase 1 | 2 | URL extraction/cleaning for pasted text |
| U-24 | App Crashes When Editing List Name | 🟢 Phase 1 | 1 | Split metadata update from places (simple refactor) |
| U-35 | Text Import Only Registers First Link | 🟢 Phase 1 | 2 | Multi-link parser + batch enqueue |
| U-10 | Location/Distance Completely Wrong | 🔵 Phase 2 | 2 | Coordinate validation + fallback |
| 9 | API Keys Exposed in Code | ⚪ Core | 1 | Move to EAS secrets (config change) |

**Subtotal:** 6 items (9 SP)

---

## Out of Sprint 0 (Moved to Later Cycles)

The following are intentionally deferred because they are not Sprint 0 goals (UI polish, feature completion, or isolated enhancements):
- OAuth feature completion: `5`, `6`, `7`
- Messaging feature bugs (Cycle 3): `U-7`, `U-8`, `15`
- UI/UX-only issues and minor visual defects: `U-11`, `U-12`, `U-14`, `U-33`, `U-34`, `U-4`, `U-2/U-5`, `U-27`
- Map/UI/device-specific issues: `4`, `U-6`, `U-1`, `10`
- Tooling/observability debt: `16`, `17`, `18`, `19`, `20`

### Moved to Sprint 2+ (Phase-Aligned)

| Bug ID | Title | Target Sprint | Reason |
|--------|-------|---------------|--------|
| U-31 | Admin Dashboard Not Synced with App Data | Sprint 2 | Admin explicitly planned in Sprint 2 Fullstack track |
| U-17 | Country Search Results Inaccurate | Sprint 3 | Discovery/Search phase; not prerequisite for Sprint 1 |
| U-18 | Location Algorithm Not Showing Local Content | Sprint 3 | Discovery/Search phase; not prerequisite for Sprint 1 |
| U-9 | Keyboard Takes Over Screen (iPhone) | Sprint 4 | Phase 3 (Messaging); UX/layout issue |

---

## Sprint 0 Total

| Bucket | Items | SP | Dev-Days |
|--------|------:|---:|---------:|
| Critical Refactor Drivers | 12 | 31 | ~4 days |
| Major Localized Bugs | 6 | 9 | ~1 day |
| **Total (Sprint 0)** | **18** | **40 SP** | **~5 days** |

### Phase Distribution (Sprint 0)

| Phase | Bug Count |
|-------|----------:|
| ⚪ Core (Foundation) | 6 |
| 🟢 Phase 1 - Creator Activation | 9 |
| 🔵 Phase 2 - Consumer Discovery | 3 |
| 🟣 Phase 3 - Social & Engagement | 0 |
| 🟠 Phase 4 - Privacy & Advanced | 0 |

> **Interpretation:** Sprint 0 is tightly focused on Core + Phase 1 prerequisites for Creator Activation (Sprint 1).

> **Capacity Buffer:** With 56 SP capacity and 40 SP allocated, there is 16 SP buffer for scope adjustments or early Sprint 1 work.

---

## Technical Deep Dive (Code Touchpoints)

This section links each Sprint 0 item to the primary code touchpoints observed in the repo, plus a concrete fix sketch. It is intended to help the dev team estimate accurately and avoid “paper” fixes that don’t address root causes.

### A) Critical Refactor Drivers — Code Touchpoints

#### 1) Bug 1 — Firebase Security Rules Not Enforced
- **Touchpoints (Rules):**
  - `firestore.rules` — Known vulnerable patterns:
    - `places: allow create: if true; allow update: if true;`
    - `lists update: allows any auth user to update imported lists (isImported == true)`
    - `lists update: allows unauth updates to engagement metrics`
  - `storage.rules`
- **Touchpoints (All Write Operations - Mobile):**
  - `src/services/firestoreService.ts` — createList, updateList, createSave, createFollow, createLike
  - `src/services/placesService.ts` — upsertPlace
  - `src/services/dmService.ts` — message creation
  - `src/services/authService.ts` — user profile creation
  - `src/services/importService.ts` — import request creation
- **Touchpoints (All Write Operations - Backend):**
  - `Web & Backend/server/firebase-service.ts` — all CRUD operations
  - `Web & Backend/server/firebase-poller.ts` — import-related writes
- **What we see in code:** `places` allows `create/update: if true`; `lists` update allows any authenticated user to update imported lists (`resource.data.isImported == true`); `notificationSent` updates are allowed without ownership checks; dev-only mock user IDs and unauth creates exist.
- **Fix sketch:** remove dev allowances; restrict writes by ownership + field-level diffs; remove client-side ability to update `notificationSent` (Admin SDK only); tighten `places` writes; add App Check and rules tests (emulator + simulator).

#### 2) Bug 2 — High API Costs (Inefficient Queries)
- **Touchpoints (Mobile - Heavy Queries):**
  - `src/services/firestoreService.ts`:
    - `getAllLists` — Reads up to 500 lists, filters client-side
    - `getAllUsers` — Fetches ALL users without pagination
    - `getListsNearMe*` — Geohash range queries without limits
    - `searchLists` — No pagination, loads full result set
  - `src/services/placesService.ts`:
    - `getAllPlaces` — Reads up to 1000 places
    - `getPlacesByIds` — N parallel getDoc calls (**N+1 pattern**)
  - `src/screens/SearchScreen.tsx` — Heavy reads on 300ms debounce
  - `src/screens/HomeScreenNew.tsx` — Multiple expensive parallel reads
  - `src/screens/ListDetailScreen.tsx` — Full place hydration on mount
- **Touchpoints (Backend - Heavy Queries):**
  - `Web & Backend/server/firebase-service.ts`:
    - `getAllLists` — limit: 50, but still unbounded
    - `listPlaces` — fetches all places
    - `getTrendingLists` — complex aggregation
- **What we see in code:** `searchLists` reads up to 500 lists and filters client-side; `searchPlacesByCity` reads up to 1000 places and filters client-side; profile search fetches **all** users; list hydration loads places with N parallel `getDoc` calls.
- **Fix sketch:** introduce pagination (cursor-based `startAfter`) + caching (React Query/SWR); replace client-side “scan & filter” with server-side search (Algolia/Typesense) or Firestore-friendly indexed tokens; batch place fetches using chunked `where('__name__','in',...)` instead of N reads.

#### 3) Bug 3 — No Data Validation at Write Time
- **Touchpoints (Mobile - All Write Operations):**
  - `src/services/firestoreService.ts` — createList, updateList, deleteList, createSave, deleteSave, createFollow, deleteFollow, createLike, deleteLike, updateUserProfile
  - `src/services/placesService.ts` — upsertPlace
  - `src/services/importService.ts` — requestLinkImport, requestImageImport, requestTextImport
  - `src/services/dmService.ts` — message creation (no sanitization)
  - `src/services/authService.ts` — user profile creation
  - `src/services/imageStorageService.ts` — storage uploads (no file type validation)
- **Touchpoints (Backend - All Write Operations):**
  - `Web & Backend/server/firebase-service.ts` — createList, updateList, deleteList, createPlace, updatePlace, updateImportRequest
  - `Web & Backend/server/firebase-poller.ts` — import-related writes
  - `Web & Backend/server/routes.ts` — API-triggered writes
- **Touchpoints (Validation Schemas - NOT Enforced):**
  - `Web & Backend/shared/schema.ts` — **Zod schemas exist but NOT used on writes**
  - `src/types/index.ts` — TypeScript types (compile-time only, no runtime validation)
- **What we see in code:** write paths primarily "clean undefined" but do not validate schemas; mixed timestamp types are written (ISO strings vs Firestore server timestamps); no centralized schema enforcement. **Zod schemas exist in shared/schema.ts but are NOT being used at write time.**
- **Fix sketch:** add Zod/Yup schemas for `User`, `List`, `Place`, `ImportRequest` and validate at service boundaries; add Firestore rules validation for required fields/types; reject malformed lat/lng/country fields.

#### 4) Bug 8 — No Defined NoSQL Schema
- **Touchpoints:** `etch-mobile-main/src/types/index.ts`, `etch-mobile-main/src/services/firestoreService.ts` (`getList`, `getUserLists`, `createList`, `updateList`)
- **What we see in code:** hybrid schema (`locations[]` embedded + `locationIds[]` references); reads prefer `locationIds` and may ignore embedded data; list geo fields (`latitude/longitude/geohash`) are not maintained on create/update in the mobile app.
- **Fix sketch:** define canonical Firestore schema and migration plan; ensure list geo fields are computed on any location change (median/centroid + geohash); harden reads to handle partial migrations; standardize timestamp strategy end-to-end.

#### 5) Bug 11 — Search Performance on Large Datasets
- **Touchpoints:** `etch-mobile-main/src/screens/SearchScreen.tsx`, `etch-mobile-main/src/services/firestoreService.ts` (`searchLists`), `etch-mobile-main/src/services/placesService.ts` (`searchPlacesByCity`)
- **What we see in code:** search triggers heavy reads on 300ms debounce; filtering/scoring is client-side; city search accepts places with empty/missing city values due to permissive matching logic.
- **Fix sketch:** same as Bug #2 plus fix city matching logic (do not treat empty city as match); apply list virtualization; add query limits/cursors and defer heavy joins (user + places) until detail view.

#### 6) Bug 13 — No Firestore Indexes Defined
- **Touchpoints:** `etch-mobile-main/firestore.indexes.json`
- **What we see in code:** composite indexes are already defined in-repo (including `lists.isPublic + saves`, `lists.isPublic + geohash`, etc.). The risk is likely “not deployed” or “missing for newly added queries”, not “missing file”.
- **Fix sketch:** audit all production queries (mobile + backend poller/admin) and confirm required composite indexes exist and are deployed via Firebase CLI.

#### 7) Bug 14 — No Rate Limiting
- **Touchpoints:** `etch-mobile-main/firestore.rules` (public/unauth update allowances), `etch-mobile-main/firebase.json` (no functions configured), `Web & Backend/server` (API endpoints)
- **What we see in code:** no App Check usage; rules allow unauth updates to engagement counters; import requests can be spammed; backend poller processes requests continuously.
- **Fix sketch:** enable App Check and require it in rules; remove unauth counter updates (move to server-side increments); add per-user quotas for `import_requests` (rules + backend enforcement); add backend rate limiting (IP/user) for costly endpoints (e.g., photo proxy).

#### 8) U-15 — Disappearing Places
- **Touchpoints (Mobile):**
  - `src/screens/ListDetailScreen.tsx` — handleAddLocation, PlacesList
  - `src/components/AddPlaceModal.tsx` — onAddPlace callback
  - `src/services/firestoreService.ts` — createList, updateList, getList
  - `src/services/placesService.ts` — getPlacesByIds, upsertPlace, batchGetPlaces
  - `src/screens/CreateListScreen.tsx` — initial list creation
  - `src/store/slices/listsSlice.ts` — setSelectedList, hydration
  - `src/types/index.ts` — List interface: `locations[]` vs `locationIds[]`
- **Touchpoints (Backend):**
  - `Web & Backend/server/firebase-service.ts` — updateList, getList, createList
  - `Web & Backend/server/firebase-poller.ts` — place upsert during imports
  - `Web & Backend/shared/schema.ts` — firebaseListSchema, listSchema
- **Touchpoints (Scripts):**
  - `scripts/cleanInvalidPlaceIds.js` — may remove valid place IDs
  - `src/scripts/cleanupDuplicates.ts` — deletes imported duplicate lists
- **Root Cause Analysis:**
  - `updateList` rebuilds `locationIds` from upsert results (**non-incremental**)
  - If list in Redux is not hydrated, updates overwrite with only new place
  - **Dual storage pattern:** `locations[]` embedded + `locationIds[]` references causes mismatch
- **What we see in code:** `updateList` rebuilds `locationIds` from upsert results; partial upsert failures lead to incomplete `locationIds`; `getList` uses `locationIds` when present and does not fallback to embedded locations if hydration is incomplete.
- **Fix sketch:** make location updates incremental (union old `locationIds` + new placeId) and/or transactional; if place hydration returns fewer docs than `locationIds`, merge/fallback to embedded locations and queue a repair job; stop "cleanup" scripts from removing IDs without repair/backfill.
- **Related:** U-26 (Cannot Add New Places) — Same root cause

#### 9) U-23 — Not All Items Imported from Google Maps
- **Touchpoints (mobile):** `etch-mobile-main/src/components/ImportTypeModal.tsx` (sends raw text as `linkUrl`)
- **Touchpoints (backend):** `Web & Backend/server/firebase-poller.ts` (import routing), `Web & Backend/server/routes.ts` (`scrapeGoogleMapsList`), `Web & Backend/server/firebase-service.ts` (`upsertPlace`, list creation)
- **What we see in code:** `link` imports pass `linkUrl` directly to `scrapeGoogleMapsList` (no URL extraction), so pasting “title + URL” can break parsing; backend skips places missing coordinates during `upsertPlace` and still creates the list, resulting in `locationIds.length < locations.length` → missing items in mobile (because reads prefer `locationIds`).
- **Fix sketch:** extract a clean URL for `link` imports (same as `google-maps` imports) OR make mobile send `importType='google-maps'` with a cleaned `googleMapsUrl`; on partial place failures, either retry geocoding/Place Details or avoid writing `locationIds` until all places are persisted (so mobile falls back to embedded).

#### 10) U-26 — Cannot Add New Places to Existing List
- **Touchpoints (Mobile):**
  - `src/components/AddPlaceModal.tsx` — place selection, onAddPlace
  - `src/screens/ListDetailScreen.tsx` — handleAddLocation (**PRIMARY**)
  - `src/services/firestoreService.ts` — updateList (rebuilds locationIds)
  - `src/services/placesService.ts` — upsertPlace, getPlacesByIds
  - `src/store/slices/listsSlice.ts` — setSelectedList (must be hydrated)
- **Touchpoints (Backend):**
  - `Web & Backend/server/firebase-service.ts` — updateList
- **Root Cause Analysis:** (Same as U-15)
  - `updateList` rebuilds entire `locationIds` array from upsert results
  - If existing places not in Redux state, they get overwritten
  - **Non-incremental update pattern loses existing data**
- **What we see in code:** add-place path appends to `list.locations`; if list in Redux is not hydrated (locations empty while `locationIds` exists), updates can overwrite the list with only the new place; `updateList` recomputes `locationIds` from the passed locations array instead of incrementally appending.
- **Fix sketch:** enforce hydration before enabling add-place; update list writes to be incremental (append to existing `locationIds`); do not rebuild `locationIds` from `updates.locations` unless explicitly doing a full replacement with validated input.
- **Related:** U-15 (Disappearing Places) — Same root cause

#### 11) U-29 — Application is Very Slow
- **Touchpoints:** `etch-mobile-main/src/screens/SearchScreen.tsx`, `etch-mobile-main/src/screens/HomeScreenNew.tsx`, `etch-mobile-main/src/services/firestoreService.ts`, `etch-mobile-main/src/services/placesService.ts`
- **What we see in code:** home/search load paths combine multiple expensive reads (geohash bounds queries + user batching + list scanning + per-place reads); no caching; several operations are repeated per keystroke/tab switch.
- **Fix sketch:** treat as the rollup of Bugs #2/#11 + batching (`getPlacesByIds`) + caching; add lightweight “preview” endpoints for home/search (no hydration, fewer fields); profile queries should not fetch full collections.

#### 12) U-30 — All Lists and Places Deleted After Migration
- **Touchpoints (Mobile Scripts - etch-mobile-main/scripts/):**
  - `cleanInvalidPlaceIds.js` — **HIGH RISK** - Removes invalid place IDs, may delete valid references
  - `addGeohashToLists.ts` — Adds geohash to lists
  - `addGeohashToPlaces.ts` — Adds geohash to places
  - `geocodePlaces.js` — Geocodes missing coordinates
  - `importData.js` — CSV data import
- **Touchpoints (Mobile Scripts - etch-mobile-main/src/scripts/):**
  - `cleanupDuplicates.ts` — **CRITICAL** - Deletes imported duplicate lists by userId
  - `fixMissingCoordinates.ts` — Fixes 0,0 coordinates
  - `migrateCityField.ts` — Migrates city field
  - `migrateListImages.ts` — Image migration
- **Touchpoints (Data Filtering - Can Hide Data):**
  - `src/utils/imageValidation.ts` — Filters out lists with invalid image URLs
  - `src/services/placesService.ts` — Filters numeric locationIds (imported IDs)
- **Touchpoints (Backend):**
  - `Web & Backend/migrations/*` — Drizzle migrations
  - Firebase Console — Data verification/restore
- **Root Cause Analysis:**
  - Cleanup scripts deleting imported lists for specific user IDs
  - Mixed ID schemes (numeric imported IDs vs Firebase Auth UIDs) making data "look gone"
  - Image validation filters hiding entire lists from display
- **What we see in code:** several “filters” can hide data (invalid image URL format → list dropped; numeric imported user IDs → places dropped); at least one script can delete imported lists for specific user IDs; mixed ID schemes (numeric imported IDs vs Firebase Auth UIDs) can make data look “gone”.
- **Fix sketch:** treat this as a data-audit + recovery cycle: confirm whether data is deleted vs hidden; stop filtering entire lists due to bad images (fallback image instead); stop treating numeric IDs as “test” (use explicit flags); ensure backups exist before running cleanup scripts.

#### 13) U-31 — Admin Dashboard Not Synced with App Data
- **Touchpoints (Mobile - Writes):**
  - `src/services/firestoreService.ts` — createList, updateList (**does NOT set regionId field**)
  - `src/screens/CreateListScreen.tsx` — list creation
  - `src/screens/ListDetailScreen.tsx` — list editing
- **Touchpoints (Backend - Reads):**
  - `Web & Backend/server/firebase-service.ts`:
    - `getAllLists` — limit: 50, may miss lists
    - `getListsByUserId`, `getListsByRegion`
    - `listPlaces`, `getTrendingLists`
    - `updateAllListsWithRegions`, `updateListRegions`
  - `Web & Backend/server/routes.ts` — /api/lists, /api/users endpoints
- **Touchpoints (Admin UI):**
  - `Web & Backend/client/src/pages/AdminUsers.tsx` — fetchUserLists, list fetching
  - `Web & Backend/client/src/pages/AdminRegions.tsx` — region filtering
- **Root Cause Analysis:**
  - Admin region sync uses `regionId` but mobile doesn't populate this field
  - Mobile uses `city` strings, admin expects `regionId`
  - No real-time sync mechanism (no webhooks or Cloud Functions triggers)
  - `getAllLists` has `limit: 50` — may miss lists beyond that
- **What we see in code:** admin region sync derives regions from `locations[].countryCode`, but mobile locations do not populate `countryCode` (mobile schema uses `city/country` strings). This results in empty `regions` for many lists → admin views that depend on `regions` will miss app-created/updated lists.
- **Fix sketch:** unify schema: populate `countryCode` in location/place documents (preferred) OR update admin sync to use list/places `country` + mapping; ensure new lists get regions automatically (not via manual admin action).

### B) Major Bugs — Code Touchpoints

#### U-21 — App Crashes/Freezes After Creating New User (iPhone)
- **Touchpoints (Mobile - Auth Flow):**
  - `src/screens/auth/WelcomeScreen.tsx` — initial auth + social auth
  - `src/screens/auth/SignUpEmailScreen.tsx` — navigation to password
  - `src/screens/auth/SignUpPasswordScreen.tsx` — navigation to birthday
  - `src/screens/auth/SignUpBirthdayScreen.tsx` — navigation to profile
  - `src/screens/auth/SignUpProfileScreen.tsx` — **PRIMARY** - profile submit handler
  - `src/screens/auth/EmailAuthScreen.tsx` — resetAndNavigate on success
- **Touchpoints (Mobile - Services):**
  - `src/services/authService.ts` — signUpWithEmail, createUserProfile
  - `src/services/imageStorageService.ts` — uploadProfileImage (**may hang on iOS**)
  - `src/services/pushNotificationService.ts` — registerForPushNotifications (**await blocks navigation**)
- **Root Cause Analysis:**
  - Push token registration is awaited despite comments saying "non-blocking"
  - iOS notification permissions/token acquisition can hang indefinitely
  - UI stays in loading state and becomes unclickable
- **What we see in code:** sign-up awaits push token registration despite comments calling it “non-blocking”; if notification permissions/token acquisition hangs on iOS, the UI stays in `isLoading=true` and becomes effectively unclickable.
- **Fix sketch:** make push token registration truly fire-and-forget (do not await) or add a short timeout; defer permission prompts to an explicit in-app step after onboarding completes.

#### U-22 — Google Map Links Not Parsing Correctly
- **Touchpoints (Mobile):**
  - `src/components/ImportTypeModal.tsx` — sends raw text as sourceUrl
  - `src/services/importService.ts` — requestLinkImport
  - `src/screens/ProfileScreen.tsx` — import progress display
  - `src/navigation/AppNavigator.tsx` — Add menu modal routing
- **Touchpoints (Backend):**
  - `Web & Backend/server/firebase-poller.ts` — processImportRequest (~500 lines)
  - `Web & Backend/server/routes.ts` — **URL parsing at lines 122, 319, 957, 2233, 2362, 4015**:
    - `scrapeGoogleMapsList` — main scraper
    - `extractPlacesFromGoogleMapsUrl` — URL extraction
    - `handleGoogleMapsImport` — import routing
  - `Web & Backend/server/firebase-service.ts` — createList, upsertPlace
  - `Web & Backend/shared/schema.ts` — insertImportRequestSchema
- **Root Cause Analysis:**
  - Mobile sends raw pasted text (e.g., "Barcelona Food · Bader\nhttps://...") as sourceUrl
  - No URL extraction/cleaning before calling scraper
  - Backend URL validation checks: `maps.app.goo.gl`, `maps.google.com`, `google.com/maps`
- **What we see in code:** mobile sends raw pasted text as `linkUrl`; backend `link` import path does not extract/clean URLs before calling `scrapeGoogleMapsList`, so "title + URL" inputs can break imports.
- **Fix sketch:** extract the first valid URL on the client (and/or server) before enqueueing; when URL is Google Maps, send `importType='google-maps'` instead of `link`.
- **Related:** U-35 (Text Import Only Registers First Link)

#### U-24 — App Crashes When Editing List Name
- **Touchpoints:** `etch-mobile-main/src/components/EditListModal.tsx`, `etch-mobile-main/src/services/firestoreService.ts` (`updateList`)
- **What we see in code:** edit-list flow always includes `updates.locations`, triggering a full re-upsert of all places on name edits; this can be slow/fragile and can surface as crashes/timeouts on device.
- **Fix sketch:** do not include `locations` in list-metadata updates (name/description/image); split “metadata update” from “places update” so edits are cheap and safe.

#### U-35 — Text Import Only Registers First Link
- **Touchpoints (Mobile):**
  - `src/components/ImportTypeModal.tsx` — sends entire pasted text as single sourceUrl
  - `src/services/importService.ts` — requestLinkImport (no multi-link splitting)
- **Touchpoints (Backend):**
  - `Web & Backend/server/firebase-poller.ts` — processImportRequest (handles single URL)
  - `Web & Backend/server/routes.ts` — URL extraction regex (stops at first match)
- **Root Cause Analysis:**
  - ImportTypeModal sends entire text as single `sourceUrl` string
  - Backend URL regex extracts only first matching URL
  - No multi-link detection or batch import creation
- **What we see in code:** multi-link pastes are sent as a single `linkUrl` string; backend treats it like a single URL.
- **Fix sketch:** parse all URLs from pasted text and enqueue one import per URL (or send as `text` import and extract all places/links server-side).
- **Related:** U-22 (Google Map Links Not Parsing)

#### U-9 — Keyboard Takes Over Screen (iPhone)
- **Touchpoints:** screen-level layouts with input + scroll (e.g., DM/chat, comment composer, profile/edit flows)
- **Fix sketch:** standardize input screens with `KeyboardAvoidingView` + correct `keyboardVerticalOffset` and verified iOS behavior; fix per-screen rather than global hacks.

#### U-10 — Location/Distance Completely Wrong
- **Touchpoints:** `etch-mobile-main/src/screens/ListDetailScreen.tsx` (`getRegionForCoordinates`), `etch-mobile-main/src/services/placesService.ts` (`upsertPlace`), `etch-mobile-main/src/scripts/fixMissingCoordinates.ts`
- **What we see in code:** map region calculation assumes valid numeric coordinates; some migrations/scripts explicitly target invalid `0,0` coords; `upsertPlace` does not update existing place coordinates when better data is available.
- **Fix sketch:** enforce coordinate validation; repair invalid/missing coordinates via migration + safe fallbacks; update upsert logic to refresh coordinates (and list geo fields) when reliable values exist.

#### U-17 — Country Search Results Inaccurate
- **Touchpoints:** `etch-mobile-main/src/services/firestoreService.ts` (`searchLists`), `etch-mobile-main/src/services/placesService.ts` (`extractCity`, `extractCountry`, `searchPlacesByCity`)
- **What we see in code:** search is metadata-only and relies on inconsistent/missing `city/country`; city/country extraction heuristics can misclassify 2-part addresses; city search can match empty city values.
- **Fix sketch:** compute normalized `city/country/countryCode` from Places API address components; store searchable tokens; tighten matching rules; (likely converges with Bug #8 schema work).

#### U-18 — Location Algorithm Not Showing Local Content
- **Touchpoints:** `etch-mobile-main/src/services/firestoreService.ts` (`getListsNearMeByGeohash`, `getPlacesNearMeByGeohash`, `createList`, `updateList`)
- **What we see in code:** near-me queries require list `geohash/latitude/longitude`, but mobile list create/update does not set these fields; imported lists (backend) do set them, so behavior diverges by list origin.
- **Fix sketch:** compute and persist list geo fields on create/update; backfill for existing lists (e.g., via `etch-mobile-main/scripts/addGeohashesToLists.js`); ensure permission fallbacks don’t silently default users to the wrong region.

#### Bug 9 — API Keys Exposed in Code
- **Touchpoints:** `etch-mobile-main/app.json`, `etch-mobile-main/eas.json`, `etch-mobile-main/scripts/*`
- **What we see in code:** Google Places keys are committed in config/scripts; keys are also duplicated across environments.
- **Fix sketch:** remove keys from repo; use build-time env for convenience but rely on Google API restrictions (bundle/package restrictions) for real safety; consider backend proxy for truly sensitive calls.

## Document History

| Date | Version | Changes |
|------|---------|---------|
| Dec 24, 2025 | 1.9 | **Added Jira Task List** - 10 tasks grouping 18 bugs by logical work unit. Added task details, execution order, epic structure, and owner distribution. Total: 40 SP (Fullstack: 32, Mobile: 8). |
| Dec 24, 2025 | 1.8 | **Sprint alignment refinement** - Moved 4 bugs to later sprints: U-31→Sprint 2 (Admin), U-17/U-18→Sprint 3 (Discovery), U-9→Sprint 4 (Messaging). Sprint 0 reduced from 56→40 SP with 16 SP buffer. Now tightly focused on Core + Phase 1 prerequisites. |
| Dec 24, 2025 | 1.7 | **Phase mapping** - Added Phase column to all bugs linking to PRD roadmap phases (Phase 1=Creator, Phase 2=Discovery, Phase 3=Social, Phase 4=Admin, Core=Foundation). Added Phase Distribution summary. |
| Dec 24, 2025 | 1.6 | **Re-estimation** - Updated total SP and added estimation key/notes per bug. |
| Dec 24, 2025 | 1.5 | Re-estimated SP based on code tracing - U-15: 13→21, U-23: 8→13, U-31: 13→21, U-21: 8→13. Total: 261→298 SP. |
| Dec 24, 2025 | 1.4 | Comprehensive code tracing - Synced with MOBILE_APP_BUGS.md v1.2; Added file inventories, root cause analysis, routes.ts line numbers. |
| Dec 24, 2025 | 1.3 | Added technical deep dive with code touchpoints and fix sketches. |
| Dec 24, 2025 | 1.2 | Narrowed Sprint 0 to major bugs + critical refactor drivers. |
