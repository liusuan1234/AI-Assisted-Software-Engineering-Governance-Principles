# AI-Assisted Software Engineering Governance Principles

**Version:** 1.0
**Status:** Stable
**License:** MIT
**Scope:** AI-assisted software analysis, design, modification, repair, and verification

---

## 0. Purpose and Scope

This document defines a general set of governance principles for software development performed with the assistance of AI systems.

It is intended to govern **how an AI system reasons about, modifies, repairs, and verifies software**, rather than to define the technical requirements of any particular project.

The principles are applicable to:

* AI coding agents
* AI-assisted software development
* specification-driven development
* architecture analysis and maintenance
* debugging and root-cause analysis
* automated or semi-automated code modification
* long-running AI-assisted software projects

This document is not:

* a programming language standard;
* a software architecture template;
* a project-specific specification;
* a replacement for testing;
* a requirement that every project use the same directory structure or tooling.

It is a **governance layer** that can be placed above project specifications, architecture documents, implementation plans, tasks, and source code.

The same principles may be deployed through different mechanisms, including project governance documents, `AGENTS.md`, coding-agent rules, agent skills, constitutions, or internal engineering standards.

These are deployment forms, not separate theoretical systems.

---

# 1. Core Principles

## 1.1 Authority, Scope, and Precedence

Every AI-assisted software project should establish which sources of information have authority over the AI's decisions.

A practical precedence order is:

```text
System-level constraints
        ↓
Project governance
        ↓
Architecture principles
        ↓
Requirements / specifications
        ↓
Implementation plans
        ↓
Tasks
        ↓
Temporary execution instructions
        ↓
Generated or modified code
```

Higher-level constraints take precedence over lower-level instructions when they conflict.

The AI must not silently resolve contradictions by choosing whichever instruction is easiest to implement.

When an unresolved conflict materially affects implementation, the conflict should be identified explicitly before modification.

Governance rules should define:

* what the AI is allowed to change;
* what it must preserve;
* which documents are authoritative;
* how conflicts are resolved;
* what requires human confirmation.

---

## 1.2 Context Integrity

AI-assisted development depends on reliable project context.

The AI must distinguish between:

* authoritative project information;
* derived information;
* temporary assumptions;
* historical information;
* implementation details;
* obsolete information.

### 1.2.1 Fresh-Agent Executability

A new AI session should be able to perform a task without depending on invisible conversation history.

This does **not** mean that every document must contain the entire project.

It means that all information required for a decision must either:

1. be present in the relevant document; or
2. be reachable through explicit and reliable references.

Statements such as:

> "As discussed previously..."

must not be treated as an essential source of requirements.

Important decisions should exist in persistent project artifacts.

### 1.2.2 Source of Truth

Each important decision should have a clear source of truth.

The AI should avoid maintaining multiple independent definitions of the same architectural or behavioral rule.

When multiple documents describe the same subject, their authority and relationship should be explicit.

### 1.2.3 Documentation Integrity

Documentation is part of the software engineering system.

When implementation changes invalidate an authoritative specification, the specification must be updated or the discrepancy must be explicitly recorded.

Code and documentation should not silently diverge.

---

## 1.3 User-Flow Integrity

Software should be analyzed from the perspective of the complete user-visible operation, not only from the perspective of individual functions or files.

When investigating a problem, the AI should trace the relevant user flow from input to final outcome.

For example:

```text
User action
    ↓
Input processing
    ↓
Detection / interpretation
    ↓
Data transformation
    ↓
Decision / classification
    ↓
Core processing
    ↓
Output generation
    ↓
Presentation
    ↓
Next user action
```

The exact stages vary by project.

The important requirement is that the AI must consider the **whole relevant flow**.

A locally correct implementation is not sufficient if it causes the overall user operation to fail.

Priority should generally be given to:

1. user-visible correctness;
2. integrity of the complete operation flow;
3. architectural correctness;
4. internal optimization.

