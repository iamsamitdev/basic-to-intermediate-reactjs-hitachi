# หลักสูตร Basic to Intermediate React.js — วันที่ 4

## Routing, ฟอร์มมืออาชีพ และ State Management

**วันที่อบรม:** วันพฤหัสบดีที่ 11 มิถุนายน 2569 | เวลา 09:30–16:30 น.
**สถานที่:** บริษัท อาร์เซลิก ฮิตาชิ โฮม แอพพลายแอนซ์ เซลส์ (ประเทศไทย) จำกัด
**วิทยากร:** อาจารย์สามิตร โกยม | IT Genius Engineering Co., Ltd.

---

## บทนำ

วันที่สี่เราจะต่อยอดสู่แอปพลิเคชันจริงด้วยการสร้างระบบหลายหน้าด้วย **React Router v7** จัดการฟอร์มอย่างมืออาชีพด้วย **React Hook Form + Zod** และเรียนรู้การจัดการ Global State ด้วย **Zustand** และ **Redux Toolkit**

---

## Module 4.1: React Router v7

### React Router คืออะไร?

React Router คือ Library สำหรับจัดการ **Client-side Routing** — การเปลี่ยนหน้าโดยไม่ต้อง Reload Browser

```
แบบเดิม (Server Routing):
ผู้ใช้คลิก Link → Browser ส่ง Request ไป Server → Server ส่ง HTML ใหม่กลับมา → หน้าโหลดใหม่

React Router (Client-side Routing):
ผู้ใช้คลิก Link → React Router จัดการ URL → แสดง Component ตรงกับ Route → ไม่โหลดหน้าใหม่
```

### ติดตั้ง React Router v7

```bash
pnpm add react-router-dom
```

### การกำหนด Routes พื้นฐาน

```tsx
// src/main.tsx
import { StrictMode } from 'react'
import { createRoot } from 'react-dom/client'
import { BrowserRouter } from 'react-router-dom'
import App from './App'
import './index.css'

createRoot(document.getElementById('root')!).render(
  <StrictMode>
    <BrowserRouter>
      <App />
    </BrowserRouter>
  </StrictMode>,
)
```

```tsx
// src/App.tsx
import { Routes, Route } from 'react-router-dom'
import Layout from './components/Layout'
import HomePage from './pages/HomePage'
import ProductsPage from './pages/ProductsPage'
import ProductDetailPage from './pages/ProductDetailPage'
import AboutPage from './pages/AboutPage'
import NotFoundPage from './pages/NotFoundPage'

function App() {
  return (
    <Routes>
      <Route path="/" element={<Layout />}>          {/* Layout ครอบทุกหน้า */}
        <Route index element={<HomePage />} />         {/* / */}
        <Route path="products" element={<ProductsPage />} />        {/* /products */}
        <Route path="products/:id" element={<ProductDetailPage />} /> {/* /products/123 */}
        <Route path="about" element={<AboutPage />} />               {/* /about */}
        <Route path="*" element={<NotFoundPage />} />                {/* 404 */}
      </Route>
    </Routes>
  )
}
```

### Layout Component กับ Outlet

```tsx
// src/components/Layout.tsx
import { Outlet, Link, NavLink } from 'react-router-dom'

function Layout() {
  return (
    <div className="min-h-screen flex flex-col">
      {/* Navigation */}
      <nav className="bg-white shadow-sm sticky top-0 z-10">
        <div className="max-w-6xl mx-auto px-4 py-3 flex items-center justify-between">
          <Link to="/" className="text-xl font-bold text-gray-900">
            🏠 Arcelik Hitachi
          </Link>
          <div className="flex gap-6">
            <NavLink
              to="/"
              end   // ← ต้องใส่ "end" ไม่งั้น "/" จะ match ทุก URL
              className={({ isActive }) =>
                isActive ? 'text-blue-600 font-medium' : 'text-gray-600 hover:text-blue-500'
              }
            >
              หน้าแรก
            </NavLink>
            <NavLink
              to="/products"
              className={({ isActive }) =>
                isActive ? 'text-blue-600 font-medium' : 'text-gray-600 hover:text-blue-500'
              }
            >
              สินค้า
            </NavLink>
            <NavLink
              to="/about"
              className={({ isActive }) =>
                isActive ? 'text-blue-600 font-medium' : 'text-gray-600 hover:text-blue-500'
              }
            >
              เกี่ยวกับ
            </NavLink>
          </div>
        </div>
      </nav>

      {/* Main Content — Outlet คือ "ช่อง" ที่ Child Routes จะแสดง */}
      <main className="flex-1 max-w-6xl mx-auto w-full px-4 py-8">
        <Outlet />
      </main>

      {/* Footer */}
      <footer className="bg-gray-800 text-white text-center py-4">
        <p>© 2026 Arcelik Hitachi Home Appliances</p>
      </footer>
    </div>
  )
}

export default Layout
```

### Dynamic Routes กับ useParams

