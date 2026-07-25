# AI Chatbot Implementation Recommendations

Standardized AI chatbot patterns, drawn from the elevate-ai project.

## Executive Summary

The elevate-ai project demonstrates a production-ready AI chatbot implementation using Vercel AI SDK v5. Key strengths include:

- ✅ Clean separation of server/client concerns
- ✅ Dual-destination analytics (database + external service)
- ✅ Dynamic prompt generation based on context
- ✅ Comprehensive tool definition pattern with generator functions
- ✅ Admin interface for tool testing and debugging
- ✅ Proper message format normalization

---

## 1. Recommended Architecture Pattern

### Directory Structure
```
/src
├── /lib/ai/                        # AI orchestration
│   ├── generateSystemPrompt.js
│   ├── generateDevicePrompt.js
│   ├── messageNormalizer.js
│   └── toolCallAnalyzer.js
│
├── /tools/                         # Tool definitions
│   ├── index.js                    # Central exports
│   ├── generators/
│   │   ├── getMetrics.js
│   │   ├── getTimeline.js
│   │   └── ...others
│   └── __tests__/                  # Unit tests for each tool
│
├── /app/api/chat/                  # Chat endpoints
│   ├── route.js                    # Main streaming endpoint
│   ├── handlers/
│   │   ├── messageHandler.js
│   │   ├── toolHandler.js
│   │   └── analyticsHandler.js
│   └── __tests__/
│
├── /components/ChatBot/            # UI components
│   ├── ChatBot.jsx                 # Main container
│   ├── ChatHistory.jsx
│   ├── MessageRenderer.jsx
│   ├── ToolCallRenderer.jsx
│   └── __tests__/
│
└── /utils/analytics/               # Observability
    ├── amplitude.js                # External analytics
    ├── eventTracker.js
    └── errorReporter.js            # Sentry integration
```

### Naming Conventions

**Tool Generators**: `generate<ToolName>`
```javascript
export function generateGetMetrics({ cookieStore, p_id }) { ... }
export function generateGetTimeline({ cookieStore, p_id }) { ... }
```

**System Prompts**: `generate<Context>SystemPrompt`
```javascript
export async function generateSystemPrompt(p_id, cookieStore) { ... }
export async function generateDeviceSystemPrompt(p_id, d_id, cookieStore) { ... }
```

**Event Tracking**: `track<EventType>`
```javascript
export async function trackChatQuestion(params) { ... }
export async function trackToolExecution(params) { ... }
```

---

## 2. Vercel AI SDK v5 Best Practices

### Message Format Standard

All internal message handling should use the **parts array format**:

```javascript
// Standard format
{
  id: string,
  role: 'user' | 'assistant' | 'tool',
  parts: [
    { type: 'text', text: string },
    { type: 'tool-call', toolName: string, args: object },
    { type: 'tool-result', toolName: string, result: object }
  ],
  createdAt?: Date
}
```

**Conversion responsibility**: Client must normalize before sending to server.

### Server Streaming Requirements

1. **Always validate messages** before streaming:
```javascript
const validated = await validateUIMessages({ messages, tools });
```

2. **Always convert before streaming**:
```javascript
const modelMessages = convertToModelMessages(validated);
```

3. **Always set step limit** to prevent infinite loops:
```javascript
stopWhen: stepCountIs(5)  // Reasonable default
```

4. **Always implement onFinish** for cleanup:
```javascript
onFinish: async (event) => {
  // Persist chat
  // Track analytics
  // Cleanup resources
}
```

### Model Selection Strategy

| Scenario | Model | Rationale |
|----------|-------|-----------|
| Complex reasoning, first message | `gpt-4.1` | Higher accuracy, better tool understanding |
| Follow-up questions | `gpt-4.1-nano` | Faster, cheaper for clarifications |
| High-volume interactive | `gpt-4o` | Latest, best capabilities |
| Cost-sensitive | `gpt-4.1-nano` | 80% cheaper, 15% slower |

**Implementation**: Allow user selection or detect via context:
```javascript
const model = llmModel === 'auto'
  ? isInitialQuestion ? 'gpt-4.1' : 'gpt-4.1-nano'
  : llmModel;
```

---

## 3. Tool Definition Standard

### Required Pattern

Every tool MUST be a generator function that:
1. **Accepts context parameters**: `{ cookieStore, p_id, d_id?, ... }`
2. **Returns a tool definition** with Zod schema
3. **Handles errors gracefully** without throwing

```javascript
export function generateGetMetrics({ cookieStore, p_id }) {
  return tool({
    description: '...',
    inputSchema: z.object({...}),
    execute: async (params) => {
      try {
        // Implementation
        return result;
      } catch (error) {
        // Log but don't throw
        return { error: error.message };
      }
    }
  });
}
```

### Tool Documentation Requirements

Each tool MUST document:
- **Purpose**: One-sentence description
- **Parameters**: Zod schema with examples
- **Return format**: Expected response structure
- **Error handling**: How failures are handled
- **Data freshness**: Caching strategy if any
- **Performance**: Typical execution time

