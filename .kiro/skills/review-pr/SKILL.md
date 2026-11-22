---
name: "review-pr"
description: "Run the full PR review workflow on one pull request from nebius-academy-templates/python-pr-review-practice. Invoke explicitly with /review-pr <PR number>. Follow the steps exactly as written, in order."
---

# Review PR

Follow these steps exactly as written, in order. Do not skip, reorder, or merge steps.

The PR number is `$ARGUMENTS`. In the IDE, take it from the user's message. If no PR number
is given, ask for it and stop.

## Steps

1. **Fetch the PR.** Fetch PR #$ARGUMENTS from `nebius-academy-templates/python-pr-review-practice`
   using the GitHub MCP server: call `pull_request_read` with `method: "get_diff"`
   (owner `nebius-academy-templates`, repo `python-pr-review-practice`). If the diff is empty
   or cannot be loaded, say so and stop.
2. **Sort the changed files.** List every changed file in diff order and classify it:
   - Test file: under a `tests/` directory, named `test_*.py` or `*_test.py`, or imports `pytest`.
   - Application file: any other Python file.
3. **Apply `app-conventions` to application files.** Load the `app-conventions` skill and
   check only the added or changed lines of application files against its rules.
4. **Apply `test-conventions` to test files.** Load the `test-conventions` skill and check
   only the added or changed lines of test files against its rules.
5. **Flag security and architecture issues.** Check the added or changed lines of every file
   against the Security and Architecture (ADR-001 to ADR-004) criteria in `AGENTS.md`.
   Security issues are `[HIGH]`.
6. **Compile the findings.** Build the review in the exact output format from `AGENTS.md`:
   one `##` section per changed file in diff order, `- No findings.` for clean files, and the
   `## Summary` section last. Report only concrete violations supported by the diff.
7. **Present the review.** Show the full review to the user, then ask exactly:
   "Do you want me to post this comment to PR #<number>? Reply `yes` to confirm."
   Stop and wait for the reply.
8. **Post only on `yes` or `post`.**
   - If the reply is `yes` or `post`: state that you are about to post the review as a
     comment on PR #<number> in `nebius-academy-templates/python-pr-review-practice`, then post
     it with `pull_request_review_write` using event `COMMENT`. Never use `APPROVE` or
     `REQUEST_CHANGES`.
   - Any other reply: do not call any GitHub write tool. Confirm that nothing was posted.

<!--
TODO (later lesson): update the steps to delegate to the three sub-agents and consolidate.
-->
