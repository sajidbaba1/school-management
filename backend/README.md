# School Management — Backend (Spring Boot)

## Tech Stack
- Spring Boot 3.x
- Spring Security + JWT
- Spring Data JPA
- MySQL 8
- Lombok
- Maven

## Project Structure

```
backend/
├── src/
│   └── main/
│       ├── java/com/school/management/
│       │   ├── SchoolManagementApplication.java  ← Main entry point
│       │   ├── config/          ← Security, CORS config
│       │   ├── controller/      ← REST API controllers
│       │   ├── service/         ← Business logic
│       │   ├── repository/      ← JPA repositories
│       │   ├── model/           ← Entity classes (DB tables)
│       │   ├── dto/             ← Data Transfer Objects
│       │   ├── security/        ← JWT filter, auth classes
│       │   └── exception/       ← Custom exceptions
│       └── resources/
│           └── application.properties  ← DB + app config
└── pom.xml                      ← Maven dependencies
```

## How to Run
```bash
mvn spring-boot:run
```
Runs on: http://localhost:8080

## API Base URL
```
http://localhost:8080/api/v1
```
