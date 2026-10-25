# Surface-Relative Colour

Design distances from the surface, not colours. Every colour in a component has a job, and that job is a distance from the surface it sits on. A dark version keeps the same distances, in the opposite direction.

## The Problem

Contrast depends on the background, but a hex value is fixed. `#eee` was chosen because it sits a small, quiet step away from white. Put the same `#eee` on a near-black card and it is now the brightest thing there. Nothing in the code changed, but its job flipped.

When a light component is reused on a dark surface with its colours unchanged, every distance inverts:

- **Zero becomes maximum.** Padding or gaps drawn in `#fff` to look like the card become a white lattice.
- **Quiet becomes loud.** Gridlines, empty cells and tracks become the highest-contrast marks on the card.
- **Ramps run backwards.** A light→dark ramp where "more" is darkest puts "more" closest to a dark surface, so the busiest values look the quietest.

Inverting the colours fails as well: hues and saturation shift, and brand and data colours stop being recognisable. The aim isn't the same colours or opposite colours. It's the same distances.

## The Rule

Give each colour a **role**, then pick a value per mode that puts it at that role's distance from the surface.

| Role | Distance from the surface | Examples |
|---|---|---|
| Surface | None: it *is* the surface | gaps and padding drawn as fill, cell borders that separate, knock-outs |
| Quiet structure | Small, but visible | gridlines, empty cells, progress tracks, zebra rows |
| Secondary | Medium | axis lines, muted labels, inactive icons |
| Data / content | Large | bars, filled cells, lines, body text |
| Ordered ramp | Grows with the value | heatmap levels: "more" is farther from the surface in **both** modes |

Then:

1. **The surface role uses the surface token.** `--card` or `--background`, never `#fff` or `white`.
2. **Use tokens wherever CSS reaches** (`bg-card`, `border-border`, `bg-muted`, `text-muted-foreground`). A token is a role with a value per mode already chosen.
3. **Where CSS doesn't reach** (canvas charts such as ECharts, colours set in JS, inline SVG fills), keep one `{ light, dark }` pair per role in a single module and pick by `resolvedTheme` from `next-themes`. Re-render on theme change.
4. **Ramps reverse their lightness order in dark mode**, so the strongest value is always farthest from the surface. The hue stays the same.
5. **Set the quiet roles deliberately in light mode too.** A distance nobody chose is usually too small to see: a `#eee` gridline on white is 1.16:1.
6. **Tune by eye from screenshots.** Start from a matching contrast ratio, then adjust. WCAG ratios aren't perceptually even on dark backgrounds, so the same ratio can look weaker or stronger there.

## Anti-Pattern

The txn dashboard charts (2026-10-25) were designed on white with hex literals. Contrast against the card:

| Element | Colour | On white | On dark card `#18181b` |
|---|---|---|---|
| Heatmap cell gap | `#fff` | 1.00 | 17.72 |
| Heatmap empty cell | `#ebedf0` | 1.17 | 15.11 |
| Heatmap "least" / "most" | `#9be9a8` / `#216e39` | 1.44 / 6.26 | 12.33 / 2.83 |
| Bar chart gridline | `#eee` | 1.16 | 15.27 |

In dark mode the gaps became a white grid, the gridlines were louder than the bars, and the heatmap read backwards. In light mode the gridlines were nearly invisible, because nobody had chosen how far they should sit from the card.

## Correct Approach

Name the roles and give each one a light and dark value at the same distance:

```js
// src/components/Charts/chartColors.js — one pair per role
export const CHART_COLORS = {
  light: { surface: '#ffffff', grid: '#e4e4e7', empty: '#ebedf0', ramp: ['#9be9a8', '#40c463', '#30a14e', '#216e39'] },
  dark:  { surface: '#18181b', grid: '#2e2e33', empty: '#27272a', ramp: ['#0e4429', '#006d32', '#26a641', '#39d353'] },
};
```

The gap is the card in both modes. The empty cell and gridline sit a small step off the card in both modes. The dark ramp goes from dim to bright, so "more" is still the strongest mark.

## Check

- [ ] Every colour in the change has a named role (surface, quiet, secondary, data, ramp)
- [ ] No raw `#fff` / `white` / `#eee`-style literal sits on a themed surface. Surface-role pixels use the surface token
- [ ] Screenshots exist in **both** modes and were read back ([Visual Verification](/ui/visual-verification.md))
- [ ] In both modes, surface-role pixels can't be told apart from the card
- [ ] In both modes, nothing quiet is louder than the data
- [ ] Ramps read less→more in both modes (legend included)
- [ ] Quiet structure is visible in light mode, not just present

## Related Notes

- [Visual Verification](/ui/visual-verification.md) — screenshot both modes, not only the default
- [Change Scope](/ui/change-scope.md)
- lib-ui `docs/architecture/theming.md` — the tokens and how the theme is applied
- lib-ui `docs/wip/dark-mode.md` — Phase 7, the repo-wide hardcoded-colour migration this note guides
