# Rental House Management System
## Database Project Presentation

---

## 1. Project Overview

### Objective
Develop a comprehensive database and web application for rental house management.

### Scope
- **Landlords** management
- **Tenants** management
- **Houses** management
- **Lease Operations** management

### Problem Statement
In traditional rental management systems:
- Manual record keeping
- Data inconsistencies
- Difficult relationship tracking
- Lack of automation

---

## 2. Database Design

### ER Diagram Components

#### Entities
1. **Landlords** (Property Owners)
2. **Houses** (Properties)
3. **Tenants** (Renters)
4. **Leases** (Rental Agreements)

#### Relationships
- **Landlords (1) → (N) Houses**: One landlord can own multiple houses
- **Houses (1) → (N) Leases**: One house can have multiple lease agreements (over time)
- **Tenants (1) → (N) Leases**: One tenant can have multiple lease agreements

---

## 3. Table Structures and Properties

### 3.1 Landlords Table

**Purpose**: Store landlord information

| Column | Type | Description |
|-------|-----|----------|
| `landlord_id` | INT (PK, AUTO_INCREMENT) | Unique identifier |
| `first_name` | VARCHAR(100) NOT NULL | First name |
| `last_name` | VARCHAR(100) NOT NULL | Last name |
| `phone_number` | VARCHAR(20) | Phone number |
| `email` | VARCHAR(100) UNIQUE NOT NULL | Email address (unique) |

**Properties**:
- Primary Key: `landlord_id`
- Unique Constraint: `email`
- NOT NULL constraints: Name and email are required

---

### 3.2 Houses Table

**Purpose**: Store rental property information

| Column | Type | Description |
|-------|-----|----------|
| `house_id` | INT (PK, AUTO_INCREMENT) | Unique identifier |
| `landlord_id` | INT (FK) | Landlord reference |
| `address` | VARCHAR(255) NOT NULL | Address |
| `city` | VARCHAR(100) NOT NULL | City |
| `zip_code` | VARCHAR(10) NOT NULL | Zip code |
| `monthly_rent` | DECIMAL(10,2) NOT NULL | Monthly rent |
| `number_of_rooms` | INT | Number of rooms |
| `is_available` | BOOLEAN NOT NULL DEFAULT TRUE | Availability status |
| `description` | TEXT | Description |

**Properties**:
- Primary Key: `house_id`
- Foreign Key: `landlord_id` → `Landlords(landlord_id)`
- Default Value: `is_available = TRUE`
- NOT NULL constraints: Address, city, zip code, and monthly rent are required

---

### 3.3 Tenants Table

**Purpose**: Store tenant information

| Column | Type | Description |
|-------|-----|----------|
| `tenant_id` | INT (PK, AUTO_INCREMENT) | Unique identifier |
| `first_name` | VARCHAR(100) NOT NULL | First name |
| `last_name` | VARCHAR(100) NOT NULL | Last name |
| `phone_number` | VARCHAR(20) | Phone number |
| `email` | VARCHAR(100) UNIQUE NOT NULL | Email address (unique) |

**Properties**:
- Primary Key: `tenant_id`
- Unique Constraint: `email`
- NOT NULL constraints: Name and email are required

---

### 3.4 Leases Table

**Purpose**: Store lease agreement information

| Column | Type | Description |
|-------|-----|----------|
| `lease_id` | INT (PK, AUTO_INCREMENT) | Unique identifier |
| `house_id` | INT (FK) NOT NULL | House reference |
| `tenant_id` | INT (FK) NOT NULL | Tenant reference |
| `start_date` | DATE NOT NULL | Start date |
| `end_date` | DATE NOT NULL | End date |
| `deposit_amount` | DECIMAL(10,2) | Deposit amount |
| `status` | ENUM('ACTIVE', 'EXPIRED', 'PENDING') NOT NULL | Status |

**Properties**:
- Primary Key: `lease_id`
- Foreign Keys: 
  - `house_id` → `Houses(house_id)`
  - `tenant_id` → `Tenants(tenant_id)`
- ENUM Constraint: Status limited to specific values
- NOT NULL constraints: House, tenant, dates, and status are required

---

## 4. Relationships and Referential Integrity

### 4.1 Foreign Key Relationships

1. **Houses → Landlords**
   - `Houses.landlord_id` → `Landlords.landlord_id`
   - A house must belong to a landlord
   - Referential integrity is maintained

