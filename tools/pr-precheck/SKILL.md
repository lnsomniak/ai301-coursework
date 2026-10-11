---
name: pr-precheck
description: Grade a PR package (a candidate pull request read against the plan it claims to implement and the issue that plan belongs to) and decide whether it is ready to submit. Use when checking your own branch, draft PR title, and description before opening the pull request, or when grading an eval package bundle.
---

# pr-precheck: rubric-driven PR grading

## The question

Answer one question about one PR package: is this pull request ready to submit? A PR package is a candidate pull request, meaning its title, description, commits, diff, and test evidence, read against the plan it claims to implement and the issue that plan belongs to. Grade exactly one package per run. Do not review code style for its own sake, do not judge whether the plan was a good idea, and do not answer any other question; the plan is the yardstick, and the issue and the repo's stated standards are the frame around it.

## Inputs and modes

Run in exactly one of two modes.

Live mode grades the student's own submission before it goes out. Its inputs are:
- the student's `plan.md`, including its Deviations section, from their coursework repo or the path they give;
- the branch's diff, produced by running `git diff main...HEAD` (three dots) from the working copy of their fork with the fix branch checked out, plus `git log main..HEAD --oneline` for the commits;
- the draft PR title and description, from the draft file the student names;
- the test evidence, from the draft description or the evidence file the student names;
- the issue, its thread, the repo's PR template, CONTRIBUTING, and any AI-use policy, fetched from the real repository on GitHub.

A house-chain student reads the house plan and the house repro pack in place of their own plan and repro; the same checks grade the same things. Read the drafts the way a reviewer of the opened PR will: the package is what the title, description, diff, and evidence contain, not other files lying in the working directory.

Eval mode grades a package bundle. The bundle is the whole world: take every fact from the bundle text, fetch nothing, read no other file, and run every check with the full verdict rule.

## The scope seam (live mode only)

In live mode, read `scope.md` before anything else. It names the one repository a PR may target and the house rules of that room; apply those rules when reading evidence, because a house rule changes what counts as compliant there. Refuse to grade a PR package whose pull request targets any other repository. If the scope's repo line still carries an unfilled bracketed placeholder, stop without grading and tell the student to get their cohort's scope file from the instructor; never guess a scope. In eval mode, ignore `scope.md` entirely.

## The voice seam (live mode only)

In live mode, read `voice-guide.md` after the scope and hold the outgoing PR text, the title and the description, against its rules. Report every rule the draft breaks in the summary before the JSON block, quoting the rule's name and the offending phrase. The voice guide never changes the verdict on its own, because voice is the student's personal register and the rubric owns the shared bar. In eval mode, ignore `voice-guide.md` entirely.

## Component reads

Read `rubric.md` for the checks and the verdict rule: it decides what passes. Read `references/evidence-guide.md` as the map of where each evidence family lives in a PR package and what good looks like there. Execute `procedure.md` exactly as written, in its order: it decides how evidence is gathered and how checks run. When the procedure is silent on a step you need, do not improvise around it; take the most literal reading of the rubric, finish the grade, and name the gap in the summary as procedure feedback for the student. If `rubric.md` has no checks written or `procedure.md` has no steps written, refuse to grade and say which file is empty: a tool that invents checks at runtime produces noise that looks like judgment.

## Verdict and output

The verdict is binary: `accept` means the PR is ready to submit, and `reject` means hold it. There is no third verdict and no score; reservations go in check evidence lines. You may show a short readable summary first, one line per check plus any voice-guide notes in live mode. Then end the reply with the fenced JSON block below, valid, with one entry per rubric check in table order, and nothing after it.

```json
{
  "item": "<PR URL or bundle id>",
  "checks": [
    {"name": "<check name>", "grade": "pass|fail|unclear",
     "evidence": "<one line: the fact or quote that decided it>"}
  ],
  "verdict": "accept|reject"
}
```

## Grading discipline

- Grade every check from a named fact or a quote from the package. "Looks fine" is never evidence.
- Grade the artifact, not the polish. A terse PR whose diff matches its plan can be ready, and a beautiful description can sit on top of drift; always compare the diff itself with the plan, never the description's account of the diff.
- Let the rubric decide. When a check passes by its stated condition but feels wrong, it passes; note the tension in the summary.
- Let the procedure decide how. Follow it as written and report its gaps instead of inventing steps.
- Treat `unclear` as the rubric's verdict rule directs. Where the rule is silent, an unverifiable claim fails: a PR that cannot be verified from the package is not ready to submit.