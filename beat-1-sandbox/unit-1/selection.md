# Unit 1 — Issue Selection

Path: `beat-1-sandbox/unit-1/selection.md`

Record of the issue carried into Unit 2, and of the evaluation runs that produced
`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in
the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the
wrong label is not graded.

---

## Selected issue

**Issue link**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/69

**Verdict output**

```
All three are in scope (`codepath/pathreview-ai301-fa26-s3`), so this is live mode with the Path Review house rule in effect. Evidence came from the GitHub REST API: repo metadata, each issue's body/labels/assignees, comment threads, issue timelines, the repo's PR list, and `docs/CONTRIBUTING.md`.

`★ Insight ─────────────────────────────────────`
- **Claim signals are environment-specific.** Issues 69 and 68 both carry recent, unanswered "I'd like to work on this" comments (Sept 19 and Sept 20) — normally the single strongest reason to walk away from a first issue. Both claimants have `author_association: NONE`, i.e. classmates, not maintainers, and `scope.md`'s house rule explicitly neutralizes classmate claims here. The rubric text and the scope file do different jobs: the rubric says *what* counts as a live claim, the scope says *how to read claims in this environment*.
- **`@pytest.mark.xfail` is the seeding tell.** Issues 69 and 68 both say "the covering test is marked xfail referencing manifest id H-01/H-02." That means the failing test already exists and encodes the expected behavior — a deliberately planted bug with a ready-made acceptance criterion. That's unusually low-risk scope: you don't have to guess what "fixed" means, and the PR template has a matching checkbox for removing the marker.
- **Repo-level facts grade once, not three times.** Alive (pushed 5 days ago, not archived), maintainer present (`Aburke225`, COLLABORATOR, authored all three issues *and* the last five commits), and policy (189-line `docs/CONTRIBUTING.md` with zero mentions of AI, Copilot, or generated code — silence passes) are identical across all three candidates. Only scope and claims differentiate them.
`─────────────────────────────────────────────────`

**Accepted, in fit order:**

1. **#69 — output parser crashes on top-level JSON array.** Best fit: the bug *is* LLM-output handling — the fallback path for when the model returns a shape you didn't ask for. That's the "LLM as a collaborator you have to negotiate with" work you said you want, and the xfail test gives you a tight iterate-until-green loop. Python, which the repo already uses.
2. **#68 — `ZeroDivisionError` on empty keyword index.** Same clean shape (one named source file, one named test, xfail to remove), but the subject is BM25 retrieval rather than model interaction, so it exercises less of what you're aiming at. One classmate has already posted a detailed investigation plan — harmless under the house rule, but you'd be the second set of eyes on identical ground.
3. **#73 — README vs `.env.example` disagree on the LLM key.** Cleanest and smallest (1–2 hours, zero comments, no claims at all), and `core/config.py` already defines both keys so the correct answer is discoverable. Ranked last only on fit: it's a docs-consistency edit, not iterative technical work.

**Rejected:** none.

Per-check grades, all three graded independently:

| Check | #73 | #69 | #68 |
|---|---|---|---|
| Repo alive (req) | pass | pass | pass |
| Maintainer responsive (pref) | pass | pass | pass |
| Nobody already on it (req) | pass | pass | pass |
| Scope fits a newcomer (req) | pass | pass | pass |
| AI-assisted work allowed (req) | pass | pass | pass |
| Good-first-issue signal (pref) | pass | pass | pass |

