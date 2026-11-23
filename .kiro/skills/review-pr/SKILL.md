---
name: "review-pr"
description: "Run the full PR review workflow on one pull request from nebius-academy-templates/python-pr-review-practice. Invoke explicitly with /review-pr <PR number>. Follow the steps exactly as written, in order."
---

# Review PR

Follow these steps exactly as written, in order. Do not skip, reorder, or merge steps.

The PR number is `$ARGUMENTS`. In the IDE, take it from the user's message. If no PR number
is given, ask for it and stop.

You are the orchestrator. You fetch the PR, delegate the review to three subagents, merge
their results, and run the publish gate. Only you talk to the user and only you may post.

## Steps

1. **Fetch the PR.** Fetch PR #$ARGUMENTS from `nebius-academy-templates/python-pr-review-practice`
   using the GitHub MCP server: call `pull_request_read` with `method: "get_diff"`
   (owner `nebius-academy-templates`, repo `python-pr-review-practice`). If the diff is empty
   or cannot be loaded, say so and stop.
2. **Delegate to the three reviewers in parallel.** Invoke these subagents at the same time.
   Pass each one the full diff text from step 1 as its task prompt, with the instruction
   "Review this diff and return your findings in your output format."
   - `style-reviewer`: naming and style, using app-conventions and test-conventions.
   - `security-reviewer`: hardcoded credentials, injection, TLS, deserialization, validation,
     debug mode.
   - `architecture-reviewer`: ADR-001 to ADR-004.
   Wait for all three to return. If a subagent fails, retry it once. If it fails again, note
   which review is missing in the summary and continue.
3. **Consolidate the findings.** Merge the three results into the exact output format from
   `AGENTS.md`:
   - One `##` section per changed file, in diff order.
   - Under each file, list findings sorted HIGH, then MEDIUM, then LOW, keeping each
     reviewer's line number and rule citation. Label each one `style`, `security`, or
     `architecture`.
   - If two reviewers report the same issue on the same line (for example a hardcoded key as
     both a security issue and ADR-002), keep the higher-severity one and mention the other
     rule in it.
   - A file with no findings from any reviewer gets `- No findings.`
   - End with `## Summary`: files reviewed, total findings with HIGH/MEDIUM/LOW counts, and
     the verdict.
   Do not add findings the subagents did not return, and do not drop any they did return.
4. **Log the review.** Append one line to `review_history.md` in the format from the
   episodic memory rule in `AGENTS.md`, for example
   `2026-05-15 | PR-03 | Hardcoded API token in PaymentService | HIGH`. Use the
   highest-severity finding, or `No findings | NONE` for a clean PR. Do this before step 5,
   so the run is logged whatever the user answers.
5. **Present the review.** Show the full consolidated review to the user, then ask exactly:
   "Do you want me to post this comment to PR #<number>? Reply `yes` to confirm."
   Stop and wait for the reply.
6. **Post only on `yes` or `post`.**
   - If the reply is `yes` or `post`: state that you are about to post the review as a
     comment on PR #<number> in `nebius-academy-templates/python-pr-review-practice`, then post
     it with `pull_request_review_write` using event `COMMENT`. Never use `APPROVE` or
     `REQUEST_CHANGES`.
   - Any other reply: do not call any GitHub write tool. Confirm that nothing was posted.
