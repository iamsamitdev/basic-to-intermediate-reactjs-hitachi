# หลักสูตร Basic to Intermediate React.js — วันที่ 5

## TanStack Query, Authentication, Performance, Deployment และ Workshop ปิดท้าย

**วันที่อบรม:** วันศุกร์ที่ 12 มิถุนายน 2569 | เวลา 09:30–16:30 น.
**สถานที่:** บริษัท อาร์เซลิก ฮิตาชิ โฮม แอพพลายแอนซ์ เซลส์ (ประเทศไทย) จำกัด
**วิทยากร:** อาจารย์สามิตร โกยม | IT Genius Engineering Co., Ltd.

---

## บทนำ

วันสุดท้ายเราจะเรียนรู้การเชื่อมต่อข้อมูลจริงด้วย **TanStack Query**, ระบบ **Authentication** ด้วย JWT, เทคนิค **Performance Optimization**, การ **Deploy** สู่ Production และปิดท้ายด้วย **Workshop** สร้างแอปครบวงจร

---

## Module 5.1: Data Fetching ด้วย TanStack Query (React Query v5)

### ปัญหาของการ Fetch ด้วย useEffect

```tsx
// ❌ ปัญหามากมายเมื่อ Fetch ด้วย useEffect เอง:
function Products() {
  const [data, setData] = useState([])
  const [loading, setLoading] = useState(true)
  const [error, setError] = useState(null)

  useEffect(() => {
    // ปัญหา 1: ไม่มี Caching — fetch ซ้ำทุกครั้งที่ mount
    // ปัญหา 2: Race condition เมื่อ Component unmount ระหว่าง fetch
    // ปัญหา 3: ต้องเขียน loading/error state เองทุกที่
    // ปัญหา 4: ไม่รู้ว่า data เก่าหรือใหม่ (Stale Data)
    // ปัญหา 5: Background Refetch ยาก
    fetch('/api/products')
      .then(res => res.json())
      .then(setData)
      .catch(setError)
      .finally(() => setLoading(false))
  }, [])
}
```

### TanStack Query แก้ทุกปัญหา

```
TanStack Query จัดการให้อัตโนมัติ:
✅ Caching — cache data และ serve จาก cache ก่อน
✅ Background Refetch — refetch ใน background โดยไม่ block UI
✅ Stale Time — กำหนดว่า data "เก่า" เมื่อไร
✅ Loading/Error State — จัดการให้
✅ Retry — Retry อัตโนมัติเมื่อ Error
✅ Pagination & Infinite Scroll — รองรับ
✅ Optimistic Updates — UI อัปเดตก่อน API
```

### ติดตั้ง

```bash
npm install @tanstack/react-query @tanstack/react-query-devtools
```

### ตั้งค่า QueryClient

```tsx
// src/main.tsx
import { QueryClient, QueryClientProvider } from '@tanstack/react-query'
import { ReactQueryDevtools } from '@tanstack/react-query-devtools'

const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      staleTime: 5 * 60 * 1000,   // 5 นาที — data ถือว่า "สด" นาน 5 นาที
      retry: 2,                    // Retry 2 ครั้งเมื่อ Error
      refetchOnWindowFocus: true,  // Refetch เมื่อ User กลับมาที่ Browser Tab
    },
  },
})

createRoot(document.getElementById('root')!).render(
  <StrictMode>
    <QueryClientProvider client={queryClient}>
      <BrowserRouter>
        <App />
      </BrowserRouter>
      <ReactQueryDevtools initialIsOpen={false} />   {/* DevTools */}
    </QueryClientProvider>
  </StrictMode>,
)
```

### useQuery — Fetching ข้อมูล

```typescript
// src/api/productsApi.ts
const BASE_URL = 'https://dummyjson.com'

export interface Product {
  id: number
  title: string
  price: number
  description: string
  category: string
  thumbnail: string
  rating: number
  stock: number
}

export interface ProductsResponse {
  products: Product[]
  total: number
  skip: number
  limit: number
}

// ─── API Functions (Query Functions) ───
export const fetchProducts = async (limit = 10, skip = 0): Promise<ProductsResponse> => {
  const res = await fetch(`${BASE_URL}/products?limit=${limit}&skip=${skip}`)
  if (!res.ok) throw new Error('โหลดสินค้าไม่สำเร็จ')
  return res.json()
}

export const fetchProductById = async (id: number): Promise<Product> => {
  const res = await fetch(`${BASE_URL}/products/${id}`)
  if (!res.ok) throw new Error('ไม่พบสินค้า')
  return res.json()
}

export const searchProducts = async (query: string): Promise<ProductsResponse> => {
  const res = await fetch(`${BASE_URL}/products/search?q=${query}`)
  if (!res.ok) throw new Error('ค้นหาไม่สำเร็จ')
  return res.json()
}
```

