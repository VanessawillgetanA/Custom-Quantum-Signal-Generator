# End-to-End Superconducting Qubit Readout Simulation

A quantum measurement simulation that models the path from a superconducting qubit and microwave resonator to noisy I/Q measurement data and qubit-state classification.

## Overview

In superconducting quantum processors, the state of a qubit can be measured indirectly through a coupled microwave resonator.

This project simulates that measurement chain:

```text
Qubit
  ↓
Microwave Resonator
  ↓
Dispersive Readout
  ↓
Complex I/Q Signal
  ↓
Measurement Noise
  ↓
Digital Signal Processing / GNU Radio
  ↓
State Classification
  ↓
|0⟩ or |1⟩
```

The goal is to connect quantum physics with microwave measurement, signal processing, and data-driven state discrimination in a single simulation workflow.

## What This Project Demonstrates

- Qubit and resonator modeling
- Tensor-product quantum systems
- Qubit-resonator coupling
- Time-dependent microwave driving
- Open quantum-system simulation
- Qubit energy relaxation (T1)
- Qubit dephasing (T2)
- Resonator energy decay (κ)
- Dispersive resonator readout
- Complex resonator response
- I/Q measurement generation
- Synthetic measurement noise
- GNU Radio signal visualization and frequency-domain analysis
- Threshold-based qubit-state discrimination
- Threshold optimization
- Logistic regression using I/Q features
- Confusion matrices
- Precision, recall, and F1 evaluation

## Physics Model

The first stage models a qubit coupled to a quantized microwave resonator.

The joint state is represented as:

```text
|ψ⟩ = |qubit⟩ ⊗ |resonator⟩
```

The simulation includes:

- Qubit frequency: `wq = 5.0`
- Resonator frequency: `wr = 5.0`
- Coupling strength: `g = 0.1`
- Resonator Hilbert-space dimension: `N = 10`

The Hamiltonian includes the qubit energy, resonator energy, qubit-resonator interaction, and microwave drive.

The simulation is performed using QuTiP's master-equation solver.

## Dissipation

Real quantum systems are not perfectly isolated, so the simulation includes several sources of dissipation:

```text
T1  → qubit energy relaxation
T2  → qubit dephasing
κ   → resonator energy decay
```

The current simulation uses:

```text
T1 = 40
T2 = 30
κ  = 0.05
```

The quantum evolution is calculated using QuTiP's `mesolve`.

## Dispersive Readout

The project then models the measurement process using a dispersive readout model.

The key idea is that the qubit state changes the effective response of the resonator.

The resonator therefore produces different complex responses for the two qubit states:

```text
|0⟩ → α₀
|1⟩ → α₁
```

where

```text
α = ⟨a⟩
```

is the complex resonator amplitude.

The measured signal can be represented as:

```text
I = Re(α)
Q = Im(α)
```

The simulation produces distinct I/Q responses for `|0⟩` and `|1⟩`.

## Noisy Measurement Data

To model measurement uncertainty, Gaussian noise is added independently to the I and Q components.

For each measurement shot:

```text
I = Re(α) + noise
Q = Im(α) + noise
```

The current dataset contains:

```text
1000 measurement shots
Noise level = 0.5
```

The generated data is saved as:

```text
readout_shots_IQ.csv
readout_shots_IQ_float32.bin
```

The CSV contains:

```text
I,Q,label
```

where:

```text
0 → |0⟩
1 → |1⟩
```

## Signal Processing with GNU Radio

The generated complex I/Q data is also processed in GNU Radio.

The GNU Radio flowgraph is used to visualize and analyze the simulated measurement signal in both the time and frequency domains.

The processing chain includes:

```text
File Source
    ↓
Low-Pass Filtering
    ↓
Decimation
    ↓
I/Q Separation
    ↓
Time-Domain Visualization
    ↓
Frequency-Domain Analysis
    ↓
I/Q Constellation
```

This provides a signal-processing view of the simulated quantum measurement data.

## Qubit-State Classification

The project evaluates two approaches for distinguishing the qubit states.

### 1. Threshold Classifier

The simplest classifier uses the in-phase component:

```text
I > threshold → |1⟩
I ≤ threshold → |0⟩
```

The threshold is optimized by testing a range of possible threshold values and selecting the value that produces the highest classification accuracy.

### 2. Logistic Regression

A second classifier uses both I and Q:

```text
X = [I, Q]
```

A logistic regression model is implemented directly with NumPy using gradient descent.

No machine-learning library is required.

