## 🧠 Smarter State Management in React: One State to Rule Them All

React makes it easy to manage UI state — *too* easy. It's tempting to go:

```tsx
const [name, setName] = useState('');
const [age, setAge] = useState(0);
const [email, setEmail] = useState('');
```

That’s fine... until your component has 10+ fields. Welcome to **state spaghetti** 🍝.

---

### 🔥 The Better Way: Use a Single Object State

Instead, combine related state into one object:

```tsx
const [formData, setFormData] = useState({
  name: '',
  age: 0,
  email: ''
});
```

**Update with ease:**

```tsx
const handleChange = (field: keyof typeof formData, value: string | number) => {
  setFormData(prev => ({ ...prev, [field]: value }));
};
```

Then use it like:

```tsx
<input
  value={formData.name}
  onChange={e => handleChange('name', e.target.value)}
/>
```

---

### ✅ Why This Rocks:

- 🧹 Cleaner code
- 🧩 Easier validation
- 🔄 Simpler resets (`setFormData(initialState)`)
- 🧪 Perfect for form libraries or schema-driven UIs

---

### 🛑 When **Not** to Use This

If fields are **independent**, like `isLoading`, `isOpen`, etc., separate states can be better for clarity.

---

### 🧘 Bonus Tip: Type-safe updates

```tsx
type FormKeys = keyof typeof formData;

const handleChange = <K extends FormKeys>(key: K, value: typeof formData[K]) => {
  setFormData(prev => ({ ...prev, [key]: value }));
};
```

---

### TL;DR

Use a single state object when data is **logically grouped** — your code will thank you later.