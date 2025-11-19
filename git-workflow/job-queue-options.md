# Job Queue Options

Configuration options for job queue tasks.

## Standard Options

```javascript
emailQueue.add(
  { to, subject, body },
  {
    attempts: 3,              // Retry up to 3 times
    backoff: {
      type: 'exponential',    // Exponential backoff
      delay: 2000,            // Start with 2s delay
    },
    delay: 5000,              // Delay job by 5 seconds
    priority: 1,              // Higher priority = processed first
    removeOnComplete: true,   // Auto-remove on completion
  }
);
```

## Retry Configuration

```javascript
{
  attempts: 3,           // Number of retry attempts
  backoff: {
    type: 'exponential', // exponential | fixed
    delay: 2000,         // Initial delay in milliseconds
  },
}
```

**Exponential backoff**: Delays increase exponentially (2s, 4s, 8s, etc.)
**Fixed backoff**: Same delay between each retry

## Prioritization

```javascript
// High priority job
await emailQueue.add(data, { priority: 1 });

// Normal priority job
await emailQueue.add(data, { priority: 5 });

// Low priority job
await emailQueue.add(data, { priority: 10 });
```

**Rule**: Lower number = higher priority

## Delayed Jobs

```javascript
// Process job 5 seconds from now
await emailQueue.add(data, { delay: 5000 });

// Process job at specific timestamp
await emailQueue.add(data, { delay: Date.now() + 60000 });
```

## Job Cleanup

```javascript
{
  removeOnComplete: true,    // Remove successful jobs
  removeOnFail: false,       // Keep failed jobs for debugging
}
```

## Related Notes
- [Job Queue Pattern](/git-workflow/job-queue-pattern.md) - Basic job queue setup
- [Job Queue Error Handling](/git-workflow/job-queue-error-handling.md) - Error handling and retries
- [Background Tasks: Cron Jobs](/git-workflow/background-tasks-cron.md) - Scheduled tasks
