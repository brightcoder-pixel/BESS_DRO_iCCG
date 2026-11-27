A MATLAB R2022a research implementation of a relevance-aware two-stage distributionally robust battery energy storage system (BESS) scheduling method.

## Features

- Gaussian-Copula PV/load uncertainty generation and fitting
- K-means reduction to 20 representative daily trajectories
- Finite-support Wasserstein distributionally robust optimization
- Adaptive multi-cut column-and-constraint generation (iCCG)
- Hourly day-ahead and 15-minute intraday recourse
- Branch-flow SOCP network constraints on a 33-bus radial feeder
- Rainflow cycle identification and cycle-depth piecewise-linear aging cost
- Backward-forward sweep AC power-flow verification
- Out-of-sample Monte Carlo voltage-violation probability and Wilson interval
- SOC relaxation slack diagnostics; rainflow/PWL cost consistency and EFC diagnostics
- Convergence, energy-trajectory, and degradation-coefficient plots

## Requirements

- MATLAB R2022a or newer
- [YALMIP](https://yalmip.github.io/)
- IBM ILOG CPLEX 12.10 (default), or change `cfg.solver` to `gurobi` / `mosek`
- Optimization Toolbox (`linprog`, for the finite-support transport LP)

The code uses `cone()` and `sdpvar()` from YALMIP.

## Quick Start

1. Add this project folder and all subfolders to the MATLAB path.
2. Confirm that YALMIP and the selected solver are installed and licensed.
3. Open `default_config.m`.
4. For a short software test, set:
   ```matlab
   cfg.Nmc = 5;
   cfg.NscenarioRaw = 50;
   cfg.nRepresentative = 5;
   cfg.maxICCG = 2;
   ```
5. Run:
   ```matlab
   results = run_full_model();
   ```

> For the full statistical assessment, restore `cfg.Nmc = 500`. The Monte Carlo evaluation is computationally expensive because every out-of-sample trajectory requires a 15-minute recourse SOCP solve.

## Test System Parameters

- 12.66 kV feeder
- Total base demand 3.715 MW / 2.30 Mvar
- PV buses 10, 17, 24, 30; aggregate PV capacity 2.5 MW
- BESS at bus 17: energy range 0.2–1.8 MWh, initial/terminal energy 1.0 MWh, charge/discharge limit 0.4 MW, efficiency 0.95
- DGs at buses 6 and 25, each 0.5 MW
- Voltage limits 0.95–1.05 p.u.
- DG cost 58 USD/MWh, curtailment penalty 75 USD/MWh, import/export limits 2/1 MW, export price 40 USD/MWh
- 1-h day-ahead and 15-min intraday scheduling
- Degradation segment coefficients 2.2, 5.0, 9.8, 18.5 USD/MWh
- 20 representative scenarios, Wasserstein radius 0.05

## Mathematical Implementation Notes

### Finite-Support Wasserstein DRO

The inner transport LP:

```
max  Σ_{s,j} γ(s,j) Q(x, ξ_j)
s.t. Σ_j γ(s,j) = π_s
     Σ_{s,j} γ(s,j) d(s,j) ≤ ε_W
     γ(s,j) ≥ 0
```

Its dual is represented by `α_s` and `λ`: `α_s + λ·d(s,j) ≥ Q(x, ξ_j)`, `λ ≥ 0`.

The iCCG procedure starts with one active candidate, solves the master, evaluates all finite candidate recourse problems, and adds up to three most violated candidate scenarios per iteration.

### Rainflow-Coupled Degradation

Rainflow turning-point pairings are fixed within each optimization solve. The four PWL segment variables are linked to the energy difference between the cycle's paired turning points. After solving, rainflow is applied again and the cycle pairings are updated. This is a sequential rainflow/optimization coordination heuristic.

## License

To be added (e.g., MIT / Apache-2.0).
