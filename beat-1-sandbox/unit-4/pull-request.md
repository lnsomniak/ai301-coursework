# Unit 4: Test and Submit

Path: `beat-1-sandbox/unit-4/pull-request.md`

---

## Your pull request

**Pull request**

https://github.com/codepath/pathreview-ai301-fa26-s3/pull/128

**Branch**

fix/69-json-array-fallback

## Eval iterations

**Run history**

1. Full 20-package run: `agreement: 14/20 scored items  (bar: 18/20: below the bar)`, with `clear-accept 1/7  not-tested 4/4  silent-drift 4/4  standards-wall 2/2  unreviewable 3/3`. Every reject agreed; six of the seven clear accepts were rejected.
2. Full 20-package run with `--save-run eval-run.txt`, after revising `diff-clean`, `fix-evidenced`, and `description-truthful`: `agreement: 20/20 scored items  (bar: 18/20: PASS)`, with `clear-accept 7/7  not-tested 4/4  silent-drift 4/4  standards-wall 2/2  unreviewable 3/3`.

**Package analysis**

pkg-02 (Textualize/rich#4208). Gold: accept. Run 1: reject, failing only `diff-clean`. Run 2: accept.

The fix reorders the `save_*` methods so the console is cleared only after a successful write, which moves the existing export calls inside the changed logic. In run 1 my check listed "a pair of `-` and `+` lines with identical content" as a red flag, and the grader applied it literally: "save_html/save_svg hunks pair identical '-' and '+' lines: '-        html = self.export_html(' / '+        html = self.export_html('". Those lines were not churn; they moved because the code around them was restructured. After I revised the check to count identical lines as part of the change when the same hunk restructures the code around them, the run-2 evidence read: "the only repeated lines are in hunks that change clear=False."

**Check rationale**

| diff-clean | Every added and removed line in the diff, and each hunk as a whole (Diff quality family) | Every changed line serves the fix, its test, or a recorded deviation. Identical removed-and-added lines count as part of the change when the same hunk restructures the code around them (lines moved by a reorder, wrapped in a new block, or kept beside changed logic). Fails on debug output left in (active or commented out), commented-out code or earlier attempts, dead functions or allow-unused markers added, TODOs added to untouched code, an import restructure no new import needs, a hunk whose only effect is re-printing, re-indenting, or reformatting lines with unchanged content, or any unrelated hunk folded in. | required |

The first version failed any identical removed-and-added pair, which rejected pkg-02 and pkg-16, two correct fixes whose diffs move lines as part of a reorder. I rejected dropping the identical-line rule altogether, because pkg-18's debris includes a hunk that only re-prints the same `println!` lines with nothing else changed, and that is exactly what this category exists to catch. So the check now separates identical lines carried by a restructuring hunk, which pass, from a hunk whose only effect is re-printing, re-indenting, or reformatting unchanged content, which fails.

**Trade-offs**

The revision loosened `diff-clean`, so the package it could flip was pkg-18, the arguable unreviewable package whose churn is mechanical. It did not flip: run 2 still rejected it on `diff-clean`, with the evidence "the Startup hunk is identical lines re-printed; the imports are restructured with no new import needed beyond IsTerminal." The case I accept this check will miss is churn hidden inside a hunk that also changes real logic, since identical lines there now pass. Nothing else changed elsewhere, and I know because run 2 was a full 20-package run in which every reject from run 1 stayed a reject and every category held its floor.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/pr-precheck/`.