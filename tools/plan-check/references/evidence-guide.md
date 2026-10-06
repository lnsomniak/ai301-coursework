# Evidence guide: where evidence lives in a plan package

An eval bundle has five parts: the repo-facts block, the issue with its thread highlights, the repro-evidence block, the candidate plan, and the candidate plan comment. In live mode the repo facts come from the repo's README, CONTRIBUTING, AI-policy file, and issue and PR templates; the issue and thread come from the GitHub issue page; the repro evidence is the student's own posted repro comment on that issue; and the plan and comment are the student's draft files, read only for what they contain and quote.

## Diagnosis and grounding

- Where it lives: the plan's "Diagnosis" or cause statement, usually first in the candidate plan, read against the repro-evidence block's steps, control runs, measurements or timing tables, debug output, and its Expected and Actual lines. Maintainer diagnoses live in the thread highlights. Live, the cause is in the draft plan and the evidence is in the student's posted repro comment.
- What good looks like: the cause explains the failing run AND survives every control. Run each control through the stated cause and ask whether the cause predicts that result. A control that still fails without the blamed component, a timing table where the blamed step costs nothing, or a control showing the blamed module working in the same build means the cause is ruled out, however confident the plan sounds or however many thread voices agree with it.

## Scope

- Where it lives: the plan's scope statement, its in-scope and not-in-scope or deferred lines, its approach steps, and its named files, read against the issue title and body.
- What good looks like: one change at the fix site, with everything else either absent or explicitly deferred. Red flags are the words that turn a fix into a campaign: rewrite, migrate, upgrade, unify, restructure, new option, new setting, new UI, retry framework, CI matrix, test-harness migration, "while I'm in there". A plan that knowingly fixes less than everything and says why is well scoped.

## Executability

- Where it lives: the plan's approach steps and its files or modules line.
- What good looks like: a stranger reads it and knows where to open the editor and what to change: one chosen approach and at least the file, module, or component. "Exact functions pinned after tracing with debug logs" is fine when the file or component is named. Red flags: no files at all, alternatives left open ("upstream or vendored, whichever is easier"), "somewhere", "not sure which layer", "investigate", "profile and optimize".

## Test plan

- Where it lives: the plan's "Test plan" line, read against the repro-evidence block's steps and its Expected line.
- What good looks like: an outcome you could watch flip from broken to fixed. The strongest form is the repro re-run with its expected result stated; a regression test encoding the issue's case, a specific assertion, or a measurement with a target also count. Red flags: "run the full test suite", "CI is green", "nothing else should break", "should feel fast", or a test of something other than the reported behavior.

## Honesty

- Where it lives: the plan's Risk, Unknowns, or deferral lines, and any confidence words in the diagnosis ("definitely", "clearly", "guaranteed"). In live mode, after a build, the deviations section of the student's plan.md records what changed between plan and build.
- What good looks like: what the evidence settles is stated plainly, and what it leaves open is named as open. A deviation recorded in the plan with its reason is honest; a confident cause the evidence contradicts is the opposite, and the cause-grounded check catches that.

## Comms

- Where it lives: the candidate plan comment, read against two things. First, the thread highlights: entries from OWNER, MEMBER, COLLABORATOR, or a named maintainer that isolate a culprit, post a patch or test binary, name an approach, link a PR, or ask contributors something. Second, the repo-facts "contribution policy" line, including any AI-use disclosure requirement. Live, the same reads against the GitHub thread and the repo's CONTRIBUTING and AI-policy files.
- What good looks like: the comment states the chosen approach in this issue's terms and points at the writer's own repro; where a maintainer has given direction, the comment visibly builds on it or names it and says why it departs. For AI policy, read the policy's scope: "disclose all AI usage" or disclosure in comments means the comment must name the tool and how it helped; PR-only disclosure, "human-written comments", or no policy are satisfied by specific comments in the writer's own voice. Treat every course package as AI-assisted.