```tsx
// src/pages/ProductsPage.tsx
import { useState } from 'react'
import { useQuery } from '@tanstack/react-query'
import { fetchProducts, searchProducts } from '../api/productsApi'
import { Link } from 'react-router-dom'

function ProductsPage() {
  const [search, setSearch] = useState('')
  const [page, setPage] = useState(1)
  const limit = 8
  const skip = (page - 1) * limit

  // ─── Query: ค้นหา หรือ โหลดทั้งหมด ───
  const { data, isLoading, isError, error, isFetching } = useQuery({
    queryKey: search ? ['products', 'search', search] : ['products', { page, limit }],
    queryFn: () => search ? searchProducts(search) : fetchProducts(limit, skip),
    placeholderData: (previousData) => previousData,   // ← ใช้ข้อมูลเก่าขณะโหลดใหม่
  })

  if (isError) {
    return (
      <div className="text-center py-16">
        <p className="text-red-500 text-xl mb-4">❌ {(error as Error).message}</p>
      </div>
    )
  }

  return (
    <div>
      <div className="flex items-center gap-4 mb-6">
        <input
          value={search}
          onChange={e => { setSearch(e.target.value); setPage(1) }}
          placeholder="ค้นหาสินค้า..."
          className="flex-1 border border-gray-300 rounded-lg px-4 py-2 focus:outline-none focus:ring-2 focus:ring-blue-300"
        />
        {isFetching && <span className="text-blue-500 text-sm">⟳ กำลังโหลด...</span>}
      </div>

      {isLoading ? (
        <div className="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4">
          {Array.from({ length: 8 }).map((_, i) => (
            <div key={i} className="bg-gray-100 rounded-xl h-64 animate-pulse" />
          ))}
        </div>
      ) : (
        <>
          <div className="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4">
            {data?.products.map(product => (
              <Link key={product.id} to={`/products/${product.id}`}>
                <div className="bg-white rounded-xl shadow-md overflow-hidden hover:shadow-lg transition-shadow">
                  <img src={product.thumbnail} alt={product.title} className="w-full h-48 object-cover" />
                  <div className="p-3">
                    <h3 className="font-medium text-gray-900 text-sm line-clamp-2">{product.title}</h3>
                    <div className="flex items-center justify-between mt-2">
                      <span className="text-blue-600 font-bold">${product.price}</span>
                      <span className="text-yellow-500 text-sm">★ {product.rating}</span>
                    </div>
                  </div>
                </div>
              </Link>
            ))}
          </div>

          {/* Pagination */}
          {!search && (
            <div className="flex items-center justify-center gap-4 mt-8">
              <button
                onClick={() => setPage(p => Math.max(1, p - 1))}
                disabled={page === 1}
                className="px-4 py-2 border rounded-lg disabled:opacity-40"
              >
                ← ก่อนหน้า
              </button>
              <span className="text-gray-600">หน้า {page} / {Math.ceil((data?.total ?? 0) / limit)}</span>
              <button
                onClick={() => setPage(p => p + 1)}
                disabled={skip + limit >= (data?.total ?? 0)}
                className="px-4 py-2 border rounded-lg disabled:opacity-40"
              >
                ถัดไป →
              </button>
            </div>
          )}
        </>
      )}
    </div>
  )
}
```

### useMutation — Create, Update, Delete

```tsx
import { useMutation, useQueryClient } from '@tanstack/react-query'

interface NewProduct {
  title: string
  price: number
  description: string
  category: string
}

// ─── API Function ───
const createProduct = async (product: NewProduct) => {
  const res = await fetch('https://dummyjson.com/products/add', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(product),
  })
  if (!res.ok) throw new Error('เพิ่มสินค้าไม่สำเร็จ')
  return res.json()
}

function AddProductForm() {
  const queryClient = useQueryClient()

  const mutation = useMutation({
    mutationFn: createProduct,

    // ─── หลังสำเร็จ: Invalidate Cache ให้ fetch ใหม่ ───
    onSuccess: (newProduct) => {
      queryClient.invalidateQueries({ queryKey: ['products'] })
      console.log('เพิ่มสำเร็จ:', newProduct)
      alert('เพิ่มสินค้าเรียบร้อย!')
    },

    onError: (error: Error) => {
      alert(`เกิดข้อผิดพลาด: ${error.message}`)
    },
  })

  const handleSubmit = (e: React.FormEvent<HTMLFormElement>) => {
    e.preventDefault()
    const formData = new FormData(e.currentTarget)
    mutation.mutate({
      title: formData.get('title') as string,
      price: Number(formData.get('price')),
      description: formData.get('description') as string,
      category: formData.get('category') as string,
    })
  }

  return (
    <form onSubmit={handleSubmit} className="space-y-4 max-w-md">
      <input name="title" placeholder="ชื่อสินค้า" required className="w-full border rounded-lg px-3 py-2" />
      <input name="price" type="number" placeholder="ราคา" required className="w-full border rounded-lg px-3 py-2" />
      <textarea name="description" placeholder="คำอธิบาย" className="w-full border rounded-lg px-3 py-2" />
      <input name="category" placeholder="หมวดหมู่" required className="w-full border rounded-lg px-3 py-2" />
      <button
        type="submit"
        disabled={mutation.isPending}
        className="w-full bg-blue-500 text-white py-2 rounded-lg disabled:bg-blue-300"
      >
        {mutation.isPending ? '⏳ กำลังบันทึก...' : '💾 บันทึกสินค้า'}
      </button>
    </form>
  )
}
```

### Query Invalidation

