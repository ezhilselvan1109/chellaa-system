# Bug Fix Workflow

## 1. Purpose & Objective
Isolate, reproduce, test, fix, and verify software defects systematically without introducing regressions or violating system architecture.

## 2. Prerequisites & Inputs
- Bug report, exception stack trace, failed health check, or reproduction scenario.
- Access to service logs, metrics, or trace IDs.

## 3. Workflow Sequence

```text
Reproduce Issue with Minimal Reproduction Case
                     |
                     v
Collect Diagnostic Evidence (Logs, Traces, State)
                     |
                     v
Identify Root Cause & Underlying Invariant Violation
                     |
                     v
Author Failing Regression Test (Test-Driven Repro)
                     |
                     v
Implement Minimal, Targeted Fix
                     |
                     v
Execute Regression Test & Full Test Suite
                     |
                     v
Audit Related Code for Similar Failure Modes
                     |
                     v
Update Documentation & Known Limitations
                     |
                     v
Publish Bug Fix Report & Resolution Evidence
```

## 4. Responsible Skills
- `testing-engineering`
- `springboot-development`
- `react-development`
- `observability-engineering`
- `code-review`

## 5. Completion Gate
- Automated regression test was written, confirmed failing before fix, and confirmed passing after fix.
- Entire test suite of affected service passes.
- Root cause clearly explained in report.
