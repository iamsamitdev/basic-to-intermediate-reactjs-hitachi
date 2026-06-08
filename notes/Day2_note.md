# หลักสูตร Basic to Intermediate React.js — วันที่ 2

## Component, Props, State, Event และการจัดสไตล์ด้วย Tailwind CSS v4

**วันที่อบรม:** วันอังคารที่ 9 มิถุนายน 2569 | เวลา 09:30–16:30 น.
**สถานที่:** บริษัท อาร์เซลิก ฮิตาชิ โฮม แอพพลายแอนซ์ เซลส์ (ประเทศไทย) จำกัด
**วิทยากร:** อาจารย์สามิตร โกยม | IT Genius Engineering Co., Ltd.

---

## บทนำ

วันที่สองเป็นหัวใจสำคัญของ React — เราจะเรียนรู้กลไกที่ทำให้ React ทรงพลัง ได้แก่ **Props** (การส่งข้อมูลระหว่าง Component), **State** (ข้อมูลที่เปลี่ยนแปลงได้), **Events** (การโต้ตอบกับผู้ใช้) และ **Tailwind CSS v4** สำหรับการตกแต่งหน้าตา

---

## Module 2.1: React Component เชิงลึก

### ทบทวน: Function Component

```tsx
// รูปแบบ Function Component มาตรฐาน
function ComponentName() {
  // Logic ของ Component อยู่ที่นี่
  return (
    // JSX ที่จะแสดงผล
    <div>...</div>
  )
}

export default ComponentName
```

### หลักการออกแบบ Component ที่ดี

#### Single Responsibility Principle

แต่ละ Component ควรทำ "หน้าที่เดียว" ให้ดีที่สุด:

```tsx
// ❌ Bad — Component ทำหลายอย่าง (ยาก Maintain)
function UserDashboard() {
  return (
    <div>
      {/* Header มี Logic เยอะ */}
      {/* Product List มี Logic เยอะ */}
      {/* User Profile มี Logic เยอะ */}
    </div>
  )
}

// ✅ Good — แยก Component ออกมา
function UserDashboard() {
  return (
    <div>
      <Header />
      <ProductList />
      <UserProfile />
    </div>
  )
}
```

### Component Composition และ children

`children` คือ Props พิเศษที่ React มีให้ — ใช้สำหรับส่ง JSX เข้าไปใน Component

```tsx
// ─── สร้าง Card Component ที่รับ children ───
interface CardProps {
  title: string
  children: React.ReactNode    // ReactNode = JSX หรืออะไรก็ได้ที่ Render ได้
}

function Card({ title, children }: CardProps) {
  return (
    <div className="card">
      <div className="card-header">
        <h3>{title}</h3>
      </div>
      <div className="card-body">
        {children}    {/* JSX ที่ส่งเข้ามาจะ render ที่นี่ */}
      </div>
    </div>
  )
}

// ─── ใช้งาน ───
function App() {
  return (
    <Card title="ข้อมูลพนักงาน">
      <p>ชื่อ: สมชาย</p>
      <p>แผนก: IT</p>
      <button>ดูรายละเอียด</button>
    </Card>
  )
}
```

### การจัดโครงสร้างโฟลเดอร์

```
src/
├── components/           # Reusable Components (ใช้ได้ทั่วทั้งแอป)
│   ├── ui/               # ─── Basic UI ───
│   │   ├── Button.tsx
│   │   ├── Card.tsx
│   │   ├── Input.tsx
│   │   └── Badge.tsx
│   └── layout/           # ─── Layout Components ───
│       ├── Header.tsx
│       ├── Sidebar.tsx
│       └── Footer.tsx
├── features/             # Feature-specific Components
│   ├── products/
│   │   ├── ProductCard.tsx
│   │   └── ProductList.tsx
│   └── users/
│       ├── UserProfile.tsx
│       └── UserAvatar.tsx
├── pages/                # หน้าต่าง ๆ
│   ├── HomePage.tsx
│   ├── ProductPage.tsx
│   └── LoginPage.tsx
└── types/                # TypeScript Types
    └── index.ts
```

---

## Module 2.2: การทำงานกับ Props ด้วย TypeScript

### Props คืออะไร?

**Props** (Properties) คือข้อมูลที่ Component หนึ่งส่งให้ Component อื่น เหมือน **Arguments ของฟังก์ชัน**

```
Component แม่ (Parent)
      │
      │  ส่ง Props → { name: "สมชาย", age: 30 }
      ▼
Component ลูก (Child)
      │
      │  รับและใช้ Props
      ▼
  แสดงผล "สมชาย อายุ 30 ปี"
```

### การกำหนด Type ให้ Props

```tsx
// ─── วิธีที่ 1: ใช้ interface (แนะนำ) ───
interface ProductCardProps {
  id: number
  name: string
  price: number
  imageUrl: string
  description?: string     // ? = Optional
  isOnSale?: boolean
}

function ProductCard({ id, name, price, imageUrl, description, isOnSale = false }: ProductCardProps) {
  return (
    <div>
      <img src={imageUrl} alt={name} />
      <h3>{name}</h3>
      {description && <p>{description}</p>}
      <p>ราคา: {price.toLocaleString()} บาท</p>
      {isOnSale && <span>🔥 ลดราคา!</span>}
    </div>
  )
}

// ─── ใช้งาน ───
<ProductCard
  id={1}
  name="เครื่องล้างจาน"
  price={15900}
  imageUrl="/dishwasher.jpg"
  isOnSale={true}
/>
```

