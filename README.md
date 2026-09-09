## Repository Structure

| Component | Repository |
|---|---|
| Backend | [CosMate Backend](https://github.com/phatdvt183465/KLTN_CosMate_Backend.git) |
| Frontend | [CosMate Frontend](https://github.com/Phan-Duong-Duy-Thien/CosMate.git) |
| Mobile | [CosMate Mobile](https://github.com/ZuuSaitoh/CosMate-App-Mobile.git) |

# CosMate — Intelligent Cosplay Rental & Service Platform

## Overview
**CosMate** is a full-stack marketplace platform designed specifically for the cosplay community, connecting cosplayers with costume rental shops and creative service providers (photographers, makeup artists, and event staff). The platform solves key peer-to-peer rental frictions—such as trust deficits, scheduling conflicts, and gear damage—through an automated deposit escrow system, dynamic service booking calendars, and real-time order tracking. In addition, CosMate integrates multimodal generative AI to deliver visual costume search, character pose scoring, and virtual try-ons directly to web and mobile users.

---

## Highlights
- **Role-Based Ecosystem**: Custom experiences and workflows for **Cosplayers**, **Providers** (Rental, Photography, Makeup, Event Staff), and **Administrators**.
- **265 Verified REST Endpoints**: 237 class-declared routes and 28 inherited CRUD/Data I/O endpoints across 43 Spring controllers.
- **54 JPA Entities**: Relational schema mapped via Hibernate 6 on Microsoft SQL Server.
- **Dual Client Apps**: Modern web single-page application and cross-platform mobile client.
- **Multimodal AI Pipeline**: Dual-vector semantic search, image moderation, automated tagging, pose evaluation, and virtual try-on.
- **Real-Time Synchronization**: Full-duplex STOMP over WebSocket supporting live chat, push notifications, and cross-device QR login.
- **Containerized Infrastructure**: Multi-stage Docker build with Nginx Proxy Manager and SQL Server orchestration.

---

## Tech Stack

| Domain | Technologies & Libraries |
| :--- | :--- |
| **Backend** | **Java 21**, **Spring Boot 3.2.4**, Spring Security 6, Spring Data JPA, Spring WebSocket, Spring Retry, Spring Mail |
| **Database** | **Microsoft SQL Server 2022**, Hibernate 6, `mssql-jdbc` |
| **Web Frontend** | **React 19.2**, **Vite 7.2**, **TypeScript 5.9**, **Tailwind CSS 4.1**, Ant Design 6.2, Radix UI, Motion 12 |
| **Mobile App** | **React Native 0.81**, **Expo SDK 54**, **Expo Router 6**, React Native Reanimated 4, Expo Camera |
| **Realtime** | **STOMP Protocol**, **SockJS Client** (Web), Native WebSocket (Mobile) |
| **AI Integrations** | **Google Gemini API** (`gemini-embedding-2-preview`, `gemini-3.1-flash-lite`, `gemini-3.5-flash`), **Fal.ai** (`idm-vton`) |
| **Third-Party APIs**| **Firebase Storage** (Media assets), **VNPay** & **MoMo** (Payment gateways), **Gmail SMTP** (Email verification) |
| **Data Processing** | **Apache POI 5.2** (Excel import/export), **OpenCSV 5.12**, **iText7 8.0** (PDF generation), **MapStruct 1.5** |
| **DevOps / Hosting**| **Docker** (Multi-stage builds), **Docker Compose**, **Nginx Proxy Manager** (Reverse proxy & SSL) |

---

## Architecture

The project is structured as a decoupled client-server system with an asynchronous backend processing pipeline:
- **Backend Architecture**: Layered domain-driven structure (`controller` -> `service` -> `repository` -> `entity`). Includes an extensible `base` package providing reusable CRUD controllers, dynamic JPA criteria specification builders, and an annotation-driven Excel/CSV/PDF Data I/O engine.
- **Frontend Architecture**: Modular feature-sliced architecture under `src/features/` with centralized route guards (`ProtectedRoute`, `OverdueLockGuard`) and React context providers for auth and WebSockets.
- **Mobile Architecture**: File-based navigation using Expo Router with segregated route groups for public auth, cosplayer tabs, and provider management tabs.
- **Cross-Cutting Concerns**: Global exception interceptor (`@RestControllerAdvice`), stateless JWT authentication filter, cross-device WebSocket authentication channel, and scheduled transaction workers.

```
Cosmate/
├── cosmate-backend/cosmate-backend/
│   ├── Dockerfile                             # Multi-stage JVM build (Temurin JDK 21 -> JRE 21 Alpine)
│   ├── docker-compose.yml                     # Local orchestration (SQL Server + Spring Boot + NPM)
│   ├── pom.xml                                # Maven build descriptors & dependencies
│   └── src/main/java/com/cosmate/
│       ├── CosMateApplication.java            # Main application bootstrap
│       ├── ServiceOrderScheduler.java         # Scheduled worker for order state & escrow lifecycles
│       ├── base/                              # Core CRUD, AutoSpecBuilder, and Data I/O abstractions
│       ├── configuration/                     # Security, WebSocket, Firebase, OpenAPI, and CORS setup
│       ├── controller/                        # 43 REST Controllers (265 endpoints)
│       ├── dto/                               # Request/Response DTOs and query filters
│       ├── entity/                            # 54 JPA Domain entities
│       ├── repository/                        # Spring Data JPA repositories
│       ├── security/                          # JWT filter, token utilities, and UserDetails
│       └── service/                           # Business logic implementations & external AI clients
├── cosmate-frontend/CosMate/
│   ├── src/
│   │   ├── app/                               # Route definitions, guards & context providers
│   │   ├── components/                        # Atomic reusable UI components (Tailwind + AntD)
│   │   ├── features/                          # 20 modular feature domains (rental, auth, orders, etc.)
│   │   └── services/                          # Axios API clients & WebSocket STOMP handlers
│   └── deploy/                                # Production Nginx configuration & deployment scripts
└── cosmate-mobile/Cosmetics-Sales-App-Mobile/
    └── app/                                   # Expo Router file-based screens & navigation
        ├── (auth)/                            # Login, registration, and onboarding screens
        ├── (tabs)/                            # Cosplayer exploration, cart, and profile tabs
        ├── (provider-tabs)/                   # Rental shop management, inventory, and order tabs
        ├── (screens)/                         # Booking, disputes, wallet, and QR login approval
        └── ai/                                # AI Search, Pose Battle, and Style Quiz screens
```

---

## Key Features

- **Authentication & Cross-Device QR Login**: JWT-based stateless authentication with BCrypt hashing, Google OAuth2 integration, email activation links, and real-time QR code login (desktop generates QR session, mobile app scans and approves via WebSocket).
- **Costume Rental Lifecycle**: End-to-end management from catalog search to deposit escrow, shipping tracking, check-in photo submission, rental extension requests, return verification, and overdue late-return account locking.
- **Creative Service Bookings**: Dedicated scheduling and booking pipelines for cosplay photographers, makeup artists, and event support staff with geo-location filtering by operating districts.
- **Escrow Wallet & Payments**: Internal ledger wallet managing available and locked balances. Integrates VNPay and MoMo for seamless wallet top-ups and order checkouts, alongside bank withdrawal requests.
- **Real-Time Chat & Notifications**: Full-duplex messaging between cosplayers and providers via STOMP WebSockets, including real-time order status and dispute updates.
- **AI Multimodal Search & Moderation**: Dual-vector search matching natural language and reference images against catalog inventory using Cosine Similarity, accompanied by automated NSFW content moderation on image uploads.
- **AI Pose Battle (Pose Scoring)**: Evaluates user cosplay photos against reference character artwork, returning detailed posture alignment, facial expression accuracy, and styling scores (0–100) with actionable advice.
- **AI Virtual Try-On (VTO)**: Renders virtual garment try-ons on user-uploaded portraits using the Fal.ai IDM-VTON model.
- **Universal Data I/O & Admin Suite**: Annotation-driven bulk import and export of platform entities into Excel (`.xlsx`), CSV, and PDF reports with automated password encryption for batch user onboarding.

---

## Technical Highlights

- **Layered Architecture**: Strict separation of concerns across controllers, service interfaces, JPA repositories, and DTO mappers ensures testability and maintainability.
- **Dual-Vector Semantic Search with Resilient Fallback**: Combines 1536-dim text and image feature embeddings (70% image weight / 30% text weight). Decorated with Spring Retry (`@Retryable`) and an automatic fallback to lexical search if AI quotas or network errors occur.
- **Escrow-Backed Transaction Management**: Critical multi-table state updates (wallet debits, order reservations, deposit locking) are bound by declarative Spring `@Transactional` boundaries, preventing double-spending and incomplete state transitions.
- **Automated Lifecycle Processing (`@Scheduled`)**: `ServiceOrderScheduler` runs background cron tasks to transition booking states, apply late return surcharges, auto-complete finished rentals, and release held provider balances.
- **Dynamic JPA Criteria Specification**: Custom `AutoSpecBuilder` converts incoming DTO filter parameters into type-safe JPA `Specification<T>` predicates via reflection, eliminating repetitive boilerplate queries.
- **Client-Side Account Quarantine Guard**: `OverdueLockGuard` in React Router evaluates rental status on every route transition, instantly locking overdue accounts from exploring or purchasing until past-due items are returned.
- **Dual-Client WebSocket Channel Interceptor**: Backend STOMP broker handles both browser SockJS and native React Native WebSockets, securing handshakes through an inbound channel interceptor that validates JWTs before allowing topic subscription.
- **Memory-Optimized Containerization**: The multi-stage Docker build produces a lean JRE 21 Alpine image configured with JVM heap limits (`-Xms128m -Xmx256m`) for dependable execution in resource-constrained environments.

---

## API Overview

The backend exposes **265 verified REST endpoints** structured around RESTful design principles. Below is a representative summary:

| Module / Domain | Base Route | Methods | Example Operations |
| :--- | :--- | :--- | :--- |
| **Auth & IAM** | `/api/auth` | `GET`, `POST` | `/login`, `/register`, `/google/login`, `/qr-generate`, `/qr-approve` |
| **AI Intelligence** | `/api/search` | `GET`, `POST`, `PUT`, `DELETE` | `/ai` (Search), `/pose-score`, `/vto` (Virtual Try-On), `/submit-quiz`, `/generate-description` |
| **Costume Catalog**| `/api/costumes` | `GET`, `POST`, `PUT`, `DELETE` | `GET /` (Catalog), `POST /` (Create), `GET /{id}` (Detail), `/generate-vector/{id}` |
| **Orders & Rentals**| `/api/orders` | `GET`, `POST`, `PUT`, `PATCH` | `POST /` (Checkout), `PUT /{id}/status`, `POST /{id}/extend` (Rental extension) |
| **Services Booking**| `/api/services` | `GET`, `POST`, `PUT`, `DELETE` | `/api/services` (List/Create), `/api/service-orders` (Booking & scheduling) |
| **Providers** | `/api/providers` | `GET`, `POST`, `PUT` | `/api/providers/subscriptions`, `/api/providers/cancellation-policies` |
| **Wallets & Escrow**| `/api/wallets` | `GET`, `POST` | `/api/wallets/me`, `/api/withdraws`, `/api/payment/api/vnpay/create` |
| **Disputes** | `/api/disputes` | `GET`, `POST`, `PUT` | `POST /` (File dispute), `PUT /{id}/resolve` (Admin settlement) |
| **Administration** | `/api/admin/*` | `GET`, `POST`, `PUT`, `DELETE` | `/users`, `/providers`, `/orders`, `/system-config`, `/export`, `/import` |

### Real-Time WebSocket Channels
- `/ws` (SockJS endpoint for web) & `/ws-mobile` (Standard WebSocket endpoint for mobile)
- Subscriptions: `/topic/chat/{roomId}`, `/topic/notifications/{userId}`, `/topic/qr/{sessionId}`

---

## Deployment

The system is configured for containerized deployment via Docker Compose:
- **Database Service (`sql_server`)**: Official Microsoft SQL Server 2022 container with persistent volume mounting (`mssql_data`).
- **Application Service (`backend`)**: Multi-stage Docker container utilizing Temurin JDK 21 for compilation and JRE 21 Alpine for execution.
- **Reverse Proxy Service (`npm`)**: Nginx Proxy Manager managing ingress traffic on ports `80` and `443`, automated SSL certificate provisioning via Let's Encrypt, and internal proxy forwarding to backend and web services.
- **Frontend Distribution**: Vite production assets built into static bundles (`dist/`) and served via Nginx.

```bash
# Run the complete production composition
cd cosmate-backend/cosmate-backend
docker compose up -d --build
```

---

## Local Development

### Prerequisites
- **Java**: OpenJDK `21`
- **Build Tool**: Apache Maven `3.9+`
- **Node.js**: Node `v20+` & npm `10+`
- **Database**: Microsoft SQL Server `2022` running locally on port `1433`
- **Mobile**: Expo CLI (`npm install -g expo-cli`) and Expo Go app

### 1. Backend Setup
```bash
cd cosmate-backend/cosmate-backend

# Configure environment variables in .env (or application-dev.properties)
# Required keys:
# SPRING_DATASOURCE_URL=jdbc:sqlserver://localhost:1433;databaseName=CosMate;encrypt=true;trustServerCertificate=true;
# SPRING_DATASOURCE_USERNAME=sa
# SPRING_DATASOURCE_PASSWORD=YourPassword123!
# JWT_SECRET=your_base64_secret_key
# GEMINI_API_KEY=your_gemini_api_key
# FAL_API_KEY=your_fal_api_key

# Build and run
mvn clean package -DskipTests
mvn spring-boot:run
```
*API runs at `http://localhost:8080`. Swagger documentation is available at `http://localhost:8080/swagger-ui.html`.*

### 2. Web Frontend Setup
```bash
cd cosmate-frontend/CosMate

# Configure .env:
# VITE_API_BASE_URL=http://localhost:8080
# VITE_APP_BASE_URL=http://localhost:5173

# Install dependencies and start development server
npm install
npm run dev
```
*Web application runs at `http://localhost:5173`.*

### 3. Mobile App Setup
```bash
cd cosmate-mobile/Cosmetics-Sales-App-Mobile

# Install dependencies and start Metro bundler
npm install
npx expo start
```
*Scan the terminal QR code with the Expo Go app or press `a` / `i` to launch emulator.*

---

## License & Author

- **License**: No formal open-source license is currently specified in this repository. All rights are reserved by the original project contributors.
- **Author & Maintainer**: **Ta Khac Khoan** ([khoantkse183194@fpt.edu.vn](mailto:khoantkse183194@fpt.edu.vn)) — Capstone Project, FPT University.
