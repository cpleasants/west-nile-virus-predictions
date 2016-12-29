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
Using XGBoost, we were able to achieve a cross-validated ROC AUC score 0.85, which is considered a good score (the score ranges from 0.5, the equivalent of random guessing, to 1.0, always correct). To help understand this score, I will illustrate with an example point on the ROC curve: with a True Positive Rate (TPR) of 80%, our False Positive Rate (FPR) would be around 25%; that is, when we correctly predict 80% of the WNV-positive cases, we will incorrectly predict 25% of the WNV-negative cases to be positive. Because of the imbalance of prevalence, this amounts to a 99% precision for WNV-negative (99% of cases predicted to be WNV-negative are correct) and a 15% precision for WNV-positive (15% of cases predicted to be WNV-positive are correct).

### Recommendation
Because even a small False Positive Rate equates to a large number of false positives (there are over 2500 mosquito specimens tested each year, and 95% are WNV-negative), it is not reasonable to use our predictions to decide where to spray for mosquitos. Spraying is too expensive and potentially harmful to spray where it is not needed. However, **what our predictions can do for the City of Chicago and CPHD is to help determine which specimens need to be sent in for testing and which don't**. If a particular specimen is predicted to have a near-zero probability of testing positive for WNV, the city can save money by not sending it in for testing. At an estimated $500 per lab test, this can save the city a lot of money. The table below outlines the estimated yearly savings at different thresholds, based on five-fold cross-validated predictions on in-sample data:

TPR/Recall (% of actual WNV-positive cases predicted correctly)|Threshold (probability needed to be predicted WNV-positive)|Estimated # of Traps to NOT be Tested Per Year (predicted WNV-negative)|Estimated Annual Savings (based on $500 per test)
-----------|---------|-----------|-----
80%|5.6%|1,889|$944,500
85%|4.1%|1,729|$864,250
90%|3.0%|1,547|$773,625
95%|2.0%|1,321|$660,250
100%|0.1%|366|$183,000

We recommend that City of Chicago and CDPH aim for a True Positive Rate of 95%. That means that only if a specimen has an estimated probability greater than 2% of being WNV-positive, they should send it for testing. This would limit the number of WNV-positive specimens being missed while also saving a great deal of money.

### Risks and Assumptions
The results of this study depend greatly on certain assumptions, chief among them being that WNV prevalence will not change dramatically from year to year. If prevalence goes up a lot one year, we would have to adjust our calculations and it's likely that more specimens would need to be tested. This would decrease savings. Another big assumption is that the distribution of WNV across the city of Chicago will not change dramatically; that is, no major changes will occur that increase the likelihood of WNV being present in one area that it hasn't been before. For the four years in the study, there are certain areas where WNV is more common, and this must remain consistent for the results to be valid. The City of Chicago might consider periodic testing of specimens from areas where WNV was previously not seen just in case. Again, this would decrease cost savings but may catch these changes more quickly. Finally, if lab costs for testing WNV specimens change greatly, the savings will change along with it. These risks and assumptions should be kept in mind as decisions get made moving forward

### Data Dictionaries
Weather Data from May 1, 2007 to Oct 31, 2014 from [NOOA](https://www.ncdc.noaa.gov/)

Column Name|Data Type|Description|Notes
-----------|---------|-----------|-----
Station|Integer|Which station the data in the row come from|Station 1 or Station 2
Date|DateTime|Date the row's data come from|
Tmin|Integer|Minimum temperature that date|
Tmax|Integer|Maximum temperature that date|
Tavg|Integer|Average temperature across the day|
Depart|Integer|Difference from normal for that day of the year|Only available for Station 1
DewPoint|Integer|Average Dew Point Temperature|Temperature where water vapor starts to condense out of the air 
WetBulb|Integer|Average Wet Bulb Temperature|Adiabatic saturation temperature ([more info here](https://en.wikipedia.org/wiki/Wet-bulb_temperature))
Heat|Integer|65 - Tavg (if Tavg <= 65)|
Cool|Integer|Tavg - 65 (if Tavg > 65)|
Sunrise|Time|Sunrise time in military time|Only availble for Station 1
Sunset|Time|Sunset time in military time|Only availble for Station 1
CodeSum|List|Code(s) for various weather conditons|e.g. FG = Fog, HZ = Haze
Depth|Integer|Precipitation Depth, if applicable (else 0)|
SnowFall|Float|Snowfall (inches)|
PrecipTotal|Float|Rain (inches)
StnPressure|Float|Average station pressure
SeaLevel|Float|Sea level pressure (inches of Hg)
ResultSpeed|Float|Resultant wind speed (mph)|Resulant wind = vector sum of wind speeds and directions
ResultDir|Integer|Resultant wind direction (degrees)|Resulant wind = vector sum of wind speeds and directions
AvgSpeed|Integer|Average wind speed

Source: [Kaggle documentation](https://kaggle2.blob.core.windows.net/competitions-data/kaggle/4366/noaa_weather_qclcd_documentation.pdf?sv=2015-12-11&sr=b&sig=OL7yE9hJ8SUv%2BmSl3qx1JQrqyMuT%2B6sT8z0TTuNw7G0%3D&se=2016-12-31T23%3A50%3A08Z&sp=r)

Trap Data

Column Name|Data Type|Description|Notes
-----------|---------|-----------|-----
Date|DateTime|Date the row's data come from|
Address|String|approximate address of the location of trap. This is used to send to the GeoCoder. 
Species|String|Species of mosquito for that row of data
Block|Integer|Block Number of trap address
Street|String|Street name of trap address
Trap|String|Trap ID
AddressNumberAndStreet|String|Address and street of the trap
Latitude|Float|Trap latitude
Longitude|Float|Trap longitude
AddressAccuracy|Integer|accuracy of trap address returned from GeoCoder
NumMosquitos|Integer|Number of mosquitos of a particular species found in a particular trap
WnvPresent|Boolean|Whether or not West Nile Virus was found in the sample

Source: [Kaggle competition](https://www.kaggle.com/c/predict-west-nile-virus/data)
