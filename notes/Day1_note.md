# หลักสูตร Basic to Intermediate React.js — วันที่ 1

## ปูพื้นฐาน React 19 + TypeScript + Vite

**วันที่อบรม:** วันจันทร์ที่ 8 มิถุนายน 2569 | เวลา 09:30–16:30 น.
**สถานที่:** บริษัท อาร์เซลิก ฮิตาชิ โฮม แอพพลายแอนซ์ เซลส์ (ประเทศไทย) จำกัด
**วิทยากร:** อาจารย์สามิตร โกยม | IT Genius Engineering Co., Ltd.

---

## บทนำ

วันแรกของหลักสูตรมุ่งเน้นการสร้างรากฐานที่แข็งแกร่ง ผู้เรียนจะทำความเข้าใจว่า React คืออะไร ทำงานอย่างไร ทำไมถึงเป็นที่นิยม แล้วลงมือสร้างโปรเจกต์แรกด้วย Vite + TypeScript พร้อมเขียน Component แรกในรูปแบบมาตรฐานปี 2026

---

## Module 0: เตรียมความพร้อมก่อนเริ่มเรียน

### 0.1 เครื่องมือที่ต้องติดตั้งก่อนอบรม

| เครื่องมือ | เวอร์ชันแนะนำ | ดาวน์โหลดจาก |
|---|---|---|
| **Node.js** | LTS (20.x หรือสูงกว่า) | https://nodejs.org |
| **Visual Studio Code** | ล่าสุด | https://code.visualstudio.com |
| **Google Chrome** | ล่าสุด | https://www.google.com/chrome |
| **Git** | ล่าสุด | https://git-scm.com |

### 0.2 ตรวจสอบ Environment

เปิด Terminal หรือ Command Prompt แล้วพิมพ์คำสั่งต่อไปนี้:

```bash
node --version
# ควรแสดง v20.x.x หรือสูงกว่า

npm --version
# ควรแสดง 10.x.x หรือสูงกว่า

code --version
# ควรแสดงเวอร์ชัน VS Code
```

### 0.3 VS Code Extensions ที่แนะนำ

ติดตั้ง Extensions ต่อไปนี้ใน VS Code:

| Extension | ประโยชน์ |
|---|---|
| **ES7+ React/Redux/React-Native snippets** | Code Snippets สำหรับ React |
| **ESLint** | ตรวจสอบคุณภาพโค้ด |
| **Prettier - Code formatter** | จัดรูปแบบโค้ดอัตโนมัติ |
| **Tailwind CSS IntelliSense** | Auto-complete สำหรับ Tailwind |
| **Auto Import** | Import modules อัตโนมัติ |

---

## Module 1.1: รู้จัก React ในยุคปี 2026

### React คืออะไร?

**React** คือ JavaScript Library สำหรับสร้าง User Interface (UI) โดยทีมวิศวกรของ **Facebook (Meta)** และเป็น Open Source ตั้งแต่ปี 2013

**จุดเด่นของ React:**

- **Component-Based** — แบ่ง UI ออกเป็นชิ้นส่วนเล็ก ๆ ที่นำกลับมาใช้ซ้ำได้
- **Declarative** — บอกว่า UI ควรจะ **เป็นอย่างไร** ไม่ใช่บอกว่าต้อง **ทำอะไร** ทีละขั้น
- **Virtual DOM** — React คำนวณว่าส่วนไหนของหน้าจอเปลี่ยน แล้วอัปเดตเฉพาะส่วนนั้น

### ทำไม React ยังครองตลาดในปี 2026?

```
npm downloads per week (ข้อมูลโดยประมาณ ปี 2026):
React          →  ~30 ล้านครั้ง/สัปดาห์
Vue            →  ~4 ล้านครั้ง/สัปดาห์
Angular        →  ~3 ล้านครั้ง/สัปดาห์
Svelte         →  ~1 ล้านครั้ง/สัปดาห์
```

เว็บไซต์ระดับโลกที่พัฒนาด้วย React: **Facebook, Instagram, Netflix, Airbnb, Uber, Pinterest, Atlassian, Discord**

