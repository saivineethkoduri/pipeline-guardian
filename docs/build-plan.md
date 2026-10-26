# Build plan

Building period: Tue Oct 27 – Wed Nov 4, 2026. Submission due **Wed Nov 4, 9:00 PM ET** (8:00 PM CT).

| Day | Date | Goal | Done when |
|---|---|---|---|
| 1 | Tue Oct 27 | Demo pipeline and failure scenarios | Rename, type-change and null-spike scenarios each break a load on demand |
| 2 | Wed Oct 28 | Read-only gateway | Query, TailLogs and DiffSchemas work over gRPC; every call is policy-checked and logged |
| 3–4 | Thu Oct 29 – Fri Oct 30 | Agent loop: detect and diagnose | The agent spots the failed run and Muse Spark names the root cause for all three scenarios |
| 5 | Sat Oct 31 | Fix generation and shadow runner | Muse Code writes a patch; the shadow database replays the load and the tests pass |
| 6 | Sun Nov 1 | Pull request with evidence | `ProposeFix` opens a PR with the diff, test output and audit excerpt |
| 7 | Mon Nov 2 | Injection attack and defense | The poisoned-log attack is blocked and flagged in the audit log |
| 8 | Tue Nov 3 | Dashboard and README | Demo dashboard runs; README lists everything built and how to run it |
| 9 | Wed Nov 4 | Record and submit | 3-minute video recorded, repo public, submission sent before 8:00 PM CT |

## Sessions worth attending (CT)

- Tue Oct 27, 11:00 AM — Agents 101: Build a Multimodal AI Agent
- Wed Oct 28, 11:00 AM — Agents 102: Best Practices for Verifiable Agents
- Fri Oct 30, 11:00 AM — Building on Fireworks with Muse Open Models
- Weekdays 9:00–10:00 AM — Discord office hours (text)

## Cut list (if time runs short)

1. Muse Spark Open injection filter → call Muse Spark through the API instead
2. Null-spike scenario → keep rename and type change only
3. Dashboard → terminal output plus audit-log view
