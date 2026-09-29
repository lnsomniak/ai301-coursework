# Voice guide: how I talk upstream

## Who I am in threads

<!-- 2-3 lines. Who is talking when you comment on an issue: your
experience level stated plainly, what you are doing in this repo, what
readers can expect from you. This is the register your rules protect. -->

## Rules I write by

### Rule: Say what I ran, not what I think

I only call something reproduced or confirmed when the output that proves it is in the same comment. Anything I believe but have not run is labeled as a guess or left out.

- Wrong: "Confirmed, the parser just doesn't handle arrays."
- Right: "Running `pytest -k test_json_array_fallback` on my fork gives `AttributeError: 'list' object has no attribute 'items'` (full output below)."

### Rule: Name the thing, not the idea of it

Every comment points at something a maintainer can open: a file, a function, a test, a line of output. If a sentence would fit under any issue in the repo, it gets rewritten or cut.

- Wrong: "I'd like to look into the parsing issue here."
- Right: "I'd like to look at `_parse_json_output` in `rag/generator/output_parser.py`, where `.items()` gets called on a value that can be a list."

### Rule: Cut the softeners

"Quite," "rather," "particularly," "oddly," and "seems like" come out of anything I post. If I am unsure, I say what I am unsure about once, plainly, instead of blurring every sentence.

- Wrong: "It seems like the fallback is particularly fragile when the model returns an array, which is rather odd."
- Right: "The fallback breaks when the model returns a top-level array. I have not checked other non-dict JSON values yet."

### Rule: Make the claim or don't

I don't set up a point and then back away from it, and I don't exit an uncertain claim with a joke. Either I stand on the sentence or I delete it.

- Wrong: "I think the fix is just an isinstance check, but honestly I could be totally off lol."
- Right: "My first attempt will be handling the list case before `.items()` is called. I'll confirm against the xfail test before proposing it."

### Rule: No apologizing for being new

Being early in my career is stated once in plain terms if it matters, and never used as a pre-apology for the work. The evidence speaks for itself.

- Wrong: "Sorry if this is a dumb question, I'm still pretty new to this, but I think I got it to break?"
- Right: "Reproduced on Windows 11, Python 3.13.1 (steps and output below)."

## Things I never post

- A date or timeline I cannot guarantee, including "I'll have a PR up by tomorrow."
- "Assign me," "please reserve this," or anything asking others to stay off an issue.
- "Same as above," "+1," or "can confirm" without my own environment, steps, and output.
- The word "reproduced" or "confirmed" in a comment that does not contain the output.
- A root cause stated as fact when all I have is a traceback.
- A joke used to soften a claim I am unsure of.
- Praise for the project in place of content about the bug.