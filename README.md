# 🗂️ Taskora — Project Management Platform

A full-stack SaaS project management platform built with **Next.js 15**, **Supabase**, and **Clerk** — featuring Kanban boards with drag-and-drop, real-time task management, sprint analytics, and subscription-based access control.

🔗 **Live Demo:** [mbilalhussain-taskora-pmp.vercel.app](https://mbilalhussain-taskora-pmp.vercel.app)

---

## ✨ Features

- 🗃️ **Kanban Boards** — create and manage boards with customizable colors and titles
- 🖱️ **Drag & Drop** — intuitive task management powered by `@dnd-kit`
- 📋 **Column Management** — create, edit, and delete workflow columns
- ✅ **Task Management** — add tasks with titles, descriptions, assignees, priorities, and due dates
- 📊 **Analytics Dashboard** — board statistics, task timeline, and activity overview
- 🔍 **Advanced Filtering** — filter tasks by priority, assignee, and due dates
- 🔐 **Authentication** — secure sign-in/sign-up via **Clerk**
- 🛡️ **Row Level Security** — database-level data isolation via Supabase RLS
- 💳 **Subscription Plans** — free and premium tiers with upgrade flow
- 🌙 **Dark / Light Mode** — theme switching
- 📱 **Responsive Design** — works across desktop and mobile

---

## 🏗️ Architecture

```
Taskora/
├── app/                    # Next.js App Router pages
│   ├── /                   # Landing page
│   ├── /dashboard          # Board overview + stats
│   ├── /boards/[id]        # Kanban board view
│   ├── /pricing            # Subscription plans
│   ├── /sign-in            # Clerk auth
│   └── /sign-up            # Clerk auth
├── features/               # Feature-based modules
│   ├── auth/               # Login & signup components
│   └── dashboard/          # Dashboard & board components
├── components/             # Shared UI components
├── lib/
│   ├── services.ts         # Supabase data layer (boards, columns, tasks)
│   └── supabase/           # Supabase client & models
└── providers/
    └── SupabaseProvider.tsx  # Clerk-authenticated Supabase client
```

### Auth + Database Flow

```
User (Clerk Auth)
        ↓
  Clerk JWT Token
        ↓
  SupabaseProvider
  (accessToken: Clerk JWT)
        ↓
  Supabase PostgreSQL
  (Row Level Security)
```

Clerk JWTs are passed directly to Supabase via the `accessToken` option — enabling row-level security policies to enforce per-user data isolation without a separate backend.

---

## 🛠️ Tech Stack

### Frontend
![Next.js](https://img.shields.io/badge/Next.js_15-000000?style=for-the-badge&logo=nextdotjs&logoColor=white)
![TypeScript](https://img.shields.io/badge/TypeScript-007ACC?style=for-the-badge&logo=typescript&logoColor=white)
![TailwindCSS](https://img.shields.io/badge/Tailwind_CSS-38B2AC?style=for-the-badge&logo=tailwind-css&logoColor=white)

| Library | Purpose |
|---------|---------|
| Next.js 15 + TypeScript | Full-stack React framework with App Router |
| **@dnd-kit** | Drag-and-drop Kanban board interactions |
| Shadcn UI + Radix UI | Accessible, headless UI components |
| Tailwind CSS | Utility-first styling |
| Lucide React | Icon library |

### Backend & Infrastructure
![Supabase](https://img.shields.io/badge/Supabase-3ECF8E?style=for-the-badge&logo=supabase&logoColor=white)
![Clerk](https://img.shields.io/badge/Clerk-6C47FF?style=for-the-badge&logo=clerk&logoColor=white)
![Vercel](https://img.shields.io/badge/Vercel-000000?style=for-the-badge&logo=vercel&logoColor=white)

| Technology | Purpose |
|------------|---------|
| **Supabase** | PostgreSQL database + Row Level Security |
| **Clerk** | Authentication, user management & JWT |
| Vercel | Deployment & hosting |

---

## 🗄️ Database Schema

```sql
boards (id, title, description, color, user_id, created_at, updated_at)
columns (id, board_id, title, sort_order, user_id, created_at)
tasks (id, column_id, title, description, assignee, due_date, priority, sort_order, created_at, updated_at)
```

---

## 🚀 Getting Started

### Prerequisites

- Node.js 18+
- [Supabase](https://supabase.com) account
- [Clerk](https://clerk.com) account

### Installation

```bash
# Clone the repository
git clone https://github.com/mbilalhussain15/Taskora-Project-Management-Platform.git
cd Taskora-Project-Management-Platform

# Install dependencies
npm install
```

### Environment Variables

Create a `.env.local` file in the root:

```env
NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY=your_clerk_publishable_key
CLERK_SECRET_KEY=your_clerk_secret_key
NEXT_PUBLIC_SUPABASE_URL=your_supabase_url
NEXT_PUBLIC_SUPABASE_ANON_KEY=your_supabase_anon_key
```

### Supabase Setup

Run the following SQL in your Supabase SQL Editor:

```sql
CREATE TABLE boards (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  title TEXT NOT NULL,
  description TEXT,
  color TEXT NOT NULL DEFAULT '#3B82F6',
  user_id TEXT NOT NULL,
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW()
);

CREATE TABLE columns (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  board_id UUID REFERENCES boards(id) ON DELETE CASCADE,
  title TEXT NOT NULL,
  sort_order INTEGER NOT NULL DEFAULT 0,
  user_id TEXT NOT NULL,
  created_at TIMESTAMPTZ DEFAULT NOW()
);

CREATE TABLE tasks (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  column_id UUID REFERENCES columns(id) ON DELETE CASCADE,
  title TEXT NOT NULL,
  description TEXT,
  assignee TEXT,
  due_date DATE,
  priority TEXT CHECK (priority IN ('low', 'medium', 'high')) DEFAULT 'medium',
  sort_order INTEGER NOT NULL DEFAULT 0,
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW()
);
```

### Clerk JWT Template

In Clerk Dashboard → Configure → JWT Templates → New Template → select **Supabase** → paste your Supabase JWT secret as the signing key.

### Run Locally

```bash
npm run dev
```

Open [http://localhost:3000](http://localhost:3000)

---

## 📄 License

MIT © [Bilal Hussain](https://github.com/mbilalhussain15)