### Virtual DOM ทำงานอย่างไร?

```
ผู้ใช้คลิกปุ่ม "เพิ่มสินค้า"
           │
           ▼
  State เปลี่ยน (count: 0 → 1)
           │
           ▼
  React สร้าง Virtual DOM ใหม่
           │
           ▼
  React เปรียบเทียบกับ Virtual DOM เก่า (Reconciliation)
           │
           ▼
  พบว่า <span>0</span> ต้องเปลี่ยนเป็น <span>1</span>
           │
           ▼
  อัปเดตเฉพาะ <span> นั้นใน DOM จริง (Efficient Update)
```

> **Key Concept:** React ไม่ได้ render ทั้งหน้าใหม่ทุกครั้ง — มันอัปเดตเฉพาะส่วนที่เปลี่ยน ทำให้เว็บเร็ว

### Ecosystem ของ React ปี 2026

| ประเภท | เครื่องมือยอดนิยม |
|---|---|
| **Build Tool** | Vite, Turbopack |
| **Meta-Framework** | Next.js, Remix |
| **Routing** | React Router v7 |
| **State Management** | Zustand, Redux Toolkit |
| **Data Fetching** | TanStack Query (React Query) |
| **Forms** | React Hook Form + Zod |
| **Styling** | Tailwind CSS, CSS Modules |
| **Testing** | Vitest, React Testing Library |

---

## Module 1.2: เตรียมเครื่องมือสำหรับนักพัฒนา

### ทำไมจึงเลิกใช้ Create React App (CRA)?

| หัวข้อ | Create React App | Vite |
|---|---|---|
| **สถานะ** | ❌ Deprecated (เลิกพัฒนา 2023) | ✅ Active development |
| **Dev Start** | ช้า (~5-30 วินาที) | เร็วมาก (<1 วินาที) |
| **HMR** | ช้า | เร็วมาก |
| **Bundle Tool** | Webpack (เก่า) | Rollup + ESBuild (ใหม่) |
| **TypeScript** | ตั้งค่าเพิ่มเติม | ✅ รองรับทันที |
| **Config** | ซับซ้อน (eject) | ง่าย มีไฟล์ vite.config.ts |

> **สรุป:** ใน 2026 มาตรฐานอุตสาหกรรมคือ **Vite** — ทีมงาน React เองก็แนะนำให้ใช้ Vite

### ติดตั้ง React Developer Tools

1. เปิด **Google Chrome**
2. ไปที่ Chrome Web Store แล้วค้นหา **"React Developer Tools"**
3. คลิก **"Add to Chrome"**
4. เปิดเว็บที่พัฒนาด้วย React (เช่น facebook.com) แล้วกด F12 จะเห็น tab **Components** และ **Profiler** ใหม่

---

## Module 1.3: สร้างโปรเจกต์แรกด้วย Vite + React + TypeScript

### 🛠️ ขั้นตอนที่ 1: สร้างโปรเจกต์

เปิด Terminal แล้วรันคำสั่ง:

```bash
npm create vite@latest my-react-app -- --template react-ts
```

หรือสร้างแบบ Interactive (เลือกทีละขั้น):

```bash
npm create vite@latest
```

ตอบคำถามดังนี้:

```
✔ Project name: my-react-app
✔ Select a framework: › React
✔ Select a variant: › TypeScript
```

### 🛠️ ขั้นตอนที่ 2: เข้าโฟลเดอร์และติดตั้ง Dependencies

```bash
cd my-react-app
npm install
```

### 🛠️ ขั้นตอนที่ 3: รัน Development Server

```bash
npm run dev
```

เปิด Browser แล้วไปที่ **http://localhost:5173** จะเห็นหน้า React App แรกของคุณ

### โครงสร้างโปรเจกต์ Vite

