# Offline tool-stream regression check

`src/agent/run.test.ts` checks the history contributed by a multi-step turn.
The adjacent `tool-stream-regression.test.ts` adds the real installed provider's
HTTP/SSE parser to that check. Run it before and after an SDK lockfile update:

```sh
npx vitest run src/agent/run.test.ts src/agent/tool-stream-regression.test.ts src/agent/tool-stream-recording.test.ts
```

The complete native and guarded controls both return two full 12,000-character
tool results under their original IDs and retain them in step history. The
guarded duplicate-ID and malformed-sibling cases must release no callbacks and
make no continuation request. Every response is authored and every tool is an
in-memory fixture; this is not a recorded provider incident or a test of the
whole `runTurn` application path.

The 0.2 preview also checks text arriving before EOF while tool callbacks wait,
then verifies the original result ID and the final answer. The other two cases
capture a token-limited response and a transport failure into temporary local
directories and replay their wire policy without a key or application tools.
Original bytes and completeness are checked; complete tool JSON followed by a
read error must remain ineligible. The healthy response creates no recording
with the default refused-only mode. All temporary files are removed afterward.

The optional `psj-ai-sdk` preview is a development dependency only. Production
providers, permission prompts, KB operations and the browser bundle are not
changed. It streams a conservative display-only prefix, with later errors still
possible; tools and later bytes wait for EOF and batch validation. Unknown
provider fields can cause buffering. Input hooks, approval continuation and
`prepareStep` remain excluded. That is why
this patch proposes an offline regression check rather than replacing the
application's provider-neutral agent loop.

The preview is maintained by this patch's author. Running it in the project,
finding a useful invariant, or deciding that the native SDK already covers the
needed cases are all useful evaluation outcomes. An operator-run test is not
independent adoption or evidence of a recurring production incident.

To evaluate retaining the dependency, run the command above in your own
checkout and record: installed SDK/provider versions; pass/fail; whether these
checks would stay in your regular SDK-upgrade workflow; and the first blocker
if you would remove them. A later rerun after a real dependency change is
separate evidence of recurring use. No usage data or recordings are uploaded.
The Node file recorder is confined to these tests, not the browser bundle.
Wire-policy replay does not rerun application validators or the SDK loop.