The model produces:

```text
P(|1⟩)
```

and classifies the measurement as:

```text
P(|1⟩) ≥ 0.5 → |1⟩
P(|1⟩) < 0.5  → |0⟩
```

## Classification Results

The notebook evaluates classification performance using:

- Accuracy
- Confusion matrix
- Precision
- Recall
- F1 score

For the current generated dataset, the logistic-regression classifier achieved:

```text
Accuracy: 91.3%
```

The resulting confusion matrix was:

```text
              Predicted
             |  0   |  1
-------------+------+------
Actual   0   | 445  | 45
         1   | 42   | 468
```

The corresponding F1 scores were approximately:

```text
|0⟩ F1: 0.911
|1⟩ F1: 0.915
```

Results will vary if the synthetic measurement data is regenerated with a different random realization.

## Project Structure

```text
superconducting-qubit-readout-simulation/
│
├── QubitResonatorReadoutClean.ipynb
├── readout_shots_IQ.csv
├── readout_shots_IQ_float32.bin
├── readout_flowgraph.grc
├── README.md
│
└── figures/
    ├── qubit_dynamics.png
    ├── iq_readout.png
    ├── threshold_classification.png
    ├── final_classification.png
    ├── gnu_radio_time.png
    ├── gnu_radio_frequency.png
    └── gnu_radio_constellation.png
```

## Software

- Python
- NumPy
- QuTiP
- Matplotlib
- GNU Radio
- Jupyter Notebook

## Running the Project

### 1. Install Python Dependencies

```bash
pip install numpy matplotlib qutip jupyter
```

GNU Radio should be installed separately.

### 2. Open the Notebook

```bash
jupyter notebook
```

Open:

```text
QubitResonatorReadoutClean.ipynb
```

### 3. Run the Notebook

Run the cells from top to bottom.

The notebook will:

1. Build the qubit-resonator system
2. Construct the Hamiltonian
3. Add microwave driving
4. Add dissipation
5. Simulate the quantum dynamics
6. Calculate resonator readout responses
7. Generate noisy I/Q measurements
8. Save the measurement data
9. Perform threshold classification
10. Optimize the classification threshold
11. Train a NumPy logistic regression model
12. Evaluate classification performance

### 4. Open the GNU Radio Flowgraph

Open:

```text
readout_flowgraph.grc
```

Run the flowgraph to visualize the generated I/Q signal in the time domain, frequency domain, and constellation plane.

## Results Visualization

The project produces several useful visualizations.

### I/Q Measurement Cloud

The simulated measurement produces separate regions corresponding to the two qubit states.

```text
        Q
        ↑

        • • •
      • • • • •        |1⟩

----------------------------→ I

      • • • • •        |0⟩
        • • •
```

Measurement noise causes the distributions to overlap, making state discrimination imperfect.

### GNU Radio Analysis

The GNU Radio stage provides:

- Time-domain I/Q signals
- Frequency-domain spectrum
- I/Q constellation visualization
- Magnitude measurements

## Limitations

This is a simulation and does not represent a complete experimental quantum-readout system.

The project uses simplified parameters and synthetic measurement noise rather than experimentally measured hardware data.

The GNU Radio stage is currently used for signal processing and visualization rather than directly driving the final classifier.

## Future Improvements

Potential extensions include:

- Generate multi-sample readout waveforms for every measurement shot
- Implement matched filtering
- Add digital downconversion
- Implement I/Q integration over a readout window
- Model amplifier noise and measurement-chain noise
- Include resonator ring-up and ring-down dynamics
- Add more realistic dispersive coupling parameters
- Compare multiple state-discrimination algorithms
- Connect processed GNU Radio output directly to the classifier
- Replace synthetic noise with experimentally measured I/Q data

## Related Project

This project focuses on the **physics and signal-processing side of qubit readout**.

A separate project focuses on **machine-learning-based superconducting qubit state discrimination**.

Together, the projects form a larger workflow:

```text
Quantum System
     ↓
Resonator Readout
     ↓
I/Q Measurement
     ↓
DSP
     ↓
Machine Learning
     ↓
Qubit State Classification
```

## Author

**Vanessa Knight** (for all the code)
**ChatGPT** as my mentor and the one who wrote this ReadMe + helped me refine the markdown descriptions in my code. 
Thankyou very much Richard, ChatGPT, for all your help! 😊



Electrical Engineering  
UCLA

Focus: Quantum Science, Quantum Hardware, Microwave Systems, and Signal Processing
