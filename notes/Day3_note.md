# หลักสูตร Basic to Intermediate React.js — วันที่ 3

## React Hooks เชิงลึก, Side Effects, Custom Hooks และฟีเจอร์ใหม่ของ React 19

**วันที่อบรม:** วันพุธที่ 10 มิถุนายน 2569 | เวลา 09:30–16:30 น.
**สถานที่:** บริษัท อาร์เซลิก ฮิตาชิ โฮม แอพพลายแอนซ์ เซลส์ (ประเทศไทย) จำกัด
**วิทยากร:** อาจารย์สามิตร โกยม | IT Genius Engineering Co., Ltd.

---

## บทนำ

วันที่สามจะพาเจาะลึก Hooks สำคัญที่นอกเหนือจาก useState ได้แก่ `useEffect`, `useRef`, `useMemo`, `useCallback` รวมถึงการแก้ปัญหา Prop Drilling ด้วย Context API, การสร้าง Custom Hooks และสุดท้ายคือฟีเจอร์ใหม่สุดของ **React 19**

---

## Module 3.1: useEffect และการจัดการ Side Effects

### Side Effects คืออะไร?

**Side Effects** คือสิ่งที่ Component ทำ **นอกเหนือจากการ Render UI** เช่น:

- เรียก API (Fetch Data)
- ตั้ง Timer (setInterval, setTimeout)
- Subscribe Event Listener
- เชื่อมต่อ WebSocket
- เปลี่ยน document.title

```
Pure Function (ไม่มี Side Effect):
Input → [Function] → Output
          ↑
     ไม่ยุ่งกับโลกภายนอก

Function with Side Effect:
Input → [Function] → Output
          ↓
     ยุ่งกับโลกภายนอก (API, DOM, Timer...)
```

### useEffect Syntax

```tsx
import { useEffect } from 'react'

useEffect(
  () => {
    // ─── Effect Function: ทำงานหลัง Render ───
    // ทำ Side Effect ที่นี่

    return () => {
      // ─── Cleanup Function (Optional) ───
      // ทำความสะอาดเมื่อ Component ออกจากหน้าจอ
      // หรือก่อน Effect ทำงานครั้งต่อไป
    }
  },
  [dependency1, dependency2]  // Dependency Array
)
```

### Dependency Array ควบคุมการทำงาน

```tsx
// ─── ไม่มี Dependency Array: ทำงานทุกครั้งที่ Render (ระวัง!) ───
useEffect(() => {
  console.log('ทำงานทุกครั้งที่ Render')
})

// ─── Array ว่าง []: ทำงานครั้งเดียวหลัง Mount (เหมือน componentDidMount) ───
useEffect(() => {
  console.log('ทำงานครั้งเดียวตอนเริ่มต้น')
}, [])

// ─── มี Dependencies: ทำงานทุกครั้งที่ค่าใน Array เปลี่ยน ───
useEffect(() => {
  console.log('userId เปลี่ยน: ดึงข้อมูลใหม่')
}, [userId])
```

### 🛠️ ตัวอย่างที่ 1: เรียก API ด้วย useEffect

```tsx
import { useState, useEffect } from 'react'

interface Post {
  id: number
  title: string
  body: string
}

function PostList() {
  const [posts, setPosts] = useState<Post[]>([])
  const [isLoading, setIsLoading] = useState(true)
  const [error, setError] = useState<string | null>(null)

  useEffect(() => {
    // ─── สร้าง AbortController เพื่อยกเลิก Request เมื่อ Cleanup ───
    const abortController = new AbortController()

    const fetchPosts = async () => {
      try {
        setIsLoading(true)
        setError(null)

        const response = await fetch('https://jsonplaceholder.typicode.com/posts?_limit=5', {
          signal: abortController.signal,
        })

        if (!response.ok) {
          throw new Error(`HTTP Error: ${response.status}`)
        }

        const data: Post[] = await response.json()
        setPosts(data)
      } catch (err) {
        if (err instanceof Error && err.name !== 'AbortError') {
          setError(err.message)
        }
      } finally {
        setIsLoading(false)
      }
    }

    fetchPosts()

    // ─── Cleanup: ยกเลิก Request เมื่อ Component ออกจากหน้าจอ ───
    return () => {
      abortController.abort()
    }
  }, [])  // [] = โหลดครั้งเดียวตอนเริ่ม

  if (isLoading) return <p>⏳ กำลังโหลด...</p>
  if (error) return <p>❌ เกิดข้อผิดพลาด: {error}</p>

  return (
    <ul>
      {posts.map(post => (
        <li key={post.id}>
          <strong>{post.title}</strong>
          <p>{post.body}</p>
        </li>
      ))}
    </ul>
  )
}
```

### 🛠️ ตัวอย่างที่ 2: โหลดข้อมูลเมื่อ userId เปลี่ยน

