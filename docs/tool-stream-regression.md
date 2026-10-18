# Offline tool-stream regression check

`src/agent/run.test.ts` checks the history contributed by a multi-step turn.
The adjacent `tool-stream-regression.test.ts` adds the real installed provider's
HTTP/SSE parser to that check. Run it before and after an SDK lockfile update:

```sh
npx vitest run src/agent/run.test.ts src/agent/tool-stream-regression.test.ts
```

The complete native and guarded controls both return two full 12,000-character
tool results under their original IDs and retain them in step history. The
guarded duplicate-ID and malformed-sibling cases must release no callbacks and
make no continuation request. Every response is authored and every tool is an
in-memory fixture; this is not a recorded provider incident or a test of the
whole `runTurn` application path.

The optional `psj-ai-sdk` preview is a development dependency only. Production
providers, permission prompts, KB operations and the browser bundle are not
changed. Its first adapter buffers a whole response, delaying first output,
and excludes input hooks, approval continuation and `prepareStep`. That is why
this patch proposes an offline regression check rather than replacing the
application's provider-neutral agent loop.

The preview is maintained by this patch's author. Running it in the project,
finding a useful invariant, or deciding that the native SDK already covers the
needed cases are all useful evaluation outcomes. An operator-run test is not
independent adoption or evidence of a recurring production incident.