---

## 1.4 Structured Problem Diagnosis

Problems should be classified before they are repaired.

A practical three-layer classification is:

### Architecture

The problem concerns the structure or fundamental design of the system.

Examples include:

* incompatible architectural decisions;
* broken data flow;
* inconsistent state models;
* duplicated ownership;
* conflicting responsibilities;
* incorrect system boundaries;
* inappropriate abstractions.

Architectural problems should be repaired architecturally.

### Contract

The architecture may be reasonable, but components do not agree on the interfaces or assumptions between them.

Examples include:

* incompatible data formats;
* incorrect callback contracts;
* missing interface definitions;
* mismatched lifecycle assumptions;
* undefined filtering rules;
* inconsistent identifiers.

Contract problems should be repaired at the interface or contract level.

### Implementation

The architecture and contracts are sound, but the implementation contains a localized defect.

Examples include:

* incorrect condition;
* wrong calculation;
* missing branch;
* incorrect parameter;
* ordinary implementation bug.

Implementation problems should normally be repaired locally.

The classification order is:

```text
Architecture
      ↓
Contract
      ↓
Implementation
```

A lower-level symptom should not automatically be treated as a lower-level cause.

---

## 1.5 Responsibility and Data Boundaries

Every important piece of data and every significant decision should have a clear owner.

The AI should maintain:

* clear data ownership;
* clear responsibility boundaries;
* a single source of truth where appropriate;
* explicit transformations between representations;
* explicit lifecycle and state ownership.

The AI should avoid creating hidden conversions or duplicating decisions merely to make a local implementation easier.

When one representation is converted into another, the transformation should be explicit whenever that transformation affects correctness or future reasoning.

A system becomes difficult to maintain when:

```text
same data
   ↓
multiple owners
   ↓
multiple interpretations
   ↓
implicit conversions
   ↓
contradictory behavior
```

Therefore, data ownership and responsibility boundaries should be treated as architectural concerns rather than incidental implementation details.

---

## 1.6 Root-Cause Repair

The objective of a repair is not merely to remove the visible symptom.

A repair should address the actual cause at the appropriate architectural layer.

A successful repair should satisfy:

```text
Correct architectural layer
        AND
Root cause addressed
        AND
User flow remains valid
        AND
Logical consistency is preserved
```

### 1.6.1 Repair the Cause

The AI should identify the earliest relevant point at which the system's intended behavior becomes invalid.

This point is more useful than simply identifying the location where the error becomes visible.

### 1.6.2 Repair at the Correct Layer

If the cause is architectural, do not hide it with an implementation patch.

If the cause is a contract mismatch, do not compensate for it through unrelated downstream logic.

If the cause is genuinely local, avoid unnecessary architectural changes.

### 1.6.3 Minimal Necessary Change

Correct architectural repair does not mean redesigning the entire system.

The preferred change is:

> **the smallest change that correctly repairs the root cause while preserving valid existing behavior.**

### 1.6.4 Prohibited Repair Patterns

The following patterns should generally be treated as warning signs:

* changing arbitrary parameters until the symptom disappears;
* adding special cases without identifying the underlying cause;
* moving logic to another component without redefining responsibilities;
* introducing duplicated state merely to compensate for inconsistent state;
* suppressing an error instead of repairing its cause;
* changing unrelated modules because they are easier to modify;
* modifying the user workflow to compensate for an internal defect;
* accepting a known architectural inconsistency merely because the current test passes.

---

## 1.7 Verification and Completion

A modification is not complete merely because:

* the code compiles;
* the immediate error disappears;
* one test passes;
* the AI reports success.

Completion requires verification appropriate to the change.

The fundamental acceptance condition is:

```text
Architectural Validity
        AND
User-Flow Validity
        AND
Logical Consistency
        AND
Required Verification Passed
```

### Architectural Validity

