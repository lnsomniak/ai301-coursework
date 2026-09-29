# Evidence guide: where proof lives in a reproduction package

A package has four parts: the issue context (title, body, thread highlights), the repo-facts block, the candidate claim comment, and the candidate repro report. In eval mode the bundle is the whole world. In live mode the issue side comes from the GitHub issue thread and the repo's CONTRIBUTING, AI policy, and issue-template files, and the candidate side is the student's draft files, read only for what they contain and quote.

## Environment

- Where it lives: eval, the repro report's "Environment" line or opening paragraph, compared with the version, platform, and settings named in the issue body, the thread highlights, and the repo facts' "latest release" line. Live, the draft report's environment line, compared with the issue body and the repo's current release or default branch.
- What good looks like: the report names the OS or platform, the version of the software under test, and whatever else the issue or maintainers say changes the failure (a runtime such as Python, a driver, a GUI backend, a build profile, a browser and its language settings). The version is the one the issue targets or newer; if it differs from the issue's, the report says so in its own words.
- Red flags: no environment line at all; an older version than the issue's confirmed target with no comment; a platform-specific issue tested on another platform without saying so; an environment detail the issue flags as decisive left out.

## Steps

- Where it lives: eval, the numbered or inline steps in the repro report, including inline command blocks and the contents of any input files they create. Live, the same in the draft report. The trigger to compare against lives in the issue body's steps and in maintainer comments in the thread highlights that add or correct conditions.
- What good looks like: starting from a clean install, every step is a command, code snippet, file content, or setting change someone else could copy. The steps reach the issue's trigger exactly: same syntax, same flags, same input shape. A control run that removes only the trigger strengthens the package.
- Red flags: "set up the project", "run the tests" with no command; inputs that live in a private repo or an unshared config; a changed operator, swapped syntax, rebound variable, or dropped flag relative to the issue; steps that stop before the trigger.

## Behavior shown

- Where it lives: eval, the fenced output blocks, logs, tracebacks, console captures, and exit codes inside the repro report, plus its "Expected" and "Actual" lines. Live, the same in the draft report. The target behavior lives in the issue body's actual-behavior section and error text.
- What good looks like: put the artifact next to the issue's symptom and they match on the thing that makes it this bug: the same exception type and message, the same wrong output values, the same crash versus graceful exit. For a cannot-reproduce, the artifact is the real output of a genuine attempt at the trigger, and the report says it did not reproduce.
- Red flags: no artifact; an artifact showing only that the program runs; a graceful validation or syntax error standing in for a reported crash or panic; a different error type or exit code than the issue's; "Expected" stated backwards from what the issue asks for.

## Honesty

- Where it lives: every sentence in the claim comment and repro report that asserts something (reproduced, confirmed, verified, root cause, "on all versions", "guaranteed"), each set against the artifacts the package actually shows.
- What good looks like: every assertion points at something visible in the package, and the narration describes what the artifacts show rather than what the writer hoped to see. An honest cannot-reproduce states the result first, names what differed from the reporter's conditions, and offers what a triggering setup might need.
- Red flags: a confident root-cause diagnosis with no transcript; "verified" or "guaranteed reproducible" with nothing shown; generalizing to a release or platform the thread says the maintainers could not reproduce on; narration that calls an artifact a crash when the artifact shows the program still alive.

## Comms

- Where it lives: eval, the candidate claim comment set against the issue title and body, and both comments set against the repo-facts "bug reports" line (template asks) and "contribution policy" line (including any AI policy). Live, the draft claim comment against the issue thread, and both drafts against the repo's CONTRIBUTING, AI policy file, and issue templates.
- What good looks like: the claim comment could only have been written about this issue: it names a specific symptom, version, file, or thread pointer, and gives a next step the writer will actually take, with no promised deadlines. For AI policy, read the policy's scope. If it requires disclosure of all AI use or disclosure in issues and comments, the comments disclose the tool and how much it helped; treat every course package as AI-assisted when deciding whether disclosure is owed. A policy that asks only for PR-level disclosure, human understanding, or human-written comments is satisfied by specific comments in the writer's own voice.
- Red flags: a +1 or me-too; "kindly assign me", "keep this reserved", "fix within 2 days guaranteed"; praise with no content about the bug; a missing disclosure in a repo whose policy requires it for comments.
