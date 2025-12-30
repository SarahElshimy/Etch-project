# Sprint 1: Auth + Lists + Import Foundation

**Version:** 1.2  
**Date:** December 24, 2025  
**Purpose:** First feature sprint focused on Creator Activation (Phase 1)  
**Developed by:** Technical Product Owner (Human + AI)

---

## 📐 Sprint Overview

| Metric | Value |
|--------|-------|
| **Duration** | 7 working days |
| **Phase** | Phase 1 (Creator Activation) |
| **Goal** | Creators can sign up, create lists, and start imports |
| **Capacity** | 112 SP total (Mobile: 56 + Fullstack: 56) |

### Prerequisites (Sprint 0 Exit Criteria)
- [ ] Firebase Security Rules deployed and tested
- [ ] Firestore schema standardized
- [ ] Data validation layer operational
- [ ] Query optimization in place
- [ ] List/Place write path refactored

---

## 📊 Sprint 1 Capacity (Post-Audit)

| Track | Target | Original | Audited | Net New | Status |
|-------|:------:|:--------:|:-------:|:-------:|:------:|
| Mobile | 56 SP | 56 SP | **46 SP** | 38 SP | ✅ Under |
| Fullstack | 56 SP | 56 SP | **64 SP** | 58 SP | ⚠️ Over |
| QA | 56 SP | 40 SP | **40 SP** | - | ✅ OK |
| **Total** | 168 SP | 152 SP | **150 SP** | 96 SP | ✅ OK |

> **📊 Repo Audit Impact:**
> - **Mobile:** 8 tasks already implemented (✅ Done), reducing net-new work from 56 SP → 38 SP
> - **Fullstack:** Multiple tasks underestimated; increased 56 SP → 64 SP (+8 SP over capacity)
> - **Recommendation:** Move S1-F18 (OG Tags, 8 SP) to Sprint 2 to fit within 56 SP capacity

> **QA Note:** QA capacity runs in parallel with dev. QA tasks begin as dev tasks complete, with ~1-2 day lag. 40 SP covers testing for all dev tasks plus regression.

---

## 📋 Jira Task List (Sprint 1)

### Mobile Track (46 SP)

| Task ID | Task Name | PRD IDs | SP | Priority | Dependencies | Status |
|---------|-----------|---------|---:|:--------:|--------------|:------:|
| S1-M01 | **Google SSO Implementation** | ON-02 | 8 | 🔴 P0 | S0-01 | 🟡 Stub |
| S1-M02 | **Apple SSO Implementation** | ON-03 | 8 | 🔴 P0 | S0-01 | 🔴 New |
| S1-M03 | **Password Reset Flow** | ON-06 | 3 | 🟠 P1 | - | 🔴 New |
| S1-M04 | **Progressive Profiling** | ON-08 | 3 | 🟠 P1 | S1-M01, S1-M02 | 🟡 Partial |
| S1-M05 | **Default Privacy Setting** | ON-09 | 2 | 🟡 P2 | S1-M04 | 🔴 New |
| S1-M06 | **First-Time Experience** | ON-10 | 3 | 🟠 P1 | S1-M04 | 🔴 New |
| S1-M07 | **Account Settings Screen** | PR-14 | 3 | 🟠 P1 | - | 🟡 Partial |
| S1-M08 | **List Card UI Component** | LI-01 | 1 | 🔴 P0 | S0-05 | ✅ Done |
| S1-M09 | **List UI Consistency** | LI-02 | 3 | 🟠 P1 | S1-M08 | 🟡 Partial |
| S1-M10 | **List Detail Page** | LI-03 | 1 | 🔴 P0 | S1-M08 | ✅ Done |
| S1-M11 | **List Cover Image** | LI-04 | 1 | 🟡 P2 | S1-M10 | ✅ Done |
| S1-M12 | **Create New List Flow** | LI-08 | 1 | 🔴 P0 | S0-05 | ✅ Done |
| S1-M13 | **Add Place via Search** | LI-09 | 1 | 🔴 P0 | S0-05, S1-M12 | ✅ Done |
| S1-M14 | **Edit List Flow** | LI-11 | 1 | 🔴 P0 | S1-M10 | ✅ Done |
| S1-M15 | **Reorder Places (Drag & Drop)** | LI-13 | 5 | 🟠 P1 | S1-M10 | 🔴 New |
| S1-M16 | **List Page - Places Tab** | LI-16 | 1 | 🔴 P0 | S1-M10 | ✅ Done |
| S1-M17 | **List Page - Map Tab** | LI-17 | 1 | 🔴 P0 | S1-M10 | ✅ Done |
| | **TOTAL** | | **46 SP** | | | |

> **📊 Repo Audit Summary (Mobile):**
> - ✅ **Done (8 tasks, ~8 SP remaining polish):** List Card, List Detail, Create/Edit List, Add Place, Cover Image, Places Tab, Map Tab
> - 🟡 **Partial (3 tasks):** Google SSO (stub exists), Progressive Profiling, Account Settings (placeholder)
> - 🔴 **New (6 tasks):** Apple SSO, Password Reset, Default Privacy, FTE, Reorder Places

### Fullstack Track (56 SP)

