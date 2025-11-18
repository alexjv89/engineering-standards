# Architecture Patterns

Atomic notes on component design, state management, API integration, database patterns, and architectural patterns.

## Database Patterns
- [Database Patterns Overview](./database/index.md) - Complete guide to Sequelize patterns
- [Sequelize Initialization](./database/sequelize-initialization.md) - Singleton instance pattern
- [Model Definition Pattern](./database/model-definition-pattern.md) - Factory function pattern
- [Database Initialization](./database/database-initialization.md) - Model registry setup
- [Model File Naming](./database/model-file-naming.md) - PascalCase.model.js convention
- [Table Naming](./database/table-naming.md) - Lowercase plural with underscores
- [Column Naming](./database/column-naming.md) - snake_case standard
- [Primary Key Strategies](./database/primary-key-strategies.md) - STRING(12), UUID, or auto-increment
- [Timestamp Columns](./database/timestamp-columns.md) - created_at/updated_at patterns
- [JSONB Columns](./database/jsonb-columns.md) - Flexible data with JSDoc
- [Foreign Key Pattern](./database/foreign-key-pattern.md) - Manual FK definitions
- [Custom Model Methods](./database/custom-model-methods.md) - Adding static methods

## Module Organization
- [Module Organization Overview](./modules/module-organization.md) - Overview of functional module patterns
- [Module Exports Pattern](./modules/module-exports-pattern.md) - Named exports and module structure
- [Module Shared State](./modules/module-shared-state.md) - Closure pattern for shared configuration
- [Utility Modules](./modules/utility-modules.md) - Organizing utility functions
- [Service Layer Pattern](./modules/service-layer-pattern.md) - Centralizing API calls in modules

## Component Patterns
- [Container/Presentational Pattern](./components/container-presentational-pattern.md) - Separating logic from UI
- [Component Composition](./components/component-composition.md) - Building complex UIs from simple components
- [Custom Hooks Pattern](./components/custom-hooks.md) - Extracting reusable stateful logic

## State Management
- [State Management: Local vs Global](./state/state-management-local-vs-global.md) - Choosing state level
- [Context API Pattern](./state/context-api-pattern.md) - Global state with React Context
- [State Colocation](./state/state-colocation.md) - Keeping state close to usage

## Functional Programming Patterns
- [Pure Functions](./functional/pure-functions.md) - Functions with no side effects
- [Function Composition](./functional/function-composition.md) - Building complexity from simple functions
- [Immutability Patterns](./functional/immutability-patterns.md) - Avoiding data mutations
- [Array Functional Methods](./functional/array-methods-functional.md) - map, filter, reduce patterns

## Error Handling & Other Patterns
- [Error Boundaries](./error-boundaries.md) - Catching React errors (class component exception)
- [Arrow Functions vs Declarations](./arrow-vs-declaration.md) - When to use each
