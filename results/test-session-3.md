# Test session 3

<!--
TODO: Paste the transcript showing selective skill loading (app-conventions on a PR that only
touches application files, test-conventions on a PR reviewed for its test files) and the
workflow run pausing before posting.
-->



Review the application files in PR #2 from https://github.com/nebius-academy-templates/python-pr-review-practice for style issues. Check that the chat shows app-conventions loading and not test-conventions.

Run Command Hook
koi-security-userPromptSubmit-prompt
Kiro

Included Steering
Included Steering

AGENTS.md

Thought complete
I need to review application files in PR #2, so I should load the app-conventions skill rather than test-conventions. The task is about application code, so I'll load app-conventions before fetching the PR itself since that's a blocking requirement for this task.
Run Command Hook
koi-security-preToolUse-tool

2 tool calls · 5 hooks triggered
Loaded skill: app-conventions

app-conventions
@github/pull_request_read
{
  "tool_id": "github::pull_request_read",
  "arguments": {
    "method": "get_diff",
    "owner": "nebius-academy-templates",
    "repo": "python-pr-review-practice",
    "pullNumber": 2
  }
}
{"diff":"diff --git a/src/profile_service.py b/src/profile_service.py\nnew file mode 100644\nindex 0000000..7ca03f0\n--- /dev/null\n+++ b/src/profile_service.py\n@@ -0,0 +1,9 @@\n+class ProfileService:\n+    # Boolean missing is/has/can prefix.\n+    loading = False\n+\n+    # Function name should be snake_case; missing type hints and docstring; mutable\n+    # default argument.\n+    def FetchProfile(self, user_id, tags=[]):\n+        tags.append(\"fetched\")\n+        return Profile(user_id, tags)\n\\ No newline at end of file\n"}
Run Command Hook
koi-security-preToolUse-tool
Run Command Hook
koi-security-postToolUse-tool
Run Command Hook
koi-security-postToolUse-tool
Run Command Hook
koi-security-preToolUse-tool
Run Command Hook
koi-security-postToolUse-tool

Thought complete
I'm going through src/profile_service.py line by line: the ProfileService class lacks a docstring, the loading flag's naming seems fine, and the FetchProfile method has issues—it's not snake_case, uses a mutable default argument, and is missing type hints. Tallying up the totals now: 0 HIGH, 4 MEDIUM, 2 LOW, for 6 total issues. I should also note that app-conventions skill loaded but test-conventions didn't, and I'll mention that briefly before the review output, keeping it concise per the expected format.
I loaded only the app-conventions skill for this review, not test-conventions. PR #2 changes one file, 
profile_service.py
, which is application code under src/.

PR Review
src/profile_service.py
[MEDIUM] Line 1: Public class ProfileService has no docstring — style: app-conventions rule 2 (type hints and docstrings)
[LOW] Line 3: The boolean attribute loading doesn't start with is_, has_, or can_ (for example, is_loading) — style: app-conventions rule 1 (naming)
[LOW] Line 7: Method name FetchProfile isn't snake_case (should be fetch_profile) — style: app-conventions rule 1 (naming)
[MEDIUM] Line 7: Public method FetchProfile has no type hints on user_id, tags, or the return value — style: app-conventions rule 2 (type hints and docstrings)
[MEDIUM] Line 7: Public method FetchProfile has no docstring — style: app-conventions rule 2 (type hints and docstrings)
[MEDIUM] Line 7: Mutable default argument tags=[], which line 8 then changes with tags.append(...) — style: app-conventions rule 3 (error handling, no mutable default arguments)
Summary
Files reviewed: 1
Findings: 6 (HIGH: 0, MEDIUM: 4, LOW: 2)
Verdict: Issues to address before human review.



Review the test files in PR #4 from https://github.com/nebius-academy-templates/python-pr-review-practice for style issues. Check that test-conventions loads and app-conventions doesn't.