```tsx
// src/pages/ProductDetailPage.tsx
import { useParams, useNavigate, Link } from 'react-router-dom'

// ข้อมูล Mock
const products = [
  { id: '1', name: 'ตู้เย็น 2 ประตู', price: 12900, description: 'ตู้เย็นประหยัดพลังงาน' },
  { id: '2', name: 'เครื่องซักผ้า', price: 18900, description: 'ซักสะอาด เงียบ ประหยัด' },
]

function ProductDetailPage() {
  const { id } = useParams<{ id: string }>()   // รับค่าจาก URL /products/:id
  const navigate = useNavigate()

  const product = products.find(p => p.id === id)

  if (!product) {
    return (
      <div className="text-center py-16">
        <h2 className="text-xl text-red-500">ไม่พบสินค้า</h2>
        <Link to="/products" className="text-blue-500 hover:underline mt-4 inline-block">
          ← กลับไปหน้าสินค้า
        </Link>
      </div>
    )
  }

  return (
    <div>
      <button
        onClick={() => navigate(-1)}   // กลับหน้าก่อนหน้า (เหมือนกด Back)
        className="text-blue-500 hover:underline mb-4 flex items-center gap-1"
      >
        ← กลับ
      </button>
      <h1 className="text-3xl font-bold">{product.name}</h1>
      <p className="text-gray-600 mt-2">{product.description}</p>
      <p className="text-2xl font-bold text-blue-600 mt-4">
        ฿{product.price.toLocaleString()}
      </p>
    </div>
  )
}

export default ProductDetailPage
```

### useSearchParams — Query String

```tsx
// src/pages/ProductsPage.tsx
import { useSearchParams } from 'react-router-dom'

// URL: /products?category=refrigerator&sort=price
function ProductsPage() {
  const [searchParams, setSearchParams] = useSearchParams()

  const category = searchParams.get('category') || 'all'
  const sort = searchParams.get('sort') || 'name'

  const updateFilter = (key: string, value: string) => {
    setSearchParams(prev => {
      const next = new URLSearchParams(prev)
      next.set(key, value)
      return next
    })
  }

  return (
    <div>
      <h1>สินค้าทั้งหมด</h1>

      <div className="flex gap-4 my-4">
        <select
          value={category}
          onChange={e => updateFilter('category', e.target.value)}
          className="border rounded px-3 py-2"
        >
          <option value="all">ทั้งหมด</option>
          <option value="refrigerator">ตู้เย็น</option>
          <option value="washing-machine">เครื่องซักผ้า</option>
        </select>

        <select
          value={sort}
          onChange={e => updateFilter('sort', e.target.value)}
          className="border rounded px-3 py-2"
        >
          <option value="name">เรียงชื่อ</option>
          <option value="price">เรียงราคา</option>
        </select>
      </div>

      {/* ใช้ category และ sort ในการ filter/sort ข้อมูล */}
      <p>Filter: {category} | Sort: {sort}</p>
    </div>
  )
}
```

### useNavigate — เปลี่ยนหน้าด้วยโค้ด

```tsx
import { useNavigate } from 'react-router-dom'

function LoginPage() {
  const navigate = useNavigate()

  const handleLogin = async () => {
    // จำลอง Login
    await Promise.resolve()

    // ─── เปลี่ยนหน้าหลัง Login สำเร็จ ───
    navigate('/dashboard')

    // ─── หรือส่ง State ไปด้วย ───
    navigate('/dashboard', { state: { message: 'เข้าสู่ระบบสำเร็จ' } })

    // ─── กลับหน้าก่อนหน้า ───
    navigate(-1)

    // ─── Replace (ไม่สร้าง History entry ใหม่) ───
    navigate('/home', { replace: true })
  }

  return <button onClick={handleLogin}>เข้าสู่ระบบ</button>
}
```

### Protected Routes (Auth Guard)

```tsx
// src/components/ProtectedRoute.tsx
import { Navigate, useLocation } from 'react-router-dom'
import { useAuth } from '../contexts/AuthContext'

interface ProtectedRouteProps {
  children: React.ReactNode
  requiredRole?: 'admin' | 'user'
}

function ProtectedRoute({ children, requiredRole }: ProtectedRouteProps) {
  const { isAuthenticated, user } = useAuth()
  const location = useLocation()

  // ─── ยังไม่ได้ Login → ไปหน้า Login ───
  if (!isAuthenticated) {
    return <Navigate to="/login" state={{ from: location }} replace />
  }

  // ─── ไม่มีสิทธิ์พอ → ไปหน้า Forbidden ───
  if (requiredRole && user?.role !== requiredRole) {
    return <Navigate to="/forbidden" replace />
  }

  return <>{children}</>
}

// ─── ใช้งานใน Routes ───
<Routes>
  <Route path="/login" element={<LoginPage />} />
  <Route
    path="/dashboard"
    element={
      <ProtectedRoute>
        <DashboardPage />
      </ProtectedRoute>
    }
  />
  <Route
    path="/admin"
    element={
      <ProtectedRoute requiredRole="admin">
        <AdminPage />
      </ProtectedRoute>
    }
  />
</Routes>
```

---

## Module 4.2: ฟอร์มระดับมืออาชีพด้วย React Hook Form + Zod