### Default Props

```tsx
interface ButtonProps {
  label: string
  variant?: 'primary' | 'secondary' | 'danger'
  size?: 'sm' | 'md' | 'lg'
  disabled?: boolean
  onClick?: () => void
}

// ─── กำหนด Default ใน Destructuring ───
function Button({
  label,
  variant = 'primary',
  size = 'md',
  disabled = false,
  onClick,
}: ButtonProps) {
  return (
    <button
      onClick={onClick}
      disabled={disabled}
      className={`btn btn-${variant} btn-${size}`}
    >
      {label}
    </button>
  )
}

// ─── ใช้งาน ───
<Button label="บันทึก" />                            // variant='primary', size='md'
<Button label="ยกเลิก" variant="secondary" />        // size='md'
<Button label="ลบ" variant="danger" size="sm" />
```

### การส่งฟังก์ชัน (Callback) ผ่าน Props

```tsx
// ─── Parent Component ───
function ShoppingCart() {
  const handleRemoveItem = (itemId: number) => {
    console.log(`ลบสินค้า ID: ${itemId}`)
  }

  const handleQuantityChange = (itemId: number, newQty: number) => {
    console.log(`เปลี่ยนจำนวน ID: ${itemId} เป็น: ${newQty}`)
  }

  return (
    <CartItem
      id={1}
      name="เครื่องซักผ้า"
      quantity={2}
      price={18900}
      onRemove={handleRemoveItem}
      onQuantityChange={handleQuantityChange}
    />
  )
}

// ─── Child Component ───
interface CartItemProps {
  id: number
  name: string
  quantity: number
  price: number
  onRemove: (id: number) => void
  onQuantityChange: (id: number, quantity: number) => void
}

function CartItem({ id, name, quantity, price, onRemove, onQuantityChange }: CartItemProps) {
  return (
    <div>
      <span>{name}</span>
      <button onClick={() => onQuantityChange(id, quantity - 1)}>-</button>
      <span>{quantity}</span>
      <button onClick={() => onQuantityChange(id, quantity + 1)}>+</button>
      <span>{(price * quantity).toLocaleString()} บาท</span>
      <button onClick={() => onRemove(id)}>🗑️</button>
    </div>
  )
}
```

### Spread Props

```tsx
interface InputProps {
  label: string
  error?: string
  // ...แล้วยอมรับ attribute ทั้งหมดของ <input> HTML
}

// รับ HTML attributes ทุกตัวของ input ด้วย React.InputHTMLAttributes
type FormInputProps = InputProps & React.InputHTMLAttributes<HTMLInputElement>

function FormInput({ label, error, ...inputProps }: FormInputProps) {
  return (
    <div>
      <label>{label}</label>
      <input {...inputProps} />          {/* Spread ส่ง attributes ทั้งหมดไป */}
      {error && <span className="error">{error}</span>}
    </div>
  )
}

// ─── ใช้งาน ───
<FormInput
  label="อีเมล"
  type="email"
  placeholder="กรอกอีเมล"
  required
  error="กรุณากรอกอีเมล"
  onChange={(e) => console.log(e.target.value)}
/>
```

---

## Module 2.3: การจัดการ State ด้วย useState

### State คืออะไร?

**State** คือข้อมูลที่ **เปลี่ยนแปลงได้** และเมื่อเปลี่ยน React จะ **Render Component ใหม่** เพื่อแสดงผลที่อัปเดต

```
ข้อมูลใน Component มี 2 แบบ:

1. Props  — ข้อมูลที่รับจาก Parent (แก้ไขไม่ได้ใน Component ลูก)
2. State  — ข้อมูลของ Component เอง (แก้ไขได้, ทำให้ Re-render)
```

### useState Syntax

```tsx
import { useState } from 'react'

// const [currentValue, setterFunction] = useState(initialValue)
const [count, setCount] = useState(0)
const [name, setName] = useState('')
const [isOpen, setIsOpen] = useState(false)
```

### 🛠️ ตัวอย่างที่ 1: Counter

```tsx
import { useState } from 'react'

function Counter() {
  const [count, setCount] = useState<number>(0)    // TypeScript กำหนด Type

  const increment = () => setCount(count + 1)
  const decrement = () => setCount(count - 1)
  const reset = () => setCount(0)

  return (
    <div>
      <h2>Counter: {count}</h2>
      <button onClick={increment}>+1</button>
      <button onClick={decrement}>-1</button>
      <button onClick={reset}>Reset</button>
    </div>
  )
}
```

### การอัปเดต State อย่างถูกต้อง (Immutability)