2. **Leases → Houses**
   - `Leases.house_id` → `Houses(house_id)`
   - A lease must belong to a house

3. **Leases → Tenants**
   - `Leases.tenant_id` → `Tenants(tenant_id)`
   - A lease must belong to a tenant

### 4.2 Relationship Types

- **One-to-Many (1:N)**:
  - Landlord → Houses
  - House → Leases (over time)
  - Tenant → Leases

- **Many-to-Many (N:M)** (Indirect):
  - Tenant ↔ Houses (through Leases)

---

## 5. Business Logic and Constraints

### 5.1 Lease Operations

#### Starting a New Lease
**Constraints**:
1. House must be available (`is_available = TRUE`)
2. Start date cannot be in the past
3. End date must be after start date
4. House and tenant must exist

**Operations**:
- New lease record is created (`status = 'ACTIVE'`)
- House status is updated (`is_available = FALSE`)
- **Transaction** is used (ACID property)

#### Terminating a Lease
**Constraints**:
1. Lease must be active (`status = 'ACTIVE'`)

**Operations**:
- Lease status is updated (`status = 'EXPIRED'`)
- House status is updated (`is_available = TRUE`)
- **Transaction** is used

### 5.2 Query Operations

#### Active Lease Query
```sql
SELECT * FROM Leases 
WHERE house_id = ? 
AND status = 'ACTIVE' 
AND end_date >= CURRENT_DATE()
```

**Purpose**: Find active lease agreement for a house

---

## 6. CRUD Operations

### 6.1 Landlords
- ✅ **Create**: Add new landlord
- ✅ **Read**: List all landlords, query by ID
- ✅ **Update**: Update landlord information
- ✅ **Delete**: Delete landlord

### 6.2 Houses
- ✅ **Create**: Add new house
- ✅ **Read**: List all houses, query by ID
- ✅ **Update**: Update house information
- ✅ **Delete**: Delete house

### 6.3 Tenants
- ✅ **Create**: Add new tenant
- ✅ **Read**: List all tenants, query by ID
- ✅ **Update**: Update tenant information
- ✅ **Delete**: Delete tenant

### 6.4 Leases
- ✅ **Create**: Start new lease
- ✅ **Read**: List all leases, query by ID
- ✅ **Update**: Terminate lease (status update)

---

## 7. Application Architecture

### 7.1 Technology Stack

**Backend**:
- **Java 21**
- **Spring Boot 4.0.0**
- **Spring JDBC** (DAO Pattern)
- **MySQL** (Database)
- **Maven** (Build Tool)

**Frontend**:
- **HTML5/CSS3/JavaScript**
- Responsive Web Design
- RESTful API Integration

**Infrastructure**:
- **Docker Compose** (MySQL container)
- **Spring Boot Embedded Tomcat**

### 7.2 Architecture Layers

```
┌─────────────────────────────────────┐
│      Web Interface (Frontend)       │
│      (HTML/CSS/JavaScript)          │
└──────────────┬──────────────────────┘
               │ HTTP REST API
┌──────────────▼──────────────────────┐
│      Controllers (REST API)         │
│  TenantController, HouseController, │
│  LandlordController, LeaseController│
└──────────────┬──────────────────────┘
               │
┌──────────────▼──────────────────────┐
│      Services (Business Logic)      │
│  TenantService, HouseService,       │
│  LandlordService, RentalService     │
└──────────────┬──────────────────────┘
               │
┌──────────────▼──────────────────────┐
│      DAOs (Data Access Objects)     │
│  TenantDAO, HouseDAO, LandlordDAO,  │
│  LeaseDAO                           │
└──────────────┬──────────────────────┘
               │
┌──────────────▼──────────────────────┐
│      MySQL Database                 │
│  (Landlords, Houses, Tenants,       │
│   Leases Tables)                    │
└─────────────────────────────────────┘
```

---

## 8. Key Features

### 8.1 Database Features
- ✅ **Referential Integrity**: Foreign Key constraints
- ✅ **Data Integrity**: NOT NULL, UNIQUE, ENUM constraints
- ✅ **Transaction Management**: ACID properties
- ✅ **Automatic Schema Creation**: Via Spring Boot

