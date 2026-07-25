# HANIA

Official implementation of:

> **HANIA: Input-Adaptive Planner-Guided Multimodal Graph Retrieval for Evidence-Grounded Question Answering**

HANIA is a lightweight framework for evidence-grounded multimodal question answering. It constructs a question-aware graph directly from the image and textual context supplied with each question, coordinates descriptive and relational evidence through a finite-state planner, and selects a compact evidence package using coverage-aware pruning before answer prediction with a frozen instruction-tuned decoder.

The current implementation is evaluated on **ScienceQA** without target-dataset fine-tuning, external knowledge retrieval, decoder modification, or iterative retrieval.

---

## Overview

Multimodal question answering systems are often affected by noisy, redundant, incomplete, or weakly grounded context. Supplying the full multimodal context to a language model may introduce distracting information and increase unsupported generation.

HANIA addresses this problem through four main stages:

1. **Input-grounded multimodal graph construction**
2. **Finite-state evidence planning**
3. **Planner-guided scoring and coverage-aware pruning**
4. **Structured multiple-choice answer prediction**

The framework preserves provenance links between selected evidence items and their original visual or textual sources.

---

## Architecture

HANIA processes a question \(Q\), answer options \(O\), an optional image \(I\), and optional textual context \(T\).

```text
Question + Options + Image/Text
                │
                ▼
1. Grounded Graph Construction
   ├── Question-relevant visual evidence extraction
   ├── Original textual evidence preservation
   ├── Entity and property extraction
   ├── Rule-based relation construction
   └── GLiREL-based relation extraction
                │
                ▼
2. FSM Evidence Planning
   ├── Descriptive evidence requirements
   ├── Relational evidence requirements
   └── Bounded finite-state plan
                │
                ▼
3. Evidence Scoring and Pruning
   ├── Question relevance
   ├── Graph confidence
   ├── Planner alignment
   ├── Concept coverage
   ├── Modality diversity
   └── Redundancy reduction
                │
                ▼
4. Structured Answer Prediction
   ├── Provenance-preserving evidence prompt
   ├── Frozen Qwen3-8B-Instruct decoder
   └── Multiple-choice answer selection