```tsx
// ─── ❌ ผิด — แก้ Object โดยตรง (React ไม่รู้ว่าเปลี่ยน ไม่ Re-render) ───
const [user, setUser] = useState({ name: 'สมชาย', age: 30 })

// อย่าทำแบบนี้!
user.age = 31
setUser(user)      // React เห็นว่า Object reference เหมือนเดิม → ไม่ Re-render

// ─── ✅ ถูก — สร้าง Object ใหม่เสมอ ───
setUser({ ...user, age: 31 })    // Spread Operator สร้าง Object ใหม่

// ─── ❌ ผิด — แก้ Array โดยตรง ───
const [items, setItems] = useState(['a', 'b', 'c'])
items.push('d')
setItems(items)    // ❌ ไม่ Re-render

// ─── ✅ ถูก — สร้าง Array ใหม่ ───
setItems([...items, 'd'])         // เพิ่มท้าย
setItems(items.filter(x => x !== 'b'))  // ลบ
setItems(items.map(x => x === 'a' ? 'A' : x))  // แก้ไข
```

### การจัดการ State ที่เป็น Object

```tsx
interface UserProfile {
  name: string
  email: string
  age: number
  isAdmin: boolean
}

function ProfileEditor() {
  const [profile, setProfile] = useState<UserProfile>({
    name: 'สมชาย',
    email: 'somchai@example.com',
    age: 30,
    isAdmin: false,
  })

  // ─── อัปเดตทีละ Field ───
  const updateName = (newName: string) => {
    setProfile(prev => ({ ...prev, name: newName }))
    // prev = State ก่อนหน้า, ปลอดภัยกว่าการอ้างถึง profile โดยตรง
  }

  const toggleAdmin = () => {
    setProfile(prev => ({ ...prev, isAdmin: !prev.isAdmin }))
  }

  return (
    <div>
      <input
        value={profile.name}
        onChange={(e) => updateName(e.target.value)}
      />
      <button onClick={toggleAdmin}>
        {profile.isAdmin ? 'ถอดสิทธิ์ Admin' : 'ให้สิทธิ์ Admin'}
      </button>
    </div>
  )
}
```

### การจัดการ State ที่เป็น Array

```tsx
interface TodoItem {
  id: number
  text: string
  completed: boolean
}

function TodoApp() {
  const [todos, setTodos] = useState<TodoItem[]>([
    { id: 1, text: 'เรียน React', completed: false },
    { id: 2, text: 'ทำ Workshop', completed: false },
  ])
  const [inputText, setInputText] = useState('')

  // ─── เพิ่ม Todo ───
  const addTodo = () => {
    if (!inputText.trim()) return
    const newTodo: TodoItem = {
      id: Date.now(),       // ใช้ timestamp เป็น ID ชั่วคราว
      text: inputText,
      completed: false,
    }
    setTodos(prev => [...prev, newTodo])
    setInputText('')        // ล้าง Input
  }

  // ─── Toggle สถานะ ───
  const toggleTodo = (id: number) => {
    setTodos(prev =>
      prev.map(todo =>
        todo.id === id ? { ...todo, completed: !todo.completed } : todo
      )
    )
  }

  // ─── ลบ Todo ───
  const removeTodo = (id: number) => {
    setTodos(prev => prev.filter(todo => todo.id !== id))
  }

  return (
    <div>
      <input
        value={inputText}
        onChange={(e) => setInputText(e.target.value)}
        placeholder="เพิ่มงาน..."
      />
      <button onClick={addTodo}>เพิ่ม</button>
      <ul>
        {todos.map(todo => (
          <li key={todo.id}>
            <input
              type="checkbox"
              checked={todo.completed}
              onChange={() => toggleTodo(todo.id)}
            />
            <span style={{ textDecoration: todo.completed ? 'line-through' : 'none' }}>
              {todo.text}
            </span>
            <button onClick={() => removeTodo(todo.id)}>ลบ</button>
          </li>
        ))}
      </ul>
    </div>
  )
}
```

### Lifting State Up (ยก State ขึ้นไปไว้ Component แม่)

เมื่อ Component หลาย ๆ ตัวต้องการ **ใช้ State ร่วมกัน** ให้ยก State ขึ้นไปไว้ที่ **Parent ร่วม**:

```tsx
// ─── ❌ ปัญหา: State อยู่คนละ Component ซิงค์กันไม่ได้ ───
function FilterButton() {
  const [filter, setFilter] = useState('all')  // State แยก
  // ...
}
function ProductList() {
  // ไม่รู้ว่า filter เป็นอะไร!
}

// ─── ✅ แก้: ยก State ขึ้นมาที่ Parent ───
function ProductPage() {
  const [filter, setFilter] = useState<'all' | 'active' | 'inactive'>('all')

  return (
    <div>
      {/* ส่ง State และ Setter ลงไปเป็น Props */}
      <FilterButton currentFilter={filter} onFilterChange={setFilter} />
      <ProductList filter={filter} />
    </div>
  )
}

interface FilterButtonProps {
  currentFilter: string
  onFilterChange: (filter: 'all' | 'active' | 'inactive') => void
}

function FilterButton({ currentFilter, onFilterChange }: FilterButtonProps) {
  return (
    <div>
      {(['all', 'active', 'inactive'] as const).map(f => (
        <button
          key={f}
          onClick={() => onFilterChange(f)}
          style={{ fontWeight: currentFilter === f ? 'bold' : 'normal' }}
        >
          {f}
        </button>
      ))}
    </div>
  )
}
```

---

## Module 2.4: การจัดการ Event

### Event Handler พื้นฐาน

