# uBOL upstream #786 — Imported generic cosmetic filters

Tracking issue: https://github.com/uBlockOrigin/uBOL-home/issues/786

Status: **research / unimplemented**, as of 2026-11-16. Do not describe this document as a working feature or PR-ready fix. Raymond Hill explicitly plans incremental support but has given no ETA.

## Root cause in current source

- `platform/mv3/extension/js/offscreen/compile-filters.js`: `compileFilters()` skips cosmetic rules where `parser.hasOptions()` is false. This includes generic `##.tjtagmanager[data-ad_id]`.
- The intermediate compiled object stores `specificCosmeticDetails` and `scriptletDetails` but does not retain generic selectors.
- `toMv3Data()` compiles specific cosmetic and scriptlet code but provides no imported-generic output.
- `mergeCompiledData()` merges DNR/specific/scriptlet entries from different imported lists. Any new generic data must be included there and maintain deduplication and exception semantics.
- `platform/mv3/extension/js/compiled-filters.js`: `prepareUserScripts()` registers imported scripts using filtering-mode exclusions for `none` and `basic`. **Generic cosmetic filtering should be limited to Complete mode**, not treated as ordinary specific imported filters that work in Optimal mode.
- The stock generic engine (`scripting-manager.js`, `scripting/css-generic.js`) uses specialized hashed selector maps and bounded DOM surveys. Naively injecting all imported selectors at document start on every site would risk CPU, memory, script-size, and compatibility regressions.

## Suggested minimal upstream design discussion

1. Decide which plain CSS generic syntax and generic exceptions are in scope for a first iteration. Do not let untrusted imported lists inject arbitrary procedural JS.
2. Define a compact compiled representation with per-list ownership, stable deduplication, and explicit exception handling; cache it alongside specific cosmetic data.
3. Reuse or extend the **bounded generic matching engine**, instead of generating an unrestricted global CSS stylesheet.
4. Apply selectors only to the appropriate browsing contexts at **Complete** filtering level, respecting site allow-lists, browser permissions, all frames, and list enable/disable.
5. Ensure recompile/unregister when an imported list updates, is removed, or is disabled; preserve correct behavior across restarts and permission changes.
6. Set measurable performance and memory budgets before deciding output-size limits; test large lists as well as one-rule examples.
7. Add a dedicated source-level parser/compile/inject test suite before requesting a maintainer merge.

## Acceptance tests needed

| Scenario | Expected |
| --- | --- |
| Import list with `##.tjtagmanager[data-ad_id]` | Matching ad container hidden in Complete mode |
| Same page in Basic and Optimal | Imported generic filter not applied |
| Domain-specific `example.org##.ad` | Existing specific filtering unchanged |
| Exception `#@#` or domain exclusion | Correctly takes precedence as defined |
| Two lists with overlapping selectors | Deduplicate without dropping a surviving rule |
| Disable, update, remove imported list | All corresponding generic effects added/removed appropriately |
| Hot changes to browser userScripts permissions | Reconciliation and registration reliable |
| Large real-world external list | Bounded page CPU, memory, and output script sizes |
| Browser matrix | Chromium/Edge, Firefox, Safari behavior verified |

## Not an implemented fix

No generic-filter implementation, new compiler, browser integration test, performance measurement, or production-ready patch has been completed. Do **not** merge a shortcut that treats all generic selectors as wildcard-specific filters without maintainer review: the performance and filtering-mode semantics differ materially.
