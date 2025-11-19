# Storybook: User Type Mocking

Mock different user types using localStorage decorators.

## localStorage Pattern

Use decorators to set user type before story renders:

```javascript
// Staff user stories
export default {
  title: 'Component/StaffUser',
  decorators: [
    (Story) => {
      localStorage.setItem('developer-mode', 'true');
      return <Story />;
    },
  ],
};

// App user stories
export default {
  title: 'Component/AppUser',
  decorators: [
    (Story) => {
      localStorage.removeItem('developer-mode');
      return <Story />;
    },
  ],
};
```

## Why Use Decorators

Decorators run before each story, ensuring correct user type:

```javascript
export default {
  title: 'Dashboard/AdminView',
  decorators: [
    (Story) => {
      // Set admin user
      localStorage.setItem('user-role', 'admin');
      localStorage.setItem('permissions', JSON.stringify(['read', 'write', 'delete']));
      return <Story />;
    },
  ],
};

export const AdminDashboard = {};
export const AdminSettings = {};
// All stories in this file use admin user
```

## Multiple User Types

Organize stories by user type:

```
stories/
  ├── Dashboard.Admin.stories.jsx    # Admin user stories
  ├── Dashboard.User.stories.jsx     # Regular user stories
  └── Dashboard.Guest.stories.jsx    # Guest user stories
```

## Cleanup Pattern

Clear localStorage in decorator to avoid cross-contamination:

```javascript
export default {
  title: 'Component/GuestUser',
  decorators: [
    (Story) => {
      // Clear all user-related localStorage
      localStorage.clear();
      return <Story />;
    },
  ],
};
```

## When to Use

Use user type mocking when:
- Component behavior differs by user role
- UI shows/hides features based on permissions
- Testing role-based access control

## Related Notes
- [Decorator Patterns](/storybook/decorators/patterns.md)
- [Mock Data Organization](/storybook/mocking/data.md)
- [Action Mocking Pattern](/storybook/mocking/actions.md)
