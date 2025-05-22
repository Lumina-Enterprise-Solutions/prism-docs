# Arsitektur Microservices Prism ERP - Model C4

## Level 1: System Context Diagram

### External Systems & Actors

#### Primary Users
- **Business Users**: End-users yang menggunakan aplikasi ERP
- **System Administrators**: Mengelola konfigurasi dan maintenance
- **API Consumers**: External applications yang mengintegrasikan dengan Prism ERP

#### External Systems
- **Identity Provider**: External OAuth2/OIDC providers (Google, Microsoft, etc.)
- **Payment Gateways**: Stripe, PayPal untuk payment processing
- **Email Service**: AWS SES, SendGrid untuk email notifications
- **SMS Gateway**: Twilio untuk SMS notifications
- **Cloud Storage**: AWS S3 untuk file storage
- **External APIs**: Shipping providers, tax services, accounting systems
- **Monitoring Systems**: External monitoring dan logging services

### System Boundary
```
┌─────────────────────────────────────────────────────────────────┐
│                        Prism ERP System                        │
│                                                                 │
│  ┌─────────────────┐    ┌─────────────────┐                   │
│  │   Web Frontend  │    │  Mobile App     │                   │
│  │   (React SPA)   │    │  (React Native) │                   │
│  └─────────────────┘    └─────────────────┘                   │
│                                                                 │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │              Backend Services Ecosystem               │   │
│  │                                                       │   │
│  │  Core, Module, Integration & Support Services        │   │
│  └─────────────────────────────────────────────────────────┘   │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

## Level 2: Container Diagram

### Frontend Containers
1. **Web Application (React SPA)**
   - Technology: React + TypeScript, Vite
   - Responsibilities: User interface, client-side logic
   - Communication: HTTPS REST APIs, WebSocket connections

2. **Mobile Application (React Native)**
   - Technology: React Native + TypeScript
   - Responsibilities: Mobile user interface
   - Communication: HTTPS REST APIs

### Backend Containers

#### Infrastructure Layer
1. **API Gateway**
   - Technology: Kong/Traefik
   - Responsibilities: Request routing, authentication, rate limiting, SSL termination
   - Ports: 80 (HTTP), 443 (HTTPS)

2. **Service Discovery**
   - Technology: Consul
   - Responsibilities: Service registration, health checks, configuration
   - Port: 8500

3. **Message Broker**
   - Technology: Apache Kafka
   - Responsibilities: Event streaming, pub/sub messaging
   - Ports: 9092 (Kafka), 2181 (Zookeeper)

#### Core Services Layer
4. **Authentication Service**
   - Technology: Go + Fiber
   - Responsibilities: User authentication, JWT token management, OAuth2
   - Port: 8001
   - Database: PostgreSQL + Redis

5. **User Management Service**
   - Technology: Go + Fiber
   - Responsibilities: User CRUD, roles, permissions, organization hierarchy
   - Port: 8002
   - Database: PostgreSQL

6. **Notification Service**
   - Technology: Go + Fiber
   - Responsibilities: Multi-channel notifications (email, SMS, push, in-app)
   - Port: 8003
   - Database: PostgreSQL + Redis

7. **File Service**
   - Technology: Go + Fiber
   - Responsibilities: File upload/download, processing, metadata management
   - Port: 8004
   - Storage: AWS S3, Database: PostgreSQL

#### Module Services Layer
8. **Prism Spectrum Service (Analytics)**
   - Technology: Go + Fiber
   - Responsibilities: Dashboard data, analytics, real-time metrics
   - Port: 8010
   - Database: PostgreSQL + InfluxDB/TimescaleDB

9. **Prism Clarity Service (Finance)**
   - Technology: Go + Fiber
   - Responsibilities: Accounting, financial transactions, reporting
   - Port: 8011
   - Database: PostgreSQL

10. **Prism Flow Service (Supply Chain)**
    - Technology: Go + Fiber
    - Responsibilities: Inventory, procurement, logistics
    - Port: 8012
    - Database: PostgreSQL + MongoDB

11. **Prism Lens Service (CRM)**
    - Technology: Go + Fiber
    - Responsibilities: Customer management, sales pipeline, support
    - Port: 8013
    - Database: PostgreSQL + MongoDB

12. **Prism Forge Service (Production)**
    - Technology: Go + Fiber
    - Responsibilities: Manufacturing, quality control, asset management
    - Port: 8014
    - Database: PostgreSQL + MongoDB

13. **Prism Talent Service (HR)**
    - Technology: Go + Fiber
    - Responsibilities: Employee management, performance, recruitment
    - Port: 8015
    - Database: PostgreSQL

14. **Prism Optics Service (BI)**
    - Technology: Go + Fiber
    - Responsibilities: Business intelligence, custom reports, data export
    - Port: 8016
    - Database: PostgreSQL + Data Warehouse

15. **Prism Nexus Service (Integration)**
    - Technology: Go + Fiber
    - Responsibilities: API integrations, workflows, customizations
    - Port: 8017
    - Database: PostgreSQL + MongoDB

### Data Storage Containers
16. **Primary Database Cluster**
    - Technology: PostgreSQL 17.4 with Multi-AZ
    - Responsibilities: Transactional data, multi-tenant schema isolation
    - Port: 5432

17. **Document Database Cluster**
    - Technology: MongoDB/DocumentDB
    - Responsibilities: Flexible schema data, audit logs, configurations
    - Port: 27017

18. **Cache Layer**
    - Technology: Redis Cluster
    - Responsibilities: Session storage, API caching, pub/sub
    - Port: 6379

19. **Time-Series Database**
    - Technology: InfluxDB/TimescaleDB
    - Responsibilities: Metrics, analytics data, time-based events
    - Port: 8086

### Monitoring & Observability
20. **Metrics Collection**
    - Technology: Prometheus
    - Responsibilities: Metrics scraping, alerting
    - Port: 9090

21. **Visualization**
    - Technology: Grafana
    - Responsibilities: Dashboards, monitoring visualization
    - Port: 3000

22. **Log Aggregation**
    - Technology: ELK Stack (Elasticsearch, Logstash, Kibana)
    - Responsibilities: Centralized logging, log analysis
    - Ports: 9200 (Elasticsearch), 5601 (Kibana)

23. **Distributed Tracing**
    - Technology: Jaeger
    - Responsibilities: Request tracing, performance analysis
    - Port: 14268

## Level 3: Component Diagram

### Authentication Service Components
```
┌─────────────────────────────────────────────────────────────┐
│                Authentication Service                       │
├─────────────────────────────────────────────────────────────┤
│  ┌─────────────────┐  ┌─────────────────┐                 │
│  │   Auth Handler  │  │  Token Manager  │                 │
│  │   - Login       │  │  - JWT Generate │                 │
│  │   - Register    │  │  - JWT Validate │                 │
│  │   - MFA         │  │  - Refresh      │                 │
│  └─────────────────┘  └─────────────────┘                 │
│                                                             │
│  ┌─────────────────┐  ┌─────────────────┐                 │
│  │  OAuth Handler  │  │  Session Mgr    │                 │
│  │  - Google       │  │  - Redis Store  │                 │
│  │  - Microsoft    │  │  - Timeout      │                 │
│  │  - SAML         │  │  - Cleanup      │                 │
│  └─────────────────┘  └─────────────────┘                 │
│                                                             │
│  ┌─────────────────┐  ┌─────────────────┐                 │
│  │ Password Mgr    │  │   RBAC Engine   │                 │
│  │ - Hash (Argon2) │  │ - Role Check    │                 │
│  │ - Validate      │  │ - Permission    │                 │
│  │ - Reset Flow    │  │ - Policy Eval   │                 │
│  └─────────────────┘  └─────────────────┘                 │
└─────────────────────────────────────────────────────────────┘
```

### Prism Clarity (Finance) Service Components
```
┌─────────────────────────────────────────────────────────────┐
│                 Prism Clarity Service                       │
├─────────────────────────────────────────────────────────────┤
│  ┌─────────────────┐  ┌─────────────────┐                 │
│  │ Accounting Core │  │ Transaction Mgr │                 │
│  │ - Chart/Account │  │ - Double Entry  │                 │
│  │ - Journal Entry │  │ - Validation    │                 │
│  │ - General Ledger│  │ - Rollback      │                 │
│  └─────────────────┘  └─────────────────┘                 │
│                                                             │
│  ┌─────────────────┐  ┌─────────────────┐                 │
│  │ Report Engine   │  │  Invoice Mgr    │                 │
│  │ - P&L Statement │  │ - Generation    │                 │
│  │ - Balance Sheet │  │ - Templates     │                 │
│  │ - Cash Flow     │  │ - Payments      │                 │
│  └─────────────────┘  └─────────────────┘                 │
│                                                             │
│  ┌─────────────────┐  ┌─────────────────┐                 │
│  │   Tax Engine    │  │  Budget Mgr     │                 │
│  │ - Calculation   │  │ - Planning      │                 │
│  │ - Filing        │  │ - Variance      │                 │
│  │ - Compliance    │  │ - Forecasting   │                 │
│  └─────────────────┘  └─────────────────┘                 │
└─────────────────────────────────────────────────────────────┘
```

### Prism Flow (Supply Chain) Service Components
```
┌─────────────────────────────────────────────────────────────┐
│                 Prism Flow Service                          │
├─────────────────────────────────────────────────────────────┤
│  ┌─────────────────┐  ┌─────────────────┐                 │
│  │ Inventory Core  │  │ Procurement Mgr │                 │
│  │ - Stock Levels  │  │ - Purchase Orders│                 │
│  │ - Movements     │  │ - Vendor Mgmt   │                 │
│  │ - Locations     │  │ - Approval Flow │                 │
│  └─────────────────┘  └─────────────────┘                 │
│                                                             │
│  ┌─────────────────┐  ┌─────────────────┐                 │
│  │ Order Processor │  │ Logistics Mgr   │                 │
│  │ - State Machine │  │ - Shipping      │                 │
│  │ - Fulfillment   │  │ - Tracking      │                 │
│  │ - Allocation    │  │ - Delivery      │                 │
│  └─────────────────┘  └─────────────────┘                 │
│                                                             │
│  ┌─────────────────┐  ┌─────────────────┐                 │
│  │ Forecast Engine │  │ Warehouse Mgr   │                 │
│  │ - Demand Predict│  │ - Bin Locations │                 │
│  │ - ML Models     │  │ - Picking       │                 │
│  │ - Optimization  │  │ - Cycle Count   │                 │
│  └─────────────────┘  └─────────────────┘                 │
└─────────────────────────────────────────────────────────────┘
```

## Level 4: Code Diagram (Service Implementation)

### Authentication Service - Package Structure
```
auth-service/
├── cmd/
│   └── main.go                 # Application entry point
├── internal/
│   ├── config/
│   │   └── config.go          # Configuration management
│   ├── handler/
│   │   ├── auth.go            # Authentication handlers
│   │   ├── oauth.go           # OAuth handlers
│   │   └── middleware.go      # HTTP middleware
│   ├── service/
│   │   ├── auth.go            # Authentication business logic
│   │   ├── token.go           # JWT token management
│   │   ├── password.go        # Password handling
│   │   └── rbac.go            # Role-based access control
│   ├── repository/
│   │   ├── user.go            # User data access
│   │   ├── session.go         # Session management
│   │   └── cache.go           # Redis operations
│   ├── model/
│   │   ├── user.go            # User domain models
│   │   ├── session.go         # Session models
│   │   └── permission.go      # Permission models
│   └── util/
│       ├── hash.go            # Password hashing utilities
│       ├── jwt.go             # JWT utilities
│       └── validator.go       # Input validation
├── pkg/
│   ├── database/
│   │   ├── postgres.go        # PostgreSQL connection
│   │   └── redis.go           # Redis connection
│   ├── logger/
│   │   └── logger.go          # Structured logging
│   └── errors/
│       └── errors.go          # Custom error types
├── api/
│   └── openapi.yaml           # OpenAPI specification
├── deployments/
│   ├── Dockerfile
│   ├── helm/
│   │   ├── Chart.yaml
│   │   ├── values.yaml
│   │   └── templates/
│   └── k8s/
│       ├── deployment.yaml
│       ├── service.yaml
│       └── configmap.yaml
└── scripts/
    ├── build.sh
    ├── test.sh
    └── migrate.sh
