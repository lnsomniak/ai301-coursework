# Calibrating an AI Pull-Request Pre-Check Skill from 14/20 to 20/20

## Overview

In Beat 1 (`beat-1-sandbox/unit-4/pull-request.md`), I built `pr-precheck`, a Claude Code skill that grades a candidate pull request against its plan, its tests, and the repository's rules before submission. I wrote the skill's grading rubric, evaluation procedure, evidence guide, and `SKILL.md` frame, then tested it against 20 practice PRs built from real open-source issues (7 good and 13 bad, with a passing bar of 18/20). On its first run, the skill caught every broken PR and rejected six of the seven good ones. After narrowing down why good PRs were failing and rewriting the rules that caused the false positives, `pr-precheck` scored 20/20 on the benchmark and greenlit my own pull request, [codepath/pathreview-ai301-fa26-s3#128](https://github.com/codepath/pathreview-ai301-fa26-s3/pull/128), which is open with all six CI checks passing.

## What happened

Run 1 of `pr-precheck` caught all 13 bad PRs across missing tests, scope drift, and rule violations, but passed only one of the seven valid PRs:

```text
agreement: 14/20 scored items  (bar: 18/20: below the bar)
clear-accept 1/7  not-tested 4/4  silent-drift 4/4  standards-wall 2/2  unreviewable 3/3
```

To see why valid PRs were failing, I opened the grader's evidence on `pkg-02` (`Textualize/rich#4208`), a clean fix that reordered `save_*` methods. Moving a method causes Git's diff to show removed lines followed by identical added lines in the same hunk, so my original `diff-clean` rule flagged normal reordering as churn:

```text
save_html/save_svg hunks pair identical '-' and '+' lines
```

I rewrote `diff-clean` so identical `-` and `+` lines count as part of the change when the same hunk restructures code around them, failing only when a hunk does nothing but reprint or reformat unchanged lines. I also loosened `fix-evidenced` and `description-truthful`, which had rejected valid PRs for similar surface mismatches:

* **Before (`diff-clean` v1):** Flagged any hunk containing "a pair of `-` and `+` lines with identical content."
* **After (`diff-clean` v2):** "Identical removed-and-added lines count as part of the change when the same hunk restructures the code around them," while a hunk "whose only effect is re-printing, re-indenting, or reformatting lines with unchanged content" still fails.

On Run 2, agreement reached 20/20. `pkg-02` passed for the right reason, while `pkg-18`, a canary case whose churn is genuinely mechanical, remained rejected:

```text
agreement: 20/20 scored items  (bar: 18/20: PASS)
pkg-02 evidence: "the only repeated lines are in hunks that change clear=False."
pkg-18 evidence: "the Startup hunk is identical lines re-printed; the imports are restructured with no new import needed beyond IsTerminal."
```

I then ran `pr-precheck` on my own pull request, [codepath/pathreview-ai301-fa26-s3#128](https://github.com/codepath/pathreview-ai301-fa26-s3/pull/128), before opening it. It passed every check, and all six CI jobs passed on GitHub.

In Beat 2, I ran into the opposite problem, a check that failed to catch a real state change, while working on [AllenCell/SubCellSeg#5](https://github.com/AllenCell/SubCellSeg/issues/5#issuecomment-6787853945), where `subcellseg predict` flooded non-terminal logs with about 8,000 `\r` progress redraws. My `issue-scout` scan found the open issue at 11:03 PM, and the maintainer merged PR #6 at 11:17 PM, four minutes before my 11:21 PM pre-claim check missed that the issue had closed. After withdrawing my claim at 11:30 PM, I verified the before-and-after behavior on my fork using the issue's reproduction command:

```text
cd3a46a  (before PR #6)   wc -c log.txt -> 153170   about 8,150 \r redraws of "0 / 64 MB (0%)" onward
40d402b  (PR #6 merged)   wc -c log.txt -> 642      11 progress lines, 0% to 100% in steps of 10
```

## Limitations and what's next

Relaxing `diff-clean` to permit identical `-` and `+` lines inside hunks that also change logic removed the false positives on method reordering, but created a blind spot: unnecessary churn hidden inside a large hunk that also contains a real one-line fix will now pass `diff-clean` unflagged. My SubCellSeg claim exposed a similar gap in a different tool, because a point-in-time repo check goes stale within minutes on an active repository.

Next, I plan to update `diff-clean` to compare the count of unchanged-but-reprinted lines against modified logic lines per hunk, flagging hunks where pure line movement exceeds a small threshold unless the PR plan explains it. I have already added a live `gh issue view` re-check to `issue-scout` right before any claim, and the next step is adding that same live state check right before opening a pull request.