```tsx
function EventDemo() {
  // ─── Click Event ───
  const handleClick = () => {
    alert('ถูกคลิก!')
  }

  // ─── Click Event พร้อม Parameter ───
  const handleItemClick = (id: number, name: string) => {
    console.log(`คลิกที่ Item ${id}: ${name}`)
  }

  // ─── Change Event (Input) ───
  const handleChange = (e: React.ChangeEvent<HTMLInputElement>) => {
    console.log('ค่าที่พิมพ์:', e.target.value)
  }

  // ─── Submit Event (Form) ───
  const handleSubmit = (e: React.FormEvent<HTMLFormElement>) => {
    e.preventDefault()      // ป้องกัน Browser reload
    console.log('Form ถูก Submit')
  }

  return (
    <div>
      <button onClick={handleClick}>คลิกฉัน</button>
      <button onClick={() => handleItemClick(1, 'สินค้า A')}>สินค้า A</button>
      <input onChange={handleChange} placeholder="พิมพ์อะไรก็ได้" />
      <form onSubmit={handleSubmit}>
        <button type="submit">ส่งฟอร์ม</button>
      </form>
    </div>
  )
}
```

### TypeScript Types สำหรับ Events

```typescript
// Event Types ที่ใช้บ่อยใน React
React.MouseEvent<HTMLButtonElement>     // คลิกบน Button
React.MouseEvent<HTMLDivElement>        // คลิกบน Div
React.ChangeEvent<HTMLInputElement>     // เปลี่ยนค่า Input
React.ChangeEvent<HTMLTextAreaElement>  // เปลี่ยนค่า Textarea
React.ChangeEvent<HTMLSelectElement>    // เลือก Option ใน Select
React.FormEvent<HTMLFormElement>        // Submit Form
React.KeyboardEvent<HTMLInputElement>   // กดปุ่มบน Input
React.FocusEvent<HTMLInputElement>      // Focus/Blur บน Input
```

### 🛠️ ตัวอย่าง: Form จัดการ Event ครบถ้วน

```tsx
import { useState } from 'react'

interface FormData {
  username: string
  password: string
  role: 'admin' | 'user'
  remember: boolean
}

function LoginForm() {
  const [formData, setFormData] = useState<FormData>({
    username: '',
    password: '',
    role: 'user',
    remember: false,
  })

  const handleInputChange = (e: React.ChangeEvent<HTMLInputElement>) => {
    const { name, value, type, checked } = e.target
    setFormData(prev => ({
      ...prev,
      [name]: type === 'checkbox' ? checked : value,
    }))
  }

  const handleSelectChange = (e: React.ChangeEvent<HTMLSelectElement>) => {
    setFormData(prev => ({ ...prev, role: e.target.value as FormData['role'] }))
  }

  const handleSubmit = (e: React.FormEvent<HTMLFormElement>) => {
    e.preventDefault()
    console.log('ส่งข้อมูล:', formData)
    alert(`เข้าสู่ระบบในฐานะ: ${formData.username}`)
  }

  return (
    <form onSubmit={handleSubmit} style={{ maxWidth: 400, margin: '0 auto' }}>
      <h2>เข้าสู่ระบบ</h2>

      <div>
        <label>ชื่อผู้ใช้:</label>
        <input
          type="text"
          name="username"
          value={formData.username}
          onChange={handleInputChange}
          required
        />
      </div>

      <div>
        <label>รหัสผ่าน:</label>
        <input
          type="password"
          name="password"
          value={formData.password}
          onChange={handleInputChange}
          required
        />
      </div>

      <div>
        <label>บทบาท:</label>
        <select value={formData.role} onChange={handleSelectChange}>
          <option value="user">ผู้ใช้ทั่วไป</option>
          <option value="admin">ผู้ดูแลระบบ</option>
        </select>
      </div>

      <div>
        <label>
          <input
            type="checkbox"
            name="remember"
            checked={formData.remember}
            onChange={handleInputChange}
          />
          {' '}จดจำการเข้าสู่ระบบ
        </label>
      </div>

      <button type="submit">เข้าสู่ระบบ</button>
    </form>
  )
}
```

---

## Module 2.5: Conditional Rendering และการแสดงผลรายการ

### Conditional Rendering

```tsx
interface User {
  name: string
  isLoggedIn: boolean
  role: 'admin' | 'user'
  unreadMessages: number
}

function Dashboard({ user }: { user: User }) {
  return (
    <div>
      {/* ─── วิธีที่ 1: if statement (ใน logic ก่อน return) ───  */}
      {/* if (!user.isLoggedIn) return <LoginPage /> */}

      {/* ─── วิธีที่ 2: Ternary Operator ─── */}
      <h1>ยินดีต้อนรับ {user.isLoggedIn ? user.name : 'ผู้เยี่ยมชม'}</h1>

      {/* ─── วิธีที่ 3: && (Short-circuit) ─── */}
      {user.isLoggedIn && <button>ออกจากระบบ</button>}

      {/* ─── วิธีที่ 4: แสดงเฉพาะเมื่อมีเงื่อนไขซับซ้อน ─── */}
      {user.isLoggedIn && user.role === 'admin' && (
        <div>
          <h2>⚙️ Admin Panel</h2>
          <button>จัดการผู้ใช้</button>
        </div>
      )}

      {/* ─── ⚠️ ระวัง: 0 จะถูก Render! ─── */}
      {/* ❌ {user.unreadMessages && <Badge count={user.unreadMessages} />} */}
      {/* ✅ */}
      {user.unreadMessages > 0 && <span>📫 {user.unreadMessages} ข้อความ</span>}
    </div>
  )
}
```

