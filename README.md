# Basic to Intermediate React.js (2026)

> **พัฒนาเว็บแอปพลิเคชันยุคใหม่ด้วย React 19 + TypeScript + Vite**  
> หลักสูตร 5 วัน (30 ชั่วโมง) | อัปเดตเนื้อหาปี 2026

---

## สารบัญ

- [เกี่ยวกับหลักสูตร](#เกี่ยวกับหลักสูตร)
- [Tech Stack](#tech-stack)
- [เริ่มต้นใช้งาน](#เริ่มต้นใช้งาน)
- [ภาพรวมหลักสูตร 5 วัน](#ภาพรวมหลักสูตร-5-วัน)
- [ข้อมูลติดต่อ](#ข้อมูลติดต่อ)

---

## เกี่ยวกับหลักสูตร

หลักสูตรนี้ครอบคลุมการพัฒนา Frontend ด้วย React ตั้งแต่ระดับพื้นฐานจนถึงระดับกลาง โดยใช้เครื่องมือมาตรฐานยุคใหม่ ได้แก่ **Vite** แทน Create React App และ **TypeScript** ตั้งแต่วันแรก

ผู้เรียนจะได้ฝึกปฏิบัติจริงตั้งแต่การสร้าง Component, จัดการ State และ Hooks ไปจนถึง React Router v7, State Management, TanStack Query, Authentication, Performance Optimization และการ Deploy ขึ้น Cloud จริง

| หัวข้อ | รายละเอียด |
|---|---|
| **วิทยากร** | อาจารย์สามิตร โกยม |
| **ระยะเวลา** | 5 วัน (30 ชั่วโมง) |
| **รูปแบบ** | บรรยาย + Hands-on Workshop |
| **ความรู้พื้นฐาน** | HTML, CSS, JavaScript ES6+ |

---

## Tech Stack

| เทคโนโลยี | เวอร์ชัน | บทบาท |
|---|---|---|
| **React** | 19 | UI Library หลัก |
| **TypeScript** | 5.x | ภาษาหลักตลอดคอร์ส |
| **Vite** | 6 | Build Tool & Dev Server |
| **Tailwind CSS** | v4 | Utility-First Styling |
| **React Router** | v7 | Client-Side Routing |
| **TanStack Query** | v5 | Server State Management |
| **Zustand** | latest | Lightweight Global State |
| **Redux Toolkit** | latest | Enterprise-grade State |
| **React Hook Form** | latest | Form Management |
| **Zod** | latest | Schema Validation |
| **Node.js** | LTS | Runtime |

---

## เริ่มต้นใช้งาน

### ความต้องการของระบบ

- **Node.js** LTS (แนะนำ v20+)
- **VS Code** พร้อม Extensions: ESLint, Prettier, ES7+ React Snippets
- **Google Chrome** พร้อม React Developer Tools

### สร้างโปรเจกต์ใหม่ด้วย Vite

```bash
# สร้างโปรเจกต์
npm create vite@latest my-react-app -- --template react-ts

# เข้าไปในโฟลเดอร์
cd my-react-app

# ติดตั้ง Dependencies
npm install

# รัน Dev Server
npm run dev
```

### ติดตั้ง Libraries ที่ใช้ในคอร์ส

```bash
# Tailwind CSS v4
npm install tailwindcss @tailwindcss/vite

# React Router v7
npm install react-router-dom

# TanStack Query v5
npm install @tanstack/react-query @tanstack/react-query-devtools

# Zustand
npm install zustand

# Redux Toolkit
npm install @reduxjs/toolkit react-redux

# React Hook Form + Zod
npm install react-hook-form zod @hookform/resolvers

# Axios (HTTP Client)
npm install axios
```

---

## ภาพรวมหลักสูตร 5 วัน

### วันที่ 1 — ปูพื้นฐาน React 19 + TypeScript + Vite

```
Module 1.1  รู้จัก React ในยุคปี 2026 (Virtual DOM, Reconciliation, React Compiler)
Module 1.2  เตรียมเครื่องมือสำหรับนักพัฒนา (Node.js, VS Code, React DevTools)
Module 1.3  สร้างโปรเจกต์แรกด้วย Vite + React + TypeScript
Module 1.4  TypeScript ที่จำเป็นสำหรับ React (type, interface, Generics)
Module 1.5  JSX/TSX และ Function Component แรกของคุณ
```

### วันที่ 2 — Component, Props, State, Event และ Tailwind CSS

```
Module 2.1  React Component เชิงลึก (Reusable, Composition, children)
Module 2.2  การทำงานกับ Props ด้วย TypeScript
Module 2.3  การจัดการ State ด้วย useState + Immutability
Module 2.4  การจัดการ Event (ChangeEvent, MouseEvent, Synthetic Events)
Module 2.5  Conditional Rendering และการแสดงรายการด้วย map()
Module 2.6  การจัดสไตล์ด้วย Tailwind CSS v4
```

### วันที่ 3 — React Hooks เชิงลึก และ React 19 Features

```
Module 3.1  useEffect และการจัดการ Side Effects + Cleanup
Module 3.2  useRef, useMemo, useCallback
Module 3.3  Context API (createContext, Provider, useContext)
Module 3.4  Custom Hooks (useToggle, useFetch, useLocalStorage)
Module 3.5  React 19 — use(), Actions, useOptimistic, useActionState
```

### วันที่ 4 — Routing, Forms และ State Management

```
Module 4.1  React Router v7 (Nested Routes, useParams, useNavigate, Protected Routes)
Module 4.2  React Hook Form + Zod (register, handleSubmit, zodResolver, Error Handling)
Module 4.3  State Management — เมื่อ useState ไม่เพียงพอ
Module 4.4  Zustand — Global State แบบเบาและยืดหยุ่น
Module 4.5  Redux Toolkit — createSlice, createAsyncThunk, RTK Query เบื้องต้น
```

### วันที่ 5 — Data Fetching, Auth, Performance, Deploy และ Workshop

```
Module 5.1  TanStack Query v5 (useQuery, useMutation, Caching, Pagination)
Module 5.2  REST API + JWT Authentication + Protected Routes
Module 5.3  Performance (React.memo, Code Splitting, React.lazy + Suspense)
Module 5.4  Error Boundaries และ Best Practices (Feature-based Structure, ESLint)
Module 5.5  Build และ Deploy (Vercel, Netlify, Cloudflare Pages, CI/CD)
Module 5.6  Workshop — Capstone Project (Task Manager / Mini Blog)
```

---

## ข้อมูลติดต่อ

**บริษัท ไอทีจีเนียส เอ็นจิเนียริ่ง จำกัด**  
*(IT Genius Engineering Co., Ltd.)*

| ช่องทาง | รายละเอียด |
|---|---|
| **โทรศัพท์** | 02-570-8449 |
| **มือถือ** | 088-807-9770 |
| **Line ID** | @itgenius |
| **เว็บไซต์** | [www.itgenius.co.th](https://www.itgenius.co.th) |
| **อีเมล** | contact@itgenius.co.th |

---

*หลักสูตรนี้จัดทำโดย อาจารย์สามิตร โกยม | IT Genius Engineering Co., Ltd. | อัปเดต 2026*