```
my-react-app/
├── public/                # ไฟล์ Static (favicon, รูปภาพ ฯลฯ)
│   └── vite.svg
├── src/                   # โค้ดทั้งหมดอยู่ที่นี่
│   ├── assets/            # รูปภาพ, ไฟล์ต่าง ๆ
│   ├── App.css            # CSS สำหรับ App Component
│   ├── App.tsx            # Component หลัก (tsx = TypeScript + JSX)
│   ├── index.css          # Global CSS
│   └── main.tsx           # Entry Point — จุดเริ่มต้นของแอป
├── index.html             # HTML template หลัก
├── package.json           # Dependencies และ Scripts
├── tsconfig.json          # TypeScript Configuration
├── tsconfig.node.json     # TypeScript config สำหรับ Vite config
└── vite.config.ts         # Vite Configuration
```

### ไฟล์สำคัญที่ต้องทำความเข้าใจ

**`index.html`** — จุดเริ่มต้น Browser จะโหลดไฟล์นี้ก่อน:

```html
<!doctype html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <title>My React App</title>
  </head>
  <body>
    <div id="root"></div>          <!-- React จะ render ลงใน div นี้ -->
    <script type="module" src="/src/main.tsx"></script>
  </body>
</html>
```

**`src/main.tsx`** — Entry Point ของ React:

```tsx
import { StrictMode } from 'react'
import { createRoot } from 'react-dom/client'
import './index.css'
import App from './App.tsx'

createRoot(document.getElementById('root')!).render(
  <StrictMode>
    <App />
  </StrictMode>,
)
```

**`src/App.tsx`** — Component หลักที่ Render ลงหน้าจอ:

```tsx
import { useState } from 'react'
import reactLogo from './assets/react.svg'
import viteLogo from '/vite.svg'
import './App.css'

function App() {
  const [count, setCount] = useState(0)

  return (
    <>
      <div>
        <a href="https://vite.dev" target="_blank">
          <img src={viteLogo} className="logo" alt="Vite logo" />
        </a>
        <a href="https://react.dev" target="_blank">
          <img src={reactLogo} className="logo react" alt="React logo" />
        </a>
      </div>
      <h1>Vite + React</h1>
      <div className="card">
        <button onClick={() => setCount((count) => count + 1)}>
          count is {count}
        </button>
        <p>Edit <code>src/App.tsx</code> and save to test HMR</p>
      </div>
    </>
  )
}

export default App
```

### Hot Module Replacement (HMR) คืออะไร?

HMR คือฟีเจอร์ที่ทำให้ Browser **อัปเดตส่วนที่เปลี่ยนแปลงทันที** โดยไม่ต้อง reload หน้าใหม่ทั้งหมด

```
คุณแก้ไขโค้ดใน VS Code
        │
        ▼
Vite ตรวจพบการเปลี่ยนแปลง (ทันที)
        │
        ▼
ส่ง Module ที่เปลี่ยนไปยัง Browser (ผ่าน WebSocket)
        │
        ▼
Browser อัปเดตเฉพาะส่วนนั้น (ไม่ reload ทั้งหน้า)
        │
        ▼
State ของแอปยังคงอยู่ (ไม่ reset)
```

### ตั้งค่า vite.config.ts

```typescript
// vite.config.ts
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'

export default defineConfig({
  plugins: [react()],
  server: {
    port: 5173,    // เปลี่ยน port ได้ที่นี่
    open: true,    // เปิด Browser อัตโนมัติเมื่อ npm run dev
  },
})
```

### 🛠️ ขั้นตอนที่ 4: ตั้งค่า ESLint และ Prettier

ติดตั้ง Prettier:

```bash
npm install -D prettier eslint-config-prettier
```

สร้างไฟล์ `.prettierrc` ที่ root ของโปรเจกต์:

```json
{
  "semi": false,
  "singleQuote": true,
  "tabWidth": 2,
  "trailingComma": "es5",
  "printWidth": 100
}
```

> **อธิบาย Options:**
> - `semi: false` — ไม่ใส่ semicolon ท้ายบรรทัด
> - `singleQuote: true` — ใช้ single quote ('')
> - `tabWidth: 2` — ย่อหน้าด้วย 2 spaces
> - `trailingComma: "es5"` — ใส่ comma ตัวท้ายใน array/object

---

## Module 1.4: TypeScript ที่จำเป็นสำหรับ React

### ทำไม TypeScript?

