# Procedure: how this skill grades a plan package

## Read order

1. Read the repo-facts block first. Write down the contribution policy line word for word, including any AI-use rule, and the bug-report and PR template asks. Nothing later in the package can change these facts, and the ai-policy-met check reads them.
2. Read the issue title and body. Write down the reported behavior in one line: what happens, and what should happen instead.
3. Read the thread highlights. List every entry from an OWNER, MEMBER, COLLABORATOR, or named maintainer that isolates a culprit, posts a patch or test binary, names an approach, links a PR, or asks contributors something. If there are none, write "no maintainer direction". Also note any diagnosis the thread asserts, from anyone, so it can be tested against the evidence in step 4.
4. Read the repro-evidence block before the plan. For the failing run, write down the observed behavior. For every control run, measurement, timing table, or debug output, write one line: what it shows, and which explanations it rules out. Do this before reading the plan so the plan's framing cannot shape what the evidence is taken to mean.
5. Read the candidate plan. Write down, separately: its stated cause, its in-scope and deferred lines, its chosen approach and named files, its test plan, and its risks or unknowns.
6. Read the candidate plan comment last. Write down the approach it states, what it points at as evidence, any date or timeline it promises, and any AI-use disclosure it contains.

In live mode, the same order applies with live sources: repo facts from the repo's docs, the issue and thread from the GitHub issue page, the repro evidence from the student's posted repro comment on that issue, and the plan and comment from the student's draft files.

## Evidence gathering

Each check's evidence comes from the notes above, located per references/evidence-guide.md:

- cause-grounded: the stated cause from step 5, tested against each step-4 line. Record every control or measurement the cause does not predict.
- bounded-scope: the in-scope lines, approach steps, and named files from step 5, set against the one-line reported behavior from step 2. Record every item that is not needed to fix that behavior and is not marked deferred or out of scope.
- executable: the chosen approach and named files from step 5. Record whether a file, module, or component is named, and quote any phrase that leaves the core decision open.
- test-decisive: the test plan from step 5, set against the repro failing run from step 4. Record the observable outcome it names, or quote the generic or subjective wording if it names none.
- thread-engaged: the maintainer-direction list from step 3, set against the plan and the comment from steps 5 and 6. Record for each direction whether it is followed, built on, or named with a reason to depart, or ignored.
- ai-policy-met: the policy line from step 1, set against the disclosure note from step 6.
- comment-specific: the comment's approach and evidence pointer from step 6.
- unknowns-named and no-promised-dates: the risks from step 5 and the timeline note from step 6.

## Check execution

Run the checks in the rubric's table order: cause-grounded, bounded-scope, executable, test-decisive, thread-engaged, ai-policy-met, comment-specific, then the preferred checks. Grade each one by applying its pass condition to the recorded evidence, without re-reading the whole package; go back to a specific part only to quote it. Each check is graded on its own: a fail on one check never changes how another is graded. The evidence line for each check names the deciding fact or quotes the deciding phrase. When a check's evidence is genuinely absent from the package (no cause stated, no test plan, no comment), grade that check fail and say what is missing. Grade unclear only when the evidence is present but the pass condition cannot decide it. When a check passes by its stated condition but something about it seems wrong, it still passes; note the tension in the summary.

## Verdict assembly

Apply the rubric's verdict rule: accept only when every required check is pass. Any required fail or unclear makes the verdict reject, with unclear counted as fail. Preferred checks never change the verdict. In the summary, name the deciding check for a reject and quote its evidence line; for an accept, say that all required checks passed. Emit the JSON block last, with one entry per check in table order.