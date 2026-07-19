# QNoise: From Quantum Phase Noise to Dependent Bitstreams

This repository is the computational companion to the paper

> **From Quantum Phase Noise to Dependent Bitstreams: Measurement-Aware Statistical Inference and Multiscale Diagnostics**  
> Soomin Oh and Brani Vidakovic

The project studies how a temporally evolving latent phase process is transformed by a quantum circuit and measurement protocol into an ordered classical bitstream. Its central statistical message is that the observed dependence is determined jointly by the latent noise and the measurement design. In particular, the Ramsey operating point can preserve, weaken, or conceal long-range dependence.

The repository contains Qiskit 2.x and Qiskit Aer experiments, reproducible statistical simulations, manuscript figures, numerical summaries, and a sentinel-assisted mitigation study.

## Scientific overview

Repeated quantum-circuit executions are often treated as independent shots from a fixed distribution. That approximation can suppress the statistical signatures of slow phase fluctuations, calibration drift, and other time-correlated errors.

The paper and notebook use the following data-generating chain:

```text
latent phase process
        |
        v
quantum circuit and noise channel
        |
        v
ordered binary measurements
        |
        v
time-series diagnosis, prediction, and mitigation
```

For acquisition block \(j\), the reported telegraph mean is modeled as

\[
m(\epsilon_j)
=
\delta-\kappa\cos(\phi_0+\epsilon_j),
\]

where:

- \(\epsilon_j\) is the latent phase perturbation;
- \(\phi_0\) is the Ramsey operating phase;
- \(\delta\) represents asymmetric readout bias;
- \(\kappa\) is the remaining contrast after visibility loss and readout error.

Conditional on the latent phase, the shots are Bernoulli. After the latent phase is marginalized out, the ordered measurements are dependent.

The main theoretical results studied computationally are:

1. an exact covariance-transfer formula from the latent Gaussian phase process to the measured telegraph process;
2. an operating-point effect in which quadrature transmits covariance to first order, while a fringe extremum transmits it to second order;
3. an exact block-variance decomposition separating conditional shot noise from variation across latent hardware states;
4. a design principle showing that additional shots within one quasi-static block do not replace temporal replication across blocks;
5. a sentinel-based strategy for predicting phase drift and correcting a nearby target observable.

## Main notebooks

### Executed notebook

```text
QuantumNoise_Qiskit2_Aer_Companion.ipynb
```

This is the fully executed notebook with stored figures, tables, numerical output, package versions, and random seeds.

### Clean source notebook

```text
QuantumNoise_Qiskit2_Aer_Companion_source.ipynb
```

This contains the same code and professional Markdown exposition, but without stored execution output.

## Computational strategy

The notebook separates the quantum and statistical layers.

### Quantum layer

Qiskit Aer density-matrix simulation is used to determine conditional circuit responses under:

- phase-damping channels;
- one- and two-qubit depolarizing channels;
- Ramsey basis rotations;
- Bell and GHZ state preparation;
- finite measurement visibility;
- asymmetric readout assignment.

The first phase-damping experiment applies eight successive channel intervals. The Bell and GHZ studies use composite noise sweeps in which depolarizing error, phase damping, and asymmetric readout are present.

### Statistical layer

After the conditional Ramsey response has been calibrated with Aer, long ordered streams and repeated Monte Carlo experiments are sampled from the corresponding Bernoulli law. This is an exact implementation of the stated hierarchical model:

\[
X_{jr}\mid\epsilon_j
\sim
\text{Bernoulli-derived telegraph observation}.
\]

Aer is therefore used to determine the circuit-to-probability map. The large time-series experiments then simulate efficiently from that calibrated conditional distribution. The notebook does not claim that thousands of separate circuits were executed individually in Aer.

Temporal dependence is introduced only through an ordered latent process indexed by acquisition block. A static Aer channel may change the mean and contrast, but it does not by itself generate laboratory-time dependence.

## Paper-to-notebook correspondence

