# Workshop Step-by-Step: "TaskFlow" — ระบบจัดการงานของทีม

**React 19 + TypeScript + Vite + Tailwind CSS v4 + shadcn/ui**
ประกอบหลักสูตร Basic to Intermediate React.js (อัปเดต 2026) — 5 วัน (30 ชั่วโมง)
วิทยากร: อาจารย์สามิตร โกยม | บริษัท ไอทีจีเนียส เอ็นจิเนียริ่ง จำกัด

> **มาตรฐานโค้ดของ Workshop นี้**
> - TypeScript ทั้งหมด **ไม่ใส่ semicolon**
> - คอมเมนต์ในโค้ดเป็นภาษาไทย
> - UI Component ใช้ **shadcn/ui** เป็นหลัก
> - Package Manager ใช้ **pnpm**

---

## สารบัญ

| วัน | เนื้อหา | ผลลัพธ์ปลายวัน |
| --- | --- | --- |
| 1 | Setup Vite + TS + Tailwind v4 + shadcn/ui, Types, Component แรก | แอปรันได้ แสดง TaskCard |
| 2 | Props, State, Event, CRUD บน Local State, Filter/Search | Task Manager ใช้งานได้จริง |
| 3 | Hooks เชิงลึก, Theme (Dark Mode), Custom Hooks, React 19 Actions | จำข้อมูลได้ + Optimistic UI |
| 4 | React Router v7, RHF + Zod, Zustand, Redux Toolkit | Multi-page + Login + Protected Routes |
| 5 | Prisma 7 + Neon, TanStack Query, JWT, Performance, Deploy | ขึ้น Production จริงบน Vercel |

---
---

# 🟦 วันที่ 1 — วางรากฐาน: Vite + React 19 + TypeScript + shadcn/ui

## Step 1.1 — สร้างโปรเจกต์ด้วย Vite

```bash
# สร้างโปรเจกต์ใหม่ด้วย template react + typescript
pnpm create vite taskflow --template react-ts

cd taskflow
pnpm install
pnpm dev
```

เปิดเบราว์เซอร์ที่ `http://localhost:5173` — ทดลองแก้ข้อความใน `src/App.tsx` แล้วสังเกต **HMR** (หน้าจออัปเดตทันทีโดยไม่รีเฟรช)

**สำรวจโครงสร้างโปรเจกต์:**

```
taskflow/
├── public/              # ไฟล์ static
├── src/
│   ├── App.tsx          # Component หลัก
│   ├── main.tsx         # จุดเริ่มต้นของแอป (entry point)
│   └── index.css        # ไฟล์ CSS หลัก
├── index.html           # HTML template (Vite ใช้ไฟล์นี้เป็น entry)
├── tsconfig.json        # การตั้งค่า TypeScript
├── vite.config.ts       # การตั้งค่า Vite
└── package.json
```

## Step 1.2 — ติดตั้ง Tailwind CSS v4

Tailwind v4 ติดตั้งง่ายกว่าเดิมมาก — ไม่ต้องมี `tailwind.config.js` อีกต่อไป

```bash
pnpm add tailwindcss @tailwindcss/vite
```

แก้ไข `vite.config.ts`:

```ts
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'
import tailwindcss from '@tailwindcss/vite'

export default defineConfig({
  plugins: [react(), tailwindcss()],
})
```

ลบเนื้อหาเดิมใน `src/index.css` ทั้งหมด แล้วใส่บรรทัดเดียว:

```css
@import "tailwindcss";
```

ทดสอบใน `App.tsx`:

```tsx
function App() {
  return (
    <h1 className="text-3xl font-bold text-blue-600 p-8">
      สวัสดี TaskFlow 🚀
    </h1>
  )
}

export default App
```

## Step 1.3 — ตั้งค่า Path Alias (@/) สำหรับ shadcn/ui

shadcn/ui ต้องการ import แบบ `@/components/...` จึงต้องตั้งค่า alias ก่อน

**แก้ไข `tsconfig.json`** (เพิ่ม `compilerOptions`):

```json
{
  "files": [],
  "references": [
    { "path": "./tsconfig.app.json" },
    { "path": "./tsconfig.node.json" }
  ],
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@/*": ["./src/*"]
    }
  }
}
```

**แก้ไข `tsconfig.app.json`** (เพิ่มใน `compilerOptions` ที่มีอยู่แล้ว):

```json
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@/*": ["./src/*"]
    }
  }
}
```

**ติดตั้ง type ของ Node แล้วแก้ `vite.config.ts`:**

```bash
pnpm add -D @types/node
```

```ts
import path from 'node:path'
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'
import tailwindcss from '@tailwindcss/vite'

export default defineConfig({
  plugins: [react(), tailwindcss()],
  resolve: {
    alias: {
      // ทำให้ import '@/...' ชี้ไปที่โฟลเดอร์ src
      '@': path.resolve(__dirname, './src'),
    },
  },
})
```

## Step 1.4 — ติดตั้ง shadcn/ui

```bash
pnpm dlx shadcn@latest init
```

ตอบคำถามตอน init:
- **Base color** → เลือก `Neutral` (หรือสีที่ชอบ)

จากนั้นติดตั้ง Component ชุดแรกที่จะใช้ในวันนี้และวันที่ 2:

```bash
pnpm dlx shadcn@latest add button card badge input label
```

shadcn จะสร้างไฟล์ไว้ที่ `src/components/ui/` — **จุดเด่นของ shadcn คือเราเป็นเจ้าของโค้ด** แก้ไขได้ตามใจ ไม่ใช่ library กล่องดำ

ทดสอบว่าใช้งานได้:

```tsx
import { Button } from '@/components/ui/button'

function App() {
  return (
    <div className="p-8">
      <Button>ปุ่มแรกจาก shadcn/ui 🎉</Button>
    </div>
  )
}

export default App
```

## Step 1.5 — TypeScript สำหรับ TaskFlow: กำหนด Type ของโดเมน

สร้างไฟล์ `src/types/task.ts` — Type เหล่านี้จะใช้ตลอดทั้ง 5 วัน:

```ts
// ============================================
// ชนิดข้อมูลหลักของระบบ TaskFlow
// ============================================

// Literal Union Type — จำกัดค่าที่เป็นไปได้ ป้องกันพิมพ์ผิด
export type TaskStatus = 'todo' | 'doing' | 'done'
export type TaskPriority = 'low' | 'medium' | 'high'

// interface สำหรับ Object ที่มีโครงสร้างชัดเจน
export interface Task {
  id: string
  title: string
  description?: string        // ? คือ optional ไม่ใส่ก็ได้
  status: TaskStatus
  priority: TaskPriority
  dueDate?: string            // เก็บเป็น ISO string เช่น '2026-06-15'
  createdAt: string
}

// ตัวช่วยแปลงค่า priority เป็นข้อความภาษาไทย
export const priorityLabel: Record<TaskPriority, string> = {
  low: 'ต่ำ',
  medium: 'ปานกลาง',
  high: 'สูง',
}

export const statusLabel: Record<TaskStatus, string> = {
  todo: 'รอดำเนินการ',
  doing: 'กำลังทำ',
  done: 'เสร็จแล้ว',
}
```

**แบบฝึกแทรก (15 นาที):** ให้ผู้เรียนทดลองใน `playground.ts`

```ts
// 1. type vs interface — ทั้งคู่ใช้กำหนดโครงสร้าง Object ได้
type UserA = { name: string }
interface UserB { name: string }

// 2. Union Type
let score: number | string = 100
score = 'A+'   // ✅ ได้ เพราะรองรับทั้งสอง type

// 3. Generics เบื้องต้น — ฟังก์ชันที่ทำงานกับ type อะไรก็ได้
function getById<T extends { id: string }>(items: T[], id: string): T | undefined {
  return items.find((item) => item.id === id)
}

// 4. Type Inference — TS เดา type ให้เองโดยไม่ต้องประกาศ
const count = 10        // TS รู้เองว่าเป็น number
```

## Step 1.6 — Component แรก: TaskCard (Static)

สร้าง `src/components/TaskCard.tsx` — วันนี้ยัง hard-code ข้อมูลก่อน พรุ่งนี้ค่อยรับผ่าน Props:

```tsx
import { Card, CardContent, CardHeader, CardTitle } from '@/components/ui/card'
import { Badge } from '@/components/ui/badge'

function TaskCard() {
  // วันนี้ใช้ข้อมูล hard-code ไปก่อน (พรุ่งนี้จะเปลี่ยนเป็น Props)
  const title = 'ออกแบบหน้า Dashboard'
  const priority = 'high'
  const dueDate = '2026-06-15'

  // JSX Expression — คำนวณจำนวนวันก่อนถึงกำหนดส่ง
  const daysLeft = Math.ceil(
    (new Date(dueDate).getTime() - Date.now()) / (1000 * 60 * 60 * 24)
  )

  return (
    <Card className="w-full max-w-sm">
      <CardHeader>
        <CardTitle className="flex items-center justify-between">
          {title}
          <Badge variant="destructive">{priority}</Badge>
        </CardTitle>
      </CardHeader>
      <CardContent>
        <p className="text-sm text-muted-foreground">
          กำหนดส่ง: {dueDate} (เหลืออีก {daysLeft} วัน)
        </p>
      </CardContent>
    </Card>
  )
}

export default TaskCard
```

## Step 1.7 — ประกอบหน้าแรกด้วย Header + Fragment

สร้าง `src/components/Header.tsx`:

```tsx
function Header() {
  return (
    <header className="border-b bg-background px-6 py-4">
      <h1 className="text-xl font-bold">📋 TaskFlow</h1>
      <p className="text-sm text-muted-foreground">ระบบจัดการงานของทีม</p>
    </header>
  )
}

export default Header
```

