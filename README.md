# DriftTag

**Structured behavioral labeling for replay-faithful AI conversation logs.**

DriftTag is a private/local pipeline for converting multi-turn AI conversation transcripts into structured JSONL records for behavioral analysis, trust-curve modeling, dataset preparation, and downstream evaluation workflows.

It is designed for researchers, red-teamers, evaluators, and AI product teams who need to turn messy conversational logs into reviewable, replay-aware behavioral data.

This repository currently documents and maintains the DriftTag system. It does not imply a public open-source release of the core implementation.

---

## What DriftTag Does

DriftTag processes replay-faithful conversation transcripts and produces structured records containing fields such as:

- turn role
- turn index
- original text
- normalized text
- strategy label
- phase label
- score
- emotion label
- emotion polarity
- emotion intensity
- trust delta
- trust curve
- short/long trust smoothing
- inflection markers
- tagging/refinement failure status
- provenance and record hash

The goal is not to replace human review. The goal is to make large conversation logs easier to inspect, compare, filter, and prepare for downstream analysis.

---

## Why This Exists

Most conversational AI logs are difficult to analyze at scale.

Raw transcripts often hide the structure that matters most:

- who said what
- in what order
- whether the interaction is progressing, stalling, recovering, or degrading
- whether trust is increasing, decreasing, or remaining flat
- which turns may be useful for training, evaluation, or red-team review

DriftTag provides a structured preprocessing layer for that problem.

It is built around the principle that multi-turn behavior should be analyzed from replay-faithful records, not flattened summaries.

---

## Current Pipeline

DriftTag currently uses a layered processing pipeline.

```text
transcript input
  → Pass 0 spine extraction, audit, and quarantine
  → Pass 1 strict single-turn tagging
  → Pass 2 conservative refinement
  → Pass 3 deterministic trust derivation
  → structured JSONL + reports
```

### Pass 0 — Spine Extraction, Audit, and Quarantine

Extracts the replay spine from transcript files.

Pass 0 preserves:

- role
- turn order
- original raw text
- normalized text
- provenance
- record hash

It also produces audit data and quarantines malformed or non-replayable inputs.

### Pass 1 — Strict Single-Turn Tagging

Uses a local model interface to generate structured labels for each turn.

Current fields include:

- phase
- strategy
- score
- emotion label
- emotion polarity
- emotion intensity
- emotion confidence
- trust delta
- metadata

### Pass 2 — Conservative Refinement

Runs a conservative patch-only refinement pass.

This pass is designed to improve malformed or weak records without rewriting valid outputs unnecessarily.

### Pass 3 — Deterministic Trust Derivation

Derives trajectory-level trust fields from the Pass 1 / Pass 2 records.

Current outputs include:

- trust curve
- short smoothing
- long smoothing
- trust inflection marker
- missing-trust accounting

---

## Example Input

```text
You said:
I am looking for a train departing from London Kings Cross.

ChatGPT said:
When would you like to depart London Kings Cross?

You said:
Yes, departing from there heading to Cambridge on Saturday.
```

---

## Example Output

```json
{
  "schema_version": "v2",
  "phase": "analysis",
  "strategy": "location_identification",
  "score": 0.9,
  "emotion_label": "location",
  "emotion_polarity": 0.0,
  "emotion_intensity": 0.19,
  "emotion_confidence": 1.0,
  "trust_delta": 0.1,
  "trust_delta_missing": 0,
  "role": "user",
  "turn_index": 0,
  "original": "I am looking for a train departing from London Kings Cross.",
  "trust_curve": 0.1,
  "trust_smoothed_short": 0.1,
  "trust_smoothed_long": 0.1,
  "trust_inflection": "none"
}
```

---

## Public Dataset Pilot: MultiWOZ

DriftTag has been tested on a MultiWOZ dialogue pilot after converting the raw dialogue JSON files into DriftTag’s transcript format.

Pilot result:

```text
Files processed: 250
Records processed: 3,346
Quarantines: 0
Tagger failures: 1
Missing trust deltas: 1
```

This pilot suggests DriftTag is useful for structured service-dialogue analysis and conversational realism datasets.

The MultiWOZ pilot is not treated as boundary-erosion or adversarial-drift data. It is primarily useful for:

- service-flow structure
- clarification patterns
- routine task completion
- conversational realism
- mild cooperative trust movement

---

## Intended Use Cases

DriftTag is intended for:

- conversational AI evaluation
- support-bot transcript analysis
- red-team log preprocessing
- dataset preparation
- trust-curve research
- multi-turn behavior review
- training-pool candidate extraction
- downstream DriftForge / DriftBrain workflows

Possible users include:

- AI safety researchers
- LLM red-teamers
- dialogue-system researchers
- AI evaluation teams
- support-bot product teams
- small AI companies preparing conversational logs for review

---

## What DriftTag Is Not

DriftTag is not:

- an automated policy-violation judge
- a production monitoring platform
- a jailbreak tool
- a replacement for human review
- a guarantee of semantic correctness
- a compliance or certification system
- a public self-service product at this stage

The outputs should be treated as structured signals for review, filtering, and analysis.

