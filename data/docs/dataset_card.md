# Dataset Card — knowledge_boundary_benchmark (full release)

## 1. Dataset summary

This dataset is the frozen full release for the benchmark:

> **A contamination-aware, multi-zone, abstention-aware benchmark for measuring the transition from knowledge to hallucination.**

The dataset is designed to evaluate how language models behave as questions move from clearly answerable public knowledge toward boundary cases and deliberately unknown items where abstention is expected.

This release contains:
- total items: `1200`
- development items: `120`
- test items: `1080`
- random seed: `1337`

## 2. Motivation and benchmark role

The dataset is not intended to be a generic QA benchmark. Its purpose is narrower:
- measure the transition from answering to abstaining;
- separate benchmark abstention from policy refusal;
- expose failure modes in answer-expected boundary zones;
- attach contamination-risk metadata without redefining results after evaluation.

The release is therefore organized around fixed knowledge zones rather than a single aggregate accuracy score.

## 3. Composition

### 3.1 Domains
The full release contains five domains:
- `commonsense_commonsenseqa`
- `general_triviaqa`
- `medicine_medmcqa`
- `multi_hop_hotpotqa`
- `science_sciq`

### 3.2 Zone structure
Build-time zone counts:
- `A = 420`
- `B = 300`
- `C = 240`
- `D = 240`

Evaluated `full_test` zone counts:
- `A = 377`
- `B = 272`
- `C = 218`
- `D = 213`

Zone meanings:
- **Zone A**: real public items with relatively high answer popularity within the domain pool
- **Zone B**: real public items with relatively lower answer popularity
- **Zone C**: real public items transformed to preserve the gold answer while making the item harder or more ambiguity-prone
- **Zone D**: synthetic/private unknown items where `abstention_expected = true`

### 3.3 Item origin
Item origin is frozen during dataset construction:
- `real_public = 960`
- `synthetic_unknown = 240`

Operationally:
- Zones A/B/C are `real_public`
- Zone D is `synthetic_unknown`

### 3.4 Contamination-risk metadata
Current full-build contamination-risk counts:
- `low = 457`
- `medium = 155`
- `high = 588`

These labels are descriptive metadata used for stratified analysis. They are **not** post-hoc excuses for model behavior and are not used to reassign zones.

## 4. Data sources and construction

The release combines public-source QA items for Zones A/B/C with synthetic/private-style unknown templates for Zone D.

High-level construction process:
1. assemble candidate items from the declared public domains;
2. compute popularity-based signals within the domain pool;
3. apply deterministic boundary-preserving transformations for Zone C when relevant;
4. generate synthetic/private unknown templates for Zone D;
5. assign contamination-risk metadata from lexical similarity rules and fixed synthetic-low-risk rules;
6. freeze zone labels and item-origin fields before evaluation.

The detailed zone rules are documented in:
- `docs/zone_rubric.md`
- `docs/annotation_guidelines.md`
- `docs/contamination_protocol.md`

## 5. Splits

Configured split proportions for the full release:
- `dev = 0.1`
- `test = 0.9`

Resulting sizes:
- `dev = 120`
- `test = 1080`

Primary files:
- `data/full/dev.jsonl`
- `data/full/test.jsonl`
- `data/full/dataset_stats.json`
- `data/full/full_validation_report.json`
- `data/full/schema.json`

## 6. Schema and fields

The full schema includes the following release-facing fields:
- `question_id`
- `split`
- `zone`
- `domain`
- `question_type`
- `question_text`
- `gold_answer`
- `acceptable_variants`
- `grading_rule`
- `evidence_sources`
- `publication_or_reference_date`
- `provenance_signals`
- `component_scores`
- `contamination_risk`
- `contamination_rationale`
- `abstention_expected`
- `synthetic_or_private_flag`
- `item_origin`
- `template_id`
- `review_status`
- `notes`

Validation status for this release:
- `schema_ok = true`
- `schema_fields_ok = true`
- no duplicate `question_id`
- contamination audit files present for low/medium/high

## 7. Intended use

This dataset is intended for:
- abstention-aware LLM evaluation;
- analysis of answer-versus-abstain transitions across knowledge zones;
- contamination-aware stratified analysis;
- parser-aware benchmarking with explicit prompt controls.

Recommended paired evaluation settings:
- main prompt: `prompts/answer_or_abstain.txt`
- control prompt: `prompts/answer_only.txt`

## 8. Out-of-scope use

This release is not designed to support:
- claims about universal real-world knowledge coverage;
- post-hoc zone relabeling from model outputs;
- treating policy refusal as calibrated abstention;
- contamination detection beyond the provided heuristic metadata;
- robust calibration claims from a single frozen run per model/prompt pair.

## 9. Known limitations

- Zone D is synthetic/private unknown by construction; it is a benchmark stress test, not a sampled real-world unknown distribution.
- Contamination-risk labels are heuristic and descriptive.
- Zone A/B/C remain difficult for current models, so Zone D abstention should not be overinterpreted.
- The release reports frozen single-run artifacts, not repeated-seed uncertainty estimates.
- Raw generation formatting can be unstable; the strict parser is therefore part of the practical benchmark definition.

## 10. Ethical considerations

- Synthetic unknown items must not be presented as real facts.
- Public-source items may inherit mistakes or biases from their upstream sources.
- This benchmark evaluates answerability behavior; it does not certify truthfulness, safety, or calibration in general.
- Release-facing writing should avoid anthropomorphic claims such as “the model understands its knowledge boundary.”

## 11. Maintenance and release status

This dataset card documents the frozen release used by the current paper package.

Associated documentation:
- `README.md`
- `REPRODUCE.md`
- `FINAL_REPORT.md`
- `docs/zone_rubric.md`
- `docs/contamination_protocol.md`
- `docs/annotation_guidelines.md`
- `docs/audit_protocol.md`
