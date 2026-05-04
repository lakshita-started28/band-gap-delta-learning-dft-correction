# Band Gap Forensics: Delta Learning for Materials Discovery

##  Overview

Accurate prediction of electronic band gaps is critical for designing semiconductor materials used in power electronics, photovoltaics, and optoelectronic devices. While Density Functional Theory (DFT), particularly the PBE functional, is widely used, it systematically underestimates band gaps due to inherent approximations in exchange–correlation physics.

This project addresses that limitation by learning the **error in DFT predictions** instead of directly predicting band gaps. A machine learning framework is developed to model the correction term:

<img width="258" height="59" alt="image" src="https://github.com/user-attachments/assets/2be17aab-d43a-4613-8d75-d6089ab4a2c5" />


The final prediction is reconstructed as:

<img width="331" height="61" alt="image" src="https://github.com/user-attachments/assets/9936282b-5546-446b-9b8a-9431ab49801f" />


This approach combines physical modeling with data-driven learning to significantly improve predictive accuracy.

---

## Approach

The project follows a structured computational pipeline.

### Data Engineering

Two datasets were used:

- DFT-computed band gaps (Materials Project)
- Experimental band gaps (curated dataset)

Materials were aligned using **standardized reduced chemical formulas**, ensuring correct matching between datasets. After filtering metallic systems and outliers, a clean dataset of **1,017 materials** was obtained.

---

### Feature Engineering

Each material was represented using **137 Magpie descriptors**, capturing:

- Electronegativity differences  
- Atomic radii  
- Valence electron distributions  
- Chemical diversity  

After removing invalid entries, the final dataset consisted of:

**965 samples × 137 features**

---

### Modeling Strategy (Delta Learning)

Instead of predicting band gaps directly, models were trained to predict the correction term (Δ). This reduces complexity by leveraging the physics already captured by DFT.

Models explored:

- Linear Regression (baseline)  
- Bayesian Ridge (regularized linear model)  
- Random Forest (non-linear ensemble)  
- Gaussian Process Regression (with uncertainty)  

---

## Key Results

- **Random Forest (Best Model):**
  - RMSE ≈ **0.57 eV** (baseline split)  
  - Improved to ≈ **0.55 eV** for certain data splits  

- **GPR (with PCA + scaling):**
  - Improved from **>1.0 eV → ~0.596 eV**

- **Dataset Size:**
  - 1,017 → cleaned → **965 usable samples**

- **Mean Correction (Δ):**
  - ~0.7 eV (confirms systematic underestimation by DFT)

- **Stability Analysis:**
  - RMSE remained within **0.55–0.65 eV** across multiple random splits



### DFT vs Experimental Band Gap
![DFT vs Expt](images/dft_vs_expt.png)

### Model Prediction vs Actual (Random Forest)
![Prediction](images/rf_prediction.png)

### Feature Importance (Physical Insights)
![Feature Importance](images/feature_importance.png)

---

## Key Insights & Novelty

### 1. DFT Error is Structured, Not Random

The correction term varies systematically with material chemistry:

- Oxides and nitrides → larger corrections  
- Chalcogenides → smaller corrections  

This demonstrates that DFT error encodes **underlying chemical physics**, making it learnable.

---

### 2. Delta Learning Outperforms Direct Prediction

Predicting the correction (Δ) instead of band gap directly:

- Reduces model complexity  
- Leverages existing physical knowledge  
- Achieves better accuracy than standalone ML models  

---

### 3. Dimensionality Reduction is Critical for Kernel Models

Gaussian Process Regression initially failed in high-dimensional space.  
Applying **StandardScaler + PCA (20 components)** significantly improved performance.

---

### 4. Model Robustness Verified

By varying `random_state`, model performance remained consistent:

- RMSE ranged between **0.55–0.65 eV**  
- Confirms generalization and stability  

---

### 5. Uncertainty-Aware Predictions

GPR provided uncertainty estimates, validated through calibration curves, enabling:

- Confidence-aware predictions  
- Identification of unreliable regions  

---

## Industry Relevance

This work has direct applications in:

### Semiconductor Design
Faster screening of materials for power electronics (e.g., SiC, GaN alternatives)

### Solar Energy Materials
Accurate band gap prediction for photovoltaic efficiency optimization  

### Materials Discovery Pipelines
Replacing expensive quantum calculations (GW methods) with ML-corrected DFT  

### AI + Physics Integration
Demonstrates how ML can augment, not replace, first-principles methods  

---

## Challenges Faced

- Dependency issues with `pymatgen` and `matminer` in cloud environments  
- Data mismatch between DFT and experimental datasets  
- Model failures due to high-dimensional feature space (GPR instability)  
- Handling non-numeric features during training  

All challenges were resolved through preprocessing, restructuring, and model optimization.

---

##  Conclusion

This project demonstrates that machine learning can systematically correct DFT errors, bridging the gap between computational efficiency and experimental accuracy.

Rather than replacing physics-based models, this approach enhances them, making it highly relevant for real-world materials discovery workflows.
