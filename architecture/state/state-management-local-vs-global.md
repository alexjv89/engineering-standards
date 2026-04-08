# State Management: Local vs Global

Choose the right level of state management based on data sharing needs.

## Decision Guide

**Local State** - Use `useState` or `useReducer`:
- Component-specific UI state
- Form input values
- Toggle states
- Data used by single component or siblings

**URL State** - Use the query string:
- List page filters, search, sort, pagination
- Anything that should be shareable or survive a reload
- See [URL as Filter State](/architecture/state/url-filter-state.md)

**Global State** - Use Context or state library:
- User authentication
- Application theme
- Shared data across many components
- Application-wide settings

## Local State Examples

```javascript
// Local state - only this component needs it
function SearchBar() {
  // Hook state: camelCase
  const [searchQuery, setSearchQuery] = useState('');

  return (
    <input
      value={searchQuery}
      onChange={(e) => setSearchQuery(e.target.value)}
    />
  );
}

// Local state - toggle
function Accordion() {
  // Hook state: camelCase
  const [isOpen, setIsOpen] = useState(false);

  return (
    <div>
      <button onClick={() => setIsOpen(!isOpen)}>Toggle</button>
      {isOpen && <div>Content</div>}
    </div>
  );
}
```

## Global State Examples

```javascript
// Global state - needed everywhere
const ThemeContext = createContext();

export function ThemeProvider({ children }) {
  const [theme, setTheme] = useState('light');

  return (
    <ThemeContext.Provider value={{ theme, setTheme }}>
      {children}
    </ThemeContext.Provider>
  );
}

// Used in many components
function Button() {
  const { theme } = useContext(ThemeContext);
  return <button className={theme}>Click me</button>;
}

function Header() {
  const { theme, setTheme } = useContext(ThemeContext);
  return <button onClick={() => setTheme(theme === 'light' ? 'dark' : 'light')}>
    Toggle Theme
  </button>;
}
```

## State Lifting

When siblings need to share state, lift it to common parent:

```javascript
// Lifted state
function ParentComponent() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <Counter count={count} onIncrement={() => setCount(count + 1)} />
      <Display count={count} />
    </div>
  );
}
```

## Server State

For data from APIs, use specialized libraries:

```javascript
// React Query for server state
import { useQuery } from '@tanstack/react-query';

function UserProfile({ userId }) {
  const { data, isLoading } = useQuery(['user', userId], () =>
    fetchUser(userId)
  );

  if (isLoading) return <LoadingSpinner />;
  return <div>{data.name}</div>;
}
```

## Related Notes
- [Context API Pattern](/architecture/state/context-api-pattern.md)
- [State Colocation](/architecture/state/state-colocation.md)
- [URL as Filter State](/architecture/state/url-filter-state.md)
- [Custom Hooks Pattern](/architecture/components/custom-hooks.md)
