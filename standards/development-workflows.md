# Development Workflows

This document defines workflows, processes, and best practices for day-to-day development activities.

## Table of Contents
- [Git Workflow](#git-workflow)
- [Branch Management](#branch-management)
- [Commit Standards](#commit-standards)
- [Pull Requests](#pull-requests)
- [Code Review](#code-review)
- [Background Tasks](#background-tasks)
- [Environment Management](#environment-management)
- [Deployment Process](#deployment-process)

## Git Workflow

### Branching Strategy

Use **Git Flow** or **GitHub Flow** depending on project complexity:

**GitHub Flow (Simple projects):**
```
main
  ├── feature/user-authentication
  ├── fix/login-error
  └── chore/update-dependencies
```

**Git Flow (Complex projects):**
```
main (production)
  └── develop (integration)
      ├── feature/new-dashboard
      ├── feature/api-integration
      ├── bugfix/calculation-error
      └── hotfix/critical-security-fix
```

### Branch Naming

Use descriptive, hyphenated names with type prefix:

**Format:** `<type>/<short-description>`

**Types:**
- `feature/` - New features
- `fix/` or `bugfix/` - Bug fixes
- `hotfix/` - Critical production fixes
- `chore/` - Maintenance tasks
- `refactor/` - Code refactoring
- `docs/` - Documentation only
- `test/` - Test additions/changes

**Examples:**
```bash
git checkout -b feature/user-authentication
git checkout -b fix/email-validation-error
git checkout -b chore/update-eslint-config
git checkout -b refactor/extract-api-service
```

## Branch Management

### Creating Branches

**Always branch from the latest code:**

```bash
# For GitHub Flow
git checkout main
git pull origin main
git checkout -b feature/new-feature

# For Git Flow
git checkout develop
git pull origin develop
git checkout -b feature/new-feature
```

### Keeping Branches Updated

**Regularly sync with base branch:**

```bash
# Rebase approach (preferred for clean history)
git checkout feature/my-feature
git fetch origin
git rebase origin/main

# Merge approach (if rebase causes issues)
git merge origin/main
```

### Deleting Branches

**Clean up after merging:**

```bash
# Delete local branch
git branch -d feature/completed-feature

# Delete remote branch
git push origin --delete feature/completed-feature
```

## Commit Standards

### Commit Message Format

Use **Conventional Commits** format:

**Format:**
```
<type>[optional scope]: <description>

[optional body]

[optional footer(s)]
```

**Types:**
- `feat:` - New feature
- `fix:` - Bug fix
- `docs:` - Documentation changes
- `style:` - Code style changes (formatting, no logic change)
- `refactor:` - Code refactoring
- `test:` - Test additions/changes
- `chore:` - Build process or auxiliary tool changes
- `perf:` - Performance improvements

**Examples:**
```bash
git commit -m "feat: add user authentication"
git commit -m "fix: resolve email validation error"
git commit -m "docs: update API documentation"
git commit -m "refactor: extract user service from controller"
git commit -m "test: add unit tests for calculateTotal function"
git commit -m "chore: update dependencies to latest versions"
```

**With scope:**
```bash
git commit -m "feat(auth): add OAuth2 integration"
git commit -m "fix(api): handle network timeout errors"
git commit -m "test(components): add tests for Button component"
```

**With body and footer:**
```bash
git commit -m "fix: prevent race condition in user login

Added mutex lock to prevent concurrent login attempts
from the same user causing database conflicts.

Fixes #123"
```

### Commit Best Practices

**Make atomic commits:**
- One logical change per commit
- Commit should be self-contained and functional

**Write clear messages:**
- Use imperative mood ("add feature" not "added feature")
- First line under 50 characters
- Body lines under 72 characters

**Commit frequently:**
- Small, focused commits are easier to review
- Easier to revert if needed
- Better git history for debugging

**Example workflow:**
```bash
# Work on feature
# Make a logical change
git add src/components/Button.jsx
git commit -m "feat: add primary button variant"

# Make another logical change
git add src/components/Button.test.jsx
git commit -m "test: add tests for primary button variant"

# Make another logical change
git add src/components/Button.stories.jsx
git commit -m "docs: add Storybook story for primary button"
```

## Pull Requests

### Creating Pull Requests

**PR Title:** Follow commit message format
```
feat: add user authentication system
fix: resolve login redirect issue
```

**PR Description Template:**
```markdown
## Summary
Brief description of what this PR does and why.

## Changes
- Added user authentication module
- Created login/logout API endpoints
- Implemented session management
- Added authentication middleware

## Type of Change
- [ ] New feature
- [ ] Bug fix
- [ ] Breaking change
- [ ] Documentation update

## Testing
- [ ] Unit tests added/updated
- [ ] Integration tests added/updated
- [ ] E2E tests added/updated
- [ ] Manual testing completed

## Screenshots (if applicable)
[Add screenshots for UI changes]

## Checklist
- [ ] Code follows style guidelines
- [ ] Tests pass locally
- [ ] Documentation updated
- [ ] No new warnings or errors
- [ ] Reviewers assigned

## Related Issues
Fixes #123
Related to #456
```

### PR Size Guidelines

**Keep PRs small and focused:**
- **Ideal:** 200-400 lines changed
- **Maximum:** 800 lines changed
- **Too large?** Split into multiple PRs

**Breaking up large changes:**
1. Create foundational PR (data models, types)
2. Create implementation PR (core logic)
3. Create UI PR (user interface)
4. Create testing PR (comprehensive tests)

### PR Labels

Use labels to categorize PRs:
- `feature` - New functionality
- `bug` - Bug fixes
- `documentation` - Docs changes
- `dependencies` - Dependency updates
- `breaking-change` - Breaking changes
- `needs-review` - Ready for review
- `work-in-progress` - Not ready for review
- `ready-to-merge` - Approved and ready

## Code Review

### Reviewer Responsibilities

**What to check:**
1. **Correctness** - Does the code do what it's supposed to?
2. **Testing** - Are there adequate tests?
3. **Style** - Does it follow style guidelines?
4. **Maintainability** - Is it readable and maintainable?
5. **Performance** - Are there performance concerns?
6. **Security** - Are there security vulnerabilities?

### Review Checklist

```markdown
- [ ] Code is correct and handles edge cases
- [ ] Tests are comprehensive and pass
- [ ] Code follows style guide
- [ ] No unnecessary complexity
- [ ] Performance is acceptable
- [ ] No security vulnerabilities
- [ ] Documentation is updated
- [ ] No secrets or sensitive data committed
- [ ] Error handling is appropriate
- [ ] Logging is appropriate
```

### Review Comments

**Be constructive and specific:**

```markdown
❌ Bad: "This is wrong"
✅ Good: "This will fail when user is null. Consider adding a null check."

❌ Bad: "Refactor this"
✅ Good: "This function is doing too much. Consider extracting the validation logic into a separate function."

❌ Bad: "Use map instead"
✅ Good: "We can simplify this loop using Array.map() which is more readable and functional."
```

**Use conventional prefixes:**
- `nit:` - Minor style/formatting suggestion
- `question:` - Asking for clarification
- `suggestion:` - Optional improvement
- `issue:` - Must be addressed before merging

### Responding to Reviews

**As the author:**
- Respond to all comments (even if just "Done")
- Ask questions if feedback is unclear
- Push additional commits for requested changes
- Mark conversations as resolved when addressed

**Requesting changes:**
```bash
# Make requested changes
git add .
git commit -m "fix: address review feedback"
git push origin feature/my-feature
```

## Background Tasks

### Task Types

**Cron Jobs** - Scheduled recurring tasks
**Job Queues** - Asynchronous task processing
**Webhooks** - Event-driven tasks

### Implementing Background Tasks

**Cron Job Pattern (Node.js):**

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
    // Alert monitoring system
  }
});
```

**Job Queue Pattern (Bull):**

```javascript
// queues/emailQueue.js
import Queue from 'bull';

export const emailQueue = new Queue('email', {
  redis: process.env.REDIS_URL,
});

// Define job processor
emailQueue.process(async (job) => {
  const { to, subject, body } = job.data;
  await sendEmail(to, subject, body);
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

**Webhook Handler Pattern:**

```javascript
// api/webhooks/stripe.js
import { verifyStripeSignature } from '@/utils/stripe';

export async function POST(request) {
  const signature = request.headers.get('stripe-signature');
  const payload = await request.text();

  // Verify webhook authenticity
  const event = verifyStripeSignature(payload, signature);

  // Handle event
  switch (event.type) {
    case 'payment_intent.succeeded':
      await handlePaymentSuccess(event.data.object);
      break;
    case 'payment_intent.failed':
      await handlePaymentFailure(event.data.object);
      break;
    default:
      console.log(`Unhandled event type: ${event.type}`);
  }

  return Response.json({ received: true });
}
```

### Background Task Best Practices

1. **Idempotency** - Tasks should be safe to run multiple times
2. **Error Handling** - Handle failures gracefully with retries
3. **Monitoring** - Log task execution and failures
4. **Timeouts** - Set appropriate timeouts to prevent hanging
5. **Resource Management** - Clean up resources (connections, files)

**Example with all best practices:**

```javascript
async function processUserData(userId, options = {}) {
  const taskId = `process-user-${userId}-${Date.now()}`;

  try {
    // Check if already processed (idempotency)
    const existing = await cache.get(taskId);
    if (existing) {
      console.log(`Task ${taskId} already processed`);
      return existing;
    }

    // Set timeout
    const timeoutPromise = new Promise((_, reject) =>
      setTimeout(() => reject(new Error('Task timeout')), 30000)
    );

    // Process with timeout
    const result = await Promise.race([
      performProcessing(userId),
      timeoutPromise,
    ]);

    // Cache result
    await cache.set(taskId, result, 3600);

    // Log success
    console.log(`Task ${taskId} completed successfully`);

    return result;
  } catch (error) {
    // Log error
    console.error(`Task ${taskId} failed:`, error);

    // Alert if critical
    if (options.critical) {
      await alertOncall(error);
    }

    throw error;
  }
}
```

## Environment Management

### Environment Files

**Use separate environment files per environment:**

```
.env.local          # Local development (not committed)
.env.development    # Development defaults (committed)
.env.staging        # Staging configuration (committed)
.env.production     # Production configuration (committed, no secrets)
.env.example        # Template (committed)
```

**Example `.env.example`:**
```bash
# API Configuration
API_BASE_URL=https://api.example.com
API_TIMEOUT=10000

# Database
DATABASE_URL=postgresql://user:password@localhost:5432/dbname

# Authentication
AUTH_SECRET=generate-random-secret-here
JWT_EXPIRY=7d

# External Services
STRIPE_PUBLIC_KEY=pk_test_xxxxx
STRIPE_SECRET_KEY=sk_test_xxxxx

# Feature Flags
ENABLE_BETA_FEATURES=false
```

### Secret Management

**Never commit secrets:**
- Add `.env.local` to `.gitignore`
- Use environment variables for secrets
- Use secret management tools (AWS Secrets Manager, Vault)

**Loading secrets:**
```javascript
// config/secrets.js
export const secrets = {
  database: {
    url: process.env.DATABASE_URL,
    password: process.env.DB_PASSWORD,
  },
  api: {
    key: process.env.API_KEY,
    secret: process.env.API_SECRET,
  },
};

// Validate required secrets on startup
const required = ['DATABASE_URL', 'API_KEY'];
for (const key of required) {
  if (!process.env[key]) {
    throw new Error(`Missing required environment variable: ${key}`);
  }
}
```

## Deployment Process

### Pre-Deployment Checklist

```markdown
- [ ] All tests passing (unit, integration, E2E)
- [ ] Code reviewed and approved
- [ ] Documentation updated
- [ ] Environment variables configured
- [ ] Database migrations ready (if applicable)
- [ ] Rollback plan prepared
- [ ] Monitoring and alerts configured
- [ ] Stakeholders notified
```

### Deployment Steps

**Standard deployment workflow:**

```bash
# 1. Ensure clean working directory
git status

# 2. Pull latest changes
git checkout main
git pull origin main

# 3. Run tests
npm test
npm run test:e2e

# 4. Build production bundle
npm run build

# 5. Run database migrations (if applicable)
npm run migrate:up

# 6. Deploy to staging first
npm run deploy:staging

# 7. Smoke test staging
npm run test:smoke:staging

# 8. Deploy to production
npm run deploy:production

# 9. Smoke test production
npm run test:smoke:production

# 10. Monitor for errors
# Check logs, error tracking, metrics
```

### Deployment Best Practices

1. **Blue-Green Deployment** - Deploy to new environment, switch traffic
2. **Canary Deployment** - Gradually roll out to subset of users
3. **Feature Flags** - Deploy code but control feature availability
4. **Database Migrations** - Always backward compatible
5. **Monitoring** - Watch metrics during and after deployment
6. **Rollback Plan** - Have quick rollback mechanism ready

### Rollback Procedure

**If deployment fails:**

```bash
# Quick rollback
git revert HEAD
git push origin main
npm run deploy:production

# Or use deployment platform rollback
# Vercel: vercel rollback
# Heroku: heroku rollback
# AWS: redeploy previous version
```

**Database rollback:**
```bash
# Run down migration
npm run migrate:down

# Or restore from backup
npm run db:restore -- --backup=pre-deploy-backup
```

---

**See Also:**
- [Code Style Guide](code-style.md)
- [Testing Practices](testing-practices.md)
- [Architecture Patterns](architecture-patterns.md)