Run Command Hook
koi-security-userPromptSubmit-prompt
Kiro

Included Steering
Included Steering

AGENTS.md

Thought complete
Since the user specifically asked about test files, I should load the test-conventions skill rather than app-conventions before diving in. I'll activate that skill now and fetch the PR diff in parallel.
Run Command Hook
koi-security-preToolUse-tool
Run Command Hook
koi-security-preToolUse-tool

2 tool calls · 4 hooks triggered
Loaded skill: test-conventions

test-conventions
@github/pull_request_read
{
  "tool_id": "github::pull_request_read",
  "arguments": {
    "method": "get_diff",
    "owner": "nebius-academy-templates",
    "repo": "python-pr-review-practice",
    "pullNumber": 4
  }
}
{"diff":"diff --git a/src/trip_service.py b/src/trip_service.py\nnew file mode 100644\nindex 0000000..b63b38b\n--- /dev/null\n+++ b/src/trip_service.py\n@@ -0,0 +1,7 @@\n+class TripService:\n+    def __init__(self, api_client):\n+        self.api_client = api_client\n+\n+    def load_trips(self, user_id):\n+        # Calls the API client directly instead of going through a repository.\n+        return self.api_client.get(f\"/users/{user_id}/trips\")\n\\ No newline at end of file\ndiff --git a/tests/test_trip_service.py b/tests/test_trip_service.py\nnew file mode 100644\nindex 0000000..415552c\n--- /dev/null\n+++ b/tests/test_trip_service.py\n@@ -0,0 +1,10 @@\n+from src.api_client import ApiClient\n+from src.trip_service import TripService\n+\n+\n+# Test name doesn't describe the behavior under test; hits a real API client\n+# instead of mocking it.\n+def test_1():\n+    service = TripService(ApiClient())\n+    result = service.load_trips(42)\n+    assert result is not None\n\\ No newline at end of file\n"}
Run Command Hook
koi-security-postToolUse-tool
Run Command Hook
koi-security-postToolUse-tool
Run Command Hook
koi-security-preToolUse-tool
Run Command Hook
koi-security-postToolUse-tool

Thought complete
Looking at the test file, I'm noting a couple issues: the test name on line 7 doesn't describe the behavior being tested, and line 8 uses a real ApiClient instead of a mock, which breaks test isolation since it's imported directly and constructed without mocking. Line 9's user id of 42 doesn't seem like sensitive data worth flagging. Since the user asked only about test files, I'll scope my output to just this file, noting that src/trip_service.py was out of scope and that app-conventions wasn't loaded — I'll mention that before the review, then end with a brief summary.
Only the test-conventions skill was loaded. app-conventions was not, because the one test file in scope is 
test_trip_service.py
. The PR also changes 
trip_service.py
, but that is application code, so I left it out of this review.

PR Review
tests/test_trip_service.py
[LOW] Line 7: The test name test_1 does not describe the behavior under test. A name like test_load_trips_returns_trips_for_user would. — style: test-conventions rule 1, naming
[MEDIUM] Line 8: TripService is built with a real ApiClient() instead of a mock or fake, so the test depends on an external service. — style: test-conventions rule 3, isolation
Summary
Files reviewed: 1
Findings: 2 (HIGH: 0, MEDIUM: 1, LOW: 1)
Verdict: Issues to address before human review.








/review-pr 1, then Review PR #1. In the IDE the number is passed as context, so keep it visible in your message. When the agent asks whether to post, answer no.

Run Command Hook
koi-security-userPromptSubmit-prompt
Kiro

Included Steering
Included Steering

AGENTS.md

Thought complete
The user ran /review-pr 1, so I need to activate the review-pr skill first before proceeding.
Run Command Hook
koi-security-preToolUse-tool

2 tool calls · 5 hooks triggered
Loaded skill: review-pr

