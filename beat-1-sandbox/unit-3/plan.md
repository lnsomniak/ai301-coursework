# Plan: issue #69, output parser crashes on a top-level JSON array

**Diagnosis.** `parse_review_output` hands any successfully decoded JSON to `_parse_json_output`, which assumes a dict and calls `data.items()` at `rag/generator/output_parser.py:68`. My reproduction on commit `2f4e82f` shows the array `["First feedback item", "Second feedback item"]` reaching that line as a list and raising `AttributeError: 'list' object has no attribute 'items'`, while the control run with a JSON object parses into 2 sections. The decoding works in both runs; the failure is the dict-only assumption in `_parse_json_output`.

**Scope.** One bounded change in `_parse_json_output`: when the decoded value is a list, build one `FeedbackSection` per item instead of calling `.items()`. The dict path stays exactly as it is. Following the issue body, the change also removes the `@pytest.mark.xfail` marker (manifest H-02) from `test_json_array_fallback`.

Not in scope: the seeded unused `sections` accumulator in `parse_review_output`, which the file marks as course material; any refactor of the dict path; and a top-level JSON scalar such as `"text"` or `42`, which hits the same `.items()` call but is not what this issue reports. I'll mention the scalar case in the PR as a known gap rather than fix it here.

**Files.**
- `rag/generator/output_parser.py`: `_parse_json_output` only.
- `tests/unit/test_output_parser.py`: `test_json_array_fallback` only.

**Approach.**
1. In `_parse_json_output`, before the dict loop, add an `isinstance(data, list)` branch. Each item becomes a section named `item_<index>`. A string or number item becomes its `str()`; a dict or list item becomes its `json.dumps()`. All get confidence 0.85, the same as existing plain-value sections, and empty suggestions.
2. Update the type hint and docstring to say the function accepts a dict or a list.
3. Remove the xfail marker and tighten `test_json_array_fallback` so it asserts what the fix delivers: 2 sections, whose contents are the two input strings.

**Relation to PR #79.** PR #79 also handles the list case, but it moves the dict path into a new helper and adds a branch for other types. My plan touches only the list case, so the dict path's behavior and code stay unchanged. If #79 merges first, I'll rebase and record the outcome under Deviations.

**Test plan.**
- Before: re-run my Unit 2 repro, `pytest -k test_json_array_fallback --runxfail -v` on `main`. It fails with the `AttributeError` at line 68.
- After: on the branch, the same test passes with no xfail marker, and the tightened assertions confirm 2 sections containing "First feedback item" and "Second feedback item".
- Regression: the dict control from my repro still returns 2 sections, and the full `tests/unit/test_output_parser.py` file passes.

**Risks and unknowns.** I don't know which section naming downstream consumers of `FeedbackSection` expect for array output. `item_<index>` is my choice, and I'll flag it for review. Model output that wraps the array in a code fence takes the same path, so the change covers both.

## Deviations

Nothing changed; the plan held. The branch ix/69-json-array-fallback adds the list branch to _parse_json_output with item_<index> sections, removes the H-02 xfail marker, and tightens 	est_json_array_fallback to assert the two sections, touching only the two files the plan named. The dict path is byte-for-byte the same, and PR #79 had not merged when I built, so there was nothing to rebase onto.