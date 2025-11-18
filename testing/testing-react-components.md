# Testing React Components: Storybook

Component testing is done with Storybook, not React Testing Library.

## Component Testing Strategy

**Unit/Integration Tests (Jest)** - Business logic, utilities, parsers, API functions

**Component Tests (Storybook)** - UI components, interactions, visual states

**E2E Tests (Playwright)** - Full user flows, authentication, navigation

## Storybook Component Testing

Components are tested using Storybook with:
- **User Journey Pattern** - Stories represent real user scenarios
- **Play Functions** - Automated interactions and assertions
- **Visual Testing** - Verify UI in different states

### Quick Example

```javascript
import { userEvent, within, expect } from 'storybook/test';

/**
 * USER JOURNEY STORY: After Successful Upload
 *
 * When: User uploaded a file and processing completed
 * User sees: Complete file details with all metadata
 */
export const AfterSuccessfulUpload = {
  args: {
    file: {
      name: 'statement.pdf',
      status: 'processed',
      parser: 'hdfc_credit__pdf',
    }
  },
  play: async ({ canvasElement }) => {
    const canvas = within(canvasElement);

    // Auto-open dialog
    await userEvent.click(canvas.getByRole('button'));

    // Assertions
    await expect(canvas.getByText('statement.pdf')).toBeVisible();
    await expect(canvas.getByText('hdfc_credit__pdf')).toBeVisible();
  },
}
```

## Why Storybook Instead of React Testing Library?

1. **Visual Documentation** - Stories serve as living component documentation
2. **Isolation** - Test components in isolation from app
3. **Developer Experience** - See component states visually
4. **User Journey Focus** - Test real scenarios, not implementation
5. **Component Catalog** - Automatically generated component library

## See These Notes for Details

- **[Storybook User Journey Pattern](./storybook-user-journey-pattern.md)** - How to structure stories
- **[Storybook Interactions](./storybook-interactions.md)** - Play functions and assertions
- **[Storybook Mock Data](./storybook-mock-data.md)** - Mock data patterns

## Jest is for Business Logic

Use Jest for testing:
- Utility functions
- Data parsers
- API integrations
- Business logic
- Database operations

See Jest-related notes:
- [Jest Mocking Patterns](./jest-mocking-patterns.md)
- [AAA Pattern](./aaa-pattern.md)
- [Mock External Dependencies](./mock-external-dependencies.md)

## Related Notes
- [Storybook User Journey Pattern](./storybook-user-journey-pattern.md)
- [Storybook Interactions](./storybook-interactions.md)
- [Storybook Mock Data](./storybook-mock-data.md)
- [Unit vs Integration Tests](./unit-vs-integration.md)