### Tool Testing Requirements

Each tool MUST have unit tests:
```javascript
// tools/__tests__/getMetrics.test.js
describe('getMetrics', () => {
  it('should return metrics for valid params', async () => {
    const tool = generateGetMetrics({ cookieStore, p_id: 'plant-1' });
    const result = await tool.execute({
      metric: 'oee',
      start: '2024-01-01T00:00:00Z',
      end: '2024-01-31T23:59:59Z'
    });
    expect(result).toHaveProperty('metric');
    expect(result).toHaveProperty('units');
  });
});
```

---

## 4. Analytics & Observability Standard

### Dual-Destination Strategy (REQUIRED)

All events MUST go to both database and external service:

```javascript
export async function trackEvent(eventName, userId, plantId, details = {}) {
  // 1. Database (primary - must succeed)
  try {
    await db.EventLog.create({ name: eventName, user: userId, plant: plantId, details });
  } catch (dbError) {
    console.error('Database tracking failed:', dbError);
    // Continue to external analytics
  }

  // 2. External analytics (best-effort)
  try {
    await amplitude.track(eventName, { user_id: userId, ...details });
  } catch (analyticsError) {
    console.error('Analytics tracking failed:', analyticsError);
    // Don't throw
  }
}
```

### Mandatory Events to Track

| Event | Trigger | Data |
|-------|---------|------|
| `chat_started` | User opens chat | user_id, plant_id, device_id |
| `message_sent` | User sends message | user_id, question_length, attachments_count |
| `tools_called` | Tool invocation | tool_names, param_count, success |
| `response_generated` | AI response complete | response_length, tool_count, latency |
| `chat_error` | Any error | error_type, error_message, context |

### Never Track
- ❌ Full user messages (security)
- ❌ System prompts with sensitive config
- ❌ Authentication credentials
- ❌ Raw tool responses with PII

---

## 5. Admin Interface Requirements

Projects implementing AI chatbots SHOULD include:

### Minimum Admin Features
1. **System Prompt Viewer**
   - Display current system prompt
   - Show prompt version/generation date
   - Display token count estimate

2. **Tool Testing Interface**
   - List all available tools
   - Display tool schemas (Zod definitions)
   - Interactive parameter forms
   - Execute and display results

3. **Chat Analytics Dashboard**
   - User questions over time
   - Tool usage distribution
   - Error rate tracking
   - Model performance comparison

### Admin Code Pattern
```javascript
// App Router page component
async function AdminToolsPage() {
  const toolsMetadata = await loadToolMetadata();

  return (
    <div>
      <h1>Tool Testing</h1>
      <ToolsList tools={toolsMetadata} />
      {/* Tool execution interface */}
    </div>
  );
}
```

---

## 6. Error Handling Strategy

### Graceful Degradation Pattern

```javascript
const result = await streamText({
  model: openai(llmModel),
  tools: tools,
  stopWhen: stepCountIs(5),
  onFinish: async (event) => {
    try {
      // Persist and track
    } catch (error) {
      // Log but don't re-throw
      console.error('Post-completion error:', error);
    }
  }
});

// Don't let analytics failures break the chat
```

### Client Error Recovery

```javascript
const { error, messages } = useChat({
  onError: (error) => {
    // Show user-friendly message
    setErrorMessage('Failed to get response. Please try again.');
    // Log for debugging
    logError(error);
  }
});

// Provide retry button
<button onClick={() => retryLastQuestion()}>Retry</button>
```

---

## 7. Message Persistence Requirements

### Database Schema (Recommended)

```javascript
// Chats table
{
  id: UUID,
  title: TEXT,           // Auto-generated from first message
  user_id: STRING,
  plant_id: STRING,
  visibility: ENUM('private', 'shared'),
  created_at: DATE,
  updated_at: DATE
}

// Messages table
{
  id: UUID,
  chat_id: UUID,         // Foreign key
  role: STRING,
  parts: JSONB,          // Always use parts format
  attachments: JSONB,    // Optional
  created_at: DATE
}
```

### Message Persistence Requirements
- ✅ Persist all user and assistant messages
- ✅ Optionally persist tool calls and results
- ✅ Load previous messages on chat open
- ✅ Support pagination for long conversations
- ❌ Don't persist temporary UI state

---

## 8. Performance Optimization

### Token Usage Minimization
- Keep system prompts focused (< 1000 tokens)
- Limit device lists (top 10-20, not all)
- Summarize rather than enumerate metrics
- Cache prompts when content is stable

### Response Time Targets
- **Initial response**: < 3 seconds
- **Tool execution**: < 5 seconds per tool
- **Full response**: < 10 seconds

### Latency Optimization
```javascript
// Use parallel data fetching
const [plant, devices, metrics] = await Promise.all([
  getPlant(p_id),
  getDevices(p_id),
  getBaselineMetrics(p_id)
]);

// Stream response immediately, fetch more data in background
const result = streamText({
  // Start streaming while tools execute
  // Don't wait for all tool results
});
```