```

## Communication Patterns

### Synchronous Communication
```
┌─────────────┐    HTTP/gRPC     ┌─────────────┐
│   Client    │ ────────────────► │    API      │
│ Application │                  │  Gateway    │
└─────────────┘                  └─────────────┘
                                        │
                                        ▼
                         ┌─────────────────────────┐
                         │   Service Discovery     │
                         │      (Consul)           │
                         └─────────────────────────┘
                                        │
                    ┌───────────────────┼───────────────────┐
                    ▼                   ▼                   ▼
          ┌─────────────┐     ┌─────────────┐     ┌─────────────┐
          │   Core      │     │   Module    │     │Integration  │
          │  Services   │     │  Services   │     │  Services   │
          └─────────────┘     └─────────────┘     └─────────────┘
```

### Asynchronous Communication
```
┌─────────────┐     Event      ┌─────────────┐
│  Service A  │ ──────────────► │    Kafka    │
└─────────────┘                 └─────────────┘
                                       │
                   ┌───────────────────┼───────────────────┐
                   ▼                   ▼                   ▼
          ┌─────────────┐     ┌─────────────┐     ┌─────────────┐
          │  Service B  │     │  Service C  │     │  Service D  │
          │ (Consumer)  │     │ (Consumer)  │     │ (Consumer)  │
          └─────────────┘     └─────────────┘     └─────────────┘