| JavaScript | TypeScript |
|---|---|
| ไม่มี Type | มี Type ชัดเจน |
| Error รู้ตอน Runtime (บน Browser) | Error รู้ตอน Compile (ใน VS Code ทันที) |
| ไม่มี Autocomplete | มี Autocomplete เต็มรูปแบบ |
| Bug ซ่อนอยู่ง่าย | Bug โผล่ให้เห็นตั้งแต่เขียนโค้ด |

### Type พื้นฐาน

```typescript
// ชนิดข้อมูลพื้นฐาน
const name: string = 'สมชาย'
const age: number = 30
const isActive: boolean = true
const nothing: null = null
const notDefined: undefined = undefined

// Array
const fruits: string[] = ['apple', 'banana', 'mango']
const scores: number[] = [90, 85, 92]

// Object
const person: { name: string; age: number } = {
  name: 'สมหญิง',
  age: 25,
}
```

### `type` และ `interface`

```typescript
// type — ใช้สำหรับ Union Types และ Aliases
type Status = 'active' | 'inactive' | 'pending'
type ID = string | number

// interface — ใช้สำหรับ Object Shape
interface User {
  id: number
  name: string
  email: string
  age?: number       // ? หมายถึง Optional (อาจมีหรือไม่มีก็ได้)
}

// ใช้งาน
const currentUser: User = {
  id: 1,
  name: 'สมชาย',
  email: 'somchai@example.com',
  // age ไม่ต้องใส่ก็ได้เพราะเป็น Optional
}
```

> **เมื่อไรใช้อะไร?**
> - ใช้ `interface` สำหรับ Object ที่อาจต้องการ extend ในอนาคต
> - ใช้ `type` สำหรับ Union Types หรือ Aliases ที่ซับซ้อน
> - ในทางปฏิบัติ ใช้ได้ทั้งคู่สำหรับ Props ของ React Component

### Function Types

```typescript
// ฟังก์ชันธรรมดา
function greet(name: string): string {
  return `สวัสดีคุณ ${name}`
}

// Arrow Function
const add = (a: number, b: number): number => a + b

// ฟังก์ชันที่ไม่ return ค่า (void)
const logMessage = (message: string): void => {
  console.log(message)
}

// ฟังก์ชันที่รับ Callback
const processData = (data: string[], callback: (item: string) => void): void => {
  data.forEach(callback)
}
```

### Generics เบื้องต้น

```typescript
// Generic Function — ทำงานได้กับ Type หลายแบบ
function getFirstItem<T>(arr: T[]): T {
  return arr[0]
}

const firstNumber = getFirstItem<number>([1, 2, 3])     // → 1
const firstName = getFirstItem<string>(['a', 'b', 'c']) // → 'a'

// ตัวอย่างที่ใช้บ่อยใน React
interface ApiResponse<T> {
  data: T
  status: number
  message: string
}

interface Product {
  id: number
  name: string
  price: number
}

// ใช้งาน
const response: ApiResponse<Product[]> = {
  data: [{ id: 1, name: 'สินค้า A', price: 100 }],
  status: 200,
  message: 'success',
}
```

### Union Types และ Literal Types

```typescript
// Union Types
type Direction = 'north' | 'south' | 'east' | 'west'
type StringOrNumber = string | number

// ใช้งานใน React
type ButtonVariant = 'primary' | 'secondary' | 'danger'
type ButtonSize = 'sm' | 'md' | 'lg'

interface ButtonProps {
  variant: ButtonVariant
  size: ButtonSize
  disabled?: boolean
}
```

### Type Inference

TypeScript สามารถ **เดาชนิดข้อมูลได้เอง** จากค่าที่ใส่:

```typescript
// TypeScript รู้ว่า count เป็น number (ไม่ต้องระบุ)
const count = 42

// TypeScript รู้ว่า name เป็น string
const name = 'React'

// TypeScript รู้ว่า isOpen เป็น boolean
const isOpen = false

// ในกรณีที่ TypeScript เดาผิด ค่อยระบุ Type เอง
const items: string[] = []  // ถ้าไม่ระบุ TypeScript จะเดาเป็น never[]
```

---

## Module 1.5: JSX/TSX และ Component แรก

