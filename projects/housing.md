## Housing Price Prediction
![Housing Model](https://raw.githubusercontent.com/gnlmano/Housing-Prices-Prediction/refs/heads/main/image_github.jpg)   
A full end-to-end machine learning project completed as part of my Data Science coursework.  
The goal was to build accurate **linear models** to predict residential property prices in Southern California.

### Project Highlights

- **Extensive Exploratory Data Analysis (EDA):**  
  Identified skewed distributions, outliers, and inconsistent features (e.g., room counts, zero-lot areas).

- **Advanced Feature Engineering:**  
  - Created log-transformed features for linearity  
  - Engineered room size ratios and interaction terms  
  - Built geographic grid features using latitude–longitude  
  - Applied **target encoding** with out-of-fold smoothing to model location effects without leakage

- **Modeling & Hyperparameter Tuning:**  
  - Trained baseline Linear Regression, Lasso, Ridge, and ElasticNet  
  - Used cross-validation and RMSE for model selection  
  - **Ridge Regression** delivered the best generalisation performance

- **Insights:**  
  Despite heavy feature engineering, the strongest predictor was still **location** —  
  specifically, the **region-code**, highlighting the economic reality of real estate.

### Deployment-Ready Pipeline

Built a clean, modular ML workflow using:

- `preprocess.py` for data cleaning  
- `create_features.py` for engineered features  
- `train_model.py` for reproducible training  
- `predict_model.py` for inference  
- Saved model + encoders via joblib for production use  

###  Deployment

The trained model is exposed via a FastAPI endpoint:
- Fully Dockerised
- Deployed on Render
- Supports real-time prediction
- Includes a /sample endpoint for quick testing
- No need to preprocess data — the API handles all feature logic internally

**[Live API Link](https://housing-prices-prediction.onrender.com/docs)**

**[View the full project on GitHub](https://github.com/gnlmano/Housing-Prices-Prediction)**  
