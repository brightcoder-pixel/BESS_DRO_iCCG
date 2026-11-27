 MATLAB project: Two-stage DRO / iCCG BESS scheduling
 1. Purpose
This project is a MATLAB R2022a research implementation of a two-stage distributionally robust battery energy storage system scheduling method based on relevance perception. It includes:

Gaussian-Copula PV/load uncertainty generation and fitting;

K-means reduction to 20 representative daily trajectories;

finite-support Wasserstein distributionally robust optimization;

adaptive multi-cut column-and-constraint generation (iCCG);

hourly day-ahead and 15-minute intraday recourse;

branch-flow SOCP network constraints on a 33-bus radial feeder;

rainflow cycle identification and cycle-depth piecewise-linear aging cost;

backward-forward sweep AC power-flow verification;

out-of-sample Monte Carlo voltage-violation probability and Wilson interval;

SOC relaxation slack diagnostics;\n- rainflow/PWL cost consistency and EFC diagnostics;\n- convergence, energy-trajectory, and degradation-coefficient plots.

 2. Required software

MATLAB R2022a or newer;

YALMIP;

IBM ILOG CPLEX 12.10 (configured by default), or change cfg.solver
to gurobi or mosek;

Optimization Toolbox (linprog) for the finite-support transport LP.

The code uses cone() and sdpvar() from YALMIP.

 3. Run
Add this project folder and all subfolders to the MATLAB path.

Confirm that YALMIP and the selected solver are installed and licensed.

Open default_config.m.

For a short software test, set cfg.Nmc = 5, cfg.NscenarioRaw = 50,
cfg.nRepresentative = 5, and cfg.maxICCG = 2.

Run:

matlab
results = run_full_model();
For the requested statistical assessment, restore cfg.Nmc = 500.
The Monte Carlo evaluation is computationally expensive because every
out-of-sample trajectory requires a 15-minute recourse SOCP solve.

 4. Data provenance
12.66-kV feeder;

total base demand 3.715 MW and 2.30 Mvar;

PV buses 10, 17, 24, 30 and aggregate PV capacity 2.5 MW;

BESS at bus 17, energy range 0.2-1.8 MWh, initial/terminal energy 1.0 MWh,
charge/discharge limit 0.4 MW and efficiency 0.95;

DGs at buses 6 and 25, each 0.5 MW;

voltage limits 0.95-1.05 p.u.;

DG cost 58 USD/MWh, curtailment penalty 75 USD/MWh,
import/export limits 2/1 MW, export price 40 USD/MWh;

1-h day-ahead and 15-min intraday scheduling;

degradation segment coefficients 2.2, 5.0, 9.8, 18.5 USD/MWh;

20 representative scenarios and Wasserstein radius 0.05.  
5. Mathematical implementation notes

5.1 Finite-support Wasserstein DRO


The inner transport LP is:

max sum_{s,j} gamma(s,j) Q(x,xi_j)

subject to:

- sum_j gamma(s,j) = pi_s;
- sum_{s,j} gamma(s,j) d(s,j) <= epsilon_W;
- gamma(s,j) >= 0.

Its dual is represented by alpha_s and lambda:
alpha_s + lambda*d(s,j) >= Q(x,xi_j), lambda >= 0.

The iCCG procedure starts with one active candidate, solves the master,
evaluates all finite candidate recourse problems, and adds up to three most
violated candidate scenarios per iteration. 

 5.2 Rainflow-coupled degradation

Rainflow turning-point pairings are fixed within each optimization solve.
The four PWL segment variables are linked to the energy difference between
the cycle's paired turning points. After solving, rainflow is applied again
and the cycle pairings are updated. This is a sequential rainflow/optimization
coordination heuristic. Its convergence and agreement with an independent