| Task ID | Task Name | PRD IDs | SP | Priority | Dependencies | Status |
|---------|-----------|---------|---:|:--------:|--------------|:------:|
| S1-F01 | **Welcome Email System** | ON-07 | 5 | 🟠 P1 | - | 🔴 New |
| S1-F02 | **PostHog Integration** | AN-01 | 1 | 🔴 P0 | - | ✅ Done |
| S1-F03 | **Core Event Tracking** | AN-02 | 5 | 🔴 P0 | S1-F02 | 🟡 Partial |
| S1-F04 | **User Properties Setup** | AN-03 | 3 | 🟠 P1 | S1-F02 | 🟡 Partial |
| S1-F05 | **Funnel Analytics Setup** | AN-04 | 3 | 🟠 P1 | S1-F03 | 🔴 New |
| S1-F06 | **Custom Location Handling** | IM-14 | 5 | 🔴 P0 | S0-06 | 🔴 New |
| S1-F07 | **Closed Place Detection** | IM-15 | 3 | 🟠 P1 | S1-F06 | 🔴 New |
| S1-F08 | **Search Sort: Relevance** | SL-05 | 1 | 🔴 P0 | S0-04 | ✅ Done |
| S1-F09 | **Search Sort: Top** | SL-06 | 2 | 🟠 P1 | S1-F08 | 🔴 New |
| S1-F10 | **Search Sort: Newest** | SL-07 | 1 | 🟠 P1 | S1-F08 | ✅ Done |
| S1-F11 | **Search Sort: Closest** | SL-08 | 3 | 🟠 P1 | S1-F08 | 🔴 New |
| S1-F12 | **Push: New Follower** | NO-09 | 5 | 🔴 P0 | S0-01 | 🔴 New |
| S1-F13 | **Push: List Saved** | NO-10 | 3 | 🟠 P1 | S1-F12 | 🔴 New |
| S1-F14 | **Push: List Liked** | NO-11 | 3 | 🟠 P1 | S1-F12 | 🔴 New |
| S1-F15 | **Push: Comment** | NO-12 | 3 | 🟠 P1 | S1-F12 | 🔴 New |
| S1-F16 | **Push: DM Notification** | NO-13 | 2 | 🟠 P1 | S1-F12 | 🟡 Partial |
| S1-F17 | **Notification Deep Links** | NO-17 | 3 | 🔴 P0 | S1-F12 | 🔴 New |
| S1-F18 | **Share Preview (OG Tags)** | SH-03 | 8 | 🟠 P1 | - | 🔴 New |
| S1-F19 | **Deferred Bugs: U-17, U-18** | - | 5 | 🟡 P2 | S0-04 | 🔴 New |
| | **TOTAL** | | **64 SP** | | | |

> **📊 Repo Audit Summary (Fullstack):**
> - ✅ **Done (3 tasks, ~3 SP remaining polish):** PostHog Integration, Search Relevance, Search Newest
> - 🟡 **Partial (3 tasks):** Core Event Tracking, User Properties, Push DM (message pipeline exists)
> - 🔴 **New (13 tasks):** Welcome Email, Custom Location, Closed Place, Sort Top/Closest, All Push (follow/save/like/comment), Deep Links, OG Tags, Discovery Bugs

> **⚠️ Capacity Warning:** Fullstack track now estimates 64 SP (8 SP over capacity). Consider:
> 1. Moving S1-F18 (OG Tags, 8 SP) to Sprint 2
> 2. Deferring S1-F19 (Discovery Bugs, 5 SP) to Sprint 2
> 3. Adding a 2nd Fullstack resource for Sprint 1

---

## 📝 Task Details

### Mobile Track

#### S1-M01: Google SSO Implementation (8 SP)
**PRD ID:** ON-02  
**URS Ref:** FR-AUTH-004  
**SRS Ref:** SRS-AUTH-010  

> **📍 Code Status:** 🟡 Stub exists
> - `authService.signInWithGoogle()` at `src/services/authService.ts:225` has TODO for token exchange
> - Social login buttons disabled in `src/screens/auth/WelcomeScreen.tsx:30`
> - Requires Firebase OAuth provider configuration + token flow completion

**Scope:**
- [ ] Configure Google OAuth provider in Firebase
- [ ] Implement `signInWithGoogle()` in authService
- [ ] Handle token exchange flow
- [ ] Add Google sign-in button to Welcome/SignIn screens
- [ ] Test on iOS and Android

**Files:** `src/services/authService.ts`, `src/screens/auth/WelcomeScreen.tsx`, `app.json`, `firebase/auth`

---

#### S1-M02: Apple SSO Implementation (8 SP)
**PRD ID:** ON-03  
**URS Ref:** FR-AUTH-004  
**SRS Ref:** SRS-AUTH-011  

> **📍 Code Status:** 🔴 Not implemented
> - No `signInWithApple()` in `src/services/authService.ts`
> - No Apple auth usage in `src/screens/auth/*`
> - Requires iOS provisioning profile + Apple Developer configuration

**Scope:**
- [ ] Configure Apple OAuth provider in Firebase
- [ ] Implement `signInWithApple()` in authService
- [ ] Handle Apple-specific token flow (nonce, etc.)
- [ ] Add Apple sign-in button (iOS only per Apple guidelines)
- [ ] Test on iOS

**Files:** `src/services/authService.ts`, `src/screens/auth/WelcomeScreen.tsx`, `app.json`

---

#### S1-M03: Password Reset Flow (3 SP)
**PRD ID:** ON-06  
**URS Ref:** FR-AUTH-003  
**SRS Ref:** SRS-AUTH-009  

> **📍 Code Status:** 🔴 Not implemented
> - No `ForgotPasswordScreen` exists
> - `sendPasswordResetEmail()` not used in authService
> - Deep linking not configured in `src/navigation/AppNavigator.tsx` (no `linking` config)

**Scope:**
- [ ] Create ForgotPasswordScreen
- [ ] Implement `sendPasswordResetEmail()` call
- [ ] Add success/error UI states
- [ ] Deep link handling for reset links

**Files:** `src/screens/auth/ForgotPasswordScreen.tsx`, `src/services/authService.ts`

---

#### S1-M04: Progressive Profiling (3 SP)
**PRD ID:** ON-08  
**URS Ref:** FR-AUTH-001  
**SRS Ref:** SRS-AUTH-005  
**Scope:**
- [ ] Implement multi-step signup flow state persistence
- [ ] Add progress indicator UI
- [ ] Handle partial signup recovery
- [ ] Validate each step before proceeding

**Files:** `src/screens/auth/SignUp*.tsx`, `src/store/slices/authSlice.ts`

---

#### S1-M05: Default Privacy Setting (2 SP)
**PRD ID:** ON-09  
**URS Ref:** FR-PRIVACY-002  
**SRS Ref:** SRS-PRIV-004  
**Scope:**
- [ ] Add default privacy setting during onboarding
- [ ] Store in user profile
- [ ] Apply to newly created lists

**Files:** `src/screens/auth/SignUpProfileScreen.tsx`, `src/services/firestoreService.ts`

---

#### S1-M06: First-Time Experience (3 SP)
**PRD ID:** ON-10  
**URS Ref:** FR-AUTH-010  
**SRS Ref:** SRS-AUTH-019  