แก้ `src/App.tsx` ให้ประกอบทุกอย่างเข้าด้วยกัน:

```tsx
import Header from '@/components/Header'
import TaskCard from '@/components/TaskCard'

function App() {
  return (
    // <></> คือ Fragment — จัดกลุ่ม element โดยไม่สร้าง div เกินจำเป็น
    <>
      <Header />
      <main className="p-6">
        <TaskCard />
      </main>
    </>
  )
}

export default App
```

## ✅ Checkpoint วันที่ 1

- [ ] `pnpm dev` รันได้ และ HMR ทำงาน
- [ ] Tailwind class แสดงผล (ตัวอักษรมีสี/ขนาดตามที่กำหนด)
- [ ] `pnpm dlx shadcn@latest add ...` แล้วมีโฟลเดอร์ `src/components/ui/`
- [ ] import แบบ `@/components/...` ใช้งานได้ไม่มี error
- [ ] หน้าแรกแสดง Header + TaskCard (จาก shadcn Card) อย่างน้อย 1 ใบ

---
---

# 🟦 วันที่ 2 — Props, State, Event และ CRUD บน Local State

## Step 2.1 — ปรับ TaskCard ให้รับข้อมูลผ่าน Props

แก้ `src/components/TaskCard.tsx` ทั้งไฟล์ — รับ `task` และ Callback จากแม่:

```tsx
import { Card, CardContent, CardHeader, CardTitle } from '@/components/ui/card'
import { Badge } from '@/components/ui/badge'
import { Button } from '@/components/ui/button'
import type { Task, TaskPriority } from '@/types/task'
import { priorityLabel, statusLabel } from '@/types/task'

// กำหนดโครงสร้าง Props ด้วย interface
interface TaskCardProps {
  task: Task
  onToggle: (id: string) => void     // ส่งฟังก์ชัน (Callback) ลงมาจากแม่
  onDelete: (id: string) => void
}

// เลือก variant ของ Badge ตามระดับความสำคัญ
const priorityVariant: Record<TaskPriority, 'secondary' | 'default' | 'destructive'> = {
  low: 'secondary',
  medium: 'default',
  high: 'destructive',
}

function TaskCard({ task, onToggle, onDelete }: TaskCardProps) {
  const isDone = task.status === 'done'

  return (
    <Card className={isDone ? 'opacity-60' : ''}>
      <CardHeader>
        <CardTitle className="flex items-center justify-between gap-2">
          {/* Conditional Rendering — ขีดฆ่าเมื่องานเสร็จ */}
          <span className={isDone ? 'line-through' : ''}>{task.title}</span>
          <Badge variant={priorityVariant[task.priority]}>
            {priorityLabel[task.priority]}
          </Badge>
        </CardTitle>
      </CardHeader>
      <CardContent className="space-y-3">
        {/* && — แสดงเฉพาะเมื่อมีค่า */}
        {task.description && (
          <p className="text-sm text-muted-foreground">{task.description}</p>
        )}
        <div className="flex items-center justify-between">
          <Badge variant="outline">{statusLabel[task.status]}</Badge>
          <div className="flex gap-2">
            <Button size="sm" variant="outline" onClick={() => onToggle(task.id)}>
              {isDone ? 'ทำใหม่' : 'เสร็จแล้ว'}
            </Button>
            <Button size="sm" variant="destructive" onClick={() => onDelete(task.id)}>
              ลบ
            </Button>
          </div>
        </div>
      </CardContent>
    </Card>
  )
}

export default TaskCard
```

## Step 2.2 — TaskList: แสดงรายการด้วย map() + key

สร้าง `src/components/TaskList.tsx`:

```tsx
import TaskCard from '@/components/TaskCard'
import type { Task } from '@/types/task'

interface TaskListProps {
  tasks: Task[]
  onToggle: (id: string) => void
  onDelete: (id: string) => void
}

function TaskList({ tasks, onToggle, onDelete }: TaskListProps) {
  // Empty State — แสดงเมื่อไม่มีงาน
  if (tasks.length === 0) {
    return (
      <div className="rounded-lg border border-dashed p-10 text-center text-muted-foreground">
        🎉 ยังไม่มีงานในรายการนี้
      </div>
    )
  }

  return (
    // Responsive Grid: มือถือ 1 คอลัมน์ → แท็บเล็ต 2 → จอใหญ่ 3
    <div className="grid grid-cols-1 gap-4 md:grid-cols-2 lg:grid-cols-3">
      {tasks.map((task) => (
        // key ต้องเป็นค่าที่ไม่ซ้ำและคงที่ — ห้ามใช้ index!
        <TaskCard key={task.id} task={task} onToggle={onToggle} onDelete={onDelete} />
      ))}
    </div>
  )
}

export default TaskList
```

> ⚠️ **จุดสอนสำคัญ:** สาธิตว่าถ้าใช้ `key={index}` แล้วลบรายการตรงกลาง UI จะแสดงผลเพี้ยนอย่างไร

## Step 2.3 — useState + เพิ่มงาน: ยก State ขึ้น App

แก้ `src/App.tsx` — State ทั้งหมดอยู่ที่แม่ (Lifting State Up):

```tsx
import { useState, type ChangeEvent } from 'react'
import Header from '@/components/Header'
import TaskList from '@/components/TaskList'
import { Input } from '@/components/ui/input'
import { Button } from '@/components/ui/button'
import type { Task } from '@/types/task'

// ข้อมูลตั้งต้นสำหรับทดสอบ
const initialTasks: Task[] = [
  {
    id: '1',
    title: 'ออกแบบหน้า Dashboard',
    description: 'ทำ wireframe และเลือกชุดสี',
    status: 'doing',
    priority: 'high',
    dueDate: '2026-06-15',
    createdAt: new Date().toISOString(),
  },
  {
    id: '2',
    title: 'เขียนเอกสาร API',
    status: 'todo',
    priority: 'medium',
    createdAt: new Date().toISOString(),
  },
]

function App() {
  // State หลักของแอป — อยู่ที่ Component บนสุด
  const [tasks, setTasks] = useState<Task[]>(initialTasks)
  const [newTitle, setNewTitle] = useState('')

  // กำหนด type ให้ event ของ input
  const handleTitleChange = (e: ChangeEvent<HTMLInputElement>) => {
    setNewTitle(e.target.value)
  }

  const addTask = () => {
    if (!newTitle.trim()) return

    const newTask: Task = {
      id: crypto.randomUUID(),
      title: newTitle.trim(),
      status: 'todo',
      priority: 'medium',
      createdAt: new Date().toISOString(),
    }

    // ✅ Immutable update — สร้าง array ใหม่ ห้าม push ใส่ของเดิม
    setTasks((prev) => [newTask, ...prev])
    setNewTitle('')
  }

  const toggleTask = (id: string) => {
    // ✅ map สร้าง array ใหม่ + spread สร้าง object ใหม่
    setTasks((prev) =>
      prev.map((task) =>
        task.id === id
          ? { ...task, status: task.status === 'done' ? 'todo' : 'done' }
          : task
      )
    )
  }

  const deleteTask = (id: string) => {
    // ✅ filter สร้าง array ใหม่ที่ไม่มีตัวที่ถูกลบ
    setTasks((prev) => prev.filter((task) => task.id !== id))
  }

  return (
    <>
      <Header />
      <main className="mx-auto max-w-6xl space-y-6 p-6">
        {/* ฟอร์มเพิ่มงานอย่างง่าย (วันที่ 3 จะอัปเกรดเป็น React 19 Action) */}
        <div className="flex gap-2">
          <Input
            placeholder="พิมพ์ชื่องานใหม่..."
            value={newTitle}
            onChange={handleTitleChange}
            onKeyDown={(e) => e.key === 'Enter' && addTask()}
          />
          <Button onClick={addTask}>เพิ่มงาน</Button>
        </div>

        <TaskList tasks={tasks} onToggle={toggleTask} onDelete={deleteTask} />
      </main>
    </>
  )
}

export default App
```

> 🧪 **แบบฝึกจับผิด (Debug Challenge):** แจกโค้ดเวอร์ชันที่เขียน `tasks.push(newTask)` แล้ว `setTasks(tasks)` — ให้ผู้เรียนอธิบายว่าทำไม UI ไม่อัปเดต (คำตอบ: reference เดิม React จึงไม่ re-render)

## Step 2.4 — Filter / Search / Sort ด้วย shadcn Tabs + Select

ติดตั้ง component เพิ่ม:

```bash
pnpm dlx shadcn@latest add tabs select
```

เพิ่ม State และ Logic ใน `App.tsx` (วางต่อจาก state เดิม):

```tsx
import { Tabs, TabsList, TabsTrigger } from '@/components/ui/tabs'
import {
  Select, SelectContent, SelectItem, SelectTrigger, SelectValue,
} from '@/components/ui/select'
import type { TaskStatus } from '@/types/task'

// --- เพิ่ม state สำหรับกรอง ค้นหา และเรียงลำดับ ---
const [filter, setFilter] = useState<TaskStatus | 'all'>('all')
const [search, setSearch] = useState('')
const [sortBy, setSortBy] = useState<'createdAt' | 'priority'>('createdAt')

// ลำดับความสำคัญสำหรับใช้เรียง
const priorityOrder = { high: 0, medium: 1, low: 2 }

// ประมวลผลข้อมูลก่อนแสดง: กรอง → ค้นหา → เรียง
const visibleTasks = tasks
  .filter((task) => filter === 'all' || task.status === filter)
  .filter((task) => task.title.toLowerCase().includes(search.toLowerCase()))
  .toSorted((a, b) =>
    sortBy === 'priority'
      ? priorityOrder[a.priority] - priorityOrder[b.priority]
      : b.createdAt.localeCompare(a.createdAt)
  )
```

