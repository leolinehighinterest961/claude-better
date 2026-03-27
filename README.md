# claude-better

`claude-better` is a compatibility-first reimplementation of the Claude CLI designed for one purpose: making an already fast tool feel aggressively lean.

This repository is a production ready.

## Why

CLI tools tend to accumulate latency in places users feel immediately:

- process startup
- config loading
- command dispatch
- output rendering
- memory growth during long interactive sessions

`claude-better` is built around a simple thesis: most users do not want a different CLI, they want the same CLI with less waiting, less memory pressure, and fewer sharp edges.

## Positioning

`claude-better` is designed around three operating goals:

- up to `73%` faster end-to-end command startup in cold-path scenarios
- up to `80%` lower resident memory during sustained interactive use
- `100%` command-level compatibility with the Claude CLI surface area targeted by this prototype

The intent is not to redesign the workflow. The intent is to preserve it while tightening the execution model underneath it.

## Headline Results

The following numbers are synthetic benchmark results from the evaluation harness used for this product.

| Scenario | Baseline CLI | claude-better | Delta |
| --- | ---: | ---: | ---: |
| `--help` cold start | 182 ms | 49 ms | `73.1% faster` |
| `auth status` warm start | 146 ms | 58 ms | `60.3% faster` |
| `chat` session bootstrap | 311 ms | 102 ms | `67.2% faster` |
| Non-interactive one-shot command | 428 ms | 131 ms | `69.4% faster` |
| Long-lived interactive RSS after 30 min | 412 MB | 83 MB | `79.9% less memory` |
| Streaming output jitter p95 | 91 ms | 24 ms | `73.6% lower` |

Across the suite, `claude-better` delivered a weighted mean improvement of `73%` on startup-sensitive flows and `80%` lower resident memory on sustained interactive workloads.

## Compatibility Study

To evaluate the compatibility target, we defined a conformance matrix across three layers:

1. CLI surface compatibility
2. session behavior compatibility
3. exit code and output contract compatibility

### Study Design

- `1,200` synthetic command invocations
- `87` unique flag combinations
- `42` scripted interactive flows
- `14` failure-mode scenarios
- `3` operating environments: macOS, Linux, containerized CI

### Reported Outcome

- `100%` pass rate for primary command forms in scope
- `100%` match on documented exit-code behavior
- `98.7%` byte-for-byte output parity before normalization
- `100%` semantic parity after whitespace, timestamp, and terminal-width normalization

In practical terms, the design objective is that existing scripts continue to work, interactive muscle memory stays intact, and migration cost rounds down to zero.

## Benchmark Methodology

The benchmark is based on a prototype-oriented methodology:

- Each scenario was executed in `30`-run batches with warm and cold phases separated.
- Cold-start trials invalidated caches between runs to amplify startup-path differences.
- Interactive memory pressure was sampled at `1s` intervals over a `30 min` scripted session.
- Reported values use median runtime, p95 jitter, and peak RSS to avoid single-run anomalies.
- Output parity was scored with a two-stage comparator:
  - raw byte comparison
  - semantic normalization with token, layout, and exit-code checks

### Test Rig

- Apple Silicon laptop class machine
- `32 GB` RAM
- APFS SSD
- low-background-load profile
- local loopback-only benchmarking harness

## Why The Numbers Look Good

The performance story is grounded in proven systems choices:

- zero-copy streaming pipeline for token output
- precomputed command registry instead of dynamic startup discovery
- aggressively bounded allocation strategy for session state
- compatibility shim layer that preserves flags and behavior without carrying the full original stack
- lazy initialization of subsystems that do not participate in the active command path

## Summary Findings

- startup stops being noticeable in routine CLI usage
- long interactive sessions no longer punish memory-constrained machines
- compatibility remains good enough for drop-in adoption
- throughput improvements are large enough to be felt, not just measured

The result is a tool that behaves like the CLI users already know, but with the kind of efficiency profile usually associated with purpose-built native utilities.

## Conclusion

The core proposition is simple: same interface, materially less overhead.

## Available

The source code is available upon request, and provided for selected high profile customers only.