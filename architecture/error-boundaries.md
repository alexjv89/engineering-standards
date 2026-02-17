# Error Boundaries for React

Catch React errors gracefully using error boundary components.

**Note**: Error boundaries are the **only** valid use of classes in React. They cannot be implemented with functional components.

## Pattern

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

## Usage

Wrap components that might throw errors:

```javascript
function App() {
  return (
    <ErrorBoundary>
      <Header />
      <ErrorBoundary>
        <MainContent />
      </ErrorBoundary>
      <Footer />
    </ErrorBoundary>
  );
}
```

## Fallback UI

```javascript
function ErrorFallback({ error }) {
  return (
    <div role="alert">
      <h2>Something went wrong</h2>
      <p>{error.message}</p>
      <button onClick={() => window.location.reload()}>
        Reload page
      </button>
    </div>
  );
}
```

## What Error Boundaries Catch

**Catches:**
- Errors during rendering
- Errors in lifecycle methods
- Errors in constructors

**Does NOT catch:**
- Event handlers (use try/catch)
- Async code (use try/catch)
- Server-side rendering errors
- Errors in error boundary itself

## Event Handler Errors

Handle separately with try/catch:

```javascript
function MyComponent() {
  const handleClick = async () => {
    try {
      await riskyOperation();
    } catch (error) {
      console.error('Operation failed:', error);
      showErrorToast(error.message);
    }
  };

  return <button onClick={handleClick}>Do Something</button>;
}
```

## Multiple Boundaries

Use multiple error boundaries for granular error handling:

```javascript
function App() {
  return (
    <div>
      <ErrorBoundary FallbackComponent={HeaderError}>
        <Header />
      </ErrorBoundary>

      <ErrorBoundary FallbackComponent={MainError}>
        <MainContent />
      </ErrorBoundary>

      <Footer />  {/* No boundary - let it fail upward */}
    </div>
  );
}
```

## Why Classes Here?

React error boundaries **require** class components because they use lifecycle methods (`componentDidCatch`, `getDerivedStateFromError`) that don't have functional equivalents.

This is an exception to the functional programming principle - use it only for error boundaries.

## Related Notes
- [Functional Programming Principle](/principles/functional-programming.md)