```tsx
// ─── ล้าง Cache เพื่อ Refetch ───
const queryClient = useQueryClient()

// Invalidate ทุก query ที่มี key 'products'
queryClient.invalidateQueries({ queryKey: ['products'] })

// Invalidate เฉพาะสินค้า ID 5
queryClient.invalidateQueries({ queryKey: ['products', 5] })

// Set ค่าโดยตรง (ไม่ต้อง Refetch)
queryClient.setQueryData(['products', 5], updatedProduct)

// Prefetch (โหลดล่วงหน้า)
queryClient.prefetchQuery({
  queryKey: ['products', id],
  queryFn: () => fetchProductById(id),
})
```

---

## Module 5.2: Authentication และ JWT

### JWT Authentication Flow

```
1. ผู้ใช้กรอก Email + Password
           │
           ▼
2. Frontend ส่ง POST /auth/login { email, password }
           │
           ▼
3. Backend ตรวจสอบ → สร้าง JWT Token
           │
           ▼
4. Backend ส่ง Token กลับมา
           │
           ▼
5. Frontend บันทึก Token (localStorage หรือ httpOnly Cookie)
           │
           ▼
6. ทุก API Request ต่อไป: ส่ง Header Authorization: Bearer <token>
           │
           ▼
7. Backend ตรวจสอบ Token → อนุญาตหรือปฏิเสธ
```

### JWT คืออะไร?

```
JWT = Header.Payload.Signature

eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.  ← Header (Base64)
eyJzdWIiOiIxMjMiLCJuYW1lIjoiU29tY2hhaSIsInJvbGUiOiJ1c2VyIn0. ← Payload (Base64)
SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c  ← Signature

Payload มี:
{
  "sub": "123",              // Subject (User ID)
  "name": "Somchai",        // ข้อมูล User
  "role": "admin",
  "iat": 1718784000,         // Issued At
  "exp": 1718870400          // Expiration (หมดอายุ)
}
```

### การเก็บ Token อย่างปลอดภัย

```
❌ อย่าเก็บ Token ใน localStorage ถ้าแอปมี User Content จาก Third Party
   เสี่ยง XSS Attack

✅ วิธีที่ปลอดภัยที่สุด: httpOnly Cookie (จัดการฝั่ง Server)
   Browser จะส่ง Cookie โดยอัตโนมัติ, JavaScript เข้าถึงไม่ได้

✅ ถ้าต้องใช้ localStorage: ตรวจสอบ Input ทุกจุด, ใช้ CSP Header
```

### 🛠️ สร้าง Auth Context พร้อม Axios

```bash
npm install axios
```

```typescript
// src/lib/axios.ts
import axios from 'axios'

const apiClient = axios.create({
  baseURL: import.meta.env.VITE_API_URL || 'https://dummyjson.com',
  timeout: 10000,
})

// ─── Request Interceptor: ใส่ Token อัตโนมัติ ───
apiClient.interceptors.request.use(
  (config) => {
    const token = localStorage.getItem('auth_token')
    if (token) {
      config.headers.Authorization = `Bearer ${token}`
    }
    return config
  },
  (error) => Promise.reject(error)
)

// ─── Response Interceptor: จัดการ Error ───
apiClient.interceptors.response.use(
  (response) => response,
  async (error) => {
    if (error.response?.status === 401) {
      // Token หมดอายุ → ล้าง Token และ Redirect Login
      localStorage.removeItem('auth_token')
      localStorage.removeItem('auth_user')
      window.location.href = '/login'
    }
    return Promise.reject(error)
  }
)

export default apiClient
```

```tsx
// src/contexts/AuthContext.tsx
import { createContext, useContext, useState, useCallback } from 'react'
import apiClient from '../lib/axios'

interface AuthUser {
  id: number
  username: string
  email: string
  firstName: string
  lastName: string
  image: string
  token: string
}

interface AuthContextType {
  user: AuthUser | null
  isAuthenticated: boolean
  login: (username: string, password: string) => Promise<void>
  logout: () => void
}

const AuthContext = createContext<AuthContextType | null>(null)

export function useAuth() {
  const context = useContext(AuthContext)
  if (!context) throw new Error('useAuth ต้องใช้ภายใน AuthProvider')
  return context
}

export function AuthProvider({ children }: { children: React.ReactNode }) {
  const [user, setUser] = useState<AuthUser | null>(() => {
    const saved = localStorage.getItem('auth_user')
    return saved ? JSON.parse(saved) : null
  })

  const login = useCallback(async (username: string, password: string) => {
    const { data } = await apiClient.post<AuthUser>('/auth/login', { username, password })

    setUser(data)
    localStorage.setItem('auth_token', data.token)
    localStorage.setItem('auth_user', JSON.stringify(data))
  }, [])

  const logout = useCallback(() => {
    setUser(null)
    localStorage.removeItem('auth_token')
    localStorage.removeItem('auth_user')
  }, [])

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

### 🛠️ Login Form ด้วย React Hook Form + TanStack Query

```tsx
// src/pages/LoginPage.tsx
import { useForm } from 'react-hook-form'
import { zodResolver } from '@hookform/resolvers/zod'
import { z } from 'zod'
import { useNavigate, useLocation } from 'react-router-dom'
import { useAuth } from '../contexts/AuthContext'
import { useState } from 'react'

