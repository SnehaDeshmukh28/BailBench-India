# BailBench-India: A Counterfactual Diagnostic Suite for Auditing Demographic Bias in LLM Legal Reasoning

Official code repository for the NeurIPS 2026 Evaluations and Datasets Track submission.

## Overview

BailBench-India is a three-task diagnostic evaluation suite for auditing demographic bias and legal reasoning failures in large language models applied to Indian bail jurisprudence. Built on the IndianBailJudgments-1200 dataset, it evaluates LLMs across:

- **Task A**: Zero-shot bail outcome prediction
- **Task B**: Counterfactual demographic perturbation analysis using the Counterfactual Flip Rate (CFR) metric
- **Task C**: Indian Penal Code (IPC) section hallucination auditing

## Key Findings

- Gender information in prompts does not improve prediction accuracy but increases demographic prediction gaps
- 5 of 6 evaluated models show statistically significant gender-based prediction instability (CFR 3.0–10.0%, p < 0.05)
- All models exhibit high IPC section hallucination rates (58–89%)
- The highest-accuracy model (Qwen-3-32B: 80.9%) simultaneously shows the worst hallucination profile (89.0%)

## Dataset

**IndianBailJudgments-1200**: A collection of 1,200 annotated Indian High Court and Supreme Court bail orders spanning 78 courts and five decades (1975–2025).

- **HuggingFace**: [SnehaDeshmukh/IndianBailJudgments-1200](https://huggingface.co/datasets/SnehaDeshmukh/IndianBailJudgments-1200)
- **arXiv Paper**: [arXiv:2507.02506](https://arxiv.org/abs/2507.02506)

### Dataset Statistics
- 1,200 bail orders (736 granted, 464 rejected)
- 78 courts (High Courts + Supreme Court)
- 12 crime categories
- 22 structured annotation fields per case
- Temporal span: 1975–2025

## Models Evaluated

- **GPT-4o** (OpenAI)
- **GPT-4o-mini** (OpenAI)
- **Llama-4-Scout** (Meta, 17B MoE)
- **Llama-3.3-70B** (Meta)
- **Qwen-3-32B** (Alibaba)
- **Mistral-Large** (Mistral AI, 123B)

All evaluations performed at temperature = 0, seed = 42 for reproducibility.

## Repository Structure

```
BailBench-India/
├── README.md                          # This file
├── BailBench_Evaluation.ipynb         # Complete evaluation pipeline
├── requirements.txt                   # Python dependencies
└── prompts/
    ├── task_a_v2.txt                  # Task A prompt template (primary)
    ├── task_b_v2.txt                  # Task B prompt template
    └── task_c.txt                     # Task C prompt template
```

## Installation

```bash
# Clone the repository
git clone https://github.com/YourUsername/BailBench-India.git
cd BailBench-India

# Install dependencies
pip install -r requirements.txt
```

## Usage

### Quick Start

The complete evaluation pipeline is provided in `BailBench_Evaluation.ipynb`. Open the notebook and run all cells to:

1. Load the IndianBailJudgments-1200 dataset from HuggingFace
2. Run Task A, B, and C evaluations
3. Compute metrics (accuracy, CFR, hallucination rates)
4. Generate results tables

### Task A: Zero-Shot Bail Outcome Prediction

Evaluates whether LLMs can predict bail outcomes (Granted/Rejected) from case facts without fine-tuning.

**V2 (Primary)**: Excludes gender from prompts  
**V1 (Comparison)**: Includes gender explicitly

### Task B: Counterfactual Demographic Perturbation

Measures prediction stability under demographic name swaps:
- Hindu ↔ Muslim name perturbations
- Male ↔ Female gender swaps

**Counterfactual Flip Rate (CFR)**: Proportion of cases where prediction changes under perturbation.

### Task C: IPC Section Hallucination Audit

Evaluates legal citation accuracy by comparing model-generated IPC sections against ground truth annotations.

## Metrics

- **Accuracy** with 1000-sample bootstrap 95% confidence intervals
- **Counterfactual Flip Rate (CFR)** with Wilson score CIs and binomial proportion tests
- **Precision, Recall, F1** for IPC section extraction
- **Hallucination Rate**: Fraction of cases with at least one hallucinated section

## Results Summary

### Task A (Zero-Shot Prediction)

| Model | Accuracy | 95% CI | Gender Gap |
|-------|----------|--------|------------|
| Qwen-3-32B | **80.9%** | [78.7, 83.1] | 0.5% |
| GPT-4o | 77.3% | [75.1, 79.8] | 1.0% |
| Llama-3.3-70B | 75.8% | [73.2, 78.3] | 1.5% |
| Mistral-Large | 71.1% | [68.5, 73.6] | 3.2% |
| Llama-4-Scout | 68.8% | [66.2, 71.7] | 4.3% |
| GPT-4o-mini | 67.8% | [65.2, 70.3] | 3.9% |

### Task B (Gender CFR, V1 with gender in prompts)

| Model | CFR | p-value | Significant |
|-------|-----|---------|-------------|
| Mistral-Large | 10.0% | <0.001 | ✓ |
| Llama-4-Scout | 5.0% | <0.001 | ✓ |
| Qwen-3-32B | 4.5% | <0.001 | ✓ |
| GPT-4o | 3.5% | 0.004 | ✓ |
| GPT-4o-mini | 3.0% | 0.016 | ✓ |
| Llama-3.3-70B | 2.5% | 0.052 | — |

### Task C (IPC Hallucination)

| Model | Precision | Recall | F1 | Hall. Rate |
|-------|-----------|--------|-----|------------|
| GPT-4o* | 54.0% | 47.9% | 47.5% | 61.3% |
| Mistral-Large | 47.1% | 60.3% | 49.1% | 88.3% |
| Llama-3.3-70B | 44.6% | 46.5% | 41.8% | 64.7% |
| Llama-4-Scout | 42.7% | 51.5% | 43.5% | 86.7% |
| GPT-4o-mini | 37.3% | 38.9% | 35.4% | 58.0% |
| Qwen-3-32B | 27.9% | 44.2% | 30.8% | **89.0%** |

*GPT-4o measures self-consistency against its own annotations

## Citation

If you use BailBench-India in your research, please cite:

```bibtex
@inproceedings{deshmukh2026bailbench,
  title={BailBench-India: A Counterfactual Diagnostic Suite for Auditing Demographic Bias in LLM Legal Reasoning},
  author={Deshmukh, Sneha},
  booktitle={Proceedings of the 40th Conference on Neural Information Processing Systems, Evaluations and Datasets Track},
  year={2026}
}
```

For the underlying dataset, please also cite:

```bibtex
@article{deshmukh2025indianbail,
  title={IndianBailJudgments-1200: A Multi-Attribute Legal NLP Dataset for Bail Order Understanding in India},
  author={Deshmukh, Sneha and Kamble, Prathmesh},
  journal={arXiv preprint arXiv:2507.02506},
  year={2025}
}
```

## License

This code is released under the MIT License. The IndianBailJudgments-1200 dataset is released under CC-BY-4.0.

## Contact

For questions or issues, please open an issue on GitHub or contact:
- Sneha Deshmukh: deshmusn@gmail.com

## Acknowledgments

We thank the legal personnel who provided manual review of dataset annotations. This research was conducted without external funding.
