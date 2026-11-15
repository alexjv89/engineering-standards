# Review Business Logic Pollution

You are tasked with reviewing all markdown files in this engineering standards knowledge base to identify domain-specific business logic pollution.

## What to Flag

### ❌ Domain-Specific Business Logic (Flag These)
Flag any references to:
- **Domain terminology**: Manufacturing, plants, devices, shifts, OEE, production, alerts, equipment
- **Company/product names**: Vimana, specific product names
- **Domain entities**: Plant IDs, device IDs, shift schedules, production metrics
- **Domain workflows**: Shop floor operations, maintenance schedules, quality metrics
- **Domain-specific context**: Timezone for plants, device states, alarm conditions

### ✅ Implementation Logic (Keep These)
DO NOT flag:
- **Analytics/tracking tools**: Umami, Sentry, EventLog, analytics patterns
- **Infrastructure**: Database schemas, API patterns, message queues
- **Frameworks/libraries**: Next.js, React, Sequelize, Zod, AI SDK
- **Instrumentation**: Logging patterns, error tracking, metrics collection
- **Generic entities**: Users, contexts, resources, capabilities
- **Generic patterns**: Authentication, authorization, caching, optimization

## Step 1: Inventory Files

Find all markdown files in topic directories:
- `naming/*.md`
- `testing/*.md`
- `architecture/*.md` (including subdirectories)
- `git-workflow/*.md`
- `principles/*.md`

**Exclude** from review:
- Root-level files: `README.md`, `CLAUDE.md`, `CHANGELOG.md`, `AI-IMPLEMENTATION-RECOMMENDATIONS.md`
- Template files in `templates/`
- Example files in `examples/`

## Step 2: Review Each File

For each file, scan for:

### Domain Terminology
- Manufacturing terms: plant, device, shift, OEE, availability, performance, quality, production, downtime, alerts, equipment, machine, sensor
- Vimana-specific: vimanaSVC, plant_id, device_id, tenant_id
- Industry-specific metrics or KPIs

### Domain Context Examples
- Code examples with domain-specific variable names
- System prompts with domain instructions
- Event tracking with domain-specific properties
- Database schemas with domain tables

### Pattern
For each domain reference found, note:
- File path
- Line number (approximate)
- What was found
- Severity: HIGH (pervasive domain logic), MEDIUM (domain examples), LOW (minor domain terminology)

## Step 3: Generate Report

### Summary
- Total files reviewed: X
- Files with domain pollution: X
- High severity: X
- Medium severity: X
- Low severity: X

### Detailed Findings

For each file with issues:

**File**: `path/to/file.md`
**Severity**: HIGH/MEDIUM/LOW
**Issues**:
- Line ~X: Found manufacturing terminology: "plant", "device", "OEE"
- Line ~Y: Domain-specific example showing plant/device context
- Line ~Z: Event tracking with plant_id, device_id

**Recommendation**:
- Replace with generic equivalents (e.g., plant → context, device → resource)
- Use generic examples instead of domain-specific ones
- Keep implementation patterns but remove domain context

---

## Step 4: Prioritize Fixes

### High Priority (Pervasive Domain Logic)
Files where domain logic is core to the examples and explanations.

### Medium Priority (Domain Examples)
Files with generic patterns but domain-specific code examples.

### Low Priority (Minor Terminology)
Files with occasional domain terms that could be genericized.

## Step 5: Present Findings

Show me the complete report with all findings organized by severity.

For each HIGH severity file, suggest specific changes:
- What domain logic to remove
- What generic replacement to use
- Whether to keep the file at all

**Important Notes**:
- Umami, EventLog, Sentry, database schemas = OK (implementation logic)
- Plant, device, shift, OEE, manufacturing = NOT OK (domain logic)
- Generic user_id, context_id, resource_id = OK
- Specific plant_id, device_id, tenant_id = NOT OK (unless explaining a generic pattern)
- Tool/instrumentation specifics = OK
- Domain workflow specifics = NOT OK
