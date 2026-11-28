# Correlation- and Degradation-Aware BESS Scheduling

Computational companion repository for the paper:

**Correlation-and Degradation-Aware Two-Stage Distributionally Robust Scheduling of Battery Energy Storage in PV-Rich Distribution Networks**

This repository provides the computational modules, parameter configurations, reference datasets, and visualization scripts associated with the proposed two-stage distributionally robust optimization framework for battery energy storage systems (BESSs) in PV-rich distribution networks.

## 1. Main Features

The repository includes the following computational components:

- **PV–Load Dependence Modeling:** Gaussian Copula-based modeling and correlated uncertainty scenario generation.
- **Distributionally Robust Optimization:** Wasserstein ambiguity-set construction and evaluation.
- **Battery Degradation Modeling:** Cycle-depth-dependent piecewise-linear degradation and Rainflow cycle counting.
- **Multi-timescale Scheduling:** Coordination between hourly day-ahead scheduling and 15-min intraday operation.
- **Optimization and Network Evaluation:** Adaptive multi-cut iCCG algorithm control, radial distribution network power-flow analysis, and reference result visualization.

## 2. Repository Structure

```text
BESS-DRO-Scheduling/
├── README.md
├── README_CN.md
├── CITATION.cff
├── requirements.txt
├── pyproject.toml
├── config/
├── data/
│   └── reference/
├── bess_dro/
│   ├── config.py
│   ├── uncertainty.py
│   ├── wasserstein.py
│   ├── degradation.py
│   ├── coordination.py
│   ├── ccg.py
│   ├── network.py
│   ├── reference_data.py
│   └── plotting.py
├── scripts/
│   ├── run_demo.py
│   └── reproduce_figures.py
├── tests/
└── docs/
```

## 3. Installation

**Requirements:**
- Python 3.10 or later
- NumPy, SciPy, pandas
- scikit-learn
- Matplotlib
- pytest

Install dependencies:

```bash
pip install -r requirements.txt
pip install -e .
```

## 4. Running the Code

Run the demonstration workflow:

```bash
python scripts/run_demo.py
```

This script generates 500 PV–load forecast-error trajectories, fits the Gaussian Copula model, reduces the trajectories to 20 representative scenarios, evaluates battery degradation cases, and generates demonstration outputs.

Reproduce the reference figures:

```bash
python scripts/reproduce_figures.py
```

Run the test suite:

```bash
pytest -q
```

The generated results are saved in the `outputs/` directory.

## 5. Main Simulation Parameters

| Parameter | Value |
|---|---|
| Test system | Modified IEEE 33-bus feeder |
| Nominal voltage | 12.66 kV |
| Total PV capacity | 2.50 MW |
| BESS location | Bus 17 |
| BESS rated energy | 2.0 MWh |
| Charge/discharge power | 0.4 MW |
| Charge/discharge efficiency | 0.95 |
| Energy operating range | 0.2–1.8 MWh |
| Gaussian Copula parameter | -0.51 |
| Wasserstein radius | 0.05 |
| Representative scenarios | 20 |
| Out-of-sample trajectories | 500 |
| iCCG final gap tolerance | 0.10% |

Detailed parameters are provided in `config/study_parameters.json`.

## 6. Citation

If you use this repository in your research, please cite the associated paper:

Ju, X., Zhang, Y., & Wu, H. *Correlation-and Degradation-Aware Two-Stage Distributionally Robust Scheduling of Battery Energy Storage in PV-Rich Distribution Networks.*

For further technical details, please refer to the documentation in the `docs/` directory.



