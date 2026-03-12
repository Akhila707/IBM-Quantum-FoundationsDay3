# IBM Quantum Foundations — Day 3

<div align="center">

![IBM Quantum](https://img.shields.io/badge/IBM%20Quantum-052FAD?style=flat-square&logo=ibm&logoColor=white)
![Qiskit](https://img.shields.io/badge/Qiskit-6929C4?style=flat-square&logoColor=white)
![Python 3.10](https://img.shields.io/badge/Python%203.10-1a1a2e?style=flat-square&logo=python&logoColor=4fc3f7)
![Jupyter](https://img.shields.io/badge/Jupyter-1a1a2e?style=flat-square&logo=jupyter&logoColor=4fc3f7)
![Day 3](https://img.shields.io/badge/Day%2003-Complete-4fc3f7?style=flat-square)
![Day 4](https://img.shields.io/badge/Day%2004-Loading...-555555?style=flat-square)

</div>

<br/>

```
  ╔══════════════════════════════════════════════════════════╗
  ║   X|0⟩ = |1⟩      H|0⟩ = |+⟩      CZ|11⟩ = -|11⟩     ║
  ║   Grover's O(√N) vs Classical O(N)                      ║
  ╚══════════════════════════════════════════════════════════╝
```

<div align="center">
<i>Part of the IBM Quantum 20-Day Learning Sprint · VIT Chennai</i>
</div>

---

## Overview

Two Jupyter notebooks covering quantum gates in depth and Grover's search algorithm — all verified experimentally using Qiskit's AerSimulator.

| Notebook | Concept | Key Result |
|----------|---------|------------|
| `01_quantum_gates.ipynb` | X, H, Z, CNOT, Toffoli gates | All 5 gates verified ✅ |
| `02_grovers_algorithm.ipynb` | Grover's search algorithm | \|11⟩ found 1000/1000 times ✅ |

---

## Quantum Gates — Reference

---

### Gate 1 · Pauli-X (Quantum NOT)

Flips the qubit state completely. The quantum equivalent of a classical NOT gate.

```
|0⟩ → |1⟩
|1⟩ → |0⟩

Matrix: [ 0  1 ]      Circuit: ─[ X ]─
        [ 1  0 ]

On Bloch Sphere: 180° rotation around X axis
```

```
Experimental result (1000 shots):
  Input |0⟩ → {'1': 1000}   always flips, no randomness
```

---

### Gate 2 · Hadamard (H)

Creates equal superposition. The most important gate in quantum computing.

```
|0⟩ → (|0⟩ + |1⟩)/√2  =  |+⟩
|1⟩ → (|0⟩ - |1⟩)/√2  =  |-⟩

Matrix: [ 1   1 ] / √2      Circuit: ─[ H ]─
        [ 1  -1 ]

Key property: H · H = Identity (self-inverse)
```

```
Experimental results (1000 shots):
  H once  → {'0': 497, '1': 503}   equal superposition
  H twice → {'0': 1000}            returns to |0⟩
```

---

### Gate 3 · Pauli-Z (Phase Flip)

Leaves |0⟩ unchanged but flips the phase of |1⟩. Phase is invisible until interference.

```
|0⟩ →  |0⟩   (no visible change)
|1⟩ → -|1⟩   (phase flips)

Matrix: [ 1   0 ]      Circuit: ─[ Z ]─
        [ 0  -1 ]
```

```
Experimental results (1000 shots):
  Z on |0⟩   → {'0': 1000}   no visible change
  H + Z + H  → {'1': 1000}   phase kickback acts like X gate
```

---

### Gate 4 · CNOT (Controlled-X)

Two-qubit gate. Flips the target qubit only when the control qubit is |1⟩.

```
|00⟩ → |00⟩
|01⟩ → |01⟩
|10⟩ → |11⟩  ← target flips
|11⟩ → |10⟩  ← target flips

Circuit: control ─■─
                  │
         target ─[X]─
```

```
Experimental truth table (1000 shots each):
  |00⟩ → |00⟩   unchanged
  |01⟩ → |10⟩   flipped
  |10⟩ → |11⟩   flipped
  |11⟩ → |01⟩   flipped
```

---

### Gate 5 · Toffoli (CCX)

Three-qubit gate. Flips the target only when both controls are |1⟩. Quantum AND gate.

```
|110⟩ → |111⟩  ← both controls = 1, target flips
|111⟩ → |110⟩  ← both controls = 1, target flips
all other inputs → unchanged

Circuit: control1 ─■─
         control2 ─■─
           target ─[X]─
```

```
Experimental truth table (1000 shots each):
  |000⟩ → |000⟩   unchanged
  |010⟩ → |010⟩   unchanged
  |100⟩ → |100⟩   unchanged
  |110⟩ → |111⟩   flipped
  |111⟩ → |110⟩   flipped
```

---

### Gates Summary

| Gate | Qubits | Operation | Bloch Sphere |
|------|--------|-----------|--------------|
| X | 1 | Bit flip \|0⟩↔\|1⟩ | 180° around X |
| H | 1 | Superposition | 90° toward equator |
| Z | 1 | Phase flip | 180° around Z |
| CNOT | 2 | Conditional flip | Entangles qubits |
| Toffoli | 3 | Quantum AND | Universal gate set |

---

## Grover's Algorithm

### The Problem

```
Searching an unsorted database of N items:
  Classical → O(N)   check items one by one
  Grover's  → O(√N)  quantum amplitude amplification

N = 1,000,000 items:
  Classical → 1,000,000 checks
  Grover's  →     1,000 checks
```

### How It Works

```
Step 1: Initialize
  Apply H to all qubits → equal superposition
  Every state has probability 1/N

Step 2: Oracle
  Marks the target state by flipping its phase
  Target |11⟩ → phase becomes negative

Step 3: Diffusion
  Amplifies the marked state
  Suppresses all other states

Step 4: Measure
  Target state appears with high probability
```

### Circuit

```
q_0: ─[H]─■─[H]─[X]─■─[X]─[H]─[M]─
           │          │
q_1: ─[H]─■─[H]─[X]─■─[X]─[H]─[M]─

     init  oracle    diffusion   measure
```

### Results

```
Searching for |11⟩ among 4 states (1000 shots):

  |00⟩  ░░░░░░░░░░░░░░░░░░░░    0  (0%)
  |01⟩  ░░░░░░░░░░░░░░░░░░░░    0  (0%)
  |10⟩  ░░░░░░░░░░░░░░░░░░░░    0  (0%)
  |11⟩  ████████████████████ 1000  (100%)

  Classical random baseline: 250 per state (25%)
```

---

## Tech Stack

```python
qiskit          >= 1.0.0
qiskit-aer      >= 0.17.2
matplotlib      >= 3.7.0
python-dotenv   >= 1.0.0
numpy           >= 1.24.0
jupyter         >= 7.0.0
```

---

## Setup

```bash
git clone https://github.com/Akhila707/IBM-Quantum-FoundationsDay3.git
cd IBM-Quantum-FoundationsDay3
pip install -r requirements.txt
jupyter notebook
```

---

## Project Structure

```
IBM-Quantum-FoundationsDay3/
│
├── notebooks/
│   ├── 01_quantum_gates.ipynb
│   └── 02_grovers_algorithm.ipynb
│
├── results/
│   ├── gates_results.png
│   └── grovers_results.png
│
├── .env
├── .gitignore
├── requirements.txt
└── README.md
```

---

## Sprint Progress

```
Day 01  ──  ✅  Qiskit setup · Hello Quantum · first IBM cloud circuit
Day 02  ──  ✅  Superposition · Entanglement · Multi-gate circuits
Day 03  ──  ✅  Gates deep-dive · Grover's algorithm
Day 04  ──  ⬡   Variational Algorithm Design · VQE · parametric circuits
Day 05  ──  ·   QAOA · optimisation landscape · Qiskit Patterns
Day 06  ──  ·   Quantum Error Mitigation · noise models · ZNE
Day 07  ──  ·   Review · run all experiments on real IBM hardware
·
·
Day 20  ──  ·   Final push · 50+ applications · LinkedIn article
```

---

## Security

- Credentials stored in `.env`, excluded from version control via `.gitignore`
- All notebooks run on local AerSimulator — no token required to reproduce results

---

<div align="center">

[![GitHub](https://img.shields.io/badge/Akhila707-181717?style=flat-square&logo=github)](https://github.com/Akhila707)
&nbsp;·&nbsp;
[![IBM Quantum](https://img.shields.io/badge/IBM%20Quantum-052FAD?style=flat-square&logo=ibm&logoColor=white)](https://quantum.ibm.com)

</div>
