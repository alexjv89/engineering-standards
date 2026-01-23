# API Versioning

Include version in URL path for API stability.

## Pattern

```
/api/v1/resources
/api/v2/resources
```

## Why URL Versioning?

- **Explicit**: Version visible in every request
- **Simple**: No header parsing needed
- **Cacheable**: Different URLs = different cache entries
- **Debuggable**: Easy to see which version in logs

## Version Increment Rules

| Change Type | Action |
|-------------|--------|
| New optional field | No version bump |
| New endpoint | No version bump |
| Remove field | New version |
| Rename field | New version |
| Change field type | New version |
| Change behavior | New version |

## Directory Structure

```
src/app/api/
├── v1/
│   ├── transactions/
│   │   ├── route.js
│   │   └── [id]/route.js
│   └── accounts/
│       ├── route.js
│       └── [id]/route.js
└── v2/
    └── transactions/
        ├── route.js
        └── [id]/route.js
```

## Deprecation

When deprecating a version:
1. Document deprecation date
2. Return `Deprecation` header
3. Maintain for reasonable period (6+ months)
4. Remove only after migration complete

## Related Notes

- [REST URL Structure](/architecture/api/rest-url-structure.md)
- [Next.js Route Organization](/architecture/api/nextjs-route-organization.md)
