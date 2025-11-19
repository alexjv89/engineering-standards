# Job Queue Pattern

Implement asynchronous task processing using job queue pattern (Bull/BullMQ).

## Basic Pattern

```javascript
// queues/emailQueue.js
import Queue from 'bull';

export const emailQueue = new Queue('email', {
  redis: process.env.REDIS_URL,
});

// Define job processor
emailQueue.process(async (job) => {
  const { to, subject, body } = job.data;
  console.log(`Sending email to ${to}...`);
  await sendEmail(to, subject, body);
  console.log(`Email sent to ${to}`);
});

// Add job to queue
export async function queueEmail(to, subject, body) {
  await emailQueue.add(
    { to, subject, body },
    {
      attempts: 3,
      backoff: {
        type: 'exponential',
        delay: 2000,
      },
    }
  );
}
```

## Usage Example

```javascript
// In API route or service
import { queueEmail } from './queues/emailQueue';

async function registerUser(userData) {
  const user = await createUser(userData);

  // Queue email asynchronously
  await queueEmail(
    user.email,
    'Welcome!',
    `Hi ${user.name}, welcome to our service!`
  );

  return user;
}
```

## When to Use Queues

**Use job queues for:**
- Email sending
- Image processing
- Report generation
- Data imports/exports
- Any long-running tasks

**Benefits:**
- Async processing doesn't block request
- Automatic retries on failure
- Job prioritization
- Distributed processing

## Related Notes
- [Job Queue Options](/git-workflow/job-queue-options.md) - Job configuration and options
- [Job Queue Error Handling](/git-workflow/job-queue-error-handling.md) - Error handling and retries
- [Background Tasks: Cron Jobs](/git-workflow/background-tasks-cron.md) - Scheduled tasks
- [Environment File Structure](/git-workflow/environment-file-structure.md) - Redis URL configuration