### ปัญหาของการทำฟอร์มแบบ Controlled Component

```tsx
// ❌ ปัญหา: Re-render ทุกครั้งที่พิมพ์
function NaiveForm() {
  const [name, setName] = useState('')
  const [email, setEmail] = useState('')
  const [password, setPassword] = useState('')
  // ... ต้องเขียน validation เอง, มี State เยอะ, Re-render บ่อย

  const validate = () => { /* เขียน validation logic ยาวมาก */ }
}
```

### React Hook Form + Zod ช่วยได้อย่างไร?

| ปัญหา | React Hook Form แก้ |
|---|---|
| Re-render บ่อย | ใช้ Uncontrolled + ref → Re-render เฉพาะที่จำเป็น |
| Validation ซับซ้อน | ใช้ Zod Schema — เขียนครั้งเดียว ใช้ได้ทั้ง Frontend และ Backend |
| Error message ยุ่งยาก | `formState.errors` จัดการให้อัตโนมัติ |
| TypeScript integration | Type-safe ตลอด |

### ติดตั้ง

```bash
pnpm add react-hook-form zod @hookform/resolvers
```

### ทำความเข้าใจ Zod

**Zod** คือ Schema Validation Library ที่ใช้ TypeScript:

```typescript
import { z } from 'zod'

// ─── สร้าง Schema ───
const userSchema = z.object({
  name: z.string()
    .min(2, 'ชื่อต้องมีอย่างน้อย 2 ตัวอักษร')
    .max(50, 'ชื่อยาวเกิน 50 ตัวอักษร'),

  email: z.string()
    .email('รูปแบบอีเมลไม่ถูกต้อง'),

  age: z.number()
    .min(18, 'ต้องมีอายุ 18 ปีขึ้นไป')
    .max(100, 'อายุไม่ถูกต้อง'),

  password: z.string()
    .min(8, 'รหัสผ่านต้องมีอย่างน้อย 8 ตัวอักษร')
    .regex(/[A-Z]/, 'ต้องมีตัวพิมพ์ใหญ่อย่างน้อย 1 ตัว')
    .regex(/[0-9]/, 'ต้องมีตัวเลขอย่างน้อย 1 ตัว'),

  role: z.enum(['admin', 'user'], {
    errorMap: () => ({ message: 'กรุณาเลือกบทบาท' }),
  }),

  website: z.string().url('URL ไม่ถูกต้อง').optional(),
})

// ─── ดึง TypeScript Type จาก Schema ───
type UserForm = z.infer<typeof userSchema>
// → { name: string; email: string; age: number; password: string; role: 'admin' | 'user'; website?: string }

// ─── Validate ───
const result = userSchema.safeParse({ name: 'ก', email: 'wrong' })
if (!result.success) {
  console.log(result.error.format())
}
```

### 🛠️ ตัวอย่าง: ฟอร์ม Register

