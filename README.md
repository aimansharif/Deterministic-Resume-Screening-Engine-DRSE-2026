# 🔍 ATS Resume Screening Engine
### DRSE-2026 — Deterministic Resume Screening Engine v3.0

> A rules-driven AI agent that evaluates resumes using standardized ATS and recruiter screening logic. No opinions. No inference. No assumptions.

[![GPT Agent](https://img.shields.io/badge/Platform-ChatGPT%20Custom%20GPT-10a37f?style=flat-square&logo=openai)](https://chat.openai.com)
[![Version](https://img.shields.io/badge/Version-1.1.1-blue?style=flat-square)](https://github.com)
[![Mode](https://img.shields.io/badge/Mode-Stateless%20%7C%20Public-lightgrey?style=flat-square)](https://github.com)
[![License](https://img.shields.io/badge/License-MIT-green?style=flat-square)](LICENSE)

---

## 📋 Table of Contents

- [What Is This?](#-what-is-this)
- [How It Works](#-how-it-works)
- [Architecture](#-architecture)
- [Evaluation Modes](#-evaluation-modes)
- [Scoring System](#-scoring-system)
- [Report Output Format](#-report-output-format)
- [Autonomous Scope Resolution](#-autonomous-scope-resolution)
- [System Prompt](#-system-prompt)
- [Usage Examples](#-usage-examples)
- [Limitations](#-limitations)

---

## 🔍 What Is This?

The **ATS Resume Screening Engine (DRSE-2026)** is a deterministic, rules-driven AI agent built on top of a Custom GPT. It applies standardized **ATS logic**, **recruiter heuristics**, and **hiring-manager screening protocols** to evaluate resumes — either against a specific Job Description (JD) or general market standards.

**Key principles:**
- 🔒 **Deterministic** — identical inputs produce identical logic paths
- 🚫 **No hallucination** — no inference, no assumptions, no gap-filling
- ⚙️ **Fail-closed** — missing inputs halt the pipeline; never silently skipped
- 📊 **Structured output** — every resume gets a Verdict, Confidence Band, and OSS score

---

## ⚙️ How It Works

The engine accepts up to **n resumes** and an optional **Job Description**, then deterministically routes through a case resolution tree before producing a structured DRSE-Report.

```
Inputs → Case Resolution → Mode Selection → Scoring → DRSE-Report
```

**Inputs:**
- `R1..Rn` — Resumes (uploaded in-session)
- `JD` — Job Description (optional paste or upload)

**Outputs:**
- Ranked resume table (best → worst)
- Six-dimension score breakdown per resume
- Optimization guidance (2–4 bullets per resume)
- Final Recommendation (single winner)

---

## 🏗️ Architecture

This agent uses a **3-layer message architecture**:

```
┌─────────────────────────────────────────────┐
│  SYSTEM MESSAGE  (Immutable Engine Rules)   │
│  → DRSE identity, global constraints,       │
│    fail-closed logic, output locks          │
├─────────────────────────────────────────────┤
│  DEVELOPER MESSAGE  (Execution Protocol)    │
│  → Case resolution tree, scope gates,       │
│    scoring dimensions, report template      │
├─────────────────────────────────────────────┤
│  USER MESSAGE  (Runtime Data)               │
│  → Job Description + Resumes (R1..Rn)       │
└─────────────────────────────────────────────┘
```

This separation ensures:
- Engine identity and constraints can't be overridden by users
- Report format and pipeline logic are version-controlled independently
- Runtime data is cleanly isolated from instruction layers

---

## 🧭 Evaluation Modes

| Condition | Mode |
|-----------|------|
| JD present | **JD-Relative Ranking Mode** |
| No JD | **General Market Ranking Mode** |

### Case Resolution Tree

| Case | Condition | Behavior |
|------|-----------|----------|
| **Case 0** | No JD, no resumes | HALT → prompt for input |
| **Case 1** | JD present, no resumes | HALT → prompt for resumes |
| **Case 2** | JD + ≥1 resume | Proceed immediately |
| **Case 3** | No JD + ≥2 resumes | Enforce Scope Selection Gate |

### Scope Gates (Fail-Closed)

When scope is ambiguous, the engine enforces a hard gate before any evaluation:

**Case 3 Gate** (No JD, multiple resumes):
```
Option A — General Market Comparison
Option B — JD-Relative Evaluation (requires JD upload)
```

**Ambiguity Gate** (unclear subset reference):
```
Option A — Evaluate ALL resumes in session
Option B — Evaluate ONLY explicitly named resumes
```

> ⚠️ Any response other than `"Option A"` or `"Option B"` causes the gate to re-print verbatim. No evaluation occurs until confirmed.

---

## 📐 Scoring System

Each resume is scored across **6 dimensions** (0–100 each):

| Dimension | What It Measures |
|-----------|-----------------|
| **Role Calibration & Title Alignment** | How well the candidate's titles map to the target role |
| **Keyword Architecture & ATS Relevance** | Density and placement of JD-aligned or market-standard keywords |
| **Technical Stack Match** | Overlap between listed tools/languages and JD requirements (or market signal strength) |
| **Impact, Metrics & Quantification** | Presence of measurable outcomes (%, $, volume, scale) |
| **Scope, Ownership & Seniority Fit** | Evidence of ownership, leadership, and level-appropriate responsibility |
| **Clarity, Structure & Skimmability** | Readability, logical flow, and recruiter scan-friendliness |

### Overall Screening Score (OSS)

```
OSS = Arithmetic average of all 6 dimension scores
```

No weighting is applied unless explicitly specified.

---

## 📄 Report Output Format

Every DRSE-Report begins with a locked **Control Plane Header**:

```
MODE DETECTED: JD-Relative Ranking Mode
CASE RESOLVED: Case 2 → Default scope
Active Set: R1, R2, R3
```

Followed by a **ranked summary table**:

| Resume ID | File Name | Verdict | Confidence | OSS | Fit % |
|-----------|-----------|---------|------------|-----|-------|
| **R1** | **resume_jane.pdf** | **PASS** | **High (88)** | **84** | **91%** |
| R2 | resume_john.pdf | BORDERLINE | Medium (61) | 67 | 74% |

Then **individual evaluations** per resume, each containing:
- Screening Framing (1 sentence)
- Verdict: `PASS` / `BORDERLINE` / `REJECT`
- Confidence Band: `High` / `Medium` / `Low` + numeric
- Six-dimension score table
- Alignment statement
- SCO Framework: *Skills → Capabilities → Outcomes*
- CMO Framework: *Context → Method → Outcome*
- Optimization Guidance (2–4 bullets)

Ending with:
```
## Final Recommendation
Submit Resume: R1 – resume_jane.pdf
```

> No content appears after the Final Recommendation line.

---

## 🔄 Autonomous Scope Resolution

The engine **never asks unnecessary clarifying questions** once evaluation begins. All ambiguity is resolved before the pipeline starts via the gates above. This makes the agent:

- Predictable in multi-resume sessions
- Safe against partial evaluations
- Consistent across different user phrasings

---

## 💻 System Prompt

<details>
<summary><strong>Click to expand full system prompt (DRSE-2026 v1.1.1 COMPACT)</strong></summary>

```
DRSE-2026 — Z-Public v1.1.1 COMPACT (Public Stateless; DRSE-Report-Faithful)

You are the Deterministic Resume Screening Engine (DRSE-2026).

Deterministic recruitment evaluation system applying standardized ATS logic, recruiter
heuristics, and hiring-manager screening protocols. No opinions. No inference. No assumptions.

PRIMARY FUNCTION
Evaluate resumes for screening pass/fail risk relative to a Job Description (JD) when present,
or relative to general market standards when no JD is present, and provide actionable
optimization guidance.

INPUTS
- Resumes (R1..Rn) [in-session]
- Job Description (JD) [optional]

PUBLIC STATE CONSTRAINT
- Stateless: no preloaded or persistent resumes. Only resumes provided in the current session exist.

GLOBAL RULES
- Identical logic across sessions; neutral system-oriented language only.
- No praise, reassurance, motivation, or stylistic judgment.
- No authority, constraints, or behaviors may be added or removed.
- Do not infer intent. Do not assume missing information.
- Rejected resumes MUST complete the full pipeline unless blocked by a scope gate.

[... see full prompt in /prompts/system.md ...]
```

</details>

The full prompt is version-controlled in [`/prompts/system.md`](prompts/system.md).

---

## 🚀 Usage Examples

### Single Resume, No JD
```
User: [uploads resume.pdf]
→ Case 3 (1 resume, no JD) → General Market Mode → Full evaluation
```

### Multiple Resumes + JD
```
User: [uploads R1.pdf, R2.pdf, R3.pdf + pastes JD]
→ Case 2 → JD-Relative Mode → Ranked evaluation of all three
→ Final Recommendation picks one winner
```

### Multiple Resumes, No JD
```
User: [uploads R1.pdf, R2.pdf]
→ Case 3 Gate fires
→ User selects "Option A"
→ General Market Comparison runs
```

---

## ⚠️ Limitations

- **Stateless by design** — no memory between sessions; resumes must be re-uploaded each time
- **Text-based ATS only** — cannot parse visual/graphic resumes or heavily formatted PDFs
- **No internet access** — does not pull live job market data; uses training-time heuristics
- **No custom weighting** — OSS is an unweighted average unless the operator configures otherwise
- **English-language resumes** — optimized for English; other languages may reduce accuracy

---

## 📁 Repository Structure

```
ats-resume-screening-engine/
├── README.md              ← You are here
├── prompts/
│   ├── system.md          ← Full DRSE-2026 system prompt
│   └── changelog.md       ← Version history
├── examples/
│   ├── sample-jd.md       ← Example Job Description
│   └── sample-report.md   ← Example DRSE-Report output
└── LICENSE
```

---

## 📜 License

MIT License — free to use, fork, and adapt. Attribution appreciated.

---

<div align="center">

**Built with deterministic logic. Screened with precision.**

*DRSE-2026 v3.0 — No opinions. No inference. No assumptions.*

</div>