```tsx
function UserProfile({ userId }: { userId: number }) {
  const [user, setUser] = useState<any>(null)
  const [isLoading, setIsLoading] = useState(false)

  useEffect(() => {
    const controller = new AbortController()

    const loadUser = async () => {
      setIsLoading(true)
      const res = await fetch(`https://jsonplaceholder.typicode.com/users/${userId}`, {
        signal: controller.signal,
      })
      const data = await res.json()
      setUser(data)
      setIsLoading(false)
    }

    loadUser()

    return () => controller.abort()
  }, [userId])   // ทุกครั้งที่ userId เปลี่ยน จะโหลดข้อมูลใหม่

  if (isLoading) return <p>กำลังโหลดข้อมูลผู้ใช้ {userId}...</p>
  if (!user) return null

  return (
    <div>
      <h2>{user.name}</h2>
      <p>{user.email}</p>
    </div>
  )
}
```

### 🛠️ ตัวอย่างที่ 3: Cleanup — setInterval

```tsx
function Stopwatch() {
  const [seconds, setSeconds] = useState(0)
  const [isRunning, setIsRunning] = useState(false)

  useEffect(() => {
    if (!isRunning) return   // ถ้าหยุด ไม่ต้องสร้าง Interval

    const intervalId = setInterval(() => {
      setSeconds(prev => prev + 1)   // ใช้ prev เพื่อความปลอดภัย
    }, 1000)

    // ─── Cleanup: ล้าง Interval ก่อน Effect ทำงานครั้งต่อไป ───
    return () => clearInterval(intervalId)

  }, [isRunning])   // ทำงานใหม่เมื่อ isRunning เปลี่ยน

  return (
    <div>
      <h2>⏱️ {seconds} วินาที</h2>
      <button onClick={() => setIsRunning(true)}>เริ่ม</button>
      <button onClick={() => setIsRunning(false)}>หยุด</button>
      <button onClick={() => { setIsRunning(false); setSeconds(0) }}>รีเซ็ต</button>
    </div>
  )
}
```

### ข้อผิดพลาดที่พบบ่อย: Infinite Loop

```tsx
// ❌ Infinite Loop! — setData ทำให้ Re-render → useEffect ทำงานใหม่ → setData → loop
const [data, setData] = useState(null)

useEffect(() => {
  fetch('/api/data')
    .then(res => res.json())
    .then(result => setData(result))
})   // ❌ ไม่มี Dependency Array

// ✅ แก้ด้วยการใส่ []
useEffect(() => {
  fetch('/api/data')
    .then(res => res.json())
    .then(result => setData(result))
}, [])   // ✅ โหลดครั้งเดียว
```

---

## Module 3.2: Hooks สำคัญอื่น ๆ

### useRef

`useRef` ใช้ได้ 2 กรณี:

1. **เข้าถึง DOM Element** โดยตรง
2. **เก็บค่าที่ไม่ทำให้ Re-render**

```tsx
import { useRef, useEffect, useState } from 'react'

function InputFocusDemo() {
  // ─── Case 1: เข้าถึง DOM ───
  const inputRef = useRef<HTMLInputElement>(null)

  const focusInput = () => {
    inputRef.current?.focus()   // เรียก .focus() บน DOM Element โดยตรง
  }

  // ─── Case 2: เก็บค่าโดยไม่ Re-render ───
  const renderCount = useRef(0)

  useEffect(() => {
    renderCount.current += 1   // ไม่ทำให้ Re-render!
    console.log(`Render ครั้งที่: ${renderCount.current}`)
  })

  // ─── Case 3: เก็บ Timer ID ───
  const timerRef = useRef<number | null>(null)

  const startTimer = () => {
    timerRef.current = setInterval(() => console.log('tick'), 1000)
  }

  const stopTimer = () => {
    if (timerRef.current) {
      clearInterval(timerRef.current)
      timerRef.current = null
    }
  }

  return (
    <div>
      <input ref={inputRef} placeholder="คลิกปุ่มด้านล่างเพื่อ Focus" />
      <button onClick={focusInput}>Focus Input</button>
    </div>
  )
}
```

### useMemo — Memoize ผลลัพธ์ที่คำนวณ

```tsx
import { useState, useMemo } from 'react'

interface Product {
  id: number
  name: string
  price: number
  category: string
}

function ExpensiveList({ products, filter }: { products: Product[]; filter: string }) {
  // ─── ❌ คำนวณทุกครั้งที่ Component Re-render (แม้ products ไม่เปลี่ยน) ───
  // const filteredProducts = products.filter(p => p.category === filter)

  // ─── ✅ คำนวณเฉพาะเมื่อ products หรือ filter เปลี่ยน ───
  const filteredProducts = useMemo(() => {
    console.log('กำลัง Filter...')   // ดู Console: ทำงานแค่เมื่อจำเป็น
    return products
      .filter(p => p.category === filter)
      .sort((a, b) => a.price - b.price)
  }, [products, filter])

  return (
    <ul>
      {filteredProducts.map(p => (
        <li key={p.id}>{p.name} — ฿{p.price.toLocaleString()}</li>
      ))}
    </ul>
  )
}
```

> **เมื่อไรควรใช้ useMemo?**
> - คำนวณที่ **ใช้เวลานาน** (Filter/Sort รายการเยอะ, คำนวณซับซ้อน)
> - ส่ง Object/Array เป็น Props ไปยัง Component ที่ใช้ `React.memo`
> - **ไม่ควรใช้:** กับการคำนวณง่าย ๆ เพราะ useMemo เองก็มี overhead

### useCallback — Memoize ฟังก์ชัน

```tsx
import { useState, useCallback, memo } from 'react'

// ─── Child Component ที่ใช้ React.memo ───
const Button = memo(({ onClick, label }: { onClick: () => void; label: string }) => {
  console.log(`Button "${label}" Rendered`)
  return <button onClick={onClick}>{label}</button>
})

