# ChaufHER App Technical Design

## Table of Contents

1. [Quick Start](#quick-start)
2. [Development Setup](#development-setup)
3. [Product Overview](#product-overview)
4. [Purpose](#purpose)
5. [Target Audience](#target-audience)
6. [Expected Outcomes](#expected-outcomes)
7. [Technical Architecture](#technical-architecture)
8. [Domain Model and Data Flow](#domain-model-and-data-flow)
9. [External Interfaces](#external-interfaces)
10. [User Interface](#user-interface)
11. [Quality Assurance](#quality-assurance)
12. [Deployment](#deployment)
13. [Advanced Safety/Panic Flow](#advanced-safetypanic-flow--app-role)

---

## Infrastructure

This project relies on centralized infrastructure and deployment workflows managed in the **ChaufHER Infra** repository: https://github.com/phoenixvc/chaufher-infra

For mobile-specific environment variables, API contracts, and deployment checklists, see:
https://github.com/phoenixvc/chaufher-infra/blob/main/MOBILE_CHECKLIST.md

---

## Quick Start

### For Developers

**Prerequisites:**
- Flutter SDK (3.0+), Dart (3.0+)
- Android SDK / Xcode (for iOS)
- Git

**Get Started in 3 Steps:**

1. **Clone & Install**
   ```bash
   git clone https://github.com/phoenixvc/chaufher-app.git
   cd chaufher-app
   flutter pub get
   ```

2. **Configure Environment**
   - Copy `.env.example` to `.env` (contact team for values)
   - Update API endpoints in `lib/core/config/env_config.dart`

3. **Run Locally**
   ```bash
   # Development (hot-reload enabled)
   flutter run -d chrome  # Web
   flutter run -d emulator-5554  # Android emulator
   flutter run  # iOS simulator (macOS only)
   ```

**Key Commands:**
- `flutter analyze` - Lint checks
- `flutter test` - Run unit/widget tests
- `fastlane ios beta` - Build iOS beta
- `fastlane android beta` - Build Android beta

**Documentation Roadmap:**
- See [Development Setup](#development-setup) for detailed environment configuration
- See [Technical Architecture](#technical-architecture) for codebase structure
- See [Quality Assurance](#quality-assurance) for testing practices

---

## Development Setup

### Environment Configuration

**1. Install Flutter & Dependencies**

```bash
# macOS
brew install flutter

# Linux/WSL
wget https://storage.googleapis.com/flutter_infra_release/releases/stable/linux/flutter_linux_3.x.x-stable.tar.xz
tar xf flutter_linux_3.x.x-stable.tar.xz
export PATH="$PATH:$PWD/flutter/bin"

# Verify installation
flutter doctor
```

**2. Platform-Specific Setup**

*Android:*
```bash
# Android SDK (API 28+), NDK
flutter config --android-sdk /path/to/android/sdk
flutter config --android-ndk /path/to/android/ndk
```

*iOS:*
```bash
# CocoaPods, Xcode 13+
sudo gem install cocoapods
xcode-select --install
```

**3. Project Dependencies**

```bash
cd chaufher-app
flutter pub get
flutter pub get --upgrade  # Update to latest compatible versions
```

**4. Environment Variables**

Create `.env` file in project root:
```env
API_URL=https://sandbox.api.chaufher.app
API_KEY=your_sandbox_key_here
FIREBASE_PROJECT=chaufher-dev
STRIPE_PUBLISHABLE_KEY=pk_test_...
SENTRY_DSN=https://...@sentry.io/...
```

**5. Run & Test**

```bash
# Development build
flutter run --debug

# Release build (local)
flutter run --release

# Run tests
flutter test --coverage
```

### IDE Configuration

**VS Code:**
1. Install extensions: Flutter, Dart, Bloc
2. Open workspace: `code .`
3. Create `.vscode/launch.json` for debugging

**Android Studio/IntelliJ:**
1. Install Flutter & Dart plugins
2. Open project folder
3. Run → Select device → Run 'main.dart'

### Build & Deploy

See [Deployment](#deployment) section for CI/CD pipelines and release procedures.

---

## Product Overview

ChaufHER is a women-only ridesharing app engineered with a strong emphasis on user safety, privacy, and reliability. Designed for deployment on Flutter/iOS/Android, the app facilitates transportation for women by women, addressing key mobility challenges and security concerns. By combining robust technical architecture with thoughtful UX design, ChaufHER delivers an accessible, trustworthy transport platform for female riders, drivers, and support operations.

Key outcomes:

* Safe and empowering ridesharing for women.

* Trusted environment with advanced in-app safety features.

* Scalable, modular framework for future enhancements.

---

## Purpose

The ChaufHER app’s core purpose is to provide a secure, on-demand ridesharing service, accessible exclusively to women. The app addresses:

* Safety concerns in traditional unisex ridesharing platforms.

* Lack of personalized, trusted transportation for women in urban and suburban areas.

**Business and Technical Problems Solved:**

* Reduces harassment and safety-related incidents.

* Fulfills an identified market need among underserved women users.

* Ensures rapid safety alert and response mechanisms through real-time data integration.

**Example Scenarios:**

* A female commuter books a ride late at night, assured by pre-verified female drivers and integrated live location tracking.

* In the event of discomfort, a rider discreetly triggers a safety alert, instantly notifying support staff and chosen contacts.

---

## Target Audience

* **Riders:** Women (18-60+) in urban/suburban areas, including students, professionals, and mothers. They need accessible, safe, and reliable transportation.

* **Drivers:** Female drivers seeking flexible income opportunities within a supportive, women-only framework. They value secure, fair-work conditions and community support.

* **Operations/Admin:** Support staff who monitor safety events, handle onboarding, manage compliance, and assist in service recovery.

**Market Segments:**

* Metropolitan regions with high professional female populations.

* University locales.

* Communities with cultural preferences for gender-specific services.

---

## Expected Outcomes

**Product Benefits:**

* Trusted transport alternative, leading to increased female mobility and participation.

* Reduced incident rates through in-app panic features and professional driver vetting.

* Strong market differentiation and network effects in trust-based communities.

**KPIs/Metrics:**

| Metric | Short-Term Target | Long-Term Goal |
|--------|-------------------|----------------|
| Weekly Active Users | 2,000+ | 25,000+ |
| Ride Completion Rate | >95% | >98% |
| Reported Safety Incidents | <0.1% of trips | <0.05% of trips |
| App Crash Rate | <1% | <0.2% |
| User Satisfaction (NPS) | >60 | >80 |

---

## Technical Architecture

ChaufHER.app is a single Flutter codebase embracing a layered and feature-module architecture designed for current cohabitation of rider, driver, and admin-support clients with smooth separation possible later.

**Core Principle:** Domain and infrastructure responsibilities are intentionally delegated to chaufher.api, ensuring the mobile app remains focused on orchestration, state, and user interface. The division between app and API is explicit: all core business rules, persistence, and system-wide invariants are anchored and enforced in the API. The app's domain/infrastructure layers are lean: they primarily manage in-app validation, local caching, and transport logic, never duplicating source-of-truth rules established server-side.

**Impacts:**

* Short-term: Immediate uptake by women seeking safer alternatives; strong word of mouth within communities.

* Long-term: Brand recognition as category leader, data-driven service improvements, expansion into new regions.

---

### Design Details

ChaufHER is engineered with a feature-first, layered architecture, promoting scalability and high maintainability:

* **Presentation Layer:** Handles UI rendering, state management (BLoC), user interaction.

* **Application Layer:** Contains use cases, coordination of business processes.

* **Domain Layer:** Encapsulates core business logic, entity models, repositories.

* **Infrastructure Layer:** Handles API calls, data persistence, third-party integrations.

**Module Structure:** Feature modules (e.g., `auth`, `rider`, `driver`, `safety`), each independent with well-defined interfaces to facilitate parallel engineering and code reuse.

**Folder Layout Example:**

```
lib/
 ├─ features/
 |    ├─ auth/
 |    ├─ rider/
 |    ├─ driver/
 |    ├─ safety/
 ├─ core/
 |    ├─ router/
 |    ├─ theme/
 |    ├─ utils/
 ├─ app.dart
```

### Layered Architecture

* **Feature modules** (e.g., Rider, Driver, Auth, Safety) encapsulate UI, logic, and data for clear ownership.
* **Shared core** for routing (GoRouter), common UI, and thematic components.
* **Presentation layer** (Flutter widgets), **BLoC** (state management), **Repositories** (domain logic), **Data providers** (API/DB).
* Asynchronous and reactive flows using Streams and Futures ensure responsiveness.
* Modules interact through well-defined contracts/interfaces, maximizing encapsulation and testability.

### Architecture Diagram

```
┌──────────────────────────────────────────────────────────────────────────────┐
│                          PRESENTATION LAYER                                  │
│   (Widgets, Screens, GoRouter, BLoC State Management, Navigation)            │
│  ┌─────────────────┬──────────────────┬─────────────────┬──────────────┐    │
│  │  Rider UI       │  Driver UI       │  Safety UI      │  Auth UI     │    │
│  │  - Booking      │  - Dashboard     │  - Panic Button │  - Login     │    │
│  │  - Trip Map     │  - Requests      │  - Live Track   │  - Register  │    │
│  │  - History      │  - Earnings      │  - SOS Chat     │  - Profile   │    │
│  └─────────────────┴──────────────────┴─────────────────┴──────────────┘    │
└──────────────────────────────────────────────────────────────────────────────┘
                                       ↕
┌──────────────────────────────────────────────────────────────────────────────┐
│                        APPLICATION LAYER (BLoC)                              │
│   (Use Cases, Coordination, Event Processing, State Orchestration)           │
│  ┌─────────────────┬──────────────────┬─────────────────┬──────────────┐    │
│  │ BookingBLoC     │ TripBLoC         │ SafetyBLoC      │ UserBLoC     │    │
│  │ - Validate      │ - Accept Request │ - Panic Trigger │ - Auth State │    │
│  │ - Search        │ - Track Location │ - Escalate      │ - Preferences│    │
│  │ - Request Ride  │ - Update Status  │ - Notifications │ - Profile    │    │
│  └─────────────────┴──────────────────┴─────────────────┴──────────────┘    │
└──────────────────────────────────────────────────────────────────────────────┘
                                       ↕
┌──────────────────────────────────────────────────────────────────────────────┐
│                          DOMAIN LAYER                                        │
│   (Entities, Repositories, Business Logic, Validation)                       │
│  ┌─────────────────┬──────────────────┬─────────────────┬──────────────┐    │
│  │ User Entity     │ Trip Entity      │ SafetyEvent     │ Payment      │    │
│  │ - Validate ID   │ - State Machine  │ - Rate-limit    │ - Validation │    │
│  │ - Role-based    │ - Route Calc     │ - Geo-cache     │ - Tokenize   │    │
│  │ - Repositories  │ - Fee Calc       │ - Retry Logic   │ - Secure     │    │
│  └─────────────────┴──────────────────┴─────────────────┴──────────────┘    │
└──────────────────────────────────────────────────────────────────────────────┘
                                       ↕
┌──────────────────────────────────────────────────────────────────────────────┐
│                       INFRASTRUCTURE LAYER                                   │
│   (Data Providers, API Client, Local Storage, External Integrations)         │
│  ┌─────────────────┬──────────────────┬─────────────────┬──────────────┐    │
│  │ HTTP Client     │ Local Storage    │ Location Svc    │ Notifications│    │
│  │ (REST/gRPC)     │ (SQLite/Secure)  │ (Geolocator)    │ (FCM)        │    │
│  │ Retry/Backoff   │ Cache Mgmt       │ Geofencing      │ Background   │    │
│  │ JWT Tokens      │ Offline Support  │ Permissions     │ Push Logic   │    │
│  └─────────────────┴──────────────────┴─────────────────┴──────────────┘    │
└──────────────────────────────────────────────────────────────────────────────┘
                                       ↕
                        ┌──────────────────────────┐
                        │   chaufher.api (Backend) │
                        │   - Auth Service         │
                        │   - Trip Orchestration   │
                        │   - Safety Escalation    │
                        │   - Payments             │
                        │   - Analytics            │
                        └──────────────────────────┘
```

**Data Flow Example (Booking a Ride):**

```
User Taps "Request Ride" 
    ↓
Presentation: BookingScreen emits RequestRideEvent
    ↓
BLoC: BookingBLoC validates input, checks cache
    ↓
Domain: RideRequestRepository checks eligibility
    ↓
Infrastructure: HTTP Client sends secure HTTPS request
    ↓
Backend API: Matches driver, returns confirmation
    ↓
Infrastructure: Caches response locally
    ↓
BLoC: Updates state with new trip
    ↓
Presentation: Shows live map, driver details, ETA
    ↓
SignalR WebSocket: Real-time location & status updates
```

**Folder Layout Snapshot:**

| Directory | Purpose |
|---|---|
| `lib/features/auth` | Authentication flows (UI, state, API) |
| `lib/features/rider` | Rider booking, dashboard, history |
| `lib/features/driver` | Driver dashboard, trip management |
| `lib/features/safety` | Panic, live tracking, safety events |
| `lib/core` | Shared routing, theming, error handling, utils |

---

## Domain Model and Data Flow

### Core Entity Models

- **User** (Rider/Driver): ID, role, name, profile, verification, preferences.
- **RideRequest**: Origin, destination, timestamp, status, rider ID.
- **Trip**: Start/end, assigned driver, status, route info, fare, safety flags.
- **PaymentMethod**: Card/token, billing info.
- **SafetyEvent**: Trip ID, type, timestamp, event status, geo-coordinates.

### Lifecycle Flows

- **Trip State Machine:** Pending → Accepted → En Route → In Progress → Completed/Cancelled.
- **Backoff/Retry:** For network/API calls (exponential backoff, failover triggers).
- **Caching:** Local stores (Secure Storage, SQLite) for ride history, offline data.

### Performance & Safety

- All entities extend base serializable models for easy migration/extension.
- Real-time updates of trip status and live location via SignalR/WebSocket streams.
- Safety logic prioritized—fast event dispatch, minimal UI-blocking I/O.

---

## External Interfaces

### API Communication

- **REST API:** Secure HTTPS (OpenAPI), JWT-based authentication, standard CRUD for user/trip management.
- **SignalR/WebSockets:** Bi-directional communication for real-time trip/location/safety event updates.

### Device Integration

- **Location Services:** Device GPS, background tracking, geofencing via platform-specific APIs.
- **Push Notifications:** Firebase Cloud Messaging (iOS/Android channels).
- **Camera:** KYC (ID, profile picture), incident recording.
- **Motion Sensors:** Crash/impact detection, aggressive driving alerts.

### Third-Party Services

- **Payments:** Stripe/PayPal SDKs, PCI-DSS-aligned token flows.
- **Secure Storage:** Flutter secure storage for tokens, credentials, PII.
- **Analytics & Monitoring:** Firebase, Sentry for crash reporting and performance tracking.

*All interface boundaries are formally defined; external dependencies are abstracted for stubbing/testing.*

---

## User Interface

### Key Flows

- **Onboarding:** User registration, onboarding tour, KYC/upload, permissions.
- **Rider Dashboard:** Map/search, ride request, live trip tracking, trip history.
- **Driver Dashboard:** Incoming requests, live tracking, balance, schedule.
- **Safety Flows:** Prominent safety button, live support chat/call, event feedback.
- **Payments:** Add payment methods, review trip charges, receipts.

### Accessibility & Safety

- Large interactive elements, high-contrast mode, voice prompts.
- Panic features always accessible; flows require minimal navigation.
- Wireframe examples referenced in project Figma repository.

| UI Component | Accessibility Alignment | Extensibility Example |
|---|---|---|
| Panic/Safety Button | Always on-screen, ARIA | Modular for feature expansion |
| Booking Flow | One-handed use, readable | Reusable across future themes |

### Permissions & Privacy

- **Permissions:** Managed on-demand, explaining context, revoked gracefully.
- **Device Abstraction:** All device data abstracted behind interfaces for dependency injection.
- **Privacy:** Only necessary data collected; no unnecessary storage or tracking.

### Flutter Plugins

- `geolocator` - GPS/location services
- `camera` - Camera access for KYC and incident recording
- `flutter_local_notifications` - Local notification handling

---

## Quality Assurance

### Test Strategy

A layered, automated QA strategy covering both rapid releases and critical real-time/safety scenarios:

- **Unit Tests:** Entity logic, business rules, state transitions (high coverage).
- **Widget Tests:** UI rendering, accessibility, navigation flows.
- **Integration Tests:** API comms, storage, device sensor integration.
- **End-to-End (E2E):** Full user journeys, with real-time trip flows and safety edge-cases.

**Primary Focus Areas:**
- Recovery from network failure
- Instant safety event triggers
- Payment reliability
- CI/CD integration for every PR and release

### Test Coverage

| Test Type | Justification | Focus Areas |
|---|---|---|
| Unit | Fast, catch regressions in logic/layers | Trip state, fee calculation, user logic |
| Widget | UI/UX consistency, edge/corner case coverage | Onboarding, dashboards, panic flow |
| Integration | Multi-module, async operations | Auth, API backoff, GPS, notifications |
| E2E | Simulate real-life flows | Booking, payment, safety escalation |
| Contract | API stability with backend | User login, trip sync, event handshake |

### Testing Tools

- **flutter_test** - Unit/widget test framework
- **mocktail** - Mock dependencies/services for isolation
- **integration_test** - UI flows and app-level behaviors
- **Firebase Test Lab** - Automated on-device testing (wide device matrix)
- **fastlane** - Automated build, deploy, screenshot capture
- **Allure/CI Dashboards** - Test report aggregation for stakeholders

These tools ensure broad device coverage, automate regression detection, and enable rapid feedback in CI pipelines.

### Test Environments

| Environment | Configuration | Purpose |
|---|---|---|
| **Dev** | Local, hot-reload, sandbox API | Development and debugging |
| **Staging** | Identical to production | UAT and pre-release testing |
| **Production** | Hardened infrastructure, secrets in vaults | Live user-facing deployment |

**Feature Flags:** Support for toggling features/flows per environment.

**Test Data & Monitoring:**
- Automated data resets
- Synthetic test accounts
- Real-time alerting (crash, performance, security events)

### Key Test Scenarios

| Area | Test Case | Expected Outcome |
|---|---|---|
| Onboarding | Registration w/ missing fields | Prompted for correction, cannot proceed |
| Booking | Rider requests ride, network drop | Retry logic kicks in, user notified gracefully |
| Trip State | Trip canceled mid-ride | State updated across both rider and driver views |
| Safety | Panic triggered with GPS loss | Partial event logged, fallback SMS/notification |
| Payment | Add invalid card | User alerted, API not called, input preserved |

These scenarios cover both normal and exception paths, validating resilient, safe, user-centric design.

### Metrics & Reporting

**Test Metrics:**
- Statement/branch coverage tracked automatically
- Pass/Fail rates aggregated per commit/release
- Trendlines shared with team

**Runtime Monitoring:**
- Crashes/Errors tracked via Sentry/Firebase with real-time dashboards
- Performance metrics: app launch, trip booking latency, UI response time
- Synthetic user flows replayed; failures auto-reported

**Stakeholder Communication:**
- Automated CI badges
- Weekly dashboards
- Incident summaries shared with product/engineering

---

## Deployment

### Deployment Strategy

Deployment is managed via robust mobile CI/CD pipelines, featuring phased rollouts (beta → gradual production), rigorous configuration management, and automation for consistency.

### Deployment Environments

| Environment | Infrastructure | Configuration | Availability |
|---|---|---|---|
| **Dev** | Local/emulated | .env.local, sandbox keys | Restart-on-change; low criticality |
| **Staging** | Cloud-based, secure | Vaulted keys, mirrors prod | Monitored; auto-scaling, UAT |
| **Production** | Redundant, cloud | Secrets vault, config maps | High-availability, DR nodes |

### Deployment Tools

- **GitHub Actions** - CI/CD pipeline for Flutter build/test
- **Azure DevOps** - Infrastructure as code (optional)
- **fastlane** - Build signing, beta deploy, screenshots, release automation
- **Play Console/App Store Connect** - Distribution, phased rollout, feedback ingestion

**Automation Policy:**
- Automated tests and staging deploys per PR
- Approval gating for production push

### Deployment Process

1. **Commit Merge** → Trigger CI pipeline (all tests must pass)
2. **Build Artifacts** → Generate signed builds via fastlane per environment
3. **Deploy to Staging** → Automated with smoke/E2E tests
4. **Manual UAT** → Operations and QA sign-off
5. **Production Release** → Phased rollout via app stores
6. **Validation Checkpoints:**
   - Crash/error monitoring live
   - Transaction logs for bookings/payments
7. **Rollback** → fastlane/app store rollback if failures detected

### Post-Deployment Verification

- **Monitoring:** Sentry/Firebase and custom alerts (crash, safety triggers, auth/API failures)
- **Smoke Testing:** Key flows validated by ops/QA after every deployment
- **Health Dashboards:** Real-time user metrics, incident reports
- **Rollback Triggers:** Automated/manual upon exceeding failure thresholds

### Continuous Deployment Practice

**Approach:**
- Automated tagged builds for every main-branch merge
- Feature branching for isolation
- Beta channels for pre-release feedback

**Automation:**
- Custom scripts + fastlane for build signing, environment config, versioning
- Semantic versioning with auto-incremented builds
- Expedited pipeline for zero-day hotfixes

**Benefits:**
- Rapid delivery with reduced manual overhead
- Early issue discovery through automated testing
- Robust audit trail for compliance and debugging
- Modular codebase with up-to-date dependencies
- Continuous security patching and adaptive monitoring

This enables sustainable and scalable CD practices.

---

## Advanced Safety/Panic Flow – App Role

### User Flow and App Boundaries

The panic button is always visible during ride/active-tracking UI states—for both rider and driver.

When activated, the app:

* Immediately presents a confirmation modal with context ("Are you safe? Dispatch safety event?") to minimize accidental triggers.
* On confirmation, the Presentation Layer forwards a TriggerSafetyEvent action to the Application Layer BLoC for orchestration.

### App Layer Technical Handling

The Application Layer:

* Records a local event timestamp + location (using last known or immediate GPS pull).
* Performs rapid in-app eligibility checks (rate-limiting, duplicate-prevention, local abuse heuristics).
* Optimistically updates the UI ("Safety event active", disables duplicate panic).
* Assembles an Event DTO and passes it to the Infrastructure Layer repository for transport.

### Failure and Fallback

The Infrastructure Layer:

* Attempts primary dispatch to the API via secure HTTPS (fail-fast).
* If the dispatch fails (offline/mobile data loss):
  * Backs up the safety event (full DTO) to encrypted local storage.
  * Attempts fallback dispatch—e.g., via SMS to a preconfigured "fallback" number, strictly if user opts in at onboarding.
  * Schedules a background retry job (using local notification/reminder) as soon as network resumes.
* UI continues to communicate to the user whether the safety event is still pending, sent, or failed. User cannot "clear" a pending safety action.

### Escalation & Integration (API Handoff)

Upon API acknowledgement:

* The app securely erases the local cached safety event.
* UI tracks live escalation via server-push (FCM/SignalR), updating with any new escalations or interventions pushed from ops.

If rejected by the API (throttling, session invalid, expired credentials):

* UI forces re-login, disables further panic triggers, and surfaces a user-recoverable error overlay.
* Flags for QA/ops review.

### Anti-abuse Controls (App)

* Rate-limiting is enforced locally (e.g., cannot trigger another safety event within X minutes unless current is resolved).
* Patterns of rapid or repeated panic usage are only shadow-logged on-device; the app does not lock users out but can surface soft warnings.
* True abuse patterns (detected API-side) are never remediated directly in-app—enforcement must be managed centrally.

### Privacy

Location/metadata for safety is acquired only in context of active trip; auto-scrubbed after event is resolved (never persisted in app beyond API ACK).

### Boundary Callout

All audit and intervention logic, incident assignment, and contact of emergency services is handled API-side and is not the app's responsibility.

### Example Scenarios

**Scenario 1: Normal Safety Event Escalation (Happy Path)**

```
Timeline: T+0s
User is in active ride with Driver (Mark)
- Location: Downtown Seattle, I-5 North
- Trip Status: In Progress
- Duration: 8 minutes elapsed

T+0s: User taps Panic Button
- Confirmation modal appears: "Are you safe? This will alert support."
- User confirms (taps "Yes, Get Help")

T+1s: SafetyBLoC processes TriggerSafetyEvent
- Validates user is in active trip ✓
- Checks rate limit (no prior events in last 2 min) ✓
- Pulls current GPS location: (47.6205, -122.3493)
- Records timestamp, trip_id, user_id
- Updates UI: "Safety event active... notifying support"
- Disables panic button (prevents duplicate triggers)

T+2s: Infrastructure layer sends HTTPS request to /api/safety-events
- Payload: { trip_id, user_id, location, timestamp, device_info }
- SSL/TLS pinning verified ✓
- API responds: 200 OK, event_id: "evt_12345"

T+3s: API-side escalation begins
- Support team receives alert in ops dashboard
- FCM push sent to rider: "Support is reviewing your case"
- SignalR establishes live connection for real-time updates

T+10s: Support reviews, dispatches local authorities
- Rider receives FCM: "Police dispatch en route, ETA 4 minutes"
- Driver is also notified (if applicable for shared safety)
- App shows escalation timeline in UI

T+25s: Authorities arrive, issue resolved
- Support marks incident as "Resolved"
- FCM push to rider: "Your safety incident is closed"
- App auto-scrubs location data from local storage
- Panic button re-enabled for future trips

Result: Safety event fully logged, investigated, and resolved.
```

**Scenario 2: Offline Safety Event with Fallback**

```
Timeline: T+0s
User is in active ride
- Network status: NO DATA (offline)
- Rider taps panic button, confirms

T+1s: App attempts HTTPS dispatch
- Request queued, but network unreachable ✗
- Error caught by Infrastructure layer

T+2s: Fallback mechanism activates
- Safety event backed up to encrypted local storage
- App checks onboarding preference: "Allow SMS fallback?" → YES
- SMS message queued: "ChaufHER Safety Alert: Trip ID xyz. Please confirm."
- Target: Emergency contact number (pre-configured)

T+5s: Network resumes
- Device reconnects to cellular/WiFi
- Background job trigger fires (WorkManager on Android, BGTaskScheduler on iOS)
- App retries primary HTTPS dispatch with backed-up event
- API acknowledges: 200 OK

T+10s: Local event cache cleaned
- Encrypted storage cleared of safety event data
- User UI updated: "Safety event delivered. Support is reviewing."

Result: Even offline, safety event was captured and transmitted.
```

**Scenario 3: Rate-Limited Panic (Abuse Prevention)**

```
Timeline: T+0s
User triggers panic, event sent successfully

T+30s: User taps panic again
- SafetyBLoC checks rate limit: "Safety events limited to 1 per 2 minutes"
- Current penalty: 90 seconds remaining
- Modal shown: "You can trigger another safety event in 90 seconds"
- Request blocked, not sent to API ✗
- Shadow log on device: "Rapid panic attempt detected"

T+120s: Rate limit expires
- Panic button re-enabled
- User can trigger another safety event if needed

Result: Prevents accidental/malicious multiple triggers while preserving user access.