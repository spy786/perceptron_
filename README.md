






# **Binary Classification using Advanced Techniques**

---

## Problem Statement
Bank A, a credit card issuer, aims to build a robust risk management framework for its existing credit card customers. To achieve this, the bank intends to develop a "Behaviour Score"—a predictive model that assesses the probability of customers defaulting on their credit cards. This score will assist in various portfolio risk management activities. A historical dataset containing 96,806 credit card records (development data) has been provided, along with independent variables such as credit limits, transaction attributes, and bureau details. Another dataset of 41,792 records (validation data) without default flags is also provided for prediction purposes.

The objective is to predict default probabilities for the validation dataset using a model trained on the development data and submit these predictions along with detailed documentation of the approach and insights.
The objective was to build a robust binary classification model to predict the bad_flag variable. The data had the following challenges:

---

## Dataset Overview
(96806, 1216)
The dataset contains features related to financial transactions, bureau data, and client attributes. It includes the following characteristics:
- **Rows**: The number of observations in the dataset.
- **Columns**: The number of attributes, including categorical and numerical variables.
- **Target Variable**: `bad_flag` (1 for bad accounts and 0 for good accounts).

#### Main Challenges Observed:
- High Dimensionality: The dataset contained 1216 features.
- Class Imbalance: Out of 96,000 rows, only 1.4% of the samples belonged to the minority class (bad_flag == 1)
- Presence of duplicate rows and null values.
- Few columns contains more than 50% null values
- Features with zero standard deviation and features with over 99.5% identical values.

---

## Data Preprocessing

####  Data Cleaning
![image](https://github.com/user-attachments/assets/9247ddf7-b341-413f-bf99-117b3b629a63)

1. **Duplicate Removal**:
   - Removed duplicate rows to ensure a clean dataset for analysis.

2. **Null Value Handling**:
   - Dropped columns where more than 50% of rows were null in the subset of bad accounts (`bad_flag` = 1).

3. **Low-Variance Features**:
   - Removed features with zero standard deviation.

4. **Highly Correlated Features**:
   - Used the correlation matrix to identify features with absolute correlations above 0.95 and removed them to prevent multicollinearity.

###  Data Splitting 
- Split the dataset into training (80%) and testing (20%) sets, stratified by the target variable to maintain the distribution of classes.

####  Feature Imputation
- **Categorical Features**: Imputed missing values using a KNN imputer.
- **Numerical Features**: Used the mean strategy for imputation with `SimpleImputer`.

---

## Data Augmentation

To address class imbalance, we implemented an augmentation strategy:
- **Dimensionality Reduction**: Applied UMAP to reduce features to 2D for clustering.
- **UMAP**:
- ![image](https://github.com/user-attachments/assets/e5dd2313-5939-41a6-af13-c6cd6e54d856)

- This is a special type of graph, called a Uniform Manifold Approximation and Projection (UMAP). UMAPs are helpful ways of displaying many types of data and are often referred to as one type of dimensionality reduction tool. Dimensionality reduction is a technique that helps represent many-dimensional data in just two or three dimensions.    
- **Cluster Identification**:
- ![image](https://github.com/user-attachments/assets/1eff2383-0a46-4f21-8f99-63e0aa90183d)

-  Identified clusters with mixed class distributions using a grid clustering approach.
- **Synthetic Data Generation**:
-![image](https://github.com/user-attachments/assets/682489c8-2ed0-4a5b-b086-613db4b14281)
-   Used a custom Beta-SMOTE method to generate synthetic samples for minority classes within mixed clusters.
-**Hyperparameter Tuning**
  Used Keras Tuner to optimize hyperparameters of:

      Autoencoders for dimensionality reduction.
      The final classification model.
  Parameters tuned included:

      Number of units in hidden layers.
      Dropout rates.
      Learning rate.
      L2 regularization strength.
---

## Feature Scaling and Transformation

####  Winsorization
![image](https://github.com/user-attachments/assets/76153fe7-5147-488c-992e-81baff83e2ec)
- Applied winsorization to numerical features to limit extreme values (0.2% on both tails).

####  Offset Correction
- Added absolute minimum values to columns with negative values.

####  Log Transformation
![image](https://github.com/user-attachments/assets/a98ea5c8-b4da-46ea-bb7f-d77ed0ecab6c)
- Applied log transformation to stabilize variance and normalize distributions.

####  Min-Max Scaling
![image](https://github.com/user-attachments/assets/3df61852-7992-4054-9e95-8dedf8ee6b29)
- Used Min-Max Scaling to normalize all features to a range of 0 to 1.

---

## Dimensionality Reduction with Autoencoders
**Autoencoder**:
![image](https://github.com/user-attachments/assets/4b5aee1e-36aa-437a-8840-d4e0f3071e37)

 Autoencoders are a powerful tool for data compression and analysis.
 They can be used to discover hidden patterns within your data and
 then use those patterns to create a compressed representation of the
 original data. It can be helpful when working with data sets that are 
 too large to conveniently or when you wish to investigate the
 distribution of different classes within your data.
####  Numerical Autoencoder
![image](https://github.com/user-attachments/assets/c1c5c121-e9f4-4d91-a78c-663f8114f655)

- Designed a deep autoencoder with 3 encoding and 3 decoding layers.
- Trained using `mean_squared_error` loss and early stopping.

####  Categorical Autoencoder
- Implemented a tunable categorical autoencoder with the help of Bayesian Optimization to select optimal hyperparameters.

---

## Neural Network
![image](https://github.com/user-attachments/assets/2d38fd25-6847-4613-971b-a76a4641b5d1)

A neural network is a machine learning algorithm that uses interconnected nodes to process data and solve complex problems. Neural networks are inspired by the human brain and are a type of artificial intelligence (AI).
##  Insights and Observations

- **Class Distribution**: The dataset was imbalanced, necessitating augmentation.
- **Feature Dependencies**: High correlations revealed redundancies.
- **Data Augmentation Impact**: Beta-SMOTE significantly improved minority class representation in clusters.

---

## Evaluation Metrics

### Key Metrics Used:
<img width="695" alt="Screenshot 2025-01-16 at 1 37 56 AM" src="https://github.com/user-attachments/assets/ce731e40-7c77-430e-b979-a112527a82b2" />

- **Precision and Recall**: Emphasized due to the imbalanced nature of the dataset.
- **F1-Score**: Provided a harmonic mean of precision and recall.
- **Loss Value**:A lower loss indicates better alignment with the true values and improved model performance.
- **Conclusion**:The key metrics we used include precision, recall, and F1 score for the minority class. This is because the dataset has a significant imbalance, with a high majority-to-minority ratio, making these metrics more meaningful. Metrics like accuracy are not suitable in this case, as a model predicting only the majority class would still achieve an accuracy of around 99%, rendering it misleading. Additionally, I considered the model's loss value as a critical metric, as a lower loss generally indicates better efficiency and performance.

---

## Result
![Screenshot 2025-01-16 014327](https://github.com/user-attachments/assets/a92c0ed9-9e2d-4070-a4b5-b0638373aaa8)
This is result on training test data.



