## ICU Mortality Prediction
![Mortality Prediction Model](https://raw.githubusercontent.com/gnlmano/Probability-of-Death-KNN/refs/heads/main/image_github.jpg)   
### Project Summary

#### 1. Data Overview
- The project uses the MIMIC-III ICU dataset (single-row per patient stay).
- Includes: demographics, vitals, lab summaries, main diagnosis, and additional comorbidities in a separate long-format table.
- Target: HOSPITAL_EXPIRE_FLAG (binary mortality indicator).

#### 2. Task: Train a KNN Classifier
- Initial requirement was to train and tune a K-Nearest Neighbors model.
- Goal: build a clinically meaningful mortality predictor, evaluated with F2-score.

#### 3. Why KNN Fails for This Problem
- During tuning, k kept collapsing to 1, and weights='distance' was always preferred.
- KNN essentially memorized training samples, giving perfect in-sample scores.
- High dimensionality + imbalanced mortality labels + noisy clinical features → KNN becomes unstable.
- Despite good CV scores, test performance dropped, exposing overfitting.
- Conclusion: KNN is not a suitable model for ICU risk prediction.

#### 4. Moving Beyond KNN: Better Models
- Tried several alternatives:
  - logistic regression with class weights
  - random forests
  - gradient boosted trees
- XGBoost consistently performed best, offering strong generalization and robust probability estimates.
- This model became the final deployed version.

#### 5. Encoding ICD-9 & Diagnosis Without Data Leakage
- ICD-9 and diagnosis fields have huge cardinality and strong outcome associations.
- Naive target encoding leaks label information across folds.
- To avoid this, I built a custom out-of-fold (OOF) target encoding pipeline:
- For each CV fold, compute mortality rates only from the training slice.
- Merge per-patient comorbidities to get:
  - max_mortality
  - mean_mortality
  - count_comorbidities
- Apply the final global mapping to the test set.
- Result: leakage-free proxy features that improved model performance and interpretability.

#### 6. API Deployment
- Packaged the final XGBoost pipeline and ICD-9 mappings using joblib.
- Built a FastAPI service with two endpoints:
  - /health — simple status check
  - /sampling — returns a random patient example + predicted mortality risk
- Containerized using Docker and ready for cloud deployment (Render, AWS, or GCP).

### 7. Prediction API Design

- The API includes a `/sampling` endpoint that serves a randomly selected
test-set patient and returns the corresponding predicted mortality risk.
- In a real hospital deployment, the model would receive structured EHR records
containing demographics, vitals, diagnoses, and comorbidities. These fields
cannot realistically be entered manually through a public API, because:
  - ICD-9 codes have thousands of valid values.
  - Comorbidities come from a separate table.
  - DIAGNOSIS strings must match the hospital coding system.
  - Many numerical features come from automated monitoring equipment.
- Therefore, the `/sampling` endpoint is intentionally included to illustrate the
model's inference capability, while avoiding an unrealistic manual input interface.

**[Live API Link](https://mortality-prediction.onrender.com)**

**[View the full project on GitHub](https://github.com/gnlmano/Probability-of-Death-KNN)**  

