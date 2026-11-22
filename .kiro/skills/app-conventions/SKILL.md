---
name: "app-conventions"
description: "Python application and library code conventions (naming, type hints, docstrings, error handling). Load only when reviewing application code: Python files that are NOT under a tests/ directory and NOT named test_*.py or *_test.py, for example files under src/. Do not load for test files."
---

## Rules

Apply these rules only to added or changed lines (`+` lines) in application files.

1. **Naming** `[LOW]`
   - Functions, methods, variables, attributes, and parameters use `snake_case`.
   - Classes use `PascalCase`. Module-level constants use `UPPER_SNAKE_CASE`.
   - Boolean variables, attributes, and functions returning `bool` start with `is_`, `has_`,
     or `can_` (for example `is_active`, `has_access`, `can_retry`).
2. **Type hints and docstrings** `[MEDIUM]`
   - Public functions and methods (names not starting with `_`) have type hints on every
     parameter and on the return value. `self` and `cls` need no hint.
   - Public classes and public module-level functions have a docstring. Methods inside a
     class do not need their own docstring if the class has one.
3. **Error handling** `[MEDIUM]`
   - No bare `except:`. Catch a specific exception type.
   - No silently swallowed exceptions (`except SomeError: pass`) without a comment saying why.
   - No mutable default arguments (`def f(items=[])`, `def f(opts={})`). Use `None` and
     create the object inside the function.
   - No mutable class-level attributes (`items = []`) used as per-instance state.
4. **Imports** `[MEDIUM]`
   - No unused imports and no wildcard imports (`from x import *`).

Report a finding only when a changed line clearly breaks one of these rules. If unsure, do
not report it.

## Template

Compliant:

```python
class RideHistoryService:
    """Loads a rider's recent trips through the ride repository."""

    def __init__(self, ride_repository: "RideRepository") -> None:
        self._ride_repository = ride_repository
        self.is_loading = False

    def load_rides(self, limit: int | None = None) -> list["Ride"]:
        """Return the rider's most recent rides."""
        try:
            return self._ride_repository.recent_rides(limit)
        except ConnectionError:
            return []
```

Non-compliant:

```python
class rideHistory:                      # [LOW] class not PascalCase
    rides = []                          # [MEDIUM] mutable class-level state

    def LoadRides(self, filters={}):    # [LOW] not snake_case; [MEDIUM] mutable default; [MEDIUM] no type hints
                                        # (also: class has no docstring → [MEDIUM])
        loading = True                  # [LOW] boolean without is_/has_/can_ prefix
        try:
            return self.repo.recent_rides(filters)
        except:                         # [MEDIUM] bare except
            pass
```
