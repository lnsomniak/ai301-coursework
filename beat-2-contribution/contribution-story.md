# Contribution: Weights download progress line floods non-terminal logs

**Contribution Number:** 1
**Student:** Sergio Hernandez
**GitHub Username:** lnsomniak
**Issue:** https://github.com/AllenCell/SubCellSeg/issues/5
**Pull Request:** None opened: fixed by the maintainer in https://github.com/AllenCell/SubCellSeg/pull/6

---

## Toolkit

**Unit 5: Ran my first real scan and lost a claim race by seven minutes**

I installed the issue-scout over my unit 1 skill, rewrote my fit profile around Python and React with Vite, and wrote three `gh` queries with a two per repo cap after test runs showed a few repos mass filing good first issues. The first scan pulled 180 unique issues, and my rubric accepted all five it graded, including a two-day-old JavaScript repo with no stars and an agent run task farm, which told me checks written for the Path Review sandbox are too lenient for open GitHub. My second pick, AllenCell/SubCellSeg#5, passed “Nobody already on it” at 11:03 PM and was closed by the maintainer’s own PR at 11:17, seven minutes before my claim went up. Next time I’d rank by most recent activity instead of fewest comments and re run the claim check right before claiming, since a scan’s read on who’s working an issue goes stale within minutes.

**Unit 6: voice-guard not built or run**

Not built or run. Because AllenCell/SubCellSeg#5 closed on November 10 before I opened a PR, there was no pull request description or maintainer review thread for a voice check to gate.

**Unit 7: ship-gate not built or run**

Not built or run. Once the maintainer merged PR #6 upstream, no fix branch or candidate pull request existed to run pre-flight ship checks against.

**Unit 8: pr-monitor not built or run**

Not built or run. With no open pull request on AllenCell/SubCellSeg, there were no CI runs or maintainer review comments to monitor.

---

## Why I Chose This Issue

I chose this issue because of a combination of its readability and apparent ease, alongside a search that felt like I was never going to find an issue I was confident in. My scout surfaced it as its second pick, but the same rubric that accepted it also waved through a two-day-old JavaScript repo with no stars and an agent-run task farm, so I came away trusting the scout's search more than its verdicts. Throughout this search for issues, I learned something new about myself. I've been developing for quite some time now, and yet I've always felt a little uneasy about public contributions since I've always been a perfectionist that eases himself through knowing that the work is mine to judge and mine only. This experience both taught me that I can do it, and should be doing it more often. Despite not actually having been able to submit my claim, the python bug was something that was fun to work through for a night.

Claim comment: https://github.com/AllenCell/SubCellSeg/issues/5#issuecomment-6787853945

Withdrawal comment: https://github.com/AllenCell/SubCellSeg/issues/5#issuecomment-6787943923

**Toolkit**

- Tool: issue-scout, my scout, run once with `claude "issue-scout: run a scan"` using my three `gh` queries and ranking filters
- What it produced: a scan of 180 unique issues narrowed to five graded candidates, all accepted; SubCellSeg#5 ranked second in fit order and passed all six checks, including “Nobody already on it,” at 11:03 PM
- Difficulties: the rubric accepted an off-profile JavaScript repo and an agent-run task farm, and its claim check was stale by 11:17 PM, when the maintainer's own PR closed the issue
- What changed: I'd rank by most recent activity instead of fewest comments and re run the claim check right before claiming

---

## Understanding the Issue

**Problem Description**

On a fresh environment, running `subcellseg predict` triggers an automatic download of 64 MB of pretrained model weights. The progress callback `_report` in `src/subcellseg/weights.py` is invoked by `urllib.request.urlretrieve` for every 8 KB chunk (about 8,000 calls) and rewrites the status line with a carriage return (`\r`) each time. Interactive terminals overwrite the line in place, but non-terminal environments such as CI runners, SLURM job logs, `nohup` outputs, and Jupyter notebooks record every redraw, flooding logs with about 150 KB of repetitive `0 / 64 MB (0%)` text.

**Expected Behavior**

Interactive terminals should display a clean, single-line progress indicator updating once per whole percentage point. Non-terminal streams (`sys.stderr.isatty() == False`) should emit concise, readable progress updates, such as a start line, 10% increments, and completion, without carriage-return log flooding.

**Current Behavior**

