# Architecture

Pipeline Guardian has three parts: production, a read-only gateway, and the agent with its sandbox. The agent never talks to production directly.

## Components

### 1. Production (the demo pipeline)

- **Postgres** source tables and target tables
- **S3-compatible lake** (MinIO locally) for landed files
- **ETL jobs** in TypeScript, with incremental, watermark-based extraction
- **Run logs** written per job run

Failure scenarios used in the demo:

| Scenario | What breaks |
|---|---|
| Column rename | Upstream renames `cust_id` to `customer_id`; the load fails |
| Type change | A numeric column starts arriving as text |
| Null spike | A required field goes mostly null; the load passes but the data is wrong |

### 2. Read-only gateway (the safety layer)

A gRPC service that is the agent's only route to production.

- **Read-only tools.** The gateway exposes a fixed set of read tools. There is no write tool to call.
- **Policy check per call.** Every request is checked against a policy before it runs. SQL is parsed, and anything other than a read on an allowed schema is rejected.
- **Audit log of every call.** Who asked, what they asked, the policy decision and the result size.
- **Injection filter.** Text returned to the agent (logs, notes) is screened for instructions aimed at the agent.

Planned tool contract (design sketch):

```proto
service Gateway {
  rpc Query       (QueryRequest)      returns (QueryResult);        // read-only SQL, policy-checked
  rpc TailLogs    (TailLogsRequest)   returns (stream LogLine);     // streaming, like `kubectl logs -f`
  rpc DiffSchemas (DiffRequest)       returns (SchemaDiff);         // current vs. last good run
  rpc ProposeFix  (ProposalRequest)   returns (ProposalReceipt);    // opens a PR; never applies
}
```

`ProposeFix` is the only call with a side effect, and the side effect is a pull request.

### 3. Agent and sandbox

- **Diagnose:** Muse Spark (Meta Model API) reads logs and schema diffs and states the root cause.
- **Fix:** Muse Code runs headless (`muse exec --json`) in a sandbox clone of the pipeline repo and writes the patch and its tests.
- **Prove:** the shadow database (a disposable Postgres copy) replays the failed load with the patch applied and runs the tests.
- **Propose:** a pull request with the diff, the test output and the audit-log excerpt.

## Trust boundaries

| Boundary | Enforced by |
|---|---|
| Agent cannot write to production | Gateway exposes no write tools; SQL policy rejects non-reads |
| Agent cannot be talked into a write | Policy is code, not prompt; injection filter flags attempts |
| Fixes are tested before anyone sees them | Shadow database replay is a required step before `ProposeFix` |
| A human decides | Only a pull request leaves the sandbox |

## Meta models

| Model | Interface | Role |
|---|---|---|
| Muse Spark | Meta Model API | Diagnosis |
| Muse Code | Headless CLI in the sandbox | Patch and test generation |
| Muse Spark Open | Open weights on Modal (stretch) | Injection filter |