ส่วน JSX (วางเหนือ `<TaskList />`):

```tsx
<div className="flex flex-col gap-3 sm:flex-row sm:items-center sm:justify-between">
  {/* Tabs กรองตามสถานะ */}
  <Tabs value={filter} onValueChange={(v) => setFilter(v as TaskStatus | 'all')}>
    <TabsList>
      <TabsTrigger value="all">ทั้งหมด</TabsTrigger>
      <TabsTrigger value="todo">รอดำเนินการ</TabsTrigger>
      <TabsTrigger value="doing">กำลังทำ</TabsTrigger>
      <TabsTrigger value="done">เสร็จแล้ว</TabsTrigger>
    </TabsList>
  </Tabs>

  <div className="flex gap-2">
    {/* ช่องค้นหา */}
    <Input
      placeholder="ค้นหางาน..."
      value={search}
      onChange={(e) => setSearch(e.target.value)}
      className="w-44"
    />
    {/* Select เรียงลำดับ */}
    <Select value={sortBy} onValueChange={(v) => setSortBy(v as typeof sortBy)}>
      <SelectTrigger className="w-36">
        <SelectValue />
      </SelectTrigger>
      <SelectContent>
        <SelectItem value="createdAt">ล่าสุดก่อน</SelectItem>
        <SelectItem value="priority">ความสำคัญ</SelectItem>
      </SelectContent>
    </Select>
  </div>
</div>

<TaskList tasks={visibleTasks} onToggle={toggleTask} onDelete={deleteTask} />
```

## Step 2.5 — Composition: สร้าง StatCard ที่ใช้ children

สร้าง `src/components/StatCard.tsx` เพื่อสอนแนวคิด `children`:

```tsx
import { Card, CardContent } from '@/components/ui/card'
import type { ReactNode } from 'react'

interface StatCardProps {
  label: string
  children: ReactNode    // รับ JSX อะไรก็ได้มาแสดงข้างใน
}

function StatCard({ label, children }: StatCardProps) {
  return (
    <Card>
      <CardContent className="pt-6">
        <p className="text-sm text-muted-foreground">{label}</p>
        <div className="text-3xl font-bold">{children}</div>
      </CardContent>
    </Card>
  )
}

export default StatCard
```

ใช้งานใน `App.tsx`:

```tsx
<div className="grid grid-cols-3 gap-4">
  <StatCard label="งานทั้งหมด">{tasks.length}</StatCard>
  <StatCard label="กำลังทำ">
    {tasks.filter((t) => t.status === 'doing').length}
  </StatCard>
  <StatCard label="เสร็จแล้ว">
    <span className="text-green-600">
      {tasks.filter((t) => t.status === 'done').length}
    </span>
  </StatCard>
</div>
```

## ✅ Checkpoint วันที่ 2

- [ ] เพิ่ม / ติ๊กเสร็จ / ลบงานได้ และโค้ดเป็น Immutable ทั้งหมด
- [ ] Tabs กรองสถานะ + ค้นหา + เรียงลำดับ ทำงานร่วมกันได้
- [ ] Empty State แสดงเมื่อไม่มีงานตรงเงื่อนไข
- [ ] Grid Responsive (ลองย่อหน้าจอ) และไม่มี key warning ใน console
- [ ] StatCard แสดงสถิติถูกต้องโดยใช้ children

---
---

# 🟦 วันที่ 3 — Hooks เชิงลึก, Dark Mode, Custom Hooks และ React 19

## Step 3.1 — useEffect: บันทึกข้อมูลลง localStorage

เพิ่มใน `App.tsx` (เวอร์ชันแรก — เดี๋ยว Step 3.4 จะ refactor เป็น Custom Hook):

```tsx
import { useEffect } from 'react'

const STORAGE_KEY = 'taskflow:tasks'

// โหลดข้อมูลจาก localStorage ตอนสร้าง state ครั้งแรก
// ใช้ lazy initializer (ส่งฟังก์ชัน) เพื่อให้อ่าน localStorage แค่ครั้งเดียว
const [tasks, setTasks] = useState<Task[]>(() => {
  const saved = localStorage.getItem(STORAGE_KEY)
  return saved ? JSON.parse(saved) : initialTasks
})

// บันทึกทุกครั้งที่ tasks เปลี่ยน — สังเกต Dependency Array [tasks]
useEffect(() => {
  localStorage.setItem(STORAGE_KEY, JSON.stringify(tasks))
}, [tasks])
```

**สาธิต Cleanup Function** — สร้าง `src/components/Clock.tsx` นาฬิกาบน Header:

```tsx
import { useState, useEffect } from 'react'

function Clock() {
  const [now, setNow] = useState(new Date())

  useEffect(() => {
    // Side Effect: ตั้ง interval อัปเดตเวลาทุกวินาที
    const timer = setInterval(() => setNow(new Date()), 1000)

    // Cleanup: ต้องเคลียร์ interval เมื่อ component ถูกถอด
    // ไม่งั้นจะเกิด memory leak
    return () => clearInterval(timer)
  }, [])   // [] = ทำงานครั้งเดียวตอน mount

  return (
    <span className="text-sm tabular-nums text-muted-foreground">
      {now.toLocaleTimeString('th-TH')}
    </span>
  )
}

export default Clock
```

> 🧪 **แบบฝึกจับผิด — Infinite Loop:** แจกโค้ดนี้ให้ผู้เรียนวิเคราะห์
>
> ```tsx
> // ❌ โค้ดนี้ loop ไม่รู้จบ เพราะอะไร?
> useEffect(() => {
>   setTasks([...tasks])
> }, [tasks])
> // คำตอบ: effect เปลี่ยน tasks → dependency เปลี่ยน → effect รันใหม่ → วนไปเรื่อย ๆ
> ```

## Step 3.2 — useRef / useMemo / useCallback

**useRef — โฟกัส input อัตโนมัติหลังเพิ่มงาน:**

```tsx
import { useRef } from 'react'

const inputRef = useRef<HTMLInputElement>(null)

const addTask = () => {
  if (!newTitle.trim()) return
  // ...โค้ดเพิ่มงานเดิม...
  setNewTitle('')
  inputRef.current?.focus()   // กลับมาโฟกัสที่ช่องกรอกทันที
}

// ใน JSX — React 19 ส่ง ref เป็น prop ปกติได้เลย ไม่ต้องใช้ forwardRef
<Input ref={inputRef} placeholder="พิมพ์ชื่องานใหม่..." ... />
```

**useMemo — คำนวณสถิติเฉพาะเมื่อ tasks เปลี่ยน:**

```tsx
import { useMemo } from 'react'

// ถ้าไม่ใช้ useMemo การคำนวณนี้จะรันใหม่ทุก re-render
// แม้ tasks ไม่เปลี่ยน (เช่น พิมพ์ในช่อง search)
const stats = useMemo(() => {
  console.log('🔄 คำนวณสถิติใหม่')   // เปิด console ดูว่ารันเมื่อไร
  return {
    total: tasks.length,
    doing: tasks.filter((t) => t.status === 'doing').length,
    done: tasks.filter((t) => t.status === 'done').length,
    overdue: tasks.filter(
      (t) => t.dueDate && t.status !== 'done' && new Date(t.dueDate) < new Date()
    ).length,
  }
}, [tasks])
```

**useCallback — คงตัวตนของฟังก์ชันที่ส่งลง List:**

```tsx
import { useCallback } from 'react'

// ถ้าไม่ใช้ useCallback ฟังก์ชันจะถูกสร้างใหม่ทุก render
// ทำให้ React.memo ที่ครอบ TaskCard ไม่มีผล (วันที่ 5 จะวัดผลจริง)
const toggleTask = useCallback((id: string) => {
  setTasks((prev) =>
    prev.map((task) =>
      task.id === id
        ? { ...task, status: task.status === 'done' ? 'todo' : 'done' }
        : task
    )
  )
}, [])
```

> 💡 **อภิปราย:** React Compiler ใน React 19 ทำ memoization ให้อัตโนมัติในหลายกรณี — สอนให้รู้หลักการ แต่ในอนาคตจะเขียน `useMemo`/`useCallback` เองน้อยลง

## Step 3.3 — Dark Mode ด้วย Context API (ตามมาตรฐาน shadcn)

shadcn ใช้ class `dark` บน `<html>` — เราจะสร้าง ThemeProvider เองเพื่อเรียนรู้ Context

สร้าง `src/contexts/ThemeContext.tsx`:

```tsx
import { createContext, useContext, useEffect, useState, type ReactNode } from 'react'

type Theme = 'light' | 'dark'

// กำหนดโครงสร้างของค่าที่ Context จะส่งให้
interface ThemeContextValue {
  theme: Theme
  toggleTheme: () => void
}

// ค่าเริ่มต้นเป็น null แล้วเช็คใน hook เพื่อบังคับให้ใช้ภายใต้ Provider เท่านั้น
const ThemeContext = createContext<ThemeContextValue | null>(null)

export function ThemeProvider({ children }: { children: ReactNode }) {
  const [theme, setTheme] = useState<Theme>(
    () => (localStorage.getItem('taskflow:theme') as Theme) ?? 'light'
  )

  useEffect(() => {
    // เพิ่ม/ลบ class 'dark' ที่ <html> ตามมาตรฐาน shadcn + Tailwind
    document.documentElement.classList.toggle('dark', theme === 'dark')
    localStorage.setItem('taskflow:theme', theme)
  }, [theme])

  const toggleTheme = () => setTheme((prev) => (prev === 'light' ? 'dark' : 'light'))

  return (
    <ThemeContext.Provider value={{ theme, toggleTheme }}>
      {children}
    </ThemeContext.Provider>
  )
}

// Custom Hook ห่อ useContext — เรียกใช้ง่ายและ type ปลอดภัย
export function useTheme() {
  const ctx = useContext(ThemeContext)
  if (!ctx) throw new Error('useTheme ต้องใช้ภายใต้ <ThemeProvider> เท่านั้น')
  return ctx
}
```