> **📍 Code Status:** 🔴 Not implemented
> - `src/components/OnboardingTooltip.tsx` does not exist
> - Net-new UI + state persistence + analytics tracking

**Scope:**
- [ ] Guide users to first action after profile setup
- [ ] Show contextual tooltips/highlights
- [ ] Track FTE completion in user profile

**Files:** `src/screens/HomeScreenNew.tsx`, `src/components/OnboardingTooltip.tsx`

---

#### S1-M07: Account Settings Screen (3 SP)
**PRD ID:** PR-14  
**URS Ref:** FR-PROFILE-003  
**SRS Ref:** SRS-PROF-001  

> **📍 Code Status:** 🟡 Partial (placeholder)
> - `src/screens/SettingsScreen.tsx` exists as placeholder
> - Password change, logout, delete account functionality needed

**Scope:**
- [ ] Create AccountSettingsScreen
- [ ] Add change password option
- [ ] Add logout functionality
- [ ] Add delete account placeholder (Phase 4)

**Files:** `src/screens/AccountSettingsScreen.tsx`, `src/navigation/AppNavigator.tsx`

---

#### S1-M08: List Card UI Component (1 SP)
**PRD ID:** LI-01  
**URS Ref:** FR-LIST-001  
**SRS Ref:** SRS-LIST-001  

> **📍 Code Status:** ✅ Done
> - `src/components/ListCard.tsx` implemented
> - `src/components/ListCardSkeleton.tsx` for loading state
> - May need minor polish/consistency fixes

**Scope:**
- [x] Create reusable ListCard component
- [x] Display cover image, title, place count, engagement metrics
- [x] Handle loading/error states
- [ ] Optimize image loading (review needed)

**Files:** `src/components/ListCard.tsx`, `src/components/ListCardSkeleton.tsx`

---

#### S1-M09: List UI Consistency (3 SP)
**PRD ID:** LI-02  
**URS Ref:** FR-LIST-001  
**SRS Ref:** SRS-LIST-001  
**Scope:**
- [ ] Standardize list display across Home, Search, Profile
- [ ] Consistent spacing, typography, colors
- [ ] Apply design system tokens

**Files:** `src/screens/HomeScreenNew.tsx`, `src/screens/SearchScreen.tsx`, `src/screens/ProfileScreen.tsx`

---

#### S1-M10: List Detail Page (1 SP)
**PRD ID:** LI-03  
**URS Ref:** FR-LIST-001  
**SRS Ref:** SRS-LIST-001  

> **📍 Code Status:** ✅ Done
> - `src/screens/ListDetailScreen.tsx` implemented
> - Tabs at `:120`, share handler at `:420`, like/save states at `:117`
> - May need minor polish

**Scope:**
- [x] Implement full list detail screen
- [x] Header with cover, title, author, stats
- [x] Tab navigation (Places, Map, Comments)
- [x] Action buttons (Like, Save, Share)
- [ ] Pull-to-refresh (verify implementation)

**Files:** `src/screens/ListDetailScreen.tsx`, `src/components/ListHeader.tsx`

---

#### S1-M11: List Cover Image (1 SP)
**PRD ID:** LI-04  
**URS Ref:** FR-LIST-007  
**SRS Ref:** SRS-LIST-009, SRS-LIST-010  

> **📍 Code Status:** ✅ Done
> - `src/screens/CreateListScreen.tsx` has cover image upload at `:147`
> - Visibility toggle at `:351`
> - `src/components/EditListModal.tsx` has cover image update at `:204`

**Scope:**
- [x] Auto-select from first place with image
- [x] Allow custom upload
- [ ] Image cropping/resizing (verify)

**Files:** `src/components/EditListModal.tsx`, `src/services/imageStorageService.ts`

---

#### S1-M12: Create New List Flow (1 SP)
**PRD ID:** LI-08  
**URS Ref:** FR-LIST-001  
**SRS Ref:** SRS-LIST-001, SRS-LIST-002  

> **📍 Code Status:** ✅ Done
> - `src/screens/CreateListScreen.tsx` implemented
> - Cover image upload, visibility toggle, loading states

**Scope:**
- [x] Create list modal/screen
- [x] Title and description input
- [x] Visibility selector
- [x] Create button with loading state

**Files:** `src/screens/CreateListScreen.tsx`, `src/services/firestoreService.ts`

---

#### S1-M13: Add Place via Search (1 SP)
**PRD ID:** LI-09  
**URS Ref:** FR-LIST-004  
**SRS Ref:** SRS-LIST-007, SRS-PLACE-003  

> **📍 Code Status:** ✅ Done
> - `src/components/AddPlaceModal.tsx` implemented
> - Uses `src/services/placesService.ts` for Google Places search

**Scope:**
- [x] Google Places search integration
- [x] Place preview before adding
- [x] Add to list confirmation
- [ ] Handle duplicates (verify)

**Files:** `src/components/AddPlaceModal.tsx`, `src/services/placesService.ts`

---

#### S1-M14: Edit List Flow (1 SP)
**PRD ID:** LI-11  
**URS Ref:** FR-LIST-002  
**SRS Ref:** SRS-LIST-005  

> **📍 Code Status:** ✅ Done
> - `src/components/EditListModal.tsx` implemented
> - Cover image update at `:204`

**Scope:**
- [x] Edit list modal
- [x] Update title, description, visibility
- [x] Change cover image
- [ ] Save with optimistic update (verify)

**Files:** `src/components/EditListModal.tsx`, `src/services/firestoreService.ts`

---

#### S1-M15: Reorder Places (Drag & Drop) (5 SP)
**PRD ID:** LI-13  
**URS Ref:** FR-LIST-012  
**SRS Ref:** SRS-LIST-019  

> **📍 Code Status:** 🔴 Not implemented
> - `src/components/DraggablePlaceList.tsx` does not exist
> - Net-new UI + gesture handling + persistence logic

**Scope:**
- [ ] Implement drag-and-drop reordering
- [ ] Visual feedback during drag
- [ ] Persist new order to Firestore
- [ ] Handle edge cases (first/last item)

**Files:** `src/screens/ListDetailScreen.tsx`, `src/components/DraggablePlaceList.tsx`

