# Autonomous Delivery Agent

## Project Overview
Design and implement an Autonomous Delivery Agent that navigates a 2D grid city to deliver packages efficiently. The agent models the environment (static obstacles, varying terrain costs, dynamic moving obstacles), reasons rationally to maximize delivery efficiency under constraints (time, fuel), and implements multiple planning strategies:

- Uninformed search: Breadth-First Search (BFS) / Uniform-Cost Search (UCS)
- Informed search: A* search with admissible heuristics
- Local search & replanning strategy for dynamic obstacles (e.g., local A* replanning, incremental search, or greedy local repair)

## Features
- Grid-based environment simulator with:
  - Static obstacles (walls, buildings)
  - Varying terrain costs (e.g., roads, slow zones)
  - Dynamic moving obstacles (pedestrians, vehicles) with simple motion models
- Path planning algorithms: BFS, UCS, A*
- Replanning strategies for dynamic changes (local replanning, partial-global replanning)
- Simulation visualizer (matplotlib) and logging for metrics
- Evaluation metrics: path cost, delivery time, fuel consumption, replanning count, success rate

## Project Structure (suggested)
```
autonomous-delivery-agent/
├── README.md
├── requirements.txt
├── src/
│   ├── env/
│   │   ├── grid.py                 # grid definition, terrain costs, static obstacles
│   │   ├── dynamic_obstacles.py    # moving obstacle models
│   │   └── simulator.py            # simulation loop, visualization hooks
│   ├── planners/
│   │   ├── bfs.py
│   │   ├── ucs.py
│   │   ├── astar.py
│   │   └── replanner.py            # local replanning strategies
│   ├── agents/
│   │   └── delivery_agent.py       # agent logic, fuel/time model, decision loop
│   ├── utils/
│   │   ├── heuristics.py
│   │   └── helpers.py
│   └── experiments/
│       └── run_experiment.py
├── data/
│   └── maps/                       # sample grid maps
└── tests/
    └── test_planners.py
```

## Getting Started

### Prerequisites
- Python 3.8+
- Recommended packages (see `requirements.txt`):
```
numpy
matplotlib
pytest
networkx
```

### Installation
```bash
git clone <your-repo-url>
cd autonomous-delivery-agent
python -m venv venv
source venv/bin/activate      # Windows: venv\Scripts\activate
pip install -r requirements.txt
```

## How to Run

### 1) Run the simulator with a default map
```bash
python src/experiments/run_experiment.py --map data/maps/simple_city.json --planner astar --visualize
```

### 2) Run experiments (batch)
```bash
python src/experiments/run_experiment.py --configs experiments/configs/benchmark.yaml
```

### 3) Run tests
```bash
pytest -q
```

## Algorithms & Implementation Notes

### Uninformed Search
- **BFS**: Suitable for uniform-cost grids (all moves cost equal). Returns shortest path in number of steps.
- **UCS**: Uses a priority queue with path cost; necessary when terrain costs vary.

### Informed Search (A*)
- Implement A* with admissible heuristics:
  - Manhattan distance for 4-way grids
  - Euclidean distance for continuous-like moves
- Ensure heuristics are admissible and consistent when expecting optimality guarantees.

### Replanning Strategy
- Monitor dynamic obstacles and when the current path is blocked or predicted to collide, trigger:
  - Local replanning: run A* on a local window around the agent
  - Incremental search (optional): use algorithms such as D* Lite or LPA* for efficient replanning
- Balance replanning frequency with CPU/time constraints.

## Agent Model (Time & Fuel)
- Define a fuel consumption model: `fuel_cost = base_cost + terrain_multiplier`
- Time model: moves per time step, wait actions, and dynamic obstacle motion update
- Reward function or cost function must combine time and fuel to allow rational trade-offs. Example:
```
total_cost = alpha * time + beta * fuel + gamma * path_cost
```
Tune `alpha`, `beta`, `gamma` per experiment.

## Evaluation & Metrics
- Path length (cost and steps)
- Delivery time (wall-clock ticks in simulation)
- Fuel consumed
- Number of replans and replanning time
- Success rate (deliveries completed without failure)
- CPU/runtime per episode

## Example Configurations
Provide JSON/YAML configs for experiments specifying:
- Map file
- Start & goal locations
- Planner choice (bfs/ucs/astar)
- Dynamic obstacle density & speed
- Agent fuel/time parameters
- Visualization on/off

## Extensions (ideas)
- Implement D* Lite or LPA* for efficient incremental replanning
- Multi-agent coordination for simultaneous deliveries
- Learning-based heuristics or neural planners
- Real-world map import (OpenStreetMap) and conversion to grid
- ROS integration for physical robot deployment
