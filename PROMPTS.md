# leanrea Prompts — Reverse Engineering with the `/leanrea` skill

A copy-paste prompt pack for [leanrea](https://github.com/aicodeking/leanrea), the
skill-only reverse-engineering helper. Unlike REA, leanrea adds **no MCP server** —
the agent uses the CLI tools it already has (`file`, `otool`, `nm`, `strings`, `rg`,
`asar`, `go version -m`, …). These prompts are adapted from
[REA-Prompts](https://github.com/JoasASantos/REA-Prompts) (MIT) for that
skill-based workflow.

Invoke the skill with `/leanrea` and paste one of the prompts below. Replace
everything in `<angle brackets>` and keep each request to **one feature** — narrow
questions get call chains and addresses; broad ones get shallow maps.

> ⚠️ **Use responsibly.** Only analyze software you own or are explicitly
> authorized to test (your own products, CTFs, bug bounties, interoperability,
> academic study). Bypassing protection of software you don't own, cheating in
> online games, or weaponizing a found vulnerability may violate law and terms of
> service. Every prompt asks the agent to **work on a copy**, **start static**, and
> **label each claim as seen / inferred / unknown** with file/function/offset
> evidence — treat that as non-negotiable.

## Table of contents

| # | Section |
|---|---------|
| 1 | [Getting started](#1-getting-started) |
| 2 | [Feature tracing (any program)](#2-feature-tracing-any-program) |
| 3 | [Native binary exploration](#3-native-binary-exploration) |
| 4 | [Behavioral cloning (clean-room)](#4-behavioral-cloning-clean-room) |
| 5 | [Games (offline / single-player)](#5-games-offline--single-player) |
| 6 | [Firmware & embedded](#6-firmware--embedded) |
| 7 | [Android](#7-android) |
| 8 | [Electron & JavaScript](#8-electron--javascript) |
| 9 | [Vulnerability research (authorized targets)](#9-vulnerability-research-authorized-targets) |
| 10 | [Protecting your own software](#10-protecting-your-own-software) |
| 11 | [Version comparison & crash triage](#11-version-comparison--crash-triage) |
| 12 | [Long-running investigations](#12-long-running-investigations) |

---

## 1. Getting started

### Map an unfamiliar app

```text
/leanrea Give me a map of <program>: file type, main parts, entry points and the
three features worth investigating first. Use file/strings/imports to orient.
Keep it short and cite evidence (file, symbol, offset) for each claim. Work on a
copy and do not execute anything yet.
```

### First feature trace

```text
/leanrea Investigate what happens when I <trigger an action, e.g. click "Export">
in <path to app>. Trace the flow from the entry point to the final effect (file
write / network send / UI change). Name every module or function on the path and
what each does, with evidence. Static analysis only.
```

---

## 2. Feature tracing (any program)

### Explain one feature

```text
/leanrea Investigate how <FEATURE> works in <PATH TO APP>. Identify the entry
point, the functions involved, the data flow, the dependencies and the observable
effects. For every conclusion present evidence: file, function, offset or snippet.
Separate confirmed facts, inferences and unknowns. Do not execute the app.
```

### Recover a rule and rebuild it

```text
/leanrea Find the rule that controls <behaviour> in <program>. State the rule in
plain words, then build a minimal standalone version I can run, and give me one
input that proves it matches the original.
```

---

## 3. Native binary exploration

### Triage an executable

```text
/leanrea Analyze <PATH TO EXECUTABLE>. Identify format and architecture (file,
otool -h / readelf), linked libraries and imports grouped by capability (network,
filesystem, crypto, process, IPC), interesting strings with offsets, and the top
entry-point candidates for <FEATURE>. Flag any signs of packing or obfuscation.
Start with static analysis.
```

### Function deep-dive

```text
/leanrea On <binary>, pick the function that handles <operation> and produce:
pseudocode, its callers and callees, the data structures it reads or writes, and
every string/global it touches. Annotate each finding with the offset that proves
it.
```

### File-format reverse engineering

```text
/leanrea Work out how <program> reads and writes <file type>. Describe the
structure field by field, show where each field is parsed, and write a small
reader that prints a sample file.
```

### Protocol / serialization extraction

```text
/leanrea Recover the wire format <feature> uses for its network or IPC traffic in
<binary>. Show the marshal/unmarshal functions with offsets, list every field
(size, endianness, encoding), and produce a Python struct/proto definition plus one
example message built from the code's constants.
```

---

## 4. Behavioral cloning (clean-room)

> Extract *behavior*, not code. Never copy proprietary source, assets, branding or
> licensing mechanisms.

### Full application spec

```text
/leanrea Study <application> as a black box for a clean-room clone. Produce
CLONE_SPEC.md: every user-visible feature, screen and state machine; inputs,
outputs and edge cases; file formats and protocols; settings/persistence layout;
error behaviors. Cite evidence per requirement; mark anything unconfirmed as
UNKNOWN. Do not copy code or assets — I implement from the spec only.
```

### Clone one feature into my own app

```text
/leanrea Recover the exact behavior of <FEATURE> in <application>: trigger, inputs,
processing steps, outputs, side effects, failure modes. Then implement it from
scratch in <MY LANGUAGE/PROJECT> matching that behavior, with tests derived from the
recovered rules. List every observable difference that remains.
```

---

## 5. Games (offline / single-player)

### Gameplay logic recovery

```text
/leanrea Reconstruct the gameplay logic of <GAME> from its executable in C and test
it against the original. For each mechanic (movement, physics, scoring, spawning),
show the function, the constants and the update loop that prove the rule.
```

### Single-player trainer research (offline only)

```text
/leanrea Locate the code that controls <weapon stats / spawn rates / economy> in
<offline single-player game>. Show the data tables or functions with offsets. I
only want to understand where the values live so I can build my own offline trainer
for personal experimentation — no multiplayer, no persistence tricks.
```

---

## 6. Firmware & embedded

### Firmware image triage

```text
/leanrea Analyze <firmware image / bin>. Identify the container format, extract
partitions/filesystems (SquashFS, JFFS2, UBI, cramfs), map each region to
architecture and load address, locate kernel and rootfs, and list boot entries and
init scripts. Show offsets and entropy observations and cite evidence per claim.
Static only — do not flash or run anything.
```

### Embedded endpoints & update mechanism

```text
/leanrea On <firmware>, recover: hardcoded network endpoints (IPs, DNS, MQTT/HTTP,
ports), the OTA update path (download URL, signature check, flash write), and any
credential material (keys, default passwords, debug tokens). For each finding give
the function/offset and say whether it is confirmed statically or still a
hypothesis.
```

---

## 7. Android

### Audit an APK

```text
/leanrea Analyze <PATH TO APK>. List declared permissions, exported components,
tracking SDKs, domains found and local storage mechanisms. Show the class/method/
file for each finding. Distinguish references present in code from behaviors
actually observed — a domain in a string is not proof it is contacted.
```

### Storage, crypto & secrets review

```text
/leanrea On <APK>, determine how the app stores <data>: SharedPreferences, SQLite,
files, keystore. Recover any embedded crypto (hardcoded keys/IVs, obfuscation
routines) with the class/method that uses them. State whether data is encrypted at
rest and in transit, with evidence, and list concrete risks.
```

---

## 8. Electron & JavaScript

### Electron app anatomy

```text
/leanrea Analyze <PATH TO ELECTRON APP>. Locate the JS and any app.asar (use asar
to extract). Map main vs renderer processes, preload scripts and IPC channels.
Then trace <FEATURE> from the UI to its implementation, including network and
filesystem calls. Show evidence.
```

### IPC & contextIsolation security review

```text
/leanrea On <Electron app>, enumerate every ipcMain handler and the channels
exposed via contextBridge/preload. For each: what arguments it accepts, whether it
reaches shell.openExternal, child_process, fs or webview navigation, and what
validation exists. Flag any handler where renderer-controlled input reaches a
privileged sink without checks, with file/function evidence.
```

### Minified bundle feature tracing

```text
/leanrea Trace <FEATURE> through <app.asar / bundle.js>. Slice the minified file
first, identify module boundaries, recover the relevant functions with readable
names, follow the data flow from UI event to network/filesystem effect, and produce
a call graph. Cite offsets in the bundle for each hop.
```

---

## 9. Vulnerability research (authorized targets)

> Only on software you own or are authorized to test (bug bounty scope, CTF, your
> own product). Report, don't weaponize.

### Trust-boundary audit

```text
/leanrea Review the security of <authorized application or binary>. Prioritize
trust boundaries, untrusted input, privileged operations, credential storage, IPC,
file handling and command execution. For each candidate issue present: input
origin and sensitive operation; the code path and existing checks; preconditions
and impact; evidence and uncertainties; a proposal for controlled validation. Do
not call a hypothesis a confirmed vulnerability without sufficient evidence.
```

### Sink hunt from untrusted input

```text
/leanrea On <binary>, list every place untrusted data enters (sockets, files, IPC,
CLI, clipboard, URL handlers) and follow each source to dangerous sinks (memcpy/
strcpy with unchecked length, system/exec, dynamic loading, deserialization, path
concatenation). For each source→sink path give the function chain with offsets and
state what validation exists in between.
```

### Patch diffing (incomplete-fix hunting)

```text
/leanrea Compare <vulnerable version> and <patched version> of <component>. Diff the
security-relevant functions: what check was added, where else the same pattern
exists without the fix, and whether the fix covers every caller of the vulnerable
routine. Report candidate incomplete-fix locations with offsets, labeled as
hypotheses until validated in a controlled environment.
```

---

## 10. Protecting your own software

> Scope: software **you own**, a vendor bug bounty, a CTF, or academic study — to
> harden it, not to defeat someone else's protection.

### Assess my own product's protections

```text
/leanrea I own <my application> and its protection scheme. Assess it as an attacker
would: locate the license check, integrity checks and anti-debug/anti-VM tricks,
and trace what a failed check does. Report which checks are trivially bypassable,
which are load-bearing, and concrete fixes — each claim backed by file/function/
offset evidence.
```

### Document a license/entitlement flow (interoperability)

```text
/leanrea Document the licensing flow of <application I legitimately own>: where
entitlement is validated, what local and remote checks exist, what state is stored
on disk, and how offline mode behaves. Goal is interoperability and security
review. Report facts with evidence; flag anything you could not confirm.
```

### Keygen-style analysis — document, don't bypass

```text
/leanrea Find the validation routine for <license key / serial> in <binary I own
or a crackme/CTF>. Recover the algorithm (decompile it), identify input encoding,
checksum/transform steps and the success/failure branches with offsets. Write a
test harness that exercises the recovered algorithm against sample inputs. Do not
patch or bypass anything — I want the algorithm documented, not the check removed.
```

---

## 11. Version comparison & crash triage

### Compare two versions

```text
/leanrea Compare <OLD VERSION> and <NEW VERSION> of <application>. Identify
functional changes, API changes, dependency and permission changes, and possible
security fixes. Map each change to functions or files and explain the impact.
Distinguish build changes from behavior changes.
```

### Crash triage to root cause

```text
/leanrea Investigate this crash in <application>. Evidence:
<STACK TRACE, LOGS, DUMP OR REPRO STEPS>
Relate the error to the functions involved, formulate hypotheses and indicate which
evidence supports each. Propose the smallest experiment that distinguishes the
hypotheses and validates the cause.
```

---

## 12. Long-running investigations

### Continue where you left off

```text
/leanrea Review the evidence and notes of this investigation. List what has not
been demonstrated yet, prioritize the next analyses by how much they reduce
uncertainty, and update INVESTIGATION.md with findings, evidence, discarded
hypotheses and next steps.
```

---

## Why these work

1. **One feature per prompt** — narrow questions return call chains and offsets.
2. **Demand evidence** — file/function/offset citations stop confabulation.
3. **Seen / inferred / unknown** — the three-bucket format keeps hypotheses honest.
4. **Static first, on a copy** — matches leanrea's rules; add runtime only when needed.
5. **End with a deliverable** — a SPEC, a reader script, a minimal rebuild forces the agent to verify its own claims.
6. **State the scope out loud** — "software I own", "offline single-player", "document, don't bypass" keeps results usable for research and reporting.

## Credits

Adapted from [REA-Prompts](https://github.com/JoasASantos/REA-Prompts) (MIT) for the
[leanrea](https://github.com/aicodeking/leanrea) skill workflow. License: MIT.