```json
[
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s3/issues/69",
    "checks": [
      {"name": "Repo alive", "grade": "pass", "evidence": "archived: false; pushed_at 2026-09-16T21:50:20Z, 5 days before today (2026-09-21)"},
      {"name": "Maintainer responsive", "grade": "pass", "evidence": "Issue filed by Aburke225 with author_association COLLABORATOR, who also authored the last 5 default-branch commits (latest 2026-09-16)"},
      {"name": "Nobody already on it", "grade": "pass", "evidence": "assignees: []; no cross-referenced PRs in the timeline; repo has 0 PRs total; the only claim (Yina-Mu, 2026-09-20) is author_association NONE, a classmate, which the Path Review house rule says does not block"},
      {"name": "Scope fits a newcomer", "grade": "pass", "evidence": "One bounded bug — '.items() on the parsed value raises AttributeError' — with two named files and an existing xfail test (manifest H-02) defining done; no disagreement in the thread, no closed unmerged attempts"},
      {"name": "Contribution policy allows AI-assisted work", "grade": "pass", "evidence": "docs/CONTRIBUTING.md (189 lines) has no match for ai/generated/assisted/copilot/llm/policy/disclose; no AI_POLICY.md in root or .github"},
      {"name": "Good-first-issue signal", "grade": "pass", "evidence": "labels: ['bug', 'good first issue', 'rag', 'tier-1']"}
    ],
    "verdict": "accept"
  },
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s3/issues/68",
    "checks": [
      {"name": "Repo alive", "grade": "pass", "evidence": "archived: false; pushed_at 2026-09-16T21:50:20Z, 5 days before today (2026-09-21)"},
      {"name": "Maintainer responsive", "grade": "pass", "evidence": "Issue filed by Aburke225 with author_association COLLABORATOR, who also authored the last 5 default-branch commits (latest 2026-09-16)"},
      {"name": "Nobody already on it", "grade": "pass", "evidence": "assignees: []; no cross-referenced PRs in the timeline; repo has 0 PRs total; the only claim (acordero4852, 2026-09-19) is author_association NONE, a classmate, which the Path Review house rule says does not block"},
      {"name": "Scope fits a newcomer", "grade": "pass", "evidence": "One bounded bug — 'index([]) raises ZeroDivisionError inside rank-bm25' — with the correct behavior already modeled by search(), two named files, and an xfail test (manifest H-01); no unresolved debate, no prior attempts"},
      {"name": "Contribution policy allows AI-assisted work", "grade": "pass", "evidence": "docs/CONTRIBUTING.md (189 lines) has no match for ai/generated/assisted/copilot/llm/policy/disclose; no AI_POLICY.md in root or .github"},
      {"name": "Good-first-issue signal", "grade": "pass", "evidence": "labels: ['bug', 'good first issue', 'rag', 'tier-1']"}
    ],
    "verdict": "accept"
  },
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s3/issues/73",
    "checks": [
      {"name": "Repo alive", "grade": "pass", "evidence": "archived: false; pushed_at 2026-09-16T21:50:20Z, 5 days before today (2026-09-21)"},
      {"name": "Maintainer responsive", "grade": "pass", "evidence": "Issue filed by Aburke225 with author_association COLLABORATOR, who also authored the last 5 default-branch commits (latest 2026-09-16)"},
      {"name": "Nobody already on it", "grade": "pass", "evidence": "assignees: []; comments: 0; no cross-referenced PRs in the timeline; repo has 0 PRs total"},
      {"name": "Scope fits a newcomer", "grade": "pass", "evidence": "Single coherent goal — 'Make the two files agree' — across README.md and .env.example, with core/config.py cited as the source of truth; estimated effort 1-2 hours"},
      {"name": "Contribution policy allows AI-assisted work", "grade": "pass", "evidence": "docs/CONTRIBUTING.md (189 lines) has no match for ai/generated/assisted/copilot/llm/policy/disclose; no AI_POLICY.md in root or .github"},
      {"name": "Good-first-issue signal", "grade": "pass", "evidence": "labels: ['bug', 'good first issue', 'docs', 'tier-1']"}
    ],
    "verdict": "accept"
  }
]
```
```

**The verdict must record `accept` for this issue.** Choose an issue your own skill
accepts. If your skill rejects every candidate you try, that is a signal about your
rubric rather than about the issues: revise it and re-run — retries are unlimited and a
partial re-run costs about $0.20 — or run the skill on different candidates. Output
recording `reject` for the issue you chose earns no credit for this field.

---

## Eval iterations

Quote source text directly in each field below. Paraphrase does not satisfy them.

**Run history**

1. Original rubric (three checks from the Wednesday calibration exercise: maintainer activity, nobody else on it, scope): **14/20**. `clear-accept` badly under matched at 3/8, driven mainly by an over-strict maintainer-responsiveness requirement and a "nobody on it" check that didn't distinguish a stale, maintainer-released claim from a live one.
2. Revised rubric — demoted Maintainer responsive to preferred, loosened the stale-claim wording on Nobody already on it, first rewrite of Scope: **3/6** on the six issues run 1 had gotten wrong. The maintainer and claim fixes held; the first Scope rewrite fixed none of the three scope-specific misses cleanly and flipped one correct reject into a wrong accept.
3. Scope reworded again, adding linked-PR history as an evidence source so the check could see a pattern of abandoned attempts, not just the thread text: **2/3** on the three Scope disputes.
4. Pulled the model's own per-check evidence for the one still-failing issue with `--out`, found it was reading optional suggested approaches as a required architecture rework, added one clarifying line: **1/1** on that issue, confirmed fixed.
5. Full run against all 20: **18/20**, bar cleared, every category matched at least once. Two different issues were wrong than the ones targeted by the fixes above, a side effect of the Scope rewrites.
6. Same rubric, run again with `--save-run` to produce the committed file: **18/20**, identical misses to run 5. Confirms the result is stable, not a one-off — this is the run in `eval-run.txt`.

**Issue analysis**

`issue-09` (conda/conda#7617, a 2018 feature request for a `conda config --clear` option). Gold label: accept — "old but valid bounded feature; the 2022 claim is stale and the maintainer invited takers." My rubric's result in the submitted run: reject, failing Scope fits a newcomer.

The issue has one closed, unmerged PR attached (conda/conda#11627) from a past abandoned attempt. Scope's current wording treats "the linked-PR history shows one or more closed, unmerged attempts at this same issue" as evidence the real scope is harder than the label suggests. I wrote that clause for `issue-15`, where two closed PRs sat behind three years and ninety-seven comments of contributors claiming the issue and going quiet, a genuine pattern. `issue-09` has exactly one closed PR from years back and nothing resembling that pattern — a maintainer directly invited someone to try it in the thread. My rubric and the gold label are looking at the same fact, a closed PR, and reading it two different ways: gold reads it as unremarkable history, mine reads any closed PR at all as a difficulty signal. The "one or more" threshold doesn't distinguish a single ordinary abandoned attempt from the genuine repeated pattern it was written to catch.

**Check rationale**

From the Scope fits a newcomer check in `rubric.md`: "...or the linked-PR history shows one or more closed, unmerged attempts at this same issue, which is evidence the real scope is harder than the label suggests regardless of how many stars or helpful labels it carries."

I added this after the first full run accepted `issue-15` by mistake — a "good first issue"-labeled Zulip issue sitting on top of two closed PRs and years of contributors claiming it and going silent. The check's evidence column originally pointed only at the issue body and thread, which meant the single strongest signal in that bundle, its linked-PR history, was invisible to the check by construction; nothing told the model to look there. Naming linked-PR history as an explicit evidence source, and treating closed unmerged PRs as a difficulty signal rather than nothing, let the check see what the gold note was actually pointing at.

**Trade-offs**

The same clause that fixed `issue-15` is what now fails `issue-09`. `issue-09` has exactly one old closed PR and nothing else resembling `issue-15`'s pattern — a maintainer directly invited someone to take it — but the check's "one or more" threshold doesn't distinguish a single ordinary abandoned attempt from a genuine multi-year pattern of failed ones. Tightening the threshold, to two or more closed PRs, say, or requiring a corroborating signal like comment count, would likely fix `issue-09`, but I didn't make that change tonight: the rubric was already past the pass bar, and I'd rather make that edit deliberately, with its own recheck against `issue-15` to confirm it doesn't undo the fix that clause exists for, than rush it in unverified. This is a known, named gap, not one I missed.

---

## Selection rationale

Graded on whether all three are answered, in your own words. Not on how good the
reasoning is, and not on length — a short honest answer to each earns the full marks.
This is also the basis for the claim comment you write in Unit 2.

**Selection rationale**

1. Fit: `issue-69` is LLM-output handling — the parser's fallback path for when a model returns a shape the code didn't expect — which is close to exactly what I told the skill I wanted to get better at: working with LLMs as a real collaborator on iterative technical work. The covering test is already marked `xfail` against a specific manifest id, so "done" is a test going green rather than something I have to define myself, which fits the time I actually have tonight.

2. What the verdict caught: two classmates have open, unanswered "I'd like to work on this" comments on `issue-69` and `issue-68`, both from accounts with no maintainer association, and normally an unanswered claim is the single strongest reason to walk away from a first issue. The skill correctly read that as a classmate, not a blocker, under the Path Review house rule, a repo-specific distinction I'd have hesitated on by instinct alone. What I weighed differently: `issue-68` graded identically clean on every required check, but I'm picking `issue-69` because it's the one actually about LLM output rather than search-index math, which the tool only used to rank, never to gate.

3. Anticipated difficulty: low on the technical side — the `xfail` test already defines correct behavior, so there's no ambiguity to resolve before writing code. The real friction is social rather than technical: a classmate posted an interest comment on this one a day before I looked at it, so I'd be claiming something someone else already expressed interest in. Harmless under the house rule, since credit attaches to the PR I open rather than to being first, but worth being upfront about when I claim it.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/issue-select/`.
