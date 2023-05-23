# A minimal example of a puzzle concerning **Coverage**.

## Summary

I claim this little project illustrates that **Coverage** does _not_ behave
as documented.

## An Executable Model

Start by making this example available locally, that is, retrieve and
exercise it with
```bash
# Assume a working (possibly virtual) environment with, for example,
# Python 3.11 available.
python -m pip install pytest==7.3.1 pytest-cov==4.0.0
git clone https://github.com/claird-ot/coverage_example_1 $WORKING_DIRECTORY
cd $WORKING_DIRECTORY
python -m pytest -s n_test.py --cov \
                 --cov-report term-missing --cov-report=term --cov-report=xml
```

At this point, you'll see something close to
```bash
============================= test session starts ==============================
platform ... -- Python 3.11.3, pytest-7.3.1, pluggy-1.0.0
rootdir: ...
plugins: cov-4.0.0
collected 2 items                                                              

n_test.py See?
.That is all.
.

---------- coverage: platform darwin, python 3.11.3-final-0 ----------
Name        Stmts   Miss  Cover   Missing
-----------------------------------------
n.py            4      1    75%   7
n_test.py       6      0   100%
-----------------------------------------
TOTAL          10      1    90%
Coverage XML written to file coverage.xml

Required test coverage of 90.0% reached. Total coverage: 90.00%

============================== 2 passed in 0.03s ==============================
```

That 75% is deceptive, of course:  we naturally want to exclude
`if __name__ == ...`, so remove comments from
`[pyproject.toml](https://github.com/claird-ot/coverage_example_1/blob/main/pyproject.toml)`
to make `pyproject.toml` look like
```bash
[tool.coverage.report]
# Notice that "fail_under = 95" results in FAILURE, as we want.
fail_under = 90

exclude-also =
    if __name__ == .__main__.:
```

If you _now_ execute
```bash
python -m pytest -s n_test.py --cov \
                 --cov-report term-missing --cov-report=term --cov-report=xml
```

you see `ERROR: .../pyproject.toml: Invalid
value (at line 5, column 15)`, even though the `exclude-also` syntax is taken
directly from
[the current documentation](https://coverage.readthedocs.io/en/latest/excluding.html#advanced-exclusion)
(notice, incidentally, this differs from
[documentation for 7.2.5](https://coverage.readthedocs.io/en/7.2.5/excluding.html#advanced-exclusion),
even though this syntax changed _before_ 7.2.5).


Questions:
- What am I missing?
- Is there a convenient introspection so **Coverage** reports what keywords
it regards as valid in that position?

I've experimented with plenty of variations on `exclude-also`, including
`exclude_also`, `exclude_lines`, and so on.  **Coverage** rejects them all,
in my experience.