function ParentComponent() {
  const [count, setCount] = useState(0)
  const [name, setName] = useState('')

  // ─── ❌ ทุกครั้งที่ name เปลี่ยน → สร้างฟังก์ชันใหม่ → Button Re-render ───
  // const handleIncrement = () => setCount(prev => prev + 1)

  // ─── ✅ ฟังก์ชันเดิม ไม่ Re-create เมื่อ name เปลี่ยน ───
  const handleIncrement = useCallback(() => {
    setCount(prev => prev + 1)
  }, [])   // ไม่มี dependencies → สร้างครั้งเดียว

  return (
    <div>
      <input value={name} onChange={e => setName(e.target.value)} placeholder="พิมพ์ชื่อ" />
      <p>Count: {count}</p>
      <Button onClick={handleIncrement} label="เพิ่ม" />
    </div>
  )
}
```

### React.memo — ป้องกัน Component Re-render โดยไม่จำเป็น

```tsx
import { memo } from 'react'

interface ProductCardProps {
  name: string
  price: number
}

// ─── Component นี้จะ Re-render เฉพาะเมื่อ name หรือ price เปลี่ยน ───
const ProductCard = memo(function ProductCard({ name, price }: ProductCardProps) {
  console.log(`ProductCard "${name}" Rendered`)
  return (
    <div>
      <h3>{name}</h3>
      <p>฿{price.toLocaleString()}</p>
    </div>
  )
})

export default ProductCard
```

> **สรุป: React Compiler ในปี 2026**
> React 19 มา **React Compiler** (ชื่อเดิม: React Forget) ที่ช่วย Memoize อัตโนมัติ ทำให้ในอนาคตไม่ต้องใช้ `useMemo`, `useCallback`, `React.memo` เองเกือบทั้งหมด แต่เรายังต้องรู้แนวคิดเพื่อ Debug และเข้าใจว่า Compiler ทำอะไร

---

## Module 3.3: Context API

### ปัญหา Prop Drilling

```
App (State: theme = "dark")
 └── Layout
      └── Sidebar
           └── NavMenu
                └── NavItem  ← ต้องการใช้ theme
```

```tsx
// ❌ ต้องส่ง theme ผ่านทุก Component
<App theme="dark">
  <Layout theme="dark">
    <Sidebar theme="dark">
      <NavMenu theme="dark">
        <NavItem theme="dark" />   {/* เป้าหมายจริง ๆ */}
      </NavMenu>
    </Sidebar>
  </Layout>
</App>
```

### แก้ด้วย Context API

**ขั้นตอนที่ 1: สร้าง Context**

```tsx
// src/contexts/ThemeContext.tsx
import { createContext, useContext, useState } from 'react'

// ─── 1. กำหนด Type ───
interface ThemeContextType {
  theme: 'light' | 'dark'
  toggleTheme: () => void
}

// ─── 2. สร้าง Context ───
const ThemeContext = createContext<ThemeContextType | null>(null)

// ─── 3. สร้าง Custom Hook สำหรับใช้ Context ───
export function useTheme() {
  const context = useContext(ThemeContext)
  if (!context) {
    throw new Error('useTheme ต้องใช้ภายใน ThemeProvider')
  }
  return context
}

// ─── 4. สร้าง Provider Component ───
export function ThemeProvider({ children }: { children: React.ReactNode }) {
  const [theme, setTheme] = useState<'light' | 'dark'>('light')

  const toggleTheme = () => {
    setTheme(prev => prev === 'light' ? 'dark' : 'light')
  }

  return (
    <ThemeContext.Provider value={{ theme, toggleTheme }}>
      {children}
    </ThemeContext.Provider>
  )
}
```

**ขั้นตอนที่ 2: ครอบแอปด้วย Provider**

```tsx
// src/main.tsx
import { StrictMode } from 'react'
import { createRoot } from 'react-dom/client'
import { ThemeProvider } from './contexts/ThemeContext'
import App from './App'
import './index.css'

createRoot(document.getElementById('root')!).render(
  <StrictMode>
    <ThemeProvider>
      <App />
    </ThemeProvider>
  </StrictMode>,
)
```

**ขั้นตอนที่ 3: ใช้ Context ในทุกที่**

```tsx
// NavItem.tsx — ไม่ต้องรับ Props theme อีกต่อไป!
import { useTheme } from '../contexts/ThemeContext'

function NavItem({ label }: { label: string }) {
  const { theme } = useTheme()   // ดึงค่าจาก Context

  return (
    <div className={theme === 'dark' ? 'text-white' : 'text-gray-900'}>
      {label}
    </div>
  )
}

// ThemeToggleButton.tsx
import { useTheme } from '../contexts/ThemeContext'

function ThemeToggleButton() {
  const { theme, toggleTheme } = useTheme()

  return (
    <button onClick={toggleTheme}>
      {theme === 'dark' ? '☀️ Light Mode' : '🌙 Dark Mode'}
    </button>
  )
}
```

### 🛠️ ตัวอย่าง: Auth Context

```tsx
// src/contexts/AuthContext.tsx
import { createContext, useContext, useState } from 'react'

interface User {
  id: number
  name: string
  email: string
  role: 'admin' | 'user'
}