| Paper component | Notebook experiment | Main output |
|---|---|---|
| Ramsey observation law | Aer calibration of the noisy response curve | `fig00_ramsey_response_calibration.png` |
| Measurement sensitivity | Direct computational-basis readout versus closing-H Ramsey readout | `fig01_phase_damping_basis_sensitivity.png` |
| Bell and GHZ degradation | Population, support, parity, and coherence-sensitive diagnostics | `fig02_entanglement_degradation.png`, `fig11_ghz_size_scaling.png` |
| Latent phase alternatives | Independent, persistent, and shifted phase processes | `fig03_latent_phase_processes.png` |
| Ordered measurement records | Centered cumulative paths | `fig04_ordered_cumulative_paths.png` |
| Dependence diagnostics | Autocorrelation comparison | `fig05_autocorrelation_comparison.png` |
| Frequency-domain diagnostics | Welch power spectra | `fig06_power_spectra.png` |
| Scaling diagnostics | Detrended fluctuation analysis | `fig07_dfa_scaling.png` |
| Multiscale diagnostics | Haar logscale energy | `fig08_haar_logscale.png` |
| Structural change | CUSUM change-point diagnostic | `fig09_change_point_cusum.png` |
| Monte Carlo variability | Replication distribution of the scaling estimate | `fig10_hurst_replication_boxplot.png` |
| Exact covariance transfer | Gaussian formula at quadrature and fringe operation | `fig12_covariance_transfer.png` |
| Aer operating-point comparison | Ordered Aer-calibrated simulations at two phases | `fig12b_aer_operating_point.png` |
| Block variance decomposition | Fixed total-shot budget with varying shots per block | `fig13_block_allocation.png` |
| Sentinel-assisted mitigation | Quadrature and near-fringe phase tracking and correction | `fig14_sentinel_mitigation.png` |

The manuscript uses the most important figures in the main text. Additional diagnostic and latent-process figures may be retained in the repository or supplementary material.

## Controlled ordered-Ramsey experiment

The principal ordered experiment uses:

```text
number of acquisition blocks: J = 128
shots per block:              S = 32
total shots:                  N = 4096
reference phase:              phi0 = pi/2
persistent phase model:       fractional Gaussian noise
Hurst parameter:              H = 0.70
phase standard deviation:     0.26 radians
calibration shift:            0.34 radians after block 64
```

Four regimes are compared:

1. ideal independent measurements;
2. static noisy measurements with Aer-fitted readout bias and contrast;
3. colored phase drift;
4. an abrupt calibration shift.

The notebook evaluates cumulative paths, autocorrelation, spectra, effective sample size, detrended fluctuation analysis, Haar logscale energy, and CUSUM behavior. Pointwise ACF reference lines are graphical aids rather than simultaneous confidence bands. The Welch spectrum uses segments of length 512 with overlap 256. The Haar logscale plot is used as a supporting multiscale diagnostic and is not converted into a Hurst estimate without a declared linear scale range.

## Sentinel-assisted correction

A quadrature sentinel is used to estimate a local phase perturbation before correcting a nearby target observable. The controlled experiment compares:

- the uncorrected target;
- correction based on a quadrature sentinel;
- correction based on a near-fringe sentinel.

The experiment illustrates why a target circuit and its diagnostic sentinel need not use the same operating phase. A target may be chosen for physical robustness, while a quadrature sentinel is chosen for local phase information.

In the controlled notebook experiment, the approximate block-level root mean squared errors are:

```text
uncorrected target:             0.198
quadrature-sentinel correction: 0.107
near-fringe correction:         0.312
```

These values describe statistical post-processing in a controlled simulation. They are not presented as a hardware feedback demonstration.

## Output directories

### `Figs/`

The notebook writes the complete figure set:

```text
fig00_ramsey_response_calibration.png
fig01_phase_damping_basis_sensitivity.png
fig02_entanglement_degradation.png
fig03_latent_phase_processes.png
fig04_ordered_cumulative_paths.png
fig05_autocorrelation_comparison.png
fig06_power_spectra.png
fig07_dfa_scaling.png
fig08_haar_logscale.png
fig09_change_point_cusum.png
fig10_hurst_replication_boxplot.png
fig11_ghz_size_scaling.png
fig12_covariance_transfer.png
fig12b_aer_operating_point.png
fig13_block_allocation.png
fig14_sentinel_mitigation.png
```

### `Results/`

Machine-readable summaries include:

```text
block_allocation_variance.csv
dfa_replication_distribution.csv
entanglement_degradation.csv
ordered_ramsey_summary.csv
phase_damping_basis_sensitivity.csv
ramsey_response_calibration.csv
sentinel_mitigation_summary.csv
run_manifest.json
```

The run manifest records the random seed, package versions, fitted Ramsey-response parameters, experiment settings, and output filenames.

## Repository structure

```text
QNoise/
├── QuantumNoise_Qiskit2_Aer_Companion.ipynb
├── QuantumNoise_Qiskit2_Aer_Companion_source.ipynb
├── Figs/
├── Results/
├── Paper/
│   ├── QuantumNoise03.tex
│   ├── QuantumNoise03.pdf
│   ├── QuantumNoise03.bib
│   └── Figs/
├── README.md
├── requirements.txt
└── environment.yml
```

The filenames inside `Paper/` may be updated as the manuscript advances, but the paper title should remain synchronized with this README and the notebook heading.

## Installation

A clean conda environment can be created with:

```bash
conda env create -f environment.yml
conda activate qnoise
jupyter lab
```

The notebook can also be run with pip:

```bash
python -m pip install -r requirements.txt
jupyter lab
```

The executed notebook records the following principal versions:

```text
Python        3.13.5
Qiskit        2.4.2
Qiskit Aer    0.17.2
NumPy         2.3.5
pandas        2.2.3
PyWavelets    1.8.0
```

The notebook targets Qiskit 2.x. It uses `AerSimulator` and `transpile` and does not use the obsolete `execute` helper.

## Reproducing the results

1. Clone the repository.

```bash
git clone https://github.com/BraniV/QNoise.git
cd QNoise
```

2. Create and activate the environment.

```bash
conda env create -f environment.yml
conda activate qnoise
```

3. Start Jupyter Lab.

```bash
jupyter lab
```

4. Open and run:

```text
QuantumNoise_Qiskit2_Aer_Companion_source.ipynb
```

5. Compare the regenerated files in `Figs/` and `Results/` with the stored outputs in the executed notebook.

The default random seed is:

```text
20260719
```

## Statistical interpretation and limitations

The fractional Gaussian phase process is used as a controlled persistent alternative. It is not asserted to be the unique microscopic model for quantum hardware. A hardware analysis should compare persistent fractional models with short-memory state-space models, random telegraph processes, hidden-state models, and explicit change-point alternatives.

Dependence in an observed bitstream does not by itself establish quantum non-Markovianity. The measured record reflects the complete experimental chain, including the latent environment, state preparation, circuit response, measurement basis, readout error, acquisition order, and scheduling.

Conversely, an apparently independent bitstream does not prove that the environment is memoryless. A poorly chosen operating point can suppress the leading dependence term.

The effective sample size reported in the notebook is a descriptive dependence summary. Under genuine long-range dependence there is no finite classical long-run variance, so scale-dependent or model-based uncertainty is required.

## Extension to quantum hardware

A hardware implementation should preserve the same statistical design while removing explicit Aer channel instructions. Recommended metadata include:

- shot order and timestamps;
- acquisition blocks and job boundaries;
- backend identity;
- physical-qubit mapping;
- transpilation settings;
- circuit order;
- calibration context;
- sentinel-to-target time gap.

A prospective study should interleave quadrature sentinels and target circuits across calibration epochs. The relevant performance criteria are prediction error at the target time, dependence-adjusted uncertainty, mean squared error, interval coverage, and out-of-sample mitigation performance.

## Citation

The computational repository may be cited as:

```bibtex
@software{ohvidakovic2026qnoise,
  author  = {Oh, Soomin and Vidakovic, Brani},
  title   = {{QNoise}: Computational Companion to
             {From Quantum Phase Noise to Dependent Bitstreams}},
  year    = {2026},
  url     = {https://github.com/BraniV/QNoise},
  note    = {Qiskit 2.x and Qiskit Aer computational repository}
}
```

Please also cite the accompanying paper once its final bibliographic information is available.

## Authors

**Soomin Oh**  
Department of Data Science  
Inha University, Korea

**Brani Vidakovic**  
Department of Statistics  
Texas A&M University, USA

## Repository

<https://github.com/BraniV/QNoise>