const loginSchema = z.object({
  username: z.string().min(1, 'กรุณากรอกชื่อผู้ใช้'),
  password: z.string().min(1, 'กรุณากรอกรหัสผ่าน'),
})

type LoginForm = z.infer<typeof loginSchema>

function LoginPage() {
  const { login } = useAuth()
  const navigate = useNavigate()
  const location = useLocation()
  const [serverError, setServerError] = useState<string | null>(null)

  const from = (location.state as any)?.from?.pathname || '/dashboard'

  const { register, handleSubmit, formState: { errors, isSubmitting } } = useForm<LoginForm>({
    resolver: zodResolver(loginSchema),
  })

  const onSubmit = async ({ username, password }: LoginForm) => {
    try {
      setServerError(null)
      await login(username, password)
      navigate(from, { replace: true })
    } catch (err: any) {
      setServerError(err.response?.data?.message || 'เข้าสู่ระบบไม่สำเร็จ กรุณาลองใหม่')
    }
  }

  return (
    <div className="min-h-screen bg-gradient-to-br from-blue-50 to-indigo-100 flex items-center justify-center p-4">
      <div className="bg-white rounded-2xl shadow-xl w-full max-w-md p-8">
        <div className="text-center mb-8">
          <h1 className="text-3xl font-bold text-gray-900">🏠 Arcelik Hitachi</h1>
          <p className="text-gray-500 mt-2">เข้าสู่ระบบเพื่อดำเนินการต่อ</p>
        </div>

        <form onSubmit={handleSubmit(onSubmit)} className="space-y-5">
          <div>
            <label className="block text-sm font-medium text-gray-700 mb-1">ชื่อผู้ใช้</label>
            <input
              {...register('username')}
              className={`w-full border rounded-lg px-4 py-3 focus:outline-none focus:ring-2 focus:ring-blue-300 ${
                errors.username ? 'border-red-400' : 'border-gray-300'
              }`}
              placeholder="emilys"
            />
            {errors.username && <p className="text-red-500 text-sm mt-1">{errors.username.message}</p>}
          </div>

          <div>
            <label className="block text-sm font-medium text-gray-700 mb-1">รหัสผ่าน</label>
            <input
              {...register('password')}
              type="password"
              className={`w-full border rounded-lg px-4 py-3 focus:outline-none focus:ring-2 focus:ring-blue-300 ${
                errors.password ? 'border-red-400' : 'border-gray-300'
              }`}
              placeholder="emilyspass"
            />
            {errors.password && <p className="text-red-500 text-sm mt-1">{errors.password.message}</p>}
          </div>

          {serverError && (
            <div className="bg-red-50 border border-red-200 text-red-600 rounded-lg p-3 text-sm">
              ❌ {serverError}
            </div>
          )}

          <div className="bg-blue-50 rounded-lg p-3 text-sm text-blue-700">
            <p className="font-medium">ทดสอบด้วย:</p>
            <p>Username: emilys | Password: emilyspass</p>
          </div>

          <button
            type="submit"
            disabled={isSubmitting}
            className="w-full bg-blue-600 hover:bg-blue-700 disabled:bg-blue-300 text-white font-semibold py-3 rounded-lg transition-colors"
          >
            {isSubmitting ? '⏳ กำลังเข้าสู่ระบบ...' : '🔑 เข้าสู่ระบบ'}
          </button>
        </form>
      </div>
    </div>
  )
}

export default LoginPage
```

---

## Module 5.3: Performance Optimization

### React.memo — ป้องกัน Re-render

```tsx
import { memo } from 'react'

// ─── Component นี้จะ Re-render เฉพาะเมื่อ Props เปลี่ยน ───
const ProductCard = memo(function ProductCard({
  name,
  price,
  onAddToCart,
}: {
  name: string
  price: number
  onAddToCart: () => void
}) {
  console.log(`${name} rendered`)
  return (
    <div>
      <h3>{name}</h3>
      <p>฿{price}</p>
      <button onClick={onAddToCart}>เพิ่มในตะกร้า</button>
    </div>
  )
})
```

### Code Splitting และ Lazy Loading

```tsx
import { lazy, Suspense } from 'react'
import { Routes, Route } from 'react-router-dom'

// ─── Lazy Load — โหลด Component เมื่อต้องการ ───
const DashboardPage = lazy(() => import('./pages/DashboardPage'))
const ReportsPage = lazy(() => import('./pages/ReportsPage'))
const SettingsPage = lazy(() => import('./pages/SettingsPage'))

// ─── Loading Fallback ───
function PageLoader() {
  return (
    <div className="flex items-center justify-center h-64">
      <div className="flex flex-col items-center gap-3 text-gray-500">
        <div className="w-10 h-10 border-4 border-blue-500 border-t-transparent rounded-full animate-spin" />
        <p>กำลังโหลดหน้า...</p>
      </div>
    </div>
  )
}

function App() {
  return (
    <Suspense fallback={<PageLoader />}>    {/* ← ครอบ Routes ทั้งหมด */}
      <Routes>
        <Route path="/dashboard" element={<DashboardPage />} />   {/* โหลดเมื่อ navigate ถึง */}
        <Route path="/reports" element={<ReportsPage />} />
        <Route path="/settings" element={<SettingsPage />} />
      </Routes>
    </Suspense>
  )
}
```

### การวิเคราะห์ Bundle Size

```bash
# ติดตั้ง rollup-plugin-visualizer
npm install -D rollup-plugin-visualizer
```

```typescript
// vite.config.ts
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'
import { visualizer } from 'rollup-plugin-visualizer'