Every 8 KB chunk writes a new `\r`-terminated status string to `stderr`, generating roughly 8,000 redraws (about 150 KB of output) when redirected to a log file, even though the download and SHA-256 verification succeed.

**Affected Components**

The `_report` reporthook function in `src/subcellseg/weights.py`, the `urllib.request.urlretrieve` call during weight initialization, and first-run `subcellseg predict` CLI execution.

**Toolkit**

- Tool: issue-scout (my Unit 1 skill extended in Unit 5)
- What it produced: scanned 180 unique open issues across three `gh` queries filtered for Python and React/Vite repositories, surfacing AllenCell/SubCellSeg#5 as the #2 ranked candidate after it passed all six eligibility checks, including no existing assignee or claim.
- Difficulties: test scans showed a few repositories mass-filing `good first issue` tickets, and the rubric accepted all 5 graded issues, including a two-day-old zero-star JavaScript repository and an automated task farm. The claim-check snapshot also went stale within 14 minutes, when the maintainer fixed and closed the issue before my claim comment posted.
- What changed: rewrote the fit profile around Python and React/Vite and added a two-issues-per-repository cap after the test scans, and on November 25 added a re-check step to issue-scout's `SKILL.md` (step 6) that runs `gh issue view` right before any claim.

---

## Reproduction Process

**Environment Setup**

- OS and shell: Windows 11 Pro (10.0.26200), Git Bash
- Runtime and package: Python 3.13.5, `subcellseg` 0.1.0 installed editable from my fork (`pip install -e .`) into a `.venv`
- Cache and input: an isolated weight cache via `SUBCELLSEG_CACHE_DIR=$(mktemp -d)` and a small dummy 3D TIFF test volume
- Challenge: reproducing the full CLI command required generating a valid dummy 3D TIFF input and isolating the cache directory so `urlretrieve` triggered a fresh 64 MB weight download on each run without touching the global user cache.

**Steps to Reproduce**

1. Checked out commit `cd3a46a`, the commit immediately before PR #6.
2. Ran the issue's reproduction command in Git Bash with a fresh temporary cache directory:
   ```bash
   SUBCELLSEG_CACHE_DIR=$(mktemp -d) subcellseg predict --input in --output out 2> log.txt
   wc -c log.txt
   ```