### การแสดงผลรายการด้วย map()

```tsx
interface Product {
  id: number
  name: string
  price: number
  category: string
  inStock: boolean
}

const products: Product[] = [
  { id: 1, name: 'เครื่องล้างจาน', price: 15900, category: 'kitchen', inStock: true },
  { id: 2, name: 'เครื่องซักผ้า', price: 18900, category: 'laundry', inStock: false },
  { id: 3, name: 'ตู้เย็น', price: 12900, category: 'kitchen', inStock: true },
]

function ProductList() {
  return (
    <ul>
      {products.map((product) => (
        // key ต้องไม่ซ้ำกัน และควรใช้ ID จริง ไม่ใช้ index
        <li key={product.id}>
          <strong>{product.name}</strong> — {product.price.toLocaleString()} บาท
          {!product.inStock && <span style={{ color: 'red' }}> (หมดสต็อก)</span>}
        </li>
      ))}
    </ul>
  )
}
```

### ความสำคัญของ key

```tsx
// ─── ❌ ใช้ index เป็น key — มีปัญหาเมื่อ List เปลี่ยนลำดับ ───
{items.map((item, index) => (
  <ItemCard key={index} item={item} />   // ❌ ปัญหาเมื่อ sort หรือ filter
))}

// ─── ✅ ใช้ ID ที่ไม่ซ้ำกัน ───
{items.map((item) => (
  <ItemCard key={item.id} item={item} />   // ✅ ถูกต้อง
))}
```

### Filter และ Sort ก่อน Render

```tsx
function FilterableProductList() {
  const [filter, setFilter] = useState<'all' | 'kitchen' | 'laundry'>('all')
  const [sortBy, setSortBy] = useState<'name' | 'price'>('name')
  const [showInStockOnly, setShowInStockOnly] = useState(false)

  // ─── คำนวณ List ที่จะแสดง ───
  const displayedProducts = products
    .filter(p => filter === 'all' || p.category === filter)
    .filter(p => !showInStockOnly || p.inStock)
    .sort((a, b) => {
      if (sortBy === 'name') return a.name.localeCompare(b.name)
      return a.price - b.price
    })

  return (
    <div>
      {/* Controls */}
      <select value={filter} onChange={e => setFilter(e.target.value as typeof filter)}>
        <option value="all">ทั้งหมด</option>
        <option value="kitchen">ครัว</option>
        <option value="laundry">ซักผ้า</option>
      </select>

      <select value={sortBy} onChange={e => setSortBy(e.target.value as typeof sortBy)}>
        <option value="name">เรียงตามชื่อ</option>
        <option value="price">เรียงตามราคา</option>
      </select>

      <label>
        <input
          type="checkbox"
          checked={showInStockOnly}
          onChange={e => setShowInStockOnly(e.target.checked)}
        />
        {' '}แสดงเฉพาะที่มีสต็อก
      </label>

      {/* List */}
      <p>แสดง {displayedProducts.length} รายการ</p>
      {displayedProducts.map(product => (
        <div key={product.id}>
          {product.name} — {product.price.toLocaleString()} บาท
        </div>
      ))}
    </div>
  )
}
```

---

## Module 2.6: การจัดสไตล์ด้วย Tailwind CSS v4

### ทำไม Tailwind CSS?

| แนวทาง | ตัวอย่าง | ข้อดี | ข้อเสีย |
|---|---|---|---|
| **Inline Style** | `style={{ color: 'red' }}` | ง่าย, scoped | ไม่มี responsive, verbose |
| **CSS Modules** | `styles.button` | Scoped, ยืดหยุ่น | ต้องสลับไฟล์ |
| **Tailwind CSS** | `className="text-red-500"` | เร็ว, consistent, responsive ง่าย | ต้องเรียนชื่อ class |

### ติดตั้ง Tailwind CSS v4 กับ Vite

```bash
pnpm add tailwindcss @tailwindcss/vite
```

แก้ไข `vite.config.ts`:

```typescript
// vite.config.ts
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'
import tailwindcss from '@tailwindcss/vite'

export default defineConfig({
  plugins: [
    react(),
    tailwindcss(),      // เพิ่ม Tailwind Plugin
  ],
})
```

แก้ไข `src/index.css` (แทนที่เนื้อหาเดิมทั้งหมด):

```css
/* src/index.css */
@import "tailwindcss";
```

### Utility Classes พื้นฐาน