---

#### S1-M16: List Page - Places Tab (1 SP)
**PRD ID:** LI-16  
**URS Ref:** FR-LIST-001  
**SRS Ref:** SRS-LIST-007  

> **📍 Code Status:** ✅ Done
> - Part of `src/screens/ListDetailScreen.tsx`
> - Places list rendering implemented

**Scope:**
- [x] Display places in scrollable list
- [x] Place card with image, name, category, rating
- [x] Tap to open place detail
- [ ] Swipe to remove (owner only) - verify

**Files:** `src/screens/ListDetailScreen.tsx`, `src/components/PlacesList.tsx`

---

#### S1-M17: List Page - Map Tab (1 SP)
**PRD ID:** LI-17  
**URS Ref:** FR-MAP-001  
**SRS Ref:** SRS-MAP-001, SRS-MAP-008  

> **📍 Code Status:** ✅ Done
> - Part of `src/screens/ListDetailScreen.tsx`
> - Map tab implemented with markers

**Scope:**
- [x] Map view of list places
- [x] Fit bounds to show all markers
- [x] Tap marker to show place info
- [x] Toggle between list/map views

**Files:** `src/screens/ListDetailScreen.tsx`, `src/components/ListMapView.tsx`

---

### Fullstack Track

#### S1-F01: Welcome Email System (5 SP)
**PRD ID:** ON-07  
**URS Ref:** FR-AUTH-001  
**SRS Ref:** SRS-AUTH-015  

> **📍 Code Status:** 🔴 Not implemented
> - `Web & Backend/server/emailService.ts` does not exist
> - Net-new infra (SendGrid/Firebase Extensions) + template + trigger

**Scope:**
- [ ] Set up email service (SendGrid/Firebase Extensions)
- [ ] Create welcome email template
- [ ] Trigger on user registration
- [ ] Include app deep link

**Files:** `Web & Backend/server/emailService.ts`, Firebase Extensions config

---

#### S1-F02: PostHog Integration (1 SP)
**PRD ID:** AN-01  
**URS Ref:** FR-ANALYTICS-001  
**SRS Ref:** SRS-ANAL-001  

> **📍 Code Status:** ✅ Done
> - Mobile: `PostHogProvider` configured at `src/navigation/AppNavigator.tsx:275`
> - Web: `Web & Backend/client/src/lib/posthog.ts`
> - Backend: `Web & Backend/server/posthog-service.ts`

**Scope:**
- [x] Install PostHog SDK (mobile + web)
- [x] Configure project API key
- [x] Initialize on app start
- [ ] Verify events in PostHog dashboard

**Files:** `src/services/analyticsService.ts`, `Web & Backend/client/src/analytics.ts`

---

#### S1-F03: Core Event Tracking (5 SP)
**PRD ID:** AN-02  
**URS Ref:** FR-ANALYTICS-002  
**SRS Ref:** SRS-ANAL-002  
**Scope:**
- [ ] Track: signup, login, list_created, place_added, import_started
- [ ] Track: list_viewed, place_viewed, search_performed
- [ ] Track: follow, save, like, comment
- [ ] Add event properties (list_id, place_id, etc.)

**Files:** `src/services/analyticsService.ts`, all screens with user actions

---

#### S1-F04: User Properties Setup (3 SP)
**PRD ID:** AN-03  
**URS Ref:** FR-ANALYTICS-003  
**SRS Ref:** SRS-ANAL-003  
**Scope:**
- [ ] Set user properties: signup_date, lists_count, followers_count
- [ ] Update properties on relevant actions
- [ ] Identify users after login

**Files:** `src/services/analyticsService.ts`, `src/services/authService.ts`

---

#### S1-F05: Funnel Analytics Setup (3 SP)
**PRD ID:** AN-04  
**URS Ref:** FR-ANALYTICS-004  
**SRS Ref:** SRS-ANAL-004  
**Scope:**
- [ ] Define signup funnel in PostHog
- [ ] Define activation funnel (signup → first list → first place)
- [ ] Create retention cohorts
- [ ] Set up weekly dashboard

**Files:** PostHog dashboard configuration

---

#### S1-F06: Custom Location Handling (5 SP)
**PRD ID:** IM-14  
**URS Ref:** FR-IMPORT-010  
**SRS Ref:** SRS-IMP-022  
**Scope:**
- [ ] Detect places not found in Google Places
- [ ] Allow user to pin custom location on map
- [ ] Create custom place entry with `source: 'custom'`
- [ ] Handle custom places in place displays

**Files:** `Web & Backend/server/firebase-poller.ts`, `src/services/placesService.ts`

---

#### S1-F07: Closed Place Detection (3 SP)
**PRD ID:** IM-15  
**URS Ref:** FR-IMPORT-011  
**SRS Ref:** SRS-IMP-023  
**Scope:**
- [ ] Check `businessStatus` during enrichment
- [ ] Flag permanently closed places
- [ ] Show warning UI for closed places
- [ ] Option to exclude from import

**Files:** `Web & Backend/server/firebase-poller.ts`, `Web & Backend/server/routes.ts`

---

#### S1-F08: Search Sort - Relevance (1 SP)
**PRD ID:** SL-05  
**URS Ref:** FR-DISCOVERY-002  
**SRS Ref:** SRS-SEARCH-004  

> **📍 Code Status:** ✅ Done
> - Basic relevance scoring in `FirestoreService.searchLists()` at `src/services/firestoreService.ts:2217`

**Scope:**
- [x] Implement relevance scoring algorithm
- [x] Consider text match, engagement, recency
- [x] Apply as default sort

**Files:** `src/services/firestoreService.ts`, `Web & Backend/server/firebase-service.ts`

---

#### S1-F09: Search Sort - Top (2 SP)
**PRD ID:** SL-06  
**URS Ref:** FR-DISCOVERY-002  
**SRS Ref:** SRS-SEARCH-004  
**Scope:**
- [ ] Sort by engagement score (saves + likes + views)
- [ ] Add sort option to search UI

**Files:** `src/services/firestoreService.ts`, `src/screens/SearchScreen.tsx`

---

#### S1-F10: Search Sort - Newest (1 SP)
**PRD ID:** SL-07  
**URS Ref:** FR-DISCOVERY-002  
**SRS Ref:** SRS-SEARCH-004  

