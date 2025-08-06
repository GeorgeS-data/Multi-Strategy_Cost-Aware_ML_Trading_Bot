# Multi-Strategy Cost-Aware ML Trading Bot

This project is a cost-aware machine learning pipeline for hourly equity forecasting. It combines multiple core trading signals enhanced with ML classifiers to predict ternary market movement classes and optimize trading strategy weights by accounting for the monetary cost of misclassifications.

---

## 🚀 Project Goal

Build a modular, interpretable trading system that:
- Uses hourly price and volume data from Yahoo Finance
- Engineers traditional technical indicators plus advanced tsfresh time-series features
- Classifies future returns into three classes: loss, neutral, gain
- Applies a cost-sensitive penalty matrix to weigh misclassification costs
- Optimizes strategy signal weights to maximize cumulative returns net of trading costs

---

## 🔑 Project Highlights

- Hourly equity data downloaded via `yfinance`
- Feature engineering including RSI, Bollinger Band width, moving averages, momentum, volatility, and tsfresh time-series features
- Ternary classification target based on future return thresholds (loss, neutral, gain)
- Preprocessing includes train/validation/test split aligned with time order, scaling, and SMOTE oversampling applied **only to training data** to prevent leakage
- Models trained: Logistic Regression, Random Forest, and XGBoost with balanced class weights
- Cost-sensitive learning via Optuna-tuned penalty matrix guiding sample weights
- Optuna also used to tune trading signal weights to maximize backtested equity curve
- Realistic backtesting simulating trading costs and slippage compared against a Buy & Hold benchmark

---

## ⚙️ ML Pipeline Overview

1. **Data Loading**  
   - Hourly OHLCV data downloaded with `yfinance`  
   - Flattened columns for convenience

2. **Feature Engineering**  
   - Custom computations: RSI (14), Bollinger Band width (20), moving averages (5, 20), momentum, volume trend, volatility  
   - Time features: hour of day, day of week  
   - tsfresh extraction on sliding windows for advanced time-series pattern recognition

3. **Label Creation**  
   - Future return computed over a 5-hour horizon  
   - Threshold ±0.5% to define three classes:  
     - 0 = Loss (return < -0.005)  
     - 1 = Neutral (-0.005 ≤ return ≤ 0.005)  
     - 2 = Gain (return > 0.005)  
   - Rows with NaNs dropped

4. **Train/Validation/Test Split**  
   - Time-based split to avoid leakage: 70% train, 10% validation, 20% test  
   - Features aligned with labels  

5. **Preprocessing**  
   - Standard scaling with `StandardScaler`  
   - SMOTE oversampling applied **only on training set** to address class imbalance

6. **Model Training**  
   - Logistic Regression, Random Forest, XGBoost classifiers initialized with balanced class weights  
   - Initial training on training set only  

7. **Cost-Sensitive Optimization**  
   - Use Optuna to tune penalty matrix weights for misclassifications on validation set  
   - Sample weights for retraining derived from penalty matrix row sums  
   - Retrain each model on combined training + validation data using updated sample weights

8. **Signal Weight Optimization**  
   - Use Optuna to tune weights for trading signals (classes 0, 1, 2) to maximize cumulative return

9. **Backtesting**  
   - Shift signals by 1 hour (trade execution delay)  
   - Calculate PnL with simulated fees and slippage  
   - Compare cumulative equity curves vs Buy & Hold baseline

---

## 📈 Evaluation Metrics

- Classification report: precision, recall, F1-score  
- Confusion matrix weighted by penalty matrix  
- Cumulative return and equity curve plots  
- Optimized trading weights and penalty costs output  
- Visual comparison to Buy & Hold benchmark

---

## 📅 Key Iterative Improvements

- Moved from daily to hourly data for richer time-based features  
- Added tsfresh time-series features for pattern extraction  
- Introduced ternary classification target based on forward returns  
- Incorporated cost-sensitive penalty matrix optimized by Optuna  
- Retrained models with cost-aware sample weights  
- Optimized trading signal weights for backtested profitability  
- Developed backtesting with realistic trading costs and slippage

---

## 🧠 Lessons Learned

- Prevent data leakage by applying SMOTE **only on training data** and using time-ordered splits  
- Financial models require cost-aware metrics, not just classification accuracy  
- Combining classical technical indicators with advanced time-series features improves predictive power  
- Modular code design allows easy experimentation and tuning  
- Realistic backtesting crucial to validate ML-driven strategies before live deployment

---

## 📁 Project Structure

```text
Multi-Strategy_Cost-Aware_ML_Trading_Bot/
├── current_model/            # Latest trained ML model
├── data/                     # Raw & processed datasets
│   ├── raw/                  # Original downloaded price data
│   └── processed/            # Feature-engineered & labeled data
├── notebooks/                # Colab notebooks for exploration & testing
├── outputs/                  # Backtesting results, charts, evaluation metrics
├── utils/                    # Utility scripts for features, labeling, etc.
└── README.md
```

---

## 🛠 Future Work

- Add walk-forward or rolling time-series cross-validation  
- Incorporate model confidence outputs to refine trade signal weighting  
- Expand to multiple equities or asset classes  
- Develop user-friendly dashboard or CLI interface

---

## 📌 Disclaimer

This project is for educational purposes only and does not constitute financial advice or a trading recommendation.