export default defineConfig({
  plugins: [
    react(),
    visualizer({
      open: true,         // เปิด Browser อัตโนมัติ
      filename: 'dist/stats.html',
      gzipSize: true,
    }),
  ],
})
```

```bash
npm run build
# หลัง build เสร็จ จะมีไฟล์ dist/stats.html เปิดให้อัตโนมัติ
# เห็นได้ว่า package ไหนใหญ่ที่สุด
```

### useMemo และ useCallback ในบริบทของ Performance

```tsx
import { useState, useMemo, useCallback, memo } from 'react'

interface Product {
  id: number
  name: string
  price: number
  category: string
}

// ─── Child Component ที่ Memoized ───
const ProductList = memo(function ProductList({
  products,
  onSelect,
}: {
  products: Product[]
  onSelect: (id: number) => void
}) {
  return (
    <ul>
      {products.map(p => (
        <li key={p.id} onClick={() => onSelect(p.id)}>
          {p.name}
        </li>
      ))}
    </ul>
  )
})

function ProductDashboard({ allProducts }: { allProducts: Product[] }) {
  const [filter, setFilter] = useState('')
  const [selectedId, setSelectedId] = useState<number | null>(null)

  // ─── useMemo: คำนวณเฉพาะเมื่อ allProducts หรือ filter เปลี่ยน ───
  const filteredProducts = useMemo(() => {
    return allProducts.filter(p =>
      p.name.toLowerCase().includes(filter.toLowerCase())
    )
  }, [allProducts, filter])

  // ─── useCallback: ฟังก์ชันเดิม ไม่สร้างใหม่เมื่อ filter เปลี่ยน ───
  const handleSelect = useCallback((id: number) => {
    setSelectedId(id)
  }, [])   // ไม่มี dependencies → สร้างครั้งเดียว

  return (
    <div>
      <input value={filter} onChange={e => setFilter(e.target.value)} />
      <ProductList
        products={filteredProducts}
        onSelect={handleSelect}     // useCallback ป้องกัน ProductList Re-render ไม่จำเป็น
      />
      {selectedId && <p>เลือก ID: {selectedId}</p>}
    </div>
  )
}
```

---

## Module 5.4: Error Handling และ Best Practices

### Error Boundaries

```tsx
// src/components/ErrorBoundary.tsx
import { Component, type ReactNode } from 'react'

interface Props {
  children: ReactNode
  fallback?: ReactNode
}

interface State {
  hasError: boolean
  error: Error | null
}

// ─── Error Boundary ต้องเป็น Class Component ───
class ErrorBoundary extends Component<Props, State> {
  state: State = { hasError: false, error: null }

  static getDerivedStateFromError(error: Error): State {
    return { hasError: true, error }
  }

  componentDidCatch(error: Error, info: React.ErrorInfo) {
    console.error('Error caught by boundary:', error, info)
    // ส่งไปยัง Error Tracking Service เช่น Sentry
  }

  render() {
    if (this.state.hasError) {
      return this.props.fallback ?? (
        <div className="text-center py-16 text-red-500">
          <h2 className="text-xl font-bold mb-2">⚠️ เกิดข้อผิดพลาด</h2>
          <p className="text-sm text-gray-500 mb-4">{this.state.error?.message}</p>
          <button
            onClick={() => this.setState({ hasError: false, error: null })}
            className="bg-blue-500 text-white px-4 py-2 rounded-lg"
          >
            ลองใหม่
          </button>
        </div>
      )
    }
    return this.props.children
  }
}

export default ErrorBoundary

// ─── ใช้งาน ───
<ErrorBoundary fallback={<p>Section นี้เกิดข้อผิดพลาด</p>}>
  <RiskyComponent />
</ErrorBoundary>
```

### Environment Variables

```bash
# .env.development
VITE_API_URL=http://localhost:3000/api
VITE_APP_NAME=Arcelik App (Dev)

# .env.production
VITE_API_URL=https://api.arcelik.co.th/api
VITE_APP_NAME=Arcelik Hitachi
```

```typescript
// ใช้งานใน Code
const apiUrl = import.meta.env.VITE_API_URL
const appName = import.meta.env.VITE_APP_NAME