### 8.2 Application Features
- ✅ **RESTful API**: Standard HTTP methods
- ✅ **Web Interface**: User-friendly modern UI
- ✅ **Swagger UI**: API documentation
- ✅ **CORS Support**: Cross-origin requests
- ✅ **Error Handling**: Appropriate HTTP status codes

### 8.3 Business Logic Features
- ✅ **Lease Validation**: Business rules enforcement
- ✅ **Automatic Status Update**: House availability status
- ✅ **Date Validation**: Valid date checks

---

## 9. Data Security and Integrity

### 9.1 Data Integrity Measures

1. **Primary Key Constraints**
   - Unique identifier in each table

2. **Foreign Key Constraints**
   - Referential integrity maintained
   - Relationships are mandatory and valid

3. **Unique Constraints**
   - Email addresses are unique
   - Prevents duplicate records

4. **NOT NULL Constraints**
   - Critical fields are required
   - Prevents missing data

5. **ENUM Constraints**
   - Status values are limited
   - Prevents invalid status

### 9.2 Transaction Management

- **ACID Properties**:
  - **Atomicity**: Transactions complete entirely or not at all
  - **Consistency**: Data integrity is maintained
  - **Isolation**: Concurrent transactions don't interfere
  - **Durability**: Transactions are permanent

**Example**: When starting a lease, both `Leases` and `Houses` tables are updated. If an error occurs in one, the entire transaction is rolled back.

---

## 10. Use Cases

### Use Case 1: Adding New Landlord and House
1. Landlord record is created
2. House record is created (with landlord reference)
3. House automatically starts as available

### Use Case 2: Lease Process
1. Tenant record is created (if it doesn't exist)
2. An available house is selected
3. Lease is started (with dates and deposit)
4. House automatically becomes "occupied"

### Use Case 3: Lease Termination
1. Active lease is queried
2. Lease is terminated
3. Lease status becomes "EXPIRED"
4. House automatically becomes "available"

---

## 11. Conclusion and Evaluation

### 11.1 Project Achievements
- ✅ Normalized database structure
- ✅ Referential integrity maintained
- ✅ Business logic rules implemented
- ✅ User-friendly web interface
- ✅ RESTful API standards compliant

### 11.2 Concepts Learned
- Database normalization
- Foreign Keys and referential integrity
- Transaction management
- CRUD operations
- RESTful API design
- DAO Pattern

### 11.3 Future Enhancements
- 🔄 Payment tracking system
- 🔄 House photo management
- 🔄 Reporting module
- 🔄 User authorization (Authentication/Authorization)
- 🔄 Email notifications

---

## 12. Technical Details

### 12.1 Project Structure
```
rental-house-management-system/
├── src/
│   └── main/
│       ├── java/
│       │   └── com/database_project/library_management_system/
│       │       ├── config/          # Configuration classes
│       │       ├── controller/      # REST API controllers
│       │       ├── entity/          # Data models
│       │       ├── repository/      # DAO classes
│       │       └── service/         # Business logic services
│       └── resources/
│           ├── static/              # Web interface
│           └── application.yaml     # Configuration
├── docker-compose.yml               # MySQL container
└── pom.xml                          # Maven dependencies
```

### 12.2 API Endpoints

**Base URL**: `http://localhost:8080/api`

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/tenants` | GET | List all tenants |
| `/tenants` | POST | Add new tenant |
| `/tenants/{id}` | GET | Get tenant by ID |
| `/tenants/{id}` | PUT | Update tenant |
| `/tenants/{id}` | DELETE | Delete tenant |
| `/landlords` | GET | List all landlords |
| `/landlords` | POST | Add new landlord |
| `/landlords/{id}` | GET | Get landlord by ID |
| `/landlords/{id}` | PUT | Update landlord |
| `/landlords/{id}` | DELETE | Delete landlord |
| `/houses` | GET | List all houses |
| `/houses` | POST | Add new house |
| `/houses/{id}` | GET | Get house by ID |
| `/houses/{id}` | PUT | Update house |
| `/houses/{id}` | DELETE | Delete house |
| `/leases` | GET | List all leases |
| `/leases/{id}` | GET | Get lease by ID |
| `/leases/start` | POST | Start new lease |
| `/leases/terminate/{id}` | PUT | Terminate lease |

---

## Questions & Answers

**Prepared by**: [Your Names]  
**Date**: [Presentation Date]  
**Institution**: [University/Department]
