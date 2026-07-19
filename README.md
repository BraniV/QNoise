# QNoise: Measurement-Aware Statistical Inference for Quantum Noise

This repository is the computational companion to the paper **From Quantum Phase Noise to Dependent Bitstreams: Measurement-Aware Statistical Inference and Multiscale Diagnostics**.

The notebook implements the paper's central separation between the conditional quantum experiment and the marginal statistical process. Qiskit Aer evaluates density matrices and measurement probabilities for Ramsey, Bell, and GHZ circuits under explicit phase-damping and depolarizing channels. Ordered dependence is then introduced through a latent phase process indexed by acquisition block. Conditional on that process, measurements are Bernoulli; after marginalization, the bitstream is dependent.

## Main notebook

`QuantumNoise_Qiskit2_Aer_Companion.ipynb` is the executed notebook with figures and numerical output. `QuantumNoise_Qiskit2_Aer_Companion_source.ipynb` is the same notebook without stored output.

The notebook reproduces the paper's principal figures and adds a sentinel-assisted mitigation experiment. It covers:

- basis sensitivity to phase damping;
- Aer calibration of the noisy Ramsey response;
- the exact covariance-transfer proposition;
- the measurement-induced change of visible memory order;
- the block variance decomposition and shot-allocation design;
- Bell and GHZ population and stabilizer diagnostics;
- ordered Ramsey streams, ACF, spectra, DFA, wavelet logscale energy, and CUSUM;
- replication variability of the scaling estimate;
- quadrature-sentinel phase tracking and time-local target correction.

## Installation

A clean conda environment can be created with:

```bash
conda env create -f environment.yml
conda activate qnoise
jupyter lab
```

Alternatively:

```bash
python -m pip install -r requirements.txt
jupyter lab
```

The notebook was executed with Qiskit 2.4.2 and Qiskit Aer 0.17.2. Qiskit 2.x is required; the notebook uses `AerSimulator` and `transpile` rather than the obsolete `execute` helper.

## Repository structure

```text
QNoise/
  QuantumNoise_Qiskit2_Aer_Companion.ipynb
  QuantumNoise_Qiskit2_Aer_Companion_source.ipynb
  Figs/
  Results/
  Paper/
    QuantumNoise03_Companion.tex
    QuantumNoise03_Companion.pdf
    QuantumNoise03.bib
    Figs/
  README.md
  requirements.txt
  environment.yml
```

`Figs` contains regenerated manuscript figures. `Results` contains machine-readable CSV summaries and `run_manifest.json`, which records package versions, random seeds, fitted Ramsey-response parameters, and output filenames.

## Statistical interpretation

A static Aer channel can change the mean, contrast, and marginal error probability, but it does not by itself model drift across laboratory time. Temporal variation is represented explicitly through ordered blocks whose circuit phase is driven by a specified latent process. This distinction prevents static gate-noise simulation from being misinterpreted as evidence about hardware memory.

The fractional Gaussian phase model is used as a controlled persistent alternative. It is not asserted to be a unique microscopic model for a quantum processor. Hardware analysis should compare fractional, short-memory state-space, telegraph, hidden-state, and change-point explanations.

## Hardware extension

A hardware study should interleave quadrature sentinel circuits with target circuits, preserve timestamps and job boundaries, and record backend, qubit mapping, transpilation, and calibration metadata. The primary empirical quantities are the covariance-transfer gain, the long-run variance of target observables, and the bias-variance effect of time-local mitigation.