> **📍 Code Status:** ✅ Done
> - Implemented as `sortBy === 'recent'` with label "Newest" in `src/screens/SearchScreen.tsx`

**Scope:**
- [x] Sort by createdAt descending
- [x] Add sort option to search UI

**Files:** `src/services/firestoreService.ts`, `src/screens/SearchScreen.tsx`

---

#### S1-F11: Search Sort - Closest (3 SP)
**PRD ID:** SL-08  
**URS Ref:** FR-DISCOVERY-004  
**SRS Ref:** SRS-SEARCH-007  
**Scope:**
- [ ] Calculate distance using Haversine formula
- [ ] Sort by proximity to user location
- [ ] Handle missing user location gracefully

**Files:** `src/services/firestoreService.ts`, `src/utils/geoUtils.ts`

---

#### S1-F12: Push Notification - New Follower (5 SP)
**PRD ID:** NO-09  
**URS Ref:** FR-NOTIF-002  
**SRS Ref:** SRS-SOC-010  

> **📍 Code Status:** 🔴 Not implemented
> - Poller only processes `messages` collection (`Web & Backend/server/firebase-service.ts:1275`)
> - Follows are written to `follows` collection (`src/services/firestoreService.ts:927`)
> - Requires new notification pipeline: either create `messages` docs on follow, or expand poller

**Scope:**
- [ ] Trigger notification on follow action
- [ ] Create notification document
- [ ] Send via Expo Push API
- [ ] Include follower name and avatar

**Files:** `Web & Backend/server/notification-poller.ts`, `src/services/firestoreService.ts`

---

#### S1-F13: Push Notification - List Saved (3 SP)
**PRD ID:** NO-10  
**URS Ref:** FR-NOTIF-002  
**SRS Ref:** SRS-SOC-011  

> **📍 Code Status:** 🔴 Not implemented
> - Saves written to `saves` collection (`src/services/firestoreService.ts:1018`)
> - Requires notification pipeline expansion (see S1-F12)

**Scope:**
- [ ] Trigger notification on save action
- [ ] Include list title and saver name
- [ ] Respect notification preferences

**Files:** `Web & Backend/server/notification-poller.ts`

---

#### S1-F14: Push Notification - List Liked (3 SP)
**PRD ID:** NO-11  
**URS Ref:** FR-NOTIF-002  
**SRS Ref:** SRS-SOC-014  

> **📍 Code Status:** 🔴 Not implemented
> - Likes written to `likes` collection
> - Requires notification pipeline expansion (see S1-F12)

**Scope:**
- [ ] Trigger notification on like action
- [ ] Include list title and liker name
- [ ] Batch multiple likes (optional)

**Files:** `Web & Backend/server/notification-poller.ts`

---

#### S1-F15: Push Notification - Comment (3 SP)
**PRD ID:** NO-12  
**URS Ref:** FR-NOTIF-002  
**SRS Ref:** SRS-SOC-012  

> **📍 Code Status:** 🔴 Not implemented
> - Requires notification pipeline expansion (see S1-F12)

**Scope:**
- [ ] Trigger notification on comment
- [ ] Include comment preview text
- [ ] Link to list detail

**Files:** `Web & Backend/server/notification-poller.ts`

---

#### S1-F16: Push Notification - DM (2 SP)
**PRD ID:** NO-13  
**URS Ref:** FR-NOTIF-002  
**SRS Ref:** SRS-MSG-007  

> **📍 Code Status:** 🟡 Partial
> - DM messages created with `notificationSent: false` + `type: 'new_message'` (`src/services/dmService.ts:29`)
> - Message sending mostly in place
> - Missing: deep-link payload for navigation

**Scope:**
- [x] Trigger notification on new message
- [x] Include sender name and preview
- [ ] Link to conversation (deep link needed)

**Files:** `Web & Backend/server/notification-poller.ts`

---

#### S1-F17: Notification Deep Links (3 SP)
**PRD ID:** NO-17  
**URS Ref:** FR-WEB-004  
**SRS Ref:** SRS-NOTIF-006, SRS-NOTIF-007  
**Scope:**
- [ ] Include data payload in push notifications
- [ ] Handle notification tap with deep link routing
- [ ] Navigate to correct screen based on type

**Files:** `src/services/pushNotificationService.ts`, `src/navigation/AppNavigator.tsx`

---

#### S1-F18: Share Preview (OG Tags) (8 SP)
**PRD ID:** SH-03  
**URS Ref:** FR-WEB-005  
**SRS Ref:** SRS-WEB-009  

> **📍 Code Status:** 🔴 Not implemented
> - `Web & Backend/client/src/pages/ListPreview.tsx` does not exist
> - No OG meta rendering route present
> - Requires SSR or dynamic meta tag injection

**Scope:**
- [ ] Generate Open Graph meta tags for lists
- [ ] Generate Twitter Card meta tags
- [ ] Dynamic image generation for share cards
- [ ] Test with social media debuggers

**Files:** `Web & Backend/client/src/pages/ListPreview.tsx`, `Web & Backend/server/routes.ts`

---

#### S1-F19: Deferred Discovery Bugs (5 SP)
**Linked Bugs:** U-17 (Country Search), U-18 (Local Content)  
**Note:** These were moved from Sprint 0 but should be addressed early in Sprint 1 as they impact search quality.  

> **📍 Code Status:** 🔴 Not implemented
> - U-18: `createList()` in `src/services/firestoreService.ts:147` does NOT populate `city/country/geohash/lat/lon`
> - Importer DOES populate geo fields (`Web & Backend/server/firebase-poller.ts:545`)
> - Gap: manually created lists have no geo data

**Scope:**
- [ ] U-17: Tighten country search query filter (empty city check)
- [ ] U-18: Add geo field population at createList/updateList

**Files:** `src/services/firestoreService.ts`, `src/services/placesService.ts`

---

## 🧪 QA Track (40 SP)

### QA Task List

