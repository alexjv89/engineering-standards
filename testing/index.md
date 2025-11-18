# Testing Practices

Atomic notes on testing patterns organized by testing layer: Unit, Component, and E2E.

## Testing Strategy Overview

**Three Testing Layers:**
1. **Unit/Integration Tests (Jest)** - Business logic, utilities, parsers, jobs
2. **Component Tests (Storybook)** - UI components with user journey pattern
3. **End-to-End Tests (Playwright)** - Full user flows in real browsers

## Core Patterns (All Layers)

- [Test Structure: AAA Pattern](./aaa-pattern.md) - Arrange-Act-Assert structure
- [Test Naming](./test-naming.md) - Descriptive test names
- [Test Independence](./test-independence.md) - Independent, isolated tests
- [Setup and Teardown](./setup-teardown.md) - beforeEach, afterEach, jest.clearAllMocks()

## Coverage & Strategy

- [Coverage Requirements](./coverage-requirements.md) - 80% minimum, 100% for critical
- [Unit vs Integration Tests](./unit-vs-integration.md) - When to use each type

## Unit Testing with Jest

### Mocking Patterns
- [Jest Mocking Patterns](./jest-mocking-patterns.md) - Module vs function mocking
- [Mock External Dependencies](./mock-external-dependencies.md) - What to mock (DB, APIs, Next.js)
- [Semantic Mocking](./semantic-mocking.md) - Implementation-based mocking (by URL, not order)
- [Mock Cleanup](./mock-cleanup.md) - jest.clearAllMocks() in beforeEach

### Test Organization
- [Test Utilities](./test-utilities.md) - Reusable validators and helpers
- [Parametric Testing](./parametric-testing.md) - test.each() for multiple cases
- [Fixture Loading](./fixture-loading.md) - beforeAll pattern for expensive fixtures
- [Test Data Factories](./test-data-factories.md) - Creating consistent test data
- [Async Testing](./async-testing.md) - Testing async code with async/await

## Component Testing with Storybook

- [Testing React Components](./testing-react-components.md) - Storybook overview
- [Storybook User Journey Pattern](./storybook-user-journey-pattern.md) - Stories as user scenarios
- [Storybook Interactions](./storybook-interactions.md) - Play functions and assertions
- [Storybook Mock Data](./storybook-mock-data.md) - Centralized mocks and Proxy pattern

## End-to-End Testing with Playwright

- [Playwright E2E Structure](./playwright-e2e-structure.md) - Project setup and configuration
- [Playwright Auth Pattern](./playwright-auth-pattern.md) - Reusable authentication
- [Playwright Helpers](./playwright-helpers.md) - Reusable test functions

## Quick Reference

### When to Use Which Test Type

**Jest Unit Tests** - Use for:
- Utility functions
- Data parsers
- API integrations
- Business logic
- Database operations

**Storybook Component Tests** - Use for:
- UI components
- User interactions
- Visual states
- Component isolation

**Playwright E2E Tests** - Use for:
- Critical user flows
- Full-stack integration
- Authentication flows
- Real browser testing

## Testing Philosophy

1. **Test user behavior, not implementation** - Focus on what users see/do
2. **Isolate units completely** - Mock all external dependencies
3. **Test both success and failure paths** - Always test error scenarios
4. **Use semantic mocking** - Mock by meaning (URL), not order
5. **Reset mocks between tests** - jest.clearAllMocks() in beforeEach
6. **Load fixtures once** - Use beforeAll for expensive operations
7. **Create reusable utilities** - DRY principle for test helpers
8. **Stories are user journeys** - Name stories after real scenarios
