# 🚗 Car Management & Fuel Tracking System

This project is a **Java-based Car Management & Fuel Tracking System** demonstrating full-stack Java development skills including Spring Boot REST APIs, manual Servlet implementation, and CLI client development.

---

## 📋 Project Overview

The system allows users to:
- ✅ Register cars with brand, model, and year
- ⛽ Record fuel refills with liters, price, and odometer readings
- 📊 Calculate fuel consumption statistics (total fuel, cost, average L/100km)
- 🖥️ Interact via REST APIs, Manual Servlet, or CLI commands

### Key Technologies
- **Backend:** Spring Boot, Java Servlets, In-Memory Storage
- **CLI Client:** Java HttpClient, Standalone Application

---

## 🧱 Project Architecture

The system is divided into **two main modules**:

### 1️⃣ Backend Server (Spring Boot)
- REST API endpoints for car and fuel management
- Manual Java Servlet for fuel statistics (`HttpServlet`)
- **In-memory storage** using Lists/Maps (no database)
- Shared service layer between REST controllers and Servlet
- No authentication required

### 2️⃣ CLI Client (Standalone Java Application)
- Separate executable module
- Communicates with backend via `java.net.http.HttpClient`
- Parses command-line arguments
- Formats and displays API responses

---

## 🖥️ Backend REST API

### Base URL
```
http://localhost:8081
```

### 🔹 Create a Car
**POST** `/api/cars`

**Request Body:**
```json
{
  "brand": "Toyota",
  "model": "Corolla",
  "year": 2018
}
```

**Response (201 Created):**
```json
{
  "id": 1,
  "brand": "Toyota",
  "model": "Corolla",
  "year": 2018,
  "fuelEntries": []
}
```

---

### 🔹 List All Cars
**GET** `/api/cars`

**Response (200 OK):**
```json
[
  {
    "id": 1,
    "brand": "Toyota",
    "model": "Corolla",
    "year": 2018,
    "fuelEntries": []
  }
]
```

---

### 🔹 Add Fuel Entry
**POST** `/api/cars/{id}/fuel`

**Request Body:**
```json
{
  "liters": 40,
  "price": 52.5,
  "odometer": 45000
}
```

**Response (200 OK):**
```
Fuel entry added successfully
```

**Error Response (404 Not Found):**
```json
{
  "error": "Car not found with id: 999"
}
```

---

### 🔹 Get Fuel Statistics (REST)
**GET** `/api/cars/{id}/fuel/stats`

**Response (200 OK):**
```json
{
  "totalFuel": 120.0,
  "totalCost": 155.0,
  "averageConsumption": 6.4
}
```

---

## 🔧 Manual Servlet Endpoint

### 🔹 Get Fuel Statistics (Servlet)
**GET** `/servlet/fuel-stats?carId={id}`

**Example:**
```
http://localhost:8081/servlet/fuel-stats?carId=1
```

**Response (200 OK):**
```json
{
  "totalFuel": 120.0,
  "totalCost": 155.0,
  "averageConsumption": 6.4
}
```

**Implementation Details:**
- Extends `HttpServlet` and overrides `doGet()`
- Manually parses `carId` from query parameters
- Sets `Content-Type: application/json` explicitly
- Returns appropriate HTTP status codes (200, 404, 500)
- **Reuses the same Service layer** as REST controllers

---

## 💻 CLI Application

### Available Commands

#### 1️⃣ Create a Car
```bash
java -cp out org.CarCliApplication create-car --brand Toyota --model Corolla --year 2018
```

**Output:**
```json
{"id":1,"brand":"Toyota","model":"Corolla","year":2018,"fuelEntries":[]}
```

---

#### 2️⃣ Add Fuel Entry
```bash
java -cp out org.CarCliApplication add-fuel --carId 1 --liters 40 --price 52.5 --odometer 45000
```

**Output:**
```
Fuel entry added successfully
```

---

#### 3️⃣ View Fuel Statistics
```bash
java -cp out org.CarCliApplication fuel-stats --carId 1
```

**Output:**
```
Total fuel: 120.0 L
Total cost: 155.00
Average consumption: 6.4 L/100km
```

---

#### 4️⃣ List All Cars
```bash
java -cp out org.CarCliApplication list-cars
```

**Output:**
```json
[
  {"id":1,"brand":"Toyota","model":"Corolla","year":2018,"fuelEntries":[...]}
]
```

---

## 🚀 Getting Started

### Prerequisites
- ☕ **Java 11** or higher
- 📦 **Maven 3.6+**
- 🌐 Internet connection (for Maven dependencies)

### Installation & Setup

#### 1. Clone the Repository
```bash
git clone <repository-url>
cd Car_Management_Fuel_Tracking
```

#### 2. Build the Project
```bash
mvn clean install
```

#### 3. Run the Backend Server
```bash
mvn spring-boot:run
```

Server will start at: `http://localhost:8081`

#### 4. Compile the CLI Client
```bash
javac -d out src/main/java/org/CarCliApplication.java
```

