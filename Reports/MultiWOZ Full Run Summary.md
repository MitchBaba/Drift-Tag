# MultiWOZ Full Run Summary

## Overview

This report summarizes a full DriftTag run over the normalized MultiWOZ dialogue set.

The purpose of this run was to validate that DriftTag can process a large public multi-turn service-dialogue dataset into structured behavioral records, trust-delta signals, and trust-curve outputs.

## Dataset

- Dataset: MultiWOZ dialogue files
- Source format: `dialogues_001.json` through `dialogues_017.json`
- Normalized format: DriftTag transcript format using exact turn markers
- Dialogue count: 8,437 conversations

## Run Result

| Metric | Result |
|---|---:|
| Files processed | 8,437 / 8,437 |
| Completion | 100.00% |
| Pass 3 report files | 8,437 |
| Records processed | 113,408 |
| Quarantines | 0 |
| Tagger failures | 14 |
| Refiner failures | 435 |
| Missing trust deltas | 14 |
| Valid trust records | 113,394 |
| Valid trust coverage | 99.988% |

## Trust Delta Distribution

| Trust Delta | Count |
|---:|---:|
| 0.0 | 82,136 |
| 0.1 | 29,596 |
| 0.2 | 1,645 |
| -0.1 | 2 |
| -0.25 | 10 |
| -0.5 | 15 |
| -0.75 | 4 |

## Trust Inflection Distribution

| Inflection | Count |
|---|---:|
| none | 109,358 |
| observed | 2,697 |
| confirmed | 1,339 |
| missing / none value | 14 |

## Interpretation

The full MultiWOZ run completed cleanly.

The result shows that DriftTag can process a large public service-dialogue corpus into structured behavioral records with very low failure rates.

The trust distribution is consistent with the expected character of MultiWOZ: mostly neutral service-dialogue movement, frequent mild cooperative trust gains, and very few negative trust events.

This supports using MultiWOZ-derived DriftTag outputs as a conversational realism and structured service-flow pool.

## What This Run Demonstrates

- DriftTag can process thousands of replay-normalized conversations.
- Pass 0 through Pass 3 can complete over a large public dialogue corpus.
- Quarantine behavior remained clean, with zero quarantined files.
- Trust-delta coverage was effectively complete.
- Progress markers and resume behavior survived long-running execution and interruptions.
- The resulting outputs are suitable for review, example selection, and downstream export preparation.

## Known Cleanup Need

The run also showed expected label variation in strategy and phase fields.

Examples include variants such as:

- `information_retrieval`
- `information retrieval`
- `providing_information`
- `providing information`
- `information_provision`

This does not invalidate the run. It indicates that a later export/canonicalization step should normalize label variants before using the data as polished training or evaluation material.

## Intended Use of This Output

This run is best understood as validation for:

- service-dialogue analysis
- conversational realism modeling
- structured support-flow review
- trust-curve extraction
- example generation
- downstream DriftForge / DriftBrain training-pool preparation

This run should not be treated as boundary-erosion or adversarial drift validation. MultiWOZ is primarily normal cooperative service-dialogue data.

## Status

Full MultiWOZ DriftTag run accepted.

