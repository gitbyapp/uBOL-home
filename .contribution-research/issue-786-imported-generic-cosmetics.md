# Upstream #786: Generic cosmetic filters in imported lists

Upstream: https://github.com/uBlockOrigin/uBOL-home/issues/786

## Source findings (2026-11-16)

In `gorhill/uBlock/platform/mv3/extension/js/offscreen/compile-filters.js`:

- `compileFilters()` skips cosmetic rules for which `parser.hasOptions()` is false.
- The result object tracks `specificCosmeticDetails` and `scriptletDetails` but no imported-generic cosmetic payload.
- `toMv3Data()` has a code-emission path for specific cosmetic filters but none for generic imported ones.

This agrees with Raymond Hill's clarification on the upstream issue that support has not yet been implemented.

## Scope for a safe implementation

1. Agree with the maintainer on generic selector representation and efficient injection strategy before coding.
2. Define rule/exception handling, multiple-list deduplication and supported syntax.
3. Add lifecycle hooks for enabling, disabling, updating and removing imported lists.
4. Validate performance overhead and site filtering modes across browsers.
5. Include regression cases for `##.tjtagmanager[data-ad_id]`, hostname-specific rules, exceptions and large imported lists.

Status: architecture investigation only. No generic-filter support code has been implemented or tested.
