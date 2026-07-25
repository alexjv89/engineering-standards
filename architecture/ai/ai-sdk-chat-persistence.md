# AI SDK Chat Persistence

Persist chat history to localStorage with the useChat hook.

## Automatic Persistence

The `useChat()` hook automatically persists messages to localStorage when you provide an `id`:

```javascript
function ChatBot({ chat, initialMessages = [] }) {
  const chatId = useMemo(() => chat.id || generateUUID(), [chat.id]);

  const { messages } = useChat({
    id: chatId,  // Enables automatic localStorage persistence
    initialMessages: transformedMessages
  });

  // Messages are automatically saved and restored
  return <MessageList messages={messages} />;
}
```

## Accessing Stored Messages

Retrieve saved messages directly from localStorage:

```javascript
/**
 * Get stored messages for a chat from localStorage
 * @param {string} chatId - Chat identifier
 * @returns {Array} Stored messages or empty array
 */
function getStoredMessages(chatId) {
  try {
    const stored = localStorage.getItem(`chat-${chatId}`);
    return stored ? JSON.parse(stored) : [];
  } catch (error) {
    console.error('Error reading stored messages:', error);
    return [];
  }
}

// Usage
const savedMessages = getStoredMessages('chat-123');
```

## Clearing Stored Chat

Remove a chat and its history from storage:

```javascript
/**
 * Clear stored chat data
 * @param {string} chatId - Chat identifier
 */
function clearChat(chatId) {
  try {
    localStorage.removeItem(`chat-${chatId}`);
    console.log(`Cleared chat: ${chatId}`);
  } catch (error) {
    console.error('Error clearing chat:', error);
  }
}

// Usage
<button onClick={() => clearChat(chatId)}>Delete Chat</button>
```

## Chat History Management

```javascript
/**
 * Get all saved chats from localStorage
 * @returns {Array} List of chat IDs
 */
function getStoredChats() {
  const chats = [];
  for (let i = 0; i < localStorage.length; i++) {
    const key = localStorage.key(i);
    if (key?.startsWith('chat-')) {
      chats.push(key.replace('chat-', ''));
    }
  }
  return chats;
}

/**
 * Get total message count across all chats
 */
function getTotalStoredMessages() {
  return getStoredChats().reduce((total, chatId) => {
    return total + getStoredMessages(chatId).length;
  }, 0);
}

/**
 * Clear all stored chats
 */
function clearAllChats() {
  getStoredChats().forEach(chatId => clearChat(chatId));
}
```

## Exporting Chat History

```javascript
/**
 * Export chat as JSON file
 */
function exportChat(chatId, filename = `chat-${chatId}.json`) {
  const messages = getStoredMessages(chatId);
  const data = JSON.stringify(messages, null, 2);
  const blob = new Blob([data], { type: 'application/json' });
  const url = URL.createObjectURL(blob);
  const link = document.createElement('a');
  link.href = url;
  link.download = filename;
  link.click();
  URL.revokeObjectURL(url);
}

/**
 * Export chat as text/markdown
 */
function exportChatAsMarkdown(chatId, filename = `chat-${chatId}.md`) {
  const messages = getStoredMessages(chatId);
  let markdown = `# Chat History\n\n`;

  messages.forEach(msg => {
    const role = msg.role.toUpperCase();
    const text = msg.parts?.[0]?.text || msg.content || '';
    markdown += `## ${role}\n\n${text}\n\n`;
  });

  const blob = new Blob([markdown], { type: 'text/markdown' });
  const url = URL.createObjectURL(blob);
  const link = document.createElement('a');
  link.href = url;
  link.download = filename;
  link.click();
  URL.revokeObjectURL(url);
}
```

## Storage Limits and Management

```javascript
// Check available storage
function getStorageStatus() {
  if (!navigator.storage?.estimate) {
    console.warn('Storage API not available');
    return null;
  }

  return navigator.storage.estimate().then(estimate => ({
    usage: estimate.usage,
    quota: estimate.quota,
    percentUsed: (estimate.usage / estimate.quota) * 100
  }));
}

// Warning when storage is low
async function checkStorageWarning() {
  const status = await getStorageStatus();
  if (status.percentUsed > 80) {
    console.warn('Storage is 80% full');
    return true;
  }
  return false;
}

// Cleanup old chats
function cleanupOldChats(maxAgeDays = 30) {
  // In a real app, you'd store timestamps with chats
  // This is a simplified example
  const chats = getStoredChats();
  chats.forEach(chatId => {
    // Check age and clear if old
  });
}
```

## Related Notes
- [AI SDK Client Hooks](/architecture/ai/ai-sdk-client-hooks.md) - useChat configuration
- [Message Format Normalization](/architecture/ai/message-format-normalization.md) - Message structure
