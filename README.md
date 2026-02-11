# Cryptareon

A research and simulation framework for **Areon**, a latency-friendly and resilient multi-proposer consensus protocol with privacy-preserving network overlay capabilities.

## Paper

This project implements and simulates the protocol described in:

**[Areon: Latency-Friendly and Resilient Multi-Proposer Consensus](https://arxiv.org/abs/2511.23025)**  
*Álvaro Castro-Castilla, Marcin Pawlowski, Hong-Sheng Zhou*

> We present Areon, a family of latency-friendly, stake-weighted, multi-proposer proof-of-stake consensus protocols. By allowing multiple proposers per slot and organizing blocks into a directed acyclic graph (DAG), Areon achieves robustness under partial synchrony. Blocks reference each other within a sliding window, forming maximal antichains that represent parallel "votes" on history. Conflicting subDAGs are resolved by a closest common ancestor (CCA)-local, window-filtered fork choice that compares the weight of each subDAG -- the number of recent short references -- and prefers the heavier one. Combined with a structural invariant we call Tip-Boundedness (TB), this yields a bounded-width frontier and allows honest work to aggregate quickly.
>
> We formalize an idealized protocol (Areon-Ideal) that abstracts away network delay and reference bounds, and a practical protocol (Areon-Base) that adds VRF-based eligibility, bounded short and long references, and application-level validity and conflict checks at the block level. On top of DAG analogues of the classical common-prefix, chain-growth, and chain-quality properties, we prove a backbone-style finality theorem that calibrates confirmation depth as a function of the window length and target tail probability. We focus on consensus at the level of blocks; extending the framework to richer transaction selection, sampling, and redundancy policies is left to future work.
>
> Finally, we build a discrete-event simulator and compare Areon-Base against a chain-based baseline (Ouroboros Praos) under matched block-arrival rates. Across a wide range of adversarial stakes and network delays, Areon-Base achieves bounded-latency finality with consistently lower reorganization frequency and depth.

## Overview

Cryptareon provides tools for modeling, simulating, and analyzing blockchain consensus mechanisms with a focus on:

- Network-layer modeling with blend networks for privacy
- Proof-of-Leadership (PoL) consensus simulation
- Transaction dependency analysis
- Block propagation and timing analysis
- Adversarial resistance testing

## Features

### Network Modeling
- **Blend Network Simulation** - Privacy-preserving message propagation through multiple network hops
- **Dissemination Delay** - Exponentially distributed delays for realistic network behavior
- **Network Delay Analysis** - Empirical delay distributions and percentile calculations

### Consensus Protocol (Areon)
- **Stake-based Block Production** - Probabilistic leader selection based on relative stake
- **Block References and Dependencies** - Transaction ordering and dependency graphs
- **Adversarial Modeling** - Configurable adversary control for security analysis
- **Slot-based Time** - Discrete time slots for deterministic simulation

### Visualization
- Interactive network graphs using PyVis
- Statistical plots for network delays and weight functions
- Block chain visualization with tree-based views

## Installation

1. Clone the repository:
```bash
git clone <repository-url>
cd cryptareon
```

2. Create and activate a virtual environment:
```bash
python -m venv .venv
source .venv/bin/activate  # On Windows: .venv\Scripts\activate
```

3. Install dependencies:
```bash
pip install -r requirements.txt
```

## Usage

Launch Jupyter Lab to explore the simulations:

```bash
jupyter lab
```

Open `cryptareon.ipynb` to access the main simulation notebook.

### Network Parameters

Configure network behavior with `NetworkParams`:

```python
blend_net = NetworkParams(
    broadcast_delay_mean=0.5,      # Average broadcast delay (seconds)
    pol_proof_time=1,               # Proof-of-leadership time
    blending_delay=3,               # Maximum blend delay
    dissemination_delay_mean=0.5,  # Average dissemination delay
    blend_hops=3,                   # Number of blend network hops
)
```

### Consensus Parameters

Define consensus behavior with `Params`:

```python
params = Params(
    SLOTS=1000,                    # Number of simulation slots
    f=0.05,                        # Active slot coefficient
    honest_stake=np.array([...]),  # Stake distribution
    adversary_control=0.3,         # Adversary stake fraction
    window_size=100,               # Sliding window size
    use_deps=True                  # Enable transaction dependencies
)
```

## Network Architecture

### Blend Network

Cryptareon models a privacy-preserving blend network where messages pass through multiple hops:

1. **Blending Stage** - Messages wait for a random delay (0 to `blending_delay`)
2. **Dissemination** - Messages propagate with exponential delays
3. **Multi-hop** - Process repeats for `blend_hops` iterations

This approach provides privacy while maintaining bounded latency.

### Block Arrival

Total block arrival time includes:
- Proof-of-Leadership computation time
- Blend network delay (sum of all hops)
- Final broadcast delay

## Consensus Model

### Proof-of-Leadership

Leaders are selected probabilistically based on:
- Relative stake in the network
- Active slot coefficient (f)
- φ function: `φ(f, α) = 1 - (1-f)^α`

### Block Structure

Each block contains:
- **ID** - Unique block identifier
- **Slot** - Time slot of production
- **Refs** - Parent block references
- **Deps** - Transaction dependencies
- **Leader** - Producer identity
- **Adversarial flag** - Marks malicious blocks

## Research Applications

- **Protocol Analysis** - Test consensus properties under various conditions
- **Security Research** - Model adversarial behavior and attack scenarios
- **Performance Optimization** - Analyze latency and throughput trade-offs
- **Privacy Study** - Evaluate blend network effectiveness

## Key Metrics

The simulation tracks:
- Block production rate and distribution
- Network propagation delays (median, 99th percentile, 99.9th percentile)
- Chain convergence properties
- Adversarial success rates
- Transaction dependency resolution

## License

MIT

## References

This project implements concepts from blockchain consensus research:
- Proof-of-Stake mechanisms
- Nakamoto-style consensus
- Privacy-preserving network overlays
- Byzantine fault tolerance

---

**Note**: This is an active research project. The code and parameters are subject to change as the protocol evolves.