// ─── กฎสำคัญ ───
// ✅ VITE_ prefix — เข้าถึงได้ใน Client (Browser)
// ❌ ไม่มี VITE_ prefix — เข้าถึงไม่ได้ใน Browser (ปลอดภัยกว่า)
// ❌ ห้ามเก็บ Secret Key ใน VITE_ เด็ดขาด! เพราะ User เห็นได้
```

### Feature-based Project Structure

```
src/
├── features/                      # จัดกลุ่มตาม Feature
│   ├── auth/
│   │   ├── components/
│   │   │   ├── LoginForm.tsx
│   │   │   └── RegisterForm.tsx
│   │   ├── contexts/
│   │   │   └── AuthContext.tsx
│   │   ├── hooks/
│   │   │   └── useAuth.ts
│   │   ├── pages/
│   │   │   ├── LoginPage.tsx
│   │   │   └── RegisterPage.tsx
│   │   └── index.ts               # Public API ของ Feature
│   │
│   ├── products/
│   │   ├── components/
│   │   │   ├── ProductCard.tsx
│   │   │   └── ProductFilters.tsx
│   │   ├── hooks/
│   │   │   └── useProducts.ts     # Custom hook สำหรับ TanStack Query
│   │   ├── pages/
│   │   │   ├── ProductsPage.tsx
│   │   │   └── ProductDetailPage.tsx
│   │   └── index.ts
│   │
│   └── cart/
│       ├── components/
│       │   ├── CartDrawer.tsx
│       │   └── CartItem.tsx
│       ├── stores/
│       │   └── useCartStore.ts    # Zustand Store
│       └── index.ts
│
├── shared/                        # ใช้ร่วมทุก Feature
│   ├── components/
│   │   ├── Button.tsx
│   │   ├── Modal.tsx
│   │   └── ErrorBoundary.tsx
│   ├── hooks/
│   │   ├── useDebounce.ts
│   │   └── useLocalStorage.ts
│   └── lib/
│       ├── axios.ts
│       └── queryClient.ts
│
└── App.tsx
```

---

## Module 5.5: Build และ Deployment

### Build โปรเจกต์

```bash
# Build สำหรับ Production
npm run build

# ผลลัพธ์อยู่ใน dist/
# dist/
# ├── index.html
# └── assets/
#     ├── index-[hash].js     ← JavaScript Bundle
#     └── index-[hash].css    ← CSS Bundle

# ทดสอบ Production Build ใน Local
npm run preview
# เปิด http://localhost:4173
```

### Deploy ขึ้น Vercel

**วิธีที่ 1: Vercel CLI**

```bash
# ติดตั้ง Vercel CLI
npm install -g vercel

# Login
vercel login

# Deploy
vercel

# Deploy Production
vercel --prod
```

**วิธีที่ 2: เชื่อม GitHub (แนะนำ)**

1. Push โค้ดขึ้น GitHub
2. ไปที่ https://vercel.com → New Project
3. Import จาก GitHub Repository
4. ตั้งค่า Framework Preset: **Vite**
5. ตั้งค่า Environment Variables
6. คลิก **Deploy**
7. Vercel สร้าง URL ให้อัตโนมัติ เช่น `my-app.vercel.app`

**ตั้งค่า Environment Variables บน Vercel:**
- Settings → Environment Variables
- ใส่ `VITE_API_URL` และค่าอื่น ๆ
- แยกตาม Environment (Production, Preview, Development)

### Deploy ขึ้น Netlify

```bash
# ติดตั้ง Netlify CLI
npm install -g netlify-cli

# Login
netlify login

# Deploy
netlify deploy --prod --dir=dist
```

หรือผ่าน Web Interface:
1. ไปที่ https://netlify.com → Add new site
2. Import from GitHub
3. Build Command: `npm run build`
4. Publish Directory: `dist`
5. Deploy!

**สำคัญ: ตั้งค่า Redirect สำหรับ SPA**

```toml
# netlify.toml — ต้องมีไฟล์นี้สำหรับ React Router
[[redirects]]
  from = "/*"
  to = "/index.html"
  status = 200
```

```
# Vercel จัดการให้อัตโนมัติ
# แต่ถ้า Deploy บน Nginx ต้องตั้งค่าเอง:
location / {
    try_files $uri $uri/ /index.html;
}
```

---

## 🎯 Workshop ปิดท้าย: สร้างแอป "Arcelik Product Hub" ครบวงจร

### โจทย์

สร้างแอปพลิเคชัน **Product Hub** สำหรับบริษัท Arcelik Hitachi ที่รวมทุกอย่างที่เรียนมา 5 วัน:

### Feature ที่ต้องมี

1. ✅ **Login/Logout** — ระบบ Authentication ด้วย JWT (ใช้ dummyjson.com)
2. ✅ **Product List** — แสดงสินค้า + Search + Filter + Pagination
3. ✅ **Product Detail** — ดูรายละเอียดสินค้า + รูปภาพ + Rating
4. ✅ **Cart** — เพิ่ม/ลด/ลบสินค้าในตะกร้า (Zustand + persist)
5. ✅ **Favorites** — Mark สินค้าที่ชอบ (Zustand)
6. ✅ **Add Product** — เพิ่มสินค้าใหม่ด้วยฟอร์ม (React Hook Form + Zod)
7. ✅ **Protected Routes** — ต้อง Login ก่อนเข้าใช้
8. ✅ **Responsive** — ใช้ได้ทั้งมือถือและ Desktop

### Tech Stack ที่ใช้

```
Vite + React 19 + TypeScript
Tailwind CSS v4                  → Styling
React Router v7                  → Routing
TanStack Query v5               → Data Fetching
Zustand                          → Cart + Favorites State
React Hook Form + Zod            → Form + Validation
Axios                            → HTTP Client
```

### โครงสร้างโปรเจกต์ขั้นสุดท้าย

```
product-hub/
├── src/
│   ├── features/
│   │   ├── auth/
│   │   │   ├── AuthContext.tsx
│   │   │   └── LoginPage.tsx
│   │   ├── products/
│   │   │   ├── productApi.ts       (TanStack Query functions)
│   │   │   ├── ProductsPage.tsx
│   │   │   ├── ProductDetailPage.tsx
│   │   │   └── AddProductPage.tsx
│   │   └── cart/
│   │       ├── useCartStore.ts     (Zustand)
│   │       └── CartPage.tsx
│   ├── shared/
│   │   ├── components/
│   │   │   ├── Layout.tsx
│   │   │   ├── ProtectedRoute.tsx
│   │   │   └── ErrorBoundary.tsx
│   │   └── lib/
│   │       ├── axios.ts
│   │       └── queryClient.ts
│   ├── App.tsx
│   └── main.tsx
├── .env
├── .env.production
└── netlify.toml
```

### ขั้นตอนที่ 1: ตั้งค่าโปรเจกต์

```bash
npm create vite@latest product-hub -- --template react-ts
cd product-hub
npm install
npm install react-router-dom @tanstack/react-query @tanstack/react-query-devtools
npm install zustand react-hook-form zod @hookform/resolvers axios
npm install tailwindcss @tailwindcss/vite
```

### ขั้นตอนที่ 2: productApi.ts

```typescript
// src/features/products/productApi.ts
import apiClient from '../../shared/lib/axios'

