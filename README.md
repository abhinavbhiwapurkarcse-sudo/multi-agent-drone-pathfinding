# Multi-Agent Drone Pathfinding: Dijkstra + Prioritized Coordination

Part I (Task Completion) of the hiring process *Quantum-Assisted Multi-Agent System Optimization for Autonomous Swarms*:
coordinating several autonomous drones on a shared 2D grid so that each reaches its target without colliding, while keeping total travel distance and mission time low.

## What is in this repository

| Path                                    | Content                                                                                                                                                                                                                                      |
| --------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Multi_Agent_Drone_Pathfinding.ipynb` | The complete solution: problem explanation, object-oriented code, exploratory data analysis, experiments, test cases, quantum (QUBO) extension discussion. Saved **with all outputs**, so it can be read on GitHub without running it. |
| `figures/`                            | All plots from the notebook (PNG) and two animated GIFs of the simulation (independent vs coordinated).                                                                                                                                      |
| `results/`                            | Result tables as CSV: worked example.                                                                                                                                                                                                        |
| `presentation/`                       | Step 2: 7-slide PowerPoint summarising *Hybrid Quantum-Classical Multi-Agent Pathfinding* (Gerlach et al., ICML 2025).                                                                                                                    |
| `requirements.txt`                    | Python packages needed.                                                                                                                                                                                                                      |
| `REFERENCES.md`                       | Paper, algorithms and libraries used.                                                                                                                                                                                                        |

## Problem and approach

* **Environment:** 2D grid, 4-connected moves plus "wait"; obstacles are blocked cells. Each drone has a start and a target cell.
* **Baseline:** every drone runs plain **Dijkstra** and ignores the others (collisions are counted, not resolved).
* **Coordinated method:** drones plan one after another, longest trip first. Each runs **Dijkstra on a (cell, time) graph** where cells already reserved by earlier drones are blocked at the vertex and swap conflicts, so a drone can wait or detour. Parked drones stay as obstacles. If some drone fails, random priority orders are tried.
* **Metrics:** total travel distance, number of collisions (vertex + swap), % of drones reaching their destination, mission completion time; plus extra distance versus the shortest paths, waiting steps and planning time.
* **Quantum extension:** Section 8 of the notebook explains which part could benefit (the joint choice of one path per drone), a QUBO formulation, and advantages and limitations.

## Results

Worked example (10x10 grid, 15% obstacles, seed 0; Scenario 2 has the 5 drones, Scenario 1 the first 2 of them):

| Scenario     | Method               | Collisions | Total distance | Reached | Completion time |
| ------------ | -------------------- | ---------- | -------------- | ------- | --------------- |
| 1 (2 agents) | Independent Dijkstra | 1          | 25             | 100%    | 15              |
| 1 (2 agents) | Coordinated          | 0          | 25             | 100%    | 15              |
| 2 (5 agents) | Independent Dijkstra | 5          | 45             | 100%    | 15              |
| 2 (5 agents) | Coordinated          | 0          | 47             | 100%    | 15              |

**Limitations:** prioritized planning is neither optimal nor complete; the world is a discrete 2D grid with no vehicle dynamics or safety radius; planning is centralised.

## Installation

Requires Python 3.9 or newer.

```bash
git clone <this repository's URL>
cd <repository folder>
python -m venv .venv
# Windows: .venv\Scripts\activate      macOS/Linux: source .venv/bin/activate
pip install -r requirements.txt
```

## How to run

```bash
jupyter notebook Multi_Agent_Drone_Pathfinding.ipynb
```

Then choose **Kernel -> Restart & Run All** (about 1 minute). The notebook recreates the figures, writes the GIFs to `figures/` and the CSV tables to `results/`. The test cases (Section 7) stop with an `AssertionError` if anything is wrong.

## Simulation environment (no dataset needed)

The environment is generated by `make_scenario(width, height, n_agents, obstacle_density, seed)` in the notebook:

1. each cell is an obstacle with probability `obstacle_density` (0.15);
2. start and goal cells are drawn without repetition from the largest connected free region, so every goal is reachable;
3. every trip is at least 5 cells long.

Everything is seeded, so results are reproducible: the worked example uses the first seed for which the independent paths collide in both scenarios. To change the setup, edit `GRID_W`, `GRID_H`, `OBSTACLE_DENSITY` or the seeds in the first cells. The optional Moving AI benchmark maps were not used.

## Presentation (Step 2)

`presentation/Hybrid_Quantum_Classical_MAPF_Presentation.pptx`: motivation, objectives, methodology, experiments, limitations and future work, and the connection to autonomous drone swarms.

## References

See [REFERENCES.md](REFERENCES.md).
