# West Nile Virus Predictions

### Problem Statement
This problem comes from comes from a 2015 Kaggle, details [here](https://www.kaggle.com/c/predict-west-nile-virus). . The official problem statement for this Kaggle is, “A more accurate method of predicting outbreaks of West Nile virus in mosquitos will help the City of Chicago and CPHD [Chicago Public Health Department] more efficiently and effectively allocate resources towards preventing transmission of this potentially deadly virus.”

### Background
West Nile Virus (WNV) is an infectious disease that was discovered 1937 in the West Nile region of Uganda. It was first seen in the United States in 1999 and is spread through infected mosquitos. Common symptoms include mild fever, headaches, body aches, and skin rashes. While most people don't experience serious symptoms, WNV can be life-threatening if it enters the brain, causing encephalitis. Unfortunately there are no vaccines or treatments available, so the best way way to prevent getting WNV is to avoid mosquito bites.

The City of Chicago and the Chicago Public Health Department (CPHD) have resorted to spraying areas when WNV is detected to kill the infected mosquites and prevent the virus' spread. They have set up traps around the city to capture mosquitos and send them off for testing to determine if WNV is present.

To learn more about the distribution of WNV in our dataset, see our Tableau Public Story [here](https://public.tableau.com/views/WestNileVirusPredictions/Story1?:embed=y&:display_count=yes)


### Goal
Using advanced data modeling techniques, we want to predict when and where different species of captured mosquitos will test positive for West Nile Virus based on past data and weather information. Ultimately, we want to find ways we can use these predictions to help the City of Chicago and CPHD allocate resources better. The effectiveness of our predictions will determine what kinds of things our predictions can be used for

### Methods
We were given data from the years 2007, 2009, 2011, and 2013, including when and where mosquitos were captures in Chicago's traps, what species they were, and whether or not they they tested positive. We were also given weather data from two weather stations in Chicago (see data dictionaries below for more information) for 2007-2014. Some data from where City of Chicago sprayed mosquitos in 2011 and 2013 were also provided, but were not used for analysis because it didn't include enough data. We cleaned the data, averaged the data from the two weather stations, and merged the weather data and mosquito capture data for analysis. After that, we build several models, including Logstic Regression, Naive Bayes, and various Decision Tree-based models (including Random Forest, Adaboost, Extreme Gradient Boosting). Because the competition required probability estimates to be included in the final output, we did not attempt methods such as K-Nearest Neighbors and Support Vector Classification, which only categorize and do not estimate category probabilities. We judged our model based on an Area Under the receiver operating characteristic (ROC) Curve (ROC AUC score) instead of overall accuracy because the presence of WNV was a rare event (chances around 5%). The best model was an XGBoost model, so this was our final model.

### Results 
Using XGBoost, we were able to achieve a cross-validated ROC AUC score 0.85, which is a good score. To help understand this score, I will illustrate with an example point on the ROC curve: with a True Positive Rate of 80%, our False Positive Rate would be around 25%; that is, when we correctly predict 80% of the WNV-positive cases, we will incorrectly predict 25% of the WNV-negative cases as positive. 
