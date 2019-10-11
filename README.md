# GA-DSI-Project-02

## Introduction

The objective of the project is to develop a machine learning model that can predict the prices of houses based on data concerning attributes/properties of each house.

Regression is used iteratively for feature selection to refine the model.

## Data
##### 2 sets of data are provided:

1) Training data comprising 2,051 rows. Each row has 80 features plus the Sale Price (total of 81 columns)


2) Test data comprising 879 rows. Each row has 80 features. The Sale Price is not provided (to be predicted by the model).

The data comprises Continuous (numeric), Ordinal (discrete ordered) and Nominal (discrete un-ordered) variables. A data dictionary was provided.

## Project Submission
The submission comprises 2 jupyter notebooks:

##### 1) 02 Data_Clean_Transform-for-ss

Cleaning of data and imputation is done here, for both the Training and Testing (for which predictions were submitted to Kaggle) data.

##### 2) 05 Feature_Engineering_Prediction-ss

The first part of the notebook involves Exploratory Data Analysis (EDA). Heatmaps are used to visualize the correlation between variables (Continuous, Ordinal) and the Sale Price. Scatter plots are used to visualize the general relationship (linear / non-linear) between these variables and the Sale Price.

The second part of the notebook contains a series of regressions (Linear, Ridge, Lasso). The regressions (baseline, plus 6 more) are carried out with scaled data to enable the selection of not more than 25 features. For each regression, the training data had been split into 75% for training (10 folds) and 25% for validation.

The last part of the notebook involves training the final model (based on 25 features) with the full set of training data and then predicting the Sale Price using test data.

The predicted Sale Price data was submitted to Kaggle, which awarded a score of 31,996.

## General Approach to Data Cleaning and Imputation
A data-driven approach is adopted. No assumptions are made regarding the importance of particular feature for predicting the Sale Price. Therefore, as much of the data as possible is preserved. Columns are dropped only if:

- An overwhelming majority of the data is the same _(zeros, missing, 'NA')_

- Directly derived from other columns (e.g. sum of 2 other columns)

No rows were dropped.

Imputation of missing data varied between Continuous, Ordinal and Nominal variables.

- *Continuous* - either 0, mean, or median was used after cross-checking with other values (dependencies, distribution) for the same row of data. For example, if other available data showed that a type of room did not exist, then all size-related attributes for the non-existent room should be 0.

- *Ordinal* - ranking/order of merit was determined from the data dictionary. The lowest rank was assigned the smallest number (or 0), while the highest rank was assigned the largest number. In cases where the variable was not applicable (e.g. basement quality where a basement DID NOT exist), then 0 was assigned because this term should not be used as a term in the regression analysis (i.e. the feature is not applicable). In other cases, the lowest rank was assigned a value of 1.

- *Nominal* - a dummy column for each possible value was generated.

After cleaning the data and generating the dummy columns, the dataset had 177 features.

## General Approach to EDA
Heatmaps were used to check the correlation between variables (continuous and ordinal) and target (Sale Price). 22 of these variables showed correlation of above 0.25 and were noted, for cross-checking with Lasso/Ridge regularization results later.

Scatter plots provided visualization of how these variables were related to the target. 3 variables were noted to potentially have polynomial relationship with the target. These were 3 variables were noted, for inclusion into the Lasso/Ridge regularization later.

## General Approach to Feature Engineering and Model Refinement
Initially, baseline regressions (Linear/Lasso/Ridge) were carried out using all 177 features. Lasso indicated that of these, 66 features had non-zero coefficients.

The 66 features were used in the next iteration of regressions and their respective coefficients were checked. Where there were zero-coefficients, the features were removed in the following iteration.

After iteration # 3, it appeared that the regressions (Lasso in particular) could no longer reduce the number of non-zero coefficients.

At iteration # 4, the 3 polynomial features (as stated earlier) were added. 2 further regressions were carried out to remove the 'weakest' predictors (i.e. lowest coefficient magnitudes).

After 6 iterations, we derived 25 features as predictors for the model.

## Working with test data to generate final prediction
One of the predictors in the model was a dummy column _'nghbd_GrnHill'_. This had been generated from the 'Neighborhood' column value _'GrnHill'_.

However, it surfaced that the test data did not have any rows with the value _'GrnHill'_ in the 'Neighborhood' column. As a result, the dummy column _'nghbd_GrnHill'_ could not be generated from the test data.

A function **'key_error_handler'** was written to capture such cases in a global list called _'features_not_in_test_data'_. The function was then called within a Try-Except clause so that the missing column would not break the execution of the code. The function and Try-Except was designed to handle any number of such cases that may occur in future test data.

Since the dummy column _'nghbd_GrnHill'_ did not appear in the test data, it could not be used as a predictor. Therefore, a final total of 24 features (25 selected features minus 1 missing) were used in the prediction.

## Conclusion
The prediction achieved a *Kaggle score of 31,996.*

The methodology/workflow of EDA and feature engineering was designed to be as objective as possible without requiring any subjective input from the user. As more housing data is received, the same workflow can be applied to re-select the 25 strongest predictors for the next iteration of the model. As such, the model would be able to continuously evolve as more data is collected.
