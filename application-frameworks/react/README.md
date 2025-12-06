# ⚛️ React - Complete Beginner's Guide

> **Master React!** Build modern, interactive user interfaces with React from scratch.

---

## 📚 Table of Contents

- [What is React?](#what-is-react)
- [Installation & Setup](#installation--setup)
- [Your First React App](#your-first-react-app)
- [JSX Basics](#jsx-basics)
- [Components](#components)
- [Props](#props)
- [State & useState Hook](#state--usestate-hook)
- [Event Handling](#event-handling)
- [Conditional Rendering](#conditional-rendering)
- [Lists & Keys](#lists--keys)
- [Forms & Controlled Components](#forms--controlled-components)
- [useEffect Hook](#useeffect-hook)
- [API Integration](#api-integration)
- [React Router](#react-router)
- [Context API](#context-api)
- [Custom Hooks](#custom-hooks)
- [Styling in React](#styling-in-react)
- [Common Commands](#common-commands)
- [Best Practices](#best-practices)

---

## 🤔 What is React?

**React** is a JavaScript library for building user interfaces (UIs).

**Why React?**
- ✅ Component-based architecture (reusable building blocks)
- ✅ Virtual DOM for fast performance
- ✅ Declarative (describe what you want, React handles how)
- ✅ Huge ecosystem (libraries, tools, community)
- ✅ Backed by Meta (Facebook)
- ✅ Great developer experience

**Common Uses:**
- Single Page Applications (SPAs)
- Dynamic dashboards
- E-commerce websites
- Social media platforms
- Admin panels

---

## 📦 Installation & Setup

### Option 1: Vite (Recommended - Fast!)

```bash
# Create new React project with Vite
npm create vite@latest my-react-app -- --template react

# Navigate to project
cd my-react-app

# Install dependencies
npm install

# Start development server
npm run dev

# Open browser: http://localhost:5173
```

### Option 2: Create React App (CRA)

```bash
# Create new React project with CRA
npx create-react-app my-react-app

# Navigate to project
cd my-react-app

# Start development server
npm start

# Open browser: http://localhost:3000
```

### Project Structure (Vite)

```
my-react-app/
├── node_modules/       # Dependencies
├── public/             # Static files
│   └── vite.svg
├── src/                # Source code
│   ├── assets/         # Images, fonts
│   ├── App.jsx         # Main component
│   ├── App.css         # App styles
│   ├── main.jsx        # Entry point
│   └── index.css       # Global styles
├── .gitignore
├── index.html          # HTML template
├── package.json        # Dependencies
└── vite.config.js      # Vite config
```

---

## 🚀 Your First React App

### Basic React Component

```jsx
// src/App.jsx
function App() {
  return (
    <div>
      <h1>Hello, React!</h1>
      <p>Welcome to your first React app.</p>
    </div>
  );
}

export default App;
```

### Entry Point

```jsx
// src/main.jsx (Vite) or src/index.js (CRA)
import React from 'react'
import ReactDOM from 'react-dom/client'
import App from './App.jsx'
import './index.css'

ReactDOM.createRoot(document.getElementById('root')).render(
  <React.StrictMode>
    <App />
  </React.StrictMode>,
)
```

---

## 📝 JSX Basics

**JSX** = JavaScript + XML. It lets you write HTML-like code in JavaScript.

### Basic JSX

```jsx
function Greeting() {
  const name = "John";
  const age = 25;
  
  return (
    <div>
      <h1>Hello, {name}!</h1>
      <p>You are {age} years old.</p>
      <p>Next year you'll be {age + 1}.</p>
    </div>
  );
}
```

### JSX Rules

```jsx
// ❌ Multiple root elements (ERROR!)
return (
  <h1>Title</h1>
  <p>Paragraph</p>
);

// ✅ Wrap in a parent element
return (
  <div>
    <h1>Title</h1>
    <p>Paragraph</p>
  </div>
);

// ✅ Or use Fragment
return (
  <>
    <h1>Title</h1>
    <p>Paragraph</p>
  </>
);
```

### JavaScript Expressions in JSX

```jsx
function Example() {
  const isLoggedIn = true;
  const items = [1, 2, 3];
  
  return (
    <div>
      {/* Ternary operator */}
      <p>{isLoggedIn ? 'Welcome back!' : 'Please log in'}</p>
      
      {/* Function call */}
      <p>Total: {items.reduce((a, b) => a + b, 0)}</p>
      
      {/* Template literals */}
      <p>{`You have ${items.length} items`}</p>
    </div>
  );
}
```

### HTML Attributes → JSX Attributes

```jsx
// ❌ HTML
<div class="container" onclick="handleClick()">

// ✅ JSX (use camelCase)
<div className="container" onClick={handleClick}>

// Other examples:
// for → htmlFor
// tabindex → tabIndex
// readonly → readOnly
```

---

## 🧩 Components

Components are reusable pieces of UI.

### Function Component (Modern Way)

```jsx
// src/components/Button.jsx
function Button() {
  return <button>Click Me</button>;
}

export default Button;

// Use in App.jsx
import Button from './components/Button';

function App() {
  return (
    <div>
      <Button />
      <Button />
    </div>
  );
}
```

### Component with Logic

```jsx
function Greeting() {
  const currentHour = new Date().getHours();
  let message;
  
  if (currentHour < 12) {
    message = "Good morning!";
  } else if (currentHour < 18) {
    message = "Good afternoon!";
  } else {
    message = "Good evening!";
  }
  
  return <h1>{message}</h1>;
}
```

---

## 📦 Props

Props (properties) pass data from parent to child components.

### Basic Props

```jsx
// Child component
function Greeting({ name, age }) {
  return (
    <div>
      <h2>Hello, {name}!</h2>
      <p>You are {age} years old.</p>
    </div>
  );
}

// Parent component
function App() {
  return (
    <div>
      <Greeting name="John" age={25} />
      <Greeting name="Jane" age={30} />
    </div>
  );
}
```

### Props with Default Values

```jsx
function Button({ text = "Click Me", color = "blue" }) {
  return (
    <button style={{ backgroundColor: color }}>
      {text}
    </button>
  );
}

// Usage
<Button text="Submit" color="green" />
<Button /> {/* Uses default values */}
```

### Props Destructuring

```jsx
// ❌ Without destructuring
function UserCard(props) {
  return (
    <div>
      <h3>{props.name}</h3>
      <p>{props.email}</p>
    </div>
  );
}

// ✅ With destructuring
function UserCard({ name, email }) {
  return (
    <div>
      <h3>{name}</h3>
      <p>{email}</p>
    </div>
  );
}
```

### Children Prop

```jsx
function Card({ children }) {
  return (
    <div className="card">
      {children}
    </div>
  );
}

// Usage
<Card>
  <h2>Title</h2>
  <p>This is the content inside the card.</p>
</Card>
```

---

## 🔄 State & useState Hook

State stores component data that can change.

### Basic useState

```jsx
import { useState } from 'react';

function Counter() {
  const [count, setCount] = useState(0);
  
  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
      <button onClick={() => setCount(count - 1)}>Decrement</button>
      <button onClick={() => setCount(0)}>Reset</button>
    </div>
  );
}
```

### Multiple State Variables

```jsx
function Form() {
  const [name, setName] = useState('');
  const [email, setEmail] = useState('');
  const [age, setAge] = useState(0);
  
  return (
    <form>
      <input 
        value={name} 
        onChange={(e) => setName(e.target.value)}
        placeholder="Name"
      />
      <input 
        value={email} 
        onChange={(e) => setEmail(e.target.value)}
        placeholder="Email"
      />
      <input 
        type="number"
        value={age} 
        onChange={(e) => setAge(e.target.value)}
        placeholder="Age"
      />
    </form>
  );
}
```

### State with Objects

```jsx
function UserForm() {
  const [user, setUser] = useState({
    name: '',
    email: '',
    age: 0
  });
  
  const handleChange = (e) => {
    setUser({
      ...user,
      [e.target.name]: e.target.value
    });
  };
  
  return (
    <form>
      <input 
        name="name"
        value={user.name} 
        onChange={handleChange}
      />
      <input 
        name="email"
        value={user.email} 
        onChange={handleChange}
      />
      <input 
        name="age"
        type="number"
        value={user.age} 
        onChange={handleChange}
      />
    </form>
  );
}
```

---

## 🎯 Event Handling

```jsx
function EventExamples() {
  const handleClick = () => {
    alert('Button clicked!');
  };
  
  const handleInputChange = (e) => {
    console.log('Input value:', e.target.value);
  };
  
  const handleSubmit = (e) => {
    e.preventDefault(); // Prevent page reload
    console.log('Form submitted');
  };
  
  return (
    <div>
      {/* Click event */}
      <button onClick={handleClick}>Click Me</button>
      
      {/* Inline function */}
      <button onClick={() => console.log('Inline click')}>
        Inline
      </button>
      
      {/* Input change */}
      <input onChange={handleInputChange} />
      
      {/* Form submit */}
      <form onSubmit={handleSubmit}>
        <button type="submit">Submit</button>
      </form>
    </div>
  );
}
```

---

## ❓ Conditional Rendering

### If-Else with Ternary

```jsx
function LoginButton({ isLoggedIn }) {
  return (
    <div>
      {isLoggedIn ? (
        <button>Logout</button>
      ) : (
        <button>Login</button>
      )}
    </div>
  );
}
```

### AND Operator (&&)

```jsx
function Notification({ hasMessages }) {
  return (
    <div>
      {hasMessages && <p>You have new messages!</p>}
    </div>
  );
}
```

### Early Return

```jsx
function UserProfile({ user }) {
  if (!user) {
    return <p>Please log in to view your profile.</p>;
  }
  
  return (
    <div>
      <h1>{user.name}</h1>
      <p>{user.email}</p>
    </div>
  );
}
```

---

## 📋 Lists & Keys

### Rendering Lists

```jsx
function TodoList() {
  const todos = [
    { id: 1, text: 'Learn React' },
    { id: 2, text: 'Build a project' },
    { id: 3, text: 'Deploy to production' }
  ];
  
  return (
    <ul>
      {todos.map((todo) => (
        <li key={todo.id}>{todo.text}</li>
      ))}
    </ul>
  );
}
```

### Why Keys?

```jsx
// ❌ Without key (warning!)
{todos.map((todo) => (
  <li>{todo.text}</li>
))}

// ✅ With key
{todos.map((todo) => (
  <li key={todo.id}>{todo.text}</li>
))}

// ⚠️ Don't use index as key if list can change
{todos.map((todo, index) => (
  <li key={index}>{todo.text}</li> // Not recommended
))}
```

### Complex List Example

```jsx
function UserList() {
  const users = [
    { id: 1, name: 'John', email: 'john@example.com' },
    { id: 2, name: 'Jane', email: 'jane@example.com' },
    { id: 3, name: 'Bob', email: 'bob@example.com' }
  ];
  
  return (
    <div>
      {users.map((user) => (
        <div key={user.id} className="user-card">
          <h3>{user.name}</h3>
          <p>{user.email}</p>
        </div>
      ))}
    </div>
  );
}
```

---

## 📝 Forms & Controlled Components

### Controlled Input

```jsx
function LoginForm() {
  const [email, setEmail] = useState('');
  const [password, setPassword] = useState('');
  
  const handleSubmit = (e) => {
    e.preventDefault();
    console.log('Login:', { email, password });
  };
  
  return (
    <form onSubmit={handleSubmit}>
      <div>
        <label>Email:</label>
        <input 
          type="email"
          value={email}
          onChange={(e) => setEmail(e.target.value)}
          required
        />
      </div>
      
      <div>
        <label>Password:</label>
        <input 
          type="password"
          value={password}
          onChange={(e) => setPassword(e.target.value)}
          required
        />
      </div>
      
      <button type="submit">Login</button>
    </form>
  );
}
```

### Checkbox & Radio

```jsx
function PreferencesForm() {
  const [newsletter, setNewsletter] = useState(false);
  const [theme, setTheme] = useState('light');
  
  return (
    <form>
      {/* Checkbox */}
      <label>
        <input 
          type="checkbox"
          checked={newsletter}
          onChange={(e) => setNewsletter(e.target.checked)}
        />
        Subscribe to newsletter
      </label>
      
      {/* Radio buttons */}
      <div>
        <label>
          <input 
            type="radio"
            value="light"
            checked={theme === 'light'}
            onChange={(e) => setTheme(e.target.value)}
          />
          Light Theme
        </label>
        
        <label>
          <input 
            type="radio"
            value="dark"
            checked={theme === 'dark'}
            onChange={(e) => setTheme(e.target.value)}
          />
          Dark Theme
        </label>
      </div>
    </form>
  );
}
```

### Select Dropdown

```jsx
function CountrySelector() {
  const [country, setCountry] = useState('us');
  
  return (
    <select value={country} onChange={(e) => setCountry(e.target.value)}>
      <option value="us">United States</option>
      <option value="uk">United Kingdom</option>
      <option value="ca">Canada</option>
    </select>
  );
}
```

---

## ⚡ useEffect Hook

Side effects: API calls, subscriptions, timers, DOM manipulation.

### Basic useEffect

```jsx
import { useState, useEffect } from 'react';

function Timer() {
  const [seconds, setSeconds] = useState(0);
  
  useEffect(() => {
    const interval = setInterval(() => {
      setSeconds((prev) => prev + 1);
    }, 1000);
    
    // Cleanup function
    return () => clearInterval(interval);
  }, []); // Empty array = run once on mount
  
  return <p>Seconds: {seconds}</p>;
}
```

### useEffect with Dependencies

```jsx
function SearchResults({ query }) {
  const [results, setResults] = useState([]);
  
  useEffect(() => {
    console.log(`Searching for: ${query}`);
    
    // Fetch results based on query
    // setResults(...)
  }, [query]); // Run when query changes
  
  return (
    <div>
      <h2>Results for "{query}"</h2>
      {/* Render results */}
    </div>
  );
}
```

### Common useEffect Patterns

```jsx
// 1. Run once on mount
useEffect(() => {
  console.log('Component mounted');
}, []);

// 2. Run on every render
useEffect(() => {
  console.log('Component rendered');
});

// 3. Run when specific value changes
useEffect(() => {
  console.log('Count changed:', count);
}, [count]);

// 4. Cleanup on unmount
useEffect(() => {
  return () => {
    console.log('Component unmounted');
  };
}, []);
```

---

## 🌐 API Integration

### Fetch Data with useEffect

```jsx
import { useState, useEffect } from 'react';

function UserList() {
  const [users, setUsers] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);
  
  useEffect(() => {
    fetch('https://jsonplaceholder.typicode.com/users')
      .then((response) => response.json())
      .then((data) => {
        setUsers(data);
        setLoading(false);
      })
      .catch((err) => {
        setError(err.message);
        setLoading(false);
      });
  }, []);
  
  if (loading) return <p>Loading...</p>;
  if (error) return <p>Error: {error}</p>;
  
  return (
    <ul>
      {users.map((user) => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
}
```

### Using Axios

```bash
npm install axios
```

```jsx
import { useState, useEffect } from 'react';
import axios from 'axios';

function Posts() {
  const [posts, setPosts] = useState([]);
  const [loading, setLoading] = useState(true);
  
  useEffect(() => {
    const fetchPosts = async () => {
      try {
        const response = await axios.get('https://jsonplaceholder.typicode.com/posts');
        setPosts(response.data);
        setLoading(false);
      } catch (error) {
        console.error('Error fetching posts:', error);
        setLoading(false);
      }
    };
    
    fetchPosts();
  }, []);
  
  if (loading) return <p>Loading...</p>;
  
  return (
    <div>
      {posts.slice(0, 10).map((post) => (
        <div key={post.id}>
          <h3>{post.title}</h3>
          <p>{post.body}</p>
        </div>
      ))}
    </div>
  );
}
```

### POST Request

```jsx
const createPost = async () => {
  try {
    const response = await axios.post('https://jsonplaceholder.typicode.com/posts', {
      title: 'New Post',
      body: 'This is a new post',
      userId: 1
    });
    
    console.log('Created:', response.data);
  } catch (error) {
    console.error('Error:', error);
  }
};
```

---

## 🧭 React Router

```bash
npm install react-router-dom
```

### Basic Routing

```jsx
// src/main.jsx
import { BrowserRouter } from 'react-router-dom';

ReactDOM.createRoot(document.getElementById('root')).render(
  <BrowserRouter>
    <App />
  </BrowserRouter>
);

// src/App.jsx
import { Routes, Route, Link } from 'react-router-dom';
import Home from './pages/Home';
import About from './pages/About';
import Contact from './pages/Contact';

function App() {
  return (
    <div>
      <nav>
        <Link to="/">Home</Link>
        <Link to="/about">About</Link>
        <Link to="/contact">Contact</Link>
      </nav>
      
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
        <Route path="/contact" element={<Contact />} />
      </Routes>
    </div>
  );
}
```

### Dynamic Routes

```jsx
<Routes>
  <Route path="/users/:id" element={<UserProfile />} />
</Routes>

// In UserProfile component
import { useParams } from 'react-router-dom';

function UserProfile() {
  const { id } = useParams();
  
  return <h1>User ID: {id}</h1>;
}
```

### Programmatic Navigation

```jsx
import { useNavigate } from 'react-router-dom';

function LoginForm() {
  const navigate = useNavigate();
  
  const handleLogin = () => {
    // After successful login
    navigate('/dashboard');
  };
  
  return <button onClick={handleLogin}>Login</button>;
}
```

---

## 🌍 Context API

Share data across components without prop drilling.

### Create Context

```jsx
// src/context/AuthContext.jsx
import { createContext, useState, useContext } from 'react';

const AuthContext = createContext();

export function AuthProvider({ children }) {
  const [user, setUser] = useState(null);
  
  const login = (userData) => {
    setUser(userData);
  };
  
  const logout = () => {
    setUser(null);
  };
  
  return (
    <AuthContext.Provider value={{ user, login, logout }}>
      {children}
    </AuthContext.Provider>
  );
}

export function useAuth() {
  return useContext(AuthContext);
}
```

### Use Context

```jsx
// src/main.jsx
import { AuthProvider } from './context/AuthContext';

ReactDOM.createRoot(document.getElementById('root')).render(
  <AuthProvider>
    <App />
  </AuthProvider>
);

// In any component
import { useAuth } from '../context/AuthContext';

function Profile() {
  const { user, logout } = useAuth();
  
  if (!user) {
    return <p>Please log in</p>;
  }
  
  return (
    <div>
      <h1>Welcome, {user.name}!</h1>
      <button onClick={logout}>Logout</button>
    </div>
  );
}
```

---

## 🔧 Custom Hooks

Reusable logic extracted into custom hooks.

### useFetch Hook

```jsx
// src/hooks/useFetch.js
import { useState, useEffect } from 'react';

function useFetch(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);
  
  useEffect(() => {
    const fetchData = async () => {
      try {
        const response = await fetch(url);
        const json = await response.json();
        setData(json);
        setLoading(false);
      } catch (err) {
        setError(err.message);
        setLoading(false);
      }
    };
    
    fetchData();
  }, [url]);
  
  return { data, loading, error };
}

export default useFetch;

// Usage
function Users() {
  const { data, loading, error } = useFetch('https://jsonplaceholder.typicode.com/users');
  
  if (loading) return <p>Loading...</p>;
  if (error) return <p>Error: {error}</p>;
  
  return (
    <ul>
      {data.map((user) => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
}
```

### useLocalStorage Hook

```jsx
import { useState, useEffect } from 'react';

function useLocalStorage(key, initialValue) {
  const [value, setValue] = useState(() => {
    const saved = localStorage.getItem(key);
    return saved !== null ? JSON.parse(saved) : initialValue;
  });
  
  useEffect(() => {
    localStorage.setItem(key, JSON.stringify(value));
  }, [key, value]);
  
  return [value, setValue];
}

// Usage
function App() {
  const [name, setName] = useLocalStorage('name', '');
  
  return (
    <input 
      value={name}
      onChange={(e) => setName(e.target.value)}
    />
  );
}
```

---

## 🎨 Styling in React

### Inline Styles

```jsx
function Button() {
  const buttonStyle = {
    backgroundColor: 'blue',
    color: 'white',
    padding: '10px 20px',
    border: 'none',
    borderRadius: '5px'
  };
  
  return <button style={buttonStyle}>Click Me</button>;
}
```

### CSS Modules

```css
/* Button.module.css */
.button {
  background-color: blue;
  color: white;
  padding: 10px 20px;
}

.button:hover {
  background-color: darkblue;
}
```

```jsx
import styles from './Button.module.css';

function Button() {
  return <button className={styles.button}>Click Me</button>;
}
```

### Tailwind CSS

```bash
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p
```

```jsx
function Button() {
  return (
    <button className="bg-blue-500 text-white px-4 py-2 rounded hover:bg-blue-600">
      Click Me
    </button>
  );
}
```

---

## 🛠️ Common Commands

```bash
# Create new app
npm create vite@latest my-app -- --template react

# Install dependencies
npm install

# Run development server
npm run dev

# Build for production
npm run build

# Preview production build
npm run preview

# Install packages
npm install axios
npm install react-router-dom
npm install @tanstack/react-query
```

---

## ✅ Best Practices

### 1. Component Organization

```
src/
├── components/
│   ├── common/         # Reusable components
│   │   ├── Button.jsx
│   │   └── Input.jsx
│   ├── layout/         # Layout components
│   │   ├── Header.jsx
│   │   └── Footer.jsx
│   └── features/       # Feature-specific
│       └── users/
│           ├── UserList.jsx
│           └── UserCard.jsx
```

### 2. Props Validation

```bash
npm install prop-types
```

```jsx
import PropTypes from 'prop-types';

function Button({ text, onClick, color }) {
  return <button onClick={onClick}>{text}</button>;
}

Button.propTypes = {
  text: PropTypes.string.isRequired,
  onClick: PropTypes.func.isRequired,
  color: PropTypes.string
};

Button.defaultProps = {
  color: 'blue'
};
```

### 3. Avoid Prop Drilling

```jsx
// ❌ Bad: Passing props through many levels
<Parent data={data}>
  <Child data={data}>
    <GrandChild data={data} />
  </Child>
</Parent>

// ✅ Good: Use Context API
<DataProvider value={data}>
  <Parent>
    <Child>
      <GrandChild /> {/* Access data via useContext */}
    </Child>
  </Parent>
</DataProvider>
```

---

## 🎓 Learning Resources

- **Official Docs**: https://react.dev/
- **React Tutorial**: https://react.dev/learn
- **React Patterns**: https://reactpatterns.com/
- **Awesome React**: https://github.com/enaqx/awesome-react

---

## 🆘 Common Errors & Solutions

### Error: "Objects are not valid as a React child"
```jsx
// ❌ Bad
<p>{user}</p> // user is an object!

// ✅ Good
<p>{user.name}</p>
```

### Error: "Cannot read property 'map' of undefined"
```jsx
// ❌ Bad
{users.map(user => ...)} // users might be undefined

// ✅ Good
{users?.map(user => ...)}
// or
{users && users.map(user => ...)}
```

### Error: "React Hook useEffect has a missing dependency"
```jsx
// Add the dependency to the array
useEffect(() => {
  fetchData(userId);
}, [userId]); // Include userId
```

---

**You're now ready to build React applications! 🚀**

For full-stack development, combine with Node.js backend!
