# HANIA

Official implementation of:

**HANIA: Input-Adaptive Planner-Guided Multimodal Graph Retrieval for Evidence-Grounded Question Answering**

HANIA is a planner-guided framework for evidence-grounded multimodal question answering. It constructs a question-aware graph from the supplied image and text, coordinates descriptive and relational evidence through a finite-state planner, and applies coverage-aware pruning before answer prediction with a frozen language model.

The code is available at:  
https://github.com/Zafar-southeast/HANIA

## Framework

HANIA contains four main stages:

1. **Grounded graph construction** from visual and textual evidence.
2. **Finite-state evidence planning** over descriptive and relational requirements.
3. **Coverage-aware evidence pruning** based on relevance, confidence, diversity, and redundancy.
4. **Structured answer prediction** using a frozen instruction-tuned decoder.

The framework preserves provenance between selected evidence and its original source. It uses no external knowledge retrieval, iterative retrieval, decoder modification, or target-dataset fine-tuning.

## Architecture

```text
Question + Options + Image/Text
                |
                v
Grounded Multimodal Graph
                |
                v
Descriptive–Relational FSM Planner
                |
                v
Evidence Scoring and Coverage-Aware Pruning
                |
                v
Structured Evidence Prompt
                |
                v
Frozen Decoder
                |
                v
Predicted Answer
```

## Main Models

- **Qwen3-VL-Instruct:** visual evidence extraction
- **GLiREL:** textual relation extraction
- **Frozen instruction-tuned model:** finite-state planning
- **Qwen3-8B-Instruct:** multiple-choice answer prediction

## Installation

```bash
git clone https://github.com/Zafar-southeast/HANIA.git
cd HANIA

conda create -n hania python=3.11
conda activate hania

pip install -r requirements.txt
```

## Dataset

HANIA is evaluated on the official **ScienceQA** dataset.

Download ScienceQA from its official source and place it under:

```text
data/scienceqa/
```

The repository does not redistribute the dataset. Each instance uses only its supplied:

- question;
- answer options;
- image, when available;
- textual context, when available.

## Configuration

Set the model and dataset paths in:

```text
configs/scienceqa.yaml
```

Example:

```yaml
models:
  vision_model: "Qwen3-VL-Instruct"
  relation_model: "GLiREL"
  planner_model: "MODEL_NAME_OR_PATH"
  answer_model: "Qwen3-8B-Instruct"

data:
  scienceqa_path: "data/scienceqa"

output:
  output_dir: "outputs/hania"

parameters:
  evidence_budget: 5
  planner_temperature: 0.0
```

## Run HANIA

```bash
python scripts/run_hania.py \
  --config configs/scienceqa.yaml \
  --split test
```

## Run Flat-RAG

Flat-RAG uses the same candidate evidence and frozen answer decoder but excludes finite-state planning and coverage-aware pruning.

```bash
python scripts/run_flat_rag.py \
  --config configs/scienceqa.yaml \
  --split test
```

## Evaluation

```bash
python scripts/evaluate.py \
  --predictions outputs/hania/predictions.json
```

The evaluation reports:

- answer accuracy;
- evidence precision;
- evidence recall;
- evidence F1;
- mean inference latency.

## Results

Results on the ScienceQA test set:

| Method | Accuracy | Precision | Recall | F1 |
|---|---:|---:|---:|---:|
| Qwen3-VL Direct | 85.40 | – | – | – |
| Flat-RAG | 86.90 | 65.30 | 59.80 | 62.43 |
| **HANIA** | **87.80** | **68.57** | **62.31** | **65.29** |

All values are percentages.

### Evidence-Budget Analysis

| Budget | Accuracy | Precision | Recall | F1 | Latency |
|---:|---:|---:|---:|---:|---:|
| 1 | 82.60 | 75.40 | 43.80 | 55.40 | 1.72 s |
| 3 | 86.10 | 71.20 | 57.40 | 63.56 | 2.18 s |
| 5 | **87.80** | 68.57 | 62.31 | 65.29 | 2.64 s |
| 7 | 86.70 | 64.10 | 67.20 | **65.61** | 3.09 s |
| 9 | 85.30 | 59.80 | 70.40 | 64.68 | 3.51 s |

The default evidence budget is \(k=5\).

## Hardware

The reported experiments were conducted on a single NVIDIA A100 40 GB GPU. All pretrained components remained frozen.

## Repository Structure

```text
HANIA/
├── configs/
├── data/
├── prompts/
├── scripts/
├── src/
├── outputs/
├── requirements.txt
├── LICENSE
└── README.md
```

## Limitations

The current evaluation is limited to ScienceQA. Future work will include additional multimodal benchmarks, detailed component ablations, improved visual relation extraction, and deeper faithfulness analysis.

## Citation

```bibtex
@inproceedings{ali2026hania,
  title={HANIA: Input-Adaptive Planner-Guided Multimodal Graph Retrieval
         for Evidence-Grounded Question Answering},
  author={Ali, Zafar and Khan, Asad and Thierry, Nimbeshaho and
          Amir, Nabila and Mohammed, Adam A. Q. and Kefalas, Pavlos},
  year={2026}
}
```

## License

This project is released under the MIT License. The pretrained models and ScienceQA dataset remain subject to their respective licenses.

## Contact

For implementation-related questions, please open an issue in this repository.
