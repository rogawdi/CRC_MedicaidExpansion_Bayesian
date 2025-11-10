Bayesian Hierarchical Regression – Medicaid Expansion and Colorectal Cancer

This repository contains R code used to analyze the association between state Medicaid expansion timing and colorectal cancer outcomes using SEER data (2006–2019).
Analyses include Bayesian difference-in-differences (DiD) survival models, stage-shift and surgery-receipt regressions, and propensity-score–matched survival comparisons.

Data
	•	Source: Surveillance, Epidemiology, and End Results (SEER) Research Plus database (https://seer.cancer.gov/data/).
	•	Cohort: Adults < 65 years with primary colorectal cancer diagnosed 2006–2019.
	•	Data are obtained under SEER’s Data-Use Agreement and cannot be publicly shared.
	•	Cleaned analytic datasets are saved as .rds objects in the /bayesian/inputs/ directory.


Software Requirements
  R >= 4.2 with the following packages: survival, survminer, dplyr, broom, Matching, MatchIt, nnet, cobalt, data.table, tidyr, 
        ggplot2, rstanarm, brms, rstan, ggpubr, usmap, survRM2, gridExtra, lmtest, sandwich, purrr, scales

Parallel chains require the rstan/brms toolchain and C++ compiler.



Workflow Overview

1 – Data Preparation
	•	Load SEER CRC dataset (crc_allstages_all.csv).
	•	Recode state identifiers into Medicaid expansion groups:
Non-Expansion (0), Early (1), On-Time (2), Late (3).
	•	Generate covariates: stage group, income and state-level quintiles, marital group, age category, and survival time truncated at 36 months.

2 – Stage and Surgery Coding
	•	Collapse SEER stage into four groups (I–IV).
	•	Create binary indicators:
stage12 = 1 (Stage I–II), 0 (Stage III–IV)
surgery_bin = 1 (“Recommended + performed”), 0 (other).

3 – Bayesian Logistic DiD Models
	•	Run unadjusted Bayesian logistic regressions of stage and surgery outcomes:
outcome ~ post * treat
	•	Separate models for Early, On-Time, and Late expansion states versus Non-Expansion.
	•	Outputs: odds ratios (OR), 95 % credible intervals (CrI), and posterior probabilities.
Results saved as
stage_shift_Bayesian_summary.csv and surgery_all_Bayesian_summary.csv.

4 – Propensity-Score Matching
	•	Match each expansion group to Non-Expansion controls (1:1 or 1:2 nearest-neighbor, caliper 0.25 SD).
	•	Evaluate covariate balance with Love plots and standardized mean differences.
	•	Save matched datasets for downstream survival modeling.

5 – Frequentist and Bayesian Survival Models
	•	Fit Cox proportional-hazards DiD models on matched data.
	•	Fit Bayesian Weibull survival models using brms with weakly informative priors:
capped_time | cens(1 – event) ~ did_time * did_treat + (1 | Medicaid.Expansion.Status)
	•	Posterior summaries include hazard ratios (HR) and probabilities of benefit.
	•	Validation: posterior-predictive checks, LOO, and WAIC metrics saved to /bayes_output/.

6 – Sensitivity & Placebo Analyses
	•	Stage- and surgery-specific subgroup analyses.
	•	Parallel-trend falsification tests using pre-expansion years only.
	•	Results exported as CSVs in /stats/ and /coxdata/.


Reproducibility

Run the R script sequentially from top to bottom.
All intermediate .rds objects and CSVs will be generated automatically.
Because SEER data are protected, only derived outputs and scripts can be shared.
Analytic code is provided here to satisfy FAIR transparency requirements.


Contact: For questions about code or replication, please contact the corresponding author.
