# Unit 3: Plan and Build

Path: `beat-1-sandbox/unit-3/plan-and-implement.md`

---

## Posted upstream

**GitHub username**

lnsomniak

**Plan comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/69#issuecomment-5900424892

## Reproduction Report

### Environment

- OS: Windows 11 (build 26200)
- Python: 3.11.9
- pytest: 9.1.1
- Repository: Tommy1070/pathreview-ai301-fa26-s3
- Branch: main
- Commit: `2f4e82f52efbcfcc57d65b3fa5348672163ca088`

### Steps to Reproduce

From the repository root, I installed the development dependencies:

```powershell
python -m pip install -e ".[dev]"
```

I then ran the existing regression test for issue #69 with the expected-failure marker disabled:

```powershell
python -m pytest .\tests\unit\test_output_parser.py::TestOutputParser::test_json_array_fallback -vv --runxfail
```

### Observed Behavior

The test failed with the following traceback:

```text
tests\unit\test_output_parser.py::TestOutputParser::test_json_array_fallback FAILED

________________________________ TestOutputParser.test_json_array_fallback ________________________________

self = <tests.unit.test_output_parser.TestOutputParser object>

    @pytest.mark.xfail(
        strict=True,
        reason="issue #69 (manifest H-02): output parser calls .items() on a JSON array fallback",
    )
    def test_json_array_fallback(self):
        """Test handling of JSON array (not dict)."""
        raw_output = json.dumps(["First feedback item", "Second feedback item"])

>       result = parse_review_output(raw_output)
                 ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

tests\unit\test_output_parser.py:149:
rag\generator\output_parser.py:48: in parse_review_output
    return _parse_json_output(data)

data = ['First feedback item', 'Second feedback item']

    def _parse_json_output(data: dict) -> list[FeedbackSection]:
        sections = []

>       for key, value in data.items():
                          ^^^^^^^^^^
E       AttributeError: 'list' object has no attribute 'items'

rag\generator\output_parser.py:68: AttributeError

FAILED tests/unit/test_output_parser.py::TestOutputParser::test_json_array_fallback - AttributeError: 'list' object has no attribute 'items'
1 failed in 0.52s
```

### Expected Behavior

A valid top-level JSON array should be handled without crashing. The parser should return the array, wrap it in the existing result shape, or otherwise handle it explicitly.

### Conclusion

I reproduced issue #69 on Windows with Python 3.11.9 at commit `2f4e82f52efbcfcc57d65b3fa5348672163ca088`. When `parse_review_output()` receives a valid top-level JSON array, the parsed value reaches `_parse_json_output()` as a list. `_parse_json_output()` then calls `.items()` on that list, resulting in `AttributeError: 'list' object has no attribute 'items'`.

---

## Your branch

**Branch**

fix/69-json-array-fallback

**Evidence**

Before, on `main` at `2f4e82f`, re-running my Unit 2 reproduction:

```
.venv\Scripts\python.exe -m pytest -k test_json_array_fallback --runxfail -v

tests/unit/test_output_parser.py::TestOutputParser::test_json_array_fallback FAILED [100%]
tests\unit\test_output_parser.py:149:
rag\generator\output_parser.py:48: in parse_review_output
E       AttributeError: 'list' object has no attribute 'items'
rag\generator\output_parser.py:68: AttributeError
FAILED tests/unit/test_output_parser.py::TestOutputParser::test_json_array_fallback
================ 1 failed, 427 deselected, 1 warning in 2.99s =================
```

After, on `fix/69-json-array-fallback` at `4adc465`, the same command, with the xfail marker removed and the test asserting both sections:

```
.venv\Scripts\python.exe -m pytest -k test_json_array_fallback --runxfail -v

tests/unit/test_output_parser.py::TestOutputParser::test_json_array_fallback PASSED [100%]
================ 1 passed, 427 deselected, 1 warning in 1.15s =================
```

What the array now parses into, and the dict control from my reproduction, unchanged:

```
.venv\Scripts\python.exe -c "import json; from rag.generator.output_parser import parse_review_output as p; r=p(json.dumps(['First feedback item','Second feedback item'])); print([(s.section_name, s.content) for s in r])"
[('item_0', 'First feedback item'), ('item_1', 'Second feedback item')]

.venv\Scripts\python.exe -c "import json; from rag.generator.output_parser import parse_review_output as p; r=p(json.dumps({'strengths':'Clear README','improvements':'Add tests'})); print(type(r).__name__, len(r), [s.section_name for s in r])"
list 2 ['strengths', 'improvements']

.venv\Scripts\python.exe -m pytest tests/unit/test_output_parser.py -q
19 passed in 0.24s
```

## Eval iterations

**Run history**

1. Full 20-package run with `--save-run eval-run.txt`: `agreement: 20/20 scored items  (bar: 18/20: PASS)`, with every category matched: `clear-accept 7/7  scope-creep 4/4  thread-convention 2/2  unbuildable 3/3  wrong-cause 4/4`.

That was the only run. I used the same Windows setup that worked for Unit 2, with the real `claude.exe` folder first on PATH and `PYTHONUTF8=1`, so no launch attempts failed this time.

**Package analysis**

pkg-14 (zellij-org/zellij#5174). Gold: accept. My rubric: accept, with every required check passing.

This was the package I wrote `executable` around, because its plan does not pin exact functions: it names the files and then says the "exact functions to be pinned in the PR after tracing the query issuance with debug logs." A stricter check demanding function names would have rejected a plan the gold calls ready. My check asks for the file, module, or component plus one chosen approach, and the grader's evidence line shows that reading: "functions to be pinned via debug-log tracing". The plan also defers the Windows variant, and `bounded-scope` passed it because my pass condition treats a stated deferral as good scoping: "Windows variant and theme-cache changes explicitly deferred with reasons".

**Check rationale**

| executable | The plan's approach, named files or modules, and order of work (Executability family) | A stranger could start building today without asking the author anything: the plan names one chosen approach and at least the file, module, or component where the change lands. Exact function names may be pinned later when the plan says how they will be found. Fails when the plan names no files or areas, leaves the core decision open ("X or Y, whichever is easier", "somewhere in the stack", "not sure which layer"), or describes investigation in place of a change. | required |

It reads this way because the unbuildable packages and the clear accepts sit close together on precision. pkg-18 says "recover() 'somewhere'" and "upstream or vendored, whichever is easier," and pkg-17 says "gocui? tcell? not sure," so the check fails a plan that leaves the core decision open. pkg-14, though, names its components and a single approach while leaving function names for later. I rejected a version that required exact functions or line numbers, since it would have failed pkg-14, and settled on "at least the file, module, or component" with function names allowed to come later when the plan says how they will be found.

**Trade-offs**

`executable` accepts that a plan naming only a component can pass even if that component turns out to be the wrong place, so a plan that is specific about where but wrong about why gets through this check. That case belongs to `cause-grounded`, which tests the stated cause against every control run, so I kept the two checks separate rather than making `executable` judge correctness. Nothing changed elsewhere, and I know because there was only one full run: the 20/20 in `eval-run.txt` graded the same `rubric.md`, `evidence-guide.md`, and `procedure.md` I uploaded to `tools/plan-check/`, and the fingerprints in its header match those files.

---

Related paths: `plan.md` and `eval-run.txt` in this directory; your skill's files in
`tools/plan-check/`.