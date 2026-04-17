# 📚 Topics to Learn Before Starting the School Management System
### School Management System | Next.js + Spring Boot

> **Author:** Sajid Shaikh | ss2727303@gmail.com
> **Repo:** https://github.com/sajidbaba1/school-management.git

---

## 🟦 Next.js (Frontend)

### 1. Core Fundamentals
- [ ] **App Router vs Pages Router** — We'll use the **App Router** (Next.js 13+)
- [ ] **File-based Routing** — `app/page.tsx`, `app/students/page.tsx`, nested routes
- [ ] **Layouts** — `layout.tsx` for shared UI (sidebar, navbar)
- [ ] **Loading & Error Pages** — `loading.tsx`, `error.tsx`
- [ ] **Route Groups** — `(auth)`, `(dashboard)` for organizing layouts without affecting URL

### 2. Data Fetching
- [ ] **`fetch()` in Server Components** — Fetching from Spring Boot APIs server-side
- [ ] **`useEffect` + `axios`** in Client Components
- [ ] **Client vs Server Components** — When to use `"use client"` directive
- [ ] **React Query** *(optional — for client-side caching)*

### 3. State Management — Redux Toolkit ⭐
- [ ] **`configureStore`** — Setting up the central Redux store
- [ ] **`createSlice`** — Combining actions + reducers together
- [ ] **`createAsyncThunk`** — Handling async API calls (login, fetch data)
- [ ] **`useSelector`** — Reading data from Redux store in components
- [ ] **`useDispatch`** — Triggering actions from components
- [ ] **Redux Provider** — Wrapping the app with `<Provider store={store}>`
- [ ] **Redux DevTools** — Browser extension for debugging state

### 4. Routing & Navigation
- [ ] **`useRouter`, `usePathname`** — Programmatic navigation
- [ ] **Dynamic Routes** — `/students/[id]/page.tsx`
- [ ] **`Link` component** — Client-side navigation without page reload

### 5. Authentication
- [ ] **NextAuth.js** — JWT-based auth integrated with Spring Boot
- [ ] **Middleware (`middleware.ts`)** — Protecting routes based on roles (Admin, Teacher, Student)
- [ ] **`httpOnly` Cookies** — Securely storing JWT tokens

