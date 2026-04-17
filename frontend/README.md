# School Management — Frontend (Next.js)

## Tech Stack
- Next.js 14 (App Router)
- Tailwind CSS
- Shadcn/UI
- Axios
- React Hook Form + Zod
- Zustand (State Management)
- NextAuth.js (Authentication)

## Project Structure

```
frontend/
├── app/
│   ├── layout.tsx               ← Root layout
│   ├── page.tsx                 ← Home / Login redirect
│   ├── (auth)/
│   │   └── login/page.tsx       ← Login page
│   ├── (dashboard)/
│   │   ├── layout.tsx           ← Dashboard layout (sidebar + navbar)
│   │   ├── admin/               ← Admin pages
│   │   │   ├── page.tsx         ← Admin dashboard
│   │   │   └── students/page.tsx
│   │   ├── teacher/             ← Teacher pages
│   │   └── student/             ← Student pages
├── components/
│   ├── ui/                      ← Shadcn components
│   ├── layout/                  ← Sidebar, Navbar, Header
│   └── shared/                  ← Reusable components
├── lib/
│   ├── api.ts                   ← Axios instance
│   └── utils.ts                 ← Utility functions
├── store/                       ← Zustand stores
├── types/                       ← TypeScript types
└── middleware.ts                 ← Route protection
```

## How to Run
```bash
npm install
npm run dev
```
Runs on: http://localhost:3000