```tsx
// src/pages/RegisterPage.tsx
import { useForm } from 'react-hook-form'
import { zodResolver } from '@hookform/resolvers/zod'
import { z } from 'zod'
import { useNavigate } from 'react-router-dom'

// ─── 1. กำหนด Schema ───
const registerSchema = z.object({
  firstName: z.string().min(2, 'ชื่อต้องมีอย่างน้อย 2 ตัวอักษร'),
  lastName: z.string().min(2, 'นามสกุลต้องมีอย่างน้อย 2 ตัวอักษร'),
  email: z.string().email('อีเมลไม่ถูกต้อง'),
  password: z.string().min(8, 'รหัสผ่านต้องมีอย่างน้อย 8 ตัวอักษร'),
  confirmPassword: z.string(),
  department: z.enum(['IT', 'HR', 'Finance', 'Marketing', 'Sales']),
  agreeToTerms: z.boolean().refine(val => val === true, {
    message: 'กรุณายอมรับข้อกำหนดการใช้งาน',
  }),
}).refine(data => data.password === data.confirmPassword, {
  message: 'รหัสผ่านไม่ตรงกัน',
  path: ['confirmPassword'],
})

// ─── 2. ดึง Type ───
type RegisterForm = z.infer<typeof registerSchema>

// ─── 3. Reusable FormField Component ───
function FormField({
  label,
  error,
  required,
  children,
}: {
  label: string
  error?: string
  required?: boolean
  children: React.ReactNode
}) {
  return (
    <div className="space-y-1">
      <label className="block text-sm font-medium text-gray-700">
        {label} {required && <span className="text-red-500">*</span>}
      </label>
      {children}
      {error && <p className="text-sm text-red-500">{error}</p>}
    </div>
  )
}

// ─── 4. Register Form Component ───
function RegisterPage() {
  const navigate = useNavigate()

  const {
    register,        // เชื่อม input กับ React Hook Form
    handleSubmit,    // ครอบ onSubmit
    formState: { errors, isSubmitting },   // state ของฟอร์ม
    watch,           // ดูค่า field แบบ real-time
  } = useForm<RegisterForm>({
    resolver: zodResolver(registerSchema),
    defaultValues: {
      department: 'IT',
      agreeToTerms: false,
    },
  })

  const password = watch('password', '')   // ดูค่า password แบบ real-time

  const onSubmit = async (data: RegisterForm) => {
    try {
      // จำลอง API Call
      await new Promise(resolve => setTimeout(resolve, 1500))
      console.log('ลงทะเบียนสำเร็จ:', data)
      navigate('/login', { state: { message: 'ลงทะเบียนสำเร็จ กรุณาเข้าสู่ระบบ' } })
    } catch {
      console.error('เกิดข้อผิดพลาด')
    }
  }

  return (
    <div className="max-w-lg mx-auto">
      <h1 className="text-2xl font-bold text-gray-900 mb-6">📝 ลงทะเบียน</h1>

      <form onSubmit={handleSubmit(onSubmit)} className="space-y-4 bg-white p-8 rounded-xl shadow-md">
        {/* ชื่อ - นามสกุล */}
        <div className="grid grid-cols-2 gap-4">
          <FormField label="ชื่อ" error={errors.firstName?.message} required>
            <input
              {...register('firstName')}   // ← เชื่อมกับ React Hook Form
              className={`w-full border rounded-lg px-3 py-2 focus:outline-none focus:ring-2 focus:ring-blue-300 ${
                errors.firstName ? 'border-red-400' : 'border-gray-300'
              }`}
              placeholder="ชื่อ"
            />
          </FormField>

          <FormField label="นามสกุล" error={errors.lastName?.message} required>
            <input
              {...register('lastName')}
              className={`w-full border rounded-lg px-3 py-2 focus:outline-none focus:ring-2 focus:ring-blue-300 ${
                errors.lastName ? 'border-red-400' : 'border-gray-300'
              }`}
              placeholder="นามสกุล"
            />
          </FormField>
        </div>

        {/* อีเมล */}
        <FormField label="อีเมล" error={errors.email?.message} required>
          <input
            {...register('email')}
            type="email"
            className={`w-full border rounded-lg px-3 py-2 focus:outline-none focus:ring-2 focus:ring-blue-300 ${
              errors.email ? 'border-red-400' : 'border-gray-300'
            }`}
            placeholder="name@arcelik.com"
          />
        </FormField>

        {/* แผนก */}
        <FormField label="แผนก" error={errors.department?.message} required>
          <select
            {...register('department')}
            className="w-full border border-gray-300 rounded-lg px-3 py-2"
          >
            <option value="IT">IT Department</option>
            <option value="HR">Human Resources</option>
            <option value="Finance">Finance</option>
            <option value="Marketing">Marketing</option>
            <option value="Sales">Sales</option>
          </select>
        </FormField>

        {/* รหัสผ่าน */}
        <FormField label="รหัสผ่าน" error={errors.password?.message} required>
          <input
            {...register('password')}
            type="password"
            className={`w-full border rounded-lg px-3 py-2 focus:outline-none focus:ring-2 focus:ring-blue-300 ${
              errors.password ? 'border-red-400' : 'border-gray-300'
            }`}
            placeholder="อย่างน้อย 8 ตัวอักษร"
          />
        </FormField>

        {/* ยืนยันรหัสผ่าน */}
        <FormField label="ยืนยันรหัสผ่าน" error={errors.confirmPassword?.message} required>
          <input
            {...register('confirmPassword')}
            type="password"
            className={`w-full border rounded-lg px-3 py-2 focus:outline-none focus:ring-2 focus:ring-blue-300 ${
              errors.confirmPassword ? 'border-red-400' : 'border-gray-300'
            }`}
            placeholder="พิมพ์รหัสผ่านอีกครั้ง"
          />
        </FormField>

        {/* Password Strength Indicator */}
        {password && (
          <div className="space-y-1">
            <p className="text-xs text-gray-500">ความปลอดภัยรหัสผ่าน:</p>
            {[
              { check: password.length >= 8, label: 'อย่างน้อย 8 ตัวอักษร' },
              { check: /[A-Z]/.test(password), label: 'มีตัวพิมพ์ใหญ่' },
              { check: /[0-9]/.test(password), label: 'มีตัวเลข' },
              { check: /[!@#$%^&*]/.test(password), label: 'มีสัญลักษณ์พิเศษ' },
            ].map(({ check, label }) => (
              <div key={label} className="flex items-center gap-2 text-xs">
                <span>{check ? '✅' : '⬜'}</span>
                <span className={check ? 'text-green-600' : 'text-gray-400'}>{label}</span>
              </div>
            ))}
          </div>
        )}

        {/* ยอมรับข้อกำหนด */}
        <FormField label="" error={errors.agreeToTerms?.message}>
          <label className="flex items-center gap-2 cursor-pointer">
            <input
              {...register('agreeToTerms')}
              type="checkbox"
              className="w-4 h-4 text-blue-600 rounded"
            />
            <span className="text-sm text-gray-600">
              ฉันยอมรับ <span className="text-blue-600 hover:underline">ข้อกำหนดการใช้งาน</span>
            </span>
          </label>
        </FormField>

        <button
          type="submit"
          disabled={isSubmitting}
          className="w-full bg-blue-500 hover:bg-blue-600 disabled:bg-blue-300 text-white font-semibold py-3 rounded-lg transition-colors"
        >
          {isSubmitting ? '⏳ กำลังลงทะเบียน...' : '✅ ลงทะเบียน'}
        </button>
      </form>
    </div>
  )
}

export default RegisterPage
```