### 6. UI & Styling
- [ ] **Tailwind CSS** — Utility-first styling (we'll use this)
- [ ] **Shadcn/UI** — Beautiful pre-built components (tables, forms, dialogs)
- [ ] **React Hook Form + Zod** — Form creation + schema-based validation

### 7. API Integration
- [ ] **axios** — Making HTTP calls to Spring Boot APIs
- [ ] **Axios Interceptors** — Auto-attach JWT token to every request
- [ ] **Environment Variables** — `.env.local` for `NEXT_PUBLIC_API_URL`
- [ ] **API Error Handling** — Try-catch + global error interceptor

---

## 🟥 Spring Boot (Backend)

### 1. Core Spring Boot
- [ ] **Spring Initializr** — Generate project at `https://start.spring.io`
- [ ] **`application.properties`** — Database config, port, JWT secret
- [ ] **Project Structure** — `controller`, `service`, `repository`, `model`, `dto`, `security`
- [ ] **`@SpringBootApplication`** — The main entry point annotation

### 2. REST API Development
- [ ] **`@RestController`, `@RequestMapping`** — Declare API controllers
- [ ] **`@GetMapping`, `@PostMapping`, `@PutMapping`, `@DeleteMapping`**
- [ ] **`@RequestBody`, `@PathVariable`, `@RequestParam`**
- [ ] **`ResponseEntity<T>`** — Returning proper HTTP status codes + body
- [ ] **DTOs (Data Transfer Objects)** — Separating DB entities from API responses

### 3. Database — Neon PostgreSQL + JPA ⭐
- [ ] **Neon Setup** — Create free account at `https://neon.tech` → copy connection string
- [ ] **Spring Data JPA** — `@Entity`, `@Table`, `@Column`, `@Id`
- [ ] **Entity Relationships** — `@OneToMany`, `@ManyToOne`, `@ManyToMany`
- [ ] **`JpaRepository`** — Built-in CRUD without writing SQL
- [ ] **PostgreSQL Dialect** — `org.hibernate.dialect.PostgreSQLDialect` in properties
- [ ] **`sslmode=require`** — Neon requires SSL in the connection string

### 4. Security (Very Important!)
- [ ] **Spring Security** — Securing API endpoints with filter chain
- [ ] **JWT (JSON Web Token)** — Generating and validating tokens with `jjwt` library
- [ ] **Role-Based Access Control** — `ADMIN`, `TEACHER`, `STUDENT`, `PARENT` roles
- [ ] **`@PreAuthorize`** — Method-level security annotations
- [ ] **`BCryptPasswordEncoder`** — Hashing passwords before saving to DB

### 5. Validation & Error Handling
- [ ] **`@Valid`, `@NotBlank`, `@Email`, `@Min`** — Request body validation
- [ ] **`@ControllerAdvice`** — Global exception handler class
- [ ] **Custom Exception Classes** — `ResourceNotFoundException`, `DuplicateResourceException`

### 6. CORS Configuration
- [ ] **`WebMvcConfigurer`** — Allow Next.js (port 3000) to call Spring Boot (port 8080)
- [ ] **Allowed Origins** — Whitelist specific frontend URLs

### 7. Advanced (Learn as You Build)
- [ ] **Pagination & Sorting** — `Pageable`, `PageRequest.of()` in repositories
- [ ] **File Upload** — `MultipartFile` for profile pictures, documents
- [ ] **Email Service** — `JavaMailSender` for fee reminders, notifications
- [ ] **Lombok** — `@Data`, `@Builder`, `@NoArgsConstructor`, `@Slf4j`

---

## 🗓️ Suggested Learning Order

```
Week 1:  Spring Boot Basics → REST APIs → JPA + Neon PostgreSQL setup
Week 2:  Spring Security → JWT → Role-based access control
Week 3:  Next.js Basics → App Router → Layouts → Data Fetching
Week 4:  Redux Toolkit → createSlice → createAsyncThunk → useSelector
Week 5:  Connect Frontend to Backend → Axios → Redux Auth Slice → Login Page
Week 6+: Start the PROJECT module by module!
```

---

## 🛠️ Tools to Install Before Starting

| Tool | Purpose | Download |
|---|---|---|
| **Node.js 20+** | Run Next.js dev server | https://nodejs.org |
| **Java 17+** | Run Spring Boot | https://adoptium.net |
| **Maven 3.8+** | Build Spring Boot project | https://maven.apache.org |
| **VS Code** | Frontend (Next.js) IDE | https://code.visualstudio.com |
| **IntelliJ IDEA** | Backend (Spring Boot) IDE | https://jetbrains.com |
| **Git** | Version control | https://git-scm.com |
| **Postman** | Test REST APIs | https://postman.com |
| **Neon Account (Free)** | Cloud PostgreSQL DB | https://neon.tech |

---

## 💻 VS Code Terminal Commands to Initialize Projects

### ✅ Initialize Next.js Frontend

Open VS Code terminal (`Ctrl + `` `), make sure you're in the project root, then run:

```bash
# Navigate into the frontend folder
cd frontend

# Create Next.js app INSIDE the frontend folder
npx create-next-app@latest . --typescript --tailwind --eslint --app --src-dir --import-alias "@/*"
```

**What each flag means:**

| Flag | Meaning |
|---|---|
| `.` | Create inside the current directory (frontend/) |
| `--typescript` | Use TypeScript (industry standard) |
| `--tailwind` | Auto-setup Tailwind CSS |
| `--eslint` | Add ESLint for code quality |
| `--app` | Use the App Router (NOT the old Pages Router) |
| `--src-dir` | Put code inside a `src/` folder |
| `--import-alias "@/*"` | Use `@/` as shortcut for imports |

**After installation, install additional packages:**

```bash
# Redux Toolkit + React-Redux
npm install @reduxjs/toolkit react-redux

# Axios for API calls
npm install axios

# React Hook Form + Zod for form validation
npm install react-hook-form zod @hookform/resolvers

# Shadcn/UI setup (run separately)
npx shadcn-ui@latest init

# Run the frontend dev server
npm run dev
```

✅ Frontend runs at: `http://localhost:3000`

---

### ✅ Initialize Spring Boot Backend (VS Code Terminal)

Open a **new terminal tab** in VS Code (`Ctrl + Shift + `` `), go back to root:

```bash
# Go back to project root first
cd ..

# Navigate into backend folder
cd backend
```

**Option A — Download from Spring Initializr Website (Recommended for Beginners)**

```
1. Open browser → Go to https://start.spring.io
2. Fill in:
   - Project:      Maven
   - Language:     Java
   - Spring Boot:  3.2.4
   - Group:        com.school
   - Artifact:     management
   - Name:         school-management
   - Packaging:    Jar
   - Java:         17
3. Add Dependencies:
   ✅ Spring Web
   ✅ Spring Data JPA
   ✅ Spring Security
   ✅ Validation
   ✅ PostgreSQL Driver
   ✅ Lombok
   ✅ Spring Boot DevTools
4. Click: GENERATE
5. Extract the downloaded ZIP into: C:\project\School Management\backend\
6. Open the backend folder in IntelliJ IDEA
```

**Option B — Using Spring Boot CLI in Terminal**

```bash
# If you have Spring Boot CLI installed:
spring init \
  --dependencies=web,data-jpa,security,validation,postgresql,lombok,devtools \
  --build=maven \
  --java-version=17 \
  --group-id=com.school \
  --artifact-id=school-management \
  --name=school-management \
  .
```

**Run the backend:**

```bash
# In backend folder
mvn spring-boot:run
```

✅ Backend runs at: `http://localhost:8080/api/v1`

---

## 📂 What VS Code Explorer Looks Like After Both Are Initialized

```
📁 SCHOOL MANAGEMENT (opened in VS Code)
├── 📁 backend/
│   ├── 📁 src/
│   │   └── 📁 main/
│   │       ├── 📁 java/com/school/management/
│   │       │   └── SchoolManagementApplication.java
│   │       └── 📁 resources/
│   │           └── application.properties
│   └── pom.xml
├── 📁 frontend/
│   ├── 📁 src/
│   │   └── 📁 app/
│   │       ├── layout.tsx
│   │       └── page.tsx
│   ├── package.json
│   ├── tailwind.config.ts
│   └── tsconfig.json
├── 📁 docs/
├── .gitignore
└── README.md
```

---

> **Pro Tip:** You don't need to master everything before starting. Learn the basics, then learn as you build. The project itself is the best teacher! 🚀
