# Background Tasks: Cron Jobs

Implement scheduled recurring tasks using cron job pattern.

## Pattern

```javascript
// jobs/dailyReport.js
import cron from 'node-cron';
import { generateDailyReport } from '../services/reportService';

// Run every day at 6 AM
cron.schedule('0 6 * * *', async () => {
  console.log('Running daily report generation...');
  try {
    await generateDailyReport();
    console.log('Daily report generated successfully');
  } catch (error) {
    console.error('Failed to generate daily report:', error);
    await alertOncall(error);
  }
});
```

## Cron Schedule Format

```
* * * * *
│ │ │ │ └─── Day of week (0-7, both 0 and 7 = Sunday)
│ │ │ └───── Month (1-12)
│ │ └─────── Day of month (1-31)
│ └───────── Hour (0-23)
└─────────── Minute (0-59)
```

## Common Schedules

```javascript
cron.schedule('0 * * * *', handler);      // Every hour
cron.schedule('0 0 * * *', handler);      // Daily at midnight
cron.schedule('0 9 * * 1', handler);      // Every Monday at 9 AM
cron.schedule('*/15 * * * *', handler);   // Every 15 minutes
```

## Best Practices

```javascript
async function runScheduledTask() {
  const taskId = `task-${Date.now()}`;

  try {
    // Check idempotency
    const existing = await cache.get(taskId);
    if (existing) return;

    // Set timeout
    const result = await Promise.race([
      performTask(),
      timeout(30000)
    ]);

    await cache.set(taskId, result, 3600);
    console.log('Task completed:', taskId);
  } catch (error) {
    console.error('Task failed:', error);
    await alertOncall(error);
  }
}

cron.schedule('0 * * * *', runScheduledTask);
```

## Related Notes
- [Job Queue Pattern](/git-workflow/job-queue-pattern.md) - Asynchronous task processing
- [Environment File Structure](/git-workflow/environment-file-structure.md) - Configuration management