> **Tailwind v4:** เพิ่มบรรทัดนี้ใน `src/index.css` เพื่อให้ dark mode ทำงานด้วย class (shadcn init มักใส่ให้แล้ว — ตรวจสอบว่ามี):
>
> ```css
> @custom-variant dark (&:is(.dark *));
> ```

ครอบแอปใน `src/main.tsx`:

```tsx
import { StrictMode } from 'react'
import { createRoot } from 'react-dom/client'
import { ThemeProvider } from '@/contexts/ThemeContext'
import App from './App'
import './index.css'

createRoot(document.getElementById('root')!).render(
  <StrictMode>
    <ThemeProvider>
      <App />
    </ThemeProvider>
  </StrictMode>
)
```

ปุ่มสลับธีมใน `Header.tsx`:

```tsx
import { Button } from '@/components/ui/button'
import { useTheme } from '@/contexts/ThemeContext'
import Clock from '@/components/Clock'

function Header() {
  // ไม่มี prop drilling — ดึงจาก Context ตรง ๆ
  const { theme, toggleTheme } = useTheme()

  return (
    <header className="flex items-center justify-between border-b bg-background px-6 py-4">
      <div>
        <h1 className="text-xl font-bold">📋 TaskFlow</h1>
        <p className="text-sm text-muted-foreground">ระบบจัดการงานของทีม</p>
      </div>
      <div className="flex items-center gap-4">
        <Clock />
        <Button variant="outline" size="icon" onClick={toggleTheme}>
          {theme === 'light' ? '🌙' : '☀️'}
        </Button>
      </div>
    </header>
  )
}

export default Header
```

## Step 3.4 — Refactor เป็น Custom Hooks

สร้าง `src/hooks/useLocalStorage.ts` — แทนโค้ด useEffect จาก Step 3.1:

```ts
import { useState, useEffect } from 'react'

// Generic Hook — ใช้กับข้อมูล type อะไรก็ได้
export function useLocalStorage<T>(key: string, initialValue: T) {
  const [value, setValue] = useState<T>(() => {
    try {
      const saved = localStorage.getItem(key)
      return saved ? (JSON.parse(saved) as T) : initialValue
    } catch {
      return initialValue
    }
  })

  useEffect(() => {
    localStorage.setItem(key, JSON.stringify(value))
  }, [key, value])

  // คืนค่าเป็น tuple เหมือน useState — ใช้แทนกันได้ทันที
  return [value, setValue] as const
}
```

แล้วใน `App.tsx` เปลี่ยนแค่บรรทัดเดียว:

```tsx
// ก่อน: const [tasks, setTasks] = useState<Task[]>(...)
// หลัง:
const [tasks, setTasks] = useLocalStorage<Task[]>('taskflow:tasks', initialTasks)
```

สร้าง `src/hooks/useToggle.ts`:

```ts
import { useState, useCallback } from 'react'

export function useToggle(initial = false) {
  const [on, setOn] = useState(initial)
  const toggle = useCallback(() => setOn((prev) => !prev), [])
  return [on, toggle] as const
}
```

> 📏 **ทบทวน Rules of Hooks ระหว่าง refactor:**
> 1. เรียก Hook ที่ระดับบนสุดเท่านั้น (ห้ามอยู่ใน if / loop / ฟังก์ชันย่อย)
> 2. เรียกได้เฉพาะใน Component หรือ Custom Hook (ชื่อขึ้นต้น use)

## Step 3.5 — React 19: Form Actions + useOptimistic + useFormStatus

อัปเกรดฟอร์มเพิ่มงานเป็นแบบ React 19 — สร้าง `src/components/AddTaskForm.tsx`:

```tsx
import { useRef } from 'react'
import { useFormStatus } from 'react-dom'
import { Input } from '@/components/ui/input'
import { Button } from '@/components/ui/button'
import type { Task } from '@/types/task'

interface AddTaskFormProps {
  onAdd: (task: Task) => Promise<void>
}

// useFormStatus ต้องอยู่ใน component ลูกของ <form> เท่านั้น
function SubmitButton() {
  const { pending } = useFormStatus()
  return (
    <Button type="submit" disabled={pending}>
      {pending ? 'กำลังบันทึก...' : 'เพิ่มงาน'}
    </Button>
  )
}

function AddTaskForm({ onAdd }: AddTaskFormProps) {
  const formRef = useRef<HTMLFormElement>(null)

  // Action — ฟังก์ชันที่รับ FormData ตรงจาก <form action={...}>
  const addTaskAction = async (formData: FormData) => {
    const title = String(formData.get('title') ?? '').trim()
    if (!title) return

    const newTask: Task = {
      id: crypto.randomUUID(),
      title,
      status: 'todo',
      priority: 'medium',
      createdAt: new Date().toISOString(),
    }

    await onAdd(newTask)
    formRef.current?.reset()   // ล้างฟอร์มหลังบันทึกสำเร็จ
  }

  return (
    <form ref={formRef} action={addTaskAction} className="flex gap-2">
      <Input name="title" placeholder="พิมพ์ชื่องานใหม่..." autoComplete="off" />
      <SubmitButton />
    </form>
  )
}

export default AddTaskForm
```

ฝั่ง `App.tsx` — ใช้ `useOptimistic` แสดงงานใหม่ทันทีระหว่างรอ "เซิร์ฟเวอร์":

```tsx
import { useOptimistic } from 'react'
import AddTaskForm from '@/components/AddTaskForm'

// จำลองความหน่วงของเซิร์ฟเวอร์ (วันที่ 5 จะเป็น API จริง)
const fakeApiDelay = () => new Promise((resolve) => setTimeout(resolve, 1200))

// optimisticTasks = tasks จริง + รายการที่ "มองโลกในแง่ดี" ว่าจะสำเร็จ
const [optimisticTasks, addOptimisticTask] = useOptimistic(
  tasks,
  (current, newTask: Task) => [newTask, ...current]
)

const handleAdd = async (newTask: Task) => {
  addOptimisticTask(newTask)        // 1) ขึ้นจอทันที (โปร่งแสงได้ถ้าอยากแยกให้เห็น)
  await fakeApiDelay()              // 2) รอ "เซิร์ฟเวอร์" ตอบ
  setTasks((prev) => [newTask, ...prev])   // 3) บันทึกจริง
}

// อย่าลืมเปลี่ยน TaskList ให้ใช้ optimisticTasks แทน tasks
// <TaskList tasks={visibleOptimisticTasks} ... />
```

> 💡 ชี้ให้เห็นว่า `useActionState` คือคู่หูของ Action เมื่อต้องเก็บ error/ผลลัพธ์ — จะได้ใช้เต็มรูปแบบกับฟอร์ม Login วันที่ 4–5

## ✅ Checkpoint วันที่ 3

- [ ] รีเฟรชหน้าแล้ว tasks + theme ยังอยู่ (ผ่าน useLocalStorage)
- [ ] นาฬิกาเดินและไม่มี memory leak (ทดสอบ unmount)
- [ ] Dark Mode สลับได้ทั้งแอปผ่าน Context — ไม่มี prop drilling
- [ ] สถิติคำนวณผ่าน useMemo (ดู console ว่าไม่รันตอนพิมพ์ search)
- [ ] เพิ่มงานแล้วขึ้นจอทันที (Optimistic) ปุ่มแสดง "กำลังบันทึก..." ระหว่างรอ
- [ ] มีโฟลเดอร์ `hooks/` พร้อม useLocalStorage และ useToggle

---
---

# 🟦 วันที่ 4 — React Router v7, ฟอร์มมืออาชีพ (RHF + Zod) และ Global State

## Step 4.1 — ติดตั้ง React Router v7 และจัดโครงหน้า

```bash
pnpm add react-router
```

> React Router v7 ใช้ package ชื่อ `react-router` (ไม่ใช่ `react-router-dom` แบบ v6)

**ปรับโครงสร้างโปรเจกต์เป็นแบบ Feature-based:**

```
src/
├── app/
│   ├── router.tsx          # กำหนดเส้นทางทั้งหมด
│   └── MainLayout.tsx      # Layout หลัก (Navbar + Outlet)
├── pages/
│   ├── DashboardPage.tsx
│   ├── TasksPage.tsx       # ย้าย logic ส่วนใหญ่จาก App.tsx มาที่นี่
│   ├── TaskDetailPage.tsx
│   ├── LoginPage.tsx
│   ├── RegisterPage.tsx
│   └── NotFoundPage.tsx
├── features/
│   └── auth/
│       └── useAuthStore.ts # Zustand (Step 4.3)
└── ...
```

สร้าง `src/app/MainLayout.tsx`:

```tsx
import { NavLink, Outlet } from 'react-router'
import Header from '@/components/Header'

// ฟังก์ชันกำหนด class ตามสถานะ active ของ NavLink
const navClass = ({ isActive }: { isActive: boolean }) =>
  `rounded-md px-3 py-1.5 text-sm transition-colors ${
    isActive
      ? 'bg-primary text-primary-foreground'
      : 'text-muted-foreground hover:bg-muted'
  }`

function MainLayout() {
  return (
    <>
      <Header />
      <nav className="flex gap-2 border-b px-6 py-2">
        <NavLink to="/" end className={navClass}>แดชบอร์ด</NavLink>
        <NavLink to="/tasks" className={navClass}>งานทั้งหมด</NavLink>
      </nav>
      <main className="mx-auto max-w-6xl p-6">
        {/* Outlet = ตำแหน่งที่หน้าลูกจะถูก render */}
        <Outlet />
      </main>
    </>
  )
}

export default MainLayout
```

สร้าง `src/app/router.tsx`:

```tsx
import { createBrowserRouter } from 'react-router'
import MainLayout from '@/app/MainLayout'
import DashboardPage from '@/pages/DashboardPage'
import TasksPage from '@/pages/TasksPage'
import TaskDetailPage from '@/pages/TaskDetailPage'
import LoginPage from '@/pages/LoginPage'
import RegisterPage from '@/pages/RegisterPage'
import NotFoundPage from '@/pages/NotFoundPage'
import ProtectedRoute from '@/app/ProtectedRoute'

export const router = createBrowserRouter([
  {
    // Layout Route — ทุก children จะถูกครอบด้วย MainLayout
    element: <MainLayout />,
    children: [
      { index: true, element: <DashboardPage /> },
      {
        // Protected — ต้อง login ก่อน (สร้างใน Step 4.4)
        element: <ProtectedRoute />,
        children: [
          { path: 'tasks', element: <TasksPage /> },
          { path: 'tasks/:id', element: <TaskDetailPage /> },   // Dynamic param
        ],
      },
    ],
  },
  { path: 'login', element: <LoginPage /> },
  { path: 'register', element: <RegisterPage /> },
  { path: '*', element: <NotFoundPage /> },   // 404 — จับทุก path ที่เหลือ
])
```

แก้ `src/main.tsx`:

```tsx
import { StrictMode } from 'react'
import { createRoot } from 'react-dom/client'
import { RouterProvider } from 'react-router'
import { ThemeProvider } from '@/contexts/ThemeContext'
import { router } from '@/app/router'
import './index.css'

createRoot(document.getElementById('root')!).render(
  <StrictMode>
    <ThemeProvider>
      <RouterProvider router={router} />
    </ThemeProvider>
  </StrictMode>
)
```

## Step 4.2 — useParams, useSearchParams และ useNavigate

**TaskDetailPage — อ่านพารามิเตอร์จาก URL:**

```tsx
import { useParams, useNavigate } from 'react-router'
import { Button } from '@/components/ui/button'
import { useLocalStorage } from '@/hooks/useLocalStorage'
import type { Task } from '@/types/task'

function TaskDetailPage() {
  // ดึงค่า :id จาก URL เช่น /tasks/abc123 → id = 'abc123'
  const { id } = useParams<{ id: string }>()
  const navigate = useNavigate()
  const [tasks] = useLocalStorage<Task[]>('taskflow:tasks', [])

  const task = tasks.find((t) => t.id === id)

  if (!task) {
    return (
      <div className="space-y-4 text-center">
        <p>ไม่พบงานที่ต้องการ 😢</p>
        <Button onClick={() => navigate('/tasks')}>กลับหน้ารายการ</Button>
      </div>
    )
  }

  return (
    <article className="space-y-3">
      <h2 className="text-2xl font-bold">{task.title}</h2>
      {task.description && <p>{task.description}</p>}
      <p className="text-sm text-muted-foreground">
        สร้างเมื่อ: {new Date(task.createdAt).toLocaleDateString('th-TH')}
      </p>
      <Button variant="outline" onClick={() => navigate(-1)}>← ย้อนกลับ</Button>
    </article>
  )
}

export default TaskDetailPage
```

**ย้าย filter ไปอยู่ใน Query String (แชร์ลิงก์ได้!):** ใน `TasksPage.tsx`

```tsx
import { useSearchParams, Link } from 'react-router'

const [searchParams, setSearchParams] = useSearchParams()

// อ่านค่าจาก URL เช่น /tasks?status=doing → filter = 'doing'
const filter = (searchParams.get('status') ?? 'all') as TaskStatus | 'all'

const handleFilterChange = (value: string) => {
  // เขียนกลับลง URL — กด back/forward ได้ แชร์ลิงก์ได้
  if (value === 'all') {
    searchParams.delete('status')
  } else {
    searchParams.set('status', value)
  }
  setSearchParams(searchParams)
}

// ใน TaskCard เพิ่มลิงก์ไปหน้า detail
// <Link to={`/tasks/${task.id}`} className="hover:underline">{task.title}</Link>
```

## Step 4.3 — ฟอร์ม Login ระดับมืออาชีพ: React Hook Form + Zod + shadcn Form

```bash
pnpm add react-hook-form zod @hookform/resolvers
pnpm dlx shadcn@latest add form sonner
```

> shadcn component `form` ถูกออกแบบมาคู่กับ React Hook Form โดยตรง — ได้ทั้ง label, error message และ accessibility ครบในตัว

สร้าง `src/features/auth/schemas.ts`:

```ts
import { z } from 'zod'

// Schema = แหล่งความจริงหนึ่งเดียวของกติกาฟอร์ม
export const loginSchema = z.object({
  email: z.string().email('รูปแบบอีเมลไม่ถูกต้อง'),
  password: z.string().min(8, 'รหัสผ่านต้องมีอย่างน้อย 8 ตัวอักษร'),
})

export const registerSchema = loginSchema
  .extend({
    name: z.string().min(2, 'กรุณากรอกชื่ออย่างน้อย 2 ตัวอักษร'),
    confirmPassword: z.string(),
  })
  .refine((data) => data.password === data.confirmPassword, {
    message: 'รหัสผ่านทั้งสองช่องไม่ตรงกัน',
    path: ['confirmPassword'],   // ให้ error ไปโผล่ที่ช่องยืนยันรหัสผ่าน
  })

// สร้าง type จาก schema อัตโนมัติ — ไม่ต้องประกาศซ้ำ
export type LoginInput = z.infer<typeof loginSchema>
export type RegisterInput = z.infer<typeof registerSchema>
```

สร้าง `src/pages/LoginPage.tsx`:

```tsx
import { useForm } from 'react-hook-form'
import { zodResolver } from '@hookform/resolvers/zod'
import { useNavigate, Link } from 'react-router'
import { toast } from 'sonner'
import { Button } from '@/components/ui/button'
import { Input } from '@/components/ui/input'
import { Card, CardContent, CardHeader, CardTitle } from '@/components/ui/card'
import {
  Form, FormControl, FormField, FormItem, FormLabel, FormMessage,
} from '@/components/ui/form'
import { loginSchema, type LoginInput } from '@/features/auth/schemas'
import { useAuthStore } from '@/features/auth/useAuthStore'

function LoginPage() {
  const navigate = useNavigate()
  const login = useAuthStore((state) => state.login)

  const form = useForm<LoginInput>({
    resolver: zodResolver(loginSchema),    // เชื่อม Zod เข้ากับ RHF
    defaultValues: { email: '', password: '' },
  })

  const onSubmit = async (values: LoginInput) => {
    // วันนี้ใช้ mock — วันที่ 5 จะเปลี่ยนเป็น API จริง
    await login(values.email, values.password)
    toast.success('เข้าสู่ระบบสำเร็จ ยินดีต้อนรับ! 🎉')
    navigate('/tasks')
  }

  return (
    <div className="flex min-h-screen items-center justify-center p-4">
      <Card className="w-full max-w-sm">
        <CardHeader>
          <CardTitle>เข้าสู่ระบบ TaskFlow</CardTitle>
        </CardHeader>
        <CardContent>
          <Form {...form}>
            <form onSubmit={form.handleSubmit(onSubmit)} className="space-y-4">
              <FormField
                control={form.control}
                name="email"
                render={({ field }) => (
                  <FormItem>
                    <FormLabel>อีเมล</FormLabel>
                    <FormControl>
                      <Input placeholder="you@example.com" {...field} />
                    </FormControl>
                    <FormMessage />  {/* ข้อความ error จาก Zod ขึ้นตรงนี้ */}
                  </FormItem>
                )}
              />
              <FormField
                control={form.control}
                name="password"
                render={({ field }) => (
                  <FormItem>
                    <FormLabel>รหัสผ่าน</FormLabel>
                    <FormControl>
                      <Input type="password" {...field} />
                    </FormControl>
                    <FormMessage />
                  </FormItem>
                )}
              />
              <Button
                type="submit"
                className="w-full"
                disabled={form.formState.isSubmitting}
              >
                {form.formState.isSubmitting ? 'กำลังตรวจสอบ...' : 'เข้าสู่ระบบ'}
              </Button>
            </form>
          </Form>
          <p className="mt-4 text-center text-sm text-muted-foreground">
            ยังไม่มีบัญชี? <Link to="/register" className="underline">สมัครสมาชิก</Link>
          </p>
        </CardContent>
      </Card>
    </div>
  )
}

export default LoginPage
```

อย่าลืมวาง `<Toaster />` ของ sonner ไว้ใน `main.tsx` (ตามเอกสาร shadcn) และให้ผู้เรียนทำ `RegisterPage` เองเป็นแบบฝึก (ใช้ `registerSchema` ที่มี `refine`)

## Step 4.4 — Zustand: Global Auth Store

```bash
pnpm add zustand
```

สร้าง `src/features/auth/useAuthStore.ts`:

```ts
import { create } from 'zustand'
import { persist, devtools } from 'zustand/middleware'

interface User {
  id: string
  name: string
  email: string
  role: 'admin' | 'member'
}

interface AuthState {
  user: User | null
  token: string | null
  // computed-style helper
  isAuthenticated: () => boolean
  // actions
  login: (email: string, password: string) => Promise<void>
  logout: () => void
}

export const useAuthStore = create<AuthState>()(
  devtools(
    persist(
      (set, get) => ({
        user: null,
        token: null,

        isAuthenticated: () => get().token !== null,

        login: async (email, _password) => {
          // 🔧 Mock API — วันที่ 5 จะแทนด้วย axios เรียก /auth/login จริง
          await new Promise((resolve) => setTimeout(resolve, 800))
          set({
            user: { id: 'u1', name: 'สมชาย ใจดี', email, role: 'member' },
            token: 'mock-jwt-token',
          })
        },

        logout: () => set({ user: null, token: null }),
      }),
      { name: 'taskflow:auth' }   // persist ลง localStorage อัตโนมัติ
    )
  )
)
```

สร้าง `src/app/ProtectedRoute.tsx`:

```tsx
import { Navigate, Outlet, useLocation } from 'react-router'
import { useAuthStore } from '@/features/auth/useAuthStore'

function ProtectedRoute() {
  const token = useAuthStore((state) => state.token)
  const location = useLocation()

  // ยังไม่ login → เด้งไปหน้า login พร้อมจำหน้าที่พยายามเข้า
  if (!token) {
    return <Navigate to="/login" state={{ from: location }} replace />
  }

  return <Outlet />
}

export default ProtectedRoute
```

แสดงชื่อผู้ใช้ + ปุ่ม Logout ใน `Header.tsx`:

```tsx
import { useAuthStore } from '@/features/auth/useAuthStore'
import { useNavigate } from 'react-router'

const { user, logout } = useAuthStore()
const navigate = useNavigate()

// ใน JSX
{user ? (
  <div className="flex items-center gap-2">
    <span className="text-sm">สวัสดี, {user.name}</span>
    <Button variant="ghost" size="sm" onClick={() => { logout(); navigate('/login') }}>
      ออกจากระบบ
    </Button>
  </div>
) : null}
```

## Step 4.5 — Redux Toolkit (เปรียบเทียบ — ทำใน branch แยก)

```bash
git checkout -b feature/redux
pnpm add @reduxjs/toolkit react-redux
```

สร้าง `src/store/tasksSlice.ts`:

```ts
import { createSlice, createAsyncThunk, type PayloadAction } from '@reduxjs/toolkit'
import type { Task } from '@/types/task'

interface TasksState {
  items: Task[]
  loading: boolean
  error: string | null
}

const initialState: TasksState = { items: [], loading: false, error: null }

// จัดการ async ด้วย createAsyncThunk (เทียบกับที่ Zustand เขียน async ใน action ตรง ๆ)
export const fetchTasks = createAsyncThunk('tasks/fetch', async () => {
  const res = await fetch('https://jsonplaceholder.typicode.com/todos?_limit=5')
  const data = await res.json()
  // แปลงข้อมูลให้ตรงกับ Task ของเรา
  return data.map((item: { id: number; title: string; completed: boolean }) => ({
    id: String(item.id),
    title: item.title,
    status: item.completed ? 'done' : 'todo',
    priority: 'medium',
    createdAt: new Date().toISOString(),
  })) as Task[]
})

const tasksSlice = createSlice({
  name: 'tasks',
  initialState,
  reducers: {
    // RTK ใช้ Immer ข้างใน — เขียนเหมือน mutate ได้ แต่จริง ๆ immutable
    addTask: (state, action: PayloadAction<Task>) => {
      state.items.unshift(action.payload)
    },
    toggleTask: (state, action: PayloadAction<string>) => {
      const task = state.items.find((t) => t.id === action.payload)
      if (task) task.status = task.status === 'done' ? 'todo' : 'done'
    },
  },
  extraReducers: (builder) => {
    builder
      .addCase(fetchTasks.pending, (state) => { state.loading = true })
      .addCase(fetchTasks.fulfilled, (state, action) => {
        state.loading = false
        state.items = action.payload
      })
      .addCase(fetchTasks.rejected, (state, action) => {
        state.loading = false
        state.error = action.error.message ?? 'เกิดข้อผิดพลาด'
      })
  },
})

export const { addTask, toggleTask } = tasksSlice.actions
export default tasksSlice.reducer
```

สร้าง `src/store/index.ts` แล้วครอบแอปด้วย `<Provider store={store}>`:

```ts
import { configureStore } from '@reduxjs/toolkit'
import tasksReducer from './tasksSlice'

export const store = configureStore({
  reducer: { tasks: tasksReducer },
})

// Type helper สำหรับ useSelector / useDispatch
export type RootState = ReturnType<typeof store.getState>
export type AppDispatch = typeof store.dispatch
```

การใช้งานใน Component:

```tsx
import { useSelector, useDispatch } from 'react-redux'
import type { RootState, AppDispatch } from '@/store'
import { fetchTasks, toggleTask } from '@/store/tasksSlice'

const dispatch = useDispatch<AppDispatch>()
const { items, loading } = useSelector((state: RootState) => state.tasks)

useEffect(() => {
  dispatch(fetchTasks())
}, [dispatch])
```

**ตารางสรุปเปรียบเทียบ (ใช้ปิดท้ายวัน):**

| ประเด็น | Zustand | Redux Toolkit |
| --- | --- | --- |
| Boilerplate | น้อยมาก | ปานกลาง (ลดลงมากจาก Redux เดิม) |
| Learning Curve | ต่ำ | ปานกลาง |
| DevTools / Time Travel | มี (ผ่าน middleware) | ดีที่สุดในตลาด |
| Async | เขียน async ใน action ได้เลย | createAsyncThunk / RTK Query |
| เหมาะกับ | แอปเล็ก–กลาง, ทีมเล็ก | องค์กรใหญ่, ทีมใหญ่, กติกาเข้มงวด |
| **คำแนะนำใน TaskFlow** | ✅ ใช้เป็นหลัก (Auth/UI State) | เรียนรู้ไว้เพื่องานองค์กร |

> หมายเหตุ: Server State (ข้อมูลจาก API) พรุ่งนี้จะยกให้ **TanStack Query** ดูแล — ไม่ควรเก็บใน Zustand/Redux

## ✅ Checkpoint วันที่ 4

- [ ] เปลี่ยนหน้าได้ครบ refresh ที่ /tasks/:id แล้วไม่พัง มีหน้า 404
- [ ] Filter อยู่ใน URL (?status=doing) — คัดลอกลิงก์ไปเปิดแท็บใหม่แล้วผลเหมือนเดิม
- [ ] Login ผิดกติกาแล้วเห็นข้อความ error ภาษาไทยจาก Zod ใต้ field
- [ ] ยังไม่ login เข้า /tasks ไม่ได้ → เด้งไป /login
- [ ] Logout แล้ว token หายจาก localStorage (ดูใน DevTools → Application)
- [ ] อธิบายความต่าง Zustand vs RTK และเหตุผลการเลือกใช้ได้

---
---

# 🟦 วันที่ 5 — Prisma 7 + Neon, TanStack Query, JWT Auth, Performance และ Deploy

> วันนี้ใช้ **starter repo ฝั่ง API** ที่วิทยากรเตรียมไว้ (Hono + Prisma) ผู้เรียนโฟกัสที่ Prisma Schema และฝั่ง React

## Step 5.1 — สร้างฐานข้อมูล PostgreSQL บน Neon

1. สมัคร/เข้าสู่ระบบที่ **neon.tech** (ฟรี ไม่ต้องใส่บัตรเครดิต)
2. สร้าง Project ใหม่ชื่อ `taskflow` → เลือก Region `Singapore (ap-southeast-1)`
3. คัดลอก **Connection String** จากหน้า Dashboard
4. ในโฟลเดอร์ `server/` ของ starter repo สร้างไฟล์ `.env`:

```env
DATABASE_URL="postgresql://user:password@ep-xxx.ap-southeast-1.aws.neon.tech/taskflow?sslmode=require"
JWT_SECRET="เปลี่ยนเป็นค่าลับของคุณเอง"
```

> ⚠️ **ย้ำเรื่องความปลอดภัย:** `.env` ต้องอยู่ใน `.gitignore` เสมอ — ให้ commit เฉพาะ `.env.example` ที่ไม่มีค่าจริง

## Step 5.2 — เขียน Prisma Schema + Migration

ไฟล์ `server/prisma/schema.prisma`:

```prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

// ผู้ใช้ระบบ — 1 User มีได้หลาย Task (One-to-Many)
model User {
  id        String   @id @default(uuid())
  name      String
  email     String   @unique
  password  String   // เก็บแบบ hash ด้วย bcrypt เท่านั้น
  role      Role     @default(MEMBER)
  tasks     Task[]
  createdAt DateTime @default(now())
}

model Task {
  id          String    @id @default(uuid())
  title       String
  description String?
  status      Status    @default(TODO)
  priority    Priority  @default(MEDIUM)
  dueDate     DateTime?
  createdAt   DateTime  @default(now())
  updatedAt   DateTime  @updatedAt

  // Relation: Task เป็นของ User คนเดียว
  owner   User   @relation(fields: [ownerId], references: [id], onDelete: Cascade)
  ownerId String
}

enum Role {
  ADMIN
  MEMBER
}

enum Status {
  TODO
  DOING
  DONE
}

enum Priority {
  LOW
  MEDIUM
  HIGH
}
```

รัน Migration แล้วไปดู Table เกิดจริงบน Neon Console:

```bash
cd server
pnpm prisma migrate dev --name init   # สร้าง table บน Neon
pnpm prisma studio                    # GUI ดู/แก้ข้อมูลในเบราว์เซอร์
pnpm dev                              # รัน API ที่ http://localhost:3000
```

**ตัวอย่าง Endpoint ใน starter (Hono + Prisma Client) — เปิดให้ผู้เรียนอ่านร่วมกัน:**

```ts
// server/src/routes/tasks.ts (ตัดมาเฉพาะส่วนสำคัญ)
import { Hono } from 'hono'
import { prisma } from '../lib/prisma'

const tasks = new Hono()

// GET /api/tasks?page=1 — ดึงงานของผู้ใช้ที่ login พร้อมแบ่งหน้า
tasks.get('/', async (c) => {
  const userId = c.get('userId')          // มาจาก JWT middleware
  const page = Number(c.req.query('page') ?? 1)
  const pageSize = 9

  const [items, total] = await Promise.all([
    prisma.task.findMany({
      where: { ownerId: userId },
      orderBy: { createdAt: 'desc' },
      skip: (page - 1) * pageSize,
      take: pageSize,
    }),
    prisma.task.count({ where: { ownerId: userId } }),
  ])

  return c.json({ items, total, page, totalPages: Math.ceil(total / pageSize) })
})

// POST /api/tasks — สร้างงานใหม่
tasks.post('/', async (c) => {
  const userId = c.get('userId')
  const body = await c.req.json()
  const task = await prisma.task.create({
    data: { ...body, ownerId: userId },
  })
  return c.json(task, 201)
})

export default tasks
```

## Step 5.3 — ฝั่ง React: axios instance + TanStack Query

```bash
# กลับมาที่โปรเจกต์ taskflow ฝั่ง frontend
pnpm add @tanstack/react-query axios
pnpm add -D @tanstack/react-query-devtools
```

สร้าง `src/lib/api.ts` — axios พร้อมแนบ token อัตโนมัติ:

```ts
import axios from 'axios'
import { useAuthStore } from '@/features/auth/useAuthStore'

export const api = axios.create({
  baseURL: import.meta.env.VITE_API_URL ?? 'http://localhost:3000/api',
})

// Request Interceptor — แนบ JWT ทุก request โดยอัตโนมัติ
api.interceptors.request.use((config) => {
  const token = useAuthStore.getState().token
  if (token) {
    config.headers.Authorization = `Bearer ${token}`
  }
  return config
})

// Response Interceptor — เจอ 401 ให้ logout แล้วพาไปหน้า login
api.interceptors.response.use(
  (response) => response,
  (error) => {
    if (error.response?.status === 401) {
      useAuthStore.getState().logout()
      window.location.href = '/login'
    }
    return Promise.reject(error)
  }
)
```

ตั้งค่า QueryClient ใน `src/main.tsx`:

```tsx
import { QueryClient, QueryClientProvider } from '@tanstack/react-query'
import { ReactQueryDevtools } from '@tanstack/react-query-devtools'

const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      staleTime: 1000 * 60,   // ข้อมูล "สด" 1 นาที — ไม่ refetch ซ้ำโดยไม่จำเป็น
      retry: 1,
    },
  },
})

createRoot(document.getElementById('root')!).render(
  <StrictMode>
    <QueryClientProvider client={queryClient}>
      <ThemeProvider>
        <RouterProvider router={router} />
      </ThemeProvider>
      <ReactQueryDevtools initialIsOpen={false} />
    </QueryClientProvider>
  </StrictMode>
)
```

## Step 5.4 — useQuery + useMutation: จุดเปลี่ยนของแอป

> 🎯 **จุดสอนสำคัญที่สุดของวัน:** สาธิตปัญหาของการ fetch ด้วย `useEffect` ก่อน (ไม่มี cache, ยิงซ้ำ, race condition, ต้องจัดการ loading/error เอง) แล้วค่อยแทนที่ด้วย TanStack Query เพื่อให้เห็นว่า "Server State" ต่างจาก "Client State" อย่างไร

สร้าง `src/features/tasks/api.ts`:

```ts
import { api } from '@/lib/api'
import type { Task } from '@/types/task'

export interface TasksPage {
  items: Task[]
  total: number
  page: number
  totalPages: number
}

export const fetchTasks = async (page: number): Promise<TasksPage> => {
  const { data } = await api.get<TasksPage>('/tasks', { params: { page } })
  return data
}

export const createTask = async (input: Pick<Task, 'title' | 'priority'>): Promise<Task> => {
  const { data } = await api.post<Task>('/tasks', input)
  return data
}

export const updateTask = async (task: Partial<Task> & { id: string }): Promise<Task> => {
  const { data } = await api.patch<Task>(`/tasks/${task.id}`, task)
  return data
}

export const deleteTask = async (id: string): Promise<void> => {
  await api.delete(`/tasks/${id}`)
}
```

สร้าง `src/features/tasks/queries.ts` — Custom Hooks ครอบ Query/Mutation:

```ts
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query'
import { toast } from 'sonner'
import { fetchTasks, createTask, updateTask, deleteTask } from './api'

// Query Key รวมไว้ที่เดียว ป้องกันพิมพ์ผิด
export const taskKeys = {
  all: ['tasks'] as const,
  list: (page: number) => ['tasks', 'list', page] as const,
}

// ดึงรายการงานแบบแบ่งหน้า
export function useTasks(page: number) {
  return useQuery({
    queryKey: taskKeys.list(page),
    queryFn: () => fetchTasks(page),
    placeholderData: (prev) => prev,   // คงข้อมูลหน้าเดิมไว้ระหว่างโหลดหน้าใหม่
  })
}

// สร้างงานใหม่ + invalidate ให้รายการรีเฟรชเอง
export function useCreateTask() {
  const queryClient = useQueryClient()
  return useMutation({
    mutationFn: createTask,
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: taskKeys.all })
      toast.success('เพิ่มงานเรียบร้อย')
    },
    onError: () => toast.error('เพิ่มงานไม่สำเร็จ กรุณาลองใหม่'),
  })
}

export function useUpdateTask() {
  const queryClient = useQueryClient()
  return useMutation({
    mutationFn: updateTask,
    onSuccess: () => queryClient.invalidateQueries({ queryKey: taskKeys.all }),
  })
}

export function useDeleteTask() {
  const queryClient = useQueryClient()
  return useMutation({
    mutationFn: deleteTask,
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: taskKeys.all })
      toast.success('ลบงานแล้ว')
    },
  })
}
```

ใช้งานใน `TasksPage.tsx` (แทน useLocalStorage เดิมทั้งหมด):

```tsx
import { useState } from 'react'
import { Button } from '@/components/ui/button'
import { Skeleton } from '@/components/ui/skeleton'   // pnpm dlx shadcn@latest add skeleton
import TaskList from '@/components/TaskList'
import { useTasks, useUpdateTask, useDeleteTask } from '@/features/tasks/queries'

function TasksPage() {
  const [page, setPage] = useState(1)
  const { data, isPending, isError, isFetching } = useTasks(page)
  const updateMutation = useUpdateTask()
  const deleteMutation = useDeleteTask()

  // Loading State ครั้งแรก — ใช้ Skeleton ของ shadcn
  if (isPending) {
    return (
      <div className="grid grid-cols-1 gap-4 md:grid-cols-2 lg:grid-cols-3">
        {Array.from({ length: 6 }).map((_, i) => (
          <Skeleton key={i} className="h-40 rounded-xl" />
        ))}
      </div>
    )
  }

  if (isError) {
    return <p className="text-center text-destructive">โหลดข้อมูลไม่สำเร็จ 😢</p>
  }

  return (
    <div className="space-y-6">
      <TaskList
        tasks={data.items}
        onToggle={(id) => {
          const task = data.items.find((t) => t.id === id)
          if (task) {
            updateMutation.mutate({
              id,
              status: task.status === 'done' ? 'todo' : 'done',
            })
          }
        }}
        onDelete={(id) => deleteMutation.mutate(id)}
      />

      {/* Pagination */}
      <div className="flex items-center justify-center gap-3">
        <Button
          variant="outline"
          disabled={page === 1}
          onClick={() => setPage((p) => p - 1)}
        >
          ← ก่อนหน้า
        </Button>
        <span className="text-sm text-muted-foreground">
          หน้า {data.page} / {data.totalPages} {isFetching && '⏳'}
        </span>
        <Button
          variant="outline"
          disabled={page >= data.totalPages}
          onClick={() => setPage((p) => p + 1)}
        >
          ถัดไป →
        </Button>
      </div>
    </div>
  )
}

export default TasksPage
```

> 🔬 เปิด **TanStack Query DevTools** ให้ผู้เรียนดู cache สด ๆ: เปลี่ยนหน้าไป-กลับแล้วข้อมูลโผล่ทันทีจาก cache, ดูสถานะ fresh / stale / fetching

## Step 5.5 — JWT Authentication ของจริง

อัปเดต `useAuthStore.ts` ให้เรียก API จริงแทน mock:

```ts
import { api } from '@/lib/api'

interface LoginResponse {
  user: User
  token: string
}

// ภายใน create()...
login: async (email, password) => {
  const { data } = await api.post<LoginResponse>('/auth/login', { email, password })
  set({ user: data.user, token: data.token })
},

register: async (name: string, email: string, password: string) => {
  const { data } = await api.post<LoginResponse>('/auth/register', { name, email, password })
  set({ user: data.user, token: data.token })
},
```

**Role-based Access** — ปุ่มลบแสดงเฉพาะ admin หรือเจ้าของงาน:

