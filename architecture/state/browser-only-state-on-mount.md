# Reading Browser-Only State on Mount Without Hydration Mismatches

Values that only exist in the browser (`localStorage`, `matchMedia`, `navigator.userAgent`) can't be read during SSR. The two naive fixes both have real failure modes — use `useSyncExternalStore` instead.

## The Trap

**Lazy `useState` initializer with an SSR guard** silences the `react-hooks/set-state-in-effect` lint rule (bundled with `eslint-config-next` 16 / `eslint-plugin-react-hooks` 7+) but is wrong: a `"use client"` component still renders once on the server (returning the SSR-safe default) and once again during client hydration — where the guard is now false and the *real* browser value comes back immediately. Server said "hidden", client hydration wants "visible": a genuine hydration mismatch, not just a lint nag.

```javascript
// Looks clean, passes lint, mismatches on hydration:
const [state] = useState(() =>
  typeof window === "undefined" ? DEFAULT : computeFromBrowser(),
);
```

**Calling `setState` synchronously in a `useEffect` on mount** avoids the mismatch (server and first client render both show the default; the effect corrects it after hydration completes) but is exactly what `react-hooks/set-state-in-effect` flags as a cascading-render anti-pattern.

## The Fix: `useSyncExternalStore`

```javascript
function subscribe() {
  return () => {}; // no-op if nothing external changes this while mounted
}
function getSnapshot() {
  return computeFromBrowser(); // real value, client-only
}
function getServerSnapshot() {
  return DEFAULT; // matches what the server rendered
}

const state = useSyncExternalStore(subscribe, getSnapshot, getServerSnapshot);
```

React renders `getServerSnapshot()` for both the server pass and the first client (hydration) pass — no mismatch — then re-checks `getSnapshot()` immediately after mount and re-renders with the real value. No manual `setState` call, so the lint rule doesn't fire either.

Give `subscribe` a real (non-no-op) implementation for values that can change while mounted (e.g. a `beforeinstallprompt` event firing later) — that's a legitimate subscription and isn't what the lint rule targets.

## Related Notes
- [PWA Install Prompt UX](/architecture/pwa/install-prompt-ux.md)
- [suppressHydrationWarning for Dates](/architecture/suppress-hydration-warning-dates.md)
- [Custom Hooks Pattern](/architecture/components/custom-hooks.md)
