# Model & environment details

Companion artifact to [*When the Agent Confidently Gets Your Dates Wrong*](README.md).

| | |
|---|---|
| **Model** | Claude Sonnet 5 (`claude-sonnet-5`) |
| **Runtime** | Claude Agent SDK, in an agentic "Cowork" configuration with file tools + sandboxed shell + connected MCP servers |
| **Tool involved** | A single-purpose MCP server exposing one tool (`getKudos`-style: takes a username, returns a JSON array of kudos/shoutout records) |
| **Payload size** | 159 JSON records, ~15–20K tokens, ~4 short string/date fields per record |
| **Tool calls made** | 3 sequential calls to the identical tool + arguments, across one conversation |
| **Result across calls** | Byte-for-byte identical on calls 2 and 3 (verified by direct comparison) — the tool was deterministic |
| **System context load** | Current date was present multiple times in context prior to the error: once in an explicit environment metadata block, once in a system reminder, and implicitly in the user's own phrasing ("since May 1st") |

## What we know vs. what's inferred

The account of *why* the error happened in the companion post is a **behavioral inference**, not a confirmed root cause pulled from internal attention weights, logits, or training data. We do not have visibility into the model's internal activations for this specific generation. What we do have, and what the post's claims are actually grounded in:

- The raw tool output (verified stable across 3 calls)
- The model's own transcriptions of that output (verified to diverge from the source on specific fields)
- The point in the 159-record list where divergence began (roughly record 130 of 159 — about 82% of the way through)
- The model's own after-the-fact explanations, which were themselves wrong on the first attempt (claiming tool non-determinism that a follow-up call disproved)

The "long-context attention decay" and "anchoring on the current date" explanations are the most parsimonious fit for this observed behavior, consistent with widely reported "lost in the middle" effects in long-context transformer inference — but they are a diagnosis by symptom, not by instrumentation.

## Reproducibility note

No special decoding parameters (temperature, top-p) were set beyond product defaults. No prompt in the conversation asked the model to summarize, round, approximate, or paraphrase the dates — the request was to filter and reproduce them. The divergence was unprompted.