---

## Module 4.3: State Management — เมื่อ useState ไม่เพียงพอ

### เมื่อไรควรใช้ Global State?

```
ใช้ useState เมื่อ:
├── State ใช้ใน Component เดียว
└── State ใช้ใน Component ที่อยู่ใกล้กันไม่กี่ชั้น

ใช้ Context API เมื่อ:
├── State ใช้หลาย Component ที่กระจายกัน
└── State เปลี่ยนไม่บ่อย (เช่น Theme, Language, Auth)

ใช้ Global State Management เมื่อ:
├── State เปลี่ยนบ่อย และมี Component หลายตัวที่ Subscribe
├── Logic ซับซ้อน (async, derived state)
└── ต้องการ DevTools สำหรับ Debug
```

---

## Module 4.4: Zustand — Global State แบบเบาและยืดหยุ่น

### ทำไม Zustand?

| คุณสมบัติ | Zustand | Redux |
|---|---|---|
| **Boilerplate** | น้อยมาก | เยอะ |
| **Bundle Size** | ~1KB | ~15KB |
| **Learning Curve** | ง่าย | ยาก |
| **TypeScript** | ดีมาก | ดีมาก |
| **DevTools** | ✅ | ✅ |
| **Async** | ง่าย | ต้องใช้ Thunk |

### ติดตั้ง

```bash
pnpm add zustand
```

### 🛠️ ตัวอย่างที่ 1: Cart Store

```typescript
// src/stores/useCartStore.ts
import { create } from 'zustand'
import { devtools, persist } from 'zustand/middleware'

export interface CartItem {
  id: number
  name: string
  price: number
  quantity: number
  image: string
}

interface CartState {
  // ─── State ───
  items: CartItem[]

  // ─── Computed (Derived) ───
  totalItems: () => number
  totalPrice: () => number

  // ─── Actions ───
  addItem: (product: Omit<CartItem, 'quantity'>) => void
  removeItem: (id: number) => void
  updateQuantity: (id: number, quantity: number) => void
  clearCart: () => void
}

const useCartStore = create<CartState>()(
  devtools(         // ← ใช้ Redux DevTools ได้
    persist(        // ← บันทึกลง localStorage อัตโนมัติ
      (set, get) => ({
        items: [],

        // ─── Computed ───
        totalItems: () => get().items.reduce((sum, item) => sum + item.quantity, 0),
        totalPrice: () => get().items.reduce((sum, item) => sum + item.price * item.quantity, 0),

        // ─── Actions ───
        addItem: (product) => {
          set(state => {
            const existingItem = state.items.find(item => item.id === product.id)
            if (existingItem) {
              return {
                items: state.items.map(item =>
                  item.id === product.id
                    ? { ...item, quantity: item.quantity + 1 }
                    : item
                ),
              }
            }
            return { items: [...state.items, { ...product, quantity: 1 }] }
          })
        },

        removeItem: (id) => {
          set(state => ({ items: state.items.filter(item => item.id !== id) }))
        },

        updateQuantity: (id, quantity) => {
          if (quantity <= 0) {
            get().removeItem(id)
            return
          }
          set(state => ({
            items: state.items.map(item =>
              item.id === id ? { ...item, quantity } : item
            ),
          }))
        },

        clearCart: () => set({ items: [] }),
      }),
      {
        name: 'cart-storage',   // Key ใน localStorage
        partialize: (state) => ({ items: state.items }),   // บันทึกเฉพาะ items
      }
    ),
    { name: 'CartStore' }   // ชื่อใน Redux DevTools
  )
)

export default useCartStore
```

### การใช้งาน Zustand ใน Component