interface AuthContextType {
  user: User | null
  isAuthenticated: boolean
  login: (email: string, password: string) => Promise<void>
  logout: () => void
}

const AuthContext = createContext<AuthContextType | null>(null)

export function useAuth() {
  const context = useContext(AuthContext)
  if (!context) throw new Error('useAuth ต้องใช้ภายใน AuthProvider')
  return context
}

export function AuthProvider({ children }: { children: React.ReactNode }) {
  const [user, setUser] = useState<User | null>(null)

  const login = async (email: string, _password: string) => {
    // จำลอง API Call
    await new Promise(resolve => setTimeout(resolve, 500))
    setUser({
      id: 1,
      name: 'สมชาย ใจดี',
      email,
      role: email.includes('admin') ? 'admin' : 'user',
    })
  }

  const logout = () => setUser(null)

  return (
    <AuthContext.Provider value={{
      user,
      isAuthenticated: user !== null,
      login,
      logout,
    }}>
      {children}
    </AuthContext.Provider>
  )
}
```

---

## Module 3.4: การสร้าง Custom Hooks

### Custom Hooks คืออะไร?

**Custom Hook** คือ JavaScript Function ที่:
- ชื่อขึ้นต้นด้วย **`use`** (กฎ!)
- สามารถเรียกใช้ Hooks อื่น ๆ ได้ภายใน
- ใช้เพื่อ **แยก Logic ออกจาก UI** และนำกลับมาใช้ซ้ำ

### 🛠️ Custom Hook 1: useToggle

```tsx
// src/hooks/useToggle.ts
import { useState, useCallback } from 'react'

function useToggle(initialValue = false) {
  const [value, setValue] = useState(initialValue)

  const toggle = useCallback(() => setValue(prev => !prev), [])
  const setTrue = useCallback(() => setValue(true), [])
  const setFalse = useCallback(() => setValue(false), [])

  return { value, toggle, setTrue, setFalse }
}

export default useToggle

// ─── ใช้งาน ───
function Modal() {
  const { value: isOpen, toggle, setFalse: closeModal } = useToggle()

  return (
    <>
      <button onClick={toggle}>เปิด Modal</button>
      {isOpen && (
        <div className="modal">
          <p>เนื้อหา Modal</p>
          <button onClick={closeModal}>ปิด</button>
        </div>
      )}
    </>
  )
}
```

### 🛠️ Custom Hook 2: useFetch

```tsx
// src/hooks/useFetch.ts
import { useState, useEffect } from 'react'

interface FetchState<T> {
  data: T | null
  isLoading: boolean
  error: string | null
  refetch: () => void
}

function useFetch<T>(url: string): FetchState<T> {
  const [data, setData] = useState<T | null>(null)
  const [isLoading, setIsLoading] = useState(true)
  const [error, setError] = useState<string | null>(null)
  const [refetchTrigger, setRefetchTrigger] = useState(0)

  useEffect(() => {
    const controller = new AbortController()

    const fetchData = async () => {
      try {
        setIsLoading(true)
        setError(null)
        const response = await fetch(url, { signal: controller.signal })
        if (!response.ok) throw new Error(`HTTP Error ${response.status}`)
        const result: T = await response.json()
        setData(result)
      } catch (err) {
        if (err instanceof Error && err.name !== 'AbortError') {
          setError(err.message)
        }
      } finally {
        setIsLoading(false)
      }
    }

    fetchData()
    return () => controller.abort()
  }, [url, refetchTrigger])

  const refetch = () => setRefetchTrigger(prev => prev + 1)

  return { data, isLoading, error, refetch }
}

export default useFetch

// ─── ใช้งาน ───
interface Post { id: number; title: string; body: string }

function PostsPage() {
  const { data: posts, isLoading, error, refetch } = useFetch<Post[]>(
    'https://jsonplaceholder.typicode.com/posts?_limit=3'
  )

  if (isLoading) return <p>⏳ กำลังโหลด...</p>
  if (error) return <button onClick={refetch}>❌ เกิดข้อผิดพลาด — คลิกเพื่อลองใหม่</button>

  return (
    <div>
      {posts?.map(post => (
        <div key={post.id}>
          <h3>{post.title}</h3>
          <p>{post.body}</p>
        </div>
      ))}
    </div>
  )
}
```

### 🛠️ Custom Hook 3: useLocalStorage

```tsx
// src/hooks/useLocalStorage.ts
import { useState } from 'react'

function useLocalStorage<T>(key: string, initialValue: T) {
  // ─── อ่านค่าจาก localStorage (ถ้ามี) หรือใช้ค่าเริ่มต้น ───
  const [storedValue, setStoredValue] = useState<T>(() => {
    try {
      const item = localStorage.getItem(key)
      return item ? JSON.parse(item) : initialValue
    } catch {
      return initialValue
    }
  })

  // ─── Setter ที่บันทึกลง localStorage ด้วย ───
  const setValue = (value: T | ((prev: T) => T)) => {
    try {
      const valueToStore = value instanceof Function ? value(storedValue) : value
      setStoredValue(valueToStore)
      localStorage.setItem(key, JSON.stringify(valueToStore))
    } catch (err) {
      console.error('useLocalStorage error:', err)
    }
  }

  return [storedValue, setValue] as const
}

export default useLocalStorage