| Task ID | Task Name | Covers Dev Tasks | SP | Priority | Start After |
|---------|-----------|------------------|---:|:--------:|-------------|
| S1-Q01 | **Auth Flow Testing** | S1-M01, S1-M02, S1-M03, S1-M04 | 5 | 🔴 P0 | Day 3 |
| S1-Q02 | **List CRUD Testing** | S1-M08, S1-M10, S1-M12, S1-M14 | 5 | 🔴 P0 | Day 5 |
| S1-Q03 | **Place Management Testing** | S1-M13, S1-M15, S1-M16 | 4 | 🔴 P0 | Day 6 |
| S1-Q04 | **Map View Testing** | S1-M17 | 2 | 🟠 P1 | Day 6 |
| S1-Q05 | **Analytics Verification** | S1-F02, S1-F03, S1-F04, S1-F05 | 3 | 🟠 P1 | Day 4 |
| S1-Q06 | **Import Pipeline Testing** | S1-F06, S1-F07 | 3 | 🔴 P0 | Day 5 |
| S1-Q07 | **Search Sort Testing** | S1-F08, S1-F09, S1-F10, S1-F11 | 3 | 🟠 P1 | Day 6 |
| S1-Q08 | **Push Notification Testing** | S1-F12→S1-F17 | 5 | 🔴 P0 | Day 7 |
| S1-Q09 | **Share Preview Testing** | S1-F18 | 2 | 🟠 P1 | Day 6 |
| S1-Q10 | **Cross-Platform Regression** | All | 5 | 🔴 P0 | Day 7 |
| S1-Q11 | **Bug Fix Verification** | S1-F19 (U-17, U-18) | 2 | 🟠 P1 | Day 5 |
| S1-Q12 | **Sprint 0 Regression** | S0-* | 1 | 🟡 P2 | Day 7 |
| | **TOTAL** | | **40 SP** | | |

### QA Task Details

#### S1-Q01: Auth Flow Testing (5 SP)
**Covers:** S1-M01, S1-M02, S1-M03, S1-M04
**Test Scenarios:**
- [ ] Google SSO: iOS + Android fresh install
- [ ] Google SSO: Existing account linking
- [ ] Apple SSO: iOS fresh install
- [ ] Apple SSO: Handle "Hide My Email" flow
- [ ] Password reset: Email delivery + reset flow
- [ ] Progressive profiling: All steps complete
- [ ] Progressive profiling: Resume interrupted flow
- [ ] Error handling: Invalid credentials, network errors

**Platforms:** iOS (iPhone 12+), Android (Pixel, Samsung)

---

#### S1-Q02: List CRUD Testing (5 SP)
**Covers:** S1-M08, S1-M10, S1-M12, S1-M14
**Test Scenarios:**
- [ ] Create list: Minimum fields (title only)
- [ ] Create list: All fields populated
- [ ] Edit list: Title, description, visibility
- [ ] Delete list: Confirm cascade (comments, saves)
- [ ] List card: Display all states (loading, error, empty)
- [ ] List detail: Header, stats, action buttons
- [ ] Cover image: Auto-select + custom upload
- [ ] Offline: Create list while offline → sync on reconnect

**Data Conditions:** Empty list, 1 place, 10 places, 100+ places

---

#### S1-Q03: Place Management Testing (4 SP)
**Covers:** S1-M13, S1-M15, S1-M16
**Test Scenarios:**
- [ ] Add place: Google Places search
- [ ] Add place: Handle duplicates
- [ ] Add place: Custom location (from import)
- [ ] Reorder places: Drag to first/last position
- [ ] Reorder places: Persist after reload
- [ ] Remove place: Swipe to delete
- [ ] Places tab: Scroll performance with 50+ places

---

#### S1-Q04: Map View Testing (2 SP)
**Covers:** S1-M17
**Test Scenarios:**
- [ ] Map displays all markers
- [ ] Fit bounds: All places visible
- [ ] Marker tap: Info popup
- [ ] Toggle: List ↔ Map view
- [ ] Empty state: No places

---

#### S1-Q05: Analytics Verification (3 SP)
**Covers:** S1-F02, S1-F03, S1-F04, S1-F05
**Test Scenarios:**
- [ ] PostHog events: Verify in dashboard
- [ ] Core events: signup, login, list_created, etc.
- [ ] User properties: Correct values after actions
- [ ] Funnel: Check conversion tracking

**Tools:** PostHog dashboard, network inspector

---

#### S1-Q06: Import Pipeline Testing (3 SP)
**Covers:** S1-F06, S1-F07
**Test Scenarios:**
- [ ] Custom location: Manual pin on map
- [ ] Custom location: Saved correctly
- [ ] Closed place: Warning displayed
- [ ] Closed place: Exclude option works
- [ ] Import with mixed valid/closed places

---

#### S1-Q07: Search Sort Testing (3 SP)
**Covers:** S1-F08, S1-F09, S1-F10, S1-F11
**Test Scenarios:**
- [ ] Sort: Relevance (default)
- [ ] Sort: Top (engagement-based)
- [ ] Sort: Newest (by date)
- [ ] Sort: Closest (by distance)
- [ ] Sort: Persistence across sessions
- [ ] Empty results: Each sort option

---

#### S1-Q08: Push Notification Testing (5 SP)
**Covers:** S1-F12, S1-F13, S1-F14, S1-F15, S1-F16, S1-F17
**Test Scenarios:**
- [ ] New follower: Notification received
- [ ] List saved: Notification received
- [ ] List liked: Notification received
- [ ] Comment: Notification received
- [ ] DM: Notification received
- [ ] Deep link: Tap opens correct screen
- [ ] Notification preferences: Respect opt-out
- [ ] Background/foreground: Both states

**Platforms:** iOS (permission prompt), Android (channels)

---

#### S1-Q09: Share Preview Testing (2 SP)
**Covers:** S1-F18
**Test Scenarios:**
- [ ] OG tags: Facebook debugger
- [ ] Twitter cards: Twitter validator
- [ ] WhatsApp: Preview image + title
- [ ] iMessage: Preview rendering

**Tools:** Facebook Sharing Debugger, Twitter Card Validator

---

#### S1-Q10: Cross-Platform Regression (5 SP)
**Covers:** All Sprint 1 features
**Test Scenarios:**
- [ ] Full user journey: Signup → Create list → Add places → Share
- [ ] iOS: iPhone 12, iPhone 14 Pro, iPad
- [ ] Android: Pixel 7, Samsung S23, Low-end device
- [ ] Network: WiFi, 4G, Offline → Online
- [ ] Memory: Background/foreground cycling