```tsx
// ─── Layout ───
<div className="flex items-center justify-between gap-4">
  {/* flex = display:flex, items-center = align-items:center */}
  {/* justify-between = justify-content:space-between, gap-4 = gap:1rem */}
</div>

<div className="grid grid-cols-3 gap-6">
  {/* grid = display:grid, grid-cols-3 = 3 columns, gap-6 = gap:1.5rem */}
</div>

// ─── Spacing ───
<div className="p-4 m-2 px-6 py-3 mt-8 mb-4">
  {/* p=padding, m=margin, x=horizontal, y=vertical */}
  {/* 1 unit = 0.25rem = 4px */}
</div>

// ─── Typography ───
<h1 className="text-3xl font-bold text-gray-900">หัวข้อใหญ่</h1>
<p className="text-sm text-gray-500 leading-relaxed">เนื้อหา</p>

// ─── Colors ───
<div className="bg-blue-500 text-white">
  {/* bg-{color}-{shade}: 50, 100, 200, 300, 400, 500, 600, 700, 800, 900 */}
</div>

// ─── Borders & Rounded ───
<div className="border border-gray-200 rounded-lg shadow-sm">...</div>
<div className="border-2 border-blue-500 rounded-full">...</div>

// ─── Sizing ───
<div className="w-full h-screen max-w-2xl min-h-0">...</div>
```

### Responsive Design

Tailwind ใช้ prefix สำหรับ Breakpoints:

```
sm:   640px+
md:   768px+
lg:   1024px+
xl:   1280px+
2xl:  1536px+
```

```tsx
// ─── Mobile First: ไม่มี prefix = mobile, มี prefix = tablet/desktop ───
<div className="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-4">
  {/* มือถือ: 1 คอลัมน์ */}
  {/* Tablet: 2 คอลัมน์ */}
  {/* Desktop: 3 คอลัมน์ */}
</div>

<h1 className="text-xl md:text-3xl lg:text-5xl font-bold">
  {/* ขนาดตัวอักษรตามหน้าจอ */}
</h1>
```

### Hover และ State

```tsx
<button className="
  bg-blue-500 text-white
  hover:bg-blue-600           /* เมื่อ hover */
  active:bg-blue-700          /* เมื่อคลิกค้าง */
  focus:outline-none focus:ring-2 focus:ring-blue-300   /* เมื่อ focus */
  disabled:opacity-50 disabled:cursor-not-allowed        /* เมื่อ disabled */
  transition-colors duration-200   /* Animation */
  px-4 py-2 rounded-lg font-medium
">
  คลิกฉัน
</button>
```

### Dark Mode

```css
/* index.css — เปิดใช้ Dark Mode */
@import "tailwindcss";
@variant dark (&:where(.dark, .dark *));
```

```tsx
<div className="bg-white dark:bg-gray-900 text-gray-900 dark:text-white">
  <h1 className="text-2xl font-bold">หัวข้อ</h1>
  <p className="text-gray-600 dark:text-gray-300">เนื้อหา</p>
</div>
```

### 🛠️ ตัวอย่าง: สร้าง Product Card ด้วย Tailwind

```tsx
interface Product {
  id: number
  name: string
  price: number
  imageUrl: string
  rating: number
  badge?: 'new' | 'sale' | 'popular'
}

function ProductCard({ name, price, imageUrl, rating, badge }: Product) {
  const badgeConfig = {
    new: { text: 'ใหม่', className: 'bg-green-500' },
    sale: { text: 'ลดราคา', className: 'bg-red-500' },
    popular: { text: 'ยอดนิยม', className: 'bg-orange-500' },
  }

  return (
    <div className="bg-white rounded-xl shadow-md overflow-hidden hover:shadow-xl transition-shadow duration-300">
      {/* Image */}
      <div className="relative">
        <img
          src={imageUrl}
          alt={name}
          className="w-full h-48 object-cover"
        />
        {badge && (
          <span className={`absolute top-2 left-2 ${badgeConfig[badge].className} text-white text-xs font-bold px-2 py-1 rounded-full`}>
            {badgeConfig[badge].text}
          </span>
        )}
      </div>

      {/* Content */}
      <div className="p-4">
        <h3 className="text-lg font-semibold text-gray-900 mb-1">{name}</h3>

        {/* Rating */}
        <div className="flex items-center gap-1 mb-3">
          {[1, 2, 3, 4, 5].map(star => (
            <span
              key={star}
              className={star <= rating ? 'text-yellow-400' : 'text-gray-300'}
            >
              ★
            </span>
          ))}
          <span className="text-sm text-gray-500 ml-1">({rating}/5)</span>
        </div>

        {/* Price */}
        <div className="flex items-center justify-between">
          <span className="text-2xl font-bold text-blue-600">
            ฿{price.toLocaleString()}
          </span>
          <button className="bg-blue-500 hover:bg-blue-600 active:bg-blue-700 text-white text-sm font-medium px-4 py-2 rounded-lg transition-colors duration-200">
            เพิ่มในตะกร้า
          </button>
        </div>
      </div>
    </div>
  )
}
```

---

## 🎯 Workshop ท้ายวัน: สร้างระบบ "Product Catalog" พร้อม Filter

### โจทย์

สร้างหน้า Product Catalog ของ Arcelik Hitachi ที่:
1. แสดงสินค้าในรูปแบบ Grid 3 คอลัมน์
2. Filter ตามหมวดหมู่ได้
3. ค้นหาตามชื่อได้
4. เพิ่มสินค้าลงตะกร้าได้ (พร้อมแสดงจำนวน)

### ขั้นตอนที่ 1: กำหนด Types