review-pr
@github/pull_request_read
{
  "tool_id": "github::pull_request_read",
  "arguments": {
    "method": "get_diff",
    "owner": "nebius-academy-templates",
    "repo": "python-pr-review-practice",
    "pullNumber": 1
  }
}
{"diff":"diff --git a/src/ride_history_service.py b/src/ride_history_service.py\nnew file mode 100644\nindex 0000000..9033ab7\n--- /dev/null\n+++ b/src/ride_history_service.py\n@@ -0,0 +1,13 @@\n+class RideHistoryService:\n+    \"\"\"Loads a rider's recent trips through the ride repository.\"\"\"\n+\n+    def __init__(self, ride_repository: \"RideRepository\") -\u003e None:\n+        self._ride_repository = ride_repository\n+        self.is_loading = False\n+\n+    def load_rides(self) -\u003e list[\"Ride\"]:\n+        self.is_loading = True\n+        try:\n+            return self._ride_repository.recent_rides()\n+        finally:\n+            self.is_loading = False\n\\ No newline at end of file\ndiff --git a/tests/test_ride_history_service.py b/tests/test_ride_history_service.py\nnew file mode 100644\nindex 0000000..9484e70\n--- /dev/null\n+++ b/tests/test_ride_history_service.py\n@@ -0,0 +1,14 @@\n+from unittest.mock import Mock\n+\n+from src.ride_history_service import RideHistoryService\n+\n+\n+def test_load_rides_returns_recent_rides_from_repository() -\u003e None:\n+    ride_repository = Mock()\n+    ride_repository.recent_rides.return_value = [\"ride-1\", \"ride-2\"]\n+    service = RideHistoryService(ride_repository)\n+\n+    rides = service.load_rides()\n+\n+    assert rides == [\"ride-1\", \"ride-2\"]\n+    assert service.is_loading is False\n\\ No newline at end of file\n"}
Run Command Hook
koi-security-postToolUse-tool
Run Command Hook
koi-security-preToolUse-tool
Run Command Hook
koi-security-postToolUse-tool
Run Command Hook
koi-security-preToolUse-tool
Run Command Hook
koi-security-postToolUse-tool
Files in diff order: 
ride_history_service.py
 is an application file, and 
test_ride_history_service.py
 is a test file. Next I'll load the convention skills.

Run Command Hook
koi-security-preToolUse-tool

2 tool calls · 3 hooks triggered
Loaded skill: app-conventions

app-conventions
Loaded skill: test-conventions

test-conventions
Run Command Hook
koi-security-preToolUse-tool
Run Command Hook
koi-security-postToolUse-tool
Run Command Hook
koi-security-postToolUse-tool

Thought complete
I'm checking whether load_rides counts as a public function needing a docstring under the app-conventions rule, noting the template's compliant version includes one, but I'm wary of over-reporting given the "if unsure, don't report" guidance. For the test file, I note a single clean test with proper assertions and mocking, nothing to flag. Now I'm moving on to check security and architecture concerns. The service injects the repository via the constructor, which is clean. My verdict: no issues found, though I want to add a brief note about my docstring judgment before presenting the review, then follow the required format and wait for the user's confirmation before posting.
A note before the review: load_rides has no docstring. The app-conventions docstring rule covers "public classes and public functions", while its type-hint rule names "functions and methods" separately, so it's unclear whether the docstring rule applies to methods. Because unclear cases are not reported, I left it out.

PR Review
src/ride_history_service.py
No findings.
tests/test_ride_history_service.py
No findings.
Summary
Files reviewed: 2
Findings: 0 (HIGH: 0, MEDIUM: 0, LOW: 0)
Verdict: No issues found.
Do you want me to post this comment to PR #1? Reply yes to confirm.

Est. Credits Used: 1.04
Elapsed time: 2m 33s


Checkpoint
Restore
no

Run Command Hook
koi-security-userPromptSubmit-prompt
Kiro
I didn't post anything to PR #1 and didn't use any GitHub write tools.