---

## 9. Testing Requirements

### Unit Tests
- **Tools**: Test each tool with valid/invalid params
- **Prompts**: Verify prompt generation logic
- **Messages**: Test format normalization
- **Analytics**: Verify event structure

### Integration Tests
- **Chat flow**: Full message → response cycle
- **Tool calling**: Request → execution → result
- **Error handling**: Graceful failure modes
- **Analytics**: Events reach both destinations

### E2E Tests
- **Chat interaction**: User sends message → gets response
- **Tool results**: Display tool data correctly
- **Error recovery**: Retry mechanisms work
- **Persistence**: Messages saved and loaded

---

## 10. Migration Guide for Existing Projects

### Phase 1: Add AI Patterns (Week 1)
1. Create `/lib/ai/` directory
2. Implement message normalizer
3. Create 2-3 core tools
4. Add basic prompt generation

### Phase 2: Improve Analytics (Week 2)
1. Set up EventLog table
2. Implement dual-destination tracking
3. Add chat persistence
4. Create admin interfaces

### Phase 3: Optimize & Standardize (Week 3)
1. Add comprehensive tool testing
2. Implement error recovery
3. Optimize token usage
4. Add unit/integration tests

### Phase 4: Documentation & Training (Week 4)
1. Document tool patterns
2. Create examples
3. Train team on standards
4. Establish code review checklist

---

## 11. Code Review Checklist

When reviewing AI chatbot code, verify:

### Server-Side
- [ ] Messages normalized to parts format
- [ ] `validateUIMessages()` called
- [ ] `convertToModelMessages()` used
- [ ] `stopWhen: stepCountIs(N)` set
- [ ] `onFinish` handler implemented
- [ ] Error handling doesn't throw
- [ ] Analytics tracking in place
- [ ] Tool definitions are generators
- [ ] Zod schemas on all tools
- [ ] maxDuration set appropriately

### Client-Side
- [ ] useChat hook properly configured
- [ ] Initial messages transformed
- [ ] Input validation before send
- [ ] Error state handled
- [ ] Loading states shown
- [ ] Message history displayed
- [ ] Tool calls rendered correctly
- [ ] Retry mechanism available

### Testing
- [ ] Tools have unit tests
- [ ] Integration tests for chat flow
- [ ] Error cases tested
- [ ] Analytics events verified

---

## 12. Recommended Extensions

### Future Improvements

1. **Multi-LLM Support**
   - Support Claude, Anthropic, local models
   - Abstraction layer for provider switching

2. **Prompt Versioning**
   - Store multiple prompt versions
   - A/B test different prompts
   - Track which version generates best results

3. **Custom Tool Libraries**
   - Domain-specific tool packs (HR, Finance, Ops)
   - Shareable tool definitions
   - Tool marketplace concept

4. **Advanced Analytics**
   - Conversation quality metrics
   - Tool usage patterns
   - User satisfaction tracking
   - Cost optimization dashboard

5. **Caching Strategy**
   - Cache frequent tool results
   - Cache system prompts
   - Cache conversation summaries
   - Reduce token usage

6. **Safety & Moderation**
   - Input moderation (block unsafe queries)
   - Output moderation (block unsafe responses)
   - Sensitive data detection
   - Rate limiting per user

---

## Atomic Notes Reference

All patterns documented in `/architecture/ai/`:

1. **[ai-tool-definition-pattern.md](/architecture/ai/ai-tool-definition-pattern.md)** - Creating tools
2. **[ai-sdk-server-streaming.md](/architecture/ai/ai-sdk-server-streaming.md)** - Server integration
3. **[ai-sdk-client-hooks.md](/architecture/ai/ai-sdk-client-hooks.md)** - Client integration
4. **[message-format-normalization.md](/architecture/ai/message-format-normalization.md)** - Message handling
5. **[tool-call-structure.md](/architecture/ai/tool-call-structure.md)** - Tool execution
6. **[system-prompt-generation.md](/architecture/ai/system-prompt-generation.md)** - Prompt patterns
7. **[event-tracking-analytics.md](/architecture/ai/event-tracking-analytics.md)** - Observability

---

## Questions & Decision Points

For projects planning AI implementation, decide on:

1. **Model Selection**: Primary model and fallback?
2. **Tool Scope**: Which tools are essential? Which are nice-to-have?
3. **Persistence**: Save all messages or only user messages?
4. **Analytics**: Which events are critical to track?
5. **Admin Features**: Which admin tools are needed?
6. **Scale**: Expected concurrent users? Message volume?
7. **Cost Budget**: Token budget? Acceptable cost per query?
8. **Latency Requirements**: Real-time vs eventual consistency?

---

## Summary

Adopt these patterns team-wide; customize per domain.

**Next Steps**:
1. Review atomic notes in `/architecture/ai/`
2. Implement core patterns in your project
3. Establish code review standards
4. Train team on new patterns
5. Iterate based on production experience