```tsx
// ─── ไม่ต้องมี Provider! ใช้ได้ทันที ───

// src/components/CartButton.tsx
import useCartStore from '../stores/useCartStore'

function CartButton() {
  // ─── Subscribe เฉพาะ totalItems (ไม่ Re-render เมื่อ items เปลี่ยนแบบอื่น) ───
  const totalItems = useCartStore(state => state.totalItems())

  return (
    <button className="relative">
      🛒
      {totalItems > 0 && (
        <span className="absolute -top-2 -right-2 bg-red-500 text-white text-xs rounded-full w-5 h-5 flex items-center justify-center">
          {totalItems}
        </span>
      )}
    </button>
  )
}

// src/components/ProductCard.tsx
import useCartStore from '../stores/useCartStore'

function AddToCartButton({ product }: { product: any }) {
  const addItem = useCartStore(state => state.addItem)   // ดึงเฉพาะ action

  return (
    <button onClick={() => addItem(product)} className="bg-blue-500 text-white px-4 py-2 rounded-lg">
      เพิ่มในตะกร้า
    </button>
  )
}

// src/pages/CartPage.tsx
import useCartStore from '../stores/useCartStore'

function CartPage() {
  const { items, totalPrice, updateQuantity, removeItem, clearCart } = useCartStore()

  if (items.length === 0) {
    return <p className="text-center text-gray-400 py-16">ตะกร้าว่างเปล่า 🛒</p>
  }

  return (
    <div className="max-w-2xl mx-auto">
      <h1 className="text-2xl font-bold mb-6">🛒 ตะกร้าสินค้า</h1>

      {items.map(item => (
        <div key={item.id} className="flex items-center gap-4 bg-white p-4 rounded-lg shadow-sm mb-3">
          <img src={item.image} alt={item.name} className="w-16 h-16 object-cover rounded" />
          <div className="flex-1">
            <h3 className="font-medium">{item.name}</h3>
            <p className="text-blue-600">฿{item.price.toLocaleString()}</p>
          </div>
          <div className="flex items-center gap-2">
            <button onClick={() => updateQuantity(item.id, item.quantity - 1)} className="w-8 h-8 border rounded-full">-</button>
            <span>{item.quantity}</span>
            <button onClick={() => updateQuantity(item.id, item.quantity + 1)} className="w-8 h-8 border rounded-full">+</button>
          </div>
          <button onClick={() => removeItem(item.id)} className="text-red-400 hover:text-red-600">🗑️</button>
        </div>
      ))}

      <div className="bg-white p-6 rounded-lg shadow-sm mt-4">
        <div className="flex justify-between text-xl font-bold">
          <span>ยอดรวม:</span>
          <span className="text-blue-600">฿{totalPrice().toLocaleString()}</span>
        </div>
        <div className="flex gap-3 mt-4">
          <button onClick={clearCart} className="flex-1 border border-gray-300 py-3 rounded-lg hover:bg-gray-50">
            ล้างตะกร้า
          </button>
          <button className="flex-1 bg-blue-500 text-white py-3 rounded-lg hover:bg-blue-600">
            ชำระเงิน
          </button>
        </div>
      </div>
    </div>
  )
}
```

---

## Module 4.5: Redux Toolkit — State Management ระดับองค์กร

### Redux Flow

```
User Action → dispatch(action) → Reducer → New State → Re-render
```

### ติดตั้ง

```bash
pnpm add @reduxjs/toolkit react-redux
```

### 🛠️ ตัวอย่าง: Notification Store ด้วย Redux Toolkit

**ขั้นตอนที่ 1: สร้าง Slice**

```typescript
// src/store/notificationSlice.ts
import { createSlice, createAsyncThunk, type PayloadAction } from '@reduxjs/toolkit'

interface Notification {
  id: string
  type: 'success' | 'error' | 'warning' | 'info'
  title: string
  message: string
  read: boolean
  createdAt: string
}

interface NotificationState {
  notifications: Notification[]
  unreadCount: number
  isLoading: boolean
  error: string | null
}

const initialState: NotificationState = {
  notifications: [],
  unreadCount: 0,
  isLoading: false,
  error: null,
}

// ─── Async Thunk: โหลดจาก API ───
export const fetchNotifications = createAsyncThunk(
  'notifications/fetchAll',
  async (userId: string, { rejectWithValue }) => {
    try {
      const response = await fetch(`/api/notifications?userId=${userId}`)
      if (!response.ok) throw new Error('โหลดการแจ้งเตือนไม่สำเร็จ')
      return await response.json()
    } catch (error) {
      return rejectWithValue(error instanceof Error ? error.message : 'เกิดข้อผิดพลาด')
    }
  }
)

// ─── Slice ───
const notificationSlice = createSlice({
  name: 'notifications',
  initialState,
  reducers: {
    addNotification: (state, action: PayloadAction<Omit<Notification, 'id' | 'read' | 'createdAt'>>) => {
      const newNotif: Notification = {
        ...action.payload,
        id: crypto.randomUUID(),
        read: false,
        createdAt: new Date().toISOString(),
      }
      state.notifications.unshift(newNotif)   // เพิ่มต้น Array
      state.unreadCount += 1
    },

    markAsRead: (state, action: PayloadAction<string>) => {
      const notif = state.notifications.find(n => n.id === action.payload)
      if (notif && !notif.read) {
        notif.read = true
        state.unreadCount = Math.max(0, state.unreadCount - 1)
      }
    },

    markAllAsRead: (state) => {
      state.notifications.forEach(n => { n.read = true })
      state.unreadCount = 0
    },

    removeNotification: (state, action: PayloadAction<string>) => {
      const index = state.notifications.findIndex(n => n.id === action.payload)
      if (index !== -1) {
        if (!state.notifications[index].read) state.unreadCount -= 1
        state.notifications.splice(index, 1)
      }
    },
  },

  // ─── จัดการ Async Thunk States ───
  extraReducers: (builder) => {
    builder
      .addCase(fetchNotifications.pending, (state) => {
        state.isLoading = true
        state.error = null
      })
      .addCase(fetchNotifications.fulfilled, (state, action) => {
        state.isLoading = false
        state.notifications = action.payload
        state.unreadCount = action.payload.filter((n: Notification) => !n.read).length
      })
      .addCase(fetchNotifications.rejected, (state, action) => {
        state.isLoading = false
        state.error = action.payload as string
      })
  },
})

export const { addNotification, markAsRead, markAllAsRead, removeNotification } = notificationSlice.actions
export default notificationSlice.reducer
```

