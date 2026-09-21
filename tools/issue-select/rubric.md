# Rubric: is this a good first issue?

<!--
THIS IS THE PART YOU WRITE. The skill in SKILL.md executes whatever checks
you define here. It ships empty on purpose: the judgment is your work.

A filled rubric must contain:

1. At least one row in the checks table. Each row needs all four columns:
   - Check: a short name (used in the output JSON).
   - Evidence: exactly what to look at, and where. Name the source
     (repo-facts block, issue body, comment thread, or the locations in
     references/evidence-guide.md). "The repo" is not a source; "the last
     5 default-branch commit dates" is.
   - Pass condition: a condition someone else could apply and get your
     answer. Prefer thresholds with numbers ("a maintainer commented
     within 30 days") over adjectives ("maintainer is responsive").
   - Weight: `required` (a fail here rejects the issue) or `preferred`
     (never changes the verdict; a nice-to-have that helps rank the
     issues you accept).

2. A verdict rule below the table: how the check grades combine into
   accept or reject, including how `unclear` is treated. The verdict
   space is binary. If you write no rule for `unclear`, the skill treats
   it as fail.

Cover what actually kills first contributions. The lecture named four
families: the maintainer is alive, the repo is in use, the scope fits a
newcomer, and nobody else is already on it. A rubric that ignores a family
will fail eval issues designed around that family.
-->

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| Repo alive | Repo facts: last push to any branch, archived flag, latest release | Not archived, and last push to any branch within 90 days (from the bundle's capture date in eval mode, from today in live mode) | required |
| Maintainer responsive | Repo facts: maintainer first-response sample; commit authorship; issue thread comments and their author_association | At least one signal that a human maintainer is actually present: a reply tagged Owner, Member, or Collaborator anywhere in the sampled evidence, human (non-bot) authorship on recent commits, or the issue itself filed by a maintainer. No fixed response-time window; a quiet but living repo still passes | preferred |
| Nobody already on it | Assignees; linked PRs and their state; claim comments in the thread | No assignee is set, no open linked PR references this issue, and either there is no claim comment, or every claim comment present is more than a few months old with no follow-up since, or was explicitly answered by a maintainer freeing the issue back up (including inviting others to take it over). A single old, unanswered, or maintainer-released claim does not block; only a current, live, unaddressed claim does | required |
| Scope fits a newcomer | Issue body and thread; repo facts' linked PRs and their state; the thread's comment count and time span | The issue names a single coherent problem or goal. That stays true even if the fix could touch several files, or a maintainer offers a few named candidate causes or approaches to pick from; naming options is not the same as leaving the scope open. A suggested approach that sounds technically meaty on its own, such as threading, multiprocessing, or a specific algorithmic change, is not by itself evidence of an architecture rework; implementing one of several such named options still counts as one bounded fix, and the newcomer is not expected to implement every suggestion listed, only to pick one that addresses a named cause. Fail when: the thread shows genuine unresolved disagreement about which approach is right, with no maintainer decision; a maintainer states the fix touches core internals; the issue is a pure support question; or the linked-PR history shows one or more closed, unmerged attempts at this same issue, which is evidence the real scope is harder than the label suggests regardless of how many stars or helpful labels it carries. Judge the actual thread content and PR history over the issue's label | required |
| Contribution policy allows AI-assisted work | Repo facts: contribution policy line; CONTRIBUTING.md / AI_POLICY.md if present | No explicit statement banning AI-generated or AI-assisted contributions. Disclosure, personal-understanding, testing, or human-review requirements are conditions to follow, not failures. No stated policy passes | required |
| Good-first-issue signal | Issue labels | Issue carries a "good first issue" label or clear equivalent | preferred |

## Verdict rule

Accept only if every required check grades pass. Unclear on a required check counts as fail: if the evidence isn't in the bundle, or can't be found live, that's a reason to pass on the issue rather than a coin flip. Preferred checks are reported but never change the verdict; use them only to rank among accepted issues in live mode.