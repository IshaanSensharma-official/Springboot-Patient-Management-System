# Spring Boot Patient Management System

A complete microservices-based patient management system built with Spring Boot, Apache Kafka, gRPC, and PostgreSQL.

This project demonstrates a robust, event-driven architecture using multiple interconnected services.

## Architecture & Services

The system is composed of the following microservices:

1. **API Gateway (Port 4004)**
   - Acts as the single entry point for all client requests.
   - Routes requests to the appropriate microservice.
   - Validates JWT tokens using a custom Spring Cloud Gateway filter.

2. **Auth Service (Port 4005)**
   - Handles user authentication and authorization.
   - Generates JWT tokens upon successful login.
   - Uses a dedicated PostgreSQL database (`auth-db`).

3. **Patient Service (Port 4000)**
   - Manages patient records (CRUD operations).
   - Produces Kafka events when patients are created or updated.
   - Communicates synchronously with the Billing Service via gRPC to create billing accounts.
   - Uses a dedicated PostgreSQL database (`patient-db`).

4. **Billing Service (Port 4001, gRPC 9001)**
   - Receives gRPC requests from the Patient Service to create billing accounts.

5. **Analytics Service (Port 4002)**
   - Listens to Kafka topics for patient events.
   - Processes and logs analytics data asynchronously.

## Technologies Used

- **Java 21**
- **Spring Boot 3** (Web, Data JPA, Security)
- **Spring Cloud Gateway**
- **Apache Kafka** (Event-driven messaging)
- **gRPC & Protobuf** (High-performance synchronous communication)
- **PostgreSQL** (Relational databases)
- **Docker & Docker Compose** (Containerization and local deployment)

## Running the Project Locally

You can run the entire system locally using Docker Compose. This will spin up the necessary databases, Kafka broker, and all the microservices.

### Prerequisites
- Docker and Docker Compose installed.

### Steps to Run

1. Clone the repository and navigate to the project root.
2. Run the following command to build and start all containers:

   ```bash
   docker-compose up --build
   ```

   *Note: The first run might take a few minutes as it downloads dependencies and builds the Docker images for each service.*

3. Once all services are up and running, you can interact with the API via the Gateway on `http://localhost:4004`.

### Default Test Credentials

The Auth Service automatically seeds a test user into the database:
- **Email:** `testuser@test.com`
- **Password:** `password`

### API Endpoints

1. **Login (Auth Service via Gateway)**
   ```http
   POST http://localhost:4004/auth/login
   ```
   *Body:*
   ```json
   {
       "email": "testuser@test.com",
       "password": "password"
   }
   ```
   *This returns a JWT token.*

2. **Create Patient (Patient Service via Gateway)**
   ```http
   POST http://localhost:4004/api/patients
   Authorization: Bearer <your-jwt-token>
   ```
   *Body:*
   ```json
   {
       "name": "John Doe",
       "email": "johndoe@example.com"
   }
   ```
   *This will save the patient, call the Billing Service via gRPC, and send a message to Kafka for the Analytics Service.*


