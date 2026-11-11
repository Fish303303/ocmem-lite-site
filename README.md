# OCMem

**Token-cheap long-term memory for [OpenCode](https://opencode.ai).**
Local-first, no embedding model, no API keys — about **200 tokens per request** instead of 1–2k.

[简体中文](./README.zh-CN.md) · [Live site](https://fish303303.github.io/ocmem-site/) · [English site](https://fish303303.github.io/ocmem-site/en.html)

```
opencode plugin add github:Fish303303/ocmem
```

---

## Why another memory plugin?

Most agent-memory plugins inject a pile of retrieved context on every turn, or ship a 500 MB
embedding model that has to be downloaded before the agent can remember your name.

OCMem takes the opposite bet: **memory should be small, cheap and boring.**

- **Rules, not models.** Auto-capture runs locally on your message with regex, so capturing costs **0 tokens**.
- **Small, fixed digest.** Up to six facts of the current topic are written into `AGENTS.md` as a
  managed block, so they ride along with the system prompt — no extra tool call, no vector search.
- **Topic groups.** Only the current topic's facts are expanded; the rest are shown as counts
  (`other topics: deploy 3`) in one line.
- **On-demand search.** A single `memory` tool (`add` / `search` / `list` / `forget`) for everything
  that is not worth injecting.

| | OCMem | Typical memory plugin |
| --- | --- | --- |
| Fixed cost per request | ~200 tokens (measured) | 1–2k (estimated) |
| Vector model | none | 100–500 MB |
| External API | none | often |
| Storage | local JSON | SQLite / cloud |
| Setup | one command | download model, configure key |

> The 1–2k figure for other plugins is an estimate based on their documentation and injection
> format — it is not a benchmark. OCMem's own number is measured; see below.

---

## Measured cost

Method: two fresh `opencode run` sessions with an identical prompt, one with the OCMem digest block
in `AGENTS.md` and one without; prompt tokens read from OpenCode's session database.

| Run | Prompt tokens |
| --- | --- |
| with digest | 6,406 |
| without digest | 6,316 |
| **delta** | **90** |

The digest block was 169 characters at the time. The `memory` tool schema is always registered
(~100 tokens), so total per-request overhead is roughly **200 tokens**. In long sessions the prefix
is cached (`cache.read`), so the billed cost is lower than the nominal size.

Reproduce it yourself:

```bash
# terminal 1 — with OCMem installed
opencode run "list files in this folder"

# terminal 2 — in a folder without the digest block
opencode run "list files in this folder"
```

Then compare prompt tokens in OpenCode's session database.

---

## What it does

1. **Auto-capture** — sentences that look like durable preferences (`I prefer…`, `from now on…`,
   `don't…`, `default to…`) are saved automatically. Questions, one-off commands and test chatter
   are filtered out.
2. **Dedupe** — near-duplicates are merged using character-bigram Jaccard ≥ 0.75.
3. **Digest** — up to 6 facts of the *current topic* (coding / computer / work / life) plus a
   one-line index of the rest, written to `AGENTS.md` inside
   `<!-- ocmem:begin --> … <!-- ocmem:end -->`.
4. **Short-term recall** — the last few non-question messages from previous sessions are kept under
   “last talked about”, so a fresh session can continue where you left off.
5. **Retrieval tool** — `memory` with `search` (bigram + recency + usage scoring), `list`, `forget`.

---

## Install

Requirements: Node.js 18+ and OpenCode.

```bash
opencode plugin add github:Fish303303/ocmem
```

That is the whole setup. Restart OpenCode and the plugin is active.

---

## Usage

Just talk. Facts are captured from normal conversation:

```
You: I prefer pnpm over npm, and don't add new dependencies without asking.
You: remember: my timezone is Asia/Shanghai.
```

Explicit control at any time:

```bash
opencode
memory list
memory search deploy
memory forget <id>
memory add "prefer short answers on mobile"
```

The digest is regenerated automatically and appears in `AGENTS.md`:

```md
<!-- ocmem:begin -->
## Memory (auto-managed)
- Prefers pnpm over npm
- Don't add dependencies without asking
- Timezone: Asia/Shanghai
other topics: deploy 3
<!-- ocmem:end -->
```

---

## Storage

| What | Where |
| --- | --- |
| Facts | `~/.local/share/ocmem/memories.json` |
| Recent messages | `~/.local/share/ocmem/recent.json` |
| Digest | managed block in `~/.config/opencode/AGENTS.md` |

Everything is plain JSON on your disk. Nothing leaves your machine, and you can delete the whole
memory by removing the block or the files.

---

## Limits

- Capture runs on a small model, so unusually worded facts can still be missed. Say `remember …` to
  force a save, or call the `memory` tool.
- No semantic search (by design). Chinese and English both work through character bigrams.
- The digest is capped at 6 facts per topic; older facts stay searchable but are not injected.
- No multi-agent sync: each machine has its own store.

---

## FAQ

**Does it work with Chinese?**
Yes. Retrieval and dedupe run on character bigrams, which handle Chinese without tokenisation.

**Will it fight with my existing `AGENTS.md`?**
No. OCMem only writes inside its own `<!-- ocmem:begin -->` block and leaves the rest untouched.

**How do I turn it off?**
Remove the plugin (`opencode plugin remove opencode-ocmem`) and delete the managed block.

**Is my data uploaded anywhere?**
No network calls are made by the plugin at all.

---

## Roadmap

- [ ] Per-project memory scopes
- [ ] Export / import of the memory store
- [ ] Optional “pin” flag so a fact never expires from the digest
- [ ] Adapters for other coding agents

---

## Development

```bash
git clone https://github.com/Fish303303/ocmem.git
cd ocmem
npm install
npm run build
```

The plugin entry is `index.ts`; the store is plain JSON, so tests can run without network access.

---

## Website

This repository is the **website** for the project:

| File | Purpose |
| --- | --- |
| `index.html` | Chinese landing page |
| `en.html` | English landing page |
| `.nojekyll` | Skip Jekyll processing |
| `README.zh-CN.md` | Chinese version of this document |

Published with GitHub Pages from `main` / root:
https://fish303303.github.io/ocmem-site/

---

## License

MIT
