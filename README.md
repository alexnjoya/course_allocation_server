# Course Allocation Platform

A Spring Boot–based service that powers student course enrollment with PostgreSQL persistence, JWT security, and rich API documentation. The project ships with seed data so you can explore the workflow immediately.

## Contents
- [Prerequisites](#prerequisites)
- [Project Setup](#project-setup)
- [Database Preparation](#database-preparation)
- [Application Configuration](#application-configuration)
- [Running the Service](#running-the-service)
- [Exploring the API](#exploring-the-api)
- [Auth Walkthrough](#auth-walkthrough)
- [Sample Dataset](#sample-dataset)
- [Endpoint Catalog](#endpoint-catalog)
- [Testing](#testing)
- [Troubleshooting](#troubleshooting)
- [Project Layout](#project-layout)
- [Technology Stack](#technology-stack)
- [Feature Highlights](#feature-highlights)
- [Security Posture](#security-posture)
- [Credit Policy](#credit-policy)
- [Support](#support)
- [License](#license)

## Prerequisites

### Java 17
Install a JDK 17 distribution and confirm with `java -version`.

- **macOS (Homebrew):** `brew install openjdk@17`
- **macOS (manual):** download from Oracle or Adoptium, install, then set `JAVA_HOME` via `export JAVA_HOME=$(/usr/libexec/java_home -v 17)`
- **Ubuntu/Debian:** `sudo apt update && sudo apt install openjdk-17-jdk`
- **Fedora/RHEL/CentOS:** `sudo dnf install java-17-openjdk-devel`
- **Arch Linux:** `sudo pacman -S jdk-openjdk`
- **Windows:** install the MSI/EXE package from Oracle or Adoptium, set `JAVA_HOME` to `C:\Program Files\Java\jdk-17` (or your chosen path).

### PostgreSQL 14+
Install PostgreSQL and make sure `psql --version` works.

- **macOS (Homebrew):** `brew install postgresql@14 && brew services start postgresql@14`
- **Linux (system packages):** install via `apt`, `dnf`, or `pacman`, then start/enable the service (`sudo systemctl start postgresql`)
- **Windows:** run the PostgreSQL installer, choose a password for the `postgres` user, and keep the default port (`5432`).

### Maven (optional)
The repository includes the Maven Wrapper, so a standalone Maven installation is optional. If you prefer having Maven globally:

- **macOS:** `brew install maven`
- **Ubuntu/Debian:** `sudo apt install maven`
- **Fedora/RHEL/CentOS:** `sudo dnf install maven`
- **Arch Linux:** `sudo pacman -S maven`
- **Windows:** download the binary zip, extract (e.g., `C:\Program Files\Apache\maven`), and add the `bin` directory to the `PATH`.

Verify with `mvn -version` if installed globally.

## Project Setup

Clone or copy the project and move into the root folder:

- **macOS/Linux:** `cd /path/to/course-allocation-server`
- **Windows:** `cd C:\path\to\course-allocation-server`

You should see:
```
course-allocation-server/
├── src/
│   ├── main/
│   └── test/
├── pom.xml
└── README.md
```

## Database Preparation

1. Ensure the PostgreSQL service is running (`brew services list`, `systemctl status postgresql`, or Windows Services console).
2. Connect as the `postgres` user with `psql -U postgres` (on Linux you may need `sudo -u postgres psql`).
3. Create the application database:
   ```sql
   CREATE DATABASE course_allocation;
   ```
4. Confirm creation with `\l`, then exit via `\q`.

To test the connection later: `psql -U postgres -d course_allocation`.

## Application Configuration

Database connection settings live in `src/main/resources/application.properties`. Adjust credentials as needed:
```
spring.datasource.url=jdbc:postgresql://localhost:5432/course_allocation
spring.datasource.username=postgres
spring.datasource.password=postgres
```

JWT configuration is also defined there:
```
jwt.secret=mySecretKeyForJWTTokenGenerationAndValidationInCourseAllocationSystem2024
jwt.expiration=86400000
```
Update the secret for production deployments.

## Running the Service

### Maven Wrapper (recommended)
- **macOS/Linux:** `./mvnw spring-boot:run`
- **Windows:** `.\mvnw.cmd spring-boot:run`

### Local Maven installation
`mvn spring-boot:run`

### IDE
Open the project in IntelliJ IDEA, Eclipse, or VS Code, let Maven sync, locate `CourseAllocationApplication.java`, and run it.

Successful startup prints something similar to `Started CourseAllocationApplication in X.XXX seconds` and serves traffic on `http://localhost:8080`.

## Exploring the API

Swagger UI: `http://localhost:8080/docs`

OpenAPI JSON: `http://localhost:8080/v3/api-docs`

## Auth Walkthrough

1. **Login** — `POST /api/auth/login`
   ```json
   {
     "studentId": "10953537",
     "pin": "12534"
   }
   ```
2. Copy the `token` field from the response.
3. In Swagger, click **Authorize**, paste only the token (without `Bearer `), and confirm.
4. When calling the API manually, set the header `Authorization: Bearer <token>`.

Tokens expire after 24 hours; re-authenticate to obtain a new one.

## Sample Dataset

Seed data is injected at startup so you can explore immediately.

**Students** (`studentId` → PIN):
- `10953537` Kwame Asante — Computer Science, Year 2 — PIN `12534`
- `10953535` Ama Mensah — Computer Science, Year 3 — PIN `56478`
- `10953539` Kofi Osei — Mathematics, Year 1 — PIN `94012`
- `10953538` Akosua Boateng — Computer Science, Year 2 — PIN `34456`

**Courses**
- `DCIT201` Data Structures and Algorithms — Level 200, 3 credits
- `DCIT301` Database Systems — Level 300, 3 credits
- `DCIT202` Object-Oriented Programming — Level 200, 3 credits
- `MATH101` Calculus I — Level 100, 3 credits
- `DCIT401` Software Engineering — Level 400, 3 credits
- `DCIT302` Operating Systems — Level 300, 3 credits

**Active semester**
- First Semister 2025 — 1 January 2025 to 15 April 2025

## Endpoint Catalog

**Auth**
- `POST /api/auth/login`

**Students** (JWT protected)
- `POST /api/students`
- `GET /api/students`
- `GET /api/students/{id}`
- `GET /api/students/student-id/{studentId}`
- `GET /api/students/department/{department}`
- `GET /api/students/year/{year}`
- `PUT /api/students/{id}`
- `DELETE /api/students/{id}`

**Courses** (JWT protected)
- `POST /api/courses`
- `GET /api/courses`
- `GET /api/courses/{id}`
- `GET /api/courses/semester/{semesterId}`
- `GET /api/courses/department/{department}`
- `GET /api/courses/instructor/{instructor}`
- `GET /api/courses/search?name={name}`
- `PUT /api/courses/{id}`
- `DELETE /api/courses/{id}`

**Semesters** (JWT protected)
- `POST /api/semesters`
- `GET /api/semesters`
- `GET /api/semesters/{id}`
- `GET /api/semesters/active`
- `GET /api/semesters/active/list`
- `PUT /api/semesters/{id}`
- `DELETE /api/semesters/{id}`

**Student course management** (JWT protected)
- `GET /api/student/courses/available?studentId={id}&semesterId={id}`
- `POST /api/student/courses/select?studentId={id}`
- `DELETE /api/student/courses/drop/{enrollmentId}?studentId={id}`
- `GET /api/student/courses/summary?studentId={id}&semesterId={id}`

**Enrollments** (JWT protected)
- `POST /api/enrollments`
- `GET /api/enrollments`
- `GET /api/enrollments/{id}`
- `GET /api/enrollments/student/{studentId}`
- `GET /api/enrollments/course/{courseId}`
- `PUT /api/enrollments/{id}`
- `DELETE /api/enrollments/{id}`

## Testing

Run the test suite with the Maven Wrapper:
```
./mvnw test
```
Windows alternative: `mvnw.cmd test`

Quick manual flow:
1. Start the app (`./mvnw spring-boot:run`).
2. Open `http://localhost:8080/docs`.
3. Log in with `10953537` / `12534`.
4. Authorize Swagger with the returned token.
5. Fetch available courses via `GET /api/student/courses/available?studentId=1&semesterId=1`.
6. Enroll using `POST /api/student/courses/select?studentId=1` with `{"courseId": 1}`.
7. Review enrollment summary via `GET /api/student/courses/summary?studentId=1&semesterId=1`.

## Troubleshooting

**Database connection errors** (`Connection refused`, `Failed to determine a suitable driver class`)
- Confirm PostgreSQL is running (`brew services list`, `systemctl status postgresql`, Windows Services).
- Ensure the `course_allocation` database exists (`psql -U postgres -l`).
- Verify credentials in `application.properties`.

**Port 8080 already in use**
- macOS/Linux: `lsof -i :8080` or `netstat -tulpn | grep 8080`, then terminate the offending process or change `server.port`.
- Windows: `netstat -ano | findstr :8080`, then `taskkill /PID <PID> /F`, or update `server.port` in `application.properties`.

**JWT problems**
- Tokens expire after 24 hours; log in again.
- Ensure the token string is complete.
- In Swagger, do not prepend `Bearer`.

**Swagger UI won’t load**
- Confirm the application started cleanly.
- Inspect the server logs for stack traces.
- Try `http://localhost:8080/v3/api-docs` directly.
- Clear the browser cache and retry.

## Project Layout
```
course-allocation-server/
├── src/
│   ├── main/
│   │   ├── java/com/courseallocation/course_allocation/
│   │   │   ├── config/
│   │   │   ├── controller/
│   │   │   ├── SemesterPayload/
│   │   │   ├── exception/
│   │   │   ├── model/
│   │   │   ├── repository/
│   │   │   └── service/
│   │   └── resources/
│   │       ├── application.properties
│   │       └── database/create-database.sql
│   └── test/
├── mvnw / mvnw.cmd
├── pom.xml
└── README.md
```

## Technology Stack
- Java 17
- Spring Boot 3.5.7
- Spring Security
- Spring Data JPA
- PostgreSQL
- jjwt 0.12.5
- Swagger / OpenAPI 3
- Lombok
- Maven with Maven Wrapper

## Feature Highlights
- JWT-backed student authentication
- Course discovery with department/year filters
- Enrollment flows with maximum credit and course rules
- Semester lifecycle management
- REST-first design with comprehensive Swagger documentation
- Centralized error handling and validation
- Automatic data seeding for demos and tests

## Security Posture
- Stateless JWT authentication with configurable expiry
- Role-based access control via Spring Security
- Dedicated authentication filter and token utility
- Stateless session policy for APIs

## Credit Policy
- Maximum of 21 credits per semester
- Maximum of 7 courses per semester
- Recommended minimum of 12 credits

## Support
If anything misbehaves, start with:
1. Reviewing the application logs in your console.
2. Visiting Swagger at `/docs` to inspect endpoint definitions.
3. Checking PostgreSQL connection details and status.
4. Regenerating a fresh JWT token.

## License
Educational use only.