---

#### S1-Q11: Bug Fix Verification (2 SP)
**Covers:** S1-F19 (U-17, U-18)
**Test Scenarios:**
- [ ] U-17: Country search returns accurate results
- [ ] U-17: Empty city doesn't match
- [ ] U-18: New lists appear in "near me"
- [ ] U-18: Edited lists update geo fields

---

#### S1-Q12: Sprint 0 Regression (1 SP)
**Covers:** S0-* tasks
**Test Scenarios:**
- [ ] Security rules: Unauthorized access blocked
- [ ] Data validation: Invalid data rejected
- [ ] Query performance: No regressions
- [ ] Write operations: Data integrity maintained

---

## 🗓️ Sprint 1 Execution Order

```
Week 1 (Days 1-3): Auth & Analytics Foundation
├── Mobile: S1-M01 (Google SSO) ──────────────────────── 5 SP
├── Mobile: S1-M02 (Apple SSO) ───────────────────────── 5 SP
├── Mobile: S1-M03 (Password Reset) ──────────────────── 2 SP
├── Mobile: S1-M04 (Progressive Profiling) ───────────── 3 SP
├── Fullstack: S1-F02 (PostHog Integration) ──────────── 3 SP
├── Fullstack: S1-F03 (Core Event Tracking) ──────────── 5 SP
├── Fullstack: S1-F04 (User Properties) ──────────────── 3 SP
└── Fullstack: S1-F01 (Welcome Email) ────────────────── 3 SP
                                    Mobile: 15 SP | Fullstack: 14 SP

Week 1 (Days 4-5): Lists Foundation
├── Mobile: S1-M08 (List Card UI) ────────────────────── 3 SP
├── Mobile: S1-M09 (List UI Consistency) ─────────────── 3 SP
├── Mobile: S1-M10 (List Detail Page) ────────────────── 5 SP
├── Mobile: S1-M12 (Create List) ─────────────────────── 3 SP
├── Fullstack: S1-F05 (Funnel Analytics) ─────────────── 3 SP
├── Fullstack: S1-F06 (Custom Location) ──────────────── 5 SP
├── Fullstack: S1-F07 (Closed Place Detection) ───────── 3 SP
└── Fullstack: S1-F19 (Deferred Bugs) ────────────────── 3 SP
                                    Mobile: 14 SP | Fullstack: 14 SP

Week 2 (Days 1-3): Lists Features & Search
├── Mobile: S1-M05 (Default Privacy) ─────────────────── 2 SP
├── Mobile: S1-M06 (First-Time Experience) ───────────── 3 SP
├── Mobile: S1-M07 (Account Settings) ────────────────── 3 SP
├── Mobile: S1-M13 (Add Place via Search) ────────────── 3 SP
├── Mobile: S1-M14 (Edit List) ───────────────────────── 3 SP
├── Mobile: S1-M15 (Reorder Places) ──────────────────── 5 SP
├── Fullstack: S1-F08 (Sort: Relevance) ──────────────── 3 SP
├── Fullstack: S1-F09 (Sort: Top) ────────────────────── 2 SP
├── Fullstack: S1-F10 (Sort: Newest) ─────────────────── 2 SP
├── Fullstack: S1-F11 (Sort: Closest) ────────────────── 3 SP
└── Fullstack: S1-F18 (Share Preview) ────────────────── 5 SP
                                    Mobile: 19 SP | Fullstack: 15 SP

Week 2 (Days 4-5): Notifications & Polish
├── Mobile: S1-M11 (List Cover Image) ────────────────── 2 SP
├── Mobile: S1-M16 (Places Tab) ──────────────────────── 3 SP
├── Mobile: S1-M17 (Map Tab) ─────────────────────────── 3 SP
├── Fullstack: S1-F12 (Push: Follower) ───────────────── 3 SP
├── Fullstack: S1-F13 (Push: Saved) ──────────────────── 2 SP
├── Fullstack: S1-F14 (Push: Liked) ──────────────────── 2 SP
├── Fullstack: S1-F15 (Push: Comment) ────────────────── 2 SP
├── Fullstack: S1-F16 (Push: DM) ─────────────────────── 2 SP
└── Fullstack: S1-F17 (Deep Links) ───────────────────── 3 SP
                                    Mobile: 8 SP | Fullstack: 14 SP

                              TOTAL: Mobile 56 SP | Fullstack 57 SP

QA Testing (starts Day 3, parallel with dev):
├── Day 3-4: S1-Q01 (Auth Flow) ───────────────────────── 5 SP
├── Day 4-5: S1-Q05 (Analytics Verification) ──────────── 3 SP
├── Day 5-6: S1-Q02 (List CRUD) ───────────────────────── 5 SP
├── Day 5-6: S1-Q06 (Import Pipeline) ─────────────────── 3 SP
├── Day 5-6: S1-Q11 (Bug Fix Verification) ────────────── 2 SP
├── Day 6-7: S1-Q03 (Place Management) ────────────────── 4 SP
├── Day 6-7: S1-Q04 (Map View) ────────────────────────── 2 SP
├── Day 6-7: S1-Q07 (Search Sort) ─────────────────────── 3 SP
├── Day 6-7: S1-Q09 (Share Preview) ───────────────────── 2 SP
├── Day 7:   S1-Q08 (Push Notifications) ──────────────── 5 SP
├── Day 7:   S1-Q10 (Cross-Platform Regression) ───────── 5 SP
└── Day 7:   S1-Q12 (Sprint 0 Regression) ─────────────── 1 SP
                                                   QA: 40 SP
```

---

## 📁 Jira Epic Structure (Post-Audit)