### JSX/TSX คืออะไร?

**JSX** (JavaScript XML) คือ Syntax Extension ที่ให้เขียน HTML-like code ใน JavaScript
**TSX** = TypeScript + JSX

```tsx
// JSX/TSX ที่เราเขียน
const element = <h1 className="title">สวัสดี React!</h1>

// JavaScript จริง ๆ ที่ถูก Compile มา (ไม่ต้องจำ แค่รู้ว่า JSX แปลงเป็นนี้)
const element = React.createElement('h1', { className: 'title' }, 'สวัสดี React!')
```

### กฎสำคัญของ JSX/TSX

```tsx
// ✅ ต้องมี Root Element เดียว (ใช้ Fragment ถ้าไม่อยากมี div ซ้อน)
return (
  <>
    <h1>หัวข้อ</h1>
    <p>เนื้อหา</p>
  </>
)

// ❌ ผิด — มี Root มากกว่า 1 ตัว
return (
  <h1>หัวข้อ</h1>
  <p>เนื้อหา</p>    // Error!
)

// ✅ ปิด Tag ทุกตัว (รวมถึง self-closing tags)
return (
  <div>
    <input type="text" />    // ✅ ปิดด้วย /
    <img src="logo.png" />   // ✅
    <br />                   // ✅
  </div>
)

// ✅ ใช้ className แทน class
return <div className="container">...</div>

// ✅ ใช้ {} สำหรับ JavaScript Expression
const name = 'สมชาย'
return <h1>สวัสดีคุณ {name}</h1>

// ✅ ใช้ camelCase สำหรับ Attributes
return <div onClick={handleClick} style={{ backgroundColor: 'red' }}>...</div>
```

### การแสดงค่าตัวแปรใน JSX

```tsx
function Greeting() {
  const name = 'สมชาย'
  const score = 95.5
  const isAdmin = true
  const today = new Date()

  return (
    <div>
      <h1>ยินดีต้อนรับ {name}</h1>
      <p>คะแนน: {score}</p>
      <p>วันที่: {today.toLocaleDateString('th-TH')}</p>
      <p>สิทธิ์: {isAdmin ? 'ผู้ดูแลระบบ' : 'ผู้ใช้ทั่วไป'}</p>
      <p>2 + 2 = {2 + 2}</p>
    </div>
  )
}
```

### 🛠️ สร้าง Function Component ตัวแรก

สร้างไฟล์ `src/components/Welcome.tsx`:

```tsx
// src/components/Welcome.tsx

// 1. กำหนด Type ของ Props ด้วย interface
interface WelcomeProps {
  name: string
  company: string
}

// 2. สร้าง Function Component
function Welcome({ name, company }: WelcomeProps) {
  return (
    <div style={{ padding: '20px', backgroundColor: '#f0f8ff', borderRadius: '8px' }}>
      <h2>🎉 ยินดีต้อนรับสู่การอบรม React!</h2>
      <p>ชื่อ: <strong>{name}</strong></p>
      <p>บริษัท: <strong>{company}</strong></p>
      <p>วันนี้คือ: {new Date().toLocaleDateString('th-TH', {
        year: 'numeric',
        month: 'long',
        day: 'numeric',
      })}</p>
    </div>
  )
}

// 3. Export Component เพื่อให้ไฟล์อื่น import ได้
export default Welcome
```

แก้ไข `src/App.tsx` เพื่อใช้งาน Component:

```tsx
// src/App.tsx
import Welcome from './components/Welcome'

function App() {
  return (
    <div>
      <Welcome name="สมชาย" company="อาร์เซลิก ฮิตาชิ" />
    </div>
  )
}

export default App
```

### การ export/import แบบต่าง ๆ

```tsx
// ─── Default Export ───
// ส่งออกแค่ 1 ตัว ตั้งชื่อตอน import ได้
export default function Button() { ... }

// Import
import Button from './components/Button'           // ตั้งชื่อเองได้
import MyButton from './components/Button'         // ✅ ก็ได้

// ─── Named Export ───
// ส่งออกได้หลายตัว ต้องใช้ชื่อเดิมตอน import
export function Card() { ... }
export function CardHeader() { ... }
export function CardBody() { ... }

// Import
import { Card, CardHeader, CardBody } from './components/Card'
import { Card as MyCard } from './components/Card'  // alias ก็ได้
```