// ─── ใช้งาน ───
function Settings() {
  const [theme, setTheme] = useLocalStorage<'light' | 'dark'>('theme', 'light')
  const [language, setLanguage] = useLocalStorage<string>('language', 'th')

  return (
    <div>
      <button onClick={() => setTheme(t => t === 'light' ? 'dark' : 'light')}>
        Theme: {theme}
      </button>
      <select value={language} onChange={e => setLanguage(e.target.value)}>
        <option value="th">ภาษาไทย</option>
        <option value="en">English</option>
      </select>
    </div>
  )
}
```

### กฎการใช้ Hooks (Rules of Hooks)

```tsx
// ✅ ถูก — เรียก Hook ที่ Top Level ของ Function
function MyComponent() {
  const [count, setCount] = useState(0)      // ✅
  const { theme } = useTheme()               // ✅
  useEffect(() => { ... }, [])               // ✅
  return <div>{count}</div>
}

// ❌ ผิด — เรียก Hook ใน if, for, nested function
function BadComponent() {
  if (someCondition) {
    const [count, setCount] = useState(0)   // ❌ ห้ามเรียกใน if
  }

  for (let i = 0; i < 3; i++) {
    useEffect(() => { ... }, [])            // ❌ ห้ามเรียกใน for
  }

  const handleClick = () => {
    const [value, setValue] = useState(0)  // ❌ ห้ามเรียกใน function ลูก
  }
}
```

---

## Module 3.5: ฟีเจอร์ใหม่ของ React 19

### ภาพรวม React 19 Releases

```
React 18:     Concurrent Features, Automatic Batching, Suspense SSR
React 19:     Actions, use() Hook, useOptimistic, useFormStatus,
              useActionState, ref เป็น prop โดยตรง, React Compiler
```

### use() Hook — อ่านค่า Promise และ Context

```tsx
import { use, Suspense } from 'react'

// ─── อ่านค่า Promise โดยตรง ───
async function fetchUser(id: number) {
  const res = await fetch(`https://jsonplaceholder.typicode.com/users/${id}`)
  return res.json()
}

// ─── Component ที่ใช้ use() ───
function UserProfile({ userPromise }: { userPromise: Promise<any> }) {
  const user = use(userPromise)   // ← use() อ่านค่า Promise โดยตรง
  return <h2>{user.name}</h2>
}

// ─── Parent ต้องครอบด้วย Suspense ───
function App() {
  const userPromise = fetchUser(1)

  return (
    <Suspense fallback={<p>กำลังโหลดข้อมูล...</p>}>
      <UserProfile userPromise={userPromise} />
    </Suspense>
  )
}
```

### Actions และ Form Actions

React 19 ให้ส่ง Function เข้า `action` ของ `<form>` ได้โดยตรง:

```tsx
import { useState } from 'react'

// ─── Action Function ───
async function submitContactForm(formData: FormData) {
  const name = formData.get('name') as string
  const email = formData.get('email') as string
  const message = formData.get('message') as string

  // จำลอง API Call
  await new Promise(resolve => setTimeout(resolve, 1000))
  console.log('ส่งข้อมูล:', { name, email, message })
  return { success: true }
}

// ─── Component ───
function ContactForm() {
  return (
    <form action={submitContactForm}>   {/* ✨ ส่ง function เข้า action โดยตรง */}
      <input name="name" placeholder="ชื่อ" required />
      <input name="email" type="email" placeholder="อีเมล" required />
      <textarea name="message" placeholder="ข้อความ" required />
      <button type="submit">ส่งข้อความ</button>
    </form>
  )
}
```

### useActionState — จัดการสถานะของ Action

```tsx
import { useActionState } from 'react'

interface FormState {
  success: boolean
  error?: string
  message?: string
}

// ─── Action ที่รับ previousState และ formData ───
async function loginAction(previousState: FormState, formData: FormData): Promise<FormState> {
  const email = formData.get('email') as string
  const password = formData.get('password') as string

  if (!email || !password) {
    return { success: false, error: 'กรุณากรอกข้อมูลให้ครบ' }
  }

  // จำลอง Login
  await new Promise(resolve => setTimeout(resolve, 1000))

  if (password === '123456') {
    return { success: true, message: `ยินดีต้อนรับ ${email}!` }
  }

  return { success: false, error: 'รหัสผ่านไม่ถูกต้อง' }
}

// ─── Component ───
function LoginForm() {
  const [state, formAction, isPending] = useActionState(
    loginAction,
    { success: false }   // Initial State
  )

  return (
    <form action={formAction}>
      <input name="email" type="email" placeholder="อีเมล" required />
      <input name="password" type="password" placeholder="รหัสผ่าน" required />

      {state.error && (
        <p style={{ color: 'red' }}>❌ {state.error}</p>
      )}
      {state.success && (
        <p style={{ color: 'green' }}>✅ {state.message}</p>
      )}

      <button type="submit" disabled={isPending}>
        {isPending ? '⏳ กำลังเข้าสู่ระบบ...' : 'เข้าสู่ระบบ'}
      </button>
    </form>
  )
}
```

### useOptimistic — Optimistic UI Update

ทำให้ UI อัปเดตทันทีก่อน API จะตอบกลับ — UX ลื่นไหลขึ้น:

```tsx
import { useState, useOptimistic } from 'react'

interface Message {
  id: number
  text: string
  sending?: boolean   // true = กำลังส่ง (Optimistic)
}