```typescript
// src/types/product.ts
export interface Product {
  id: number
  name: string
  nameEn: string
  category: 'refrigerator' | 'washing-machine' | 'dishwasher' | 'air-conditioner'
  price: number
  image: string
  rating: number
  inStock: boolean
  badge?: 'new' | 'sale' | 'popular'
}

export const CATEGORIES = {
  all: 'ทั้งหมด',
  refrigerator: 'ตู้เย็น',
  'washing-machine': 'เครื่องซักผ้า',
  dishwasher: 'เครื่องล้างจาน',
  'air-conditioner': 'เครื่องปรับอากาศ',
} as const
```

### ขั้นตอนที่ 2: สร้างข้อมูล Mock

```typescript
// src/data/products.ts
import type { Product } from '../types/product'

export const products: Product[] = [
  {
    id: 1,
    name: 'ตู้เย็น 2 ประตู',
    nameEn: 'Double Door Refrigerator',
    category: 'refrigerator',
    price: 12900,
    image: 'https://placehold.co/400x300?text=Refrigerator',
    rating: 4,
    inStock: true,
    badge: 'popular',
  },
  {
    id: 2,
    name: 'เครื่องซักผ้าฝาหน้า 9 กก.',
    nameEn: 'Front Load 9kg',
    category: 'washing-machine',
    price: 18900,
    image: 'https://placehold.co/400x300?text=WashingMachine',
    rating: 5,
    inStock: true,
    badge: 'new',
  },
  {
    id: 3,
    name: 'เครื่องล้างจาน 13 ชุด',
    nameEn: 'Dishwasher 13 Place',
    category: 'dishwasher',
    price: 15900,
    image: 'https://placehold.co/400x300?text=Dishwasher',
    rating: 4,
    inStock: false,
  },
  {
    id: 4,
    name: 'แอร์ Inverter 12000 BTU',
    nameEn: 'Inverter AC 12000 BTU',
    category: 'air-conditioner',
    price: 25900,
    image: 'https://placehold.co/400x300?text=AirCon',
    rating: 5,
    inStock: true,
    badge: 'sale',
  },
  {
    id: 5,
    name: 'ตู้เย็น Side by Side',
    nameEn: 'Side by Side Refrigerator',
    category: 'refrigerator',
    price: 35900,
    image: 'https://placehold.co/400x300?text=SideBySide',
    rating: 4,
    inStock: true,
  },
  {
    id: 6,
    name: 'เครื่องซักผ้าฝาบน 10 กก.',
    nameEn: 'Top Load 10kg',
    category: 'washing-machine',
    price: 8900,
    image: 'https://placehold.co/400x300?text=TopLoad',
    rating: 3,
    inStock: true,
    badge: 'popular',
  },
]
```

### ขั้นตอนที่ 3: สร้าง ProductCard Component

```tsx
// src/components/ProductCard.tsx
import { useState } from 'react'
import type { Product } from '../types/product'

interface ProductCardProps {
  product: Product
  onAddToCart: (product: Product) => void
}

const BADGE_STYLE: Record<string, string> = {
  new: 'bg-green-500',
  sale: 'bg-red-500',
  popular: 'bg-orange-500',
}

const BADGE_TEXT: Record<string, string> = {
  new: 'ใหม่',
  sale: 'ลดราคา',
  popular: 'ยอดนิยม',
}

export default function ProductCard({ product, onAddToCart }: ProductCardProps) {
  const [isAdded, setIsAdded] = useState(false)

  const handleAddToCart = () => {
    onAddToCart(product)
    setIsAdded(true)
    setTimeout(() => setIsAdded(false), 1500)
  }

  return (
    <div className="bg-white rounded-xl shadow-md overflow-hidden hover:shadow-xl transition-all duration-300 hover:-translate-y-1">
      <div className="relative">
        <img src={product.image} alt={product.name} className="w-full h-48 object-cover" />
        {product.badge && (
          <span className={`absolute top-2 left-2 ${BADGE_STYLE[product.badge]} text-white text-xs font-bold px-3 py-1 rounded-full`}>
            {BADGE_TEXT[product.badge]}
          </span>
        )}
        {!product.inStock && (
          <div className="absolute inset-0 bg-black/40 flex items-center justify-center">
            <span className="text-white font-bold text-lg">หมดสต็อก</span>
          </div>
        )}
      </div>

      <div className="p-4">
        <h3 className="font-semibold text-gray-900 mb-1">{product.name}</h3>
        <p className="text-sm text-gray-400 mb-2">{product.nameEn}</p>

        <div className="flex items-center gap-1 mb-3">
          {'★★★★★'.split('').map((star, i) => (
            <span key={i} className={i < product.rating ? 'text-yellow-400' : 'text-gray-200'}>
              {star}
            </span>
          ))}
        </div>

        <div className="flex items-center justify-between">
          <span className="text-xl font-bold text-blue-600">
            ฿{product.price.toLocaleString()}
          </span>
          <button
            onClick={handleAddToCart}
            disabled={!product.inStock}
            className={`
              text-sm font-medium px-3 py-2 rounded-lg transition-all duration-200
              ${product.inStock
                ? isAdded
                  ? 'bg-green-500 text-white'
                  : 'bg-blue-500 hover:bg-blue-600 text-white'
                : 'bg-gray-200 text-gray-400 cursor-not-allowed'
              }
            `}
          >
            {isAdded ? '✅ เพิ่มแล้ว' : '🛒 เพิ่มในตะกร้า'}
          </button>
        </div>
      </div>
    </div>
  )
}
```

