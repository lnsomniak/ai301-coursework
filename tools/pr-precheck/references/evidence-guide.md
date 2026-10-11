# Evidence guide: where evidence lives in a PR package

An eval bundle has five parts: the repo-facts block, the issue with its thread highlights, the plan context, and the candidate PR, whose subsections are Title, Description, Commits, Diff, and Test evidence. In live mode the repo facts come from the repo's PR template, CONTRIBUTING, and AI-policy file; the issue from the GitHub issue page; the plan from the student's `plan.md` with its Deviations section; the diff and commits from `git diff main...HEAD` and `git log main..HEAD --oneline` on the fix branch; and the title, description, and evidence from the student's draft.

## Plan fidelity (harness category: silent-drift)

- Where it lives: the plan context block's in-scope changes, named files, deferrals, and any deviation note, read against the Diff section's file headers (`--- a/` and `+++ b/` lines) and hunks, then against the Description's claims. Live, `plan.md` and its Deviations section against `git diff main...HEAD`.
- What good looks like: list every file and hunk in the diff before reading the description, and label each one with the plan item it implements. A clean match has a label for every hunk and a hunk for every planned item, or a recorded deferral where one is missing. The description then says nothing the diff contradicts. Red flags: a hunk in a file the plan never names, a second hunk quietly rewriting another function, a new option, flag, or setting, a renaming pass, and description lines like "exactly as planned" or "no changes beyond the plan" sitting over such hunks; or a description crediting work, such as docs, that no hunk contains.

## Test evidence (harness category: not-tested)

- Where it lives: the Test evidence section, read against the plan context's test plan and the issue's repro steps, and against which function or branch the diff changes. Live, the draft's evidence block.
- What good looks like: commands with their real output, before and after, on the path that broke. The strongest form is the plan's repro re-run: the failure before, the fix after. If the plan names a second failure mode of the bug, it is re-run or covered by an added test. Controls and suite runs may be stated in a line. Red flags: "tests pass" or "cargo test passes" with no names; "verified working on my machine"; a before/after run on the control or on a request type the diff never touched; a single repro when the plan named two.

## Diff quality (harness category: unreviewable)

- Where it lives: every `+` and `-` line in the Diff section, and the Commits list, where "fmt pass" or "cleanup" commits are a hint to look for churn.
- What good looks like: removing any changed line would break the fix or its test. Red flags: `println!`, `eprintln!`, `console.log`, `print(` debug lines, active or behind `//` or `#`; commented-out first attempts; new functions nothing calls, or `allow(dead_code)`; added TODOs; re-indented blocks; imports reordered or split with no new import needed; a hunk that only re-prints identical lines with nothing else changed. Identical lines moved by a reorder or wrapped in a new block inside a hunk that changes logic are part of the fix.

## Standards and comms (harness category: standards-wall)

- Where it lives: the repo-facts block's pull-request template line, contributing asks, and contribution policy (including any AI-use rule), read against the Description; required-file asks (changelog, whatsnew) read against the Diff. Live, the repo's `.github/PULL_REQUEST_TEMPLATE.md`, CONTRIBUTING, and AI-policy file against the draft description and the diff.
- What good looks like: each template section is present with content specific to this PR, each checklist item is answered, the issue is linked in the form the template asks for, and any required changelog entry is a hunk in the diff. For AI policy, read its scope: a repo requiring disclosure in PRs or of all AI usage needs the tool and the extent named in the description; a repo with no policy needs nothing. Treat every course package as AI-assisted.