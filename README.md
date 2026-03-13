# Measuring Quantum Preimage Attacks: Classical vs. Quantum-Native Hashing

This project provides an experimental framework for evaluating the security of two hashing paradigms **Classical-Style logic** and **Quantum-Native entanglement** against a preimage attack based on **Grover's Algorithm**.

By executing these attacks on the IBM **Heron-class** hardware, the circuit depth and success probability is quantified to analyse how different hashing structures influence vulnerability to quantum cryptanalysis.

## Abstract
The project investigates the security of two distinct hashing paradigms against quantum cryptanalysis (based on Grover’s algorithm). While Grover’s Algorithm offers a theoretical quadratic speedup for finding hash preimages, the overhead of implementing the algorithm on real hardware can negate these gains. This project compares Classical-Style Hashing (translated reversible logic) against Quantum Hashing (rotation and entanglement-based logic). By benchmarking these on the IBM Heron architecture, the project aims to show how increased circuit depth and computation costs impact the success probability of preimage attacks. Performing a head-to-head benchmark can determine if a shallow quantum hash is more secure than a deep classical hash due to noise accumulation.
 
---

## Overview

The core of this research is a "Quantum Preimage Attack." I attempt to find the original input (preimage) that produces a specific target hash. 

### Comparisons:
*   **Classical Hashing**: Constructed using reversible versions of classical gates (CNOT, Toffoli).
*   **Quantum Hashing**: Built using rotations and entanglement, leveraging natural quantum states.

---

## Findings

### 1. Hardware Paradox
The experimental data reveals a significant trade-off in resource allocation. While the Quantum-Native hash uses gates that are more "natural" for the quantum hardware, the entanglement required for high diffusion leads to **greater circuit depth** (11 layers for quantum hash vs 9 layers for the classical-style hash).

### 2. Successful Preimage Identification
The Grover Search successfully converged on the correct preimages despite hardware noise.

| Target Hash | Identified Preimage | Peak Count (on `ibm_fez`) |
| :--- | :--- | :--- |
| **101** | `100` | 1,361 hits |
<img width="630" height="470" alt="Quantum_101" src="https://github.com/user-attachments/assets/0e193ac8-76df-47a4-b67b-ccc3efb1ed62" />


| Target Hash | Identified Preimage | Peak Count (on `ibm_marrakesh`) |
| :--- | :--- | :--- |
| **010** | `110` | 1,446 hits |
<img width="630" height="470" alt="Quantum_010" src="https://github.com/user-attachments/assets/08410ac2-6201-4b00-b805-782c75c3023b" />


The results showed a clear Signal-to-Noise Ratio, proving that Heron-class processors can maintain high fidelity even at the depths required for Grover iterations.

---

## Results

Below is the comparison of the Quantum execution results for the two target preimages. Note the distinct spikes at the correct bitstrings compared to the "noise floor" of the incorrect states.

![Quantum Attack Results Comparison](quantum_attack_results.png)

### Mathematical Verification
Using a statevector simulator, I confirmed that the Quantum-Native design uses **Probabilistic Mapping**. 
*   Unlike classical one-to-one mapping, input `100` results in a superposition where **101 and 010** are the most likely hashes (32.01% each).
*   This "One-to-Many" mapping makes reverse-engineering significantly more complex for classical systems, as a whole statevector must be reconstructed.

| Hash Output (Bitstring) | Probability |
| :--- | :--- |
| **101** | **32.01%** |
| **010** | **32.01%** |
| 000 | 10.67% |
| 111 | 10.67% |
| 011 | 5.49% |
| 100 | 5.49% |
| 110 | 1.83% |
| 001 | 1.83% |

---

## Circuit Analysis

| Metric | Classical Hash | Quantum Native |
| :--- | :--- | :--- |
| **Attack Depth** | 9 | 11 |
| **Gate Complexity** | High (Toffoli-heavy) | Moderate (CNOT-heavy) |

---

# Quantum vs Classical
Below is a comparison of the Quantum execution results vs the classical execution results for bitstring 101.

<img width="640" height="480" alt="classical_vs_quantum_attack_results" src="https://github.com/user-attachments/assets/7fa90a54-ec0a-4016-b9b8-81277d9f8629" />

* **The Classical Results**: The data shows a singular amplified peak. This is the result of Deterministic Mapping, where the classical-style hash only has one correct key. Because bitstring 011 is the only key that mathematically resolves to target 101, Grover's Algorithm focuses the amplification power on that single state.

* **The Quantum Results**: These results show a lower primary peak and notably, a secondary peak. This distribution is the result of Probabilistic Mapping, where the Quantum-native hash acts like a probability vector. Instead of one correct answer, multiple inputs (100 and 110) can lead to the target of 101, causing Grover's Algorithm to share the amplification across multiple valid states

### Why the Results Don't Converge
The reason the spikes do not align at the same value is that they are solving two different mathematical puzzles to reach the same goal. The Classical Hash uses a reversible translation of Boolean logic (Toffoli/CNOT), while the Quantum-Native Hash uses rotations and rings of entanglement. Because the internal workings of these two functions is different, the preimage required to produce target 101 is different for each.

### Hardware Performance on the Heron Chip
Executing these circuits on ibm_fez and ibm_marrakesh showcases the robustness of the Heron architecture. The fact that the target states are ~5x more frequent than the incorrect states proves that modern error suppression and high-fidelity gates are making small-scale quantum cryptanalysis more practical.

# Summary
This study concludes that while quantum-native hashes leverage entanglement to create complex signatures with fewer gates, the total circuit depth remains the primary vulnerability. For a hash to be truly quantum-resistant, it must not only be mathematically complex but also designed to minimize the T-gate complexity and depth during a Grover-style attack.

---
# References
*  Ablayev, Farid, and Alexander Vasiliev. “Quantum Hashing.” arXiv:1310.4922. Preprint, arXiv, October 18, 2013. https://doi.org/10.48550/arXiv.1310.4922.
*  Gilkolae, Ramin Rezvani. “Quantum Resource Analysis of Low-Round Keccak/SHA-3 Preimage Attack: From Classical 2^57.8 to Quantum 2^28.9 Using Qiskit Modeling.” arXiv:2512.14759. Preprint, arXiv, December 15, 2025. https://doi.org/10.48550/arXiv.2512.14759.
*  Grover, Lov K. “A Fast Quantum Mechanical Algorithm for Database Search.” arXiv:quant-Ph/9605043. Preprint, arXiv, November 19, 1996. https://doi.org/10.48550/arXiv.quant-ph/9605043.
*  Henderson, Jessie M., Elena R. Henderson, Aviraj Sinha, Mitchell A. Thornton, and D. Michael Miller. “Automated Quantum Oracle Synthesis with a Minimal Number of Qubits.” arXiv:2304.03829. Preprint, arXiv, April 7, 2023. https://doi.org/10.48550/arXiv.2304.03829.
*  Preskill, John. “Quantum Computing in the NISQ Era and Beyond.” arXiv:1801.00862. Preprint, arXiv, July 31, 2018. https://doi.org/10.48550/arXiv.1801.00862.
*  Zhang, Kun, Kwangmin Yu, and Vladimir Korepin. “Quantum Search on Noisy Intermediate-Scale Quantum Devices.” arXiv:2202.00122. Preprint, arXiv, September 27, 2022. https://doi.org/10.48550/arXiv.2202.00122.