function Chat() {
  const [messages, setMessages] = useState<Message[]>([
    { id: 1, text: 'สวัสดีครับ' },
    { id: 2, text: 'เป็นยังไงบ้าง?' },
  ])

  // ─── Optimistic State ───
  const [optimisticMessages, addOptimisticMessage] = useOptimistic(
    messages,
    (currentMessages, newMessage: Message) => [...currentMessages, newMessage]
  )

  const sendMessage = async (formData: FormData) => {
    const text = formData.get('text') as string
    const tempMessage: Message = {
      id: Date.now(),
      text,
      sending: true,   // แสดง "กำลังส่ง"
    }

    // ─── อัปเดต UI ทันที (Optimistic) ───
    addOptimisticMessage(tempMessage)

    // ─── จำลอง API Call ───
    await new Promise(resolve => setTimeout(resolve, 1500))

    // ─── อัปเดต State จริงหลัง API สำเร็จ ───
    setMessages(prev => [...prev, { id: Date.now(), text }])
  }

  return (
    <div>
      <div>
        {optimisticMessages.map(msg => (
          <div key={msg.id} style={{ opacity: msg.sending ? 0.5 : 1 }}>
            {msg.text}
            {msg.sending && ' (กำลังส่ง...)'}
          </div>
        ))}
      </div>
      <form action={sendMessage}>
        <input name="text" placeholder="พิมพ์ข้อความ" required />
        <button type="submit">ส่ง</button>
      </form>
    </div>
  )
}
```

### useFormStatus — ติดตามสถานะฟอร์ม

```tsx
import { useFormStatus } from 'react-dom'

// ─── ต้องอยู่ใน Component ลูก ของ <form> ───
function SubmitButton() {
  const { pending } = useFormStatus()   // รู้ว่า form กำลัง submit อยู่

  return (
    <button type="submit" disabled={pending}>
      {pending ? (
        <span>⏳ กำลังประมวลผล...</span>
      ) : (
        <span>ส่งข้อมูล</span>
      )}
    </button>
  )
}

function ContactForm() {
  async function handleSubmit(formData: FormData) {
    await new Promise(resolve => setTimeout(resolve, 2000))
    console.log('ส่งแล้ว:', formData.get('name'))
  }

  return (
    <form action={handleSubmit}>
      <input name="name" placeholder="ชื่อ" required />
      <SubmitButton />   {/* useFormStatus จะรู้สถานะของ <form> ข้างนอก */}
    </form>
  )
}
```

### ref เป็น prop ได้โดยตรง (ไม่ต้องใช้ forwardRef)

```tsx
// ─── React 18 และก่อนหน้า (ต้องใช้ forwardRef) ───
const OldInput = React.forwardRef<HTMLInputElement, { placeholder: string }>(
  ({ placeholder }, ref) => <input ref={ref} placeholder={placeholder} />
)

// ─── React 19 (ส่ง ref เป็น prop ธรรมดาได้เลย) ───
function NewInput({ placeholder, ref }: { placeholder: string; ref?: React.Ref<HTMLInputElement> }) {
  return <input ref={ref} placeholder={placeholder} />
}

// ─── ใช้งาน ───
function App() {
  const inputRef = useRef<HTMLInputElement>(null)

  return <NewInput placeholder="พิมพ์ที่นี่" ref={inputRef} />   // ✅ ง่ายกว่ามาก
}
```

---

## 🎯 Workshop ท้ายวัน: สร้างระบบ "Task Manager" ด้วย Context + Custom Hooks

### โจทย์

สร้าง Task Manager ที่ใช้:
- **Context API** สำหรับ State ของ Tasks ทั้งหมด
- **Custom Hook** `useTaskManager`
- **useEffect** สำหรับบันทึกลง localStorage
- **React 19 Form Actions** สำหรับเพิ่ม Task

### ขั้นตอนที่ 1: Types

```typescript
// src/types/task.ts
export interface Task {
  id: string
  title: string
  description: string
  status: 'todo' | 'in-progress' | 'done'
  priority: 'low' | 'medium' | 'high'
  createdAt: string
}
```

### ขั้นตอนที่ 2: TaskContext

```tsx
// src/contexts/TaskContext.tsx
import { createContext, useContext, useState, useEffect } from 'react'
import type { Task } from '../types/task'

interface TaskContextType {
  tasks: Task[]
  addTask: (title: string, description: string, priority: Task['priority']) => void
  updateStatus: (id: string, status: Task['status']) => void
  deleteTask: (id: string) => void
}

const TaskContext = createContext<TaskContextType | null>(null)

export function useTaskContext() {
  const context = useContext(TaskContext)
  if (!context) throw new Error('useTaskContext ต้องใช้ภายใน TaskProvider')
  return context
}

