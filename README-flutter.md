# ChaufHER App — Technical Design

A women-only ridesharing mobile application built with Flutter, prioritizing safety, privacy, and reliability for riders and drivers.

---

## Table of Contents

### Business & Product
- [Product Overview](#product-overview)
- [Purpose & Value](#purpose--value)
- [Target Audience](#target-audience)
- [Expected Outcomes](#expected-outcomes)
- [Multi-Repo Architecture](#multi-repo-architecture)

### Architecture & Design
- [Technical Architecture](#technical-architecture)
- [Design Philosophy](#design-philosophy)
- [Core Flows](#core-flows)
- [Domain Model](#domain-model)

### Integration
- [External Interfaces](#external-interfaces)
- [Related Repositories](#related-repositories)
- [Infrastructure](#infrastructure)

### Developer Guide
- [Quick Start](#quick-start)
- [Development Setup](#development-setup)
- [Development Guidelines](#development-guidelines)
- [Testing & Quality](#testing--quality)
- [Deployment](#deployment)
- [Security Guidelines](#security-guidelines)
- [Troubleshooting](#troubleshooting)

### Reference
- [User Interface](#user-interface)
- [Contributing](#contributing)
- [License](#license)

---

# Business & Product

## Product Overview

ChaufHER is a women-only ridesharing platform engineered to address critical safety, privacy, and reliability concerns in traditional ridesharing. The mobile app serves as the primary interface for riders and drivers, delivering a trusted transportation experience exclusively for women.

### Core Value Proposition

- **Safety First**: Advanced panic features, real-time tracking, and instant incident escalation
- **Women-Only Network**: Pre-verified female drivers and riders create a trusted community
- **Privacy Protected**: Minimal data collection with secure, encrypted storage
- **Always Reliable**: Offline support, fallback mechanisms, and robust error handling

### Key Capabilities

| Feature | Rider Experience | Driver Experience |
|---------|-----------------|-------------------|
| **Booking** | One-tap ride requests with live ETA | Incoming request notifications with route preview |
| **Safety** | Always-visible panic button, live tracking | Panic access, incident reporting, support chat |
| **Payments** | Secure card storage, transparent pricing | Real-time earnings, instant payouts |
| **Communication** | In-app chat, call masking | Rider contact without exposing personal numbers |
| **History** | Trip receipts, ratings, favorites | Performance metrics, earnings history |

---

## Purpose & Value

### Business Problems Solved

1. **Safety Concerns**: Reduces harassment and security incidents through women-only network
2. **Market Gap**: Serves underserved female demographic seeking trusted transportation
3. **Trust Deficit**: Builds confidence through verification, ratings, and safety features
4. **Economic Opportunity**: Provides flexible income for female drivers in safe environment
5. **Community Need**: Addresses cultural preferences for gender-specific services

### Technical Challenges Addressed

1. **Real-Time Coordination**: Instant updates between riders, drivers, and operations
2. **Offline Resilience**: Critical safety features work without network connectivity
3. **Data Privacy**: Minimal PII collection with secure storage and transmission
4. **Cross-Platform Consistency**: Single Flutter codebase for iOS and Android
5. **Scalability**: Modular architecture supports future feature expansion

### Example Scenarios

**Scenario 1: Late-Night Commute**
- Female professional books ride at 11 PM after work
- Sees driver profile, photo, rating, and vehicle details before accepting
- Shares live trip tracking with emergency contacts
- Arrives safely with in-app payment (no cash exchange)

**Scenario 2: Safety Incident**
- Rider feels uncomfortable during trip
- Taps panic button (always visible, one-tap access)
- App immediately alerts operations team with location
- Support calls rider, dispatches help if needed
- Incident logged for investigation and follow-up

**Scenario 3: Driver Earnings**
- Driver logs in during morning commute hours
- Accepts ride requests based on route preferences
- Tracks real-time earnings and trip count
- Receives instant payout at end of day
- Reviews performance metrics and rider feedback

---

## Target Audience

### Primary Users

#### Riders
- **Demographics**: Women aged 18-60+, urban/suburban areas
- **Personas**: Students, professionals, mothers, elderly
- **Needs**: Safe, reliable transportation; transparent pricing; easy booking
- **Pain Points**: Safety concerns, harassment, lack of trust in traditional services

#### Drivers
- **Demographics**: Female drivers seeking flexible income
- **Personas**: Part-time workers, students, caregivers, retirees
- **Needs**: Safe work environment, fair compensation, flexible schedule
- **Pain Points**: Safety risks, unpredictable earnings, lack of support

### Secondary Users

#### Operations/Support
- **Role**: Monitor safety events, handle escalations, manage compliance
- **Needs**: Real-time dashboards, incident management tools, communication channels
- **Tools**: Admin portal (chaufher-web), incident tracking, user management

### Market Segments

- **Metropolitan Areas**: High professional female population, dense urban centers
- **University Towns**: Student populations, campus commutes, late-night safety
- **Cultural Communities**: Regions with preferences for gender-specific services
- **Suburban Families**: Mothers, caregivers needing reliable transportation

---

## Expected Outcomes

### Business Impact

**Short-Term (0-6 months):**
- Strong word-of-mouth adoption within target communities
- Reduced incident rates compared to traditional ridesharing
- High user satisfaction and retention

**Long-Term (6-24 months):**
- Category leadership in women-focused transportation
- Geographic expansion to new markets
- Data-driven service improvements
- Brand recognition and trust

### Key Performance Indicators

| Metric | Short-Term Target | Long-Term Goal | Measurement |
|--------|-------------------|----------------|-------------|
| **Weekly Active Users** | 2,000+ | 25,000+ | Analytics dashboard |
| **Ride Completion Rate** | >95% | >98% | Trip status tracking |
| **Safety Incidents** | <0.1% of trips | <0.05% of trips | Incident reports |
| **App Crash Rate** | <1% | <0.2% | Firebase Crashlytics |
| **User Satisfaction (NPS)** | >60 | >80 | In-app surveys |
| **Driver Retention** | >70% monthly | >85% monthly | Active driver tracking |
| **Average Rating** | >4.5/5 | >4.8/5 | Rating system |

---

## Multi-Repo Architecture

ChaufHER is a coordinated multi-repository ecosystem:

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

### Repository Responsibilities

| Repository | Purpose | Technology | Integration Points |
|------------|---------|------------|-------------------|
| **chaufher-workspace** | Monorepo coordination, shared docs | Markdown, scripts | All repos |
| **chaufher-app** (this repo) | Mobile client for riders/drivers | Flutter/Dart | chaufher-api, chaufher-infra |
| **chaufher-web** | Admin portal for operations | React/TypeScript | chaufher-api |
| **chaufher-api** | Backend services, business logic | .NET 9, PostgreSQL | chaufher-infra |
| **chaufher-infra** | Infrastructure as code, CI/CD | Bicep/Terraform | All repos |

### Integration Overview

- **API Contracts**: REST endpoints and SignalR hubs defined in chaufher-api
- **Authentication**: Shared JWT tokens from Azure AD B2C
- **Real-Time**: SignalR WebSocket connections for live updates
- **Deployment**: Infrastructure managed by chaufher-infra
- **Monitoring**: Shared Application Insights workspace

---

# Architecture & Design

## Technical Architecture

ChaufHER uses a feature-first, layered architecture built on Flutter, emphasizing modularity, testability, and separation of concerns.

### Architectural Principles

**Core Principle**: Domain and infrastructure responsibilities are delegated to chaufher-api. The mobile app focuses on orchestration, state management, and user interface. Business rules, persistence, and system-wide invariants are enforced server-side.

**App Responsibilities**:
- UI rendering and user interaction
- Local state management (BLoC pattern)
- Client-side validation and caching
- Device integration (GPS, camera, sensors)
- Offline support and retry logic

**API Responsibilities**:
- Business logic and domain rules
- Data persistence and consistency
- Payment processing and verification
- Safety event escalation and routing
- Analytics and reporting

### Layer Architecture

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
│  │ (REST/SignalR)  │ (SQLite/Secure)  │ (Geolocator)    │ (FCM)        │    │
│  │ Retry/Backoff   │ Cache Mgmt       │ Geofencing      │ Background   │    │
│  │ JWT Tokens      │ Offline Support  │ Permissions     │ Push Logic   │    │
│  └─────────────────┴──────────────────┴─────────────────┴──────────────┘    │
└──────────────────────────────────────────────────────────────────────────────┘
                                       ↕
                        ┌──────────────────────────┐
                        │   chaufher-api (Backend) │
                        │   - Auth Service         │
                        │   - Trip Orchestration   │
                        │   - Safety Escalation    │
                        │   - Payments             │
                        │   - Analytics            │
                        └──────────────────────────┘
```

### Folder Structure

```
lib/
 ├─ features/
 │   ├─ auth/
 │   │   ├─ presentation/    # Screens, widgets
 │   │   ├─ application/     # BLoC, events, states
 │   │   ├─ domain/          # Entities, repositories
 │   │   └─ infrastructure/  # API clients, storage
 │   ├─ rider/
 │   │   ├─ presentation/
 │   │   ├─ application/
 │   │   ├─ domain/
 │   │   └─ infrastructure/
 │   ├─ driver/
 │   ├─ safety/
 │   └─ payments/
 ├─ core/
 │   ├─ router/              # GoRouter configuration
 │   ├─ theme/               # App theming, colors, typography
 │   ├─ utils/               # Helpers, extensions
 │   ├─ config/              # Environment configuration
 │   └─ widgets/             # Shared UI components
 └─ app.dart                 # App initialization
```

---

## Design Philosophy

### Core Principles

#### 1. Feature-First Architecture
- **Modular Design**: Each feature is self-contained with clear boundaries
- **Parallel Development**: Teams can work on features independently
- **Code Reuse**: Shared components in core/ directory
- **Scalability**: Easy to add new features without refactoring

#### 2. Separation of Concerns
- **Presentation**: UI rendering, user interaction (Flutter widgets)
- **Application**: State management, use case orchestration (BLoC)
- **Domain**: Business entities, validation rules (pure Dart)
- **Infrastructure**: External integrations, storage, APIs

#### 3. Testability
- **Unit Tests**: Pure domain logic without dependencies
- **Widget Tests**: UI components in isolation
- **Integration Tests**: Full feature flows with mocked APIs
- **E2E Tests**: Critical user journeys on real devices

#### 4. Offline-First
- **Local Caching**: SQLite for trip history, user preferences
- **Retry Logic**: Exponential backoff for failed API calls
- **Fallback Mechanisms**: SMS for critical safety events
- **Sync on Resume**: Automatic sync when network returns

#### 5. Safety-First
- **Always Accessible**: Panic button visible during active trips
- **Fast Response**: Minimal latency for safety event triggers
- **Redundancy**: Multiple escalation paths (API, SMS, local notification)
- **Privacy**: Location data auto-scrubbed after event resolution

### Design Patterns

| Pattern | Usage | Benefit |
|---------|-------|---------|
| **BLoC** | State management | Predictable state, testable, reactive |
| **Repository** | Data access abstraction | Testability, flexibility, caching |
| **Dependency Injection** | Service locator (GetIt) | Loose coupling, testability |
| **Strategy** | Payment methods, pricing | Extensibility, A/B testing |
| **Observer** | Real-time updates (SignalR) | Decoupling, async processing |
| **State Machine** | Trip lifecycle | Clear transitions, validation |

---

## Core Flows

### Ride Booking Flow

```
1. Rider opens app, sees map with current location
   ↓
2. Taps "Where to?" and enters destination
   ↓
3. App shows fare estimate, available drivers
   ↓
4. Rider confirms booking
   ↓
5. BookingBLoC validates input, checks cache
   ↓
6. API matches driver, returns confirmation
   ↓
7. App shows driver details, live location, ETA
   ↓
8. Driver arrives → Trip state: EN_ROUTE
   ↓
9. Trip starts → Trip state: IN_PROGRESS
   ↓
10. Trip completes → Payment captured
    ↓
11. Rating/feedback screen
```

### Safety Event Flow

```
User taps panic button
   ↓
Confirmation modal: "Are you safe? Dispatch safety event?"
   ↓
User confirms
   ↓
SafetyBLoC records timestamp + location
   ↓
Rate limit check (prevent duplicate triggers)
   ↓
UI updates: "Safety event active... notifying support"
   ↓
Infrastructure layer sends HTTPS request to API
   ↓
If offline:
   - Backup event to encrypted local storage
   - Attempt SMS fallback (if user opted in)
   - Schedule background retry
   ↓
API acknowledges → App clears local cache
   ↓
SignalR updates: Real-time escalation status
   ↓
Support team reviews, dispatches help
   ↓
Incident resolved → UI notified
   ↓
Location data auto-scrubbed
```

### Driver Request Flow

```
Driver logs in, sets availability
   ↓
TripBLoC subscribes to incoming requests (SignalR)
   ↓
New ride request arrives
   ↓
App shows rider details, pickup location, fare
   ↓
Driver accepts or declines
   ↓
If accepted:
   - API assigns trip
   - Navigation starts to pickup location
   - Rider notified with driver details
   ↓
Driver arrives → Taps "Start Trip"
   ↓
Trip in progress → Live location updates
   ↓
Driver completes trip → Earnings updated
   ↓
Rating screen for rider
```

---

## Domain Model

### Core Entities

**User (Rider/Driver)**
- ID, role (rider/driver), name, email, phone
- Profile photo, verification status
- Preferences (payment methods, emergency contacts)
- Rating, trip count, join date

**Trip**
- ID, status (pending, accepted, en-route, in-progress, completed, canceled)
- Rider ID, driver ID
- Pickup/dropoff locations, route
- Start/end timestamps
- Fare, payment method
- Safety flags, incident references

**SafetyEvent**
- ID, trip ID, user ID
- Type (panic, harassment, accident, other)
- Timestamp, location (lat/lon)
- Status (pending, escalated, resolved)
- Escalation level, assigned ops team

**PaymentMethod**
- ID, user ID
- Type (card, wallet, bank)
- Token (encrypted), last 4 digits
- Expiry, billing address

### State Machines

**Trip Lifecycle:**
```
PENDING → ACCEPTED → EN_ROUTE → IN_PROGRESS → COMPLETED
                                                 ↓
                                              CANCELED
```

**Safety Event:**
```
TRIGGERED → ACKNOWLEDGED → ESCALATED → RESOLVED
                              ↓
                          DISMISSED (false alarm)
```

---

# Integration

## External Interfaces

### API Communication

**REST API (chaufher-api):**
- Base URL: Environment-specific (dev/staging/prod)
- Authentication: JWT bearer tokens (OAuth2/Azure AD B2C)
- Endpoints: Users, trips, safety events, payments, notifications
- Error Handling: Standardized error responses with correlation IDs
- Retry Logic: Exponential backoff for transient failures

**SignalR (Real-Time):**
- Hub: `/hubs/rideevents`
- Events: `ride.updated`, `safety.event.update`, `driver.location.updated`
- Connection: Persistent WebSocket with automatic reconnection
- Fallback: Long-polling if WebSocket unavailable

### Device Integration

| Service | Plugin | Purpose | Permissions |
|---------|--------|---------|-------------|
| **Location** | `geolocator` | GPS tracking, geofencing | Location (always for drivers) |
| **Camera** | `camera` | KYC, incident recording | Camera |
| **Notifications** | `firebase_messaging` | Push notifications | Notifications |
| **Storage** | `flutter_secure_storage` | Encrypted local storage | Storage |
| **Sensors** | `sensors_plus` | Crash detection, motion | Sensors |

### Third-Party Services

- **Payments**: Stripe/PayPal SDKs (PCI-DSS compliant)
- **Analytics**: Firebase Analytics, Crashlytics
- **Monitoring**: Sentry for error tracking
- **Maps**: Google Maps SDK (iOS/Android)

---

## Related Repositories

| Repository | Purpose | Link |
|------------|---------|------|
| **chaufher-workspace** | Monorepo entry point, shared docs | [GitHub](https://github.com/phoenixvc/chaufher-workspace) |
| **chaufher-api** | Backend REST APIs, SignalR hubs | [GitHub](https://github.com/phoenixvc/chaufher-api) |
| **chaufher-web** | React admin portal | [GitHub](https://github.com/phoenixvc/chaufher-web) |
| **chaufher-infra** | Azure IaC, CI/CD pipelines | [GitHub](https://github.com/phoenixvc/chaufher-infra) |

### Cross-Repo Integration

**API Contracts:**
- REST endpoints documented in chaufher-api
- SignalR hub contracts for real-time events
- See chaufher-api OpenAPI specs for details

**Infrastructure:**
- Deployment managed by chaufher-infra
- Environment variables via Azure Key Vault
- See [chaufher-infra/MOBILE_CHECKLIST.md](https://github.com/phoenixvc/chaufher-infra/blob/main/MOBILE_CHECKLIST.md)

---

## Infrastructure

This project relies on centralized infrastructure managed in **[chaufher-infra](https://github.com/phoenixvc/chaufher-infra)**.

For mobile-specific environment variables, API contracts, and deployment checklists, see:
**[chaufher-infra/MOBILE_CHECKLIST.md](https://github.com/phoenixvc/chaufher-infra/blob/main/MOBILE_CHECKLIST.md)**

---

# Developer Guide

## Quick Start

### Prerequisites

| Tool | Version | Purpose |
|------|---------|---------|
| **Flutter SDK** | 3.0+ | Build and run the app |
| **Dart SDK** | 3.0+ | Programming language |
| **Android SDK** | API 28+ | Android builds |
| **Xcode** | 13+ | iOS builds (macOS only) |
| **Git** | Latest | Version control |

### Get Started in 3 Steps

**1. Clone & Install**

**2. Configure Environment**
- Copy `.env.example` to `.env` (contact team for values)
- Update API endpoints in `lib/core/config/env_config.dart`

**3. Run Locally**

### Key Commands

- `flutter analyze` - Lint checks
- `flutter test` - Run unit/widget tests
- `flutter test --coverage` - Generate coverage report
- `fastlane ios beta` - Build iOS beta
- `fastlane android beta` - Build Android beta

---

## Development Setup

### Environment Configuration

**1. Install Flutter & Dependencies**

Verify installation with `flutter doctor`

**2. Platform-Specific Setup**

*Android:*
- Android SDK (API 28+), NDK
- Configure paths via `flutter config`

*iOS (macOS only):*
- CocoaPods, Xcode 13+
- Install command line tools

**3. Project Dependencies**

**4. Environment Variables**

Create `.env` file in project root with required configuration values.

**5. Run & Test**

### IDE Configuration

**VS Code:**
1. Install extensions: Flutter, Dart, Bloc
2. Open workspace
3. Create `.vscode/launch.json` for debugging

**Android Studio/IntelliJ:**
1. Install Flutter & Dart plugins
2. Open project folder
3. Run → Select device → Run 'main.dart'

---

## Development Guidelines

### Naming Conventions

| Element | Convention | Example |
|---------|-----------|---------|
| **Features** | snake_case | `lib/features/rider/`, `lib/features/safety/` |
| **Screens** | PascalCase + suffix | `BookingScreen.dart`, `RideMapScreen.dart` |
| **BLoC Files** | PascalCase + suffix | `SafetyBLoC.dart`, `SafetyEvent.dart` |
| **Models** | PascalCase | `User.dart`, `Trip.dart`, `SafetyEvent.dart` |
| **Utilities** | camelCase | `gpsHelper.dart`, `locationTracker.dart` |
| **Constants** | UPPER_SNAKE_CASE | `RATE_LIMIT_MINUTES`, `API_TIMEOUT_SECONDS` |
| **Variables** | camelCase | `currentLocation`, `driverId`, `tripStatus` |

### Commit Conventions

Use conventional commits:

```
<type>(<scope>): <subject>
```

**Types:** `feat`, `fix`, `refactor`, `test`, `docs`, `chore`, `style`

**Examples:**
- `feat(safety): add panic event escalation with local rate limiting`
- `fix(auth): resolve JWT token refresh race condition on network resume`
- `docs(setup): add iOS build instructions for new developers`

### Git Workflow

**Branching:**
- `main` — Production-ready
- `feature/your-feature` — New features
- `fix/issue-description` — Bugfixes
- `chore/task-description` — Non-code changes

**Pull Request Process:**
1. Create feature branch from `main`
2. Push and open PR with description
3. All checks must pass (tests, linting, review)
4. Address feedback from reviewers
5. Squash and merge on approval
6. Delete branch after merge

### Code Quality Standards

- **Linting**: `flutter analyze` (enforced in CI)
- **Formatting**: `dart format .` (standardized style)
- **Test Coverage**: Minimum 80% for critical logic (safety, auth, payments)
- **Complexity**: Avoid methods exceeding 50 lines
- **Async**: Use `async/await` consistently

---

## Testing & Quality

### Test Strategy

| Test Type | Framework | Purpose | Coverage Target |
|-----------|-----------|---------|-----------------|
| **Unit** | flutter_test | Domain logic, pure functions | 90%+ |
| **Widget** | flutter_test | UI components, navigation | 80%+ |
| **Integration** | integration_test | Feature flows, API integration | 70%+ |
| **E2E** | Firebase Test Lab | Critical user journeys | Key flows |

### Testing Tools

- **flutter_test** - Unit/widget test framework
- **mocktail** - Mock dependencies for isolation
- **integration_test** - UI flows and app-level behaviors
- **Firebase Test Lab** - Automated device matrix testing
- **fastlane** - Automated build, deploy, screenshot capture

### Test Coverage Focus

| Area | Priority | Scenarios |
|------|----------|-----------|
| **Safety** | Critical | Panic trigger, offline fallback, rate limiting |
| **Authentication** | High | Login, token refresh, session expiry |
| **Booking** | High | Request ride, network failure, cancellation |
| **Payments** | High | Add card, invalid input, transaction failure |
| **Trip State** | Medium | State transitions, driver acceptance, completion |

### Running Tests

**Unit Tests:**

**Widget Tests:**

**Integration Tests:**

### Test Environments

| Environment | Configuration | Purpose |
|-------------|--------------|---------|
| **Dev** | Local, hot-reload, sandbox API | Development and debugging |
| **Staging** | Mirrors production | UAT and pre-release testing |
| **Production** | Hardened, secrets in vault | Live user-facing deployment |

---

## Deployment

### Deployment Strategy

Managed via mobile CI/CD pipelines with phased rollouts:

1. **CI Build**: Compile, lint, run tests
2. **Build Artifacts**: Generate signed builds via fastlane
3. **Deploy to Staging**: Automated with smoke tests
4. **Manual UAT**: Operations and QA sign-off
5. **Production Release**: Phased rollout via app stores
6. **Monitoring**: Crash/error monitoring live
7. **Rollback**: fastlane/app store rollback if needed

### Deployment Tools

- **GitHub Actions** - CI/CD pipeline
- **fastlane** - Build signing, beta deploy, screenshots
- **Play Console/App Store Connect** - Distribution, phased rollout
- **Firebase App Distribution** - Internal testing

### Deployment Environments

| Environment | Infrastructure | Availability |
|-------------|---------------|--------------|
| **Dev** | Local/emulated | Restart-on-change |
| **Staging** | Cloud-based, secure | Monitored, auto-scaling |
| **Production** | Redundant, cloud | High-availability, DR |

### Post-Deployment Verification

- **Monitoring**: Sentry/Firebase for crashes and errors
- **Smoke Testing**: Key flows validated after deployment
- **Health Dashboards**: Real-time user metrics
- **Rollback Triggers**: Automated/manual upon failure thresholds

---

## Security Guidelines

### Secrets Management

**Never commit:**
- `.env` files with API keys, tokens, secrets
- Firebase service account JSON files
- Private certificates or signing keys
- Database credentials or connection strings
- Hardcoded API keys or webhook tokens

**Always:**
- Store secrets in Azure Key Vault (per environment)
- Use environment variables at runtime
- Fetch credentials securely at app startup
- Rotate credentials every 90 days (automated)
- Use managed identities for service auth

### Code Security

**Input Validation:**
- Validate all user inputs before processing
- Sanitize data before API calls
- Check for null/empty values

**Network Security:**
- Always use HTTPS with certificate pinning
- Validate SSL/TLS certificates
- Never transmit sensitive data in query params

**Secure Storage:**
- Use `flutter_secure_storage` for tokens, credentials
- Never store passwords (use tokens/OAuth)
- Encrypt sensitive local data at rest

**Logging Security:**
- Never log passwords, API keys, or PII
- Log security-critical events (auth failures, safety triggers)
- Sanitize PII in logs (email, phone, location)

---

## Troubleshooting

### Common Issues

**Flutter not found in PATH:**
- Verify installation with `flutter --version`
- Add Flutter to PATH

**Android SDK/NDK not found:**
- Configure paths via `flutter config`
- Verify with `flutter doctor`

**iOS setup issues (macOS only):**
- Install CocoaPods
- Update iOS pods
- Clean build

**Gradle build fails:**
- Clear Gradle cache
- Rebuild project

**Emulator/device not found:**
- List devices with `flutter devices`
- Start emulator or connect physical device

**Hot reload not working:**
- Restart development server
- Full rebuild if persistent

**API calls fail with certificate error:**
- Verify certificate in infrastructure
- Check Azure Key Vault
- Ensure API_URL matches certificate

**Panic button not triggering:**
- Check network connectivity
- Verify rate limit
- Ensure trip is in active state
- Review logs

### Getting Help

- **Documentation**: [chaufher-workspace](https://github.com/phoenixvc/chaufher-workspace)
- **API Issues**: [chaufher-api](https://github.com/phoenixvc/chaufher-api)
- **Infrastructure**: [chaufher-infra](https://github.com/phoenixvc/chaufher-infra)
- **GitHub Issues**: Open with error details, steps to reproduce
- **Contact**: hans@chaufher.app

---

# Reference

## User Interface

### Key Flows

- **Onboarding**: Registration, tour, KYC, permissions
- **Rider Dashboard**: Map, search, ride request, live tracking, history
- **Driver Dashboard**: Incoming requests, live tracking, earnings, schedule
- **Safety Flows**: Panic button, live support, event feedback
- **Payments**: Add methods, review charges, receipts

### Accessibility

- Large interactive elements, high-contrast mode
- Voice prompts for critical actions
- Panic features always accessible
- WCAG 2.1 AA compliance

### Permissions & Privacy

- Permissions requested on-demand with context
- Graceful handling of denied permissions
- Minimal data collection
- Location data auto-scrubbed after use

---

## Contributing

### Before You Commit

- [ ] Code follows naming conventions
- [ ] All tests pass: `flutter test`
- [ ] Linting passes: `flutter analyze`
- [ ] Code formatted: `dart format .`
- [ ] No secrets in commit
- [ ] Commit message follows convention
- [ ] Branch up-to-date with `main`

### Pull Request Process

1. Create feature branch
2. Make changes and commit
3. Push and open PR with description
4. Address feedback from reviewers
5. Ensure all checks pass
6. Squash and merge on approval
7. Delete branch after merge

### Code Review Checklist

- [ ] Follows naming and style conventions
- [ ] Tests are comprehensive and passing
- [ ] No security vulnerabilities
- [ ] Accessibility standards met
- [ ] Documentation updated if needed
- [ ] Aligns with technical architecture

---

## License

Copyright (c) 2025 ChaufHER. All rights reserved.

[Add License Here — e.g., MIT, Apache 2.0, or proprietary]

---

## Acknowledgments

ChaufHER App is part of the ChaufHER platform, a women-only ridesharing application prioritizing safety, privacy, and reliability. Special thanks to the DevOps, Backend, and Product teams for coordinated multi-repo engineering excellence.
