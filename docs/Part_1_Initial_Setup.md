# 📘 Part 1 — Initial Project Setup
### School Management System | Next.js + Spring Boot

> **Author:** Sajid Shaikh  
> **Email:** ss2727303@gmail.com  
> **GitHub:** [@sajidbaba1](https://github.com/sajidbaba1)  
> **Date:** April 17, 2026  
> **Repo:** https://github.com/sajidbaba1/school-management.git

---

## 📋 Table of Contents

1. [Tech Stack Overview](#tech-stack-overview)
2. [Prerequisites](#prerequisites)
3. [Step 1 — Configure Git Identity](#step-1--configure-git-identity)
4. [Step 2 — Initialize Git Repository](#step-2--initialize-git-repository)
5. [Step 3 — Create Root README.md](#step-3--create-root-readmemd)
6. [Step 4 — Create Root .gitignore](#step-4--create-root-gitignore)
7. [Step 5 — Create Backend Folder Structure](#step-5--create-backend-folder-structure)
8. [Step 6 — Create Maven pom.xml](#step-6--create-maven-pomxml)
9. [Step 7 — Create Main Application Class](#step-7--create-main-application-class)
10. [Step 8 — Create application.properties](#step-8--create-applicationproperties)
11. [Step 9 — Create Frontend Folder Structure](#step-9--create-frontend-folder-structure)
12. [Step 10 — Git Commit & Push to GitHub](#step-10--git-commit--push-to-github)
13. [Final Project Structure](#final-project-structure)
14. [What's Coming Next](#whats-coming-next)

---

## 🛠️ Tech Stack Overview

| Layer | Technology | Version |
|---|---|---|
| Frontend Framework | Next.js (App Router) | 14.x |
| Frontend Styling | Tailwind CSS | 3.x |
| Frontend UI Library | Shadcn/UI | Latest |
| Frontend State | Redux Toolkit | Latest |
| Frontend Auth | NextAuth.js | Latest |
| Backend Framework | Spring Boot | 3.2.4 |
| Backend Language | Java | 17 |
| Backend Security | Spring Security + JWT | 0.11.5 |
| Database | Neon (PostgreSQL — Serverless) | Latest |
| ORM | Spring Data JPA / Hibernate | Latest |
| Build Tool (Backend) | Maven | 3.8+ |
| Version Control | Git + GitHub | — |

---

## ✅ Prerequisites

Before starting this project, make sure the following tools are installed on your machine:

| Tool | Purpose | Download |
|---|---|---|
| **Node.js 20+** | Run Next.js dev server | https://nodejs.org |
| **Java 17+** | Run Spring Boot | https://adoptium.net |
| **Maven 3.8+** | Build Spring Boot project | https://maven.apache.org |
| **Neon Account** | Cloud PostgreSQL database (free) | https://neon.tech |
| **Git** | Version control | https://git-scm.com |
| **IntelliJ IDEA** | Backend IDE (recommended) | https://jetbrains.com |
| **VS Code** | Frontend IDE (recommended) | https://code.visualstudio.com |
| **Postman** | API testing tool | https://postman.com |

---

## Step 1 — Configure Git Identity

> 🎯 **Purpose:** Tell Git who you are so every commit is tagged with your name and email.

### Command
```bash
git config --global user.name "Sajid Shaikh"
git config --global user.email "ss2727303@gmail.com"
```

### Explanation
- `--global` → Applies to ALL git repositories on your machine
- `user.name` → Your display name on GitHub commits
- `user.email` → Must match your GitHub account email

### Verify
```bash
git config --global --list
```
You should see:
```
user.name=Sajid Shaikh
user.email=ss2727303@gmail.com
```

---

## Step 2 — Initialize Git Repository

> 🎯 **Purpose:** Turn the project folder into a Git-tracked repository.

### Navigate to Project Folder
```bash
cd "c:\project\School Management"
```

### Command
```bash
git init
```

### Output
```
Initialized empty Git repository in C:/project/School Management/.git/
```

### Explanation
- `git init` creates a hidden `.git/` folder inside your project
- This `.git/` folder tracks all changes, history, and branches
- From now on, Git monitors everything inside `School Management/`

---

## Step 3 — Create Root README.md

> 🎯 **Purpose:** Document the project overview visible on GitHub homepage.

### File: `README.md` (at root level)

```markdown
# 🏫 School Management System

A full-stack School Management System built with Next.js (Frontend) and Spring Boot (Backend).
```

**Contains:**
- Tech stack table
- Project structure overview
- Roles (Admin, Teacher, Student, Parent)
- How to run instructions
- Development phases checklist
- Author information

---

## Step 4 — Create Root .gitignore

> 🎯 **Purpose:** Tell Git which files and folders to NEVER track or push to GitHub.

### File: `.gitignore` (at root level)

```gitignore
# FRONTEND
frontend/.next/         ← Next.js build output
frontend/node_modules/  ← Installed packages (huge folder!)
frontend/.env.local     ← Secret API keys

# BACKEND
backend/target/         ← Compiled Java classes
backend/*.jar           ← Built JAR file
backend/*.war           ← Built WAR file

# IDE FILES
.idea/                  ← IntelliJ settings
.vscode/settings.json   ← VS Code settings

# OS FILES
.DS_Store               ← Mac system file
Thumbs.db               ← Windows thumbnail cache
*.log                   ← Log files
```

### Why This Matters
- `node_modules/` can be **500MB+** — never push it!
- `.env.local` contains **secret API keys** — never expose it!
- `target/` contains compiled bytecode — auto-generated, no need

---

## Step 5 — Create Backend Folder Structure

> 🎯 **Purpose:** Create all the Java package folders following Spring Boot MVC architecture.

### Packages Created

```
backend/src/main/java/com/school/management/
├── controller/     ← Handles HTTP requests (@RestController)
├── service/        ← Business logic layer (@Service)
├── repository/     ← Database queries (JpaRepository)
├── model/          ← Entity classes mapped to DB tables (@Entity)
├── dto/            ← Data Transfer Objects (API request/response shapes)
├── config/         ← Security config, CORS config
├── security/       ← JWT filter, UserDetailsService
└── exception/      ← Custom exception classes
```

### Command Used (PowerShell)
```powershell
$folders = @("controller","service","repository","model","dto","config","security","exception")
foreach ($folder in $folders) {
    New-Item -ItemType Directory -Force -Path "$basePath\$folder"
    New-Item -ItemType File -Force -Path "$basePath\$folder\.gitkeep"
}
```

> 💡 **Note:** `.gitkeep` is an empty file placed in empty folders so Git tracks them. Git normally ignores empty directories.

---

## Step 6 — Create Maven pom.xml

> 🎯 **Purpose:** Define all backend dependencies — like `package.json` but for Java/Maven.

### File: `backend/pom.xml`

### Key Dependencies Added

| Dependency | Artifact ID | Purpose |
|---|---|---|
| Spring Web | `spring-boot-starter-web` | Build REST APIs |
| Spring Data JPA | `spring-boot-starter-data-jpa` | Talk to Neon PostgreSQL via ORM |
| Spring Security | `spring-boot-starter-security` | Secure API endpoints |
| Spring Validation | `spring-boot-starter-validation` | Validate request data |
| PostgreSQL Driver | `postgresql` | Connect Java to Neon PostgreSQL |
| JWT API | `jjwt-api` v0.11.5 | Create JWT tokens |
| JWT Impl | `jjwt-impl` v0.11.5 | JWT implementation |
| JWT Jackson | `jjwt-jackson` v0.11.5 | JWT JSON support |
| Lombok | `lombok` | Remove boilerplate code |
| DevTools | `spring-boot-devtools` | Hot reload during dev |
| Spring Test | `spring-boot-starter-test` | Unit testing |

### Project Metadata
```xml
<groupId>com.school</groupId>
<artifactId>school-management</artifactId>
<version>0.0.1-SNAPSHOT</version>
<java.version>17</java.version>
<parent>spring-boot-starter-parent 3.2.4</parent>
```

---

## Step 7 — Create Main Application Class

> 🎯 **Purpose:** The entry point of the entire Spring Boot application.

### File: `backend/src/main/java/com/school/management/SchoolManagementApplication.java`

```java
package com.school.management;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class SchoolManagementApplication {

    public static void main(String[] args) {
        SpringApplication.run(SchoolManagementApplication.class, args);
    }

}
```

### Explanation Line by Line

| Line | Explanation |
|---|---|
| `package com.school.management` | Declares which Java package this class belongs to |
| `@SpringBootApplication` | Combines `@Configuration`, `@EnableAutoConfiguration`, `@ComponentScan` |
| `SpringApplication.run(...)` | Boots up the embedded Tomcat server and starts the app |

### How to Run
```bash
cd backend
mvn spring-boot:run
```
✅ App runs on: `http://localhost:8080/api/v1`

---

## Step 8 — Create application.properties

> 🎯 **Purpose:** Central configuration file for the entire Spring Boot app — database, JWT, ports, logging.

### File: `backend/src/main/resources/application.properties`

### Sections Explained

#### Server Config
```properties
server.port=8080
server.servlet.context-path=/api/v1
```
- All API routes will be prefixed with `/api/v1`
- Example: `http://localhost:8080/api/v1/students`

#### Database Config (Neon PostgreSQL)
```properties
# Get this connection string from: https://console.neon.tech
spring.datasource.url=jdbc:postgresql://<your-neon-host>.neon.tech/school_management_db?sslmode=require
spring.datasource.username=your_neon_username
spring.datasource.password=your_neon_password
spring.datasource.driver-class-name=org.postgresql.Driver
spring.jpa.database-platform=org.hibernate.dialect.PostgreSQLDialect
```
- Go to [console.neon.tech](https://console.neon.tech) → Create Project → Copy the connection string
- `sslmode=require` → Neon requires SSL — always include this!
- No local DB install needed — Neon is fully cloud-based ☁️

#### JPA/Hibernate Config
```properties
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
```
- `ddl-auto=update` → Auto creates/updates tables based on your `@Entity` classes
- `show-sql=true` → Prints SQL queries in terminal (useful for debugging)

#### JWT Config
```properties
app.jwt.secret=school_management_super_secret_key
app.jwt.expiration=86400000
```
- `expiration=86400000` → Token expires in **24 hours** (milliseconds)
- ⚠️ Change the secret key before going to production!

---

## Step 9 — Create Frontend Folder Structure

> 🎯 **Purpose:** Pre-create all Next.js app directories following the App Router pattern.

### Structure Created

```
frontend/
├── app/
│   ├── (auth)/
│   │   └── login/           ← Login page
│   └── (dashboard)/
│       ├── admin/            ← Admin pages
│       ├── teacher/          ← Teacher pages
│       └── student/          ← Student pages
├── components/
│   ├── ui/                   ← Shadcn/UI components
│   ├── layout/               ← Sidebar, Navbar
│   └── shared/               ← Reusable components
├── lib/                      ← axios instance, utilities
├── store/                    ← Redux Toolkit global state (slices)
├── types/                    ← TypeScript interfaces
└── public/                   ← Static assets (images, icons)
```

### Why Route Groups `(auth)` and `(dashboard)`?
- Parentheses `()` in folder names = **Route Groups** in Next.js
- They group routes WITHOUT affecting the URL path
- Example: `app/(dashboard)/admin/page.tsx` → URL is `/admin` not `/dashboard/admin`
- Each group can have its **own layout** (e.g., dashboard has sidebar, auth pages don't)

---

## Step 10 — Git Commit & Push to GitHub

> 🎯 **Purpose:** Save all our work and push it to the remote GitHub repository.

### Step A — Stage All Files
```bash
git add .
```
- `.` means "add ALL new and changed files"

### Step B — Create Commit
```bash
git commit -m "Part 1: Initial project setup - folder structure, pom.xml, app config"
```
- A commit is a **saved snapshot** of your code at this point
- The message describes **what** was done

### Step C — Set Main Branch
```bash
git branch -M main
```
- Renames default branch from `master` to `main` (GitHub standard)

### Step D — Connect to Remote GitHub
```bash
git remote add origin https://github.com/sajidbaba1/school-management.git
```
- `origin` is the alias for your remote GitHub repo URL
- Links your local repo to the GitHub repo

### Step E — Push to GitHub
```bash
git push -u origin main --force
```
- `-u` sets `origin/main` as the default upstream (future: just `git push`)
- `--force` was needed because GitHub repo already had an initial README

### ✅ Result
```
branch 'main' set up to track 'origin/main'.
To https://github.com/sajidbaba1/school-management.git
 + 4610893...06bc65f main -> main (forced update)
```

---

## 📁 Final Project Structure

```
school-management/
├── .gitignore
├── README.md
├── backend/
│   ├── README.md
│   ├── pom.xml
│   └── src/
│       └── main/
│           ├── java/com/school/management/
│           │   ├── SchoolManagementApplication.java  ✅
│           │   ├── controller/.gitkeep
│           │   ├── service/.gitkeep
│           │   ├── repository/.gitkeep
│           │   ├── model/.gitkeep
│           │   ├── dto/.gitkeep
│           │   ├── config/.gitkeep
│           │   ├── security/.gitkeep
│           │   └── exception/.gitkeep
│           └── resources/
│               └── application.properties  ✅
└── frontend/
    ├── README.md
    ├── app/
    │   ├── (auth)/login/.gitkeep
    │   └── (dashboard)/
    │       ├── admin/.gitkeep
    │       ├── teacher/.gitkeep
    │       └── student/.gitkeep
    ├── components/
    │   ├── ui/.gitkeep
    │   ├── layout/.gitkeep
    │   └── shared/.gitkeep
    ├── lib/.gitkeep
    ├── store/.gitkeep
    ├── types/.gitkeep
    └── public/.gitkeep
```

---

## 🔜 What's Coming Next

| Part | Topic |
|---|---|
| **Part 2** | Initialize Next.js app + Install Tailwind + Shadcn/UI |
| **Part 3** | Database Design — Create all PostgreSQL Entities (Student, Teacher, Fee, etc.) |
| **Part 4** | Spring Security + JWT Authentication setup |
| **Part 5** | Login & Register APIs (Backend) + Login Page (Frontend) |
| **Part 6** | Student Management Module (CRUD) |

---

> ✅ **Part 1 Complete!** Your project skeleton is live on GitHub.  
> 🔗 https://github.com/sajidbaba1/school-management
