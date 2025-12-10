# Job Application Portal - Complete Documentation

## 📋 Table of Contents
1. [Project Overview](#project-overview)
2. [Technology Stack](#technology-stack)
3. [Architecture & Design](#architecture--design)
4. [Database Schema](#database-schema)
5. [Project Structure](#project-structure)
6. [Core Components](#core-components)
7. [User Roles & Features](#user-roles--features)
8. [Setup & Configuration](#setup--configuration)
9. [Running the Application](#running-the-application)

---

## 🎯 Project Overview

**Job Application Portal** is a full-stack web application built with **Spring Boot** that enables employers to post jobs and employees to apply for them. The system includes an admin dashboard for managing users, jobs, and applications.

### Key Features:
- User authentication with role-based access control (ADMIN, EMPLOYER, EMPLOYEE)
- Employers can create and manage job postings
- Employees can browse and apply for jobs
- Admins can monitor all users, jobs, and applications
- Session-based security with login/logout functionality

---

## 🛠️ Technology Stack

### Backend
- **Framework**: Spring Boot 3.5.5
- **Language**: Java 17
- **ORM**: JPA (Jakarta Persistence API) with Hibernate
- **Database**: MySQL 8.0
- **Build Tool**: Maven

### Frontend
- **Template Engine**: Thymeleaf (Server-side rendering)
- **Styling**: CSS (Custom styles with Tailwind CSS utilities)
- **HTML**: HTML5 with semantic markup

### Other Dependencies
- **Spring Boot Starter Web**: REST and web functionality
- **Spring Boot Starter Data JPA**: Database operations
- **MySQL Connector/J**: JDBC driver for MySQL
- **Spring Boot Starter Thymeleaf**: Template rendering

---

## 🏗️ Architecture & Design

### MVC Pattern
The application follows the **Model-View-Controller (MVC)** architecture:

```
┌─────────────────────────────────────────────┐
│           Frontend (Thymeleaf)              │
│   User Interface & Form Handling            │
└──────────────┬──────────────────────────────┘
               │
┌──────────────▼──────────────────────────────┐
│       Controllers (Request Handling)        │
│  • HomeController                           │
│  • AdminController                          │
│  • EmployerController                       │
│  • EmployeeController                       │
└──────────────┬──────────────────────────────┘
               │
┌──────────────▼──────────────────────────────┐
│      Services (Business Logic)              │
│  • UserService                              │
│  • JobService                               │
└──────────────┬──────────────────────────────┘
               │
┌──────────────▼──────────────────────────────┐
│    Repositories (Data Access)               │
│  • UserRepository                           │
│  • JobRepository                            │
│  • JobApplicationRepository                 │
└──────────────┬──────────────────────────────┘
               │
┌──────────────▼──────────────────────────────┐
│      Database (MySQL)                       │
│  • users table                              │
│  • jobs table                               │
│  • applications table                       │
└─────────────────────────────────────────────┘
```

### Session Management
- User sessions are managed using `HttpSession`
- User objects are stored in session after successful login
- Session validation is performed before accessing protected endpoints

---

## 💾 Database Schema

### 1. **Users Table**
Stores user information with role-based access control.

```sql
CREATE TABLE users (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(255) NOT NULL,
    email VARCHAR(255) UNIQUE NOT NULL,
    password VARCHAR(255) NOT NULL,
    role VARCHAR(50) NOT NULL  -- ADMIN, EMPLOYER, EMPLOYEE
);
```

**Relationships:**
- One User (EMPLOYER) → Many Jobs
- One User (EMPLOYEE) → Many JobApplications

### 2. **Jobs Table**
Stores job postings created by employers.

```sql
CREATE TABLE jobs (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    job_title VARCHAR(255) NOT NULL,
    company_name VARCHAR(255) NOT NULL,
    job_description VARCHAR(1000),
    job_location VARCHAR(255),
    workplace_type VARCHAR(50),  -- On-site, Remote, Hybrid
    skills_and_qualifications VARCHAR(255),
    experience_level VARCHAR(255),
    education VARCHAR(255),
    salary_range VARCHAR(255),
    job_type VARCHAR(50),  -- Full-time, Part-time, Contract, etc.
    employer_id BIGINT NOT NULL,
    FOREIGN KEY (employer_id) REFERENCES users(id)
);
```

**Relationships:**
- Many Jobs → One User (employer)
- One Job → Many JobApplications

### 3. **Applications Table**
Stores job applications from employees.

```sql
CREATE TABLE applications (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    status VARCHAR(50) DEFAULT 'PENDING',  -- PENDING, APPROVED, REJECTED, WITHDRAWN
    employee_id BIGINT NOT NULL,
    job_id BIGINT NOT NULL,
    FOREIGN KEY (employee_id) REFERENCES users(id),
    FOREIGN KEY (job_id) REFERENCES jobs(id)
);
```

**Relationships:**
- Many Applications → One User (employee)
- Many Applications → One Job

---

## 📁 Project Structure

```
Job-Application-Portal/
│
├── pom.xml                          # Maven configuration file
├── mvnw / mvnw.cmd                 # Maven wrapper scripts
├── package.json                     # NPM configuration (Tailwind CSS)
│
├── src/main/
│   ├── java/
│   │   └── com/JobApplicationPortal/Job_portal/
│   │       ├── JobPortalApplication.java      # Main Spring Boot entry point
│   │       │
│   │       ├── Controller/
│   │       │   ├── HomeController.java        # Login, Signup, Logout
│   │       │   ├── AdminController.java       # Admin dashboard operations
│   │       │   ├── EmployerController.java    # Employer job operations
│   │       │   └── EmployeeController.java    # Employee job search & apply
│   │       │
│   │       ├── Entity/
│   │       │   ├── User.java                  # User entity with relationships
│   │       │   ├── Job.java                   # Job posting entity
│   │       │   └── JobApplication.java        # Job application entity
│   │       │
│   │       ├── Repository/
│   │       │   ├── UserRepository.java        # User database operations
│   │       │   ├── JobRepository.java         # Job database operations
│   │       │   └── JobApplicationRepository.java  # Application queries
│   │       │
│   │       └── Service/
│   │           ├── UserService.java           # User business logic
│   │           └── JobService.java            # Job business logic
│   │
│   └── resources/
│       ├── application.properties             # Spring Boot configuration
│       ├── static/
│       │   └── css/
│       │       └── style.css                  # Global styles & utilities
│       │
│       └── templates/
│           ├── index.html                     # Landing page
│           ├── login.html                     # Login form
│           ├── signup.html                    # Registration form
│           ├── admin-dashboard.html           # Admin home page
│           ├── manage-users.html              # Admin: User management
│           ├── monitor-jobs.html              # Admin: Monitor all jobs
│           ├── monitor-applications.html      # Admin: Monitor applications
│           ├── employer-dashboard.html        # Employer home page
│           ├── create-job.html                # Employer: Create job posting
│           ├── my-jobs.html                   # Employer: View own jobs
│           ├── applicants.html                # Employer: View applicants
│           ├── employee-dashboard.html        # Employee home page
│           ├── employee-jobs.html             # Employee: Browse jobs
│           ├── employee-applications.html     # Employee: View applications
│           └── fragments/
│               ├── navbar.html                # Reusable navigation bar
│               └── footer.html                # Reusable footer
│
└── src/test/
    └── java/
        └── com/JobApplicationPortal/Job_portal/
            └── JobPortalApplicationTests.java # Unit tests
```

---

## 🔧 Core Components

### 1. **Entities** (Data Models)

#### **User Entity** (`Entity/User.java`)
Represents a system user with three possible roles:

```java
@Entity
@Table(name = "users")
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    private String name;
    @Column(unique = true)
    private String email;
    private String password;
    private String role;  // ADMIN, EMPLOYER, EMPLOYEE
    
    @OneToMany(mappedBy = "employer", cascade = CascadeType.ALL)
    private List<Job> jobs;  // Jobs created by employer
    
    @OneToMany(mappedBy = "employee", cascade = CascadeType.ALL)
    private List<JobApplication> applications;  // Applications by employee
}
```

#### **Job Entity** (`Entity/Job.java`)
Represents a job posting with comprehensive details:

```java
@Entity
@Table(name = "jobs")
public class Job {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    // Core Details
    private String jobTitle;
    private String companyName;
    private String jobDescription;
    private String jobLocation;
    private String workplaceType;  // On-site, Remote, Hybrid
    
    // Requirements
    private String skillsAndQualifications;
    private String experienceLevel;
    private String education;
    
    // Compensation
    private String salaryRange;
    private String jobType;  // Full-time, Part-time, etc.
    
    @ManyToOne
    @JoinColumn(name = "employer_id")
    private User employer;  // Who posted this job
    
    @OneToMany(mappedBy = "job", cascade = CascadeType.ALL)
    private List<JobApplication> applications;
}
```

#### **JobApplication Entity** (`Entity/JobApplication.java`)
Represents an employee's application to a job:

```java
@Entity
@Table(name = "applications")
public class JobApplication {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    private String status;  // PENDING, APPROVED, REJECTED, WITHDRAWN
    
    @ManyToOne
    @JoinColumn(name = "employee_id")
    private User employee;  // Who applied
    
    @ManyToOne
    @JoinColumn(name = "job_id")
    private Job job;  // Which job they applied for
}
```

### 2. **Repositories** (Data Access Layer)

#### **UserRepository** (`Repository/UserRepository.java`)
```java
public interface UserRepository extends JpaRepository<User, Long> {
    Optional<User> findByEmail(String email);  // Find user by email
}
```

#### **JobRepository** (`Repository/JobRepository.java`)
```java
public interface JobRepository extends JpaRepository<Job, Long> {
    // Inherits CRUD operations: findAll(), findById(), save(), delete()
}
```

#### **JobApplicationRepository** (`Repository/JobApplicationRepository.java`)
```java
public interface JobApplicationRepository extends JpaRepository<JobApplication, Long> {
    List<JobApplication> findByJob(Job job);  // All applications for a job
    List<JobApplication> findByEmployee(User employee);  // All applications by employee
    Optional<JobApplication> findByEmployeeAndJob(User employee, Job job);  // Check if already applied
}
```

### 3. **Services** (Business Logic)

#### **UserService** (`Service/UserService.java`)
Handles user authentication:

```java
@Service
public class UserService {
    @Autowired
    private UserRepository userRepository;
    
    // Register a new user
    public User signup(User user) {
        return userRepository.save(user);
    }
    
    // Authenticate user
    public User login(String email, String password) {
        return userRepository.findByEmail(email)
                .filter(u -> u.getPassword().equals(password))
                .orElse(null);
    }
}
```

#### **JobService** (`Service/JobService.java`)
Handles job operations:

```java
@Service
public class JobService {
    @Autowired
    private JobRepository jobRepository;
    
    public Job createJob(Job job) {
        return jobRepository.save(job);
    }
}
```

### 4. **Controllers** (Request Handlers)

#### **HomeController** (`Controller/HomeController.java`)
Handles authentication and page routing:

| Endpoint | Method | Purpose |
|----------|--------|---------|
| `/` | GET | Landing page |
| `/signup` | GET | Signup form page |
| `/signup` | POST | Process signup |
| `/login` | GET | Login form page |
| `/login` | POST | Process login |
| `/logout` | GET | Logout & invalidate session |

**Key Features:**
- Role-based redirection after login
- Session attribute storage for logged-in users
- Input validation and error handling

#### **AdminController** (`Controller/AdminController.java`)
Admin-only operations for system management:

| Endpoint | Method | Purpose |
|----------|--------|---------|
| `/admin/dashboard` | GET | Admin home page |
| `/admin/manage-users` | GET | List all users (filterable by role) |
| `/admin/delete-user/{id}` | POST | Delete a user |
| `/admin/monitor-jobs` | GET | View all jobs in system |
| `/admin/monitor-applications` | GET | View all applications |

**Authorization:**
- All endpoints check if user role is "ADMIN"
- Redirect to login if not authorized

#### **EmployerController** (`Controller/EmployerController.java`)
Job posting and applicant management:

| Endpoint | Method | Purpose |
|----------|--------|---------|
| `/employer/dashboard` | GET | Employer home page |
| `/employer/create-job` | GET | Job creation form |
| `/employer/create-job` | POST | Submit new job posting |
| `/employer/my-jobs` | GET | View employer's own jobs |
| `/employer/job/{jobId}/applicants` | GET | View job applicants |
| `/employer/application/{id}/update-status` | POST | Accept/Reject applicant |

**Key Features:**
- Only show jobs posted by logged-in employer
- Applicants filtered by job
- Status update (APPROVED/REJECTED)

#### **EmployeeController** (`Controller/EmployeeController.java`)
Job search and application management:

| Endpoint | Method | Purpose |
|----------|--------|---------|
| `/employee/dashboard` | GET | Employee home page |
| `/employee/jobs` | GET | Browse all available jobs |
| `/employee/apply/{jobId}` | POST | Apply for a job |
| `/employee/applications` | GET | View all own applications |
| `/employee/applications/{applicationId}/withdraw` | POST | Withdraw application |

**Key Features:**
- Prevent duplicate applications
- Track application status
- Allow withdrawal of pending applications

### 5. **Frontend Templates** (Thymeleaf)

All templates use Thymeleaf template engine with HTML5 structure:

- **index.html**: Landing page with project overview
- **login.html**: User login form
- **signup.html**: User registration with role selection
- **admin-dashboard.html**: Admin control center
- **manage-users.html**: User list with delete functionality
- **monitor-jobs.html**: View all jobs in system
- **monitor-applications.html**: Application tracking
- **employer-dashboard.html**: Employer welcome page
- **create-job.html**: Job creation form with all fields
- **my-jobs.html**: Employer's posted jobs list
- **applicants.html**: List applicants for specific job
- **employee-dashboard.html**: Employee welcome page
- **employee-jobs.html**: Available jobs browser
- **employee-applications.html**: Application history with status

### 6. **Styling** (`static/css/style.css`)

Comprehensive CSS with:
- CSS Variables for consistent theming
- Utility classes (spacing, typography, colors)
- Responsive design utilities
- Navbar styling
- Form styling
- Card and button components
- Transitions and animations

---

## 👥 User Roles & Features

### **ADMIN**
- View all users (filter by role)
- Delete users
- Monitor all jobs in the system
- Monitor all applications
- Full system oversight

### **EMPLOYER**
- Create job postings with detailed information
- View all own posted jobs
- View applicants for each job
- Update application status (APPROVED/REJECTED)
- Manage company job listings

### **EMPLOYEE**
- Browse all available job postings
- Apply for jobs (with duplicate prevention)
- View all own applications and their statuses
- Withdraw applications if pending
- Track application progress

---

## ⚙️ Setup & Configuration

### Prerequisites
- **Java 17** or higher
- **Maven 3.6+**
- **MySQL 8.0+** (running locally)
- **Git** (for cloning)

### Configuration File (`application.properties`)

```properties
# Application Name
spring.application.name=Job-portal

# Database Configuration
spring.datasource.url=jdbc:mysql://localhost:3306/job_portal_db
spring.datasource.username=root
spring.datasource.password=root
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver

# JPA/Hibernate Configuration
spring.jpa.hibernate.ddl-auto=update  # Auto-create tables
spring.jpa.show-sql=true              # Log SQL queries
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL8Dialect
```

### Database Setup

1. Create MySQL database:
```sql
CREATE DATABASE job_portal_db;
USE job_portal_db;
```

2. Tables are auto-created by Hibernate (ddl-auto=update)

### Test User Credentials

Create these users via signup or SQL:

```sql
-- Admin User
INSERT INTO users (name, email, password, role) 
VALUES ('Admin', 'admin@example.com', 'admin123', 'ADMIN');

-- Employer User
INSERT INTO users (name, email, password, role) 
VALUES ('John Employer', 'employer@example.com', 'pass123', 'EMPLOYER');

-- Employee User
INSERT INTO users (name, email, password, role) 
VALUES ('Jane Employee', 'employee@example.com', 'pass123', 'EMPLOYEE');
```

---

## 🚀 Running the Application

### Option 1: Using Maven

```bash
# Navigate to project directory
cd Job-Application-Portal

# Clean and compile
mvn clean compile

# Run the application
mvn spring-boot:run
```

### Option 2: Using Maven Wrapper (Windows)

```bash
# Navigate to project directory
cd Job-Application-Portal

# Run the application
mvnw.cmd spring-boot:run
```

### Option 3: Build JAR and Run

```bash
# Build JAR file
mvn clean package

# Run JAR file
java -jar target/Job-portal-0.0.1-SNAPSHOT.jar
```

### Access the Application

Once running, open your browser and navigate to:
```
http://localhost:8080
```

**Default Port:** 8080 (configured in Spring Boot)

### Login Flow

1. Navigate to `http://localhost:8080/login`
2. Or signup at `http://localhost:8080/signup`
3. Select your role: ADMIN, EMPLOYER, or EMPLOYEE
4. After login, you'll be redirected to role-specific dashboard

---

## 🔐 Security Notes

⚠️ **Current Implementation:**
- Passwords stored as plain text (NOT SECURE)
- Basic session-based authentication
- SQL injection vulnerable to input

⚠️ **For Production, Implement:**
- BCrypt password hashing
- Spring Security framework
- CSRF protection
- Input validation/sanitization
- HTTPS/TLS encryption
- SQL parameterized queries (already using JPA)

---

## 📝 Maven Dependencies (pom.xml)

```xml
<!-- Spring Boot Framework -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>

<!-- JPA/Hibernate ORM -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>

<!-- Thymeleaf Template Engine -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>

<!-- MySQL JDBC Driver -->
<dependency>
    <groupId>com.mysql</groupId>
    <artifactId>mysql-connector-j</artifactId>
    <scope>runtime</scope>
</dependency>

<!-- Testing -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
</dependency>
```

---

## 📚 NPM Dependencies (package.json)

```json
{
  "name": "job-portal",
  "version": "1.0.0",
  "devDependencies": {
    "tailwindcss": "^4.1.13",
    "postcss": "^8.5.6",
    "autoprefixer": "^10.4.21"
  }
}
```

Used for styling with Tailwind CSS utilities.

---

## 🎓 Key Concepts Used

### Spring Boot
- Dependency Injection (@Autowired)
- Stereotypes (@Service, @Controller, @Entity)
- Component Scanning & Auto-Configuration

### JPA/Hibernate
- Entity Relationships (@OneToMany, @ManyToOne)
- Cascade Operations (CascadeType.ALL)
- Query Methods & Derived Queries
- JPQL & Native SQL

### Web Development
- MVC Architecture Pattern
- Session Management (HttpSession)
- Thymeleaf Template Rendering
- HTML Forms (POST/GET)

### Database Design
- Relational Database (MySQL)
- Primary & Foreign Keys
- Cascading Deletes
- Unique Constraints

---

## 🤝 Contributing

To extend this project:
1. Add new endpoints in Controllers
2. Create Entity classes for new features
3. Define Repository methods for data access
4. Add business logic in Services
5. Create Thymeleaf templates for UI
6. Update database configuration as needed

---

## 📄 License

ISC License - See package.json for details

---

## 🔗 Repository

GitHub: https://github.com/vinayak-dhar/Job-Application-Portal

---

**Last Updated:** December 2025
**Version:** 0.0.1-SNAPSHOT
**Java Version:** 17
**Spring Boot Version:** 3.5.5

