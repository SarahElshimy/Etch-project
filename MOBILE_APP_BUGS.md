# Etch Mobile App - Bug Tracker

**Version:** 1.1  
**Last Updated:** Dec 24, 2025  
**Status:** Active  
**Reference:** See also [MOBILE_APP_STATUS.md](../MOBILE_APP_STATUS.md) for full context.
**Developed by** : Technical Product Owner , (Human)

**Sources:**
- Internal code review and technical analysis
- Client Bug Tracker CSV (Bug_Enhancement Tracker - Bugs.csv)

---

## 📐 Effort Estimation Key

| Story Points | Hours | Complexity | Example |
|-------------|-------|------------|---------|
| 1 SP | 1 hr | Trivial | Config change, text fix |
| 2 SP | 2 hrs | Simple | Single file fix, minor UI |
| 3 SP | 3 hrs | Small | Multi-file fix, testing |
| 5 SP | 5 hrs | Medium | Feature fix, debugging |
| 8 SP | 8 hrs (1 day) | Complex | Multiple services, refactor |
| 13 SP | 13 hrs (~2 days) | Large | Major system fix |
| 21 SP | 21 hrs (~3 days) | X-Large | Architectural fix |
| 34 SP | 34 hrs (~4 days) | Critical | System overhaul |

**Definition:** 1 SP = 1 hour of mid-level developer effort

---

## 🚨 Blocker Priority (User-Reported)

> **Source:** Client Bug Tracker - User Testing Feedback

### U-15. Disappearing Places
- **Status:** Failed Test
- **Reported:** Nov 24, 2025 by Khaled
- **Impact:** Blocker - Users losing data
- **Location (Mobile):**
  - `src/screens/ListDetailScreen.tsx` (handleAddLocation, PlacesList)
  - `src/components/AddPlaceModal.tsx` (onAddPlace callback)
  - `src/services/firestoreService.ts` (createList, updateList, getList)
  - `src/services/placesService.ts` (getPlacesByIds, upsertPlace, batchGetPlaces)
  - `src/screens/CreateListScreen.tsx` (initial list creation)
  - `src/store/slices/listsSlice.ts` (setSelectedList, hydration)
  - `src/types/index.ts` (List interface: locations[] vs locationIds[])
- **Location (Backend):**
  - `Web & Backend/server/firebase-service.ts` (updateList, getList, createList)
  - `Web & Backend/server/firebase-poller.ts` (place upsert during imports)
  - `Web & Backend/shared/schema.ts` (firebaseListSchema, listSchema)
- **Location (Scripts):**
  - `scripts/cleanInvalidPlaceIds.js` (may remove valid place IDs)
  - `src/scripts/cleanupDuplicates.ts` (deletes imported duplicate lists)
- **Screens:** `src/screens/ListDetailScreen.tsx`, `src/screens/CreateListScreen.tsx`
- **SP Estimate:** 13 SP (database sync investigation + fix + data recovery)
- **Description:** Multiple users report that places they have uploaded in their lists are disappearing. This applies to new lists being created after the update and lists that were already created and migrated from MapGallery.
- **Root Cause Analysis:**
  - `updateList` rebuilds `locationIds` from upsert results (non-incremental)
  - If list in Redux is not hydrated, updates overwrite with only new place
  - Dual storage pattern: `locations[]` embedded + `locationIds[]` references causes mismatch
- **Steps to Reproduce:**
  1. Create a new list
  2. Search London and add London as a place
  3. See "Adding Place" notification loader
  4. Directed to an empty list page
- **Developer Notes:** Should be fixed in next build

### U-21. App Crashes After Creating New User (iPhone)
- **Status:** 🟡 Fixed - Pending QA Verification
- **Reported:** Nov 28, 2025 by Fawaz
- **Fixed:** Dec 24, 2025
- **Impact:** Blocker - Cannot complete onboarding
- **Platform:** iPhone
- **Location (Mobile - Auth Flow):**
  - `src/screens/auth/WelcomeScreen.tsx` (initial auth + social auth)
  - `src/screens/auth/SignUpEmailScreen.tsx` (navigation to password)
  - `src/screens/auth/SignUpPasswordScreen.tsx` (navigation to birthday)
  - `src/screens/auth/SignUpBirthdayScreen.tsx` (navigation to profile)
  - `src/screens/auth/SignUpProfileScreen.tsx` (**PRIMARY** - profile submit handler)
  - `src/screens/auth/EmailAuthScreen.tsx` (resetAndNavigate on success)
- **Location (Mobile - Services):**
  - `src/services/authService.ts` (signUpWithEmail, createUserProfile)
  - `src/services/imageStorageService.ts` (uploadProfileImage - **FIXED: 30s timeout added**)
  - `src/services/pushNotificationService.ts` (registerForPushNotifications - **FIXED: non-blocking**)
- **Screens:** `src/screens/auth/SignUpProfileScreen.tsx`
- **SP Estimate:** 8 SP (iOS-specific debugging + navigation fix)
- **Description:** Users with iPhones are stuck on the profile page after creating a profile. The app freezes and no button is clickable. Users must log out and sign back in.
- **Root Cause Analysis:**
  - Push token registration is awaited despite comments saying "non-blocking"
  - iOS notification permissions/token acquisition can hang indefinitely
  - UI stays in loading state and becomes unclickable
