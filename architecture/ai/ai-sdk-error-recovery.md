# AI SDK Error Recovery Patterns

Handle and recover from chat errors using the useChat hook.

## Error State Management

```javascript
function ChatBot() {
  const [chatError, setChatError] = useState(null);
  const { messages, sendMessage, error } = useChat({
    onError: (error) => {
      console.error("Chat error:", error);
      setChatError(error);
    }
  });

  return (
    <>
      {chatError && (
        <ErrorMessage
          error={chatError}
          onRetry={handleRetry}
          onReset={handleReset}
        />
      )}
    </>
  );
}
```

## Retry Last Message

Implement retry logic to resend the last user message:

```javascript
const handleRetry = () => {
  setChatError(null);

  // Find the last user message
  const lastUserMessage = messages
    .filter(m => m.role === 'user')
    .pop();

  if (lastUserMessage) {
    // Extract text from parts array
    const text = lastUserMessage.parts
      ?.find(p => p.type === 'text')?.text;

    if (text) {
      sendMessage({ text });
    }
  }
};
```

## Reset and Start Fresh

Allow users to clear state and start over:

```javascript
const handleReset = () => {
  setChatError(null);
  // Option 1: Full page reload
  window.location.reload();

  // Option 2: Clear local state (if managing messages locally)
  // setMessages([]);
  // setChatId(generateUUID());
};
```

## Status Monitoring for User Feedback

Monitor loading states to show appropriate UI:

```javascript
function ChatBot() {
  const { status, stop } = useChat();

  // Status values: "idle", "streaming", "generating", "pending"
  const isLoading = status !== 'idle';

  return (
    <>
      {/* Show loading indicator */}
      {status === 'streaming' && (
        <div className="loading">Streaming response...</div>
      )}
      {status === 'generating' && (
        <div className="loading">Generating response...</div>
      )}

      {/* Disable send while processing */}
      <button onClick={handleSubmit} disabled={isLoading}>
        {isLoading ? 'Sending...' : 'Send'}
      </button>

      {/* Allow stopping long operations */}
      {isLoading && <button onClick={stop}>Stop</button>}
    </>
  );
}
```

## Error UI Component

```javascript
function ErrorMessage({ error, onRetry, onReset }) {
  const errorMessage = error?.message || 'An error occurred';

  return (
    <div className="error-container">
      <div className="error-icon">⚠️</div>
      <div className="error-message">{errorMessage}</div>
      <div className="error-actions">
        <button onClick={onRetry} className="btn-retry">
          Retry
        </button>
        <button onClick={onReset} className="btn-reset">
          Start Over
        </button>
      </div>
    </div>
  );
}
```

## Related Notes
- [AI SDK Client Hooks](/architecture/ai/ai-sdk-client-hooks.md) - Core useChat hook setup
- [AI SDK Server Integration](/architecture/ai/ai-sdk-server-streaming.md) - Server-side error handling
