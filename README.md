# Rental House Management System

**CENG 301 Database Systems Project**  
**University of Turkish Aeronautical Association**  
**Computer Engineering**

---

This project implements a comprehensive rental house management system with a MySQL database backend and a Spring Boot REST API. For detailed presentation and project documentation, please refer to [PRESENTATION.md](PRESENTATION.md).

---

## 1. Database Setup (Using Docker)

To start the database dependency (MySQL), run the following command in the project root directory:

```bash
docker compose up -d
```

## 2. Running the Application

To build and run the project:

```bash
./mvnw spring-boot:run
```

The application will run by default at `http://localhost:8080`.

## 3. Web Interface

The project includes a modern web interface for testing all database operations.

### Features:
- **4 Main Modules**: Separate management panels for Tenants, Landlords, Houses, and Leases
- **Full CRUD Operations**: Create, Read, Update, and Delete operations for all modules
- **Lease Management**: Start new leases and terminate active leases
- **Dynamic Forms**: Modal-based user-friendly forms
- **Real-time Updates**: Automatic list refresh after operations
- **Responsive Design**: Modern interface compatible with mobile and desktop devices

### Usage:
When the application is running, navigate to:
```
http://localhost:8080
```

The web interface performs database operations using all REST API endpoints. To access Swagger UI documentation:
```
http://localhost:8080/swagger-ui/index.html
```

## Database ER Diagram
<a href="ER-diagram.drawio" download="ER-diagram.drawio"> dowload ER-diagram.drawio </a>
![Rental Management System ERD](ER-diagram.png)

## Database Scheme

![Rental Management System ERD](rental_management_system_schema.png)

### 4. Database Schema (CREATE TABLE Scripts)

#### House
```sql
CREATE TABLE IF NOT EXISTS Houses (
    house_id INT AUTO_INCREMENT PRIMARY KEY,
    landlord_id INT,
    address VARCHAR(255) NOT NULL,
    city VARCHAR(100) NOT NULL,
    zip_code VARCHAR(10) NOT NULL,
    monthly_rent DECIMAL(10, 2) NOT NULL,
    number_of_rooms INT,
    is_available BOOLEAN NOT NULL DEFAULT TRUE,
    description TEXT,
    FOREIGN KEY (landlord_id) REFERENCES Landlords(landlord_id)
)
```

#### Landlord
```sql
CREATE TABLE IF NOT EXISTS Landlords (
    landlord_id INT AUTO_INCREMENT PRIMARY KEY,
    first_name VARCHAR(100) NOT NULL,
    last_name VARCHAR(100) NOT NULL,
    phone_number VARCHAR(20),
    email VARCHAR(100) UNIQUE NOT NULL
)
```

#### Tenant
```sql
CREATE TABLE IF NOT EXISTS Tenants (
    tenant_id INT AUTO_INCREMENT PRIMARY KEY,
    first_name VARCHAR(100) NOT NULL,
    last_name VARCHAR(100) NOT NULL,
    phone_number VARCHAR(20),
    email VARCHAR(100) UNIQUE NOT NULL
)
```

#### Lease
```sql
CREATE TABLE IF NOT EXISTS Leases (
    lease_id INT AUTO_INCREMENT PRIMARY KEY,
    house_id INT NOT NULL,
    tenant_id INT NOT NULL,
    start_date DATE NOT NULL,
    end_date DATE NOT NULL,
    deposit_amount DECIMAL(10, 2),
    status ENUM('ACTIVE', 'EXPIRED', 'PENDING') NOT NULL,
    FOREIGN KEY (house_id) REFERENCES Houses(house_id),
    FOREIGN KEY (tenant_id) REFERENCES Tenants(tenant_id)
)
```

### 5. Database Operations Scripts

#### Houses
```sql
INSERT INTO Houses (landlord_id, address, city, zip_code, monthly_rent, number_of_rooms, is_available, description) VALUES (?, ?, ?, ?, ?, ?, ?, ?)
SELECT * FROM Houses WHERE house_id = ?
SELECT * FROM Houses
UPDATE Houses SET landlord_id = ?, address = ?, city = ?, zip_code = ?, monthly_rent = ?, number_of_rooms = ?, is_available = ?, description = ? WHERE house_id = ?
DELETE FROM Houses WHERE house_id = ?
```

#### Landlord
```sql
INSERT INTO Landlords (first_name, last_name, phone_number, email) VALUES (?, ?, ?, ?)
SELECT * FROM Landlords WHERE landlord_id = ?
SELECT * FROM Landlords
UPDATE Landlords SET first_name = ?, last_name = ?, phone_number = ?, email = ? WHERE landlord_id = ?
DELETE FROM Landlords WHERE landlord_id = ?
```

#### Lease
```sql
INSERT INTO Leases (house_id, tenant_id, start_date, end_date, deposit_amount, status) VALUES (?, ?, ?, ?, ?, ?)
SELECT * FROM Leases WHERE lease_id = ?
SELECT * FROM Leases
UPDATE Leases SET house_id = ?, tenant_id = ?, start_date = ?, end_date = ?, deposit_amount = ?, status = ? WHERE lease_id = ?
DELETE FROM Leases WHERE lease_id = ?
SELECT * FROM Leases WHERE house_id = ? AND status = 'ACTIVE' AND end_date >= CURRENT_DATE()
```
