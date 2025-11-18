# Architecture Patterns

This document defines architectural patterns, design principles, and structural guidelines for building maintainable, scalable systems.

## Table of Contents
- [Design Principles](#design-principles)
- [Component Architecture](#component-architecture)
- [State Management](#state-management)
- [API Integration](#api-integration)
- [Database Patterns](#database-patterns)
- [Error Handling](#error-handling)
- [Performance Optimization](#performance-optimization)

## Design Principles

### SOLID Principles

Apply SOLID principles to maintain clean architecture:

**Single Responsibility Principle (SRP)**
- Each module, class, or function should have one reason to change
- Example: Separate data fetching from presentation logic

**Open/Closed Principle (OCP)**
- Open for extension, closed for modification
- Use composition and dependency injection

**Liskov Substitution Principle (LSP)**
- Derived types must be substitutable for their base types
- Maintain consistent interfaces

**Interface Segregation Principle (ISP)**
- Many specific interfaces are better than one general interface
- Don't force clients to depend on methods they don't use

**Dependency Inversion Principle (DIP)**
- Depend on abstractions, not concretions
- Use dependency injection for flexibility

### DRY (Don't Repeat Yourself)

**Extract reusable logic:**
```javascript
// Bad - Repeated logic
function getFullName(user) {
  return `${user.firstName} ${user.lastName}`;
}
function displayUserName(user) {
  return `${user.firstName} ${user.lastName}`;
}

// Good - Single source of truth
function formatUserName(user) {
  return `${user.firstName} ${user.lastName}`;
}
```

### KISS (Keep It Simple, Stupid)

- Prefer simple solutions over clever ones
- Optimize for readability, not brevity
- Add complexity only when necessary

### YAGNI (You Aren't Gonna Need It)

- Don't build features "just in case"
- Implement only what's required now
- Refactor when new requirements emerge

## Component Architecture

### Container/Presentational Pattern

**Separate logic from presentation:**

```javascript
// Container Component (logic)
function UserProfileContainer({ userId }) {
  const { data, error, isLoading } = useFetchUser(userId);
  const [isEditing, setIsEditing] = useState(false);

  const handleSave = async (updates) => {
    await updateUser(userId, updates);
    setIsEditing(false);
  };

  return (
    <UserProfile
      user={data}
      error={error}
      isLoading={isLoading}
      isEditing={isEditing}
      onEdit={() => setIsEditing(true)}
      onSave={handleSave}
    />
  );
}

// Presentational Component (UI)
function UserProfile({ user, error, isLoading, isEditing, onEdit, onSave }) {
  if (isLoading) return <LoadingSpinner />;
  if (error) return <ErrorMessage error={error} />;

  return (
    <div>
      <h1>{user.name}</h1>
      {isEditing ? (
        <EditForm user={user} onSave={onSave} />
      ) : (
        <button onClick={onEdit}>Edit</button>
      )}
    </div>
  );
}
```

### Component Composition

**Build complex UIs from simple components:**

```javascript
// Atomic components
function Button({ children, onClick, variant }) { }
function Card({ children, title }) { }
function Avatar({ src, name }) { }

// Composed component
function UserCard({ user, onEdit }) {
  return (
    <Card title={user.name}>
      <Avatar src={user.avatar} name={user.name} />
      <p>{user.bio}</p>
      <Button variant="primary" onClick={onEdit}>
        Edit Profile
      </Button>
    </Card>
  );
}
```

### Custom Hooks Pattern

**Extract reusable stateful logic:**

```javascript
// Custom hook
function useAuth() {
  const [user, setUser] = useState(null);
  const [isLoading, setIsLoading] = useState(true);

  useEffect(() => {
    checkAuthStatus()
      .then(setUser)
      .finally(() => setIsLoading(false));
  }, []);

  const login = async (credentials) => {
    const user = await authenticate(credentials);
    setUser(user);
  };

  const logout = () => {
    setUser(null);
    clearSession();
  };

  return { user, isLoading, login, logout };
}

// Usage
function App() {
  const { user, login, logout } = useAuth();
  // ...
}
```

## State Management

### Local vs Global State

**Local state** - Use `useState` or `useReducer`:
- Component-specific UI state
- Form input values
- Toggle states

**Global state** - Use Context or state library:
- User authentication
- Application theme
- Shared data across many components

### Context API Pattern

**Use Context for shared state:**

```javascript
// Create context
const AuthContext = createContext();

// Provider component
export function AuthProvider({ children }) {
  const [user, setUser] = useState(null);

  const value = {
    user,
    login: async (credentials) => {
      const user = await authenticate(credentials);
      setUser(user);
    },
    logout: () => setUser(null),
  };

  return <AuthContext.Provider value={value}>{children}</AuthContext.Provider>;
}

// Custom hook for easy access
export function useAuth() {
  const context = useContext(AuthContext);
  if (!context) {
    throw new Error('useAuth must be used within AuthProvider');
  }
  return context;
}

// Usage
function App() {
  return (
    <AuthProvider>
      <Dashboard />
    </AuthProvider>
  );
}
```

### State Colocation

**Keep state as close to where it's used as possible:**

```javascript
// Bad - State too high
function App() {
  const [searchQuery, setSearchQuery] = useState('');
  return (
    <div>
      <Header />
      <SearchBar query={searchQuery} onChange={setSearchQuery} />
      <Footer />
    </div>
  );
}

// Good - State colocated
function SearchSection() {
  const [searchQuery, setSearchQuery] = useState('');
  return <SearchBar query={searchQuery} onChange={setSearchQuery} />;
}

function App() {
  return (
    <div>
      <Header />
      <SearchSection />
      <Footer />
    </div>
  );
}
```

## API Integration

### Service Layer Pattern

**Centralize API calls in service modules:**

```javascript
// services/userService.js
import { apiClient } from './apiClient';

export const userService = {
  async getUser(userId) {
    const response = await apiClient.get(`/users/${userId}`);
    return response.data;
  },

  async updateUser(userId, updates) {
    const response = await apiClient.put(`/users/${userId}`, updates);
    return response.data;
  },

  async deleteUser(userId) {
    await apiClient.delete(`/users/${userId}`);
  },
};

// Usage in component
import { userService } from '@/services/userService';

function UserProfile({ userId }) {
  const [user, setUser] = useState(null);

  useEffect(() => {
    userService.getUser(userId).then(setUser);
  }, [userId]);
  // ...
}
```

### API Client Configuration

**Centralize API configuration:**

```javascript
// services/apiClient.js
import axios from 'axios';

export const apiClient = axios.create({
  baseURL: process.env.API_BASE_URL,
  timeout: 10000,
  headers: {
    'Content-Type': 'application/json',
  },
});

// Add request interceptor for auth
apiClient.interceptors.request.use((config) => {
  const token = getAuthToken();
  if (token) {
    config.headers.Authorization = `Bearer ${token}`;
  }
  return config;
});

// Add response interceptor for error handling
apiClient.interceptors.response.use(
  (response) => response,
  (error) => {
    if (error.response?.status === 401) {
      redirectToLogin();
    }
    return Promise.reject(error);
  }
);
```

### Data Fetching Hooks

**Use SWR or React Query for data fetching:**

```javascript
import useSWR from 'swr';

function useUser(userId) {
  const { data, error, isLoading, mutate } = useSWR(
    userId ? `/users/${userId}` : null,
    (url) => userService.getUser(userId)
  );

  return {
    user: data,
    error,
    isLoading,
    refresh: mutate,
  };
}

// Usage
function UserProfile({ userId }) {
  const { user, error, isLoading } = useUser(userId);

  if (isLoading) return <LoadingSpinner />;
  if (error) return <ErrorMessage />;
  return <div>{user.name}</div>;
}
```

## Database Patterns

### Repository Pattern

**Abstract database operations:**

```javascript
// repositories/userRepository.js
export class UserRepository {
  constructor(db) {
    this.db = db;
  }

  async findById(id) {
    return this.db.users.findByPk(id);
  }

  async findByEmail(email) {
    return this.db.users.findOne({ where: { email } });
  }

  async create(userData) {
    return this.db.users.create(userData);
  }

  async update(id, updates) {
    const user = await this.findById(id);
    return user.update(updates);
  }

  async delete(id) {
    const user = await this.findById(id);
    return user.destroy();
  }
}
```

### Query Builder Pattern

**Use ORM or query builder for complex queries:**

```javascript
// Using Sequelize
async function getActiveUsers(filters = {}) {
  const query = {
    where: {
      isActive: true,
    },
  };

  if (filters.role) {
    query.where.role = filters.role;
  }

  if (filters.search) {
    query.where[Op.or] = [
      { name: { [Op.iLike]: `%${filters.search}%` } },
      { email: { [Op.iLike]: `%${filters.search}%` } },
    ];
  }

  return db.users.findAll(query);
}
```

### Transaction Pattern

**Use transactions for atomic operations:**

```javascript
async function transferFunds(fromAccountId, toAccountId, amount) {
  const transaction = await db.sequelize.transaction();

  try {
    const fromAccount = await db.accounts.findByPk(fromAccountId, { transaction });
    const toAccount = await db.accounts.findByPk(toAccountId, { transaction });

    await fromAccount.decrement('balance', { by: amount, transaction });
    await toAccount.increment('balance', { by: amount, transaction });

    await transaction.commit();
  } catch (error) {
    await transaction.rollback();
    throw error;
  }
}
```

## Error Handling

### Error Boundaries (React)

**Catch React errors gracefully:**

```javascript
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false, error: null };
  }

  static getDerivedStateFromError(error) {
    return { hasError: true, error };
  }

  componentDidCatch(error, errorInfo) {
    console.error('Error caught by boundary:', error, errorInfo);
    // Log to error tracking service
    logErrorToService(error, errorInfo);
  }

  render() {
    if (this.state.hasError) {
      return <ErrorFallback error={this.state.error} />;
    }
    return this.props.children;
  }
}
```

### API Error Handling

**Standardize error responses:**

```javascript
// API route
export async function POST(request) {
  try {
    const data = await request.json();
    const result = await processData(data);
    return Response.json(result);
  } catch (error) {
    console.error('API error:', error);

    if (error instanceof ValidationError) {
      return Response.json(
        { error: 'Validation failed', details: error.details },
        { status: 400 }
      );
    }

    if (error instanceof UnauthorizedError) {
      return Response.json(
        { error: 'Unauthorized' },
        { status: 401 }
      );
    }

    return Response.json(
      { error: 'Internal server error' },
      { status: 500 }
    );
  }
}
```

### Custom Error Classes

**Create domain-specific errors:**

```javascript
export class ValidationError extends Error {
  constructor(message, details) {
    super(message);
    this.name = 'ValidationError';
    this.details = details;
  }
}

export class NotFoundError extends Error {
  constructor(resource, id) {
    super(`${resource} with id ${id} not found`);
    this.name = 'NotFoundError';
    this.resource = resource;
    this.id = id;
  }
}

// Usage
async function getUser(userId) {
  const user = await db.users.findByPk(userId);
  if (!user) {
    throw new NotFoundError('User', userId);
  }
  return user;
}
```

## Performance Optimization

### Code Splitting

**Split code by route or feature:**

```javascript
// Next.js dynamic imports
import dynamic from 'next/dynamic';

const HeavyComponent = dynamic(() => import('./HeavyComponent'), {
  loading: () => <LoadingSpinner />,
  ssr: false, // Disable SSR if not needed
});

function Page() {
  return (
    <div>
      <Header />
      <HeavyComponent />
    </div>
  );
}
```

### Memoization

**Memoize expensive computations:**

```javascript
import { useMemo, useCallback } from 'react';

function ExpensiveComponent({ data, onItemClick }) {
  // Memoize expensive calculation
  const processedData = useMemo(() => {
    return data.map(item => heavyProcessing(item));
  }, [data]);

  // Memoize callback to prevent child re-renders
  const handleClick = useCallback((id) => {
    onItemClick(id);
  }, [onItemClick]);

  return (
    <div>
      {processedData.map(item => (
        <Item key={item.id} data={item} onClick={handleClick} />
      ))}
    </div>
  );
}
```

### Lazy Loading

**Load resources on demand:**

```javascript
// Lazy load images
function LazyImage({ src, alt }) {
  const [isLoaded, setIsLoaded] = useState(false);

  return (
    <img
      src={src}
      alt={alt}
      loading="lazy"
      onLoad={() => setIsLoaded(true)}
      className={isLoaded ? 'loaded' : 'loading'}
    />
  );
}

// Lazy load data
function InfiniteList() {
  const { data, fetchMore } = useInfiniteScroll();

  return (
    <div>
      {data.map(item => <Item key={item.id} data={item} />)}
      <Observer onInView={fetchMore} />
    </div>
  );
}
```

### Caching Strategies

**Cache data appropriately:**

```javascript
// Client-side caching with SWR
const { data } = useSWR('/api/data', fetcher, {
  revalidateOnFocus: false,
  revalidateOnReconnect: false,
  dedupingInterval: 60000, // 1 minute
});

// Server-side caching (Next.js)
export async function getData() {
  const response = await fetch('https://api.example.com/data', {
    next: { revalidate: 3600 }, // Revalidate every hour
  });
  return response.json();
}
```

---

**See Also:**
- [Code Style Guide](code-style.md)
- [Testing Practices](testing-practices.md)
- [Development Workflows](development-workflows.md)