### React Fragment

```tsx
// ❌ ต้องมี div ห่อ ทำให้ DOM เยอะขึ้น
function List() {
  return (
    <div>
      <li>Item 1</li>
      <li>Item 2</li>
    </div>
  )
}

// ✅ ใช้ Fragment — ไม่สร้าง DOM Node เพิ่ม
function List() {
  return (
    <>
      <li>Item 1</li>
      <li>Item 2</li>
    </>
  )
}

// หรือใช้แบบ explicit (ต้องใช้เมื่อต้องการ key prop)
import { Fragment } from 'react'

function List({ items }: { items: string[] }) {
  return (
    <>
      {items.map((item, index) => (
        <Fragment key={index}>
          <dt>{item}</dt>
          <dd>รายละเอียด</dd>
        </Fragment>
      ))}
    </>
  )
}
```

---

## 🎯 Workshop ท้ายวัน: สร้างหน้า "บัตรพนักงาน"

### โจทย์

สร้างโปรเจกต์ React ที่แสดง **บัตรพนักงาน** โดยใช้ความรู้ที่เรียนวันนี้:

### ขั้นตอนที่ 1: สร้างโปรเจกต์ใหม่

```bash
npm create vite@latest employee-card -- --template react-ts
cd employee-card
npm install
```

### ขั้นตอนที่ 2: สร้าง Type

สร้างไฟล์ `src/types/employee.ts`:

```typescript
// src/types/employee.ts
export interface Employee {
  id: number
  firstName: string
  lastName: string
  position: string
  department: string
  email: string
  phone: string
  startDate: string
  isActive: boolean
}
```

### ขั้นตอนที่ 3: สร้าง EmployeeCard Component

สร้างไฟล์ `src/components/EmployeeCard.tsx`:

```tsx
// src/components/EmployeeCard.tsx
import type { Employee } from '../types/employee'

interface EmployeeCardProps {
  employee: Employee
}

function EmployeeCard({ employee }: EmployeeCardProps) {
  const fullName = `${employee.firstName} ${employee.lastName}`
  const statusText = employee.isActive ? '✅ ทำงานอยู่' : '❌ ลาออกแล้ว'
  const statusColor = employee.isActive ? '#22c55e' : '#ef4444'

  return (
    <div
      style={{
        border: '1px solid #e5e7eb',
        borderRadius: '12px',
        padding: '24px',
        maxWidth: '400px',
        boxShadow: '0 2px 8px rgba(0,0,0,0.1)',
        backgroundColor: '#ffffff',
        fontFamily: 'sans-serif',
      }}
    >
      {/* Avatar */}
      <div
        style={{
          width: '80px',
          height: '80px',
          borderRadius: '50%',
          backgroundColor: '#3b82f6',
          display: 'flex',
          alignItems: 'center',
          justifyContent: 'center',
          fontSize: '32px',
          marginBottom: '16px',
        }}
      >
        👤
      </div>

      {/* ชื่อและตำแหน่ง */}
      <h2 style={{ margin: '0 0 4px 0', fontSize: '20px' }}>{fullName}</h2>
      <p style={{ margin: '0 0 16px 0', color: '#6b7280', fontSize: '14px' }}>
        {employee.position}
      </p>

      {/* สถานะ */}
      <span
        style={{
          color: statusColor,
          fontWeight: 'bold',
          fontSize: '14px',
          display: 'block',
          marginBottom: '16px',
        }}
      >
        {statusText}
      </span>

      {/* ข้อมูลติดต่อ */}
      <div style={{ borderTop: '1px solid #e5e7eb', paddingTop: '16px' }}>
        <p style={{ margin: '4px 0', fontSize: '14px' }}>
          🏢 <strong>แผนก:</strong> {employee.department}
        </p>
        <p style={{ margin: '4px 0', fontSize: '14px' }}>
          📧 <strong>อีเมล:</strong> {employee.email}
        </p>
        <p style={{ margin: '4px 0', fontSize: '14px' }}>
          📱 <strong>โทร:</strong> {employee.phone}
        </p>
        <p style={{ margin: '4px 0', fontSize: '14px' }}>
          📅 <strong>เริ่มงาน:</strong>{' '}
          {new Date(employee.startDate).toLocaleDateString('th-TH', {
            year: 'numeric',
            month: 'long',
            day: 'numeric',
          })}
        </p>
      </div>
    </div>
  )
}

export default EmployeeCard
```

