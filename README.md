# ITC2021: sports timetabling with MILP

Our entry to the [International Timetabling Competition 2021](https://www.sportscheduling.ugent.be/ITC2021/), which asked for double round-robin schedules for sports leagues. Each instance fixes the teams and time slots and lists hard and soft constraints. A solution assigns every game (home team, away team) to a slot, and its score is the total penalty of the violated soft constraints.

## Approach

- `ITC21_parser.py` reads the competition XML instances (`data/`: early, middle, late and test instances).
- `MILP/ITC21 MILP.py` is a Gurobi model. A binary variable `X_ijt` says whether team `i` hosts team `j` in slot `t`; `BH_it` and `BA_it` mark home and away breaks. It covers the general rules (one game per team per slot, phased mode) and the five constraint families of the competition: capacity (CA1-CA4), game (GA1), break (BR1-BR2), fairness (FA2) and separation (SE1). Each soft constraint gets an integer penalty variable and the objective is their weighted sum.
- Large instances were solved in two stages. `MILP/ITC21 MILP_br2.py` first minimises the BR2 break penalties only. `MILP/ITC21 MILP_pen.py` then solves the full model, warm-started from that schedule.
- The `*_constraints.py` modules and `solution.py` read a solution XML back and compute its penalty per constraint, to check results independently of the solver.
- `OrganizationITC2021_V6.pdf` is the official problem description.

## Run

Requires Python 3, `gurobipy` with a Gurobi licence, `pandas` and `lxml`.

```bash
cd MILP
python "ITC21 MILP.py" ../data/TestInstances_V3/ITC2021_Test5.xml run1
# two-stage variant
python "ITC21 MILP_br2.py" ../data/LateInstances/ITC2021_Late_1.xml br2
python "ITC21 MILP_pen.py" ../data/LateInstances/ITC2021_Late_1.xml full br2
```

Solutions are written as competition XML under `MILP/solutions/`. Time limits are set to 20-24 hours per instance, as used during the competition; lower `TimeLimit` in the script for a quick run.

## Authors

Amine Athmani and Mourad Terzi.
