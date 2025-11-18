# JSONB Columns

Use JSONB columns for flexible data structures and document comprehensive JSDoc schemas for complex data.

## Basic Pattern

```javascript
sequelize.define('Orgs', {
  details: {
    type: DataTypes.JSONB,
    allowNull: true,
    defaultValue: {},
  },
  settings: {
    type: DataTypes.JSONB,
    allowNull: true,
    defaultValue: {},
  },
  feature_flags: {
    type: DataTypes.JSONB,
    allowNull: true,
    defaultValue: {
      'show_onboarding': true
    },
  },
});
```

## When to Use JSONB

- **Flexible schemas**: Data structure may evolve over time
- **Settings/config**: User preferences, feature flags
- **Metadata**: Additional contextual information
- **Multi-format data**: Different parsing formats (PDFs, CSVs)
- **Avoid over-normalization**: Prevent excessive table joins

## JSDoc Documentation for Complex JSONB

For complex JSONB structures, add comprehensive JSDoc comments:

```javascript
/**
 * Validated parsed data with strict Zod schema
 *
 * Populated by: detectParser job (step: 'extract_data')
 * Validated by: Zod schemas in src/utils/validation/parser-schemas.js
 *
 * @property {Array<Object>} transactions - Parsed transaction array
 *   - date: string - Transaction date in YYYY-MM-DD format
 *   - particulars: string - Transaction description
 *   - inflow: number - Credit amount in paise (integer)
 *   - outflow: number - Debit amount in paise (integer)
 *   - balance: number - Account balance in paise (integer)
 *
 * @property {Object} metadata - Statement metadata
 *   - acc_no: string - Account number (required)
 *   - currency: string - Currency code (e.g., "INR", "USD")
 *
 * @example
 * {
 *   transactions: [{
 *     date: "2025-01-01",
 *     particulars: "ATM Withdrawal",
 *     inflow: 0,
 *     outflow: 100000,
 *     balance: 500000
 *   }],
 *   metadata: {
 *     acc_no: "1234567890",
 *     currency: "INR"
 *   }
 * }
 */
parsed_data: {
  type: DataTypes.JSONB,
  allowNull: true,
},
```

## JSDoc Best Practices

- **Document structure**: Explain the shape of the JSON object
- **Property types**: Specify data types for each property
- **Nested structures**: Document nested objects and arrays
- **Provide examples**: Include realistic example values
- **External validation**: Reference Zod schemas or validation files
- **Populate/update info**: Note which processes modify this field
- **Deprecation notices**: Mark deprecated fields with `@deprecated`

## Default Values

```javascript
// Empty object
defaultValue: {}

// Preset values
defaultValue: {
  'show_onboarding': true
}

// Empty array
defaultValue: []
```

## Related Notes
- [Column Naming](./column-naming.md) - JSONB column naming (snake_case)
