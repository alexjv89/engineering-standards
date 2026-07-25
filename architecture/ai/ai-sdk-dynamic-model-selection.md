# AI SDK Dynamic Model Selection

Let users switch AI models at runtime.

## Model Selection State

```javascript
function ChatBot({ chat, initialMessages = [] }) {
  const [llmModel, setLlmModel] = useState("gpt-4.1");

  const { p_id, t_id } = useParams();

  // useChat hook with dynamic model in transport
  const { messages, sendMessage } = useChat({
    transport: new DefaultChatTransport({
      body: () => ({
        p_id,
        t_id,
        llmModel  // Pass current model to server
      })
    })
  });

  return (
    <>
      {/* Messages and input */}
      <MessageList messages={messages} />

      {/* Model selector */}
      <ModelSelector value={llmModel} onChange={setLlmModel} />
    </>
  );
}
```

## Model Selection Component

```javascript
function ModelSelector({ value, onChange }) {
  const models = [
    {
      id: 'gpt-4.1',
      name: 'GPT-4.1 Turbo',
      description: 'Best for complex reasoning',
      cost: 'higher'
    },
    {
      id: 'gpt-4.1-nano',
      name: 'GPT-4.1 Nano',
      description: 'Faster and cheaper',
      cost: 'lower'
    },
    {
      id: 'gpt-4o',
      name: 'GPT-4o',
      description: 'Latest capabilities',
      cost: 'highest'
    }
  ];

  return (
    <select
      value={value}
      onChange={(e) => onChange(e.target.value)}
      className="model-selector"
    >
      {models.map(model => (
        <option key={model.id} value={model.id}>
          {model.name} ({model.cost})
        </option>
      ))}
    </select>
  );
}
```

## Programmatic Model Switching

```javascript
const handleSwitchModel = (newModel) => {
  setLlmModel(newModel);
  // Next message will use the new model
};

// Examples:
const switchToFastModel = () => handleSwitchModel('gpt-4.1-nano');
const switchToBestModel = () => handleSwitchModel('gpt-4.1');
const switchToLatest = () => handleSwitchModel('gpt-4o');
```

## Model Context and Documentation

```javascript
const MODEL_INFO = {
  'gpt-4.1': {
    name: 'GPT-4.1 Turbo',
    use_cases: [
      'Complex reasoning',
      'Initial questions',
      'Multi-step reasoning'
    ],
    latency: 'Medium (3-5s)',
    cost_per_token: 'High'
  },
  'gpt-4.1-nano': {
    name: 'GPT-4.1 Nano',
    use_cases: [
      'Follow-up questions',
      'Simple queries',
      'High-volume interactions'
    ],
    latency: 'Fast (1-2s)',
    cost_per_token: 'Low'
  },
  'gpt-4o': {
    name: 'GPT-4o',
    use_cases: [
      'Latest features',
      'Multi-modal content',
      'Highest accuracy'
    ],
    latency: 'Medium-Fast (2-4s)',
    cost_per_token: 'Medium-High'
  }
};

// Display info to users
function ModelInfo({ model }) {
  const info = MODEL_INFO[model];
  return (
    <div className="model-info">
      <h4>{info.name}</h4>
      <p>Latency: {info.latency}</p>
      <p>Cost: {info.cost_per_token}</p>
      <ul>
        {info.use_cases.map(uc => <li key={uc}>{uc}</li>)}
      </ul>
    </div>
  );
}
```

## Auto-Selection Strategy

```javascript
function ChatBot({ initialMessages = [] }) {
  const [llmModel, setLlmModel] = useState('auto');

  const determineModel = () => {
    if (llmModel !== 'auto') return llmModel;

    // Auto-select based on context
    const isInitialQuestion = initialMessages.length === 0;
    const messageCount = initialMessages.length;

    if (isInitialQuestion) {
      return 'gpt-4.1'; // Use best model for first question
    }

    if (messageCount > 10) {
      return 'gpt-4.1-nano'; // Use fast model for long conversations
    }

    return 'gpt-4.1'; // Default to best
  };

  const activeModel = determineModel();

  const { messages, sendMessage } = useChat({
    transport: new DefaultChatTransport({
      body: () => ({ llmModel: activeModel })
    })
  });

  return (
    <>
      <MessageList messages={messages} />
      <ModelSelector value={llmModel} onChange={setLlmModel} />
      <p className="model-note">Using: {activeModel}</p>
    </>
  );
}
```

## Related Notes
- [AI SDK Client Hooks](/architecture/ai/ai-sdk-client-hooks.md) - Core useChat setup
- [AI SDK Server Integration](/architecture/ai/ai-sdk-server-streaming.md) - Receiving model selection on server