**ขั้นตอนที่ 2: สร้าง Store**

```typescript
// src/store/index.ts
import { configureStore } from '@reduxjs/toolkit'
import notificationReducer from './notificationSlice'
// import cartReducer from './cartSlice'  ← เพิ่ม slices อื่นได้

export const store = configureStore({
  reducer: {
    notifications: notificationReducer,
    // cart: cartReducer,
  },
})

// ─── TypeScript Types ───
export type RootState = ReturnType<typeof store.getState>
export type AppDispatch = typeof store.dispatch
```

**ขั้นตอนที่ 3: Typed Hooks**

```typescript
// src/store/hooks.ts
import { useDispatch, useSelector } from 'react-redux'
import type { RootState, AppDispatch } from './index'

// ─── Custom hooks ที่มี Type ครบถ้วน ───
export const useAppDispatch = () => useDispatch<AppDispatch>()
export const useAppSelector = <T>(selector: (state: RootState) => T) =>
  useSelector<RootState, T>(selector)
```

**ขั้นตอนที่ 4: เชื่อม Provider**

```tsx
// src/main.tsx
import { Provider } from 'react-redux'
import { store } from './store'

createRoot(document.getElementById('root')!).render(
  <StrictMode>
    <Provider store={store}>     {/* ← ครอบแอปด้วย Redux Provider */}
      <BrowserRouter>
        <App />
      </BrowserRouter>
    </Provider>
  </StrictMode>,
)
```

**ขั้นตอนที่ 5: ใช้งานใน Component**

```tsx
// src/components/NotificationBell.tsx
import { useAppDispatch, useAppSelector } from '../store/hooks'
import { markAsRead, markAllAsRead, removeNotification } from '../store/notificationSlice'

function NotificationBell() {
  const dispatch = useAppDispatch()
  const { notifications, unreadCount } = useAppSelector(state => state.notifications)

  return (
    <div className="relative">
      {/* Bell Icon */}
      <button className="relative p-2">
        🔔
        {unreadCount > 0 && (
          <span className="absolute -top-1 -right-1 bg-red-500 text-white text-xs rounded-full w-5 h-5 flex items-center justify-center">
            {unreadCount}
          </span>
        )}
      </button>

      {/* Dropdown */}
      <div className="absolute right-0 top-10 w-80 bg-white rounded-xl shadow-lg border z-50 overflow-hidden">
        <div className="flex items-center justify-between p-4 border-b">
          <h3 className="font-semibold">การแจ้งเตือน</h3>
          {unreadCount > 0 && (
            <button
              onClick={() => dispatch(markAllAsRead())}
              className="text-sm text-blue-500 hover:underline"
            >
              อ่านทั้งหมด
            </button>
          )}
        </div>

        <div className="max-h-80 overflow-y-auto">
          {notifications.length === 0 ? (
            <p className="text-center text-gray-400 py-8">ไม่มีการแจ้งเตือน</p>
          ) : (
            notifications.map(notif => (
              <div
                key={notif.id}
                className={`p-4 border-b flex gap-3 ${!notif.read ? 'bg-blue-50' : ''}`}
              >
                <div className="text-xl">
                  {notif.type === 'success' && '✅'}
                  {notif.type === 'error' && '❌'}
                  {notif.type === 'warning' && '⚠️'}
                  {notif.type === 'info' && 'ℹ️'}
                </div>
                <div className="flex-1 min-w-0">
                  <p className="font-medium text-sm">{notif.title}</p>
                  <p className="text-xs text-gray-500 truncate">{notif.message}</p>
                </div>
                <div className="flex flex-col gap-1">
                  {!notif.read && (
                    <button
                      onClick={() => dispatch(markAsRead(notif.id))}
                      className="text-xs text-blue-500 hover:underline"
                    >
                      อ่าน
                    </button>
                  )}
                  <button
                    onClick={() => dispatch(removeNotification(notif.id))}
                    className="text-xs text-red-400 hover:underline"
                  >
                    ลบ
                  </button>
                </div>
              </div>
            ))
          )}
        </div>
      </div>
    </div>
  )
}
```

### เปรียบเทียบ Zustand กับ Redux Toolkit

| หัวข้อ | Zustand | Redux Toolkit |
|---|---|---|
| **Boilerplate** | น้อยมาก | ปานกลาง |
| **Bundle Size** | ~1KB | ~15KB |
| **Learning Curve** | ง่ายมาก | ปานกลาง |
| **DevTools** | Redux DevTools ผ่าน middleware | ✅ มีในตัว |
| **Async** | async/await ใน action โดยตรง | createAsyncThunk |
| **Middleware** | ง่าย (persist, devtools) | configureStore |
| **เหมาะกับ** | โปรเจกต์ขนาดกลาง | โปรเจกต์ขนาดใหญ่, ทีมใหญ่ |