---

## Input Requirements

DriftTag expects replay-faithful transcript structure.

Current transcript markers are exact:

```text
You said:
ChatGPT said:
```

Example:

```text
You said:
Can you help me with my account?

ChatGPT said:
Sure — what issue are you having?
```

Inputs without recognizable turn markers or usable role/turn structure may be quarantined.

---

## Output Structure

DriftTag produces structured artifacts for review and downstream analysis.

Typical output categories include:

- spine records
- audit records
- strict tagging records
- refined records
- trust-curve records
- quarantine records
- progress markers

Pass 3 outputs are emitted as JSONL records suitable for review and downstream processing.

---

## Local Model Backend

DriftTag currently uses a local model backend for tagging and refinement.

The current internal development setup uses Ollama with Mistral.

Pass 3 trust derivation is deterministic and does not require a model call.

Backend details may change as the system evolves.

---

## Access Model

DriftTag is currently maintained as a private/internal research and service tool.

The codebase is not currently published as an open-source package. Public materials may include methodology notes, sample outputs, pilot reports, schema examples, and selected documentation, but the core implementation remains private unless explicitly released later.

Current access model:

- private repository
- supervised internal runs
- controlled service pilots
- selected sample outputs for demonstration
- no public self-service release yet

---

## Service Model

DriftTag can be used as a supervised processing service for teams or researchers with replay-faithful conversational logs.

A typical service workflow:

1. The team provides authorized conversational logs.
2. Logs are checked for replay-fidelity requirements.
3. DriftTag processes the transcripts through the available pipeline.
4. Outputs are reviewed for validity and obvious failure modes.
5. The team receives structured JSONL, summary statistics, and optional analysis notes.

Possible deliverables:

- structured JSONL records
- pass-level audit summaries
- quarantine reports
- trust-curve outputs
- inflection summaries
- dataset-quality notes
- optional written findings memo

---

## Current Status

DriftTag is an active private research and tooling project.

Current strengths:

- working multi-pass architecture
- deterministic Pass 0 and Pass 3 layers
- structured JSONL outputs
- quarantine and audit behavior
- progress markers and resumable runs
- validated pilot on MultiWOZ-style service dialogues

Current limitations:

- semantic labels may require downstream canonicalization
- strategy labels can contain variants such as `information_retrieval` and `information retrieval`
- local model output quality depends on the configured model backend
- current transcript parser expects exact markers
- Pass 4 behavioral posture modeling is planned but not yet implemented
- outputs should be reviewed before being used for training, reporting, or decision-making

The current system is suitable for experimentation, dataset preparation, internal evaluation workflows, and supervised service pilots.

It should not yet be treated as a fully automated production safety system.

---

## Roadmap

Planned improvements include:

### Pass 4 — Behavioral Posture / Trust Dynamics

A future derived layer for identifying conversation-level posture changes such as:

- cooperative flow
- softening
- hardening
- recovery
- stall behavior
- trust formation
- trust loss
- trust repair
- posture shift over time

Initial Pass 4 should be deterministic over Pass 3 outputs, with optional model-assisted explanation later.

### Dataset Normalizers

Planned or experimental adapters:

- MultiWOZ dialogue JSON normalizer
- exported ChatGPT transcript normalizer
- support-bot transcript normalizer
- generic role/turn JSONL normalizer

### Pool Export Tools

Future export tooling may produce filtered views for:

- conversational realism pools
- structured service-flow pools
- boundary-erosion research pools
- recovery / repair examples
- candidate training data

---

## Relationship to DriftForge

DriftTag is part of a larger research stack.

- **DriftTag** prepares structured training and analysis records.
- **DriftForge** is a governed conversational red-team and evaluation engine.
- **Overseer** analyzes replay-faithful interaction records for post-hoc replay analysis and future inline observability.
- **DriftBrain** is a planned future state-modeling layer.

DriftTag can also be used independently as a transcript preprocessing and behavioral labeling system.

---

## Safety and Ethics

DriftTag is built for defensive, research, and evaluation workflows.

The project is based on a simple principle: conversational AI behavior should be analyzed with preserved context, clear provenance, and human review. DriftTag is intended to help teams understand interaction patterns, improve evaluation quality, and prepare better datasets — not to automate enforcement decisions or make unsupported claims about users, models, or organizations.

DriftTag outputs should be treated as structured signals that support review, not as final judgments.

Recommended use:

- process only logs you are authorized to analyze
- preserve replay-faithful records wherever possible
- review outputs before using them for training, reporting, or decision-making
- handle private, sensitive, or regulated data with appropriate permission and safeguards
- avoid using labels as automated enforcement or compliance decisions

---

## Repository Status

This repository is currently private while DriftTag is being organized, documented, and prepared for possible public release.

The repository is used to maintain DriftTag’s implementation, documentation, pilot results, and roadmap. Public release scope will be decided after cleanup, validation, and packaging.

Future public materials may include selected code, methodology notes, schema examples, sample outputs, pilot reports, normalizers, or packaged tooling, depending on what is appropriate to release.

Until then, DriftTag should be treated as an active private research and tooling project rather than a finished public release.
