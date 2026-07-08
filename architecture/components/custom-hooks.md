# Custom Hooks Pattern

Extract reusable stateful logic into custom hooks.

## Pattern

Custom hooks encapsulate state and side effects:

```javascript
function useAuth() {
  // Hook state: camelCase
  const [user, setUser] = useState(null);
  const [isLoading, setIsLoading] = useState(true);

  useEffect(() => {
    checkAuthStatus()
      .then(setUser)
      .finally(() => setIsLoading(false));
  }, []);

  const login = async (credentials) => {
    // Internal variables: snake_case
    const authenticated_user = await authenticate(credentials);
    setUser(authenticated_user);
  };

  const logout = () => {
    setUser(null);
    clearSession();
  };

  // Return values: camelCase (will be destructured as hook returns)
  return { user, isLoading, login, logout };
}
```

## Usage

```javascript
function App() {
  // Hook returns: camelCase
  const { user, isLoading, login, logout } = useAuth();

  if (isLoading) return <LoadingSpinner />;

  return user ? (
    <Dashboard user={user} onLogout={logout} />
  ) : (
    <LoginForm onLogin={login} />
  );
}
```

## Common Custom Hooks

**Data fetching:**
```javascript
function useFetchData(url) {
  // Hook state: camelCase
  const [data, setData] = useState(null);
  const [error, setError] = useState(null);
  const [isLoading, setIsLoading] = useState(true);

  useEffect(() => {
    fetch(url)
      .then(res => res.json())
      .then(setData)
      .catch(setError)
      .finally(() => setIsLoading(false));
  }, [url]);

  // Return values: camelCase
  return { data, error, isLoading };
}
```

**Local storage:**
```javascript
function useLocalStorage(key, initial_value) {
  // Hook state: camelCase
  const [value, setValue] = useState(() => {
    // Internal variables: snake_case
    const stored_value = localStorage.getItem(key);
    return stored_value ? JSON.parse(stored_value) : initial_value;
  });

  useEffect(() => {
    localStorage.setItem(key, JSON.stringify(value));
  }, [key, value]);

  // Return values: camelCase (array for useState-like API)
  return [value, setValue];
}
```

## Benefits

- **Reusability**: Use same logic across components
- **Testability**: Test hooks in isolation
- **Organization**: Keep components focused on rendering

## Related Notes
- [Hooks Naming: use Prefix](/naming/hooks-use-prefix.md)
- [Container/Presentational Pattern](/architecture/components/container-presentational-pattern.md)
- [State Colocation](/architecture/state/state-colocation.md)
- [Reading Browser-Only State on Mount Without Hydration Mismatches](/architecture/state/browser-only-state-on-mount.md)
