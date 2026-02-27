# Orbital AI v13.8

[![Python Version](https://img.shields.io/badge/python-3.8%2B-blue)](https://www.python.org/downloads/)
[![License](https://img.shields.io/badge/license-MIT-green)](LICENSE)
[![Build Status](https://img.shields.io/badge/build-passing-brightgreen)](https://example.com) <!-- Placeholder; update if CI is set up -->

Orbital AI v13.8 is a first-principles simulation framework for radiation-hardened AI hardware in low-Earth orbit (LEO). It models a constellation of AI chips with features like systolic arrays, quantum nodes for optimization, self-healing swarms, thermal management, power systems, and radiation effects based on real NOAA GOES proton flux data. The simulation integrates machine learning (e.g., CNN training on CIFAR-10), quantum computing via QuTiP, and swarm coordination with QKD-secured federated learning.

This project demonstrates resilient AI systems for space environments, handling single-event upsets (SEUs), thermal runaway, power criticality, and swarm reconfiguration using QAOA (Quantum Approximate Optimization Algorithm).

## Table of Contents

- [Architecture](#architecture)
- [Features](#features)
- [Requirements](#requirements)
- [Installation](#installation)
- [Usage](#usage)
- [Deployment](#deployment)
- [Testing](#testing)
- [Contributing](#contributing)
- [License](#license)
- [Acknowledgments](#acknowledgments)

## Architecture

Orbital AI v13.8 is structured as a modular simulation system, combining classical ML, quantum computing, physics-based modeling, and swarm intelligence. The core is built around the `OrbitalAIChip` class, which represents a single node in space. Multiple nodes form a constellation managed by `SwarmCoordinator`.

### High-Level Overview

- **Single Node (OrbitalAIChip)**: Simulates an individual AI chip with:
  - Systolic array for compute-intensive tasks.
  - Radiation-hardened weights (RHBD) with SEU corruption and scrubbing.
  - Thermal management (predictive controller, radiators, louvers, active cooling).
  - Power system (solar arrays, batteries, dynamic frequency scaling).
  - Quantum node for decoherence-aware computations.
  - ML model (TinyCNN for CIFAR-10 classification).
  - Communication (laser comms, QKD for secure key exchange).

- **Swarm/Constellation (ODCConstellation)**: A group of nodes with:
  - Self-healing: Health monitoring, isolation, and reintegration.
  - Reconfiguration: Quantum-optimized topology changes (e.g., mesh to star).
  - Coordination: QKD-secured federated averaging for model updates.

- **Simulation Flow**:
  1. Initialize chips/constellation with config.
  2. Simulate orbits: Update radiation flux (from GOES data), corrupt weights, manage thermal/power, retrain ML model if accuracy drops.
  3. Swarm sync: Heal, reconfigure, and average models periodically.
  4. Handle exceptions like endurance failure, thermal runaway, or power critical states.

- **Physics Modules**:
  - Radiation: GOESProtonFlux fetches real-time proton data or simulates solar cycles.
  - Thermal: First-principles heat balance with sun/earth albedo, radiators, and predictive NN controller.
  - Power: Solar degradation, battery SOC, low-power modes.
  - Quantum: QuTiP-based simulation with T1/T2 decoherence and QAOA for optimization.

- **ML Integration**:
  - OrbitalTinyCNN: Radiation-aware CNN with wrapped parameters for SEU simulation.
  - Training: Onboard retraining with RadHardOptimizer; uses torchvision for CIFAR-10.

The system uses PyTorch for ML, QuTiP for quantum sim, and Matplotlib for visualizations (e.g., accuracy over orbits).

### Key Components

| Component              | Description                                                                 | Dependencies       |
|------------------------|-----------------------------------------------------------------------------|--------------------|
| **OrbitalAIChip**     | Core hardware simulation with thermal, power, and quantum subsystems.      | PyTorch, QuTiP    |
| **QuantumNode**       | Onboard quantum processor for QAOA-based reconfiguration.                  | QuTiP             |
| **SelfHealingSwarm**  | Monitors node health; isolates/reintegrates based on temp, SOC, SEU, acc.  | -                 |
| **SwarmCoordinator**  | Handles QKD-secured federated learning and scrubbing.                      | PyTorch           |
| **PredictiveThermalController** | NN-based thermal prediction for proactive control.                       | PyTorch           |
| **GOESProtonFlux**    | Fetches/simulates real proton flux for radiation effects.                  | Requests          |
| **OrbitalTinyCNN**    | Radiation-hardened CNN model.                                              | PyTorch, Torchvision |
| **MissionSimulator**  | Runs the full orbit-by-orbit simulation.                                   | Matplotlib        |

Data flow: Mission time advances in orbit steps (95 min each). Each step updates physics, corrupts weights, checks thresholds, and triggers ML retraining/swarm actions.

## Features

- **Radiation Hardening**: Realistic SEU simulation with scrubbing and TMR/EDAC.
- **Quantum Integration**: Decoherence modeling and QAOA for swarm reconfiguration.
- **Thermal/Power Management**: Predictive control, deployable radiators, solar degradation.
- **Swarm Resilience**: Self-healing, isolation, QKD-secured coordination.
- **ML Onboard**: Retraining on CIFAR-10 with accuracy monitoring.
- **Real Data**: Integrates live NOAA GOES proton flux.
- **Export**: FPGA export stub for hardware acceleration (extendable).
- **Exceptions**: Custom errors for failure modes (e.g., ThermalRunaway).

## Requirements

- **Python**: 3.8 or higher.
- **Libraries**:
  - `torch` (>=2.0.0) - For ML and tensors.
  - `torchvision` (>=0.15.0) - For datasets like CIFAR-10.
  - `qutip` (>=4.7.0) - For quantum simulations.
  - `matplotlib` (>=3.5.0) - For plotting simulation results.
  - `requests` (>=2.25.0) - For fetching GOES data.
  - Standard libraries: `argparse`, `logging`, `math`, `random`, `os`, `dataclasses`, `typing`.

No additional hardware required; runs on CPU (GPU optional for faster ML training).

## Installation

1. Clone the repository:
   ```
   git clone https://github.com/yourusername/orbital-ai.git
   cd orbital-ai
   ```

2. Create a virtual environment (recommended):
   ```
   python -m venv venv
   source venv/bin/activate  # On Windows: venv\Scripts\activate
   ```

3. Install dependencies:
   ```
   pip install torch torchvision qutip matplotlib requests
   ```

4. Download the script: The core file is `OrbitalAIv13.8.py` (rename from `.txt` if needed).

## Usage

Run the simulation demo via command-line arguments.

### Basic Command
```
python OrbitalAIv13.8.py --nodes 3 --seed 42
```

- `--nodes`: Number of chips in the constellation (default: 1).
- `--seed`: Random seed for reproducibility (default: 42).
- `--export_fpga`: Export model to FPGA format (stub; outputs a basic Verilog skeleton).

### What Happens in a Run?
- Initializes constellation.
- Downloads/simulates GOES proton data.
- Runs a 100-orbit mission simulation.
- Trains/retrains CNN on CIFAR-10 subsets.
- Logs thermal, power, SEU, accuracy, and swarm events.
- Plots accuracy over orbits.
- Handles failures (e.g., raises exceptions on critical events).

Example Output Snippet:
```
2026-02-27 18:00:00 [INFO] orbital-ai-v13.8: GOES proton data loaded: 1000 records (live NOAA SWPC)
2026-02-27 18:00:01 [INFO] orbital-ai-v13.8: 🚀 128x128 Systolic Array online
...
🧼 RHBD scrub #1 (EDAC + TMR)
🌐 Swarm QKD-secured coordination orbit 16
```

## Deployment

This is a simulation; no real deployment to space hardware. However:

- **Local/Cloud Simulation**: Run on any Python environment. For large constellations (>10 nodes), use GPU for faster training: `CUDA_VISIBLE_DEVICES=0 python OrbitalAIv13.8.py`.
- **FPGA Export**: Use `--export_fpga` to generate a basic Verilog file for the CNN and systolic array. Extend `FPGAExporter` for full hardware synthesis (requires tools like Vivado).
- **Containerization**: Dockerize for reproducibility:
  ```
  FROM python:3.10
  RUN pip install torch torchvision qutip matplotlib requests
  COPY OrbitalAIv13.8.py .
  CMD ["python", "OrbitalAIv13.8.py", "--nodes", "3"]
  ```
  Build and run: `docker build -t orbital-ai . && docker run orbital-ai`.

For production-like deployment (e.g., CI/CD for simulations), integrate with GitHub Actions.

## Testing

Tests are not explicitly defined in the code but can be added using `unittest` or `pytest`. Basic validation:

1. Install `pytest`: `pip install pytest`.
2. Create `tests/test_orbital_ai.py`:
   ```python
   import pytest
   from OrbitalAIv13.8 import Config, OrbitalAIChip, GOESProtonFlux

   def test_flux_multiplier():
       flux = GOESProtonFlux()
       assert flux.get_flux_multiplier(0) >= 1.0

   def test_chip_init():
       chip = OrbitalAIChip(Config())
       assert chip.temp_c == -18.0
       assert chip.battery_soc == 1.0
   ```

3. Run tests: `pytest tests/`.

- **Unit Tests**: Cover individual classes (e.g., QuantumNode fidelity decay).
- **Integration Tests**: Simulate short missions and assert no unhandled exceptions.
- **Coverage**: Aim for >80% with `pytest-cov`.

Manually test by running with different `--nodes` and checking logs/plots for consistency.

## Contributing

Contributions welcome! Fork the repo, create a branch, and submit a PR. Focus areas:
- Add more quantum algorithms.
- Integrate real-time GOES streaming.
- Enhance FPGA export.
- Add GUI for visualization.

Follow PEP8 style. Include tests for new features.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Acknowledgments

- Built with PyTorch, QuTiP, and NOAA SWPC data.
- Inspired by space AI research from NASA/JPL.
- Thanks to the open-source community for robust libraries.