- **Fix Applied:**
  - Added 30-second timeout to `fetchWithTimeout()` in `imageStorageService.ts` preventing infinite hangs
  - Push token registration wrapped in try/catch and marked non-critical (won't block navigation)
  - Added retry dialog for `IMAGE_UPLOAD_FAILED` with options: "Try Again", "Continue Without Image", "Cancel"
  - Proper loading state management with `finally` block ensuring UI never stays stuck
  - Comprehensive logging added for debugging any edge cases

### U-22. Google Map Links Not Parsing Correctly
- **Status:** Open
- **Reported:** Dec 1, 2025 by Bader Habib
- **Impact:** Blocker - Import fails
- **Location (Mobile):**
  - `src/components/ImportTypeModal.tsx` (sends raw text as sourceUrl)
  - `src/services/importService.ts` (requestLinkImport)
  - `src/screens/ProfileScreen.tsx` (import progress display)
  - `src/navigation/AppNavigator.tsx` (Add menu modal routing)
- **Location (Backend):**
  - `Web & Backend/server/firebase-poller.ts` (processImportRequest, ~500 lines)
  - `Web & Backend/server/routes.ts` (**URL parsing at lines 122, 319, 957, 2233, 2362, 4015**)
    - `scrapeGoogleMapsList` - main scraper
    - `extractPlacesFromGoogleMapsUrl` - URL extraction
    - `handleGoogleMapsImport` - import routing
  - `Web & Backend/server/firebase-service.ts` (createList, upsertPlace)
  - `Web & Backend/shared/schema.ts` (insertImportRequestSchema)
- **Screens:** `src/screens/ProfileScreen.tsx` (import progress), Add Menu flow
- **SP Estimate:** 5 SP (URL parser regex fix + edge case handling)
- **Description:** The import feature fails because it reads the user name and city instead of the link itself. User had to delete extra text for import to work.
- **Root Cause Analysis:**
  - Mobile sends raw pasted text (e.g., "Barcelona Food · Bader\nhttps://...") as sourceUrl
  - No URL extraction/cleaning before calling scraper
  - Backend URL validation checks: `maps.app.goo.gl`, `maps.google.com`, `google.com/maps`
- **Example:** `Barcelona Food · Bader\nhttps://maps.app.goo.gl/J75Ztoscwys3NwdB6?g_st=i` - reads "Barcelona Food · Bader" instead of URL

### U-24. App Crashes When Editing List Name
- **Status:** Open
- **Reported:** Dec 2, 2025 by Bader Habib
- **Impact:** Blocker - Cannot edit lists
- **Location:** `src/components/EditListModal.tsx`, `src/services/firestoreService.ts` (`updateList`)
- **Screens:** `src/screens/ListDetailScreen.tsx`
- **SP Estimate:** 5 SP (crash log analysis + input handler fix)
- **Description:** App crashes when user tries to change the list name.

### U-26. Cannot Add New Places to Existing List
- **Status:** Open
- **Reported:** Nov 28, 2025 by Khaled
- **Impact:** Blocker - Core functionality broken
- **Location (Mobile):**
  - `src/components/AddPlaceModal.tsx` (place selection, onAddPlace)
  - `src/screens/ListDetailScreen.tsx` (handleAddLocation - **PRIMARY**)
  - `src/services/firestoreService.ts` (updateList - rebuilds locationIds)
  - `src/services/placesService.ts` (upsertPlace, getPlacesByIds)
  - `src/store/slices/listsSlice.ts` (setSelectedList - must be hydrated)
- **Location (Backend):**
  - `Web & Backend/server/firebase-service.ts` (updateList)
- **Screens:** `src/screens/ListDetailScreen.tsx`
- **SP Estimate:** 8 SP (Firestore write operation debugging + fix)
- **Description:** Adding a place to an existing list triggers a loader but doesn't actually add the place.
- **Root Cause Analysis:** (Same as U-15)
  - `updateList` rebuilds entire `locationIds` array from upsert results
  - If existing places not in Redux state, they get overwritten
  - Non-incremental update pattern loses existing data
- **Related:** U-15 (Disappearing Places) - Same root cause

### U-30. All Lists and Places Deleted After Migration
- **Status:** Open
- **Reported:** Dec 7, 2025 by Khaled
- **Impact:** Blocker - Complete data loss
- **Location (Mobile Scripts - etch-mobile-main/scripts/):**
  - `cleanInvalidPlaceIds.js` - **HIGH RISK** - Removes invalid place IDs, may delete valid references
  - `addGeohashToLists.ts` - Adds geohash to lists
  - `addGeohashToPlaces.ts` - Adds geohash to places
  - `geocodePlaces.js` - Geocodes missing coordinates
  - `importData.js` - CSV data import
- **Location (Mobile Scripts - etch-mobile-main/src/scripts/):**
  - `cleanupDuplicates.ts` - **CRITICAL** - Deletes imported duplicate lists by userId
  - `fixMissingCoordinates.ts` - Fixes 0,0 coordinates
  - `migrateCityField.ts` - Migrates city field
  - `migrateListImages.ts` - Image migration
- **Location (Data Filtering - Can Hide Data):**
  - `src/utils/imageValidation.ts` - Filters out lists with invalid image URLs
  - `src/services/placesService.ts` - Filters numeric locationIds (imported IDs)
- **Location (Backend):**
  - `Web & Backend/migrations/*` - Drizzle migrations
  - Firebase Console - Data verification/restore
- **Screens:** `src/screens/HomeScreenNew.tsx`, `src/screens/ProfileScreen.tsx`
- **SP Estimate:** 21 SP (data recovery + migration script fix + verification)
- **Description:** All Lists and Places migrated from MapGallery have been deleted. Some lists and places remain but it's unclear from which versions.
- **Root Cause Analysis:**
  - Cleanup scripts deleting imported lists for specific user IDs
  - Mixed ID schemes (numeric imported IDs vs Firebase Auth UIDs) making data "look gone"
  - Image validation filters hiding entire lists from display

### U-31. Admin Dashboard Not Synced with App Data
- **Status:** Open
- **Reported:** Dec 7, 2025 by Khaled
- **Impact:** Blocker - Admin tools unusable
- **Location (Mobile - Writes):**
  - `src/services/firestoreService.ts` (createList, updateList - **does NOT set regionId field**)
  - `src/screens/CreateListScreen.tsx` (list creation)
  - `src/screens/ListDetailScreen.tsx` (list editing)
- **Location (Backend - Reads):**
  - `Web & Backend/server/firebase-service.ts`:
    - `getAllLists` (limit: 50 - may miss lists)
    - `getListsByUserId`, `getListsByRegion`
    - `listPlaces`, `getTrendingLists`
  - `Web & Backend/server/routes.ts` (/api/lists, /api/users endpoints)
- **Location (Admin UI):**
  - `Web & Backend/client/src/pages/AdminUsers.tsx` (fetchUserLists, list fetching)
  - `Web & Backend/client/src/pages/AdminRegions.tsx` (region filtering)
- **Screens:** `src/screens/CreateListScreen.tsx`, `src/screens/ListDetailScreen.tsx` (edit), Admin dashboard (web)
- **SP Estimate:** 13 SP (sync mechanism investigation + webhook/trigger fix)
- **Description:** Lists created or updated on Etch are not synced with Etch Admin. Example: Bashaier has a list for Sri Lanka on Etch but it doesn't appear on Admin.
- **Root Cause Analysis:**
  - Admin region sync uses `regionId` but mobile doesn't populate this field
  - Mobile uses `city` strings, admin expects `regionId`
  - No real-time sync mechanism (no webhooks or Cloud Functions triggers)
  - `getAllLists` has `limit: 50` - may miss lists beyond that

### U-35. Text Import Only Registers First Link
- **Status:** Open
- **Reported:** Dec 20, 2025 by Khaled
- **Impact:** Blocker - Import partially broken
- **Platform:** iOS
- **Location (Mobile):**
  - `src/components/ImportTypeModal.tsx` (sends entire pasted text as single sourceUrl)
  - `src/services/importService.ts` (requestLinkImport - no multi-link splitting)
- **Location (Backend):**
  - `Web & Backend/server/firebase-poller.ts` (processImportRequest - handles single URL)
  - `Web & Backend/server/routes.ts` (URL extraction regex - stops at first match)
- **Screens:** `src/screens/ProfileScreen.tsx` (import progress), Add Menu flow
- **SP Estimate:** 5 SP (multi-link parser fix + iOS-specific testing)
- **Description:** The text import will only register the first link and disregards the rest when pasting multiple Google Maps links with text between them.
- **Root Cause Analysis:**
  - ImportTypeModal sends entire text as single `sourceUrl` string
  - Backend URL regex extracts only first matching URL
  - No multi-link detection or batch import creation
- **Related:** U-22 (Google Map Links Not Parsing)

---

## 🚨 Blocker Priority (Technical)

> **Source:** Internal Code Review - System-level issues blocking production readiness

### 1. Firebase Security Rules Not Enforced
- **Status:** Open
- **Impact:** Blocker - Unauthorized data access/modification possible
- **Location (Rules):**
  - `firestore.rules` - **CURRENT RULES - Known vulnerable patterns:**
    - `places: allow create: if true; allow update: if true;`
    - `lists update: allows any auth user to update imported lists (isImported == true)`
    - `lists update: allows unauth updates to engagement metrics`
  - `storage.rules` - Storage security rules
- **Location (Config):**
  - `firebase.json` - Firebase configuration
  - `firestore.indexes.json` - Composite indexes
- **Location (All Write Operations - Mobile):**
  - `src/services/firestoreService.ts` - createList, updateList, createSave, createFollow, createLike
  - `src/services/placesService.ts` - upsertPlace
  - `src/services/dmService.ts` - message creation
  - `src/services/authService.ts` - user profile creation
  - `src/services/importService.ts` - import request creation
- **Location (All Write Operations - Backend):**
  - `Web & Backend/server/firebase-service.ts` - all CRUD operations
  - `Web & Backend/server/firebase-poller.ts` - import-related writes
- **Screens:** All screens (global)
- **SP Estimate:** 21 SP (security audit + rule implementation + testing)
- **Description:** Firestore and Storage rules are currently too permissive (often `allow read, write: if true;` or similar during dev). Any authenticated (or anonymous) user could potentially read/write any data.
- **Action Required:** 
  - Audit current rules in Firebase Console
  - Implement user-based access control (users can only edit their own profile/lists)
  - Test with Firebase Rules Simulator
  - Ensure public lists readable by all, private only by owner
  - Validate saves/likes/follows for user ownership

### 2. High API Costs (Inefficient Queries)
- **Status:** Open
- **Impact:** Blocker - ~$200+/month in avoidable costs
- **Location (Mobile - Heavy Queries):**
  - `src/services/firestoreService.ts`:
    - `getAllLists` - Reads up to 500 lists, filters client-side
    - `getAllUsers` - Fetches ALL users without pagination
    - `getListsNearMe*` - Geohash range queries without limits
    - `searchLists` - No pagination, loads full result set
  - `src/services/placesService.ts`:
    - `getAllPlaces` - Reads up to 1000 places
    - `getPlacesByIds` - N parallel getDoc calls (N+1 pattern)
  - `src/screens/SearchScreen.tsx` - Heavy reads on 300ms debounce
  - `src/screens/HomeScreenNew.tsx` - Multiple expensive parallel reads
  - `src/screens/ListDetailScreen.tsx` - Full place hydration on mount
- **Location (Backend - Heavy Queries):**
  - `Web & Backend/server/firebase-service.ts`:
    - `getAllLists` - limit: 50, but still unbounded
    - `listPlaces` - fetches all places
    - `getTrendingLists` - complex aggregation
- **Screens:** `src/screens/HomeScreenNew.tsx`, `src/screens/SearchScreen.tsx`, `src/screens/ListDetailScreen.tsx`
- **SP Estimate:** 34 SP (pagination implementation + caching + query optimization)
- **Description:** Search and list fetching logic often retrieves full collections or large datasets (up to 500 docs) without pagination.
- **Action Required:** 
  - Implement cursor-based pagination across all list/search endpoints
  - Add query result caching (React Query or SWR)
  - Replace N+1 getDoc calls with batched reads (max 10 per batch)
  - Set billing alerts at $50, $100, $200

### 3. No Data Validation at Write Time
- **Status:** Open
- **Impact:** Blocker - Data corruption possible
- **Location (Mobile - All Write Operations):**
  - `src/services/firestoreService.ts`:
    - `createList`, `updateList`, `deleteList`
    - `createSave`, `deleteSave`
    - `createFollow`, `deleteFollow`
    - `createLike`, `deleteLike`
    - `updateUserProfile`
  - `src/services/placesService.ts`: `upsertPlace`
  - `src/services/importService.ts`: `requestLinkImport`, `requestImageImport`, `requestTextImport`
  - `src/services/dmService.ts`: message creation (no sanitization)
  - `src/services/authService.ts`: user profile creation
  - `src/services/imageStorageService.ts`: storage uploads (no file type validation)
- **Location (Backend - All Write Operations):**
  - `Web & Backend/server/firebase-service.ts`:
    - `createList`, `updateList`, `deleteList`
    - `createPlace`, `updatePlace`
    - `updateImportRequest`, various update operations
  - `Web & Backend/server/firebase-poller.ts`: import-related writes
  - `Web & Backend/server/routes.ts`: API-triggered writes
- **Location (Validation Schemas - NOT Enforced):**
  - `Web & Backend/shared/schema.ts` - Zod schemas exist but NOT used on writes
  - `src/types/index.ts` - TypeScript types (compile-time only, no runtime validation)
- **Screens:** `src/screens/CreateListScreen.tsx`, `src/screens/ListDetailScreen.tsx`, `src/screens/EditProfileScreen.tsx`, `src/screens/DMConversationScreen.tsx`
- **SP Estimate:** 21 SP (Zod schema creation + service layer validation + rules)
- **Description:** User input is not validated before Firestore writes. Malformed or malicious data can be written to the database.
- **Action Required:**
  - Add Zod/Yup validation in service layer before ALL writes
  - Implement Firestore rules-based validation as second layer
  - Sanitize text inputs (prevent XSS/injection attacks)
  - Validate file types and sizes before storage uploads

---

## 🔴 Critical Priority

### U-9. Keyboard Takes Over Screen (iPhone)
- **Status:** Failed Test
- **Reported:** Oct 30, 2025 by Abuldrahman
- **Impact:** Critical - Cannot see typed content
- **Platform:** iPhone
- **Location:** `src/screens/DMConversationScreen.tsx` (`KeyboardAvoidingView`), `src/screens/EditProfileScreen.tsx`, `src/screens/auth/*` (keyboard handling)
- **Screens:** `src/screens/DMConversationScreen.tsx`, `src/screens/EditProfileScreen.tsx`, `src/screens/auth/EmailAuthScreen.tsx`
- **SP Estimate:** 5 SP (KeyboardAvoidingView configuration + iOS testing)
- **Description:** The user cannot see what they are typing because of the keyboard. User has to scroll to see the typed message. Persists after updates.

### U-10. Location/Distance Completely Wrong
- **Status:** Failed Test
- **Reported:** Oct 30, 2025 by Abuldrahman
- **Impact:** Critical - Navigation unusable
- **Platform:** iPhone/Android
- **Location:** `src/screens/ListDetailScreen.tsx`, `src/screens/MapScreen.tsx`, `src/services/placesService.ts` (place coordinates), `src/scripts/fixMissingCoordinates.ts`
- **Screens:** `src/screens/ListDetailScreen.tsx`, `src/screens/MapScreen.tsx`, `src/screens/PlaceDetailScreen.tsx`
- **SP Estimate:** 8 SP (geolocation service debugging + fallback logic)
- **Description:** User didn't have a VPN on and was in Riyadh but the navigation suggested that a place in Riyadh was in the middle of the ocean. Place directs correctly on Google Maps, but map view from List Page shows pin in wrong location.
- **Developer Notes:** Default location is Riyadh if GPS fails. Investigating why "middle of ocean" occurs - may be bad connection/timeout issue. Added debug text on profile to help diagnose.

### U-16. Profiles Missing from Lists in Search Results
- **Status:** Open
- **Reported:** Nov 24, 2025 by Khaled
- **Impact:** Critical - Poor UX, inconsistent with Home Page
- **Location:** `src/services/firestoreService.ts` (`searchLists` + user hydration), `src/screens/SearchScreen.tsx`, `src/components/ListCard.tsx`
- **Screens:** `src/screens/SearchScreen.tsx`
- **SP Estimate:** 3 SP (query join fix + UI component update)
- **Description:** List owner profile name and photo do not appear with lists from search results.
- **Developer Notes:** Fixed in Dec 5 build

### U-17. Country Search Results Inaccurate
- **Status:** Ready to Test
- **Reported:** Nov 24, 2025 by Khaled
- **Impact:** Critical - Search returns wrong results
- **Location:** `src/services/firestoreService.ts` (`searchLists`), `src/screens/SearchScreen.tsx` (filters), `src/components/SearchFilterModal.tsx`
- **Screens:** `src/screens/SearchScreen.tsx`
- **SP Estimate:** 5 SP (search algorithm fix + metadata indexing)
- **Description:** Searching "Bahrain" shows lists that are not related to Bahrain.
- **Developer Notes:** Old build searched places within lists; new build searches only list metadata.

### U-18. Location Algorithm Not Showing Local Content
- **Status:** Open
- **Reported:** Nov 24, 2025 by Khalil
- **Impact:** Critical - Discovery broken
- **Location:** `src/services/firestoreService.ts` (`getListsNearMe*`, geohash/lat/lon fields), `src/screens/HomeScreenNew.tsx` (near-you feed)
- **Screens:** `src/screens/HomeScreenNew.tsx`, `src/screens/SearchScreen.tsx`
- **SP Estimate:** 8 SP (geo-query debugging + radius logic + permission checks)
- **Description:** User is in Bahrain but isn't seeing the Bahrain content that exists on Etch. Same issue reported in Kuwait.
- **Developer Notes:** Check if user has shared location permissions. Verify blue pin location on Map. 50km search radius for "near you".

### U-25. Profiles Missing from Lists (Homepage)
- **Status:** Passed Test
- **Reported:** Nov 28, 2025 by Khaled
- **Impact:** Critical - Missing creator info
- **Location:** `src/screens/HomeScreenNew.tsx` (list fetch/hydration), `src/components/ListCard.tsx`
- **Screens:** `src/screens/HomeScreenNew.tsx`
- **SP Estimate:** 2 SP (verified fixed - regression testing only)
- **Description:** Profile image and name missing from lists in Trending Lists and Lists for You rows.

### U-28. Profiles Missing from Lists (Search)
- **Status:** Passed Test
- **Reported:** Nov 28, 2025 by Khaled
- **Impact:** Critical - Duplicate of U-16
- **Location:** `src/services/firestoreService.ts` (`searchLists` + user hydration), `src/screens/SearchScreen.tsx`, `src/components/ListCard.tsx`
- **Screens:** `src/screens/SearchScreen.tsx`
- **SP Estimate:** 1 SP (verified fixed - regression testing only)
- **Description:** Profile image and name missing from lists in search results.

---

## 🟠 High Priority

### 4. Android Map Marker Size Limit (~40px)
- **Status:** Open / Workaround Applied
- **Context:** Expo SDK 54 / React Native 0.81.4 / New Architecture (Fabric)
- **Location:** `ListDetailScreen.tsx`, `MapScreen.tsx`, `MapMarkerAvatar.tsx`
- **Screens:** `src/screens/ListDetailScreen.tsx`, `src/screens/MapScreen.tsx`
- **SP Estimate:** 5 SP (library upgrade + compatibility testing + workaround removal)
- **Description:** Custom markers on Android are clipped if larger than ~40px due to a regression in `react-native-maps` versions prior to 1.21.0.
- **Current Workaround:** The app currently detects Android and renders a simple 32px colored pin instead of the 60px user avatar marker used on iOS.
- **Fix:** 
  1. Upgrade `react-native-maps` from **1.20.1** to **1.21.0** or higher.
  2. Remove the `Platform.OS === 'android'` check in `ListDetailScreen.tsx` and `MapScreen.tsx`.
  3. **Note:** Verify compatibility with `react-native-map-clustering` v4.0.0 after upgrade (may require patch or migration to `react-native-map-supercluster`).
- **References:** 
  - https://github.com/react-native-maps/react-native-maps/issues/5247
  - https://github.com/react-native-maps/react-native-maps/issues/5239

### 5. Google OAuth Not Functional
- **Status:** TODO in Code
- **Location:** `src/services/authService.ts:244`
- **Screens:** `src/screens/auth/WelcomeScreen.tsx`
- **SP Estimate:** 8 SP (OAuth flow completion + token exchange + user info retrieval)
- **Description:** Google OAuth flow is configured but the token exchange step is incomplete. Code contains `// TODO: Exchange code for tokens and get user info`.
- **Action Required:** Complete implementation to exchange auth code for tokens and retrieve user profile.

### 6. Facebook OAuth Not Functional
- **Status:** TODO in Code
- **Location:** `src/services/authService.ts:289`
- **Screens:** `src/screens/auth/WelcomeScreen.tsx`
- **SP Estimate:** 8 SP (OAuth flow completion + Facebook SDK integration)
- **Description:** Facebook OAuth flow is configured but the token exchange step is incomplete. Code contains `// TODO: Exchange code for access token and get user info`.
- **Action Required:** Complete implementation similar to Google OAuth.

### 7. TikTok OAuth Not Implemented
- **Status:** Not Started
- **Location:** `src/services/authService.ts:322`
- **Screens:** `src/screens/auth/WelcomeScreen.tsx`
- **SP Estimate:** 13 SP (full TikTok OAuth implementation from scratch)
- **Description:** TikTok OAuth is referenced in code but not implemented. Code contains `// TODO: Implement TikTok OAuth flow`.
- **Action Required:** Implement full TikTok OAuth flow or remove from UI if not needed.

### 8. No Defined NoSQL Schema
- **Status:** Open
- **Impact:** High - Data inconsistencies across collections.
- **Location:** `types/index.ts`, `firestoreService.ts`
- **Screens:** `src/screens/CreateListScreen.tsx`, `src/screens/ListDetailScreen.tsx`, `src/screens/SearchScreen.tsx`, `src/screens/HomeScreenNew.tsx`
- **SP Estimate:** 21 SP (schema definition + migration + timestamp standardization)
- **Description:** 
  - Dual storage pattern exists: `locations[]` array AND `locationIds[]` references
  - Timestamps are inconsistent (Date vs string vs Firestore Timestamp)
  - No formal schema documentation or enforcement
- **Action Required:**
  - Define formal Firestore schema
  - Migrate fully from `locations[]` to `locationIds[]` references
  - Standardize all timestamps on ISO strings

### 9. API Keys Exposed in Code
- **Status:** Open
- **Impact:** High - Keys could be extracted from app builds.
- **Location:** `app.json`, various services
- **Screens:** All screens that call external APIs (Places/Search/Import)
- **SP Estimate:** 8 SP (EAS secrets setup + key restrictions + backend proxy)
- **Description:** Google API keys are visible in `app.json` and potentially extractable from compiled builds.
- **Action Required:**
  - Use EAS secrets for sensitive keys
  - Implement API key restrictions in Google Console (restrict by app bundle ID)
  - Consider backend proxy for sensitive API calls

### U-1. Apple Maps Only Navigation Option (iOS)
- **Status:** Failed Test
- **Reported:** Oct 29, 2025 by Khalil
- **Impact:** Major - Poor UX for users who prefer Google Maps/Waze
- **Platform:** iOS
- **Location:** `src/screens/PlaceDetailScreen.tsx`, `src/screens/LocationDetailScreen.tsx` (maps deep links), `app.json` (iOS URL scheme allowlist)
- **Screens:** `src/screens/PlaceDetailScreen.tsx`, `src/screens/LocationDetailScreen.tsx`
- **SP Estimate:** 3 SP (navigation picker implementation + deep link handling)
- **Description:** The user's phone automatically opens Apple Maps as the navigation tool and there is no way to select an alternative app like Waze or Google Maps. User can select between Waze and Apple Maps, but Google Maps doesn't show as an option.
- **Developer Notes:** Works on Android. Should work in production iOS build with Apple Developer account. Expo Go limitation - Google Maps should appear if installed on user's phone.
- **Related:** FR-MAP-005 (Navigation App Selection)

### U-3. User's Own Lists Don't Appear in Search
- **Status:** Failed Test
- **Reported:** Oct 29, 2025 by Khalil
- **Impact:** Major - Cannot find own content
- **Location:** `src/services/firestoreService.ts` (`searchLists` filters `isPublic == true` + `hasValidListImage`), `src/screens/SearchScreen.tsx`
- **Screens:** `src/screens/SearchScreen.tsx`
- **SP Estimate:** 3 SP (search query scope fix + own content inclusion)
- **Description:** The user's own lists don't appear when they search for lists on the search page.
- **Developer Notes:** Cannot reproduce - lists appear in search. May be fixed in current build.

### U-7. Cannot Send DM Messages
- **Status:** Open
- **Reported:** Nov 9, 2025 by Rania
- **Impact:** Major - Messaging broken
- **Platform:** iPhone
- **Location:** `src/screens/DMConversationScreen.tsx` (send handler + UI), `src/services/dmService.ts` (`sendMessage`)
- **Screens:** `src/screens/DMConversationScreen.tsx`
- **SP Estimate:** 8 SP (DM service debugging + iOS-specific send button fix)
- **Description:** The user is able to see the typed message but cannot click send.

### U-8. DM Messages Not Displaying to Recipient
- **Status:** Open
- **Reported:** Nov 10, 2025 by Ragad M.
- **Impact:** Major - Messaging broken
- **Platform:** iPhone
- **Location:** `src/screens/DMConversationScreen.tsx` (no realtime listener), `src/services/dmService.ts` (`getMessagesWithUser`)
- **Screens:** `src/screens/DMConversationScreen.tsx`, `src/screens/InboxScreen.tsx`
- **SP Estimate:** 8 SP (real-time listener debugging + message sync fix)
- **Description:** The sent messages are not appearing to the other user. Ragad sent messages to Rania, but the chat was empty on her end.
- **Related:** U-7

### U-20. Not All Profiles Appear in Search
- **Status:** Open
- **Reported:** Nov 28, 2025 by Fawaz
- **Impact:** Major - Search incomplete
- **Location:** `src/screens/SearchScreen.tsx` (`getFilteredProfiles` relies on `allLists` scope), `src/services/firestoreService.ts` (`getAllUsers`, `searchLists`)
- **Screens:** `src/screens/SearchScreen.tsx`
- **SP Estimate:** 5 SP (profile search query expansion + index creation)
- **Description:** User searched "London" and only one profile appeared, even though multiple profiles have created a London List.

### U-23. Not All Items Imported from Google Maps
- **Status:** Open
- **Reported:** Dec 1, 2025 by Bader Habib
- **Impact:** Major - Data loss during import
- **Location:** `src/services/importService.ts` (request queue), `Web & Backend/server/firebase-poller.ts` (processing), `Web & Backend/server/routes.ts` (`scrapeGoogleMapsList`)
- **Screens:** `src/screens/ProfileScreen.tsx` (import progress), Add Menu flow (modal via `src/navigation/AppNavigator.tsx`)
- **SP Estimate:** 8 SP (import pipeline debugging + error logging + retry logic)
- **Description:** The Google Maps list has 73 places, only 68 were imported (5 missing).
- **Example:** `Barcelona Food · Bader - https://maps.app.goo.gl/J75Ztoscwys3NwdB6?g_st=i`

### U-27. Remove Social Media Popup When Adding Places
- **Status:** Open
- **Reported:** Nov 28, 2025 by Khaled
- **Impact:** Major - Disruptive UX
- **Location:** `src/screens/ListDetailScreen.tsx` (add-place flow), `src/components/AddPlaceModal.tsx`
- **Screens:** `src/screens/ListDetailScreen.tsx`
- **SP Estimate:** 2 SP (popup removal or UX redesign)
- **Description:** Adding places triggers a popup to add the social media accounts of that place. Although useful, this interrupts the flow.

### U-29. Application is Very Slow
- **Status:** Open
- **Reported:** Dec 7, 2025 by Khaled
- **Impact:** Major - Poor performance
- **Location:** `src/screens/HomeScreenNew.tsx`, `src/screens/SearchScreen.tsx`, `src/services/firestoreService.ts` (large queries/no pagination)
- **Screens:** `src/screens/HomeScreenNew.tsx`, `src/screens/SearchScreen.tsx`
- **SP Estimate:** 13 SP (performance profiling + optimization - depends on Bug #2)
- **Description:** The application takes a long time to load on both the Home Page and Search.
- **Related:** Bug #2 (High API Costs), Bug #11 (Search Performance)

---

## 🟡 Medium Priority

### 10. Map Pin Images API Issue
- **Status:** Investigating
- **Location:** `placesService.ts`, `MapMarkerAvatar.tsx`
- **Screens:** `src/screens/HomeScreenNew.tsx`, `src/screens/ListDetailScreen.tsx`, `src/screens/MapScreen.tsx`
- **SP Estimate:** 8 SP (image caching layer + fallback handling + photo reference refresh)
- **Description:** Some place photos from Google Places API fail to load or return 403/404 errors when used as map markers. This causes visual inconsistencies.
- **Action Required:** 
  - Implement robust fallback images
  - Add image caching layer
  - Handle expired photo references gracefully

### 11. Search Performance on Large Datasets
- **Status:** Open
- **Location:** `SearchScreen.tsx`, `firestoreService.ts`
- **Screens:** `src/screens/SearchScreen.tsx`
- **SP Estimate:** 13 SP (server-side search implementation or virtual scrolling)
- **Description:** Search functionality becomes slow when filtering large datasets (500+ items) on the client side. UI may freeze during filtering.
- **Action Required:** 
  - Move search logic to server-side (Algolia/Typesense)
  - Or implement virtual scrolling with pagination
  - Optimize client-side indexing

### 12. Login Flow Edge Cases
- **Status:** 🟡 Fixed - Pending QA Verification
- **Fixed:** Dec 24, 2025
- **Location:** Auth screens (`EmailAuthScreen.tsx`, `SignUpEmailScreen.tsx`, etc.)
- **Screens:** `src/screens/auth/EmailAuthScreen.tsx`, `src/screens/auth/SignUpEmailScreen.tsx`, `src/screens/auth/SignUpPasswordScreen.tsx`, `src/screens/auth/SignUpBirthdayScreen.tsx`, `src/screens/auth/SignUpProfileScreen.tsx`
- **SP Estimate:** 5 SP (error handling + user-friendly messages + retry)
- **Description:** Some error states during login/signup are not handled gracefully. Users may see generic errors or blank screens.
- **Fix Applied:**
  - `getFriendlyErrorMessage()` function maps Firebase error codes to user-friendly messages
  - All error codes handled: `auth/invalid-email`, `auth/user-not-found`, `auth/wrong-password`, `auth/invalid-credential`, `auth/email-already-in-use`, `auth/weak-password`, `auth/too-many-requests`, `auth/network-request-failed`
  - `SignUpProfileScreen` has retry dialogs for `IMAGE_UPLOAD_FAILED` and `PROFILE_SAVE_FAILED`
  - Loading states properly managed with `finally` blocks

### 13. No Firestore Indexes Defined
- **Status:** 🟡 Fixed - Pending QA Verification
- **Fixed:** Dec 24, 2025
- **Location:** `firestore.indexes.json`
- **Screens:** `src/screens/SearchScreen.tsx`, `src/screens/HomeScreenNew.tsx`
- **SP Estimate:** 5 SP (query audit + index creation + deployment)
- **Description:** Composite indexes are not defined for all queries, causing performance issues and potential query failures.
- **Fix Applied:**
  - `firestore.indexes.json` now contains 25+ composite indexes covering:
    - `lists`: by `isPublic`, `city`, `country`, `geohash` with various sort fields (`views`, `saves`, `likes`, `createdAt`, `updatedAt`)
    - `messages`: by `fromUserId`, `toUserId`, `notificationSent`, `read`
    - `saves`, `comments`, `import_requests`, `places` collections
  - Indexes deployed to Firebase

### 14. No Rate Limiting
- **Status:** Open
- **Location:** All Firestore operations
- **Screens:** All screens (global)
- **SP Estimate:** 13 SP (security rules rate limiting + Cloud Functions + monitoring)
- **Description:** No rate limiting on Firestore operations. A malicious user could run up costs with rapid requests.
- **Action Required:**
  - Implement Firestore security rules with rate limiting
  - Add Cloud Functions for rate-limited operations
  - Monitor for unusual patterns in Firebase Console

### U-19. Place Images Missing on Homepage
- **Status:** Passed Test
- **Reported:** Nov 24, 2025 by Khaled
- **Impact:** Medium - Visual issue
- **Location (Mobile):** `src/screens/HomeScreenNew.tsx` (places row), `src/components/PlaceRow.tsx` (renders `place.images[0]`), `src/services/placesService.ts` (`getPlacesNearMe`)
- **Screens:** `src/screens/HomeScreenNew.tsx`
- **SP Estimate:** 1 SP (verified fixed - regression testing only)
- **Description:** There are no images for the Places on the Homepage.
- **Developer Notes:** Fixed in next build

---

## 🟢 Low Priority / Technical Debt

### 15. DM Unread Indicators
- **Status:** Partially Fixed
- **Location:** `src/services/dmService.ts`
- **Screens:** `src/screens/InboxScreen.tsx`, `src/screens/DMConversationScreen.tsx`
- **SP Estimate:** 3 SP (read status logic fix + debug log cleanup)
- **Description:** Unread message indicators sometimes desync or persist after reading. Debug logging still present in production code.
- **Action Required:** Review `dmService.ts` read status logic and remove debug logs.

### 16. Debug Logging in Production
- **Status:** Open
- **Location:** `dmService.ts`, `placesService.ts`, multiple files
- **Screens:** All screens (global)
- **SP Estimate:** 3 SP (console.log audit + __DEV__ gating + logger migration)
- **Description:** `console.log` statements and debug logging present in production code, affecting performance and exposing internal state.
- **Action Required:** 
  - Remove or gate behind `__DEV__`
  - Use the `logger.ts` utility consistently

### 17. Deprecated LocationDetail Navigation
- **Status:** Open
- **Location:** Navigation types, `LocationDetailScreen.tsx`
- **Screens:** `src/screens/LocationDetailScreen.tsx`, `src/screens/PlaceDetailScreen.tsx`
- **SP Estimate:** 3 SP (dead code removal + route migration)
- **Description:** Old `LocationDetail` route and screen still exist alongside new `PlaceDetail`. Causes confusion and dead code.
- **Action Required:** Remove deprecated `LocationDetail` code paths and migrate all usages to `PlaceDetail`.

### 18. No Error Boundaries
- **Status:** Open
- **Location:** All screens
- **Screens:** All screens (global)
- **SP Estimate:** 5 SP (error boundary components + screen wrapping)
- **Description:** No React error boundaries implemented. Unhandled errors can crash the entire app instead of showing a fallback UI.
- **Action Required:** Add error boundaries around major screen sections.

### 19. Missing Sentry/Error Monitoring Integration
- **Status:** Open
- **Location:** `src/utils/logger.ts:143` contains `// TODO: Integrate with Sentry or similar service`
- **Screens:** All screens (global)
- **SP Estimate:** 8 SP (Sentry SDK setup + error reporting + source maps)
- **Description:** No crash reporting or error monitoring service integrated. Difficult to track production issues.
- **Action Required:** Integrate Sentry or similar service for crash reporting and error tracking.

### 20. No Component/Screen Tests
- **Status:** Open
- **Location:** `__tests__/components/`, `__tests__/screens/`
- **Screens:** All screens (global)
- **SP Estimate:** 34 SP (component tests + screen tests + E2E setup)
- **Description:** 40+ components and 14+ screens have 0% test coverage. Only services and Redux slices are tested.
- **Action Required:**
  - Add component tests with @testing-library/react-native
  - Add screen integration tests
  - Set up E2E tests with Maestro

### U-2/U-5. Social Media Icons on Places Lead Nowhere
- **Status:** Open
- **Reported:** Oct 29, 2025 by Khalil; Nov 9, 2025 by Amal Malek
- **Impact:** Minor - Misleading UI
- **Location (Mobile):** `src/screens/LocationDetailScreen.tsx` (Instagram/TikTok buttons fallback to homepages when missing), `src/types/index.ts` (Place fields: instagram/tiktok/website)
- **Screens:** `src/screens/LocationDetailScreen.tsx`
- **SP Estimate:** 3 SP (conditional icon display + link validation)
- **Description:** All places include social media icons but they don't lead to the place's account.
- **Proposed Solutions:**
  1. Hide icons if there is no linked account/website
  2. Allow users to add missing accounts
  3. Hide all social media icons (except website) until solution implemented
- **Related:** FR-PLACE-014 (Social Media Links on Places)

### U-4. Country Flags Don't Match
- **Status:** Open
- **Reported:** Oct 30, 2025 by Khalil
- **Impact:** Minor - Visual inconsistency
- **Location (Mobile):** `src/components/ListCard.tsx` (flag image is hardcoded to KSA)
- **Screens:** `src/screens/HomeScreenNew.tsx`, `src/screens/SearchScreen.tsx`, `src/screens/ProfileScreen.tsx`
- **SP Estimate:** 2 SP (flag mapping logic fix from place country data)
- **Description:** The flags that appear under lists do not reflect the country they are next to (all show KSA by default).

### U-6. Map Not Full Display on Samsung Fold
- **Status:** Open
- **Reported:** Nov 2, 2025 by Ayman
- **Impact:** Minor - Foldable device support
- **Platform:** Samsung Fold
- **Location (Mobile):** `src/screens/MapScreen.tsx` (layout/sizing), `src/screens/ListDetailScreen.tsx` (map tab)
- **Screens:** `src/screens/MapScreen.tsx`, `src/screens/ListDetailScreen.tsx`
- **SP Estimate:** 5 SP (foldable device detection + responsive layout)
- **Description:** The map page isn't in full display on the fold phone.

### U-11. UI Overlap on List Page (iPhone)
- **Status:** Ready to Test
- **Reported:** Nov 15, 2025 by Rania
- **Impact:** Minor - Visual bug
- **Platform:** iPhone 16
- **Location (Mobile):** `src/screens/PlaceDetailScreen.tsx` / `src/screens/LocationDetailScreen.tsx` (action icon row layout), `src/components/IconButton.tsx` (hitSlop)
- **Screens:** `src/screens/ListDetailScreen.tsx`, `src/screens/PlaceDetailScreen.tsx`
- **SP Estimate:** 2 SP (spacing adjustment + verification)
- **Description:** Overlap of phone icons and app icons when the user scrolls.
- **Developer Notes:** Should have more space in newest build to prevent overlap.

### U-12. Cannot View Lowest DM in Conversation
- **Status:** Passed Test
- **Reported:** Nov 18, 2025 by Khaled
- **Impact:** Minor - UI cutoff
- **Platform:** Samsung S24+
- **Location (Mobile):** `src/screens/DMConversationScreen.tsx` (FlatList + input bar + KeyboardAvoidingView)
- **Screens:** `src/screens/DMConversationScreen.tsx`
- **SP Estimate:** 1 SP (verified fixed - regression testing only)
- **Description:** Users cannot view the lowest DM message (cut off at bottom).
- **Developer Notes:** DM work in previous build may have fixed this.

### U-13. Profile Link Not Clickable
- **Status:** Open
- **Reported:** Nov 19, 2025 by Khaled
- **Impact:** Minor - Feature not working
- **Platform:** Samsung S24+
- **Location (Mobile):** `src/screens/ProfileScreen.tsx` (social link rendered without `onPress`/Linking handler)
- **Screens:** `src/screens/ProfileScreen.tsx`
- **SP Estimate:** 2 SP (link pressable area fix)
- **Description:** Link on profile is not clickable.

### U-14. Description Paste Character Limit Issue
- **Status:** Open
- **Reported:** Nov 19, 2025 by Khaled
- **Impact:** Minor - UX issue
- **Location (Mobile):** `src/components/CharacterLimitInput.tsx` (rejects pasted text if `text.length > maxLength`), `src/components/EditListModal.tsx` (list description maxLength=250)
- **Screens:** `src/screens/ListDetailScreen.tsx` (edit list modal)
- **SP Estimate:** 2 SP (paste handler modification for truncation)
- **Description:** If user attempts to paste a description more than 250 characters, the app will not paste any text. Should accept allowed characters and truncate the rest.

### U-32. List Image Mismatch (Admin vs App)
- **Status:** Open
- **Reported:** Dec 16, 2025 by Khaled
- **Impact:** Minor - Data sync issue
- **Location (Mobile):** `src/components/EditListModal.tsx` (updates `coverImage`), `src/services/firestoreService.ts` (`updateList`), `src/screens/HomeScreenNew.tsx` (prefers `imageUrl || coverImage`)
- **Location (Backend/Web):** `Web & Backend/server/firebase-service.ts` (admin list mapping reads `imageUrl`), `Web & Backend/client/*` (admin list UI)
- **Screens:** `src/screens/ListDetailScreen.tsx`, Admin dashboard (web)
- **SP Estimate:** 5 SP (image sync investigation + CDN cache handling)
- **Description:** The list image on Admin doesn't match the one that's live on the app. Additionally, another list didn't have an image on Admin but has one on the app.

### U-33. Sign-In UX Forces User Back on Error
- **Status:** Open
- **Reported:** Dec 16, 2025 by Fawaz
- **Impact:** Minor - Poor UX
- **Location (Mobile):** `src/screens/auth/EmailAuthScreen.tsx` (error handling + state), `src/screens/auth/WelcomeScreen.tsx` (navigation to EmailAuth)
- **Screens:** `src/screens/auth/EmailAuthScreen.tsx`, `src/screens/auth/WelcomeScreen.tsx`
- **SP Estimate:** 2 SP (navigation flow fix + form state preservation)
- **Description:** The application forces the user back when user attempts to sign in with a wrong password. Should remain on Login Page with email already typed in.

### U-34. iOS Keyboard Autocomplete Disabled
- **Status:** Open
- **Reported:** Dec 16, 2025 by Fawaz
- **Impact:** Minor - Missing convenience feature
- **Platform:** iPhone
- **Location (Mobile):** `src/components/TextInput.tsx`, `src/components/CharacterLimitInput.tsx`, Auth screens (`src/screens/auth/*`) (TextInput props: `autoCorrect`, `autoComplete`, `textContentType`)
- **Screens:** `src/screens/auth/EmailAuthScreen.tsx`, `src/screens/auth/SignUpProfileScreen.tsx`, `src/screens/EditProfileScreen.tsx`
- **SP Estimate:** 1 SP (TextInput autocomplete prop fix)
- **Description:** The text autocomplete feature of iPhone isn't enabled in text fields.

---

## 📋 TODOs Found in Codebase

| Location | TODO |
|----------|------|
| `src/utils/logger.ts:143` | Integrate with Sentry or similar service |
| `src/services/authService.ts:244` | Exchange code for tokens and get user info (Google) |
| `src/services/authService.ts:289` | Exchange code for access token and get user info (Facebook) |
| `src/services/authService.ts:322` | Implement TikTok OAuth flow |
| `src/config/homeFeedAlgorithm.ts:36` | Add shares tracking to List type |
| `src/config/homeFeedAlgorithm.ts:159` | Add shares tracking to List type |

---

## 📊 Bug Summary

### By Priority (with Effort Estimates)
| Priority | Internal | User-Reported | Total Bugs | Total SP | Dev-Days |
|----------|----------|---------------|------------|----------|----------|
| 🚨 Blocker (Technical) | 3 | 0 | **3** | **76** | ~10 |
| 🚨 Blocker (User-Reported) | 0 | 8 | **8** | **78** | ~10 |
| 🔴 Critical | 0 | 7 | **7** | **33** | ~4 |
| 🟠 High | 6 | 8 | **14** | **114** | ~14 |
| 🟡 Medium | 5 | 1 | **6** | **45** | ~6 |
| 🟢 Low/Minor | 6 | 10 | **16** | **81** | ~10 |
| **Total** | **20** | **34** | **54** | **427 SP** | **~54 days** |

### Effort by Category
| Category | Bugs | SP | Notes |
|----------|------|---:|-------|
| Security & Data | 4 | 89 | Firebase rules, validation, API keys, rate limiting |
| Performance | 3 | 60 | API costs, search perf, app slowness |
| Import/Migration | 5 | 52 | URL parsing, data loss, migration issues |
| Authentication | 4 | 34 | OAuth implementations, login edge cases |
| Messaging (DM) | 4 | 20 | Send/receive/indicators |
| Map & Navigation | 5 | 26 | Markers, location, navigation options |
| Search & Discovery | 4 | 21 | Results accuracy, local content |
| UI/UX Issues | 9 | 24 | Visual bugs, keyboard, overlap |
| Data Sync | 4 | 41 | Admin sync, schema, image mismatch |
| Technical Debt | 6 | 60 | Tests, error boundaries, logging, deprecations |

### By Status
| Status | Count |
|--------|-------|
| 🔴 Open | 42 |
| 🟡 Ready to Test | 5 |
| 🟢 Passed Test | 4 |
| ⚪ Partial | 1 |
| 🔵 Won't Fix | 2 |

### Bug IDs by Priority
- **Blocker - Technical (3):** #1 (Security Rules), #2 (API Costs), #3 (Data Validation)
- **Blocker - User-Reported (8):** U-15, U-21, U-22, U-24, U-26, U-30, U-31, U-35
- **Critical (7):** U-9, U-10, U-16, U-17, U-18, U-25, U-28
- **High (14):** #4-9, U-1, U-3, U-7, U-8, U-20, U-23, U-27, U-29
- **Medium (6):** #10-14, U-19
- **Low/Minor (16):** #15-20, U-2/U-5, U-4, U-6, U-11, U-12, U-13, U-14, U-32, U-33, U-34

---

## 📁 Complete File Inventory (Affected by Blockers)

### Mobile App Files (etch-mobile-main/)
| Category | Files |
|----------|-------|
| **Services** | `firestoreService.ts`, `placesService.ts`, `importService.ts`, `authService.ts`, `imageStorageService.ts`, `pushNotificationService.ts`, `dmService.ts` |
| **Screens** | `ListDetailScreen.tsx`, `CreateListScreen.tsx`, `HomeScreenNew.tsx`, `SearchScreen.tsx`, `ProfileScreen.tsx`, all auth screens |
| **Components** | `AddPlaceModal.tsx`, `ImportTypeModal.tsx`, `EditListModal.tsx`, `ListCard.tsx` |
| **Store** | `listsSlice.ts` |
| **Types** | `types/index.ts` |
| **Rules** | `firestore.rules`, `storage.rules` |
| **Scripts** | `cleanupDuplicates.ts`, `cleanInvalidPlaceIds.js`, migration scripts |

### Backend Files (Web & Backend/)
| Category | Files |
|----------|-------|
| **Server** | `firebase-service.ts`, `firebase-poller.ts`, `routes.ts` |
| **Client** | `AdminUsers.tsx`, `AdminRegions.tsx` |
| **Shared** | `schema.ts` |
| **Migrations** | `migrations/*` |

---

## Document History

| Date | Version | Changes |
|------|---------|---------|
| Dec 24, 2025 | 1.2 | **Comprehensive code trace** - Promoted #1, #2, #3 from Critical to Blocker (now 11 blockers total); Added complete frontend/backend file inventories for all blockers; Added root cause analysis; Added Related Bug links; Added Complete File Inventory section |
| Dec 24, 2025 | 1.1 | **Added effort estimation** - SP estimates for all 54 bugs (427 SP total); Added Effort Estimation Key; Added effort by category breakdown |
| Dec 23, 2025 | 1.0 | **Integrated client-reported bugs** - Added 34 user-reported bugs from Bug_Enhancement Tracker CSV; Added new 🚨 Blocker Priority section; Reorganized by source (Internal vs User-Reported) |
| Jan 2025 | 0.2 | Initial bug tracker created |
| Jan 2025 | 0.2 | Added Android map marker bug with fix instructions |
| Jan 2025 | 0.2 | Consolidated all bugs from MOBILE_APP_STATUS.md |
