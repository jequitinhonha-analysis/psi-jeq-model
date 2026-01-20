# GitHub Copilot Instructions for PSI_YÉKIT Model

## Project Overview

**PSI_YÉKIT** ($\Psi_{Yékit}$) is a Bayesian state-space model for predicting systemic socio-environmental collapse risk in Brazil's Jequitinhonha Valley. It combines climate hazards (H), hydrological exposure (E), institutional vulnerability (V), and upstream land use (L) into a latent instability index ($\Psi$).

- **Primary language**: Portuguese (documentation, variables, comments)
- **Technical stack**: R ≥4.3.0, Stan ≥2.32.0 (via `cmdstanr`), Bayesian MCMC
- **Domain**: Academic research, environmental justice, hydro-political risk modeling
- **Repository focus**: Data curation, reproducibility, and rigorous documentation (not active code development)
- **Dataset temporal coverage**: 2015-01 to 2024-12 (120 monthly observations)—anchored to validation period (training: 2015-2022, test: 2023-2024)

## Critical Context

### 1. This is a **Data & Documentation Repository**

- **No active Stan models or R scripts** currently exist in the workspace—only data artifacts and extensive documentation
- The README describes a _planned_ workflow (`scripts/01_preprocess_data.R`, `model/psi_jeq.stan`) that is **not yet implemented**
- Primary artifacts are:
  - [data/DATA_DICTIONARY.csv](../data/DATA_DICTIONARY.csv): Complete metadata for all variables
  - [docs/data/PROVENANCE.md](../docs/data/PROVENANCE.md): Exhaustive traceability of data transformations
  - [docs/data/ANONYMIZATION.md](../docs/data/ANONYMIZATION.md): k-anonymity (k≥5) compliance procedures
  - Technical PDFs: Full mathematical specification, annexes, and reports (in Portuguese/Spanish/English)

### 2. Licensing is **Artifact-Specific** (Not Repo-Wide)

See [LICENSES.md](../LICENSES.md) for the dual-licensing strategy:
- **Code & data**: CC0 1.0 (Public Domain)—applies to any scripts, Stan models, CSVs, outputs
- **Text & reports**: CC BY 4.0—applies to README, PDFs, documentation (attribution required)

When creating new files:
- Scripts, data files → Add CC0 header or no license (implicit CC0)
- Markdown docs, technical writeups → Add CC BY 4.0 notice

⚠️ **Current issue**: The active branch (`license/clarify`) is resolving inconsistencies between LICENSE file (CC0) and README badge (CC BY 4.0).

### 3. Data Privacy & LGPD Compliance

All data handling must respect **k-anonymity (k≥5)**:
- Municipal-level monthly aggregation (never individual-level)
- Sensitive variables (`V_income_percapita_brl`, `V_infant_mortality_rate`, etc.) are suppressed when population <5
- Use `quality_flag` field to document: `observed`, `estimated`, `interpolated`, `calculated`, `suppressed`, `census`
- See [docs/data/ANONYMIZATION.md](../docs/data/ANONYMIZATION.md) for full protocol

## Development Workflows

### Data Management

1. **Variable naming convention**: `<Dimension>_<description>_<unit>`
   - Dimensions: `H` (Hazard), `E` (Exposure), `V` (Vulnerability), `L` (Land use)
   - Example: `H_precipitation_mm`, `V_poverty_rate_pct`
   
2. **Date format**: `year_month` as `YYYY-MM` (ISO 8601), monthly resolution
Temporal coverage**: 2015-01 to 2024-12 (120 months)
   - This is the **official published range** for PSI_YÉKIT v1.0
   - Validation split: training (2015-2022), test (2023-2024)
   - Census years used for interpolation: 2000, 2010, 2022 (but only 2015-2024 is published)

4. **
3. **CSV standards**:
   - UTF-8 encoding
   - Comma separator
   - Decimal point (`.`)
   - Missing values as `NA`

### If Creating Stan Models (Future Work)

- Main model should implement Dynamic Generalized Linear Model (DGLM):
  - Latent state: $\Psi_t \sim N(\alpha + \beta_H H_t + \beta_E E_t + \beta_V V_t + \rho \Psi_{t-1}, \sigma_{process})$
  - Observation: $Y_t \sim \text{Bernoulli}(\text{logit}^{-1}(\Psi_{t-L}))$
