# Update Test Badge

Run the JAX upstream test suite against the MPS backend and update the pass-rate badge in README.md.

## Step 1: Run the JAX Test Suite

Run the test suite script with `--keep` to reuse any existing clone, redirecting output to a timestamped log file under `/tmp` so prior runs are preserved for comparison:

```
LOG=/tmp/jax_tests_$(date +%Y%m%d_%H%M%S).log
uv run python scripts/run_jax_tests.py --keep > "$LOG" 2>&1
echo "Log: $LOG"
```

This runs all upstream JAX tests (except float64-only tests) against the MPS backend. It takes a long time — wait for it to complete. Do NOT pipe to `tail`: that discards earlier output you may need to introspect (failures, tracebacks, collection counts).

## Step 2: Extract the Results

Read the summary block from the log (e.g. `tail -50 "$LOG"` once the run is done, or `grep -E "passed|failed|error|collected" "$LOG"`). Note:
- **Passed**: number of passing tests
- **Available**: collected - skipped - xfailed (the denominator)
- **Pass rate**: passed / available as a percentage

## Step 3: Update the README Badge

The badge is on line 3 of `README.md` and looks like:

```
![JAX tests](https://img.shields.io/badge/JAX_tests-XX.X%25_passing-COLOR)
```

Update the percentage to match the new pass rate, rounded to one decimal place. Do not change the badge color.

Only edit the badge if the percentage has actually changed. If it already matches, report that no update is needed.

## Step 4: Report

Provide a summary to the user with:
- Total collected, skipped, available, passed, failed, errors
- The new pass rate
- Wall-clock runtime of the test suite (pytest prints this on its summary line, e.g. `... in 5086.08s (1:24:46)`. Grep for it: `grep -E "in [0-9.]+s" "$LOG" | tail -1`).
- Whether the badge was updated or already correct
