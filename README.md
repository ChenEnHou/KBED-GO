# KBED-GO
Formal Verification of KBED on Go games

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Python: 3.10+](https://img.shields.io/badge/Python-3.10%2B-blue.svg)](https://www.python.org/)
[![Status: Pre-registered Artifact](https://img.shields.io/badge/Status-Pre--registered%20Artifact-success.svg)](#pre-registration-and-empirical-results)

**Author:** Chen-En Hou (Principal Investigator)  
**Year:** 2026  
**Manuscripts:** [`docs/KBED_Decomposition_Conditions_Paper_V1EN.docx`](docs/KBED_Decomposition_Conditions_Paper_V1EN.docx) | [`docs/KBED_Decomposition_Conditions_Paper_V1.docx`](docs/KBED_Decomposition_Conditions_Paper_V1.docx)

---

## 1. Overview & Research Question

**KBED (Knot-Belt Exhaustive Decomposition)** posited that exhaustive coverage of a finite state space does not require independent calculation of every global state. By partitioning the system into localized units (**knots**), solving each exactly, and composing them through an algebra of games, global exact solutions can be achieved at reduced complexity.

This repository provides the empirical verification and formal falsification framework for KBED applied to **Go endgames (官子)** under Japanese territory rules. Using whole-board exhaustive Minimax search as ground truth, we evaluate:
1. Under what exact interface conditions can local exact values be safely composed?
2. When do boundary interactions force knots to merge and recompute?
3. Can these boundary failures be reliably screened before merging?

---

## 2. Key Empirical Findings (T1–T4)

The framework tests the interface between knots by relaxing boundary constraints step by step:

| Experiment | Interface Type | Benchmark / Scale | Core Result | Complexity Impact |
| :--- | :--- | :--- | :--- | :--- |
| **T1: Summation** | Zero interface (Benson uncapturable walls) | 316 positions (9×9, $\le 12$ empty) | **316/316 exact match** on scores & optimal moves. | Median states: **9** (Decomposition) vs. **6,885** (Exhaustive Minimax). |
| **T2: Substitution** | Canonical equivalence & interchangeability | 350 pairs, 1,750 contexts | **200/200 equal-value pairs invariant** across 1,000 contexts. | Proved stop-only or temperature-only sorting is unsafe (48.7% accuracy). |
| **T3: Shared Object** | Capturable shared wall (H5 chain) | 1,250 positions, 7,870 states | Interactions span up to 3 regions; **862/862 exact** when premises hold. | Proved pairwise checking is insufficient; hypergraph dependency established. |
| **T4: Virtual Boundary** | Arbitrary open seams (7–10 points) | 3,214 cuts evaluated (Rounds 2–5) | Arbitrary cuts exact in only **35%** (1,112/3,214). Pre-registered screen $M_{1v}$ achieved **0 errors (71/994)**. | Safe cuts save **5.6× states** (148 vs. 836), but conservative screening accepts only **7%** of cuts. |

---

## 3. Discovered Failure Modes (Minimal Counterexamples)

Through pre-registered adversarial blind-testing, three discrete physical dependencies were isolated and classified:

* **Type A (Interface Dependence):** The interface point valued in isolation is falsely treated as local territory without regard to two-sided dynamic play. *(Resolved via bidirectional interface-side screening $M_{1b}$)*
* **Type B (Capture Transition on Seam):** A stone on one side has its final liberty on the interface; playing on the interface triggers an unmodeled group capture. *(Isolated in seed `500781`; resolved via transition checking $M_{1t}$ and dynamic move simulation)*
* **Type C (Shared Liberties Across Knots):** A stone located on the interface shares liberties across two adjacent knots, requiring multi-stage moves on both sides to capture. *(Isolated in seed `850002`; resolved via whole-group structural checking in $M_{1v}$)*

Minimal ASCII board reproductions for Types B and C are documented in Section 4.4 of the technical paper.

---

## 4. Repository Structure

```text
kbed-go-verification/
├── README.md               # Project overview, findings, and replication guide
├── LICENSE                 # MIT License
├── docs/                   # Formal technical papers & chronicle
│   ├── KBED_Decomposition_Conditions_Paper_V1EN.docx
│   ├── KBED_Decomposition_Conditions_Paper_V1.docx
│   ├── KBED_Virtual_Boundary_Report_v0.4.docx
│   └── CHRONICLE.md        # Full audit trail & cache debugging history
├── go/                     # Core combinatorial game engine & verification scripts
│   ├── cgt.py              # Combinatorial game theory canonical form reduction
│   ├── goeng.py            # Go engine (Japanese scoring, eye & life verification)
│   ├── fullexact.py        # Ground-truth exhaustive minimax solver (ko-history invariant)
│   ├── t1.py, t2.py, t3.py # T1–T3 experiment runners
│   ├── t4.py, gen4.py      # T4 virtual seam generator & screening pipeline
│   ├── regress_ko.py       # Regression benchmark (280 tiny ko positions)
│   └── regress_value4.py   # Regression benchmark (20 fixed value-4 positions)
└── results/                # Raw outputs, pre-registered logs, and counterexamples
    ├── t4_counterexamples.json  # 76 historical counterexamples archived
    └── PROGRESS.md         # Timestamped pre-registration ledger



Author Contributions & Multi-Agent AI Verification DisclosureChen-En Hou (Principal Investigator) conceived the KBED methodology, formulated the hierarchical state abstraction framework, designed the staged empirical validation roadmap (T1–T4), and enforced all Pre-registration Agreements and falsification criteria.   To ensure rigorous formal validation, three advanced AI language models were deployed in a structured, adversarial multi-agent workflow:Primary Developer (Claude Pro): Implemented and refactored the combinatorial game engine, Japanese scoring mechanics, exhaustive minimax baseline solvers, and automated regression suites.   Code & Logic Auditor (ChatGPT Pro): Conducted independent static code audits, which successfully identified history-dependent cache anomalies in earlier solver iterations.   Adversarial Validator (Gemini Pro): Acted as an adversarial challenger and red-team engine, systematically stress-testing boundary screening rules and verifying topological counterexamples.   All theoretical interpretations, physical failure mode classifications, algorithmic decisions, and manuscript approvals were directed, verified, and finalized by the human author.  