3. Observed `153170` bytes written to `log.txt` across about 8,150 `\r` redraws starting from `0 / 64 MB (0%)`, exiting with status `0`.
4. Checked out merge commit `40d402b` (PR #6 merged) and re-ran the identical command against a fresh temporary cache directory.
5. Observed `642` bytes written to `log.txt` across 11 clean progress lines (`0%` through `100%` in steps of 10), exiting with status `0`.

**Reproduction Evidence**

- **Commit showing reproduction:** https://github.com/lnsomniak/SubCellSeg/commit/862ba517f4df86d1ce9967a234124b18195f1b73 (branch `repro/issue-5`, adding `repro/simulate_progress.py`)
- **Screenshots/logs:**
  ```text
  cd3a46a  (before PR #6)   wc -c log.txt -> 153170   about 8,150 \r redraws of "0 / 64 MB (0%)" onward
  40d402b  (PR #6 merged)   wc -c log.txt -> 642      11 progress lines, 0% to 100% in steps of 10
  ```
- **My findings:** a function-level simulation (8,193 lines and 153,632 bytes before, 11 lines and 207 bytes after) and full end-to-end `subcellseg predict` runs both confirmed the roughly 150 KB of log bloat reported in #5, and showed that PR #6 cut non-terminal stderr output by 99.6% while keeping exit code `0`.

**Toolkit**

- Tool: Claude Code CLI and Git (`repro/simulate_progress.py`)
- What it produced: an isolated function-level simulation script (`repro/simulate_progress.py`, +29 lines) committed to `repro/issue-5` on my fork with the `Assisted-by: Claude` trailer, followed by end-to-end CLI verification across `cd3a46a` and `40d402b`.
- Difficulties: I first ran only a standalone simulation, before setting up the full CLI pipeline with a dummy 3D TIFF and an isolated `SUBCELLSEG_CACHE_DIR`.
- What changed: added full end-to-end `subcellseg predict` runs on the pre-fix (`cd3a46a`) and post-fix (`40d402b`) commits, so the byte counts reflect actual CLI stderr output.

---

## Solution Approach

**Analysis**

In `src/subcellseg/weights.py`, `urllib.request.urlretrieve` invokes `_report(block_num, block_size, total_size)` on every 8 KB block transferred. Because `_report` unconditionally formatted the byte count and percentage and wrote `\r...` to `sys.stderr` on every call, without checking whether the displayed percentage had changed or whether `sys.stderr.isatty()` was `True`, a 64 MB download produced about 8,150 redundant writes.

**Proposed Solution**

My claim-comment plan was to track the last printed percentage and redraw with `\r` only when the whole-number percentage changed in interactive terminals, and, when `sys.stderr.isatty()` is `False`, print a start line, one newline-terminated update per 10% milestone, and a final completion line, with unit tests for both TTY and non-TTY paths. What I would not change: the underlying `urlretrieve` download flow, cache path resolution, or the post-download SHA-256 checksum verification.

**Implementation Plan**

1. Refactor `_report` in `src/subcellseg/weights.py` to keep state across chunks and branch on `sys.stderr.isatty()`.
2. Emit `\r`-updated lines only when the whole-number percent increments on TTYs, and newline-delimited 10% steps on non-TTYs.
3. Add unit tests covering both TTY and non-TTY `stderr` streams and run `pytest`.
4. Open a PR referencing `#5` with the `Assisted-by: Claude` trailer per `CONTRIBUTING.md`.

Deviation: maintainer `@smishra3` merged PR #6 (`40d402b`) seven minutes before my claim comment posted, so I withdrew my claim instead of opening a duplicate PR. Comparing PR #6 against my plan, the non-terminal behavior matches (11 lines from 0% to 100% in 10% steps), while PR #6 improved on my plan by keeping state in a `_Progress` class with a `finish()` method that writes the final newline only on TTYs, and by handling unknown-size (`total_size <= 0`) downloads.

**Toolkit**

- Tool: Claude Code CLI (codebase exploration and diff comparison)
- What it produced: located `_report` in `src/subcellseg/weights.py`, drafted the scoped plan posted in my claim comment, and compared PR #6 (+37 -10 in `src/subcellseg/weights.py`, plus `CHANGELOG.md` and tests) against my proposed design.
- Difficulties: the issue was resolved upstream while I was inspecting the repository and preparing my claim, so there was nothing left to change in `src/subcellseg/weights.py` on my fork.
- What changed: shifted from writing a fix PR to committing a reproduction branch (`repro/issue-5`) and measuring the maintainer's merged fix (`40d402b`) against the parent commit (`cd3a46a`).

---

## Testing Strategy

1. **Redirected-stderr byte and line count:** run `SUBCELLSEG_CACHE_DIR=$(mktemp -d) subcellseg predict --input in --output out 2> log.txt` and measure `wc -c log.txt` before (153,170 bytes, about 8,150 redraws) and after (642 bytes, 11 lines).
2. **TTY vs. non-TTY unit tests:** parametrize `test_download_progress_stays_short` across `isatty() == True` (asserting 101 `\r` redraws, one per whole percent from 0% to 100%) and `isatty() == False` (asserting 11 newline-terminated lines in 10% steps with no `\r`), the test structure merged in PR #6.
3. **Full suite regression check:** run `pytest` across the repository to confirm weight downloading, caching, and SHA-256 validation still work. On November 25 I ran it at `40d402b`:
   ```text
   13 passed, 1 warning in 36.94s
   ```

**Toolkit**

- Tool: Git Bash, `wc`, and `pytest`
- What it produced: exact byte counts (153,170 vs. 642) and line formatting under redirected `stderr` across `cd3a46a` and `40d402b`, plus a full `pytest` run at `40d402b` with 13 passed.
- Difficulties: making sure each run used an empty `SUBCELLSEG_CACHE_DIR` so cached weights did not skip the download hook.
- What changed: standardized the verification command around `SUBCELLSEG_CACHE_DIR=$(mktemp -d)` so every run exercised the live `urlretrieve` progress path from 0% to 100%.

---

## Implementation Notes

**2026-11-10 10:33 PM CST**

Maintainer `@smishra3` opened AllenCell/SubCellSeg#5 ("Weights download progress line floods non-terminal logs").

**2026-11-10 11:03 PM CST**

Ran issue-scout; #5 ranked #2 among 180 scanned issues and passed all six checks, including no existing claims.

**2026-11-10 11:12 to 11:17 PM CST**

The maintainer committed the fix, opened PR #6, and merged it (`40d402b`), closing #5.

**2026-11-10 11:21 PM CST (wrong turn)**

My pre-claim status check missed that #5 had closed four minutes earlier.

**2026-11-10 11:24 PM CST**

Posted my claim comment on #5 (https://github.com/AllenCell/SubCellSeg/issues/5#issuecomment-6787853945) outlining the TTY vs. non-TTY fix plan and `CONTRIBUTING.md` compliance.

**2026-11-10 11:28 PM CST**

Ran a first function-level simulation of the old and new progress hooks: 8,193 lines and 153,632 bytes before vs. 11 lines and 207 bytes after. The script was not committed yet.

**2026-11-10 11:30 PM CST**

Noticed #5 had closed right before my claim and posted a withdrawal comment (https://github.com/AllenCell/SubCellSeg/issues/5#issuecomment-6787943923) thanking the maintainer for the quick fix.

**2026-11-18**

Committed `repro/simulate_progress.py` to `lnsomniak/SubCellSeg` (`862ba51`) with `Assisted-by: Claude`, and ran the issue's full `subcellseg predict` command at `cd3a46a` (153,170 bytes) and `40d402b` (642 bytes).

**2026-11-25**

Ran `pytest` at `40d402b` (13 passed), and added a step 6 re-check to issue-scout's `SKILL.md` that runs `gh issue view <number> --json state,comments,assignees` right before any claim.

**Toolkit**

- Tool: GitHub CLI (`gh`) and Git
- What it produced: a timestamped trail of the scan, the issue comments, fork commit `862ba51`, and the CLI reproduction measurements.
- Difficulties: my 11:21 PM check came four minutes after the 11:17 PM merge and still missed it, so I claimed an already-closed issue at 11:24 PM.
- What changed: on November 25 I added a re-check step (step 6) to issue-scout's `SKILL.md` that runs `gh issue view` immediately before any claim and stops if the issue is closed, assigned, or newly claimed.

---

## Pull Request

**PR Link:** None opened: fixed by the maintainer in https://github.com/AllenCell/SubCellSeg/pull/6

**PR Description:** No PR was opened, because maintainer `@smishra3` merged PR #6 (`40d402b`) seven minutes before my claim comment posted, so I withdrew my claim to avoid duplicate work.

**Maintainer Feedback:**

- 2026-11-10: none directly on my comments; the issue was closed by the maintainer's own PR #6, and I posted a withdrawal note thanking them for the fix.

**Status:** Closed (resolved upstream via PR #6)

**Toolkit**

- Tool: GitHub web and `gh` CLI
- What it produced: my claim and withdrawal comments on AllenCell/SubCellSeg#5, and my reproduction script on fork branch `repro/issue-5`.
- Difficulties: there is no upstream PR to submit for an issue the maintainer already fixed and closed.
- What changed: closed out the contribution with a public withdrawal comment instead of leaving a stale claim or opening a redundant PR.

---

## Learnings and Reflections

**Technical Skills Gained**

I learned how `urllib.request.urlretrieve` reporthook callbacks behave across terminal and non-terminal outputs (`sys.stderr.isatty()`), and how keeping progress state in a small class (`_Progress` in PR #6) handles edge cases like unknown `Content-Length` and trailing newlines.

**Challenges Overcome**

When AllenCell/SubCellSeg#5 closed minutes before my claim comment landed, I didn't open a duplicate PR, withdrew publicly within six minutes, and still checked the root cause and the fix on my fork (`862ba51`) using the issue's own command, 153,170 bytes down to 642.

**What I'd Do Differently Next Time**

Two changes. First, tighten issue-scout's grading rubric and sort by most recent activity rather than fewest comments, so low-signal repos like zero-star projects or automated task farms get filtered before grading. Second, run `gh issue view <num> --json state,comments` immediately before posting a claim instead of trusting a 20-minute-old scan; I have now added that as step 6 in issue-scout.

---

## Resources Used

- Issue #5 problem statement and reproduction command: https://github.com/AllenCell/SubCellSeg/issues/5
- The maintainer's merged fix and parametrized test in PR #6: https://github.com/AllenCell/SubCellSeg/pull/6
- AllenCell/SubCellSeg `CONTRIBUTING.md` guidelines, which require `Assisted-by:` trailers for AI-assisted contributions
- Python standard library documentation for `urllib.request.urlretrieve` (the `reporthook` signature) and `sys.stderr.isatty()`