export function TaskProvider({ children }: { children: React.ReactNode }) {
  const [tasks, setTasks] = useState<Task[]>(() => {
    const saved = localStorage.getItem('tasks')
    return saved ? JSON.parse(saved) : []
  })

  // ─── บันทึกลง localStorage ทุกครั้งที่ tasks เปลี่ยน ───
  useEffect(() => {
    localStorage.setItem('tasks', JSON.stringify(tasks))
  }, [tasks])

  const addTask = (title: string, description: string, priority: Task['priority']) => {
    const newTask: Task = {
      id: crypto.randomUUID(),
      title,
      description,
      status: 'todo',
      priority,
      createdAt: new Date().toISOString(),
    }
    setTasks(prev => [newTask, ...prev])
  }

  const updateStatus = (id: string, status: Task['status']) => {
    setTasks(prev =>
      prev.map(task => task.id === id ? { ...task, status } : task)
    )
  }

  const deleteTask = (id: string) => {
    setTasks(prev => prev.filter(task => task.id !== id))
  }

  return (
    <TaskContext.Provider value={{ tasks, addTask, updateStatus, deleteTask }}>
      {children}
    </TaskContext.Provider>
  )
}
```

### ขั้นตอนที่ 3: Custom Hook useTaskFilter

```tsx
// src/hooks/useTaskFilter.ts
import { useState, useMemo } from 'react'
import type { Task } from '../types/task'

function useTaskFilter(tasks: Task[]) {
  const [statusFilter, setStatusFilter] = useState<Task['status'] | 'all'>('all')
  const [priorityFilter, setPriorityFilter] = useState<Task['priority'] | 'all'>('all')
  const [searchText, setSearchText] = useState('')

  const filteredTasks = useMemo(() => {
    return tasks
      .filter(t => statusFilter === 'all' || t.status === statusFilter)
      .filter(t => priorityFilter === 'all' || t.priority === priorityFilter)
      .filter(t =>
        t.title.toLowerCase().includes(searchText.toLowerCase()) ||
        t.description.toLowerCase().includes(searchText.toLowerCase())
      )
  }, [tasks, statusFilter, priorityFilter, searchText])

  const stats = useMemo(() => ({
    total: tasks.length,
    todo: tasks.filter(t => t.status === 'todo').length,
    inProgress: tasks.filter(t => t.status === 'in-progress').length,
    done: tasks.filter(t => t.status === 'done').length,
  }), [tasks])

  return {
    filteredTasks,
    stats,
    statusFilter, setStatusFilter,
    priorityFilter, setPriorityFilter,
    searchText, setSearchText,
  }
}

export default useTaskFilter
```

### ขั้นตอนที่ 4: AddTaskForm (ใช้ React 19 Form Action)

```tsx
// src/components/AddTaskForm.tsx
import { useActionState } from 'react'
import { useFormStatus } from 'react-dom'
import { useTaskContext } from '../contexts/TaskContext'
import type { Task } from '../types/task'

function SubmitBtn() {
  const { pending } = useFormStatus()
  return (
    <button
      type="submit"
      disabled={pending}
      className="w-full bg-blue-500 hover:bg-blue-600 disabled:bg-blue-300 text-white font-medium py-2 px-4 rounded-lg transition-colors"
    >
      {pending ? '⏳ กำลังเพิ่ม...' : '➕ เพิ่ม Task'}
    </button>
  )
}

export default function AddTaskForm() {
  const { addTask } = useTaskContext()

  const [state, formAction] = useActionState(
    async (_prev: { success: boolean }, formData: FormData) => {
      const title = formData.get('title') as string
      const description = formData.get('description') as string
      const priority = formData.get('priority') as Task['priority']

      if (!title.trim()) return { success: false }

      await new Promise(resolve => setTimeout(resolve, 300))   // จำลอง async
      addTask(title.trim(), description.trim(), priority)
      return { success: true }
    },
    { success: false }
  )

  return (
    <form action={formAction} className="bg-white p-6 rounded-xl shadow-md space-y-4">
      <h2 className="text-lg font-bold text-gray-800">➕ เพิ่ม Task ใหม่</h2>

      <div>
        <label className="block text-sm font-medium text-gray-700 mb-1">หัวข้อ *</label>
        <input
          name="title"
          placeholder="เช่น: ประชุมทีม"
          required
          className="w-full border border-gray-300 rounded-lg px-3 py-2 focus:outline-none focus:ring-2 focus:ring-blue-300"
        />
      </div>

      <div>
        <label className="block text-sm font-medium text-gray-700 mb-1">รายละเอียด</label>
        <textarea
          name="description"
          placeholder="รายละเอียดเพิ่มเติม..."
          rows={2}
          className="w-full border border-gray-300 rounded-lg px-3 py-2 focus:outline-none focus:ring-2 focus:ring-blue-300"
        />
      </div>

      <div>
        <label className="block text-sm font-medium text-gray-700 mb-1">ความสำคัญ</label>
        <select
          name="priority"
          defaultValue="medium"
          className="w-full border border-gray-300 rounded-lg px-3 py-2"
        >
          <option value="low">🟢 ต่ำ</option>
          <option value="medium">🟡 กลาง</option>
          <option value="high">🔴 สูง</option>
        </select>
      </div>

      {state.success && (
        <p className="text-green-600 text-sm">✅ เพิ่ม Task เรียบร้อย!</p>
      )}

      <SubmitBtn />
    </form>
  )
}
```

### ขั้นตอนที่ 5: รวมเป็น App

```tsx
// src/App.tsx
import { TaskProvider, useTaskContext } from './contexts/TaskContext'
import useTaskFilter from './hooks/useTaskFilter'
import AddTaskForm from './components/AddTaskForm'

const STATUS_LABELS = { todo: '📋 รอดำเนินการ', 'in-progress': '⚡ กำลังทำ', done: '✅ เสร็จแล้ว' }
const PRIORITY_COLORS = { low: 'bg-green-100 text-green-700', medium: 'bg-yellow-100 text-yellow-700', high: 'bg-red-100 text-red-700' }