```

### Event-Driven Architecture
```
Business Events:
- UserCreated
- OrderPlaced
- InventoryUpdated
- PaymentProcessed
- InvoiceGenerated
- CustomerRegistered
- ProductionStarted
- QualityCheckCompleted

Event Flow:
┌─────────────┐     UserCreated     ┌─────────────┐
│   User      │ ──────────────────► │ Notification│
│  Service    │                     │   Service   │
└─────────────┘                     └─────────────┘
       │                                   │
       │ UserCreated                       │ SendWelcomeEmail
       ▼                                   ▼
┌─────────────┐                     ┌─────────────┐
│   Audit     │                     │   Email     │
│  Service    │                     │  Service    │
└─────────────┘                     └─────────────┘
```

## Data Architecture

### Multi-tenancy Strategy
```
PostgreSQL Schema-based Multi-tenancy:

Database: prism_erp
├── Schema: tenant_001
│   ├── users
│   ├── organizations
│   ├── financial_transactions
│   ├── inventory_items
│   └── ...
├── Schema: tenant_002
│   ├── users
│   ├── organizations
│   ├── financial_transactions
│   ├── inventory_items
│   └── ...
└── Schema: shared
    ├── tenant_metadata
    ├── system_configurations
    └── feature_flags
```

### Database Distribution
```
┌─────────────────┐   ┌─────────────────┐   ┌─────────────────┐
│   PostgreSQL    │   │    MongoDB      │   │     Redis       │
│   (Primary)     │   │  (Documents)    │   │    (Cache)      │
├─────────────────┤   ├─────────────────┤   ├─────────────────┤
│ • Users         │   │ • Audit Logs    │   │ • Sessions      │
│ • Organizations │   │ • Configurations│   │ • API Cache     │
│ • Transactions  │   │ • Flexible Data │   │ • Pub/Sub       │
│ • Inventory     │   │ • Integrations  │   │ • Rate Limits   │
│ • Orders        │   │ • Custom Fields │   │ • Temp Data     │
│ • Employees     │   │ • Workflow Data │   │ • Queue Jobs    │
└─────────────────┘   └─────────────────┘   └─────────────────┘
```

## Security Architecture

### Authentication Flow
```
1. User Login Request
   ┌─────────────┐    POST /auth/login    ┌─────────────┐
   │   Client    │ ─────────────────────► │    API      │
   │             │                        │  Gateway    │
   └─────────────┘                        └─────────────┘
                                                 │
                                                 ▼
                                        ┌─────────────┐
                                        │     Auth    │
                                        │   Service   │
                                        └─────────────┘
                                                 │
                                                 ▼
                                        ┌─────────────┐
                                        │ PostgreSQL  │
                                        │   + Redis   │
                                        └─────────────┘

