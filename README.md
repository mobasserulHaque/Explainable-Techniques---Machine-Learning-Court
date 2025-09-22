[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/mobasserulHaque/Explainable-Techniques---Machine-Learning-Court/blob/main/Assignment3_Explainable_Techniques_ML_Court.ipynb)

## 🧾 Attribution (Use of AI Citation)  

> This README was generated with assistance from an **AI system (GPT-5 Thinking)** and subsequently reviewed/edited by the author, who is responsible for the final content.  



# Explainable-Techniques---Machine-Learning-Court
Investigate a real-world AI decision and argue either for or against its validity using explainable AI (XAI) techniques such as SHAP, LIME, or Anchors. 

# Interpretable_XAI_Case2 — Tumor Misclassification (Breast Cancer)

**Role:** 🟥 Prosecution  

## 📌 Project Overview  

This project applies **interpretable / explainable machine learning (XAI)** to audit a breast cancer classifier that mislabeled **Patient X as benign**, who was later confirmed malignant.  

Acting as the **prosecution**, the goals are:  
1. Show why the system made this unsafe decision.  
2. Propose defensible fixes that reduce the risk of missed cancers.  

The approach combines:  
- **Model performance + calibration**  
- **Cost-aware decision policy**  
- **Local and global explanations** using SHAP and LIME  

The core question is not only *“Is the model accurate?”* but rather:  
**“Is the decision policy aligned with clinical risk, where FN ≫ FP?”**


## 🗂 Repository Structure
```
Interpretable_ML_Assignment2/
│── Assignment3_Explainable_Techniques_ML_Court.ipynb   # Main Colab notebook with code, results, and discussion
│── README.md                            # Project documentation
```

## 📑 Tasks Completed

### Data & Model Setup
- Loaded the **UCI Breast Cancer (Wisconsin)** dataset via `sklearn.datasets.load_breast_cancer()`.  
- Target mapping: `0 = malignant`, `1 = benign`.  
- Trained a `RandomForestClassifier(random_state=42)` with a stratified split.  

### Baseline Performance & Calibration (Threshold = 0.50)
- Reported **precision / recall / F1**, **ROC AUC (malignant)**, **Brier score**, and a **reliability curve**.  
- Found strong discrimination and reasonable calibration, but the default decision policy produced **false negatives**.  

### Cost-Aware Thresholding (FN ≫ FP)
- Kept the trained model fixed; swept thresholds `t ∈ [0,1]` minimizing:  

  $$
  C_{FN} \cdot FN + C_{FP} \cdot FP
  $$  

  with $C_{FN} = 10$, $C_{FP} = 1$.  
- Identified **t* ≈ 0.14** as the safer cutoff for screening.  

### Focus Instance (Patient X / False Negative)
- Recreated the **Patient X** feature vector (from starter code) and scored it.  
- $P(\text{malignant}) = 0.15$ → **benign at 0.50**, but correctly referred at **t***.  

### SHAP Explanations (Local & Global)
- **Local (Patient X & matched FN):** benign-pushing contributions from low texture / concavity  
  *(e.g., worst texture, mean concavity).*  
- **Global:** top mean-|SHAP| drivers are size/shape irregularity  
  *(e.g., worst area, concave points, worst perimeter, worst radius).*  
- **Mismatch:** globally important malignancy indicators were down-weighted locally → explains the unsafe benign call.  

### LIME Explanations (Local + Stability)
- Local rules (e.g., *worst texture ≤ 21.05 (+0.06)*, *mean texture ≤ 16.17 (+0.03)*) pushed benign for Patient X and the FN.  
- Stability sweep across seeds: **sign_consistency ≈ 1.0** with low std → systematic **texture-first rationale**, not noise.  

### Summary Tables & Final Verdict
- Compiled metrics, threshold comparison (**0.50 vs t***), Patient X probability, and top local SHAP features.  
- Issued a **Final Verdict (Prosecution)** with a **remediation plan**.  

## 📊 Key Results  

- **Model quality:** AUC ≈ 0.995, Brier ≈ 0.027 (good separation; reasonable calibration).  
- **Default 0.50 policy (test set N=114):** FN = 3, FP = 1 → *avoidable false negatives* in screening.  
- **Cost-aware cutoff:** with $C_{FN} = 10$, $C_{FP} = 1$, optimal $t^* \approx 0.14$ → FN 3 → 0, FP 1 → 9.  
- **Patient X:** $P(\text{malignant}) = 0.15$ → benign at 0.50, referred at $t^*$.  
- **XAI synthesis:** SHAP + LIME agree on a **texture-first** local rationale that *suppresses malignant risk*,  
  while **size/shape cues** are *underweighted locally* despite being top global drivers.  


## ▶️ Running the Notebook

- Developed and tested in **Google Colab**.  
- Open the notebook and run cells **top-to-bottom**.  
- If prompted for missing packages, install them (e.g., `pip install lime shap`).  
- Plots and tables will render inline; **no extra files required**.  

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/mobasserulHaque/Explainable-Techniques---Machine-Learning-Court/blob/main/Assignment3_Explainable_Techniques_ML_Court.ipynb)


## ✅ Final Verdict (Prosecution)

> The benign decision for **Patient X** is **not clinically defensible** in a screening setting where **FN ≫ FP**.  
The issue is not model capability but **policy alignment and local rationale**: the **0.50 threshold** encodes the wrong trade-off, and a **texture-first** emphasis downplays key **size/shape** malignancy cues.  

> Switching to a **cost-aware cutoff (≈ t*)**, adding an **alert zone**, and auditing feature influence would likely have **prevented this miss** and better protect patients going forward.  
