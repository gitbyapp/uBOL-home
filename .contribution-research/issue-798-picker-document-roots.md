# Upstream #798: Inspect document roots with the picker

Upstream: https://github.com/uBlockOrigin/uBOL-home/issues/798

## Source findings (2026-11-16)

- `gorhill/uBlock/platform/mv3/extension/js/scripting/picker.js` walks ancestors through `elem.parentElement`, so the raw candidate list includes the `body` and `html` elements.
- Automatic slider candidates use `Math.max(listParts.length-2, 0)` and intentionally omit the two top-level ancestors.
- The full picker UI exposes ancestor selection separately, which merits testing before adding more automatic candidates.

## Proposed safe scope

1. Preserve the default behavior and offer document-root exploration only as a deliberate opt-in in fully expanded view.
2. Distinguish inspection of the root element's attributes from creating dangerous CSS hide rules that could blank the page.
3. Verify touch/mobile access and the reported scroll-lock workflow.
4. Test with simple pages plus Reddit-style page-wide overflow, pointer-events and class toggles.

Status: design investigation only. No runtime validation or complete code fix has been performed.
