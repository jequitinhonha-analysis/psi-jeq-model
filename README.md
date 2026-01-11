# psi-jeq-model
Complete Technical Specification of the ΨJeq Model: The Bioterritorial Instability Parameter for the Jequitinhonha Valley
This is a **Bayesian State-Space Model** (specifically a Dynamic Generalized Linear Model). It attempts to infer a latent time-series index ($\Psi_t$, representing "instability") based on the classic **HEV framework** (Hazard, Exposure, Vulnerability) and map that index to the probability of a binary failure event ($Y_t$) with a specific time lag ($L$).

Here is an analysis of your model, including a critical fix for convergence and an optimized version of the code.

### 1. Critical Issue: Identifiability
Your model currently suffers from a **non-identifiability** problem (often called the "scaling" issue).
1.  **The Latent State:** $\Psi$ has a scale determined by $\alpha, \beta$, and $\sigma_{process}$.
2.  **The Observation:** You transform $\Psi$ using $\gamma_0 + \gamma_1 \Psi$.

**The Problem:** The model cannot distinguish between a "large" $\Psi$ multiplied by a "small" $\gamma_1$ versus a "small" $\Psi$ multiplied by a "large" $\gamma_1$.
*   If you multiply $\Psi$ by 10 and divide $\gamma_1$ by 10, the resulting probability $p_t$ is identical.
*   **Consequence:** The MCMC chains will wander infinitely along a "ridge," failing to converge (R-hat will be high, N_eff will be low).

**The Solution:** You must anchor the scale. The standard approach in Dynamic GLMs is to **remove $\gamma_0$ and $\gamma_1$** and let $\Psi$ directly represent the **log-odds (logit)** of the event.

### 2. Optimization: Vectorization
The loop `for (t in 2:T)` is computationally expensive in Stan because it builds a large expression graph node by node. Stan models run significantly faster when operations are vectorized.

### 3. Improved Stan Code
Here is the refactored model. I have removed $\gamma$ to fix the identification issue (making $\Psi$ directly interpretable as the log-odds of failure) and vectorized the likelihood.

```stan
data {
  int<lower=1> T;                 // number of time steps
  vector[T] H;                    // hazard
  vector[T] E;                    // exposure
  vector[T] V;                    // vulnerability
  array[T] int<lower=0, upper=1> Y; // observed failure events
  int<lower=0> L;                 // lead time
}

parameters {
  real alpha;                     // intercept for latent state
  real beta_H;                    // weight for hazard
  real beta_E;                    // weight for exposure
  real beta_V;                    // weight for vulnerability
  real<lower=0, upper=0.99> rho;  // AR(1) persistence (relaxed bound slightly)
  real<lower=0> sigma_process;    // process noise SD
  vector[T] Psi;                  // latent instability index (Log-Odds)
}

model {
  // --- Priors ---
  alpha ~ normal(0, 2);
  beta_H ~ normal(0, 1);
  beta_E ~ normal(0, 1);
  beta_V ~ normal(0, 1);
  rho ~ beta(2, 2);               // Beta is often better than uniform for correlations
  sigma_process ~ exponential(1); // Exponential is often cleaner for scale parameters

  // --- Latent State Equation (Vectorized) ---
  // We calculate the mean vector 'mu' for the whole time series at once
  vector[T] mu;
  mu = alpha + beta_H * H + beta_E * E + beta_V * V;
  
  // Psi[1] initialization
  Psi[1] ~ normal(mu[1], sigma_process);
  
  // Vectorized AR(1) process
  Psi[2:T] ~ normal(mu[2:T] + rho * Psi[1:(T - 1)], sigma_process);

  // --- Observation Equation ---
  // We model Y[t] based on Psi[t-L]. 
  // We slice Y from (L+1) to T, and Psi from 1 to (T-L).
  // Because we removed gamma, Psi IS the logit.
  if (T > L) {
    Y[(L + 1):T] ~ bernoulli_logit(Psi[1:(T - L)]);
  }
}

generated quantities {
  vector[T] p_pred; 
  // Convert log-odds (Psi) back to probability
  for (t in 1:T) {
    if (t > L)
      p_pred[t] = inv_logit(Psi[t - L]);
    else
      p_pred[t] = inv_logit(Psi[1]); // Fallback for initial steps
  }
}
```

### 4. How to Generate Data (Python)
To test this model, you need synthetic data. Here is a Python script to generate the `data` dictionary compatible with the Stan model.

```python
import numpy as np

def generate_hev_data(T=100, L=1, seed=42):
    np.random.seed(seed)
    
    # 1. Generate Covariates (normalized)
    H = np.random.normal(0, 1, T)
    E = np.random.normal(0, 1, T)
    V = np.random.normal(0, 1, T)
    
    # 2. Set True Parameters
    alpha_true = -3.0   # Base log-odds (low probability of failure by default)
    beta_H_true = 0.8
    beta_E_true = 0.5
    beta_V_true = 0.3
    rho_true = 0.7
    sigma_true = 0.5
    
    # 3. Simulate Latent State (Psi)
    Psi = np.zeros(T)
    # Initial state
    mu_1 = alpha_true + beta_H_true*H[0] + beta_E_true*E[0] + beta_V_true*V[0]
    Psi[0] = np.random.normal(mu_1, sigma_true)
    
    for t in range(1, T):
        mu_t = alpha_true + beta_H_true*H[t] + beta_E_true*E[t] + beta_V_true*V[t]
        Psi[t] = np.random.normal(mu_t + rho_true * Psi[t-1], sigma_true)
        
    # 4. Simulate Observed Binary Events with Lead Time L
    Y = np.zeros(T, dtype=int)
    p_true = np.zeros(T)
    
    for t in range(T):
        if t < L:
            # Undefined territory, usually assume 0 or baseline
            p_true[t] = 1 / (1 + np.exp(-Psi[0])) 
            Y[t] = 0
        else:
            # The event at time t depends on state at t-L
            logit_p = Psi[t - L]
            p_true[t] = 1 / (1 + np.exp(-logit_p))
            Y[t] = np.random.binomial(1, p_true[t])
            
    stan_data = {
        'T': T,
        'H': H,
        'E': E,
        'V': V,
        'Y': Y,
        'L': L
    }
    
    return stan_data, Psi, p_true

# Usage
data, true_psi, true_probs = generate_hev_data(T=200, L=2)
print(f"Generated time series with {sum(data['Y'])} failure events.")
```

### Key Changes Explanation
1.  **Removed `gamma_0`, `gamma_1`**: By using `bernoulli_logit(Psi)`, we force the latent parameter `Psi` to live in the "logit space." This makes the model robust. If `Psi` increases, probability increases.
2.  **`bernoulli_logit`**: This is a numerically stable function in Stan (more stable than `bernoulli(inv_logit(...))`).
3.  **Vector Slicing**: `Psi[1:(T - L)]` aligns perfectly with `Y[(L + 1):T]`. This replaces the manual loop and is much faster for the sampler.