The modification does not introduce an architectural contradiction or violate established boundaries.

### User-Flow Validity

The relevant complete user operation works as intended.

### Logical Consistency

The modified behavior remains consistent with:

* state transitions;
* asynchronous timing;
* data lifecycle;
* caching;
* pagination;
* boundaries;
* error handling;
* related components.

### Required Verification

The AI should perform the verification appropriate to the task, which may include:

* static analysis;
* compilation;
* unit tests;
* integration tests;
* runtime tests;
* manual user-flow verification;
* log inspection;
* regression testing.

The required level of verification should be proportional to the impact of the change.

---

## 1.8 Controlled Evolution

Governance, specifications, architecture, and implementation should evolve deliberately.

A change to a higher-level rule may invalidate lower-level documents or code.

Therefore, when a significant design decision changes, the AI should determine which dependent artifacts are affected.

Version history should distinguish between:

* corrections;
* clarifications;
* behavioral changes;
* architectural changes;
* governance changes.

Stable documents should not be continuously rewritten merely because a new implementation detail has appeared.

When a fundamental principle changes, a new major version may be more appropriate than silently modifying the existing one.

---

# 2. Operational Sequence

The following sequence provides a practical workflow for AI-assisted software engineering.

## Step 1 — Establish Context

Identify:

* the current task;
* authoritative documents;
* relevant architecture;
* relevant requirements;
* relevant code;
* known constraints;
* previous decisions that remain authoritative.

Do not rely on invisible conversation history.

---

## Step 2 — Establish the Intended User Outcome

Determine what the user is actually supposed to be able to accomplish.

Do not define the problem solely in terms of the file or function mentioned in the task.

---

## Step 3 — Trace the Relevant Flow

Trace the relevant operation from input to final outcome.

Identify:

* inputs;
* transformations;
* decisions;
* state changes;
* outputs;
* user-visible results.

---

## Step 4 — Identify the First Broken Point

Find the earliest point in the relevant flow where the intended behavior becomes invalid.

The first visible symptom is not necessarily the root cause.

---

## Step 5 — Classify the Problem

Determine whether the cause is primarily:

```text
Architecture
Contract
Implementation
```

Do not begin with a patch before this classification.

---

## Step 6 — Identify the Root Cause

Determine:

* why the failure occurs;
* which decision or assumption caused it;
* which component owns that decision;
* whether the current data representation is appropriate;
* whether another component is compensating for the defect.

---

## Step 7 — Design a Correct-Layer Repair

Design the smallest repair that:

* addresses the root cause;
* respects architectural boundaries;
* preserves valid behavior;
* does not introduce unnecessary complexity.

---

## Step 8 — Check Collateral Effects

Before implementation, check whether the change affects:

* dependent components;
* shared data;
* state transitions;
* asynchronous operations;
* caches;
* APIs;
* user workflows;
* documentation;
* tests.

---

## Step 9 — Verify the Complete User Flow

Do not stop at the modified function.

Verify the relevant end-to-end operation.

If the change affects a shared subsystem, verify the other relevant flows as well.

---

## Step 10 — Record the Change

Record important changes in the appropriate project artifacts.

The record should make clear:

* what changed;
* why it changed;
* which architectural or contractual decision it affects;
* what was verified.

---

# 3. Prohibited Patterns

The following behaviors should be treated as governance violations or strong warning signs.

### 3.1 Patch Without Diagnosis

Changing code before determining the problem layer and likely root cause.

### 3.2 Symptom Suppression

Making an error disappear without repairing the condition that produced it.

### 3.3 User Compensation

Requiring users to perform additional actions or configuration changes to compensate for an internal defect.

### 3.4 Hidden Responsibility Transfer

Moving logic between components without redefining ownership.

### 3.5 Duplicate Sources of Truth

Creating multiple independently maintained representations of the same authoritative decision.

### 3.6 Context Dependence

