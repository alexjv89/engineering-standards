# Event Tracking & Analytics Pattern

Track AI chatbot interactions for observability, analytics, and compliance using dual-destination pattern.

## Dual-Destination Tracking Pattern

Track all events to both database and external analytics service:

```javascript
import { trackServerEvent } from '@fob/lib-ui/umami/server';
import { getDB } from '@/database';

/**
 * Generic event tracking function
 * Logs to the database (the record) and Umami (analytics)
 */
export async function trackEvent(eventName, userId, contextId, eventProperties = {}) {
  // 1. Database tracking (always reliable)
  try {
    const db = getDB();
    await db.EventLog.create({
      user: userId,
      context: contextId,
      name: eventName,
      details: eventProperties,
      created_at: new Date(),
    });
  } catch (dbError) {
    console.error('[Database] Error:', dbError);
    // Continue to Umami even if DB fails
  }

  // 2. Analytics (best-effort; trackServerEvent never throws)
  await trackServerEvent(eventName, {
    user_id: userId,
    data: { ...eventProperties, context_id: contextId },
  });
}
```

## Related Notes
- [Product Analytics With Self-Hosted Umami](/architecture/analytics/umami-product-analytics.md) - `trackServerEvent` and what event data may carry
- [AI SDK Server Integration](/architecture/ai/ai-sdk-server-streaming.md) - Where tracking is integrated
- [Event Tracking Chat Events](/architecture/ai/event-tracking-chat-events.md) - Chat-specific event tracking
- [Tool Call Structure](/architecture/ai/tool-call-structure.md) - Understanding tool calls