### ขั้นตอนที่ 4: สร้าง App หลัก

```tsx
// src/App.tsx
import { useState } from 'react'
import ProductCard from './components/ProductCard'
import { products } from './data/products'
import { CATEGORIES } from './types/product'
import type { Product } from './types/product'

type CategoryKey = keyof typeof CATEGORIES

function App() {
  const [selectedCategory, setSelectedCategory] = useState<CategoryKey>('all')
  const [searchText, setSearchText] = useState('')
  const [cartCount, setCartCount] = useState(0)

  const handleAddToCart = (product: Product) => {
    setCartCount(prev => prev + 1)
  }

  const filteredProducts = products
    .filter(p => selectedCategory === 'all' || p.category === selectedCategory)
    .filter(p =>
      p.name.toLowerCase().includes(searchText.toLowerCase()) ||
      p.nameEn.toLowerCase().includes(searchText.toLowerCase())
    )

  return (
    <div className="min-h-screen bg-gray-50">
      {/* Header */}
      <header className="bg-white shadow-sm sticky top-0 z-10">
        <div className="max-w-6xl mx-auto px-4 py-4 flex items-center justify-between">
          <div>
            <h1 className="text-xl font-bold text-gray-900">Arcelik Hitachi</h1>
            <p className="text-xs text-gray-500">Home Appliances</p>
          </div>
          <div className="flex items-center gap-2">
            <span className="text-2xl">🛒</span>
            {cartCount > 0 && (
              <span className="bg-red-500 text-white text-xs font-bold w-5 h-5 rounded-full flex items-center justify-center">
                {cartCount}
              </span>
            )}
          </div>
        </div>
      </header>

      <main className="max-w-6xl mx-auto px-4 py-8">
        {/* Search */}
        <div className="mb-6">
          <input
            type="text"
            placeholder="ค้นหาสินค้า..."
            value={searchText}
            onChange={e => setSearchText(e.target.value)}
            className="w-full md:w-96 px-4 py-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-300"
          />
        </div>

        {/* Category Filter */}
        <div className="flex flex-wrap gap-2 mb-8">
          {Object.entries(CATEGORIES).map(([key, label]) => (
            <button
              key={key}
              onClick={() => setSelectedCategory(key as CategoryKey)}
              className={`px-4 py-2 rounded-full text-sm font-medium transition-colors duration-200 ${
                selectedCategory === key
                  ? 'bg-blue-500 text-white'
                  : 'bg-white text-gray-600 border border-gray-200 hover:border-blue-300'
              }`}
            >
              {label}
            </button>
          ))}
        </div>

        {/* Products */}
        {filteredProducts.length === 0 ? (
          <div className="text-center py-16 text-gray-400">
            <p className="text-4xl mb-4">🔍</p>
            <p>ไม่พบสินค้าที่ค้นหา</p>
          </div>
        ) : (
          <>
            <p className="text-sm text-gray-500 mb-4">
              พบ {filteredProducts.length} รายการ
            </p>
            <div className="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-6">
              {filteredProducts.map(product => (
                <ProductCard
                  key={product.id}
                  product={product}
                  onAddToCart={handleAddToCart}
                />
              ))}
            </div>
          </>
        )}
      </main>
    </div>
  )
}

export default App
```

---

## สรุปวันที่ 2

วันนี้เราได้เรียนรู้และลงมือทำ:

- ✅ หลักการออกแบบ Component ที่ดี — Reusable, Single Responsibility
- ✅ Component Composition และการใช้ `children` Props
- ✅ Props พร้อม TypeScript — interface, default values, callbacks, spread props
- ✅ useState — การจัดการ State แบบ Object และ Array อย่างถูกต้อง (Immutability)
- ✅ Lifting State Up — ยก State ขึ้นไป Parent เมื่อหลาย Component ต้องใช้ร่วมกัน
- ✅ Event Handling — Mouse, Change, Form events พร้อม TypeScript types
- ✅ Conditional Rendering ทุกรูปแบบ
- ✅ การแสดงผล List ด้วย `.map()` พร้อม Filter และ Sort
- ✅ Tailwind CSS v4 — Utility classes, Responsive, Hover, Dark mode
- ✅ Workshop Product Catalog ครบวงจร

**พรุ่งนี้ (วันที่ 3):** เจาะลึก React Hooks สำคัญ — useEffect, useRef, useMemo, useCallback, Context API, Custom Hooks และฟีเจอร์ใหม่ของ React 19

---

## แหล่งอ้างอิงเพิ่มเติม

- [Tailwind CSS Docs](https://tailwindcss.com/docs) — เอกสาร Tailwind CSS ครบถ้วน
- [Tailwind CSS Cheat Sheet](https://tailwindcomponents.com/cheatsheet) — สรุป Class ทั้งหมด
- [React Docs — State Management](https://react.dev/learn/managing-state) — เอกสาร React อย่างเป็นทางการ
