# Job Queue Error Handling

Error handling and monitoring for job queues.

## Event Listeners

```javascript
emailQueue.on('failed', (job, err) => {
  console.error(`Job ${job.id} failed:`, err);
  // Alert if critical
  if (job.opts.priority === 1) {
    alertOncall(err);
  }
});

emailQueue.on('completed', (job) => {
  console.log(`Job ${job.id} completed`);
});
```

## Available Events

```javascript
emailQueue.on('failed', (job, err) => {
  // Job failed after all retries
});

emailQueue.on('completed', (job, result) => {
  // Job completed successfully
});

emailQueue.on('error', (error) => {
  // Queue error occurred
});

emailQueue.on('stalled', (job) => {
  // Job has stalled (worker crashed)
});
```

## Error Handling in Processor

```javascript
emailQueue.process(async (job) => {
  try {
    const { to, subject, body } = job.data;
    await sendEmail(to, subject, body);
    return { success: true };
  } catch (error) {
    // Log error
    console.error('Email send failed:', error);

    // Throw to trigger retry
    throw error;
  }
});
```

## Alerting on Failures

```javascript
emailQueue.on('failed', (job, err) => {
  // Check if job exhausted all retries
  if (job.attemptsMade >= job.opts.attempts) {
    // Send alert to monitoring system
    alertOncall({
      message: `Email job failed after ${job.attemptsMade} attempts`,
      error: err,
      data: job.data,
    });
  }
});
```

## Related Notes
- [Job Queue Pattern](/git-workflow/job-queue-pattern.md) - Basic job queue setup
- [Job Queue Options](/git-workflow/job-queue-options.md) - Job configuration and retry options
- [Background Tasks: Cron Jobs](/git-workflow/background-tasks-cron.md) - Scheduled tasks