### ขั้นตอนที่ 4: สร้างข้อมูลและใช้ใน App

แก้ไข `src/App.tsx`:

```tsx
// src/App.tsx
import EmployeeCard from './components/EmployeeCard'
import type { Employee } from './types/employee'

const employees: Employee[] = [
  {
    id: 1,
    firstName: 'สมชาย',
    lastName: 'ใจดี',
    position: 'Senior Software Engineer',
    department: 'IT Department',
    email: 'somchai@arcelik.com',
    phone: '081-234-5678',
    startDate: '2020-03-01',
    isActive: true,
  },
  {
    id: 2,
    firstName: 'สมหญิง',
    lastName: 'มีสุข',
    position: 'HR Manager',
    department: 'Human Resources',
    email: 'somying@arcelik.com',
    phone: '082-345-6789',
    startDate: '2019-06-15',
    isActive: true,
  },
  {
    id: 3,
    firstName: 'วิชัย',
    lastName: 'รักการเรียน',
    position: 'Marketing Specialist',
    department: 'Marketing',
    email: 'wichai@arcelik.com',
    phone: '083-456-7890',
    startDate: '2021-01-10',
    isActive: false,
  },
]

function App() {
  return (
    <div
      style={{
        minHeight: '100vh',
        backgroundColor: '#f9fafb',
        padding: '40px 20px',
      }}
    >
      <h1 style={{ textAlign: 'center', marginBottom: '32px' }}>
        👥 บัตรพนักงาน — Arcelik Hitachi
      </h1>
      <div
        style={{
          display: 'flex',
          flexWrap: 'wrap',
          gap: '24px',
          justifyContent: 'center',
        }}
      >
        {employees.map((emp) => (
          <EmployeeCard key={emp.id} employee={emp} />
        ))}
      </div>
    </div>
  )
}

export default App
```

### ขั้นตอนที่ 5: รันและดูผลลัพธ์

```bash
npm run dev
```

เปิด http://localhost:5173 ดูบัตรพนักงาน 3 คน

---

## สรุปวันที่ 1

วันนี้เราได้เรียนรู้และลงมือทำ:

- ✅ ทำความเข้าใจ React คืออะไร และทำงานอย่างไร (Virtual DOM, Reconciliation)
- ✅ เปรียบเทียบ Vite กับ Create React App และเข้าใจว่าทำไม Vite ถึงเป็นมาตรฐาน
- ✅ สร้างโปรเจกต์ด้วย Vite + React + TypeScript ครั้งแรก
- ✅ ทำความเข้าใจโครงสร้างโปรเจกต์ทุกไฟล์
- ✅ เรียนรู้ TypeScript ที่จำเป็น — Types, Interface, Generics, Union Types
- ✅ เขียน JSX/TSX และสร้าง Function Component แรก
- ✅ ฝึกการ export/import Component ระหว่างไฟล์
- ✅ ทำ Workshop บัตรพนักงานด้วยตัวเอง

**พรุ่งนี้ (วันที่ 2):** เราจะเจาะลึก Component, Props, State, Events และเพิ่มความสวยงามด้วย Tailwind CSS v4

---

## แหล่งอ้างอิงเพิ่มเติม

- [React Official Docs](https://react.dev) — เอกสารทางการของ React (ภาษาอังกฤษ)
- [Vite Docs](https://vitejs.dev) — เอกสาร Vite
- [TypeScript Handbook](https://www.typescriptlang.org/docs/) — เอกสาร TypeScript อย่างเป็นทางการ
- [TypeScript Playground](https://www.typescriptlang.org/play) — ทดสอบ TypeScript Online
