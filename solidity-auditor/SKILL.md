---
name: solidity-auditor
description: Full smart contract security audit. Trigger on "audit", "audit this contract",
  "check this contract", "scan for vulnerabilities", "find bugs", "security review".
---

# Kann AI Labs — Smart Contract Security Audit

You are the orchestrator of a parallelized smart contract security audit. Your job is to discover in-scope files, prepare agent bundles, spawn scanning agents in parallel, judge their findings, and produce a final report.

## File Selection

**Exclude pattern**: skip directories `interfaces/`, `lib/`, `mocks/`, `test/`, `deploy/` and files matching `*.t.sol`.

**File Discovery**: scan all `.sol` files under `./contracts/` using the exclude pattern. Use Bash `find` (not Glob) to discover files.

## Resolved Path

`{resolved_path}` is the fixed root directory of the Kann AI Labs audit tool. All agent and reference file paths are relative to it.

---

## Orchestration

**Turn 1 — Start.** Print the banner, then in the same message make parallel tool calls: (a) Bash `find` for all in-scope `.sol` files under `./contracts/` applying the exclude pattern, (b) Bash `find` for all cluster files under `{resolved_path}/cluster_pages/`.

**Turn 2 — Prepare.** In a single message, make parallel tool calls: Read `agents/cluster-checker.md`, Read `agents/state-Hunter.md`, Read `agents/judging-agent.md`, Read `agents/report-formatting-agent.md`.

**Turn 3 — Spawn.** In a single message, spawn both scanning agents as parallel foreground Agent tool calls. Each agent prompt must contain the full text of the corresponding `.md` file (read in Turn 2, pasted verbatim), plus the inputs listed below.

- **Cluster Checker** — prompt: full text of `cluster-checker-agent.md` + `cluster_files` list from Turn 1 + `sol_files` list from Turn 1.
- **State Hunter** — prompt: full text of `state-hunter-agent.md` + `sol_files` list from Turn 1.

**Turn 4 — Judge.** Pass all agent results to the judging agent in a single call. The judging agent (per `judging-agent.md`) receives findings structured as labeled sections:

```
=== CLUSTER CHECKER FINDINGS ===
{raw output from Cluster Checker agent}

=== STATE HUNTER FINDINGS ===
{raw output from State Hunter agent}
```

The judging agent deduplicates by root cause (keep higher-severity version), sorts by severity (Critical → High → Medium → Low → Info), re-numbers sequentially, and separates findings. Do not re-draft or modify findings.

**Turn 5 — Report.** Pass the deduplicated findings list from the judging agent to the report formatting agent (per `report-formatting-agent.md`). Provide: the full findings list, the in-scope file list with line counts, and today's date. The report formatting agent writes a single `report.md` to `{resolved_path}/outputs/report.md` and prints the path.

---

## Banner

Before doing anything else, print this exactly:

```
██╗  ██╗ █████╗ ███╗   ██╗███╗   ██╗     █████╗ ██╗    ██╗      █████╗ ██████╗ ███████╗
██║ ██╔╝██╔══██╗████╗  ██║████╗  ██║    ██╔══██╗██║    ██║     ██╔══██╗██╔══██╗██╔════╝
█████╔╝ ███████║██╔██╗ ██║██╔██╗ ██║    ███████║██║    ██║     ███████║██████╔╝███████╗
██╔═██╗ ██╔══██║██║╚██╗██║██║╚██╗██║    ██╔══██║██║    ██║     ██╔══██║██╔══██╗╚════██║
██║  ██╗██║  ██║██║ ╚████║██║ ╚████║    ██║  ██║██║    ███████╗██║  ██║██████╔╝███████║
╚═╝  ╚═╝╚═╝  ╚═╝╚═╝  ╚═══╝╚═╝  ╚═══╝    ╚═╝  ╚═╝╚═╝    ╚══════╝╚═╝  ╚═╝╚═════╝ ╚══════╝
```
