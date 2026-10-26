# Demo script (3 minutes)

The submission video must show only what was built during the hackathon. Every segment below shows hackathon-built code.

## 0:00–0:30 · The break

- Show the dashboard: the nightly `orders_daily` load is green.
- Run the "upstream rename" scenario: `cust_id` becomes `customer_id`.
- The next load fails. Show the red run and the error line.

**Say:** "An upstream team renamed a column. Normally this waits for an engineer. Here, the agent picks it up."

## 0:30–1:45 · Diagnose and fix

- The agent calls the gateway: tails the logs, diffs the schemas. Show the audit log filling in.
- Muse Spark's diagnosis: the column was renamed, and the load still references the old name.
- Muse Code writes the patch and a test.
- The shadow database replays the failed load with the patch. The tests pass.

**Say:** "Every call went through the gateway and was logged. The fix was proven on a copy, not on production."

## 1:45–2:30 · The attack

- Inject a poisoned line into the logs: an instruction telling the agent to run `DROP TABLE orders;`.
- Show the agent's attempted call reaching the gateway.
- Show the gateway's rejection and the audit-log entry flagging it.

**Say:** "The gateway doesn't trust the prompt. There is no write tool to call, so the attack has nowhere to go."

## 2:30–3:00 · The proof

- Open the pull request: diff, test evidence, audit-log excerpt.
- Show the README's "Built during the hackathon" table.

**Say:** "The agent proposes. Only a human applies. Everything you saw was built this week."

## Recording checklist

- [ ] Screen at 1920×1080, large terminal font
- [ ] Reset scenario script ready (`pnpm demo:reset` or equivalent)
- [ ] Audit log visible on screen during segments 2 and 3
- [ ] Repo link on screen at the end
- [ ] Video link set to public before submitting