#### 5. Run CLI Commands
```bash
java -cp out org.CarCliApplication create-car --brand Toyota --model Corolla --year 2018
```

---

## 📊 Data Model

### Car Entity
```java
public class Car {
    private Long id;              // Auto-generated
    private String brand;         // e.g., "Toyota"
    private String model;         // e.g., "Corolla"
    private Integer year;         // e.g., 2018
    private List<FuelEntry> fuelEntries;
}
```

### FuelEntry Entity
```java
public class FuelEntry {
    private Long id;              // Auto-generated
    private Double liters;        // Fuel amount in liters
    private Double price;         // Total price paid
    private Integer odometer;     // Odometer reading in km
    private LocalDateTime date;   // Timestamp of entry
}
---

## 🧪 Complete Testing Workflow

### Scenario: Track fuel consumption for a Toyota Corolla

#### Step 1: Create the car
```bash
java -cp out org.CarCliApplication create-car --brand Toyota --model Corolla --year 2018
```
**Result:** Car created with ID 1

#### Step 2: First fuel fill (baseline reading)
```bash
java -cp out org.CarCliApplication add-fuel --carId 1 --liters 40 --price 55 --odometer 1200
```
**Note:** This is the starting point (no consumption calculated yet)

#### Step 3: Second fuel fill
```bash
java -cp out org.CarCliApplication add-fuel --carId 1 --liters 45 --price 62 --odometer 1800
```
**Distance traveled:** 1800 - 1200 = 600 km  
**Fuel consumed:** 45 L  
**Consumption:** (45 / 600) × 100 = 7.5 L/100km

#### Step 4: Check statistics
```bash
java -cp out org.CarCliApplication fuel-stats --carId 1
```
**Output:**
```
Total fuel: 85.0 L
Total cost: 117.00
Average consumption: 7.5 L/100km
```

#### Step 5: Test via Servlet
```bash
curl "http://localhost:8081/servlet/fuel-stats?carId=1"
```

---

## 🏗️ Technical Implementation

### Backend Architecture

```
┌─────────────────────────────────────┐
│     Spring Boot Application         │
├─────────────────────────────────────┤
│  REST Controllers  │  Manual Servlet│
├────────────┬───────┴────────────────┤
│            │   Service Layer        │
│            │  (Shared Business      │
│            │   Logic)               │
├────────────┴────────────────────────┤
│     In-Memory Storage               │
│  - List<Car> cars                   │
│  - AtomicLong idGenerator           │
└─────────────────────────────────────┘
```

### Key Features

#### ✅ Service Layer Reuse
Both REST controllers and the manual Servlet use the **same service instance** to ensure consistency.

#### ✅ Average Consumption Calculation
```java
// Requires at least 2 fuel entries
// Formula: (totalFuel / totalDistance) * 100
averageConsumption = (sumOfFuel / (lastOdometer - firstOdometer)) * 100
```

#### ✅ Error Handling
- **404 Not Found:** Invalid car ID
- **400 Bad Request:** Missing required fields
- **500 Internal Server Error:** Unexpected errors

#### ✅ Thread-Safe ID Generation
```java
private static final AtomicLong idCounter = new AtomicLong(0);
```

---

## 📝 Assignment Requirements Checklist

### Part 1: Backend REST API ✅
- [x] POST `/api/cars` - Create car with brand, model, year
- [x] GET `/api/cars` - List all cars
- [x] POST `/api/cars/{id}/fuel` - Add fuel entry (liters, price, odometer)
- [x] GET `/api/cars/{id}/fuel/stats` - Return total fuel, cost, avg/100km
- [x] In-memory storage (Lists/Maps)
- [x] No database or authentication

### Part 2: Servlet Integration ✅
- [x] Manual Servlet at GET `/servlet/fuel-stats?carId={id}`
- [x] Extends `HttpServlet` and overrides `doGet()`
- [x] Manually parses `carId` from query parameters
- [x] Sets `Content-Type: application/json`
- [x] Sets HTTP status codes explicitly
- [x] Uses same Service layer as REST API

### Part 3: CLI Application ✅
- [x] Separate module using `java.net.http.HttpClient`
- [x] `create-car` command with --brand, --model, --year
- [x] `add-fuel` command with --carId, --liters, --price, --odometer
- [x] `fuel-stats` command with --carId
- [x] Displays formatted statistics
- [x] Separate executable from backend

---

## 🐛 Troubleshooting

### Issue: Port 8081 already in use
**Solution:** Change port in `application.properties`:
```properties
server.port=8080
```

### Issue: CLI returns "Connection refused"
**Solution:** Ensure backend server is running:
```bash
mvn spring-boot:run
```

### Issue: 404 on Servlet endpoint
**Solution:** Check Servlet registration in Spring configuration:
```java
@Bean
public ServletRegistrationBean<FuelStatsServlet> fuelStatsServlet() {
    return new ServletRegistrationBean<>(new FuelStatsServlet(), "/servlet/fuel-stats");
}
```