```
📁 Epic: Sprint 1 - Auth + Lists + Import Foundation
│
├── 📂 Auth & Onboarding (Mobile) [27 SP net-new]
│   ├── 📋 S1-M01: Google SSO Implementation [8 SP] 🟡
│   ├── 📋 S1-M02: Apple SSO Implementation [8 SP] 🔴
│   ├── 📋 S1-M03: Password Reset Flow [3 SP] 🔴
│   ├── 📋 S1-M04: Progressive Profiling [3 SP] 🟡
│   ├── 📋 S1-M05: Default Privacy Setting [2 SP] 🔴
│   ├── 📋 S1-M06: First-Time Experience [3 SP] 🔴
│   └── 📋 S1-M07: Account Settings Screen [3 SP] 🟡
│
├── 📂 List Management (Mobile) [11 SP net-new]
│   ├── 📋 S1-M08: List Card UI Component [1 SP] ✅
│   ├── 📋 S1-M09: List UI Consistency [3 SP] 🟡
│   ├── 📋 S1-M10: List Detail Page [1 SP] ✅
│   ├── 📋 S1-M11: List Cover Image [1 SP] ✅
│   ├── 📋 S1-M12: Create New List Flow [1 SP] ✅
│   ├── 📋 S1-M13: Add Place via Search [1 SP] ✅
│   ├── 📋 S1-M14: Edit List Flow [1 SP] ✅
│   ├── 📋 S1-M15: Reorder Places [5 SP] 🔴
│   ├── 📋 S1-M16: List Page - Places Tab [1 SP] ✅
│   └── 📋 S1-M17: List Page - Map Tab [1 SP] ✅
│
├── 📂 Analytics & Tracking (Fullstack) [14 SP]
│   ├── 📋 S1-F01: Welcome Email System [5 SP] 🔴
│   ├── 📋 S1-F02: PostHog Integration [1 SP] ✅
│   ├── 📋 S1-F03: Core Event Tracking [5 SP] 🟡
│   ├── 📋 S1-F04: User Properties Setup [3 SP] 🟡
│   └── 📋 S1-F05: Funnel Analytics Setup [3 SP] 🔴
│
├── 📂 Import Enhancements (Fullstack) [8 SP]
│   ├── 📋 S1-F06: Custom Location Handling [5 SP] 🔴
│   └── 📋 S1-F07: Closed Place Detection [3 SP] 🔴
│
├── 📂 Search Improvements (Fullstack) [7 SP]
│   ├── 📋 S1-F08: Search Sort - Relevance [1 SP] ✅
│   ├── 📋 S1-F09: Search Sort - Top [2 SP] 🔴
│   ├── 📋 S1-F10: Search Sort - Newest [1 SP] ✅
│   └── 📋 S1-F11: Search Sort - Closest [3 SP] 🔴
│
├── 📂 Notifications (Fullstack) [19 SP]
│   ├── 📋 S1-F12: Push - New Follower [5 SP] 🔴
│   ├── 📋 S1-F13: Push - List Saved [3 SP] 🔴
│   ├── 📋 S1-F14: Push - List Liked [3 SP] 🔴
│   ├── 📋 S1-F15: Push - Comment [3 SP] 🔴
│   ├── 📋 S1-F16: Push - DM [2 SP] 🟡
│   └── 📋 S1-F17: Notification Deep Links [3 SP] 🔴
│
├── 📂 Web & Sharing (Fullstack) [8 SP] ⚠️ Consider Sprint 2
│   └── 📋 S1-F18: Share Preview (OG Tags) [8 SP] 🔴
│
└── 📂 Bug Fixes (Fullstack) [5 SP]
    └── 📋 S1-F19: Deferred Discovery Bugs [5 SP] 🔴
        ├── 🐛 U-17: Country Search Inaccurate
        └── 🐛 U-18: Local Content Algorithm

📂 QA Testing [40 SP]
├── 📋 S1-Q01: Auth Flow Testing [5 SP]
├── 📋 S1-Q02: List CRUD Testing [5 SP]
├── 📋 S1-Q03: Place Management Testing [4 SP]
├── 📋 S1-Q04: Map View Testing [2 SP]
├── 📋 S1-Q05: Analytics Verification [3 SP]
├── 📋 S1-Q06: Import Pipeline Testing [3 SP]
├── 📋 S1-Q07: Search Sort Testing [3 SP]
├── 📋 S1-Q08: Push Notification Testing [5 SP]
├── 📋 S1-Q09: Share Preview Testing [2 SP]
├── 📋 S1-Q10: Cross-Platform Regression [5 SP]
├── 📋 S1-Q11: Bug Fix Verification [2 SP]
└── 📋 S1-Q12: Sprint 0 Regression [1 SP]
```

---

## ✅ Sprint 1 Exit Criteria

### Must Have (P0)
- [ ] Google SSO working on iOS + Android
- [ ] Apple SSO working on iOS
- [ ] Users can create, edit, and delete lists
- [ ] Users can add/remove places from lists
- [ ] List detail page displays places and map
- [ ] Push notification infrastructure operational
- [ ] Analytics tracking core events

### Should Have (P1)
- [ ] Progressive profiling flow complete
- [ ] All search sort options functional
- [ ] Welcome email sending
- [ ] Share previews with OG tags

### Nice to Have (P2)
- [ ] Drag-and-drop reorder polished
- [ ] First-time experience tooltips
- [ ] Custom location import handling

---

## 📊 Owner Distribution (Post-Audit)

| Owner | Tasks | Audited SP | Net-New SP | % of Sprint |
|-------|------:|:----------:|:----------:|:-----------:|
| Mobile | 17 | 46 SP | 38 SP | 31% |
| Fullstack | 19 | 64 SP | 58 SP | 42% |
| QA | 12 | 40 SP | 40 SP | 27% |
| **Total** | **48** | **150 SP** | **136 SP** | 100% |

---

## 📝 Document History

| Date | Version | Changes |
|------|---------|---------|
| Dec 24, 2025 | 1.3 | **Integrated code-based estimation review** into task tables and details. Added Status column, Code Status notes, and adjusted SP estimates based on repo audit. Removed separate audit section. |
| Dec 24, 2025 | 1.2 | Added code-based SP audit vs current repo implementation (high-confidence "done" items + under/over estimation notes). |
| Dec 24, 2025 | 1.1 | Added QA track with 12 tasks (40 SP). Total sprint effort now 152 SP across 48 tasks. |
| Dec 24, 2025 | 1.0 | Initial Sprint 1 document with 36 tasks (17 Mobile, 19 Fullstack), 112 SP total. Added task details, execution order, epic structure. |