> **แนะนำ:** ใช้ **Zustand** สำหรับโปรเจกต์ส่วนใหญ่ เพราะเขียนน้อยกว่า ง่ายกว่า และเร็วกว่า ใช้ **Redux Toolkit** เมื่อทีมใหญ่ต้องการ Structure ที่ชัดเจน หรือมี Global State ซับซ้อนมาก

---

## 🎯 Workshop ท้ายวัน: สร้างแอป "Employee Directory" หลายหน้า

### โจทย์

สร้างระบบค้นหาพนักงานของ Arcelik Hitachi ที่มี:
1. หน้าแรก (`/`) — แสดงสถิติภาพรวม
2. หน้า Employees (`/employees`) — ค้นหา/Filter รายชื่อพนักงาน
3. หน้า Employee Detail (`/employees/:id`) — ดูข้อมูลพนักงานละเอียด
4. หน้า Profile Form (`/employees/add`) — เพิ่มพนักงานใหม่ด้วย React Hook Form + Zod
5. ตะกร้า Favorite (`/favorites`) — พนักงานที่ Mark Favorite ด้วย Zustand

### โครงสร้างโปรเจกต์

```
src/
├── components/
│   ├── Layout.tsx
│   ├── ProtectedRoute.tsx
│   └── EmployeeCard.tsx
├── pages/
│   ├── HomePage.tsx
│   ├── EmployeesPage.tsx
│   ├── EmployeeDetailPage.tsx
│   ├── AddEmployeePage.tsx
│   └── FavoritesPage.tsx
├── stores/
│   └── useFavoriteStore.ts   (Zustand)
├── types/
│   └── employee.ts
└── App.tsx
```

### useFavoriteStore (Zustand)

```typescript
// src/stores/useFavoriteStore.ts
import { create } from 'zustand'
import { persist } from 'zustand/middleware'

interface FavoriteStore {
  favoriteIds: number[]
  toggleFavorite: (id: number) => void
  isFavorite: (id: number) => boolean
  clearFavorites: () => void
}

const useFavoriteStore = create<FavoriteStore>()(
  persist(
    (set, get) => ({
      favoriteIds: [],
      toggleFavorite: (id) => {
        set(state => ({
          favoriteIds: state.favoriteIds.includes(id)
            ? state.favoriteIds.filter(fid => fid !== id)
            : [...state.favoriteIds, id],
        }))
      },
      isFavorite: (id) => get().favoriteIds.includes(id),
      clearFavorites: () => set({ favoriteIds: [] }),
    }),
    { name: 'favorite-employees' }
  )
)

export default useFavoriteStore
```

### App.tsx — Routes ทั้งหมด

```tsx
// src/App.tsx
import { Routes, Route } from 'react-router-dom'
import Layout from './components/Layout'
import HomePage from './pages/HomePage'
import EmployeesPage from './pages/EmployeesPage'
import EmployeeDetailPage from './pages/EmployeeDetailPage'
import AddEmployeePage from './pages/AddEmployeePage'
import FavoritesPage from './pages/FavoritesPage'

function App() {
  return (
    <Routes>
      <Route path="/" element={<Layout />}>
        <Route index element={<HomePage />} />
        <Route path="employees" element={<EmployeesPage />} />
        <Route path="employees/add" element={<AddEmployeePage />} />
        <Route path="employees/:id" element={<EmployeeDetailPage />} />
        <Route path="favorites" element={<FavoritesPage />} />
      </Route>
    </Routes>
  )
}

export default App
```

---

## สรุปวันที่ 4

วันนี้เราได้เรียนรู้และลงมือทำ:

- ✅ React Router v7 — Routes, Link, NavLink, Outlet (Layout)
- ✅ Dynamic Routes ด้วย useParams
- ✅ Query String ด้วย useSearchParams
- ✅ Navigation ด้วย useNavigate
- ✅ Protected Routes (Auth Guard)
- ✅ Zod — Schema Validation พร้อม TypeScript inference
- ✅ React Hook Form — register, handleSubmit, formState, watch
- ✅ Zustand — สร้าง Store, Actions, Persist Middleware
- ✅ Redux Toolkit — createSlice, createAsyncThunk, configureStore
- ✅ Workshop Employee Directory หลายหน้าครบวงจร

**พรุ่งนี้ (วันที่ 5):** TanStack Query สำหรับ Data Fetching, Authentication, Performance Optimization, Deployment และ Workshop ปิดท้าย

---

## แหล่งอ้างอิงเพิ่มเติม

- [React Router v7 Docs](https://reactrouter.com) — เอกสาร React Router
- [React Hook Form Docs](https://react-hook-form.com) — เอกสาร React Hook Form
- [Zod Docs](https://zod.dev) — เอกสาร Zod
- [Zustand Docs](https://zustand-demo.pmnd.rs) — เอกสาร Zustand
- [Redux Toolkit Docs](https://redux-toolkit.js.org) — เอกสาร Redux Toolkit
