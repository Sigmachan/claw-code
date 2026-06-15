# claw-code

A fast, local-first **Claude Code–style terminal agent**, written in Rust. Talks to
any OpenAI- or Anthropic-compatible endpoint, so it drives a local model on your own
GPU just as happily as the hosted Claude API — with a polished TUI and a built-in
tool loop (bash, read/write/edit, glob/grep, web search, MCP).

> Downstream of [`instructkr/claw-code`](https://github.com/instructkr/claw-code).
> This build adds a Claude-Code-style interface and a 3-endpoint launcher tuned for a
> local vLLM rig.

---

## What this build adds

- **Claude-Code-style TUI** — rounded welcome card, `⏺` tool-call dots, `⎿` result
  tree, dim per-turn status line. Keeps a fsociety-green accent instead of going full
  monochrome.
- **`claw-kira.sh`** — one launcher, three endpoints (see below).
- **Persona profile** support via `--profile` (TOML mixed into the system prompt).

## Endpoints (`claw-kira.sh`)

| Command   | Endpoint | Model |
|-----------|----------|-------|
| `claw`    | local vLLM (`127.0.0.1:8080/v1`) | `gemma4-31b-abliterated` |
| `clawc`   | Claude Code subscription (reuses your `claude login` OAuth) | `claude-opus-4-8` |
| `claw --claude` | Anthropic API (`ANTHROPIC_API_KEY`) | `claude-opus-4-8` |

```bash
claw                       # interactive REPL on the local model
claw "summarize ./src"     # one-shot prompt, local model
clawc "review this diff"   # same, on Claude via your subscription OAuth
```

No prompt → interactive REPL on whichever endpoint you picked.

## Build

```bash
cd rust
cargo build --release -p rusty-claude-cli   # binary: target/release/claw
# (claw-kira.sh prefers target/debug/claw, falls back to release)
```

Run the test suite:

```bash
cargo test -p rusty-claude-cli
```

## Layout

```
rust/
  crates/
    rusty-claude-cli/   # the CLI binary `claw` — REPL, rendering, tool display
    runtime/            # session, conversation loop, config, permissions, compaction
    api/                # OpenAI/Anthropic HTTP client + SSE streaming
    commands/           # slash command metadata/parsing
    tools/              # built-in tool implementations
    plugins/            # MCP + plugin host
    telemetry/          # metrics
claw-kira.sh            # 3-endpoint launcher
```

## Credits

Built on [`instructkr/claw-code`](https://github.com/instructkr/claw-code). All
upstream authors retain their copyright. This is a personal fork — non-commercial.