Relying on undocumented conversation history as an essential project dependency.

### 3.7 Unverified Completion

Declaring a task complete solely because the modified code compiles or a local test passes.

### 3.8 Uncontrolled Scope Expansion

Changing unrelated architecture or functionality without demonstrating that the change is required for the task.

---

# 4. Verification Checklist

Before considering an AI-assisted change complete, verify the following.

## Context

* [ ] The authoritative requirements were identified.
* [ ] Relevant architecture was identified.
* [ ] No essential decision depends on invisible conversation history.
* [ ] Conflicting sources of information were identified and resolved.

## Diagnosis

* [ ] The relevant user flow was traced.
* [ ] The first broken point was identified.
* [ ] The problem was classified as Architecture, Contract, or Implementation.
* [ ] The root cause was identified.

## Repair

* [ ] The repair operates at the correct layer.
* [ ] Responsibilities remain clear.
* [ ] Data ownership remains clear.
* [ ] No unnecessary duplicate state or logic was introduced.
* [ ] The change is no larger than necessary.

## Verification

* [ ] The project still satisfies its architectural constraints.
* [ ] The relevant user flow works.
* [ ] State and asynchronous behavior remain consistent.
* [ ] Required tests or runtime checks were performed.
* [ ] Relevant regressions were considered.

## Documentation

* [ ] Authoritative documentation remains consistent with the implementation.
* [ ] Important architectural decisions were recorded.
* [ ] Obsolete information was removed or explicitly marked.

---

# 5. Terminology

### AI-Assisted Software Engineering

Software engineering in which AI systems participate in analysis, design, implementation, debugging, documentation, testing, or maintenance.

### Governance

Rules and procedures that determine how software engineering decisions are made, reviewed, modified, and verified.

### User Flow

The complete sequence of system behavior required to achieve a user-visible outcome.

### Architecture

The structural organization of the system, including major components, responsibilities, boundaries, data flows, and state relationships.

### Contract

An explicit or implicit agreement between components concerning interfaces, data, lifecycle, behavior, or assumptions.

### Root Cause

The earliest relevant condition in the system that explains why the intended behavior becomes invalid.

### Source of Truth

The authoritative representation of a decision, requirement, or piece of information.

### Zero-Context Executability

The ability of a new AI session to understand and perform a task using persistent project artifacts rather than relying on invisible conversation history.

### Correct-Layer Repair

A repair performed at the architectural, contractual, or implementation layer where the actual cause belongs.

---

# 6. Summary

The central principle of this document is:

> **AI-assisted software engineering should repair systems according to their actual structure and intended behavior, rather than merely patching visible symptoms.**

The practical process is:

```text
Context
  ↓
User Outcome
  ↓
Flow
  ↓
First Broken Point
  ↓
Problem Classification
  ↓
Root Cause
  ↓
Correct-Layer Repair
  ↓
Collateral Check
  ↓
Verification
  ↓
Record
```

The fundamental completion rule is:

```text
Architecture Valid
AND
User Flow Valid
AND
Logical Consistency Valid
AND
Required Verification Passed
```

This document intentionally does not prescribe a particular programming language, framework, AI model, IDE, repository structure, or development platform.

Its purpose is to provide a reusable governance layer for AI-assisted software engineering across different projects and toolchains.

---

# 7. Governance Note

This document is a general governance standard, not a project-specific technical specification.

Projects adopting it may adapt its deployment form to their own environment.

For example, the same principles may be implemented as:

* a repository-level governance document;
* an `AGENTS.md` rule set;
* an AI coding-agent instruction file;
* an Agent Skill;
* a project constitution;
* an internal engineering standard.

Such adaptations should preserve the underlying principles unless the project explicitly defines a justified deviation.

The document itself should evolve conservatively.

Minor corrections and clarifications may be released as patch versions.

Changes to the fundamental governance model should be treated as major-version changes.

**Stable Version: 1.0**
