# ChaufHER App Technical Design

## Table of Contents

1. [Infrastructure](#infrastructure)
2. [Quick Start](#quick-start)
3. [Development Setup](#development-setup)
4. [Product Overview](#product-overview)
5. [Purpose](#purpose)
6. [Target Audience](#target-audience)
7. [Expected Outcomes](#expected-outcomes)
8. [Technical Architecture](#technical-architecture)
9. [Domain Model and Data Flow](#domain-model-and-data-flow)
10. [External Interfaces](#external-interfaces)
11. [User Interface](#user-interface)
12. [Quality Assurance](#quality-assurance)
13. [Deployment](#deployment)
14. [Advanced Safety/Panic Flow](#advanced-safetypanic-flow--app-role)
15. [Multi-Repo Architecture](#multi-repo-architecture)
16. [Development Guidelines](#development-guidelines)
17. [Security Guidelines](#security-guidelines)
18. [Troubleshooting](#troubleshooting)
19. [Contributing](#contributing)
20. [Related Repositories](#related-repositories)

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
```

---

## Multi-Repo Architecture

The ChaufHER platform is a coordinated multi-repository ecosystem:

```
                    chaufher-workspace (entry point, shared docs)
                            |
        ┌───────────────────┼───────────────────┐
        |                   |                   |
   chaufher-app       chaufher-web         chaufher-api
 (Flutter mobile)     (React admin)       (.NET backend)
        |                   |                   |
        └───────────────────┼───────────────────┘
                            |
                     chaufher-infra
                  (Azure IaC, CI/CD)
```

**Repository Overview:**

- **[chaufher-workspace](https://github.com/phoenixvc/chaufher-workspace)** – Monorepo coordination, shared documentation, local development setup
- **chaufher-app** (this repo) – Flutter mobile client for riders/drivers; consumes REST APIs and SignalR hubs
- **[chaufher-web](https://github.com/phoenixvc/chaufher-web)** – React admin portal for real-time oversight, incident escalation, compliance reporting
- **[chaufher-api](https://github.com/phoenixvc/chaufher-api)** – .NET 9 backend; provides REST APIs, SignalR hubs, business logic
- **[chaufher-infra](https://github.com/phoenixvc/chaufher-infra)** – Azure IaC (Bicep/Terraform), CI/CD pipelines, deployment automation

All repositories are independently version-controlled but designed for seamless integration via well-defined API contracts.

---

## Development Guidelines

### Naming Conventions

**File & Folder Structure:**
- **Features:** `lib/features/{feature_name}/` (e.g., `lib/features/rider/`, `lib/features/safety/`)
- **Screens:** PascalCase, suffix with `Screen` (e.g., `BookingScreen.dart`, `RideMapScreen.dart`)
- **BLoC Files:** PascalCase, suffix with `BLoC` or `Event`/`State` (e.g., `SafetyBLoC.dart`, `SafetyEvent.dart`)
- **Models/Entities:** PascalCase (e.g., `User.dart`, `Trip.dart`, `SafetyEvent.dart`)
- **Utilities/Helpers:** camelCase (e.g., `gpsHelper.dart`, `locationTracker.dart`)
- **Constants:** UPPER_SNAKE_CASE (e.g., `RATE_LIMIT_MINUTES`, `API_TIMEOUT_SECONDS`)
- **Variables:** camelCase (e.g., `currentLocation`, `driverId`, `tripStatus`)

**Code Patterns:**
- Use `final` instead of `var` for immutability
- Avoid single-letter variable names (except loop counters)
- Use meaningful error messages and descriptive variable names

### Git Workflow

**Branching Strategy:**
- `main` – Production-ready code
- `feature/your-feature` – Feature branches
- `fix/issue-description` – Bugfix branches
- `chore/task-description` – Non-code changes

**Commit Conventions:**

Use conventional commits:
```
<type>(<scope>): <subject>

<body>

<footer>
```

Types: `feat`, `fix`, `refactor`, `test`, `docs`, `chore`, `style`

Examples:
```
feat(safety): add panic event escalation with local rate limiting
fix(auth): resolve JWT token refresh race condition on network resume
docs(setup): add iOS build instructions for new developers
```

**Pull Request Process:**
1. Create feature branch from `main`
2. Push and open PR with descriptive title and description
3. All checks must pass (tests, linting, code review)
4. Address feedback from reviewers
5. Squash and merge on approval
6. Delete branch after merge

### Code Quality Standards

- **Linting:** `flutter analyze` (enforced in CI)
- **Formatting:** `dart format .` (standardized code style)
- **Test Coverage:** Minimum 80% for critical business logic (safety, auth, payments)
- **Complexity:** Avoid methods exceeding 50 lines; break into smaller functions
- **Async Patterns:** Use `async/await` consistently; avoid callback hell

### Testing Requirements

**Unit Tests:**
```bash
# Run all unit tests
flutter test

# Run specific test file
flutter test test/features/safety/bloc_test.dart

# Generate coverage report
flutter test --coverage
```

**Widget Tests:**
```bash
# Run widget tests
flutter test test/features/rider/screens

# Test specific widget
flutter test test/features/rider/screens/booking_screen_test.dart
```

**Integration Tests:**
```bash
# Run integration tests
flutter test integration_test/

# Test on specific device
flutter test -d emulator-5554 integration_test/
```

---

## Security Guidelines

### Secrets Management

**What NOT to commit:**
- `.env` files with API keys, tokens, or secrets
- Firebase service account JSON files
- Private certificates or signing keys
- Database credentials or connection strings
- Any hardcoded API keys or webhook tokens

**Secure Practices:**
- Store all secrets in **Azure Key Vault** (per environment)
- Use environment variables at runtime (never compile secrets)
- Fetch credentials securely at app startup via dependency injection
- Rotate credentials every 90 days (automated via infra)
- Use managed identities for service-to-service authentication

**Example (WRONG - never do this):**
```dart
// ❌ WRONG: Hardcoded secret
const String API_KEY = "sk_live_abc123xyz789";
```

**Example (CORRECT):**
```dart
// ✅ CORRECT: Fetch from environment at runtime
final String apiKey = Platform.environment['API_KEY'] ?? '';
if (apiKey.isEmpty) throw Exception('API_KEY environment variable not set');
```

### Code Security Patterns

**Input Validation:**
```dart
// Validate all user inputs before processing
if (location == null || location.latitude == null) {
  logger.error('Invalid location received');
  return SafetyEventFailure();
}
```

**SQL Injection Prevention:**
- Use ORM or parameterized queries (SQLite via sqflite)
- Never concatenate user input into SQL strings

**Network Security:**
- Always use HTTPS with certificate pinning for critical APIs
- Validate SSL/TLS certificates
- Never transmit sensitive data in query parameters

**Secure Storage:**
- Use `flutter_secure_storage` for tokens, credentials, encryption keys
- Never store passwords (use tokens/OAuth)
- Encrypt sensitive local data at rest

**Logging Security:**
- Never log passwords, API keys, or personal information
- Log events/audit trails for security-critical actions
- Sanitize PII (email, phone, location) in logs

---

## Troubleshooting

### Common Setup Issues

**Flutter not found in PATH**
```bash
# Verify Flutter installation
flutter --version

# Add Flutter to PATH (if needed)
export PATH="$PATH:$HOME/flutter/bin"
export PATH="$PATH:$HOME/flutter/bin/cache/dart-sdk/bin"
```

**Android SDK/NDK not found**
```bash
# Configure Android SDK
flutter config --android-sdk /path/to/android/sdk
flutter config --android-ndk /path/to/android/ndk

# Verify setup
flutter doctor
```

**iOS setup issues (macOS only)**
```bash
# Install CocoaPods
sudo gem install cocoapods

# Update iOS pods
cd ios && pod repo update && pod install && cd ..

# Clean build
flutter clean
flutter pub get
flutter run
```

**Gradle build fails**
```bash
# Clear Gradle cache
rm -rf ~/.gradle/caches/
rm -rf android/.gradle

# Rebuild
flutter clean
flutter pub get
flutter run
```

**Emulator/device not found**
```bash
# List available devices
flutter devices

# Start Android emulator
emulator @pixel_4_api_30

# Connect physical device via USB
adb devices
```

**Hot reload not working**
```bash
# Restart the development server
flutter run

# Full rebuild if persistent
flutter clean && flutter run
```

**API calls fail with certificate pinning error**
```bash
# Verify certificate is installed correctly in infrastructure
# Check Azure Key Vault has correct certificate
# Ensure API_URL in .env matches certificate CN/SAN
```

**Panic button not triggering safety event**
```bash
# Check network connectivity
# Verify rate limit: can only trigger once per X minutes
# Ensure trip is in active state (not completed)
# Review logs: flutter logs | grep -i safety
```

### Getting Help

- Check [chaufher-workspace](https://github.com/phoenixvc/chaufher-workspace) for project-wide documentation
- Review [chaufher-api](https://github.com/phoenixvc/chaufher-api) for backend API issues
- Consult [chaufher-infra](https://github.com/phoenixvc/chaufher-infra) for infrastructure/deployment questions
- Open a GitHub issue with:
  - Flutter version (`flutter --version`)
  - Device/emulator info (OS, Android/iOS version)
  - Error logs and stack trace
  - Steps to reproduce
  - Attempted fixes

---

## Contributing

### Before You Commit

- [ ] Code follows naming conventions and style guidelines
- [ ] All tests pass locally: `flutter test`
- [ ] Linting passes: `flutter analyze`
- [ ] Code is formatted: `dart format .`
- [ ] No secrets or credentials in commit
- [ ] Commit message follows conventional format
- [ ] Branch is up-to-date with `main`

### Submitting a Pull Request

1. **Create feature branch:**
   ```bash
   git checkout -b feature/your-feature-name
   ```

2. **Make changes and commit:**
   ```bash
   git add .
   git commit -m "feat(scope): descriptive message"
   ```

3. **Push and open PR:**
   ```bash
   git push origin feature/your-feature-name
   ```

4. **PR Description:**
   Include:
   - What problem does this solve?
   - How did you test?
   - Any breaking changes?
   - Related issues/PRs

5. **Code Review:**
   - Address feedback from reviewers
   - Re-request review after changes
   - Ensure all checks pass (tests, lint, build)

6. **Merge:**
   - Squash and merge on approval
   - Delete branch after merge

### Code Review Checklist (For Reviewers)

- [ ] Code follows naming and style conventions
- [ ] Tests are comprehensive and passing
- [ ] No security vulnerabilities (secrets, validation, etc.)
- [ ] Accessibility standards met (WCAG 2.1 AA)
- [ ] Documentation updated if needed
- [ ] Changes align with technical architecture

---

## Related Repositories

ChaufHER App is part of a multi-repository workspace. For coordination, shared docs, and cross-repo setup, start with:

- **[chaufher-workspace](https://github.com/phoenixvc/chaufher-workspace)** – Monorepo entry point and shared documentation
- **[chaufher-api](https://github.com/phoenixvc/chaufher-api)** – Backend REST APIs, SignalR hubs, business logic
- **[chaufher-web](https://github.com/phoenixvc/chaufher-web)** – React admin portal for operations and compliance
- **[chaufher-infra](https://github.com/phoenixvc/chaufher-infra)** – Azure infrastructure as code, CI/CD pipelines, deployment automation

### Key Integration Points

**API Contract:**
- All REST endpoints documented in [chaufher-api](https://github.com/phoenixvc/chaufher-api)
- SignalR hubs for real-time ride/safety event updates
- See `MOBILE_CHECKLIST.md` in chaufher-infra for mobile-specific contracts

**Authentication:**
- JWT-based auth (OAuth2 / Azure AD B2C)
- Tokens fetched securely; never hardcoded
- Refresh tokens before expiry; 401 responses trigger re-login

**Environment Parity:**
- Dev, Staging, Production environments mirror each other
- Use correct API URL for each environment (.env configuration)
- Feature flags support staged rollouts

**Incident Response:**
- Safety events escalated to backend ops team
- App surfaces minimal data; backend handles investigation
- Rate limiting and fallback SMS handled cooperatively

---

## License

[Add License Here — e.g., MIT, Apache 2.0, or proprietary]

Copyright (c) 2025 ChaufHER. All rights reserved.

## Acknowledgments

This repository is part of the ChaufHER platform, a women-only ridesharing application prioritizing safety, privacy, and reliability. Special thanks to the DevOps, Backend, and Product teams for coordinated, multi-repo engineering excellence.