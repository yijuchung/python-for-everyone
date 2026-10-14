# Python version notes and proposed course additions

**Last researched: October 14, 2026.** The latest stable release is
**[Python 3.15.0](https://www.python.org/downloads/release/python-3150/)**,
released October 9, 2026, as listed on the [official downloads page](https://www.python.org/downloads/).
This is not a new course minimum or a claim of full 3.15 compatibility.

## Recommended additions from Python 3.15

These are proposals, not topics already integrated into the lessons. Prefer
short, version-labelled “Go Deeper” boxes over replacing foundational examples.
Feature references below point to the official Python release documentation.

| Priority | Feature and source | Where it fits | Suggested lesson or exercise |
| --- | --- | --- | --- |
| High | [UTF-8 mode enabled by default (PEP 686)](https://docs.python.org/3.15/whatsnew/3.15.html#whatsnew315-utf8-default) | Chapters 13 (files) and 26 (production debugging) | Explain the changed default for text I/O, then read a non-ASCII file. Keep explicit `encoding="utf-8"` in portable examples; legacy files may need another encoding. UTF-8 mode can still be disabled. |
| High | [`frozendict` (PEP 814)](https://docs.python.org/3.15/whatsnew/3.15.html#whatsnew315-frozendict) | Chapters 8 (dictionaries) and 25 (memory) | Compare mutable configuration with an immutable mapping. Show failed assignment, shallow immutability, and hashability only when all keys and values are hashable. It is not a `dict` subclass. |
| High | [Unpacking in comprehensions (PEP 798)](https://docs.python.org/3.15/whatsnew/3.15.html#whatsnew315-unpacking-in-comprehensions) | Chapter 9 (comprehensions) | Compare `[*group for group in groups]` with the existing nested-comprehension form. Add dictionary merging and ask which duplicate key wins: the last value encountered. |
| Medium | [Explicit lazy imports (PEP 810)](https://docs.python.org/3.15/whatsnew/3.15.html#whatsnew315-lazy-imports) | Chapters 11 (modules) and 31 (professional Python) | Measure CLI startup before and after `lazy import`. Discuss delayed import errors and module side effects. The new syntax is module-scope only, not inside functions, classes, or `try` blocks. Keep normal imports as the default teaching pattern. |
| Medium | [`sentinel` (PEP 661)](https://docs.python.org/3.15/whatsnew/3.15.html#whatsnew315-sentinel) | Chapter 10 (functions) | Distinguish an omitted argument from an explicitly supplied `None`, using identity checks. Contrast with the older `object()` sentinel pattern. |
| Medium | [`profiling` and Tachyon (PEP 799)](https://docs.python.org/3.15/whatsnew/3.15.html#whatsnew315-profiling-package) | Chapter 26 (production debugging) | Compare deterministic call tracing with statistical sampling. Introduce `profiling.tracing` and `profiling.sampling`; retain `cProfile`, which remains a compatibility alias. Check platform and process-attachment restrictions before adding a lab. |
| Optional | [Stable ABI for free-threaded builds (PEP 803)](https://docs.python.org/3.15/whatsnew/3.15.html#whatsnew315-abi3t) | Chapters 20 (concurrency) and 31 (packaging) | Add an ecosystem note about extension compatibility, not a C-extension tutorial. A stable ABI does not make every extension thread-safe or automatically enable GIL-free execution. |

### Two small examples to develop into exercises

**Python 3.15+ only — immutable mapping (Chapter 8):**

```python
settings = frozendict(theme="dark", retries=3)
print(settings["theme"])  # dark
# settings["theme"] = "light"  # TypeError: assignment is not supported
```

Ask why a list stored as a value can still change: the mapping itself is
immutable, not every object it references. A `frozendict` containing a list
value is not hashable.

**Python 3.15+ only — flattening (Chapter 9):**

```python
groups = [[1, 2], [3], [4, 5]]
flattened = [*group for group in groups]
print(flattened)  # [1, 2, 3, 4, 5]
```

Keep the older-compatible equivalent alongside it:

```python
flattened = [item for group in groups for item in group]
```

The 3.15-specific examples above were checked against the official
documentation, but have not been executed on Python 3.15 in this workspace.

## Relevant Python 3.14 gaps worth covering too

These are **3.14 introductions**, not new 3.15 features. The current Chapter 20
already explains the optional free-threaded build and its supported status
from 3.14; retain that guidance rather than presenting it as new in 3.15.

| Feature and source | Where it fits | Important teaching boundary |
| --- | --- | --- |
| [Template strings / t-strings (PEP 750)](https://docs.python.org/3.14/whatsnew/3.14.html#whatsnew314-template-string-literals) | Chapters 6 (strings) and 22 (web APIs) | A t-string produces a `Template`, not a `str`. It does not automatically escape HTML or make SQL safe: processing must be context-appropriate; continue teaching parameterized SQL. |
| [Deferred annotations (PEPs 649/749)](https://docs.python.org/3.14/whatsnew/3.14.html#whatsnew314-deferred-annotations) | Chapters 10 and 31 | Explain forward references and `annotationlib` as an advanced extension. Annotations do not enforce runtime argument types. |
| [`InterpreterPoolExecutor`](https://docs.python.org/3.14/library/concurrent.futures.html#interpreterpoolexecutor) | Chapter 20 | Compare with the existing thread/process pools. Workers have isolated interpreters and their own GILs; mutable state is not shared as in ordinary threads, and serialization and extension compatibility matter. |
| [`argparse` suggestions and color](https://docs.python.org/3.14/library/argparse.html#suggest-on-error) | Chapter 31 | Small CLI usability improvement; label the required version and avoid tests that depend on terminal color. |

## Compatibility and implementation order

1. Add the UTF-8 explanation first. Chapter 13 currently demonstrates `open()`
   without an encoding; Chapter 26 discusses platform-dependent defaults.
   Explain both pre-3.15 behavior and 3.15's new default instead of assuming
   every learner has the same interpreter or input-file encoding.
2. Add optional `frozendict` and unpacking examples, then a quiz/debugging task
   for each. Keep older-compatible core exercises available.
3. Add lazy imports, sentinels, and profiling as intermediate/advanced extras.
   Leave C API details, experimental JIT tuning, and package startup hooks out
   of the beginner path. Do not promise universal performance gains.
4. Before putting new syntax behind a “Run Code” button, verify the actual
   browser Python version with `import sys; print(sys.version)`.
   Both `assets/playground.js` and `assets/challenge-engine.js` currently pin
   Pyodide 0.26.4; that pin is separate from a learner's local Python install.
   Use clearly labelled local-only examples until the browser runtime is
   deliberately upgraded and tested, including NumPy and pandas loading.
5. Add 3.15 to the capstone CI matrices only after checking dependencies and
   running the suites; retain existing supported versions. For example,
   `.github/workflows/capstone-2-data-cli-ci.yml` currently tests 3.11 and 3.12.
   Documentation of the latest release is not evidence of tested support.

When refreshing this note, recheck the official downloads page for the latest
stable patch release, update the dated notes in `README.md` and `index.html`,
and distinguish released features from previews or proposals.
