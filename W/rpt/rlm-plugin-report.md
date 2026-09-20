# Oh-My-Pi RLM Plugin — Report 2026-09-20

**Source:** web search (GitHub repos `shift-labs-ai/pi-rlm`, `manojlds/pi-rlm`, `adibirzu/rlm-plugin`; npm `@hicaru/pi-rlm`; docs at ohmypi.xyz).

## What it does
An extension for the Pi coding agent that replaces the fixed tool-set with a **single `execute` tool** running TypeScript in a persistent Bun evaluator. Capabilities arrive as functions (`Bun.$`, subagent spawning `rlm.run()`, persistent namespace, lazy npm imports) rather than separate tool schemas. Example: instead of calling `read` + `bash` + `edit` separately, the agent writes `await Bun.file("foo.ts").text()` inside `execute`.

## Key features (summary)
- Persistent evaluator across turns (variables survive).
- Subagents as a call stack (`rlm.run`).
- One-tool surface (`execute`) vs fixed vocabulary.
- Shell as structured values (`await Bun.$`...`), not parsed text.

## Example invocation
```
pi --rlm
rlm.execute(`const files = await Bun.$"ls -1".text(); console.log(files);`)
```

This collapses read/shell/subagent calls into code in a single persistent cell.
