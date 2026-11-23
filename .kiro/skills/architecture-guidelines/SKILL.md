---
name: "architecture-guidelines"
description: "Team architecture guidelines from docs/adr/ (layered architecture, configuration, repository pattern, dependency injection). Load when reviewing services, entrypoints, configuration, or data-access code for architecture issues."
---

## Rules

Every finding must cite the matching ADR by number and name, for example
`ADR-003 (Repository Pattern)`. The full ADRs are in `docs/adr/`.

1. **ADR-001 (Layered Architecture)** `[MEDIUM]`
   Entrypoints (route handlers, CLI commands, a script's `main()`) only parse input and format
   output. Business logic belongs in a service. An entrypoint must not contain business logic
   or access data directly.
2. **ADR-002 (Configuration)** `[MEDIUM]`, `[HIGH]` if the value is a secret
   Configuration and secrets come from environment variables or the single config module.
   No hardcoded configuration values, and no `os.environ` calls outside the config module.
3. **ADR-003 (Repository Pattern)** `[MEDIUM]`
   Services get data only through a repository. A service must not import or reference
   `ApiClient`, construct its own HTTP client, or call a database/ORM directly. Only
   repositories in the data layer may depend on `ApiClient`.
4. **ADR-004 (Dependency Injection)** `[MEDIUM]`
   Dependencies are passed in through the constructor or function parameters. No module-level
   global instances, class-level singletons (such as `Instance`), or ServiceLocator lookups.

Use `[LOW]` only for a minor structural issue that clearly breaks one of these rules but has
little impact, for example a single hardcoded non-secret default in an otherwise compliant
module. If you are not sure a rule is broken, do not report it.

## Template

Compliant:

```python
class TripService:
    """Loads a user's trips."""

    def __init__(self, trip_repository: "TripRepository") -> None:  # ADR-004: injected
        self._trip_repository = trip_repository

    def load_trips(self, user_id: int) -> list["Trip"]:
        return self._trip_repository.trips_for_user(user_id)        # ADR-003: via repository
```

Non-compliant:

```python
from src.api_client import ApiClient

class TripService:
    def __init__(self):
        self.api_client = ApiClient()                       # [MEDIUM] ADR-004 (Dependency Injection)

    def load_trips(self, user_id):
        return self.api_client.get(f"/users/{user_id}/trips")  # [MEDIUM] ADR-003 (Repository Pattern)
```
