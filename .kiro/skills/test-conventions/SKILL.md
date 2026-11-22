---
name: "test-conventions"
description: "Python test code conventions (test naming, fixtures, isolation). Load only when reviewing test code: files under a tests/ directory, files named test_*.py or *_test.py, or files that import pytest. Do not load for application code under src/."
---

## Rules

Apply these rules only to added or changed lines (`+` lines) in test files.

1. **Naming** `[LOW]`
   - Test files are named `test_*.py` (or `*_test.py`).
   - Test functions start with `test_` and describe the behavior under test, for example
     `test_load_rides_returns_empty_list_when_repository_fails`. Names like `test_1`,
     `test_it`, or `test_service` are findings.
2. **Fixtures** `[LOW]`
   - When two or more tests in the changed code repeat the same setup (building the same
     objects or mocks), that setup belongs in a pytest fixture instead of being duplicated.
3. **Isolation** `[MEDIUM]`
   - External dependencies are mocked or faked: no real network calls, HTTP clients,
     databases, or filesystem paths outside `tmp_path`.
   - No test depends on another test's side effects: no module-level mutable state that
     tests modify, and no reliance on test execution order.
4. **Assertions** `[MEDIUM]`
   - Every test function contains at least one assertion (or `pytest.raises`).

Report a finding only when a changed line clearly breaks one of these rules. If unsure, do
not report it.

## Template

Compliant:

```python
from unittest.mock import Mock

import pytest

from src.ride_history_service import RideHistoryService


@pytest.fixture
def ride_repository() -> Mock:
    repository = Mock()
    repository.recent_rides.return_value = ["ride-1", "ride-2"]
    return repository


def test_load_rides_returns_recent_rides_from_repository(ride_repository: Mock) -> None:
    service = RideHistoryService(ride_repository)

    assert service.load_rides() == ["ride-1", "ride-2"]


def test_load_rides_resets_is_loading_flag(ride_repository: Mock) -> None:
    service = RideHistoryService(ride_repository)

    service.load_rides()

    assert service.is_loading is False
```

Non-compliant:

```python
import requests

from src.ride_history_service import RideHistoryService

CACHE = []                                       # [MEDIUM] shared state between tests


def test_1():                                    # [LOW] name doesn't describe behavior
    client = requests.get("https://api.example.com/rides")  # [MEDIUM] real network call
    CACHE.append(client.json())


def test_rides():
    service = RideHistoryService(CACHE[0])       # [MEDIUM] depends on test_1's side effects
    service.load_rides()                         # [MEDIUM] no assertion
```
