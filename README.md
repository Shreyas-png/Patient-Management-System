<img width="994" height="760" alt="image" src="https://github.com/user-attachments/assets/8ef7c490-ab68-466d-ac24-fedba1453d1b" />🏥 Patient Management System
A production-grade, cloud-ready microservices-based Patient Management System built with Java 17 and Spring Boot 3.x. The system is designed for scalability, security, and inter-service communication using modern patterns like gRPC, Apache Kafka, and JWT authentication.

📐 Architecture Overview

The system is composed of 5 independent microservices, each containerized via Docker and communicating through dedicated protocols:

Client
  │
  ▼
┌─────────────────────┐
│   Gateway Service   │  ← Spring Cloud Gateway (WebFlux)
│   (Port: 4004)      │  ← Routes all incoming requests
└────────┬────────────┘
         │
  ┌──────┼──────────────────────┐
  ▼      ▼                      ▼
Auth   Patient              Billing
Service Service             Service
(JWT)  (REST/JPA)          (gRPC Server)
         │
         │ Kafka Events
         ▼
    Notification
      Service
   (Email via SMTP)

🧩 Services
1. auth-service
Handles user authentication and authorization.

Tech: Spring Boot 3.5.9, Spring Security, JWT (JJWT 0.12.6)
Database: PostgreSQL (via Spring Data JPA)
Responsibilities:

User login and registration
JWT token generation and validation
Password encryption via Spring Security


2. patient-service
Core service managing patient records and data.

Tech: Spring Boot 3.5.9, Spring Security, JWT
Database: PostgreSQL (via Spring Data JPA)
Responsibilities:

CRUD operations on patient data
Secured endpoints with JWT-based authorization
Input validation via Spring Validation


3. billing-service
Manages billing accounts linked to patients.

Tech: Spring Boot 3.5.6, gRPC + REST, Protocol Buffers (Protobuf 4.29.1)
Database: PostgreSQL (via Spring Data JPA)
Responsibilities:

Exposes a gRPC server for patient and Rest end points for billing operations
Creates billing accounts when new patients are registered
Secured with Spring Security + JWT


4. notif-service (Notification Service)
Sends email notifications triggered by system events.

Tech: Spring Boot 3.5.9, Apache Kafka (spring-kafka), Spring Mail, Protocol Buffers
Responsibilities:

Consumes Kafka events published by other services
Sends email notifications via SMTP (Spring Boot Mail)
Deserializes Protobuf-encoded Kafka messages


5. gateway-service
Single entry point for all client requests.

Tech: Spring Boot 3.5.9, Spring Gateway
Responsibilities:

Routes HTTP requests to the appropriate downstream service
Acts as a reverse proxy
Reactive (non-blocking) via Project Reactor / WebFlux


🛠️ Tech Stack
Language: Java 17
Framework: Spring Boot 3.x. 
Build Tool: Maven(with Maven Wrapper) 
API Gateway: Spring Gateway 
Auth & Security: Spring Security + JWT (JJWT 0.12.6)
Inter-service RPC: gRPC 1.69.0 + Protocol Buffers
Async Messaging: Apache Kafka 
Email: Spring Boot Mail (SMTP)
Database: PostgreSQL 
ORM: Spring Data JPA (Hibernate) 
Containerization: Docker 
Utilities: Lombok, Spring Validation

📁 Repository Structure
This is a mono-repo with submodules. Each service lives in its own GitHub repository and is registered as a Git submodule under the services/ directory.
Patient-Management-System/
└── services/
    ├── auth-service/         # Authentication & JWT
    ├── patient-service/      # Patient records management
    ├── billing-service/      # Billing (gRPC)
    ├── notif-service/        # Notifications (Kafka + Email)
    └── gateway-service/      # API Gateway (Spring Cloud)

🚀 Getting Started
Prerequisites

Java 17+
Maven 3.8+
Docker & Docker Compose
PostgreSQL instance
Apache Kafka broker

Clone the Repository (with Submodules)
bash git clone --recurse-submodules https://github.com/Shreyas-png/Patient-Management-System.git
cd Patient-Management-System
If you already cloned without submodules:
bashgit submodule update --init --recursive
Running a Service Locally
Navigate to any service directory and run:
bashcd services/auth-service
./mvnw spring-boot:run
Building with Docker
Each service contains a Dockerfile. Build and run:
bashcd services/auth-service
docker build -t auth-service .
docker run -p 8080:8080 auth-service

🔐 Security

All services are protected with JWT-based authentication.
Tokens are issued by the auth-service and validated on each secured endpoint.
The gateway-service acts as the front door — downstream services enforce their own security as well.

📡 Inter-Service Communication
Pattern Used 
REST: (HTTP)Gateway → Patient/Auth Service. 
gRPC Patient Service → Billing Service
Apache KafkaPatient Service → Notification Service

🗃️ Database
Each service that requires persistence uses its own dedicated PostgreSQL database, following the microservices principle of data isolation.

📬 Notifications
The notif-service listens on Kafka topics and sends emails using Spring Boot Mail. Messages are serialized/deserialized using Protocol Buffers (Protobuf) for efficient binary encoding.

👤 Author
Shreyas — https://github.com/Shreyas-png
