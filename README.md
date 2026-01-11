Based on the comprehensive documentation provided—ranging from the theoretical "Hydro-cosmopolitics" paper to the technical specifications and the scientific manuscript draft—here is a robust, ready-to-use `README.md` for the GitHub repository.

***

# $\Psi_{Yékit}$ (Psi-Jequitinhonha) Model
## A Bayesian Framework for Predicting Systemic Collapse Under Extractivist Pressure

![Status](https://img.shields.io/badge/Status-Audit_Ready-success)
![Version](https://img.shields.io/badge/Version-3.2-blue)
![R](https://img.shields.io/badge/R-%3E%3D4.3.0-blue)
![Stan](https://img.shields.io/badge/Stan-%3E%3D2.32.0-red)
[![License: CC BY 4.0](https://img.shields.io/badge/License-CC_BY_4.0-lightgrey.svg)](https://creativecommons.org/licenses/by/4.0/)

> **"The crisis in Jequitinhonha is not just a distributive environmental conflict, but a tectonic clash between two irreconcilable ontologies: hydro-ontology (life as flow) and extractivist entropy (mineral as inertia)."**

### 📋 Overview

The **$\Psi_{Yékit}$ Model** (or $\Psi_{Jeq}$) is an open-source, Bayesian early-warning system designed to quantify the risk of systemic socio-environmental collapse.

Developed in response to the intensive lithium mining boom ("Green Colonialism") in Brazil's semi-arid Jequitinhonha Valley, the model moves beyond traditional impact assessments. It integrates **climate hazards**, **hydrological exposure**, and **institutional vulnerability** into a single latent instability index ($\Psi$).

When $\Psi$ crosses a critical threshold, it predicts the probability of systemic failure events, defined as:
1.  **Water Rupture:** Physical exhaustion of water supply ($Q < Q_{7,10}$).
2.  **Sanitary Saturation:** Collapse of local health capacity due to heat/pollution.

---

### 🏗️ Theoretical Framework: The H-E-V Trinity

The model operationalizes the IPCC AR6 risk framework using a dynamic Bayesian State-Space architecture.

| Component | Variable | Definition | Source Proxy |
| :--- | :---: | :--- | :--- |
| **Hazard** | $H$ | **Biophysical Urgency.** The intensification of the "Imperial Climate." | **SPEI-12** (Drought) + **Heat Waves** ($T_{max} > P95$). |
| **Exposure** | $E$ | **Resource Friction.** The collision between mining demand and finite supply. | **Water Stress Ratio** (Demand / $Q_{7,10}$) + **Pop. at Risk** (10km buffer). |
| **Vulnerability**| $V$ | **Institutional Collapse.** The "State of Exception" and regulatory capture. | **$\Omega$ Index (Institutional Friction):** Ratio of regulatory vacancies to mining volume. |

---

### 🔧 Installation

The model is implemented in **Stan** (via `cmdstanr` or `rstan`) and **R**.

#### Prerequisites
*   R version $\ge$ 4.3.0
*   Stan version $\ge$ 2.32.0
*   4 CPU Cores (recommended for MCMC chains)
*   16 GB RAM

#### Dependencies
```r
install.packages(c("tidyverse", "cmdstanr", "posterior", "bayesplot", "sf", "terra", "lubridate"))
# Install CmdStan if not already present
cmdstanr::install_cmdstan()
```

---

### 🚀 Usage

#### 1. Clone the Repository
```bash
git clone https://github.com/jequitinhonha-analysis/psi-jeq-model.git
cd psi-jeq-model
```

#### 2. Data Preparation
The `data/` folder contains raw CSVs from INMET (Climate), ANA (Hydrology), and DATASUS (Health). Run the pre-processing script to generate the Stan data dictionary.
```bash
Rscript scripts/01_preprocess_data.R
```

#### 3. Run the Model (MCMC Sampling)
Execute the HMC sampler. This will run 4 chains with 2,000 warm-up and 3,000 sampling iterations.
```bash
Rscript scripts/02_fit_model.R
```

#### 4. Generate Diagnostics & Report
Produces the HTML validation report, trace plots, and posterior predictive checks.
```bash
Rscript scripts/03_generate_report.R
```

---

### 📐 Mathematical Specification

The model is a **Dynamic Generalized Linear Model (DGLM)**.

**1. Latent State Equation (The Invisible Risk):**
The systemic instability $\Psi_t$ evolves as an AR(1) process driven by covariates:
$$ \Psi_t \sim N(\mu_t, \sigma_{process}) $$
$$ \mu_t = \alpha + \beta_H H_t + \beta_E E_t + \beta_V V_t + \rho \Psi_{t-1} $$

**2. Observation Equation (The Visible Collapse):**
Observed failure events ($Y_t$) are manifestations of the latent state, triggered via a logistic link:
$$ Y_t \sim \text{Bernoulli}(p_t) $$
$$ \text{logit}(p_t) = \Psi_{t-L} $$
*(Where $L$ is the lead time, typically 1 month)*

---

### 📊 Key Findings (Jequitinhonha Case Study)

Analysis of data through **December 2025** indicates:

*   **Current Risk Level:** The valley has surpassed the stability baseline by **82%** ($\Psi \approx 1.82$).
*   **Urgency:** Probability of systemic failure in the next 12 months is **23%**.
*   **Tipping Point:** Under the SSP2-4.5 scenario and current mining expansion plans, the "Point of No Return" (runaway collapse) is projected for **November 2026**.
*   **The "Green" Paradox:** We document "NDC Inversion," where ~R$486 million in Climate Funds were used to finance water-intensive mining, effectively subsidizing local climate vulnerability.

---

### 📂 Repository Structure

```
.
├── data/
│   ├── raw/                  # Original datasets (INMET, ANA, DATASUS)
│   ├── processed/            # Anonymized (k=5) and cleaned data
│   └── dictionary.json       # Metadata and variable definitions
├── model/
│   ├── psi_jeq.stan          # Main Stan model file
│   └── psi_jeq_lite.stan     # Simplified version for rapid testing
├── scripts/
│   ├── 01_preprocess_data.R  # Data cleaning and index calculation
│   ├── 02_fit_model.R        # CmdStanR execution script
│   └── 03_generate_report.R  # Visualization and sensitivity analysis
├── outputs/
│   ├── figures/              # Posterior plots and risk trajectories
│   └── tables/               # Summary statistics
├── docs/
│   ├── Technical_Note_01_2026.pdf   # Biophysical Urgency Report
│   └── Model_Specification_v3.2.pdf # Full mathematical details
└── README.md
```

---

### 🛡️ Ethics & "Scientific Disobedience"

This model is not neutral. It is a tool for **Planetary Justice**.
*   **Privacy:** All health data is k-anonymized (k=5) to protect patient privacy while revealing public health trends.
*   **Transparency:** All assumptions, priors, and code are open to prevent "black box" policy-making.
*   **Purpose:** This repository supports the concept of "Scientific Disobedience"—using rigorous data science to challenge institutional negligence and "necropolitical" resource extraction.

### 📜 License

*   **Code:** MIT License
*   **Data:** Creative Commons Attribution 4.0 International (CC-BY 4.0)

### 📚 Citation

If you use this model or data, please cite:

> **[Author Name et al.] (2026).** "$\Psi_{Yékit}$: A Bayesian Predictive Model of Systemic Collapse Under Extractivist Pressure – Evidence from Brazil’s Jequitinhonha Valley." *Science* (Under Review). DOI: 10.5281/zenodo.XXXXXX

---

**Contact:** info@yekit.org | [Submit an Issue](https://github.com/jequitinhonha-analysis/psi-jeq-model/issues)