function TaskList() {
  const { tasks, updateStatus, deleteTask } = useTaskContext()
  const { filteredTasks, stats, statusFilter, setStatusFilter, searchText, setSearchText } = useTaskFilter(tasks)

  return (
    <div className="space-y-4">
      {/* Stats */}
      <div className="grid grid-cols-4 gap-3">
        {[
          { label: 'ทั้งหมด', count: stats.total, color: 'bg-blue-50 text-blue-700' },
          { label: 'รอทำ', count: stats.todo, color: 'bg-gray-50 text-gray-700' },
          { label: 'กำลังทำ', count: stats.inProgress, color: 'bg-yellow-50 text-yellow-700' },
          { label: 'เสร็จ', count: stats.done, color: 'bg-green-50 text-green-700' },
        ].map(({ label, count, color }) => (
          <div key={label} className={`${color} rounded-lg p-3 text-center`}>
            <p className="text-2xl font-bold">{count}</p>
            <p className="text-xs">{label}</p>
          </div>
        ))}
      </div>

      {/* Search & Filter */}
      <div className="flex gap-2">
        <input
          value={searchText}
          onChange={e => setSearchText(e.target.value)}
          placeholder="ค้นหา Task..."
          className="flex-1 border rounded-lg px-3 py-2 text-sm"
        />
        <select
          value={statusFilter}
          onChange={e => setStatusFilter(e.target.value as typeof statusFilter)}
          className="border rounded-lg px-3 py-2 text-sm"
        >
          <option value="all">ทุกสถานะ</option>
          <option value="todo">รอดำเนินการ</option>
          <option value="in-progress">กำลังทำ</option>
          <option value="done">เสร็จแล้ว</option>
        </select>
      </div>

      {/* Task Cards */}
      {filteredTasks.length === 0 ? (
        <p className="text-center text-gray-400 py-8">ไม่มี Task</p>
      ) : (
        filteredTasks.map(task => (
          <div key={task.id} className="bg-white rounded-xl shadow-sm p-4 border border-gray-100">
            <div className="flex items-start justify-between gap-2">
              <div className="flex-1">
                <div className="flex items-center gap-2 mb-1">
                  <h3 className="font-semibold text-gray-900">{task.title}</h3>
                  <span className={`text-xs px-2 py-0.5 rounded-full font-medium ${PRIORITY_COLORS[task.priority]}`}>
                    {task.priority}
                  </span>
                </div>
                {task.description && (
                  <p className="text-sm text-gray-500 mb-2">{task.description}</p>
                )}
                <select
                  value={task.status}
                  onChange={e => updateStatus(task.id, e.target.value as typeof task.status)}
                  className="text-xs border rounded px-2 py-1"
                >
                  <option value="todo">📋 รอดำเนินการ</option>
                  <option value="in-progress">⚡ กำลังทำ</option>
                  <option value="done">✅ เสร็จแล้ว</option>
                </select>
              </div>
              <button
                onClick={() => deleteTask(task.id)}
                className="text-red-400 hover:text-red-600 text-sm"
              >
                🗑️
              </button>
            </div>
          </div>
        ))
      )}
    </div>
  )
}

export default function App() {
  return (
    <TaskProvider>
      <div className="min-h-screen bg-gray-50 py-8">
        <div className="max-w-4xl mx-auto px-4">
          <h1 className="text-2xl font-bold text-gray-900 mb-8 text-center">
            📋 Task Manager — Arcelik Hitachi
          </h1>
          <div className="grid grid-cols-1 md:grid-cols-3 gap-6">
            <div className="md:col-span-1">
              <AddTaskForm />
            </div>
            <div className="md:col-span-2">
              <TaskList />
            </div>
          </div>
        </div>
      </div>
    </TaskProvider>
  )
}
```

---

## สรุปวันที่ 3

วันนี้เราได้เรียนรู้และลงมือทำ:

- ✅ useEffect — Side Effects, Dependency Array, Cleanup Function
- ✅ useRef — เข้าถึง DOM และเก็บค่าโดยไม่ Re-render
- ✅ useMemo — Memoize ผลลัพธ์การคำนวณ
- ✅ useCallback — Memoize ฟังก์ชัน
- ✅ React.memo — ป้องกัน Component Re-render ไม่จำเป็น
- ✅ Context API — แก้ปัญหา Prop Drilling
- ✅ Custom Hooks — useToggle, useFetch, useLocalStorage
- ✅ React 19: use(), useActionState, useOptimistic, useFormStatus, ref-as-prop
- ✅ Workshop Task Manager ครบวงจร

**พรุ่งนี้ (วันที่ 4):** React Router v7, ฟอร์มระดับมืออาชีพด้วย React Hook Form + Zod และ State Management ด้วย Zustand และ Redux Toolkit

---

## แหล่งอ้างอิงเพิ่มเติม

- [React 19 Release Notes](https://react.dev/blog/2024/12/05/react-19) — ฟีเจอร์ใหม่ใน React 19
- [React Hooks Reference](https://react.dev/reference/react) — เอกสาร Hooks ทั้งหมด
- [React Compiler Docs](https://react.dev/learn/react-compiler) — React Compiler คืออะไร