export interface Product {
  id: number
  title: string
  description: string
  price: number
  discountPercentage: number
  rating: number
  stock: number
  brand: string
  category: string
  thumbnail: string
  images: string[]
}

export interface ProductsResponse {
  products: Product[]
  total: number
  skip: number
  limit: number
}

export const productKeys = {
  all: ['products'] as const,
  list: (params: object) => [...productKeys.all, 'list', params] as const,
  detail: (id: number) => [...productKeys.all, 'detail', id] as const,
  search: (q: string) => [...productKeys.all, 'search', q] as const,
}

export const productsApi = {
  getAll: (limit: number, skip: number) =>
    apiClient.get<ProductsResponse>(`/products?limit=${limit}&skip=${skip}`).then(r => r.data),

  getById: (id: number) =>
    apiClient.get<Product>(`/products/${id}`).then(r => r.data),

  search: (q: string) =>
    apiClient.get<ProductsResponse>(`/products/search?q=${q}`).then(r => r.data),

  add: (product: Partial<Product>) =>
    apiClient.post<Product>('/products/add', product).then(r => r.data),
}
```

### ขั้นตอนที่ 3: useCartStore.ts

```typescript
// src/features/cart/useCartStore.ts
import { create } from 'zustand'
import { persist, devtools } from 'zustand/middleware'
import type { Product } from '../products/productApi'

interface CartItem extends Product {
  quantity: number
}

interface CartStore {
  items: CartItem[]
  favoriteIds: number[]
  totalItems: () => number
  totalPrice: () => number
  addToCart: (product: Product) => void
  removeFromCart: (id: number) => void
  updateQty: (id: number, qty: number) => void
  clearCart: () => void
  toggleFavorite: (id: number) => void
  isFavorite: (id: number) => boolean
}

const useCartStore = create<CartStore>()(
  devtools(
    persist(
      (set, get) => ({
        items: [],
        favoriteIds: [],

        totalItems: () => get().items.reduce((sum, item) => sum + item.quantity, 0),
        totalPrice: () => get().items.reduce((sum, item) => sum + item.price * item.quantity, 0),

        addToCart: (product) => set(state => {
          const exists = state.items.find(i => i.id === product.id)
          if (exists) {
            return { items: state.items.map(i => i.id === product.id ? { ...i, quantity: i.quantity + 1 } : i) }
          }
          return { items: [...state.items, { ...product, quantity: 1 }] }
        }),

        removeFromCart: (id) => set(state => ({ items: state.items.filter(i => i.id !== id) })),

        updateQty: (id, qty) => {
          if (qty <= 0) { get().removeFromCart(id); return }
          set(state => ({ items: state.items.map(i => i.id === id ? { ...i, quantity: qty } : i) }))
        },

        clearCart: () => set({ items: [] }),

        toggleFavorite: (id) => set(state => ({
          favoriteIds: state.favoriteIds.includes(id)
            ? state.favoriteIds.filter(fid => fid !== id)
            : [...state.favoriteIds, id],
        })),

        isFavorite: (id) => get().favoriteIds.includes(id),
      }),
      { name: 'product-hub-store' }
    ),
    { name: 'ProductHub' }
  )
)

export default useCartStore
```

### ขั้นตอนที่ 4: App.tsx — Routes ทั้งหมด

```tsx
// src/App.tsx
import { lazy, Suspense } from 'react'
import { Routes, Route } from 'react-router-dom'
import Layout from './shared/components/Layout'
import ProtectedRoute from './shared/components/ProtectedRoute'

const LoginPage = lazy(() => import('./features/auth/LoginPage'))
const ProductsPage = lazy(() => import('./features/products/ProductsPage'))
const ProductDetailPage = lazy(() => import('./features/products/ProductDetailPage'))
const AddProductPage = lazy(() => import('./features/products/AddProductPage'))
const CartPage = lazy(() => import('./features/cart/CartPage'))

