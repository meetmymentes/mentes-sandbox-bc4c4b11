# Plan: `farewell(name)` helper

## Problem

The project has no standard way to produce a goodbye message for a user.
Callers that need one would each build the string themselves, so the wording,
punctuation and handling of odd input (an empty or whitespace-only name) can
drift between call sites. We want one helper, `farewell(name)`, that returns
`Goodbye, <name>!` (for example, `farewell("Ada")` → `"Goodbye, Ada!"`) and
behaves predictably for edge-case input.

## Candidate solutions

### Option A: minimal string formatter

`farewell(name)` interpolates `name` into a fixed template and returns
`Goodbye, <name>!`, with no validation. An empty string yields `Goodbye, !`.

Pros: smallest possible change, trivially testable, no policy decisions baked in.
Cons: callers must validate input themselves, and bad input produces an
odd-looking message rather than a clear failure.

### Option B: validating formatter

`farewell(name)` trims `name`, and if the result is empty it raises a clear
error (for example, `ValueError("name must be non-empty")`). Otherwise it
returns `Goodbye, <trimmed name>!`.

Pros: one place enforces a sensible contract, so malformed messages never
reach users. The empty-name behaviour is explicit and tested.
Cons: slightly more code and tests, and callers passing possibly-empty names
must handle the error.

**Recommendation:** Option B, because the point of a shared helper is
consistent output, and that includes the edge cases.

## Plan

- **Add the helper:** create a `farewell(name)` function that returns the string `Goodbye, <name>!` (for example, `farewell("Ada")` → `"Goodbye, Ada!"`).
- **Test it:** add a unit test for a typical name, and decide what should happen with an empty string.
- **Document it:** add a short usage example for `farewell` to the README.