2. JWT Token Response
   ┌─────────────┐   JWT + Refresh Token  ┌─────────────┐
   │   Client    │ ◄───────────────────── │     Auth    │
   │             │                        │   Service   │
   └─────────────┘                        └─────────────┘

3. Authenticated Request
   ┌─────────────┐   Authorization: Bearer ┌─────────────┐
   │   Client    │ ─────────────────────► │    API      │
   │             │                        │  Gateway    │
   └─────────────┘                        └─────────────┘
                                                 │
                                                 ▼
                                        ┌─────────────┐
                                        │   Target    │
                                        │   Service   │
                                        └─────────────┘
```

### Service-to-Service Authentication
```
mTLS Communication:
┌─────────────┐    mTLS Certificate   ┌─────────────┐
│  Service A  │ ◄──────────────────► │  Service B  │
└─────────────┘                      └─────────────┘

Service Discovery with Auth:
┌─────────────┐                      ┌─────────────┐
│   Service   │ ──── Register ─────► │   Consul    │
│             │                      │  (with ACL) │
│             │ ◄─── Discovery ───── │             │
└─────────────┘                      └─────────────┘
```

## Deployment Architecture

### Kubernetes Cluster Layout
```
┌─────────────────────────────────────────────────────────────┐
│                    AWS EKS Cluster                          │
│                                                             │
│  ┌─────────────────┐  ┌─────────────────┐                 │
│  │   Namespace:    │  │   Namespace:    │                 │
│  │ prism-platform  │  │  prism-core     │                 │
│  │                 │  │                 │                 │
│  │ • API Gateway   │  │ • Auth Service  │                 │
│  │ • Consul        │  │ • User Service  │                 │
│  │ • Kafka         │  │ • Notification  │                 │
│  │ • Monitoring    │  │ • File Service  │                 │
│  └─────────────────┘  └─────────────────┘                 │
│                                                             │
│  ┌─────────────────┐  ┌─────────────────┐                 │
│  │   Namespace:    │  │   Namespace:    │                 │
│  │ prism-modules   │  │ prism-data      │                 │
│  │                 │  │                 │                 │
│  │ • Spectrum      │  │ • PostgreSQL    │                 │
│  │ • Clarity       │  │ • MongoDB       │                 │
│  │ • Flow          │  │ • Redis         │                 │
│  │ • Lens          │  │ • InfluxDB      │                 │
│  │ • Forge         │  │                 │                 │
│  │ • Talent        │  │                 │                 │
│  │ • Optics        │  │                 │                 │
│  │ • Nexus         │  │                 │                 │
│  └─────────────────┘  └─────────────────┘                 │
└─────────────────────────────────────────────────────────────┘
```

### Network Architecture
```
┌─────────────────────────────────────────────────────────────┐
│                        AWS VPC                              │
│                                                             │
│  ┌─────────────────────────────────────────────────────┐   │
│  │                Public Subnets                       │   │
│  │                                                     │   │
│  │  ┌─────────────┐  ┌─────────────┐  ┌──────────────┐ │   │
│  │  │   ALB       │  │  NAT Gateway│  │  Bastion     │ │   │
│  │  │ (Port 443)  │  │             │  │   Host       │ │   │
│  │  └─────────────┘  └─────────────┘  └──────────────┘ │   │
│  └─────────────────────────────────────────────────────┘   │
│                                                             │
│  ┌─────────────────────────────────────────────────────┐   │
│  │                Private Subnets                      │   │
│  │                                                     │   │
│  │  ┌─────────────┐  ┌─────────────┐  ┌──────────────┐ │   │
│  │  │ EKS Worker  │  │ EKS Worker  │  │ EKS Worker   │ │   │
│  │  │   Nodes     │  │   Nodes     │  │   Nodes      │ │   │
│  │  │   (AZ-1)    │  │   (AZ-2)    │  │   (AZ-3)     │ │   │
│  │  └─────────────┘  └─────────────┘  └──────────────┘ │   │
│  └─────────────────────────────────────────────────────┘   │
│                                                             │
│  ┌─────────────────────────────────────────────────────┐   │
│  │                Database Subnets                     │   │
│  │                                                     │   │
│  │  ┌─────────────┐  ┌─────────────┐  ┌──────────────┐ │   │
│  │  │ RDS Primary │  │ RDS Replica │  │  ElastiCache │ │   │
│  │  │   (AZ-1)    │  │   (AZ-2)    │  │   Cluster    │ │   │
│  │  └─────────────┘  └─────────────┘  └──────────────┘ │   │
│  └─────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
```

## Monitoring & Observability

### Three Pillars of Observability
```
┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐
│     METRICS     │  │      LOGS       │  │     TRACES      │
├─────────────────┤  ├─────────────────┤  ├─────────────────┤
│ • Prometheus    │  │ • ELK Stack     │  │ • Jaeger        │
│ • Grafana       │  │ • Structured    │  │ • OpenTelemetry │
│ • Custom KPIs   │  │ • Centralized   │  │ • Correlation   │
│ • Alerting      │  │ • Searchable    │  │ • Performance   │
└─────────────────┘  └─────────────────┘  └─────────────────┘
                                    │
                                    ▼
                        ┌─────────────────┐
                        │   ALERTING      │
                        ├─────────────────┤
                        │ • PagerDuty     │
                        │ • Slack         │
                        │ • Email         │
                        │ • SMS           │
                        └─────────────────┘
```

## Kesimpulan & Next Steps

Arsitektur microservices Prism ERP menggunakan model C4 ini memberikan:

1. **Skalabilitas**: Setiap service dapat di-scale independen
2. **Maintainability**: Clear separation of concerns
3. **Resilience**: Fault isolation dan circuit breaking
4. **Flexibility**: Teknologi yang tepat untuk setiap domain
5. **Security**: Multi-layer security dengan authentication/authorization
6. **Observability**: Comprehensive monitoring dan logging

Dokumentasi ini akan menjadi blueprint untuk implementasi teknis fase berikutnya.