const PageLoader = () => (
  <div className="flex justify-center items-center h-64">
    <div className="w-10 h-10 border-4 border-blue-500 border-t-transparent rounded-full animate-spin" />
  </div>
)

export default function App() {
  return (
    <Suspense fallback={<PageLoader />}>
      <Routes>
        <Route path="/login" element={<LoginPage />} />

        <Route path="/" element={
          <ProtectedRoute>
            <Layout />
          </ProtectedRoute>
        }>
          <Route index element={<ProductsPage />} />
          <Route path="products/:id" element={<ProductDetailPage />} />
          <Route path="products/add" element={<AddProductPage />} />
          <Route path="cart" element={<CartPage />} />
        </Route>
      </Routes>
    </Suspense>
  )
}
```

### ขั้นตอนที่ 5: Build และ Deploy

```bash
# Build
npm run build

# ตรวจสอบ
npm run preview

# Deploy Vercel
vercel --prod

# หรือ Push ไป GitHub แล้วเชื่อม Vercel/Netlify
git init
git add .
git commit -m "feat: Arcelik Product Hub — React Course Final Project"
git remote add origin https://github.com/username/product-hub.git
git push -u origin main
```

---

## ต่อยอดหลังคอร์ส: Next.js App Router

เมื่อเชี่ยวชาญ React แล้ว ขั้นต่อไปคือ **Next.js** ซึ่งเพิ่มความสามารถสำคัญ:

| React (คอร์สนี้) | Next.js (ถัดไป) |
|---|---|
| CSR (Client-side Rendering) | CSR + SSR + SSG + RSC |
| Routing ด้วย React Router | File-based Routing |
| ต้องมี Backend แยก | API Routes ในตัว |
| SEO ยาก | SEO ง่าย |
| Deploy เป็น Static Files | Deploy หลายรูปแบบ |

```
แนะนำให้ศึกษาต่อ:
1. Next.js App Router (Vercel)
2. Prisma + PostgreSQL (Database)
3. Better Auth (Authentication)
4. Vitest + Testing Library (Testing)
5. Docker + CI/CD (DevOps)
```

---

## สรุปภาพรวม 5 วัน

### วันที่ 1 — รากฐาน
- React 19 คืออะไร, Virtual DOM, Ecosystem
- Vite + TypeScript Setup
- JSX/TSX และ Function Component

### วันที่ 2 — หัวใจของ React
- Props + State + Events
- Conditional Rendering + List Rendering
- Tailwind CSS v4

### วันที่ 3 — Hooks เชิงลึก
- useEffect, useRef, useMemo, useCallback
- Context API, Custom Hooks
- React 19 Features: Actions, useOptimistic, useFormStatus

### วันที่ 4 — แอปจริง
- React Router v7 — หลายหน้า
- React Hook Form + Zod — ฟอร์มมืออาชีพ
- Zustand + Redux Toolkit — Global State

### วันที่ 5 — Production Ready
- TanStack Query — Server State
- JWT Authentication
- Performance: Lazy Loading, Code Splitting
- Deploy บน Vercel/Netlify
- Final Workshop: Product Hub

---

## สิ่งที่ผู้เรียนสามารถทำได้หลังจบคอร์ส

- ✅ สร้าง React Application จากศูนย์ด้วย Vite + TypeScript
- ✅ ออกแบบและสร้าง Component ที่ Reusable และ Maintainable
- ✅ จัดการ State ทุกระดับ — Local, Context, Global (Zustand/Redux)
- ✅ เชื่อมต่อ API และจัดการ Server State ด้วย TanStack Query
- ✅ สร้าง Multi-page Application ด้วย React Router
- ✅ จัดการฟอร์มซับซ้อนด้วย React Hook Form + Zod
- ✅ ทำระบบ Authentication ด้วย JWT
- ✅ ปรับแต่งประสิทธิภาพและ Deploy สู่ Production

---

## แหล่งอ้างอิงสำหรับการเรียนรู้ต่อ

**เอกสารทางการ:**
- [React Docs](https://react.dev) — เอกสาร React อย่างเป็นทางการ
- [TanStack Query Docs](https://tanstack.com/query/latest) — เอกสาร TanStack Query
- [TypeScript Handbook](https://www.typescriptlang.org/docs/) — เอกสาร TypeScript
- [Tailwind CSS Docs](https://tailwindcss.com) — เอกสาร Tailwind

**คอร์สและบทความ:**
- [React Road](https://www.roadmap.sh/react) — React Learning Roadmap
- [Josh Comeau's Blog](https://www.joshwcomeau.com) — บทความ React เชิงลึก
- [Kent C. Dodds Blog](https://kentcdodds.com/blog) — Best Practices จาก React Expert

**ช่องทางติดต่อวิทยากร:**
- **IT Genius Engineering**: [www.itgenius.co.th](https://www.itgenius.co.th)
- **Line ID**: @itgenius
- **โทร**: 02-570-8449

---

*ขอบคุณทุกท่านที่เข้าร่วมอบรมหลักสูตร Basic to Intermediate React.js*
*บริษัท อาร์เซลิก ฮิตาชิ โฮม แอพพลายแอนซ์ เซลส์ (ประเทศไทย) จำกัด*
*8–12 มิถุนายน 2569*
