# Disease_Prediction_with_NHANES
This project utilizes the National Health and Nutrition Examination Survey (NHANES) to predict people's likelihood of getting six medical conditions, including depression, fracture, hypertension, high cholesterol, diabetes and oral issues.
# Disease Prediction with NHANES
This project utilizes the [***National Health and Nutrition Examination Survey (NHANES)*** ](https://www.cdc.gov/nchs/nhanes/index.htm?CDC_AA_refVal=https%3A%2F%2Fwww.cdc.gov%2Fnchs%2Fnhanes.htm) to predict people's likelihood of getting six medical conditions, including ***depression, fracture (risk), hypertension, high cholesterol, diabetes and oral issues***.

  

## 1 - Results Highlight
 
The grouped model correctly predicts ***56.3%*** of the people in the validation set with their most likely health conditions.

The optimal models for each disease ***all achieve precisions higher than the prevalence rate***, which means they work better than random guessings. The models have ***a particularly strong predictive power for fracture (risk), hypertension, high cholesterol and diabetes***.

## 2 - Why this project matters

- **(1) Making predictions with limited information.**

Predicting disease is tricky for two reasons: ***(a) solid domain knowledge in medical science is required***, and ***(b) health data is sensitive to collect and protected by law***. However, knowing the customer well is critical for businesses to make sound marketing and strategic decisions. Therefore, this research tries to solve the problem by ***predicting health conditions with limited and non-sensitive information that people feel comfortable providing***.

- **(2) Utilizing NHANES data to build the models.**

NHANES is trustworthy, regularly updated, and publicly available for free. More details will be discussed in the next chapter.

This project aims to ***facilitate decision makings for marketing and sales professionals***. Also, since the dataset is free, the project is friendly to businesses with a limited budget. On the contrary, the project is ***NOT trying to replace doctors and medical equipment at hospitals***. This difference is critical to remember when evaluating model performances.
  

## 3 - About NHANES

[***National Health and Nutrition Examination Survey (NHANES)*** ](https://www.cdc.gov/nchs/nhanes/index.htm?CDC_AA_refVal=https%3A%2F%2Fwww.cdc.gov%2Fnchs%2Fnhanes.htm) is a systematic program of studies designed by the [Centers for Disease Control and Prevention (CDC) ](https://www.cdc.gov) to assess people's health and nutritional status (in the U.S.). It includes both interviews and physical examinations of the interviewees. The survey updates every two years. For details about NHANES, please [click](https://www.cdc.gov/nchs/nhanes/index.htm?CDC_AA_refVal=https%3A%2F%2Fwww.cdc.gov%2Fnchs%2Fnhanes.htm) here.

## 4 - Data Cleaning

[NHANES 2013-2014 data ](https://wwwn.cdc.gov/nchs/nhanes/continuousnhanes/default.aspx?BeginYear=2013) is selected for this project because it includes reasonably comprehensive information than more recent surveys (recent data is still under process).

NHANES stores the raw data in different files based on category. This project uses MySQL to clean and manipulate the data.
  

Missing data is a great challenge for data cleaning. Data are missed for three reasons: refused, don't know, or does not apply (N/A). This project uses grouping average to replace the missing values. Common grouping factors are age, gender, race and education. Please refer to the SQL script for details.


Since NHANES records limited information for people under 20 years old, who are also less likely to suffer from the health conditions of concern, this research only studies people equal to or older than 20.


5,769 observations are included in the final dataset.

  

## 5 - Variable Selection and Visualization

### 5.1 - Selecting Xs

Four factors are considered for variable selection.

  

(1) The relevance of the data with the diseases.

(2) Sensitiveness of the data.

(3) Availability of the data.

(4) Total numbers of variables included

  

Twenty-three variables are selected as model inputs (another variable (poverty level) is droped in the final model due to availability issue).

  

|**#**|**Variable**| **Meaning** |**#**|**Variable**| **Meaning** |  
|---------|--|--|--|--|--|
|1| `age_group`|The tens digit of age (e.g. 36 -> 3)| 13|`pressure_check`|# checked blood pressure at home last 12 months|
|2| `gender`|Gender| 14|`family_heart`|Family history of heart attack|
|3| `race`|Race| 15|`family_diabetes`|Family history of diabetes|
|4| `weight`|Weight in kg| 16|`family_osteoporosis`|Family history of osteoporosis|
|5| `height`|Height in cm| 17|`sit_hour`|# of hours spend sitting on a typical day|
|6| `bmi`|BMI Index|18|`sleep_hour`|# of hours sleeping on a typical day
|7| `college`|Received bachelor degree or above| 19|`smoke_100`|Smoked at least 100 cigarettes in life|
|8| `drink_milk`|Drink milk at least weekly| 20|`drink_days`|# of days drink alcohol in the past twelve months|
|9| `household_size`|# of people in household| 21|`cost_food`|Monthly food expenditure (exclude restaurant and deliver)|
|10| `health_condition`|Health condition self-evaluation (out of 5)| 22|`cost_nonfood`|Monthly non-food expenditure|
|11| `fast_food`|# of meals from fast food or pizza place in the past seven days| 23|`cost_restaurant`|Monthly restaurant expenditure|
|12| `nutrition_info`|Would use restaurant nutrition info|24*|`poverty_level_category*`|Family poverty level index categories (Deleted eventually)|

  

Initially, the project uses PCA to reduce data dimensions, but this approach is not chosen because it makes interpretation difficult. Instead, **the variables are selected based on knowledge in medical science and economics.**

  

### 5.2 - Obtaining Y (The medical conditions)


Collecting diagnoses from the NHANES dataset may be trickier than people thought. For example, only comparing blood pressure value at physical examinations may generate biased hypertension diagnoses because patients take medicine to lower the pressure. Also, since the project is designed for business practitioners (not doctors), ***the conditions' thresholds have been relaxed (people near the threshold may be classified as patients)***. Such aggressive classification may help businesses provide disease prevention services. Finally, the prevalence rates are calculated based on the NHANES 2013-2014 dataset - that is, the number of identified patients over sample size.

The benchmarks used to define diagnoses are summarized below. ***(Note: If a condition have multiple criteria, people will be considered as "patient" if AT LEAST ONE criterion is meet)***

  

#|**Condition**|**Criteria**|**Related Variable from NHANES**|
|-----------------------------------------------------------|--|--|--|
|1| Depression <br> (Prevalence Rate = 0.22)                                                          |Over the last 2 weeks, feeling down, depressed, or hopeless for at least several days|`DPQ020`|
|2| High Fracture Risk <br> (Prevalence Rate = 0.20)                                                          |Self-reported that fracture after age 20;<br>10-year hip fracture risk score (assuming previous fracture) > 1.5 * Average (10-year hip fracture risk score (assuming previous fracture))|`DXXPRVFX`<br>`DXXFRAX1`|
||      |  Self-reported that fracture after age 20;<br>10-year major osteoporotic fracture risk score (assuming previous fracture) > 1.5 * Average (10-year major osteoporotic fracture risk score (assuming previous fracture))  |  `DXXPRVFX`<br>`DXXFRAX2`  |
||      |  Self-reported that NO fracture after age 20;<br>10-year hip fracture risk score (assuming no previous fracture) > 1.5 * Average (10-year hip fracture risk score (assuming no previous fracture))  |  `DXXPRVFX`<br>`DXXFRAX3`  |
||      |  Self-reported that NO fracture after age 20;<br>10-year major osteoporotic fracture risk score (assuming no previous fracture) > 1.5 * Average (10-year major osteoporotic fracture risk score (assuming no previous fracture))  |  `DXXPRVFX`<br>`DXXFRAX4`  |
|3| Hypertension <br> (Prevalence Rate = 0.42)                                                          |Average diastolic pressure (mm Hg) >= 90|`BPXDI1` <br> `BPXDI2` <br> `BPXDI3`|
||      |  Average systolic pressure (mm Hg) >= 140  |  `BPXSY1` <br> `BPXSY2` <br> `BPXSY3`  |
||      |  Now taking prescribed medicine to control blood pressure  |  `BPQ050A`  |
||      |  Been told by a doctor or other health professional that had hypertension  |  `BPQ020`  |
|4| High Cholesterol <br> (Prevalence Rate = 0.47)                                                          |[Total Cholesterol (mmol/L) >= 5.5](https://www.mayoclinic.org/tests-procedures/cholesterol-test/about/pac-20384601)|`LBDTCSI`|
||      |  Been told by a doctor or other health professional that blood cholesterol level was high?  |  `BPQ080`  |
||      |  Been told by a doctor or other health professional to take prescribed medicine to lower cholesterol  |  `BPQ090D`  |
|5| Diabetes <br> (Prevalence Rate = 0.16)                                                          |Been told by a doctor or health professional that (the person) have diabetes|`DIQ010`|
|6| Oral Health <br> (Prevalence Rate = 0.49)                                                          |Overall recommendation for oral care suggest the person should see a dentist|`OHAREC`|


### 5.3 - Visualization

The charts below visualize how the selected variables are related to the diseases concerned.

  

**(a) Depression versus Health Condition Self-Evaluation**

Plot 2(a) and 1(b) shows people with a worse (self-awared) health condition are more likely to get depressed

  ![say sth](https://github.com/simon201918/Disease_Prediction_with_NHANES/blob/main/Plots/1%20(a)%20Bar%20Plot%20of%20Depression%20by%20Health%20Condition.jpg?raw=true)
  
  ![say sth](https://github.com/simon201918/Disease_Prediction_with_NHANES/blob/main/Plots/1%20(b)%20Bar%20Plot%20of%20Depression%20by%20Health%20Condition(%25).jpg?raw=true)

**(b) Fracture (Risk) versus age**

Plot 2(a) and 2(b) shows fracture risk increases with age

  ![say sth](https://github.com/simon201918/Disease_Prediction_with_NHANES/blob/main/Plots/2%20(a)%20Bar%20Plot%20of%20Fracture%20Risk%20by%20Age%20Group.jpg?raw=true)
  
  ![say sth](https://github.com/simon201918/Disease_Prediction_with_NHANES/blob/main/Plots/2%20(b)%20Bar%20Plot%20of%20Fracture%20Risk%20by%20Age%20Group(%25).jpg?raw=true)
  

**(c) Hypertension versus education level**

Plot 3(a) and 3(b) shows hypertension is less frequently seen in higher educated populations, and a college degree makes a significant difference

   ![say sth](https://github.com/simon201918/Disease_Prediction_with_NHANES/blob/main/Plots/3%20(a)%20Bar%20Plot%20of%20Hypertension%20by%20Education%20Level.jpg?raw=true)
  
  ![say sth](https://github.com/simon201918/Disease_Prediction_with_NHANES/blob/main/Plots/3%20(b)%20Bar%20Plot%20of%20Hypertension%20by%20Education(%25).jpg?raw=true)

**(d-1) High Cholesterol versus Smoking Now**

Plot 4(a) and 4(b) shows smoking people are less likely to get high cholesterol - weird!

   ![say sth](https://github.com/simon201918/Disease_Prediction_with_NHANES/blob/main/Plots/4%20(a)%20Bar%20Plot%20of%20High%20Cholesterol%20by%20Smoking%20Now.jpg?raw=true)
  
  ![say sth](https://github.com/simon201918/Disease_Prediction_with_NHANES/blob/main/Plots/4%20(b)%20Bar%20Plot%20of%20High%20Cholesterol%20by%20Smoking%20Now(%25).jpg?raw=true)

Another version of the story: People quit smoking after diagnosed with high cholesterol. Now, check the demographic structure of smoking population (plot 4(c) and 4(d)).

   ![say sth](https://github.com/simon201918/Disease_Prediction_with_NHANES/blob/main/Plots/4%20(c)%20Bar%20Plot%20of%20Smoking%20Now%20by%20Age%20Group.jpg?raw=true)
  
  ![say sth](https://github.com/simon201918/Disease_Prediction_with_NHANES/blob/main/Plots/4%20(d)%20Bar%20Plot%20of%20Smoking%20Now%20by%20Age%20Group(%25).jpg?raw=true)

Based on the data, people tend to quit smoking after 60, or people who frequently smoke are less likely to reach senior age. Whichever hypothesis is correct, "smoking now" is not an excellent factor to predict high cholesterol.

**(d-2) High Cholesterol versus Smoking_100**

Now, switch to another variable - "Smoking_100", which shows whether the person has smoked at least 100 cigarettes in life. The plots are shown in 4(e) and 4(f).

   ![say sth](https://github.com/simon201918/Disease_Prediction_with_NHANES/blob/main/Plots/4%20(e)%20Bar%20Plot%20of%20High%20Cholesterol%20by%20Smoking%20History.jpg?raw=true)
  
  ![say sth](https://github.com/simon201918/Disease_Prediction_with_NHANES/blob/main/Plots/4%20(f)%20Bar%20Plot%20of%20High%20Cholesterol%20by%20Smoking%20Now(%25).jpg?raw=true)

Based on the data, people with a smoking history are slightly more likely to get high cholesterol than non-smokers, making more sense.

  

**(e) Diabetes versus Family History (Diabetes)**

As shown in 5(a) and 5(b), people with a family history of diabetes are three times more likely to get diabetes than those without a family history (or history unknown)

   ![say sth](https://github.com/simon201918/Disease_Prediction_with_NHANES/blob/main/Plots/5%20(a)%20Bar%20Plot%20of%20Diabetes%20by%20Family%20History.jpg?raw=true)
  
  ![say sth](https://github.com/simon201918/Disease_Prediction_with_NHANES/blob/main/Plots/5%20(b)%20Bar%20Plot%20of%20Diabetes%20by%20Family%20History(%25).jpg?raw=true)
  

**(f) Oral Health versus Poverty Level**

  

Based on 6(a) and 6(b), People with better financial conditions suffer less from oral health problems.

   ![say sth](https://github.com/simon201918/Disease_Prediction_with_NHANES/blob/main/Plots/6%20(a)%20Bar%20Plot%20of%20Oral%20Care%20Suggestion%20by%20Poverty%20Level.jpg?raw=true)
  
  ![say sth](https://github.com/simon201918/Disease_Prediction_with_NHANES/blob/main/Plots/6%20(b)%20Bar%20Plot%20of%20Oral%20Care%20Suggestion%20by%20Poverty%20Level(%25).jpg?raw=true)

However, the poverty level is not available in real-life (people unwilling to provide, or even illegal to ask). Therefore, this project uses monthly restaurant expenditure as a proxy.

  

## 6 - Modeling

  

### 6.1 - Training the optimal model for each condition

  

Eight candidate models are applied, tuned and compared for each of the six medical conditions of concern. The eight candidate models include: 

(1) Ridge Logistic

(2) Lasso Logistic

(3) K Nearest Neighbors (KNN)

(4) Naive Bayes

(5) Support Vector Machine (SVM)

(6) Decision Tree

(7) Random Forest

(8) Neural Network

The optimal models and their summaries are listed below (Validation Size = 769):

  

|**Condition**|**Optimal Model**|**Evaluation<br>(Y = Diagnosd)**|**Testing Performance**|**Vaildation Performance**|  |**Confusion Matrix**|  |
|---------|--|--|--|--|--|--|--|
|***Depression <br> (Prevalence Rate = 0.22)***|**Naive Bayes**|**Precision**|0.25|0.22| |**Predicted Healthy**|**Predicted Diagnosed**|
| | |**Recall**|0.51|0.55|**Actual Healthy**| 323 |294|
| | |**F1 Score**|0.33|0.31|**Actual Diagnosed**| 69 |83|
|***(High) Fracture Risk <br> (Prevalence Rate = 0.20)***| **Random Forest** |**Precision**|         0.75 |    0.76| |**Predicted Healthy**|**Predicted Diagnosed**|
| | |**Recall**|         0.65|  0.60   |**Actual Healthy**|     583  |  29|
| | |**F1 Score**|    0.70|  0.67   |**Actual Diagnosed**| 63 |  94|
|***Hypertension <br> (Prevalence Rate = 0.42)***| **Random Forest**   |**Precision**|         0.74 |    0.78| |**Predicted Healthy**|**Predicted Diagnosed**|
| | |**Recall**|         0.66|  0.68   |**Actual Healthy**|     385  |  63|
| | |**F1 Score**|    0.70|  0.72   |**Actual Diagnosed**| 103 |  218|
|***High Cholesterol <br> (Prevalence Rate = 0.47)***| **Random Forest**   |**Precision**|  0.67 |    0.64| |**Predicted Healthy**|**Predicted Diagnosed**|
| | |**Recall**|         0.70|  0.66   |**Actual Healthy**|     291  |  131|
| | |**F1 Score**|    0.68|  0.65   |**Actual Diagnosed**| 118 |  229|
|***Diabetes <br> (Prevalence Rate = 0.16)***| **Naive Bayes**   |**Precision**|         0.34 |    0.34| |**Predicted Healthy**|**Predicted Diagnosed**|
| | |**Recall**|         0.34|  0.33   |**Actual Healthy**|    549  |  87|
| | |**F1 Score**|    0.34|  0.33   |**Actual Diagnosed**| 89 |  44|
|***Oral Health <br> (Prevalence Rate = 0.49)***| **Support Vector Machine**   |**Precision**|         0.60 |    0.58| |**Predicted Healthy**|**Predicted Diagnosed**|
| | |**Recall**|         0.65|  0.60   |**Actual Healthy**|    233  |  160|
| | |**F1 Score**|    0.62|  0.59   |**Actual Diagnosed**| 152 |  224|

Because the model is designed to facilitate business decisions (not medical diagnosis), precision is a slightly more important factor than recall.

#### Quick Recap Table
|Factor|Meaning|
|---------------------|--|
|**Precision**|Among all the people who **PREDICTED** to be ill, how many of them are **ACTUALLY** ill.|
|   **Recall**|Among all the people who are **ACTUALLY** ill, how many of them are correctly **PREDICTED** as ill.|
|    **F1 Score**|A weighted average of the precision and recall|

  

### 6.2 - Grouping the models to derive probability matrix

  

Predicting whether someone has one or more of the six medical conditions is not the end of this project. Marketing practitioners may care more about the most likely issues versus other diseases because over-sending marketing materials could be annoying and even backfire the campaign. Therefore, the final step is to group the optimal model of each health condition and generates the likelihood matrix.

An example may help you understand this process better. Assume there are three people - Jack, Lily, and Sam. The table below listed their likelihood of getting certain medical conditions, and the last column shows the model recommendation.


|**Name**|**Depression**|**High Fracture Risk**|**Hypertension**|**High Cholesterol**|**Diabetes**|**Oral Health**|**Predicted Most Likely Condition**|
|---------|--|--|--|--|--|--|--|
|Jack|9%|42%|**83%**|69%|37%|40%|**Hypertension**|
|Lily|18%|23%|42%|54%|37%|**69%**|**Oral Health**|
|Sam|**39%**|21%|16%|32%|27%|8%|**Depression**|

  

Predictions are intuitive for Jack and Lily. Jack's most likely condition is hypertension (83%), and Lily's most likely situation is oral health (69%), so the model selects those conditions as recommendations. Sam looks healthier than Jack and Lily, and none of Sam's probability goes above 50%. However, the model decides that depression is the most likely condition for Sam, even though the exact value is low (39%). This decision may not make sense for doctors - doctors should not ask people to take medicine when the "patient" is 39% likely to have the condition. However, the prediction is still valuable to business practitioners, which will be discussed more in the final chapter.

  

## 7 - Project Evaluation

  

**The grouped model correctly predicts 56.3% of people with their most likely conditions in the unseen data, but how good is 56.3%?**

  

As described in chapter two, this project aims to facilitate decision makings for marketing and sales professionals - not to replace doctors and medical equipment. In other words, the project tries to **make the best educated-guess with LIMITED information**, and the educated-guess is used to **promote business decisions - not a diagnosis**. This explains why classifying Sam as a potential depression patient makes sense in the previous chapter. For instance, companies could send Sam marketing materials on depression prevention or early treatment.

**For the same reason, a 56.3% accuracy rate is satisfying, especially compared with the diseases' prevalence rates**. Having said that, the result may be improved with more domain expertise in medicine and including more relevant variables.
