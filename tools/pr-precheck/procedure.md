# Procedure: how this tool grades a PR package

## Read order

1. Read the repo-facts block first. Copy down word for word the PR template's required sections and checklist, any contributing asks for PRs (issue link form, changelog or whatsnew entry, test command), and the contribution policy line with any AI-use rule.
2. Read the issue title and body, and the thread highlights. Write the reported behavior in one line, and note any maintainer direction the plan cites.
3. Read the plan context. List, separately: each change the plan commits to with its file, each deferral or not-in-scope item, any deviation note, and each repro or failure mode its test plan names.
4. Read the Diff before the description. Build a hunk inventory: one line per hunk with its file and a short note of what it changes. Label each hunk with the plan item from step 3 it implements, as "test" if it adds or changes a test for a planned item, or as "unplanned". Mark separately any line matching a diff-quality red flag from the evidence guide.
5. Read the Commits list. Note any commit whose message names work outside the plan, or a formatting, cleanup, or fmt pass.
6. Read the Test evidence. For each repro named in step 3, note whether it is shown before and after with commands and output, and which code path it exercises.
7. Read the Title and Description last. List each claim about what the PR does or does not do, each template section and checklist item present, the issue link, and any AI-use disclosure.

Reading the diff before the description is the point of this order: a description that claims "exactly as planned" cannot shape the inventory, because the inventory already exists.

In live mode, take the same parts from live sources: the repo's template and policy files on GitHub, the issue page, the student's `plan.md` with Deviations, `git diff main...HEAD` and `git log main..HEAD --oneline` from the fix branch, and the draft title, description, and evidence.

## Evidence gathering

- diff-matches-plan: the step-4 inventory against the step-3 lists. Record every "unplanned" hunk, and every planned change with no hunk that is not recorded as deferred or deviated.
- description-truthful: the step-7 claims against the step-4 inventory and step-3 deferrals. Record any claim the inventory contradicts, and any plan deferral the description does not restate.
- fix-evidenced: the step-6 notes against the step-3 repro list and the paths the step-4 hunks change. Record each named repro as shown before and after, shown on an unchanged path only, or not shown.
- diff-clean: the red-flag marks from step 4 and any churn commit from step 5. Quote each offending line.
- template-met: the step-1 required sections, checklist, issue-link form, and required files against the step-7 description and the step-4 inventory.
- ai-policy-met: the step-1 policy line against the step-7 disclosure note.
- repo-checks-shown and regression-test-added: the step-6 notes and the "test" hunks from step 4.

## Check execution

Run the checks in the rubric's table order: diff-matches-plan, description-truthful, fix-evidenced, diff-clean, template-met, ai-policy-met, then the preferred checks. Grade each from the recorded notes by applying its pass condition; go back into the package only to quote the deciding line. Each check stands alone, so a fail on one never changes another's grade, even when the same hunk matters to both. When a check's evidence is absent from the package (no test evidence, no description), grade it fail and name what is missing. Grade unclear only when evidence exists but the pass condition cannot decide it.

## Verdict assembly

Apply the rubric's verdict rule: accept only when every required check is pass; any required fail or unclear means reject, with unclear counted as fail. Preferred checks never change the verdict. For a reject, name the deciding check in the summary and quote its evidence line; for an accept, state that every required check passed. Emit one JSON entry per check in table order, and end with the JSON block.