# State Colocation

Keep state as close to where it's used as possible to improve performance and maintainability.

## The Problem

State placed too high in the component tree causes unnecessary re-renders:

```javascript
// Bad - state too high
function App() {
  // Hook state: camelCase
  const [searchQuery, setSearchQuery] = useState('');
  const [theme, setTheme] = useState('light');
  const [isMenuOpen, setIsMenuOpen] = useState(false);

  return (
    <div>
      <Header theme={theme} />
      <SearchBar query={searchQuery} onChange={setSearchQuery} />
      <SideMenu isOpen={isMenuOpen} onToggle={setIsMenuOpen} />
      <Footer theme={theme} />
    </div>
  );
}
// Every state change re-renders entire App
```

## The Solution

Move state to the component that actually needs it:

```javascript
// Good - state colocated
function SearchSection() {
  // Hook state: camelCase
  const [searchQuery, setSearchQuery] = useState('');
  return <SearchBar query={searchQuery} onChange={setSearchQuery} />;
}

function Navigation() {
  // Hook state: camelCase
  const [isMenuOpen, setIsMenuOpen] = useState(false);
  return <SideMenu isOpen={isMenuOpen} onToggle={setIsMenuOpen} />;
}

function App() {
  // Hook state: camelCase
  const [theme, setTheme] = useState('light');  // Only shared state here

  return (
    <div>
      <Header theme={theme} />
      <SearchSection />
      <Navigation />
      <Footer theme={theme} />
    </div>
  );
}
```

## Benefits

- **Performance**: Fewer components re-render
- **Maintainability**: State near usage is easier to understand
- **Testability**: Components more self-contained

## When to Lift State

Lift state only when components actually need to share it:

```javascript
// Siblings need to share - lift to parent
function ParentComponent() {
  // Hook state: camelCase
  const [selectedId, setSelectedId] = useState(null);

  return (
    <div>
      <List items={items} onSelect={setSelectedId} />
      <Details itemId={selectedId} />
    </div>
  );
}
```

## Rule of Thumb

Start with state in the component that needs it. Lift only when:
- Siblings need to communicate
- Parent needs to coordinate children
- Multiple distant components need same data

## Related Notes
- [State Management: Local vs Global](/architecture/state/state-management-local-vs-global.md)
- [Context API Pattern](/architecture/state/context-api-pattern.md)
- [URL as Filter State](/architecture/state/url-filter-state.md)
- [Component Composition](/architecture/components/component-composition.md)
