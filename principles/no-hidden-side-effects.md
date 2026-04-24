# No Hidden Side Effects

A function, step, or module should do exactly what its name says — nothing more, nothing less. When behavior exceeds what the name advertises, consumers cannot reason about the system without reading the implementation.

## The Principle

Also known as the **Principle of Least Astonishment (POLA)**: anyone reading a function call or step name should be able to predict what it does without looking inside.

Hidden side effects create **semantic dishonesty** — the interface (name) doesn't match the implementation.

## Anti-Pattern

```javascript
// BAD: "fetch_companies" also calculates dates
async function fetchCompanies(task) {
  const companies = await getCompanies();
  const report_date = calculateReportDate();  // hidden side effect
  const day_before = subtractDay(report_date); // hidden side effect

  return { companies, report_date, day_before };
}
```

A reader sees `fetch_companies` and assumes it fetches companies. The date logic is invisible from the call site. Downstream consumers now depend on date outputs from a step that conceptually has nothing to do with dates.

## Correct Approach

```javascript
// GOOD: each function does what its name says
async function fetchCompanies(task) {
  const companies = await getCompanies();
  return { companies };
}

function resolveDates(task) {
  const report_date = calculateReportDate();
  const day_before = subtractDay(report_date);
  return { report_date, day_before };
}
```

## Why It Matters

- **Discoverability** — logic is where you expect it to be
- **Debugging** — when dates are wrong, you look at the date step, not the company step
- **Reusability** — `fetch_companies` can be reused without dragging along date logic
- **Onboarding** — new team members can navigate by name, not by reading every implementation

## Smell Test

If you're returning data that doesn't relate to the function name, the function is doing too much. Ask: "Would someone searching for this logic look in this function?"

## Related Notes

- [Functional Programming](/principles/functional-programming.md)
