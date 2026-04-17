# 🏗️ Industry Architecture Best Practices
### School Management System — Next.js + Spring Boot

> **Author:** Sajid Shaikh | ss2727303@gmail.com
> **Repo:** https://github.com/sajidbaba1/school-management.git

---

## 📋 Table of Contents

1. [Layered Architecture (Backend)](#1-layered-architecture-backend)
2. [RESTful API Design Standards](#2-restful-api-design-standards)
3. [DTO Pattern — Never Expose Entities](#3-dto-pattern--never-expose-entities)
4. [Repository Pattern](#4-repository-pattern)
5. [JWT-Based Stateless Authentication](#5-jwt-based-stateless-authentication)
6. [Role-Based Access Control (RBAC)](#6-role-based-access-control-rbac)
7. [Global Exception Handling](#7-global-exception-handling)
8. [Environment-Based Configuration](#8-environment-based-configuration)
9. [CORS Configuration](#9-cors-configuration)
10. [Next.js App Router Architecture](#10-nextjs-app-router-architecture)
11. [API Layer Abstraction (Frontend)](#11-api-layer-abstraction-frontend)
12. [State Management Strategy (Frontend)](#12-state-management-strategy-frontend)
13. [Form Validation Strategy](#13-form-validation-strategy)
14. [Component Architecture (Frontend)](#14-component-architecture-frontend)
15. [Route Protection & Middleware](#15-route-protection--middleware)
16. [Database Design Best Practices](#16-database-design-best-practices)
17. [Pagination & Sorting](#17-pagination--sorting)
18. [Logging Best Practices](#18-logging-best-practices)
19. [Security Hardening Checklist](#19-security-hardening-checklist)
20. [Git Branching Strategy](#20-git-branching-strategy)

---

## 1. Layered Architecture (Backend)

> **What it is:** Organizing Spring Boot code into distinct layers, each with a single responsibility.

### The 4 Layers

```
┌─────────────────────────────────────┐
│         CONTROLLER LAYER            │  ← Handles HTTP Requests/Responses
├─────────────────────────────────────┤
│          SERVICE LAYER              │  ← Business Logic Lives Here
├─────────────────────────────────────┤
│        REPOSITORY LAYER             │  ← Database Queries (JPA)
├─────────────────────────────────────┤
│          MODEL LAYER                │  ← Entity classes = DB Tables
└─────────────────────────────────────┘
```

### Rule: Each layer only talks to the one directly below it

```
HTTP Request
     ↓
Controller  → calls → Service  → calls → Repository  → calls → Database
Controller  ← returns ← Service  ← returns ← Repository  ← returns ← Data
     ↓
HTTP Response
```

### Example Flow: Create a Student

```java
// 1. CONTROLLER — receives HTTP POST /api/v1/students
@RestController
@RequestMapping("/students")
public class StudentController {
    private final StudentService studentService;

    @PostMapping
    public ResponseEntity<StudentDTO> createStudent(@RequestBody @Valid CreateStudentRequest request) {
        StudentDTO student = studentService.createStudent(request);  // calls service
        return ResponseEntity.status(HttpStatus.CREATED).body(student);
    }
}

// 2. SERVICE — business rules (check duplicate, set defaults)
@Service
public class StudentService {
    private final StudentRepository studentRepository;

    public StudentDTO createStudent(CreateStudentRequest request) {
        // Business rule: check if roll number already exists
        if (studentRepository.existsByRollNumber(request.getRollNumber())) {
            throw new DuplicateResourceException("Roll number already exists");
        }
        Student student = mapToEntity(request);     // convert DTO to entity
        student = studentRepository.save(student);  // calls repository
        return mapToDTO(student);                   // return DTO, not entity
    }
}

// 3. REPOSITORY — just database operations
@Repository
public interface StudentRepository extends JpaRepository<Student, Long> {
    boolean existsByRollNumber(String rollNumber);
    List<Student> findByClassId(Long classId);
}

// 4. MODEL — maps to DB table
@Entity
@Table(name = "students")
public class Student {
    @Id @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;
    private String rollNumber;
    // ...
}
```

### ✅ Why This Matters
- **Testability** — You can unit test each layer independently
- **Maintainability** — Change database? Only touch repository. Change UI? Only touch controller.
- **Team Work** — Different developers can work on different layers simultaneously

---

## 2. RESTful API Design Standards

> **What it is:** A consistent naming convention for all your API endpoints.

### URL Design Rules

| Rule | ❌ Bad | ✅ Good |
|---|---|---|
| Use nouns, not verbs | `/getStudents` | `/students` |
| Use plural nouns | `/student` | `/students` |
| Use hyphens for spaces | `/studentFees` | `/student-fees` |
| Nest related resources | `/fees?student=1` | `/students/1/fees` |
| Use HTTP methods for actions | `/createStudent` (POST) | `/students` (POST) |

### HTTP Methods — What Each Does

| Method | Purpose | Example URL | Response Code |
|---|---|---|---|
| `GET` | Fetch data | `GET /students` | 200 OK |
| `POST` | Create new | `POST /students` | 201 Created |
| `PUT` | Update entire record | `PUT /students/1` | 200 OK |
| `PATCH` | Update partial record | `PATCH /students/1` | 200 OK |
| `DELETE` | Delete record | `DELETE /students/1` | 204 No Content |

### Our School System API Design

```
/api/v1/
├── /auth/
│   ├── POST   /login           ← Get JWT token
│   ├── POST   /register        ← Create account
│   └── POST   /refresh-token   ← Refresh expired JWT
│
├── /students/
│   ├── GET    /                ← List all students (paginated)
│   ├── POST   /                ← Create new student
│   ├── GET    /{id}            ← Get student by ID
│   ├── PUT    /{id}            ← Update student
│   ├── DELETE /{id}            ← Delete student
│   └── GET    /{id}/fees       ← Get fees of specific student
│
├── /teachers/
│   ├── GET    /
│   ├── POST   /
│   ├── GET    /{id}
│   └── GET    /{id}/timetable  ← Teacher's schedule
│
├── /fees/
│   ├── GET    /                ← All fee records
│   ├── POST   /                ← Create fee record
│   └── GET    /pending         ← Only unpaid fees
│
└── /attendance/
    ├── POST   /                ← Mark attendance
    └── GET    /student/{id}    ← Student's attendance
```

### Standard API Response Format

**Always return consistent JSON structure:**

```json
// ✅ Success Response
{
  "success": true,
  "message": "Student created successfully",
  "data": {
    "id": 1,
    "name": "Ahmed Ali",
    "rollNumber": "A-001"
  },
  "timestamp": "2026-04-17T07:00:00"
}

// ✅ Error Response
{
  "success": false,
  "message": "Student not found",
  "errorCode": "STUDENT_NOT_FOUND",
  "timestamp": "2026-04-17T07:00:00"
}

// ✅ Paginated List Response
{
  "success": true,
  "data": [...],
  "pagination": {
    "page": 0,
    "size": 10,
    "totalElements": 150,
    "totalPages": 15
  }
}
```

---

## 3. DTO Pattern — Never Expose Entities

> **What it is:** DTOs (Data Transfer Objects) are separate classes that control EXACTLY what data goes in and out of your API — never your raw database entities.

### Why This is Critical

```
❌ WITHOUT DTO (DANGEROUS):
Client sends → @RequestBody Student student
Client sees  → @ResponseBody Student student
               ↑ this exposes fields like "password", "createdAt", internal IDs!

✅ WITH DTO (SAFE):
Client sends → @RequestBody CreateStudentRequest (only what you need)
Client sees  → @ResponseBody StudentResponse (only what you want to show)
```

### Implementation Pattern

```java
// 📥 Request DTO — what client sends when CREATING a student
public class CreateStudentRequest {
    @NotBlank(message = "Name is required")
    private String name;

    @NotBlank(message = "Roll number is required")
    private String rollNumber;

    @Email(message = "Invalid email format")
    private String email;

    @Min(value = 1, message = "Class must be between 1-12")
    @Max(value = 12)
    private int classGrade;
}

// 📤 Response DTO — what client receives (you control what's visible)
public class StudentResponse {
    private Long id;
    private String name;
    private String rollNumber;
    private String email;
    private String className;
    private String profilePicture;
    // ❌ NO password field here
    // ❌ NO internal audit fields
}

// 📝 Update DTO — what client sends when UPDATING
public class UpdateStudentRequest {
    private String name;       // nullable = partial update allowed
    private String email;
    private String phone;
    // rollNumber NOT included = cannot be changed
}
```

### Mapping Entity ↔ DTO

```java
// In Service Layer
private StudentResponse mapToDTO(Student student) {
    StudentResponse dto = new StudentResponse();
    dto.setId(student.getId());
    dto.setName(student.getName());
    dto.setEmail(student.getEmail());
    // Use MapStruct library to automate this!
    return dto;
}
```

---

## 4. Repository Pattern

> **What it is:** All database interaction is abstracted behind interfaces — the service layer never writes SQL directly.

### JPA Repository Hierarchy

```
JpaRepository<Entity, ID>
    └── Provides: save(), findById(), findAll(), delete(), count(), existsById()

CrudRepository<Entity, ID>
    └── Provides basic CRUD

PagingAndSortingRepository<Entity, ID>
    └── Provides: findAll(Pageable)
```

### Custom Query Methods — Naming Convention Magic

```java
@Repository
public interface StudentRepository extends JpaRepository<Student, Long> {

    // Spring Data JPA generates the SQL automatically from method names!

    // SELECT * FROM students WHERE email = ?
    Optional<Student> findByEmail(String email);

    // SELECT * FROM students WHERE class_grade = ?
    List<Student> findByClassGrade(int grade);

    // SELECT * FROM students WHERE name LIKE '%?%'
    List<Student> findByNameContaining(String name);

    // SELECT * FROM students WHERE class_grade = ? AND is_active = ?
    List<Student> findByClassGradeAndIsActive(int grade, boolean isActive);

    // Custom JPQL query when naming convention isn't enough
    @Query("SELECT s FROM Student s WHERE s.feeStatus = 'PENDING'")
    List<Student> findStudentsWithPendingFees();

    // Check existence
    boolean existsByRollNumber(String rollNumber);

    // Count
    long countByClassGrade(int grade);
}
```

---

## 5. JWT-Based Stateless Authentication

> **What it is:** Instead of storing sessions on the server, we issue a signed token to the client. The server verifies the token on every request — **no session storage needed**.

### How JWT Works in Our System

```
1. Client sends: POST /api/v1/auth/login { email, password }
             ↓
2. Server verifies credentials against DB
             ↓
3. Server generates JWT token:
   Header: { algorithm: "HS256", type: "JWT" }
   Payload: { userId: 1, email: "admin@school.com", role: "ADMIN", exp: 24h }
   Signature: HMACSHA256(header + payload, SECRET_KEY)
             ↓
4. Client stores token in: localStorage or httpOnly Cookie
             ↓
5. Every subsequent request sends:
   Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5...
             ↓
6. Server JwtFilter intercepts → validates token → proceeds
```

### JWT Filter Chain

```java
// This runs on EVERY request before hitting Controller
@Component
public class JwtAuthenticationFilter extends OncePerRequestFilter {

    @Override
    protected void doFilterInternal(HttpServletRequest request,
                                    HttpServletResponse response,
                                    FilterChain filterChain) {
        // 1. Extract token from Authorization header
        String authHeader = request.getHeader("Authorization");
        if (authHeader == null || !authHeader.startsWith("Bearer ")) {
            filterChain.doFilter(request, response);  // No token, skip
            return;
        }

        // 2. Extract the token string
        String token = authHeader.substring(7);  // Remove "Bearer " prefix

        // 3. Validate token
        if (jwtService.isTokenValid(token)) {
            // 4. Set authentication in Spring Security context
            String email = jwtService.extractEmail(token);
            UserDetails userDetails = userDetailsService.loadUserByUsername(email);
            UsernamePasswordAuthenticationToken authToken =
                new UsernamePasswordAuthenticationToken(userDetails, null, userDetails.getAuthorities());
            SecurityContextHolder.getContext().setAuthentication(authToken);
        }

        filterChain.doFilter(request, response); // Continue
    }
}
```

---

## 6. Role-Based Access Control (RBAC)

> **What it is:** Different users see and can do different things based on their assigned role.

### Roles in Our School System

```
ADMIN      → Full access — manage everything
TEACHER    → Can enter marks, take attendance, view students in their class
STUDENT    → Can view own results, fees, attendance
PARENT     → Can view their child's data only
```

### Implementation in Spring Security

```java
// Security Config — defining who can access what
@Configuration
@EnableMethodSecurity
public class SecurityConfig {

    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
        http
            .authorizeHttpRequests(auth -> auth
                // Public endpoints — no token needed
                .requestMatchers("/api/v1/auth/**").permitAll()

                // Admin only
                .requestMatchers(HttpMethod.DELETE, "/api/v1/**").hasRole("ADMIN")
                .requestMatchers("/api/v1/admin/**").hasRole("ADMIN")

                // Teacher and Admin
                .requestMatchers("/api/v1/attendance/**").hasAnyRole("TEACHER", "ADMIN")
                .requestMatchers("/api/v1/marks/**").hasAnyRole("TEACHER", "ADMIN")

                // Any authenticated user
                .anyRequest().authenticated()
            );
        return http.build();
    }
}

// Method-level security on specific endpoints
@RestController
public class StudentController {

    @GetMapping("/{id}")
    @PreAuthorize("hasRole('ADMIN') or @studentSecurity.isOwner(#id)")
    public ResponseEntity<StudentResponse> getStudent(@PathVariable Long id) {
        // Admin can see any student, students can only see themselves
    }

    @DeleteMapping("/{id}")
    @PreAuthorize("hasRole('ADMIN')")
    public ResponseEntity<Void> deleteStudent(@PathVariable Long id) {
        // Only ADMIN can delete
    }
}
```

---

## 7. Global Exception Handling

> **What it is:** Instead of try-catch in every controller, one central class handles ALL exceptions and returns clean, consistent error responses.

```java
@RestControllerAdvice  // ← Applies to ALL controllers
public class GlobalExceptionHandler {

    // 404 — Resource not found
    @ExceptionHandler(ResourceNotFoundException.class)
    public ResponseEntity<ApiErrorResponse> handleNotFound(ResourceNotFoundException ex) {
        ApiErrorResponse error = new ApiErrorResponse(
            false,
            ex.getMessage(),
            "RESOURCE_NOT_FOUND",
            LocalDateTime.now()
        );
        return ResponseEntity.status(HttpStatus.NOT_FOUND).body(error);
    }

    // 409 — Duplicate data
    @ExceptionHandler(DuplicateResourceException.class)
    public ResponseEntity<ApiErrorResponse> handleDuplicate(DuplicateResourceException ex) {
        return ResponseEntity.status(HttpStatus.CONFLICT)
            .body(new ApiErrorResponse(false, ex.getMessage(), "DUPLICATE", LocalDateTime.now()));
    }

    // 400 — Validation errors (@Valid failed)
    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<Map<String, String>> handleValidation(MethodArgumentNotValidException ex) {
        Map<String, String> errors = new HashMap<>();
        ex.getBindingResult().getFieldErrors().forEach(err ->
            errors.put(err.getField(), err.getDefaultMessage())
        );
        return ResponseEntity.badRequest().body(errors);
    }

    // 500 — Catch-all
    @ExceptionHandler(Exception.class)
    public ResponseEntity<ApiErrorResponse> handleAll(Exception ex) {
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR)
            .body(new ApiErrorResponse(false, "Something went wrong", "INTERNAL_ERROR", LocalDateTime.now()));
    }
}
```

---

## 8. Environment-Based Configuration

> **What it is:** Different settings for Development, Testing, and Production — never hardcode secrets!

### Spring Boot Profiles

```
application.properties           ← Common config (shared)
application-dev.properties       ← Development (local MySQL, verbose logs)
application-prod.properties      ← Production (cloud DB, minimal logs)
```

```properties
# application.properties (shared base)
spring.application.name=school-management
server.port=8080

# application-dev.properties
spring.datasource.url=jdbc:postgresql://localhost:5432/school_dev_db
spring.jpa.show-sql=true
logging.level.com.school=DEBUG

# application-prod.properties
spring.datasource.url=${DATABASE_URL}        ← From environment variable
spring.datasource.username=${DB_USERNAME}    ← Never hardcode in prod!
spring.datasource.password=${DB_PASSWORD}
spring.jpa.show-sql=false
logging.level.com.school=WARN
```

### Next.js Environment Files

```bash
.env.local               ← Local development (gitignored)
.env.development         ← Dev environment
.env.production          ← Production

# .env.local
NEXT_PUBLIC_API_URL=http://localhost:8080/api/v1
NEXTAUTH_SECRET=your_nextauth_secret
NEXTAUTH_URL=http://localhost:3000

# .env.production
NEXT_PUBLIC_API_URL=https://api.yourschool.com/api/v1
```

> ⚠️ Variables starting with `NEXT_PUBLIC_` are exposed to the browser. Never put secrets there!

---

## 9. CORS Configuration

> **What it is:** Telling your Spring Boot backend which frontend URLs are allowed to call it.

```java
@Configuration
public class CorsConfig implements WebMvcConfigurer {

    @Override
    public void addCorsMappings(CorsRegistry registry) {
        registry.addMapping("/api/v1/**")
            .allowedOrigins(
                "http://localhost:3000",           // Dev frontend
                "https://school.yourdomain.com"   // Prod frontend
            )
            .allowedMethods("GET", "POST", "PUT", "PATCH", "DELETE", "OPTIONS")
            .allowedHeaders("*")
            .exposedHeaders("Authorization")
            .allowCredentials(true)
            .maxAge(3600);  // Cache preflight for 1 hour
    }
}
```

---

## 10. Next.js App Router Architecture

> **What it is:** Organizing the Next.js frontend for scalability using the built-in App Router.

### Route Groups & Layouts

```
app/
├── layout.tsx                    ← Root layout (applies to ALL pages)
├── page.tsx                      ← "/" → Redirects to login or dashboard
│
├── (auth)/                       ← Auth route group (NO sidebar)
│   ├── layout.tsx                ← Auth layout (centered card)
│   └── login/
│       └── page.tsx              ← /login
│
└── (dashboard)/                  ← Dashboard route group (WITH sidebar)
    ├── layout.tsx                ← Dashboard layout (sidebar + topnav)
    ├── admin/
    │   ├── page.tsx              ← /admin (Admin dashboard)
    │   ├── students/
    │   │   ├── page.tsx          ← /admin/students (list)
    │   │   └── [id]/
    │   │       └── page.tsx      ← /admin/students/42 (detail)
    │   ├── teachers/page.tsx
    │   └── fees/page.tsx
    ├── teacher/
    │   ├── page.tsx              ← /teacher (Teacher dashboard)
    │   └── marks/page.tsx
    └── student/
        ├── page.tsx              ← /student (Student dashboard)
        └── results/page.tsx
```

### Server vs Client Components

```tsx
// ✅ SERVER COMPONENT (default) — fetches data on server
// app/(dashboard)/admin/students/page.tsx
async function StudentsPage() {
    const students = await fetch(`${process.env.API_URL}/students`, {
        headers: { Authorization: `Bearer ${getServerToken()}` }
    }).then(res => res.json());

    return <StudentsTable data={students} />;  // No loading state needed!
}

// ✅ CLIENT COMPONENT — uses state, browser APIs, event handlers
// components/shared/SearchBar.tsx
"use client";
function SearchBar({ onSearch }) {
    const [query, setQuery] = useState("");

    return (
        <input
            value={query}
            onChange={(e) => { setQuery(e.target.value); onSearch(e.target.value); }}
        />
    );
}
```

---

## 11. API Layer Abstraction (Frontend)

> **What it is:** One central axios instance with all configurations — baseURL, headers, interceptors.

```ts
// lib/api.ts — Central API client
import axios from "axios";

const api = axios.create({
    baseURL: process.env.NEXT_PUBLIC_API_URL,
    headers: { "Content-Type": "application/json" },
    timeout: 10000,  // 10 second timeout
});

// Request interceptor — attach token to every request automatically
api.interceptors.request.use((config) => {
    const token = localStorage.getItem("token");
    if (token) config.headers.Authorization = `Bearer ${token}`;
    return config;
});

// Response interceptor — handle 401 globally
api.interceptors.response.use(
    (response) => response,
    (error) => {
        if (error.response?.status === 401) {
            localStorage.removeItem("token");
            window.location.href = "/login";
        }
        return Promise.reject(error);
    }
);

export default api;

// ─────────────────────────────────────
// lib/services/studentService.ts — API calls for students
import api from "../api";

export const studentService = {
    getAll: (page = 0, size = 10) =>
        api.get(`/students?page=${page}&size=${size}`),

    getById: (id: number) =>
        api.get(`/students/${id}`),

    create: (data: CreateStudentRequest) =>
        api.post("/students", data),

    update: (id: number, data: UpdateStudentRequest) =>
        api.put(`/students/${id}`, data),

    delete: (id: number) =>
        api.delete(`/students/${id}`),
};
```

---

## 12. State Management Strategy (Frontend)

> **What it is:** Deciding what state lives where.

### State Hierarchy

```
Local State (useState)          → UI state: modal open/close, form inputs
                                   Don't need to share with other components

Server State (React Query)      → Data from APIs: students list, teacher data
                                   Handles caching, loading, refetching automatically

Global State (Redux Toolkit)    → Auth user, user role, theme, notifications
                                   Needs to be accessible anywhere in the app
```

### Why Redux Toolkit (RTK)?

| Feature | Benefit |
|---|---|
| `createSlice` | Combines actions + reducers in one place — much less boilerplate |
| `createAsyncThunk` | Handles async API calls with loading/error states built-in |
| `RTK Query` | Built-in data fetching + caching (alternative to React Query) |
| DevTools | Powerful Redux DevTools browser extension for debugging |
| Industry Standard | Used in large enterprise projects — great for your resume |

### Redux Store Setup

```ts
// store/index.ts — The central Redux store
import { configureStore } from "@reduxjs/toolkit";
import authReducer from "./slices/authSlice";
import uiReducer from "./slices/uiSlice";

export const store = configureStore({
    reducer: {
        auth: authReducer,    // Auth state (user, token, role)
        ui: uiReducer,        // UI state (sidebar open, theme)
    },
});

export type RootState = ReturnType<typeof store.getState>;
export type AppDispatch = typeof store.dispatch;
```

### Auth Slice Example

```ts
// store/slices/authSlice.ts
import { createSlice, createAsyncThunk, PayloadAction } from "@reduxjs/toolkit";
import { authService } from "../../lib/services/authService";

interface AuthState {
    user: User | null;
    token: string | null;
    role: "ADMIN" | "TEACHER" | "STUDENT" | "PARENT" | null;
    loading: boolean;
    error: string | null;
}

const initialState: AuthState = {
    user: null,
    token: localStorage.getItem("token"),  // Rehydrate on refresh
    role: null,
    loading: false,
    error: null,
};

// Async thunk — handles the API call + loading/error states automatically
export const loginUser = createAsyncThunk(
    "auth/login",
    async (credentials: { email: string; password: string }, { rejectWithValue }) => {
        try {
            const response = await authService.login(credentials);
            localStorage.setItem("token", response.data.token); // Persist token
            return response.data;  // { user, token, role }
        } catch (error: any) {
            return rejectWithValue(error.response?.data?.message || "Login failed");
        }
    }
);

const authSlice = createSlice({
    name: "auth",
    initialState,
    reducers: {
        logout: (state) => {
            state.user = null;
            state.token = null;
            state.role = null;
            localStorage.removeItem("token");
        },
    },
    extraReducers: (builder) => {
        builder
            .addCase(loginUser.pending, (state) => {
                state.loading = true;
                state.error = null;
            })
            .addCase(loginUser.fulfilled, (state, action) => {
                state.loading = false;
                state.user = action.payload.user;
                state.token = action.payload.token;
                state.role = action.payload.role;
            })
            .addCase(loginUser.rejected, (state, action) => {
                state.loading = false;
                state.error = action.payload as string;
            });
    },
});

export const { logout } = authSlice.actions;
export default authSlice.reducer;
```

### Using Redux in Components

```tsx
// In any component — read from store
import { useSelector, useDispatch } from "react-redux";
import { RootState } from "../../store";
import { logout, loginUser } from "../../store/slices/authSlice";

function LoginPage() {
    const dispatch = useDispatch();
    const { loading, error, user } = useSelector((state: RootState) => state.auth);

    const handleLogin = async (data) => {
        dispatch(loginUser(data));  // Triggers async thunk
    };

    return (
        <button onClick={handleLogin} disabled={loading}>
            {loading ? "Logging in..." : "Login"}
        </button>
    );
}

// Wrap entire app with Provider
// app/layout.tsx
import { Provider } from "react-redux";
import { store } from "../store";

export default function RootLayout({ children }) {
    return (
        <html>
            <body>
                <Provider store={store}>{children}</Provider>
            </body>
        </html>
    );
}
```

---

## 13. Form Validation Strategy

> **What it is:** Validate forms on the frontend before sending data to the backend.

```tsx
// React Hook Form + Zod — industry standard combo
import { useForm } from "react-hook-form";
import { zodResolver } from "@hookform/resolvers/zod";
import { z } from "zod";

// 1. Define schema (what valid data looks like)
const createStudentSchema = z.object({
    name: z.string().min(2, "Name must be at least 2 characters"),
    email: z.string().email("Invalid email address"),
    rollNumber: z.string().regex(/^[A-Z]-\d{3}$/, "Format: A-001"),
    classGrade: z.number().min(1).max(12),
    phone: z.string().optional(),
});

type CreateStudentForm = z.infer<typeof createStudentSchema>;

// 2. Use in component
function CreateStudentForm() {
    const { register, handleSubmit, formState: { errors } } = useForm<CreateStudentForm>({
        resolver: zodResolver(createStudentSchema),
    });

    const onSubmit = async (data: CreateStudentForm) => {
        await studentService.create(data);
    };

    return (
        <form onSubmit={handleSubmit(onSubmit)}>
            <input {...register("name")} />
            {errors.name && <p>{errors.name.message}</p>}
            {/* ... */}
        </form>
    );
}
```

---

## 14. Component Architecture (Frontend)

> **What it is:** Organizing React components by responsibility.

```
components/
├── ui/                       ← Pure UI, no business logic (Shadcn/UI base)
│   ├── Button.tsx
│   ├── Table.tsx
│   ├── Modal.tsx
│   └── Badge.tsx
│
├── layout/                   ← App shell components
│   ├── Sidebar.tsx
│   ├── Topbar.tsx
│   └── DashboardLayout.tsx
│
└── shared/                   ← Reusable business-aware components
    ├── StudentCard.tsx        ← Uses student data shape
    ├── FeeStatusBadge.tsx     ← Shows PAID/PENDING/OVERDUE
    ├── AttendanceTable.tsx
    └── SearchAndFilter.tsx
```

### Component Design Rule — Single Responsibility

```tsx
// ❌ BAD — Component does too much
function StudentsPage() {
    // fetches data AND renders table AND handles pagination AND has delete modal
}

// ✅ GOOD — Each component has ONE job
function StudentsPage() {
    return (
        <>
            <StudentsFilter onFilter={...} />
            <StudentsTable data={students} onDelete={...} />
            <Pagination page={page} totalPages={total} onChange={...} />
        </>
    );
}
```

---

## 15. Route Protection & Middleware

> **What it is:** Automatically redirect unauthenticated users to login, and prevent wrong roles from accessing pages.

```ts
// middleware.ts (Next.js — runs on every request at the EDGE)
import { NextResponse } from "next/server";
import type { NextRequest } from "next/server";

export function middleware(request: NextRequest) {
    const token = request.cookies.get("token")?.value;
    const { pathname } = request.nextUrl;

    // Public routes — always accessible
    if (pathname.startsWith("/login")) return NextResponse.next();

    // No token = redirect to login
    if (!token) {
        return NextResponse.redirect(new URL("/login", request.url));
    }

    // Role-based protection
    const userRole = getUserRoleFromToken(token); // decode JWT

    if (pathname.startsWith("/admin") && userRole !== "ADMIN") {
        return NextResponse.redirect(new URL("/unauthorized", request.url));
    }

    if (pathname.startsWith("/teacher") && !["TEACHER", "ADMIN"].includes(userRole)) {
        return NextResponse.redirect(new URL("/unauthorized", request.url));
    }

    return NextResponse.next();
}

export const config = {
    matcher: ["/((?!api|_next|favicon.ico).*)"],  // Apply to all non-api routes
};
```

---

## 16. Database Design Best Practices

> **What it is:** Structuring your **Neon PostgreSQL** tables properly for performance and data integrity.

### Why Neon PostgreSQL?

| Feature | Benefit |
|---|---|
| **Serverless PostgreSQL** | No server to manage — Neon auto-scales |
| **Free Tier** | Generous free tier — perfect for development |
| **Branching** | Create DB branches like Git branches (dev/prod) |
| **Cloud-native** | Works perfectly with Vercel, Railway, Render deployments |
| **PostgreSQL** | More powerful than MySQL — supports JSON, arrays, full-text search |

### Key Principles

| Principle | Rule | Example |
|---|---|---|
| **Primary Keys** | Always use auto-increment `BIGINT` | `id BIGSERIAL PRIMARY KEY` |
| **Timestamps** | Every table has `created_at`, `updated_at` | Use `@CreationTimestamp`, `@UpdateTimestamp` |
| **Soft Delete** | Never hard delete — add `is_deleted` flag | `is_deleted BOOLEAN DEFAULT false` |
| **Normalization** | No duplicate data across tables | Student class → FK to `classes` table |
| **Indexing** | Index columns used in WHERE clauses | `email`, `roll_number`, `class_id` |

### Entity Base Class (Audit Fields)

```java
// Extend this in ALL entities
@MappedSuperclass
public abstract class BaseEntity {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @CreationTimestamp
    @Column(updatable = false)
    private LocalDateTime createdAt;

    @UpdateTimestamp
    private LocalDateTime updatedAt;

    private boolean isDeleted = false;  // Soft delete
}

// Usage
@Entity
@Table(name = "students")
public class Student extends BaseEntity {
    private String name;
    // All audit fields inherited automatically!
}
```

---

## 17. Pagination & Sorting

> **What it is:** Never return ALL records at once — always paginate for performance.

### Backend — Spring Data Pageable

```java
// Repository
Page<Student> findByClassGrade(int grade, Pageable pageable);

// Service
public Page<StudentResponse> getStudents(int page, int size, String sortBy) {
    Pageable pageable = PageRequest.of(page, size, Sort.by(sortBy));
    return studentRepository.findAll(pageable)
                            .map(this::mapToDTO);
}

// Controller
@GetMapping
public ResponseEntity<Page<StudentResponse>> getStudents(
    @RequestParam(defaultValue = "0") int page,
    @RequestParam(defaultValue = "10") int size,
    @RequestParam(defaultValue = "name") String sortBy
) {
    return ResponseEntity.ok(studentService.getStudents(page, size, sortBy));
}
```

### Frontend — Pagination Hook

```ts
// GET /students?page=0&size=10&sortBy=name
const { data, isLoading } = useQuery({
    queryKey: ["students", page, size],
    queryFn: () => studentService.getAll(page, size),
});
```

---

## 18. Logging Best Practices

> **What it is:** Record what your app is doing — critical for debugging production issues.

```java
// Use SLF4J (comes with Spring Boot)
@Service
public class StudentService {
    private static final Logger log = LoggerFactory.getLogger(StudentService.class);
    // Or with Lombok: @Slf4j

    public StudentResponse createStudent(CreateStudentRequest request) {
        log.info("Creating student with roll number: {}", request.getRollNumber());

        try {
            Student saved = studentRepository.save(student);
            log.info("Student created successfully with ID: {}", saved.getId());
            return mapToDTO(saved);
        } catch (Exception e) {
            log.error("Failed to create student: {}", e.getMessage(), e);
            throw new ServiceException("Failed to create student");
        }
    }
}
```

### Log Levels

| Level | When to Use |
|---|---|
| `log.DEBUG` | Detailed info for development debugging |
| `log.INFO` | Important business events (user created, fee paid) |
| `log.WARN` | Something unexpected but not critical |
| `log.ERROR` | Actual errors that need attention |

---

## 19. Security Hardening Checklist

> **Must-do before going live:**

- [ ] **Passwords** — Always hashed with `BCryptPasswordEncoder` (never store plain text!)
- [ ] **JWT Secret** — Min 256-bit key, stored in environment variables
- [ ] **HTTPS** — Never run production on HTTP
- [ ] **SQL Injection** — JPA/JPQL parameterized queries (never string concatenation)
- [ ] **XSS** — Sanitize all user input; use Content Security Policy headers
- [ ] **Rate Limiting** — Max login attempts (prevent brute force)
- [ ] **CORS** — Only allow your specific frontend domains
- [ ] **Sensitive fields** — Never log passwords, tokens, or credit cards
- [ ] **File Uploads** — Validate file type, limit size, store outside webroot
- [ ] **Dependency Scan** — Run `mvn dependency:check` and `npm audit` regularly

---

## 20. Git Branching Strategy

> **What it is:** A disciplined workflow for managing code changes in a team.

### Branch Strategy (Feature Branch Workflow)

```
main            ← Production-ready code ONLY
  └── develop   ← Staging/integration branch
        ├── feature/student-module      ← New feature
        ├── feature/auth-jwt            ← Another feature
        ├── bugfix/fee-calculation      ← Bug fix
        └── hotfix/login-crash          ← Critical prod fix
```

### Commit Message Convention

```bash
# Format: <type>: <short description>

feat: add student CRUD APIs         ← New feature
fix: correct fee calculation logic  ← Bug fix
docs: update API documentation      ← Documentation
refactor: extract student service   ← Code improvement (no feature change)
style: format controller files      ← Formatting
test: add student service unit test ← Tests
chore: update Spring Boot to 3.2.5  ← Maintenance
```

### Git Workflow

```bash
# 1. Always start from latest develop
git checkout develop
git pull origin develop

# 2. Create feature branch
git checkout -b feature/student-module

# 3. Do your work and commit
git add .
git commit -m "feat: add create student API"

# 4. Push feature branch
git push origin feature/student-module

# 5. Create Pull Request (GitHub) → reviewed → merged into develop

# 6. When develop is stable → merge to main (Production Deploy)
```

---

## 🏆 Summary — Architecture at a Glance

```
┌─────────────────────────────────────────────────────────────┐
│                    CLIENT (Browser)                          │
│  Next.js App Router + Tailwind + Shadcn/UI + Redux Toolkit  │
│  Route Protection via Middleware + React Hook Form + Zod    │
│  Axios Instance with Interceptors                           │
└──────────────────────────┬──────────────────────────────────┘
                           │ HTTPS + JWT Bearer Token
                           ▼
┌─────────────────────────────────────────────────────────────┐
│                SPRING BOOT BACKEND                           │
│  JWT Filter → Security → Controller → Service → Repository  │
│  DTOs (Request/Response) | Global Exception Handler         │
│  Pagination | Role-Based Access | Logging                   │
└──────────────────────────┬──────────────────────────────────┘
                           │ JPA / Hibernate
                           ▼
┌─────────────────────────────────────────────────────────────┐
│          Neon PostgreSQL Database (Cloud)                   │
│  Normalized Tables | Soft Deletes | Indexed Columns         │
│  Base Entity Audit Fields (created_at, updated_at)          │
└─────────────────────────────────────────────────────────────┘
```

> ✅ Following all 20 practices = **Enterprise-Grade, Production-Ready** School Management System!