- Use `cmdstanr` (not `rstan`)—see README prerequisites
- Output posterior draws with municipality-month resolution
- Separate latent $\Psi(t)$ outputs from observables (already done in data structure)

### If Creating R Scripts (Future Work)

Expected workflow from README:
1. `scripts/01_preprocess_data.R`: Reads `data/*.csv`, applies transformations in [PROVENANCE.md](../docs/data/PROVENANCE.md), outputs Stan-ready data list
2. `scripts/02_fit_model.R`: Calls CmdStan HMC sampler (4 chains, 2000 warmup, 3000 sampling)
3. `scripts/03_generate_report.R`: Produces diagnostics (trace plots, $\hat{R}$, ESS), posterior predictive checks, HTML report

Dependencies (from README):
```r
c("tidyverse", "cmdstanr", "posterior", "bayesplot", "sf", "terra", "lubridate")
```

## Key Patterns & Conventions

### Documentation Philosophy

- **Radical transparency**: Every transformation documented in [PROVENANCE.md](../docs/data/PROVENANCE.md) with source IDs, methods, and quality flags
- **Bilingual/trilingual**: Core README in English, docs mix Portuguese/Spanish/English (reflect actual research context)
- **Mathematical rigor**: Use LaTeX notation ($\Psi$, $\beta_H$) consistently; KaTeX in markdown when possible
- **Political framing**: This is explicitly a "Scientific Disobedience" project—accept non-neutral language about extractivism, environmental justice, and institutional critique

### Repository Structure

```
├── data/                    # Metadata only (no raw CSVs committed)
│   ├── DATA_DICTIONARY.csv
│   ├── sources_metadata.csv
│   └── units_metadata.csv
├── docs/                    # Extensive documentation
│   ├── data/
│   │   ├── PROVENANCE.md     # Full data lineage
│   │   ├── ANONYMIZATION.md  # Privacy protocols
│   │   └── README_DATA.md    # Dataset overview
│   └── ZENODO_METADATA.md
├── model/                   # (Future) Stan files
├── scripts/                 # (Future) R workflow scripts
├── outputs/                 # (Future) Figures, tables
├── Technical PDFs           # Versioned technical specifications
└── README.md                # Primary entry point
```

## Critical Files to Review Before Changes

- [README.md](../README.md): Authoritative project description (update sparingly)
- [LICENSES.md](../LICENSES.md): Dual-licensing logic
- [data/DATA_DICTIONARY.csv](../data/DATA_DICTIONARY.csv): Complete variable registry
- [CITATION.cff](../CITATION.cff): Citation metadata (Zenodo DOI placeholders)

## External Dependencies & Integrations

- **Data sources** (from [PROVENANCE.md](../docs/data/PROVENANCE.md)):
  - INMET (climate), ANA (hydrology), DATASUS (health), IBGE (census), MapBiomas (land use)
  - All sources CC BY 4.0 or CC0, except MapBiomas (verify Collection 8 license)
- **Zenodo integration**: Placeholder DOIs in [CITATION.cff](../CITATION.cff) and metadata JSONs
- **GitHub PR workflow**: Active on `license/clarify` branch (see [pr_description.md](../pr_description.md))

## Common Pitfalls

1. **Don't assume code exists**: No `scripts/` or `model/` directories yet—treat README as specification, not reality
2. **Respect language mixing**: Portuguese variable names, comments, and docs are intentional (research context)
3. **Don't simplify licensing**: Dual CC0/CC BY 4.0 scheme is deliberate—consult [LICENSES.md](../LICENSES.md) before adding license headers
4. **Honor k-anonymity**: Never suggest individual-level data or operations that would violate k≥5 threshold
5. **Mathematical notation matters**: Use `$\Psi$` (not `Psi`), maintain subscript conventions ($\beta_H$, not `beta_H`)

## Questions to Clarify with User

- Are Stan models or R scripts planned for this repository, or should they live elsewhere?
- Should future code contributions use English or Portuguese for variable names and comments?
- Is the MapBiomas Collection 8 license confirmed (CC BY vs CC BY-SA affects dataset license)?