```tsx
const user = useAuthStore((state) => state.user)
const canDelete = user?.role === 'admin' || task.ownerId === user?.id

{canDelete && (
  <Button size="sm" variant="destructive" onClick={() => onDelete(task.id)}>
    ลบ
  </Button>
)}
```

> 💬 **อภิปรายความปลอดภัย:** ข้อดี/ข้อเสียของการเก็บ token ใน localStorage (เสี่ยง XSS) เทียบกับ httpOnly cookie (เสี่ยง CSRF แต่ปลอดภัยกว่าโดยรวม) — โปรดักชันจริงแนะนำ httpOnly cookie + Refresh Token Rotation

## Step 5.6 — Performance + Error Boundary

**Code Splitting ราย Route ด้วย React.lazy:**

```tsx
// src/app/router.tsx
import { lazy, Suspense } from 'react'
import { Skeleton } from '@/components/ui/skeleton'

// หน้า Dashboard จะถูกแยกเป็นไฟล์ JS ต่างหาก โหลดเมื่อเข้าหน้าจริง
const DashboardPage = lazy(() => import('@/pages/DashboardPage'))

const PageLoader = () => (
  <div className="space-y-4 p-6">
    <Skeleton className="h-8 w-1/3" />
    <Skeleton className="h-40 w-full" />
  </div>
)

// ใน routes
{
  index: true,
  element: (
    <Suspense fallback={<PageLoader />}>
      <DashboardPage />
    </Suspense>
  ),
},
```

**วัดผลจริงด้วย Bundle Visualizer:**

```bash
pnpm add -D rollup-plugin-visualizer
pnpm build   # เปิดไฟล์ stats.html เทียบขนาดก่อน/หลังทำ lazy
```

**React.memo กับ TaskCard:**

```tsx
import { memo } from 'react'

// re-render เฉพาะเมื่อ props เปลี่ยนจริง
// ทำงานร่วมกับ useCallback ของ onToggle/onDelete จากวันที่ 3
export default memo(TaskCard)
```

**Error Boundary** — สร้าง `src/components/ErrorBoundary.tsx`:

```tsx
import { Component, type ReactNode } from 'react'
import { Button } from '@/components/ui/button'

interface Props { children: ReactNode }
interface State { hasError: boolean }

// Error Boundary ยังต้องเป็น Class Component (กรณีเดียวที่ใช้ Class ในคอร์สนี้)
class ErrorBoundary extends Component<Props, State> {
  state: State = { hasError: false }

  static getDerivedStateFromError() {
    return { hasError: true }
  }

  render() {
    if (this.state.hasError) {
      return (
        <div className="flex min-h-screen flex-col items-center justify-center gap-4">
          <p className="text-lg">เกิดข้อผิดพลาดบางอย่าง 😵</p>
          <Button onClick={() => window.location.reload()}>โหลดหน้าใหม่</Button>
        </div>
      )
    }
    return this.props.children
  }
}

export default ErrorBoundary
```

ครอบทั้งแอปใน `main.tsx` แล้วทดสอบโดยสร้าง component ที่ `throw new Error('ทดสอบ!')`

## Step 5.7 — Build + Deploy ขึ้น Vercel

**ตั้งค่า Environment Variable ฝั่ง Frontend** — สร้าง `.env.production`:

```env
VITE_API_URL=https://taskflow-api.your-domain.com/api
```

> ตัวแปรใน Vite ต้องขึ้นต้นด้วย `VITE_` เท่านั้นจึงจะถูกฝังเข้า bundle และอ่านผ่าน `import.meta.env`

**Build และตรวจสอบ:**

```bash
pnpm build      # ได้โฟลเดอร์ dist/
pnpm preview    # ทดสอบ production build ที่เครื่องก่อน deploy
```

**Deploy ขึ้น Vercel:**

1. push โค้ดขึ้น GitHub
2. เข้า **vercel.com** → Add New Project → เลือก repo `taskflow`
3. Vercel ตรวจพบ Vite อัตโนมัติ (Build: `pnpm build`, Output: `dist`)
4. ใส่ Environment Variable `VITE_API_URL` ใน Settings
5. เพิ่มไฟล์ `vercel.json` กัน 404 ตอน refresh หน้าที่เป็น SPA route:

```json
{
  "rewrites": [{ "source": "/(.*)", "destination": "/index.html" }]
}
```

6. กด Deploy → ได้ URL จริง เปิดจากมือถือทดสอบ Register → Login → CRUD ครบวงจร

> ฝั่ง API (server/) deploy ขึ้นบริการเช่น Railway / Render / Fly.io — วิทยากร deploy ตัวอย่างไว้ล่วงหน้า 1 ชุดให้ทุกคนชี้ไปใช้ได้ทันทีหากเวลาไม่พอ

## Step 5.8 — Capstone ปิดท้าย: ฟีเจอร์เสริม + นำเสนอ (60 นาที)

แต่ละคน/ทีมเลือก 1 ฟีเจอร์ ลงมือทำเองโดยใช้ความรู้ทั้ง 5 วัน แล้วนำเสนอ 3 นาที:

| ฟีเจอร์ | แนวทาง | ความรู้ที่ใช้ |
| --- | --- | --- |
| แท็ก/หมวดหมู่งาน | เพิ่ม model Tag + relation Many-to-Many | Prisma, Query |
| Badge แจ้งงานใกล้ครบกำหนด | คำนวณจาก dueDate แสดงบน Navbar | useMemo, Badge |
| หน้า Profile แก้ไขข้อมูล | ฟอร์ม RHF + Zod + useMutation | วันที่ 4–5 |
| Dialog ยืนยันก่อนลบ | `pnpm dlx shadcn@latest add alert-dialog` | shadcn, Event |
| Infinite Scroll | เปลี่ยน useQuery เป็น useInfiniteQuery | TanStack Query |

ปิดคอร์สด้วยการแนะนำเส้นทางต่อยอด: **Next.js App Router + Server Components** (เนื้อหาหลายส่วนของ React 19 เช่น Actions ถูกออกแบบมาเพื่อโลก Full-stack นี้โดยตรง)

## ✅ Checkpoint วันที่ 5 (Definition of Done ของทั้งคอร์ส)

- [ ] ข้อมูลอยู่บน Neon จริง (Prisma Studio เห็นข้อมูลตรงกับหน้าเว็บ)
- [ ] CRUD ทั้งหมดผ่าน TanStack Query + เปิด DevTools อธิบาย cache ได้
- [ ] Login/Logout ด้วย JWT จริง, 401 แล้วเด้งออกอัตโนมัติ, Role แสดงผลต่างกัน
- [ ] Dashboard ถูก lazy load (ดูใน Network tab เป็นไฟล์แยก)
- [ ] เว็บออนไลน์บน Vercel เข้าจากมือถือได้ refresh หน้าไหนก็ไม่ 404
- [ ] นำเสนอฟีเจอร์เสริมของตนเอง 1 อย่าง

---

## ภาคผนวก ก. — สรุปคำสั่งติดตั้งทั้งหมด (Quick Reference)

```bash
# วันที่ 1
pnpm create vite taskflow --template react-ts
pnpm add tailwindcss @tailwindcss/vite
pnpm add -D @types/node
pnpm dlx shadcn@latest init
pnpm dlx shadcn@latest add button card badge input label

# วันที่ 2
pnpm dlx shadcn@latest add tabs select

# วันที่ 4
pnpm add react-router
pnpm add react-hook-form zod @hookform/resolvers
pnpm dlx shadcn@latest add form sonner
pnpm add zustand
pnpm add @reduxjs/toolkit react-redux        # (branch feature/redux)

# วันที่ 5
pnpm add @tanstack/react-query axios
pnpm add -D @tanstack/react-query-devtools rollup-plugin-visualizer
pnpm dlx shadcn@latest add skeleton alert-dialog
```

## ภาคผนวก ข. — โครงสร้างโปรเจกต์สุดท้าย

```
taskflow/
├── src/
│   ├── app/
│   │   ├── router.tsx
│   │   ├── MainLayout.tsx
│   │   └── ProtectedRoute.tsx
│   ├── pages/
│   │   ├── DashboardPage.tsx
│   │   ├── TasksPage.tsx
│   │   ├── TaskDetailPage.tsx
│   │   ├── LoginPage.tsx
│   │   ├── RegisterPage.tsx
│   │   └── NotFoundPage.tsx
│   ├── features/
│   │   ├── auth/
│   │   │   ├── schemas.ts
│   │   │   └── useAuthStore.ts
│   │   └── tasks/
│   │       ├── api.ts
│   │       └── queries.ts
│   ├── components/
│   │   ├── ui/                  # shadcn components (เราเป็นเจ้าของโค้ด)
│   │   ├── TaskCard.tsx
│   │   ├── TaskList.tsx
│   │   ├── AddTaskForm.tsx
│   │   ├── StatCard.tsx
│   │   ├── Header.tsx
│   │   ├── Clock.tsx
│   │   └── ErrorBoundary.tsx
│   ├── contexts/ThemeContext.tsx
│   ├── hooks/
│   │   ├── useLocalStorage.ts
│   │   └── useToggle.ts
│   ├── lib/api.ts
│   ├── types/task.ts
│   └── main.tsx
├── server/                      # API starter (Hono + Prisma 7)
│   ├── prisma/schema.prisma
│   └── src/routes/
├── .env.example
└── vercel.json
```

---

*คู่มือ Workshop ประกอบหลักสูตร Basic to Intermediate React.js (อัปเดต 2026)*
*บริษัท ไอทีจีเนียส เอ็นจิเนียริ่ง จำกัด — วิทยากร: อาจารย์สามิตร โกยม*
*www.itgenius.co.th | Line: @itgenius*
