# Project: Offline AI Code Standardization for C (MISRA C / CERT C)

> **Company:** Independent — built within The Purple Movement community
> **Vertical:** Developer Tooling · Software Quality & Safety-Critical Compliance
> **Type:** Open Source
> **Status:** In Progress

---

## Project Summary

An **offline, privacy-first** assistant that takes C source code and rewrites it to follow established coding standards — **MISRA C:2012**, **SEI CERT C**, and general C best practices — **without changing the program's behavior**. It produces a **before/after compliance score**, a **plain-language change report** (what changed, where, and why), and a **ground-truth diff** so developers can trust and review every edit.

The model — Google's **Gemma (`gemma-4-e4b`)** — runs **locally via LM Studio**, so proprietary source never leaves the developer's machine. It is delivered as a small local web app; the language and target domain are selectable, with **C implemented first** and other languages on the roadmap.

---

## Motivation

Before a codebase is released to a customer or pushed to a shared repository, it usually must comply with coding standards — especially in regulated, safety-critical industries (automotive, aerospace, medical). Today that work is slow and manual, and the cloud AI tools that could help **require uploading source code to external servers**, exposing proprietary IP and creating an unacceptable confidentiality risk for many companies.

This project fills that gap with an **on-premise, offline** model: the privacy of local execution combined with explainable, behavior-preserving standardization. Instead of silently rewriting code (which erodes trust), it explains every change and **reports — rather than silently "fixes" — anything that could alter behavior**.

---

## Goals

- **Offline & private** — all analysis and rewriting run locally; no code leaves the machine.
- **Behavior-preserving** — verification-gated and human-in-the-loop; risky edits (logic fixes, dead-code removal, public renames) are reported, never silently applied.
- **Trust through transparency** — a structured change report (rule ID, reason, risk) plus a before/after compliance score computed by a deterministic analyzer, not by the model.
- **Language & domain aware** — selectable language (C first) and target domain: Generic C, Embedded C, Automotive (MISRA), Security (CERT C), Aerospace, Medical Devices.
- **Legacy modernization** — improve naming, flag dead code, reduce complexity, and add documentation — all while preserving functionality.

---

## Technical Approach

A local pipeline wraps the LLM with deterministic tooling so correctness never depends on the model alone:

1. **Frontend** — a local web app (Flask + vanilla JS); everything is served from `localhost`.
2. **Detection & scoring** — `cppcheck` (with the MISRA C:2012 addon) and `clang-tidy` (CERT checks) produce objective violation lists and a transparent before/after compliance score. *The model never grades its own work.*
3. **LLM proposer** — Gemma (`gemma-4-e4b`) via LM Studio's OpenAI-compatible local API returns a behavior-preserving rewrite as strict JSON (`standardized_code`, `summary`, `changes[]`, `suggestions[]`), each change carrying a rule ID, reason, and risk level.
4. **Ground-truth diff** — computed deterministically (`difflib`) so the change report cannot hide or invent edits; the model only annotates the real diff.
5. **Verification gates (next phase)** — compile the rewrite and run the project's existing test suite; reject any change that breaks compilation or tests; add differential testing for higher assurance.

**Hard prompt guardrails:** never weaken `volatile` / `const` / `static`, never delete suspected dead code, never silently fix logic bugs, never rename externally-linked symbols, never alter `#include`s or introduce dynamic memory / recursion.

**Stack:** Python (Flask + standard library), vanilla HTML/CSS/JS, cppcheck, clang-tidy, Gemma via LM Studio.

---

## Milestones

| Milestone | Description | Target Date |
|---|---|---|
| M1 | Offline MVP: analyze → score → Gemma rewrite → ground-truth diff → explained change report | Jun 2026 (done) |
| M2 | Verification gate: compile + run the project's test suite; block any behavior-breaking change | Jul 2026 |
| M3 | Legacy modernization tiers + differential testing / fuzzing + additional domains and languages | Q3 2026 |

---

## How to Contribute

**Skills Needed:**
- C programming and familiarity with coding standards (MISRA C, CERT C)
- Static analysis tooling (cppcheck, clang-tidy) and rule mapping
- Local LLM usage & prompt engineering (Gemma / LM Studio)
- Python (Flask) backend and lightweight web frontend
- Software testing / CI (for the verification gate)

**Getting Started:**
1. Run an offline Gemma model in LM Studio and start its local server; install `cppcheck`.
2. Set up the app in a Python virtual environment and launch the local web UI.
3. Pick a domain or a specific MISRA/CERT rule, improve its detection/prompt handling, and add a test case under the verification gate.

---

## Current Contributors

| Name | GitHub | Role |
|---|---|---|
| Devu Gopakumar | [@Devugopakumar](https://github.com/Devugopakumar) | Creator & Maintainer |

---

## Related Problem Statements

- Pre-release coding-standard compliance for IT and embedded teams, without exposing proprietary source to cloud services. *(Software Quality & Safety-Critical Compliance vertical.)*

---

## Resources

- MISRA C:2012 Guidelines (licensed standard — rule IDs are referenced; full rule text requires a license)
- SEI CERT C Coding Standard — https://wiki.sei.cmu.edu/confluence/display/c
- cppcheck + MISRA addon — https://cppcheck.sourceforge.io
- clang-tidy (CERT checks) — https://clang.llvm.org/extra/clang-tidy/
- Gemma open models (Google) and LM Studio (offline runtime)

---

*Part of Beyond Borders by The Purple Movement.*
