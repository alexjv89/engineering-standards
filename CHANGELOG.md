# Changelog

All notable changes to the engineering standards will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).

## [Unreleased]

### Added

### Changed

### Fixed

### Removed

## [1.1.0] - 2025-11-18

### Added
- **Testing Documentation Expansion (12 new notes)**:
  - `testing/` - Expanded from 10 to 22 notes with comprehensive three-layer testing strategy
  - **Jest Unit Testing (6 new notes)**:
    - `jest-mocking-patterns.md` - Module vs function mocking patterns
    - `semantic-mocking.md` - Implementation-based mocking (mock by URL, not call order)
    - `mock-cleanup.md` - jest.clearAllMocks() pattern for test independence
    - `test-utilities.md` - Reusable validators and helpers (validateTransaction, validateMetadata)
    - `parametric-testing.md` - test.each() pattern for multiple test cases
    - `fixture-loading.md` - beforeAll pattern for expensive fixture loading
  - **Storybook Component Testing (3 new notes)**:
    - `storybook-user-journey-pattern.md` - Stories represent real user scenarios
    - `storybook-interactions.md` - Play functions with automated interactions and assertions
    - `storybook-mock-data.md` - Centralized mock data and Proxy-based action mocking
  - **Playwright E2E Testing (3 new notes)**:
    - `playwright-e2e-structure.md` - E2E test organization and configuration
    - `playwright-auth-pattern.md` - Reusable authentication setup pattern
    - `playwright-helpers.md` - Feature-based helper functions for test reusability

### Changed
- **Testing Strategy Reorganization**:
  - `testing/index.md` - Reorganized into three distinct testing layers (Jest/Storybook/Playwright)
  - `testing-react-components.md` - Updated to reflect actual practice: Storybook for components (not React Testing Library)
  - `mock-external-dependencies.md` - Added Next.js (redirect, notFound, cookies) and Sequelize DB mocking patterns
  - `setup-teardown.md` - Added jest.clearAllMocks() pattern used in actual projects
- **Testing Philosophy Documentation**:
  - Documents actual testing patterns from elevate-ai and txn.fobrix.com projects
  - Three-layer testing strategy: Unit (Jest), Component (Storybook), E2E (Playwright)
  - Semantic mocking over order-dependent mocks
  - User journey pattern for Storybook stories
  - Reusable test utilities and fixture loading patterns

## [1.0.0] - 2025-11-18

### Added
- **Database Patterns (11 new notes)**:
  - `architecture/database/` - Complete Sequelize ORM pattern guide
  - `sequelize-initialization.md` - Singleton instance pattern with environment config
  - `model-definition-pattern.md` - Factory function pattern for models
  - `database-initialization.md` - Model registry and database setup
  - `model-file-naming.md` - PascalCase.model.js convention
  - `table-naming.md` - Lowercase plural table naming
  - `column-naming.md` - snake_case column naming with timestamp exceptions
  - `primary-key-strategies.md` - STRING(12), UUID, or auto-increment patterns
  - `timestamp-columns.md` - Manual created_at/updated_at with timestamps:false
  - `jsonb-columns.md` - JSONB usage with comprehensive JSDoc schemas
  - `foreign-key-pattern.md` - Manual FK definitions without associations
  - `custom-model-methods.md` - Adding static methods to models
  - Architecture note count: 9 → 20 notes
  - Total note count: 36 → 47 notes
- **JavaScript-First Philosophy**:
  - `principles/javascript-with-jsdoc.md` - New principle documenting JavaScript with JSDoc approach
  - `naming/jsdoc-types.md` - JSDoc type patterns and documentation guide
  - Small team philosophy: minimize complexity, fewer build steps, simpler tooling
- **Functional Programming Philosophy**:
  - `principles/functional-programming.md` - Comprehensive guide on functional vs OOP approach
  - `architecture/module-organization.md` - Organizing code in functional modules
  - Focus on pure functions, immutability, composition over classes
  - Classes only for React Error Boundaries and very specific objects (Date, etc.)
- **Atomic Knowledge Base Structure**:
  - `principles/atomic-knowledge-base.md` - Core principles documentation
  - 11 naming convention notes in `naming/`
  - 9 testing practice notes in `testing/`
  - 9 architecture pattern notes in `architecture/`
  - 7 git workflow notes in `git-workflow/`
  - Index files for each topic directory
- **Improved Discovery**:
  - Topic-based directories with clear organization
  - Cross-referenced notes with "Related Notes" sections
  - Searchable by topic, keyword, or file pattern
- **LLM Optimization**:
  - Updated CLAUDE.md with atomic knowledge base usage patterns
  - Updated README.md with quick start guides for humans and LLMs
  - Each note self-contained and independently useful

### Changed
- Variable naming convention documented as snake_case
  - `naming/variables-snake-case.md` - Comprehensive variable naming guide
  - **Exception**: React hook returns use camelCase (e.g., `const [isEditing, setIsEditing] = useState()`)
  - **Exception**: All variables returned from React hooks use camelCase (e.g., `const { data, error, isLoading } = useQuery()`)
  - Regular variables (derived values, event handler variables, service layer) use snake_case
  - Functions remain camelCase, components remain PascalCase
  - React props remain camelCase (React convention)
  - Constants remain SCREAMING_SNAKE_CASE
  - Files exporting modules: camelCase.js
  - Files exporting components: PascalCase.jsx
- Restructured repository as atomic knowledge base
  - Replaced long-form standards documents with 47 focused atomic notes
  - Organized into topic directories: naming/, testing/, architecture/, git-workflow/, principles/
  - Each note addresses single concept (20-50 lines typical)
  - Added index.md files for topic navigation
  - Optimized for LLM consumption and cost efficiency
- Refactored to Functional Programming patterns:
  - Updated `architecture/service-layer-pattern.md` - Now uses exported functions instead of objects
  - Updated `naming/jsdoc-types.md` - Replaced class examples with module patterns
  - Updated `architecture/error-boundaries.md` - Noted as exception requiring classes
  - Updated `architecture/arrow-vs-declaration.md` - Removed class example, added functional guidance

### Removed
- **TypeScript References**: Removed all TypeScript-specific content
  - Deleted `naming/types-pascalcase.md` (TypeScript-specific)
  - Removed `.tsx` and `.ts` file extension references
  - Updated all examples to use plain JavaScript
  - Refocused `enums-pattern.md` on JavaScript objects instead of TypeScript enums
- **Comprehensive Standards Documents**:
  - `standards/code-style.md` (replaced by 11 atomic notes in naming/)
  - `standards/testing-practices.md` (replaced by 9 atomic notes in testing/)
  - `standards/architecture-patterns.md` (replaced by 9 atomic notes in architecture/)
  - `standards/development-workflows.md` (replaced by 7 atomic notes in git-workflow/)
  - `standards/` directory itself
