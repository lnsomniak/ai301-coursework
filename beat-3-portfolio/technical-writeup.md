# Calibrating an AI Pull Request Pre-Check Skill from 14/20 to 20/20

## Overview

Before submitting a pull request to an external repository, small mistakes like untested code paths, accidental churn, or missed contribution rules waste maintainer time. In Beat 1 (`beat-1-sandbox/unit-4/pull-request.md`), I built `pr-precheck`, a Claude Code skill that grades a candidate pull request against its implementation plan, its test evidence, and the target repository's contribution rules before submission. I wrote the skill's grading rubric, evaluation procedure, evidence guide, and `SKILL.md` frame, then calibrated it against a 20 PR benchmark of practice pull requests built on real open-source issues (7 good and 13 bad, across failure modes like missing tests, scope drift, and rule violations), with a passing bar of 18/20. After loosening an overly strict diff hygiene rule and two checks that were strict the same way, `pr-precheck` went from 14/20 to 20/20 on the benchmark and passed my own pull request, [codepath/pathreview-ai301-fa26-s3#128](https://github.com/codepath/pathreview-ai301-fa26-s3/pull/128), which also passed all six CI checks and is still open.

## What happened

I started by building the first version of `pr-precheck` around strict structural checks and running it against the 20 PR evaluation suite. The first run caught every broken pull request in the benchmark, 13 out of 13, but rejected six of the seven(lol) valid ones, landing well below the 18/20 threshold:

```text
agreement: 14/20 scored items  (bar: 18/20: below the bar)
clear-accept 1/7  not-tested 4/4  silent-drift 4/4  standards-wall 2/2  unreviewable 3/3
```

To see why valid pull requests were failing, I opened the grader's evidence on pkg-02 (Textualize/rich#4208), a clean fix that reordered `save_*` methods. Because moving code makes the diff show removed lines followed by identical added lines in the same hunk, my original `diff-clean` rule flagged normal restructuring as sloppy churn. The run-1 grader output confirmed the false positive:

```text
save_html/save_svg hunks pair identical '-' and '+' lines
```

I changed `diff-clean` so identical `-` and `+` lines count as part of the change whenever the same hunk restructures the code around them, and only fail when a hunk does nothing but re-print or reformat unchanged lines. At the same time, I loosened `fix-evidenced` and `description-truthful`, which had been rejecting valid PRs under the same rigid reading:

- Before (`diff-clean` v1): flagged any hunk containing "a pair of `-` and `+` lines with identical content."
- After (`diff-clean` v2): "Identical removed-and-added lines count as part of the change when the same hunk restructures the code around them," while a hunk "whose only effect is re-printing, re-indenting, or reformatting lines with unchanged content" still fails.

On run 2, agreement reached 20/20. pkg-02 passed with the right reasoning, and pkg-18, a canary package whose churn is genuinely mechanical, was still rejected:

```text
agreement: 20/20 scored items  (bar: 18/20: PASS)
pkg-02 evidence: "the only repeated lines are in hunks that change clear=False."
pkg-18 evidence: "the Startup hunk is identical lines re-printed; the imports are restructured with no new import needed beyond IsTerminal."
```

I then ran `pr-precheck` on my own pull request, codepath/pathreview-ai301-fa26-s3#128, before opening it. It passed every required check, and all six CI jobs passed on GitHub.

That habit of checking automated verdicts against ground truth before acting carried into my Beat 2 contribution attempt on AllenCell/SubCellSeg#5, where `subcellseg predict` flooded non terminal logs with about 8,000 `\r` progress redraws. My issue-scout scan found the open issue at 11:03 PM, but the maintainer merged PR #6 at 11:17 PM, four minutes before my 11:21 PM pre-claim check missed the state change. After withdrawing my claim at 11:30 PM, I verified the before and after behavior on my fork with the issue's own reproduction command:

```text
cd3a46a  (before PR #6)   wc -c log.txt -> 153170   about 8,150 \r redraws of "0 / 64 MB (0%)" onward
40d402b  (PR #6 merged)   wc -c log.txt -> 642      11 progress lines, 0% to 100% in steps of 10
```

## Limitations and what's next

Relaxing `diff-clean` to allow identical `-` and `+` lines inside hunks that also change logic removed the false positives on reordering, but it created a specific blind spot: unnecessary churn hidden inside a large hunk that also contains a real one line fix will now pass `diff-clean` unflagged. My SubCellSeg claim showed the same kind of gap in a different tool, since a point in time repo check goes stale within minutes on an active repository.

Next, I plan to update `diff-clean` to compare the number of unchanged-but-reprinted lines against the modified logic lines in each hunk, flagging hunks where pure line movement passes a small threshold unless the PR plan explains it. I have already added a live `gh issue view` re-check to issue-scout right before any claim, and the next step is to add the same live state check right before opening a PR.
