By Eric Sun (z9sun@ucsd.edu) & Sunan Xu (sux002@ucsd.edu)

---

# Framing the Problem
Power outages can have significant impacts on society, causing disruptions in daily life, affecting businesses, and posing potential risks to public safety. Understanding the patterns and characteristics of power outages can help utilities and policymakers develop strategies to improve the resilience and reliability of the electrical grid.

The climate region in which a power outage occurs can provide valuable insights into the causes and consequences of outages. Climate regions are defined based on the prevailing weather conditions and climatic factors of a specific geographic area. The West Climate region, for example, may be characterized by dry and hot conditions, while other regions may experience colder temperatures or more severe weather events.

By predicting whether a power outage occurs in the West Climate region or not, we can gain a better understanding of the geographical distribution of outages and identify any specific factors or vulnerabilities associated with this region. This information can help utilities allocate resources, implement preventive measures, and improve response plans for power outages in the West Climate region.

To make accurate predictions, we will utilize historical data on power outages that have occurred after 2012. This dataset includes various features such as the `year`, `month`, `NERC.REGION`, `CLIMATE.REGION`, `CLIMATE.CATEGORY`, `CAUSE.CATEGORY`, `CAUSE.CATEGORY.DETAIL`, `OUTAGE.DURATION(mins)`, `DEMAND.LOSS.MW(Megawatt)`, `CUSTOMERS.AFFECTED`, and more.

By training a classification model using this data, we aim to develop a predictive tool that can assist in identifying whether a power outage is likely to occur in the West Climate region, based on the available information at the time of prediction. This can ultimately contribute to more effective planning, preparation, and mitigation strategies for power outages.

## About data cleaning
Just like what we have done in the previous analysis, which can be found [here](https://ericsun153.github.io/Illuminating_US_Outage_Landscape/). We have converted the `xlsx` file into `csv` file and drop all unnecessary rows and columns, and leave all the columns that has no direct symbolic relationship/characteristics of a specific state or region we would like to predict. At last, and we are able to obtain a train and test set separately by specifically selecting according to the year of power outage happening, and here are the first few lines of out train set:

|    |   YEAR |   MONTH | NERC.REGION   |   CLIMATE.REGION | CLIMATE.CATEGORY   | CAUSE.CATEGORY   | CAUSE.CATEGORY.DETAIL   |   OUTAGE.DURATION(mins) |   DEMAND.LOSS.MW(Megawatt) |   CUSTOMERS.AFFECTED |   RES.PRICE(cents / kilowatt-hour) |   COM.PRICE(cents / kilowatt-hour) |   IND.PRICE(cents / kilowatt-hour) |   TOTAL.PRICE(cents / kilowatt-hour) |   RES.SALES(Megawatt-hour) |   COM.SALES(Megawatt-hour) |   IND.SALES(Megawatt-hour) |   TOTAL.SALES(Megawatt-hour) |   RES.PERCEN(%) |   COM.PERCEN(%) |   IND.PERCEN(%) |   RES.CUSTOMERS |   COM.CUSTOMERS |   IND.CUSTOMERS |   TOTAL.CUSTOMERS |   RES.CUST.PCT(%) |   COM.CUST.PCT(%) |   IND.CUST.PCT(%) |   PC.REALGSP.STATE(USD) |   PC.REALGSP.USA(USD) |   PC.REALGSP.REL(fraction) |   PC.REALGSP.CHANGE(%) |   UTIL.REALGSP(USD) |   TOTAL.REALGSP(USD) |   UTIL.CONTRI(%) |   PI.UTIL.OFUSA(%) |   POPULATION |   ANOMALY.LEVEL |
|---:|-------:|--------:|:--------------|-----------------:|:-------------------|:-----------------|:------------------------|------------------------:|---------------------------:|---------------------:|-----------------------------------:|-----------------------------------:|-----------------------------------:|-------------------------------------:|---------------------------:|---------------------------:|---------------------------:|-----------------------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|------------------:|------------------:|------------------:|------------------:|------------------------:|----------------------:|---------------------------:|-----------------------:|--------------------:|---------------------:|-----------------:|-------------------:|-------------:|----------------:|
|  0 |   2011 |       7 | MRO           |                0 | normal             | severe weather   | nan                     |                    3060 |                        nan |                70000 |                              11.6  |                               9.18 |                               6.81 |                                 9.28 |                2.33292e+06 |                2.11477e+06 |                2.11329e+06 |                  6.56252e+06 |         35.5491 |         32.225  |         32.2024 |     2.30874e+06 |          276286 |           10673 |       2.5957e+06  |           88.9448 |           10.644  |          0.411181 |                   51268 |                 47586 |                    1.07738 |                    1.6 |                4802 |               274182 |          1.75139 |                2.2 |  5.34812e+06 |            -0.3 |
|  2 |   2010 |      10 | MRO           |                0 | cold               | severe weather   | heavy wind              |                    3000 |                        nan |                70000 |                              10.87 |                               8.19 |                               6.07 |                                 8.15 |                1.46729e+06 |                1.80168e+06 |                1.9513e+06  |                  5.22212e+06 |         28.0977 |         34.501  |         37.366  |     2.30029e+06 |          276463 |           10150 |       2.58690e+06 |           88.9206 |           10.687  |          0.392361 |                   50447 |                 47287 |                    1.06683 |                    2.7 |                4571 |               267895 |          1.70627 |                2.1 |  5.3109e+06  |            -1.5 |
|  3 |   2012 |       6 | MRO           |                0 | normal             | severe weather   | thunderstorm            |                    2550 |                        nan |                68200 |                              11.79 |                               9.25 |                               6.71 |                                 9.19 |                1.85152e+06 |                1.94117e+06 |                1.99303e+06 |                  5.78706e+06 |         31.9941 |         33.5433 |         34.4393 |     2.31734e+06 |          278466 |           11010 |       2.60681e+06 |           88.8954 |           10.6822 |          0.422355 |                   51598 |                 48156 |                    1.07148 |                    0.6 |                5364 |               277627 |          1.93209 |                2.2 |  5.38044e+06 |            -0.1 |
|  5 |   2010 |      11 | MRO           |                0 | cold               | severe weather   | winter storm            |                    1860 |                        nan |                60000 |                              10.63 |                               8.34 |                               6.15 |                                 8.28 |                1.67635e+06 |                1.78614e+06 |                1.90987e+06 |                  5.37415e+06 |         31.1928 |         33.2358 |         35.5382 |     2.30029e+06 |          276463 |           10150 |       2.58690e+06 |           88.9206 |           10.687  |          0.392361 |                   50447 |                 47287 |                    1.06683 |                    2.7 |                4571 |               267895 |          1.70627 |                2.1 |  5.3109e+06  |            -1.4 |
|  6 |   2010 |       7 | MRO           |                0 | cold               | severe weather   | tornadoes               |                    2970 |                        nan |                63000 |                              11.41 |                               9.11 |                               6.71 |                                 9.12 |                2.18754e+06 |                2.10083e+06 |                2.08471e+06 |                  6.37494e+06 |         34.3147 |         32.9545 |         32.7017 |     2.30029e+06 |          276463 |           10150 |       2.58690e+06 |           88.9206 |           10.687  |          0.392361 |                   50447 |                 47287 |                    1.06683 |                    2.7 |                4571 |               267895 |          1.70627 |                2.1 |  5.3109e+06  |            -0.9 |

And here is also the first few lines of our test set:

|    |   YEAR |   MONTH | NERC.REGION   |   CLIMATE.REGION | CLIMATE.CATEGORY   | CAUSE.CATEGORY     | CAUSE.CATEGORY.DETAIL   |   OUTAGE.DURATION(mins) |   DEMAND.LOSS.MW(Megawatt) |   CUSTOMERS.AFFECTED |   RES.PRICE(cents / kilowatt-hour) |   COM.PRICE(cents / kilowatt-hour) |   IND.PRICE(cents / kilowatt-hour) |   TOTAL.PRICE(cents / kilowatt-hour) |   RES.SALES(Megawatt-hour) |   COM.SALES(Megawatt-hour) |   IND.SALES(Megawatt-hour) |   TOTAL.SALES(Megawatt-hour) |   RES.PERCEN(%) |   COM.PERCEN(%) |   IND.PERCEN(%) |   RES.CUSTOMERS |   COM.CUSTOMERS |   IND.CUSTOMERS |   TOTAL.CUSTOMERS |   RES.CUST.PCT(%) |   COM.CUST.PCT(%) |   IND.CUST.PCT(%) |   PC.REALGSP.STATE(USD) |   PC.REALGSP.USA(USD) |   PC.REALGSP.REL(fraction) |   PC.REALGSP.CHANGE(%) |   UTIL.REALGSP(USD) |   TOTAL.REALGSP(USD) |   UTIL.CONTRI(%) |   PI.UTIL.OFUSA(%) |   POPULATION |   ANOMALY.LEVEL |
|---:|-------:|--------:|:--------------|-----------------:|:-------------------|:-------------------|:------------------------|------------------------:|---------------------------:|---------------------:|-----------------------------------:|-----------------------------------:|-----------------------------------:|-------------------------------------:|---------------------------:|---------------------------:|---------------------------:|-----------------------------:|----------------:|----------------:|----------------:|----------------:|----------------:|----------------:|------------------:|------------------:|------------------:|------------------:|------------------------:|----------------------:|---------------------------:|-----------------------:|--------------------:|---------------------:|-----------------:|-------------------:|-------------:|----------------:|
|  1 |   2014 |       5 | MRO           |                0 | normal             | intentional attack | vandalism               |                       1 |                        nan |                  nan |                              12.12 |                               9.71 |                               6.49 |                                 9.28 |                1.58699e+06 |                1.80776e+06 |                1.88793e+06 |                  5.28423e+06 |         30.0325 |         34.2104 |         35.7276 |     2.34586e+06 |          284978 |            9898 |       2.64074e+06 |           88.8335 |           10.7916 |          0.37482  |                   53499 |                 49091 |                    1.08979 |                    1.9 |                5226 |               291955 |          1.79    |                2.2 |  5.45712e+06 |            -0.1 |
|  4 |   2015 |       7 | MRO           |                0 | warm               | severe weather     | nan                     |                    1740 |                        250 |               250000 |                              13.07 |                              10.16 |                               7.74 |                                10.43 |                2.02888e+06 |                2.16161e+06 |                1.77794e+06 |                  5.97034e+06 |         33.9826 |         36.2059 |         29.7795 |     2.37467e+06 |          289044 |            9812 |       2.67353e+06 |           88.8216 |           10.8113 |          0.367005 |                   54431 |                 49844 |                    1.09203 |                    1.7 |                4873 |               292023 |          1.6687  |                2.2 |  5.48959e+06 |             1.2 |
|  8 |   2015 |       3 | MRO           |                0 | warm               | intentional attack | sabotage                |                     155 |                         20 |                 5941 |                              11.53 |                               8.89 |                               6.61 |                                 9.03 |                1.8443e+06  |                1.95687e+06 |                1.79586e+06 |                  5.59949e+06 |         32.9369 |         34.9472 |         32.072  |     2.37467e+06 |          289044 |            9812 |       2.67353e+06 |           88.8216 |           10.8113 |          0.367005 |                   54431 |                 49844 |                    1.09203 |                    1.7 |                4873 |               292023 |          1.6687  |                2.2 |  5.48959e+06 |             0.6 |
|  9 |   2013 |       6 | MRO           |                0 | normal             | severe weather     | hailstorm               |                    3621 |                        nan |               400000 |                              12.71 |                              10.31 |                               7.29 |                                10    |                1.68862e+06 |                1.8951e+06  |                1.90538e+06 |                  5.49063e+06 |         30.7546 |         34.5151 |         34.7023 |     2.32973e+06 |          281187 |           11383 |       2.62230e+06 |           88.843  |           10.7229 |          0.434084 |                   52479 |                 48396 |                    1.08437 |                    1.7 |                5613 |               284542 |          1.97264 |                2.3 |  5.42054e+06 |            -0.2 |
| 10 |   2013 |       6 | MRO           |                0 | normal             | severe weather     | hailstorm               |                    7740 |                        nan |               193000 |                              12.71 |                              10.31 |                               7.29 |                                10    |                1.68862e+06 |                1.8951e+06  |                1.90538e+06 |                  5.49063e+06 |         30.7546 |         34.5151 |         34.7023 |     2.32973e+06 |          281187 |           11383 |       2.62230e+06 |           88.843  |           10.7229 |          0.434084 |                   52479 |                 48396 |                    1.08437 |                    1.7 |                5613 |               284542 |          1.97264 |                2.3 |  5.42054e+06 |            -0.2 |

## Prediction Problem: Classification
The prediction problem is to classify whether a power outage occurring after 2012 (since 2013) is in the West Climate region or not, and we will use the data before 2013 to predict the state appearance of all number of power outages since 2013 in US.

## Response Variable
The response variable is a binary variable indicating whether a power outage is in the West Climate region or not. It will have two possible values: 1 for being in the West Climate region and 0 for not being in the West Climate region.

## Justification
The choice of predicting whether a power outage is in the West Climate region or not is based on the assumption that the region's climate might have an impact on power outage occurrence and characteristics. By determining if a power outage is in the West Climate region, we can potentially identify patterns and factors specific to that region.

## Features
To train the model, we will use columns that contain characteristics different among each outage, excluding the timestamp and columns directly equal to the postal code column (e.g., U.S._state). These columns will provide relevant information for predicting the power outage's location. At the time of prediction, we would have access to the features mentioned above for the power outage in question. We would not have any future information beyond what is available in the dataset.

## Metric for Evaluation
To evaluate the model's performance, we can use metrics such as accuracy or R square. However, given that the dataset is likely to have imbalanced classes (as the number of outages in the West Climate region may differ from those outside the region), it would be advisable to use metrics that are more robust to class imbalance, such as R square.

## Justification for Metric Choice
The choice of using R square over accuracy is due to the potential class imbalance in the dataset. Accuracy alone may provide misleading results when the classes are imbalanced, as a high accuracy score could be achieved by simply predicting the majority class. R square, which considers both precision and recall, provides a more balanced evaluation of the model's performance when dealing with imbalanced datasets.

---

# Baseline Model

## Model Description
The model used in this prediction task is a logistic regression model with one-hot encoding for categorical features. The selected features for the model are 'NERC.REGION', 'CAUSE.CATEGORY', and 'OUTAGE.DURATION(mins)'.

## Features
'NERC.REGION': This is a nominal feature representing the NERC (North American Electric Reliability Corporation) region where the power outage occurred. It is a categorical variable.
'CAUSE.CATEGORY': This is a nominal feature describing the category of the cause of the power outage. It is a categorical variable.
'OUTAGE.DURATION(mins)': This is a quantitative feature representing the duration of the power outage in minutes. It is a numerical variable.

## Encoding
The model uses one-hot encoding to convert the categorical features ('NERC.REGION' and 'CAUSE.CATEGORY') into numerical representation. This encoding technique creates binary columns for each unique category, indicating the presence or absence of that category in the data. The 'remainder' parameter in the ColumnTransformer is set to 'passthrough', which means the numerical feature ('OUTAGE.DURATION(mins)') is passed through without any encoding.

## Model Performance
The model achieved a training accuracy of 94.04% and a testing accuracy of 90.29%. The precision and recall scores for the model are 0.617 and 0.649, respectively.

Based on the accuracy and other performance metrics, the current model can be considered good. It achieves a relatively high accuracy on both the training and testing sets, indicating that it can effectively classify whether a power outage occurs in the West Climate region or not. However, it is important to note that the precision and recall scores are relatively lower, which suggests that the model may have some difficulty in correctly identifying positive instances (outages in the West Climate region). Further evaluation and refinement may be necessary to improve these scores and ensure a more balanced performance across all classes.

![](asset/confusionmatrix.jpg)

The confusion matrix shows that there are 363 true positives (TP), 20 false negatives (FN), 37 true negatives (TN), and 23 false positives (FP). These values provide a detailed breakdown of the model's predictions, allowing for a deeper analysis of its performance.

## Summary
Upon analyzing the performance of the current model, there are a few observations to consider. The model achieves high accuracy, indicating that it is able to make correct predictions for the majority of instances. However, the precision and recall scores are relatively lower compared to the accuracy.

Precision represents the proportion of correctly predicted positive instances out of the total instances predicted as positive. In this context, it measures the ability of the model to correctly identify power outages occurring in the West Climate region. The precision score of 0.617 suggests that the model has some difficulty in precisely identifying these instances. There is a possibility of false positives, where the model incorrectly classifies a power outage as occurring in the West Climate region.

Recall, also known as sensitivity or true positive rate, represents the proportion of correctly predicted positive instances out of the actual positive instances. It measures the ability of the model to capture all the power outages occurring in the West Climate region. The recall score of 0.649 indicates that the model captures a substantial portion of these instances but still misses some, resulting in false negatives.

Considering the imbalanced nature of the classes (West Climate region vs. other regions), where the West Climate region may be a minority class, the lower precision and recall scores can be partly attributed to the class imbalance. It is important to note that optimizing for both precision and recall can be a trade-off, and the choice depends on the specific requirements of the problem. In some cases, precision might be more critical, while in others, recall might be the primary concern.

To further improve the model's performance, additional steps can be taken, such as:

- Feature engineering: Explore and include additional relevant features that might have a strong correlation with the West Climate region. These features could provide more discriminatory power and improve the model's predictive ability.

- Hyperparameter tuning: Experiment with different hyperparameter settings for the logistic regression model or try alternative classification algorithms to find a configuration that better balances precision and recall.

- Handling class imbalance: Implement techniques to address the class imbalance issue, such as oversampling the minority class (West Climate region) or using weighted loss functions to give more importance to the minority class during training.

Upon all these analysis, we will continue and use these strategies to train our final model using random forest classifier, which will be better and easier at hyperparam tuning process.

---

# Final Model

## Model Choosing and features:
After conducting several trials, we have decided to use the random forest classifier as our model for two main reasons. Firstly, although logistic regression performs well as a baseline model, it has a limited number of tunable hyperparameters compared to other models. This makes it challenging for us to fine-tune the final model effectively. Secondly, our dataset contains numerous categorical features, suggesting that a classifier may be a better choice. Here are the features we have chosen for our model:

- `CLIMATE.CATEGORY`: This feature is transformed using one-hot encoding. It provides valuable information about a region's climate, as different climate categories can have distinct patterns or effects on the predicted climate region.

- `CAUSE.CATEGORY.DETAIL`: Transformed using one-hot encoding, this feature captures specific details about the cause category, which could influence the climate region.

- `CAUSE.CATEGORY`: Also transformed using one-hot encoding, this feature represents the cause category of the outage and offers insights into the climate region.

- `PC.REALGSP`: Scaled using StandardScaler, this feature represents the real gross state product and is standardized to have a mean of 0 and unit variance. It may be relevant for predicting the climate region.

- `OUTAGE.DURATION(mins)`: Scaled using StandardScaler, this feature represents the duration of the outage in minutes. Scaling helps handle features with different scales and magnitudes. The duration of the outage may be related to the climate region, as regions with severe weather conditions may experience longer power outages.

- `PI.UTIL.OFUSA(%)`: Scaled using StandardScaler, this feature represents the utility of power infrastructure in the USA. Scaling is applied for consistency and to prevent dominance by features with larger values. The distribution of power infrastructure across different areas can provide information about the climate.

## Model Performance
Even before tuning any hyperparameters, our final model already outperformed the previous version. The test accuracy is approximately 93%. We utilized GridSearch to find the best hyperparameter values, including the number of estimators, maximum features, and minimum sample splits. This tuning resulted in a better-performing model, although the improvement was not significant. Ultimately, our accuracy reached around 93.5%. The recall is approximately 0.95, and the precision is around 0.67, showing significant improvement compared to our baseline model.

## Summary
The random forest classifier yielded promising results for our prediction task. It enhanced the overall accuracy and recall of our model. Moreover, this process highlighted the distinct advantages of each model. Logistic regression performed well even with a limited number of features, but the random forest classifier demonstrated superior performance when additional features were incorporated, along with optimized hyperparameters. Model selection is a crucial step in this process, and fine-tuning hyperparameters can further enhance model performance.

---

# Fairness Analysis

## Accuracy Analysis
For our fairness assessment, we have categorized the test dataset into two groups: power outages affecting more than 50,000 people and those affecting fewer than 50,000 individuals. An outage affecting more than 50,000 people is considered a severe outage. Our primary evaluation metric is accuracy. We propose a null hypothesis asserting that our model's accuracy for determining outage severity is roughly equivalent across all cases, with any observed differences attributable to random variability. Conversely, our alternative hypothesis suggests that the model demonstrates unfairness, with a higher accuracy for less severe power outages than for severe ones. We have selected the accuracy disparity between less severe and severe outages as our test statistic, with a significance level of 0.01. After running a permutation test 5,000 times, we obtained a p-value of 0.1286, which exceeds our significance level. This outcome leads us to retain the null hypothesis, indicating that our model, based on this accuracy metric, is fair. However, we cannot definitively assert its complete fairness as the permutation test results are also contingent on random chance. Hence, we recommend further testing with more data to verify if it is 'truly fair'.

<iframe src="asset/fairness2.html" width=800 height=600 frameBorder=0></iframe>


## R squared Analysis
Our secondary evaluation metric is the R-squared value. We present a similar null hypothesis suggesting that our model's R-squared value for determining outage severity is approximately equal in all scenarios, and any discrepancies are due to random chance. Alternatively, our model could be unfair if the R-squared value is higher for less severe outages compared to severe ones. We've chosen the R-squared difference between less severe and severe outages as our test statistic, with a significance level of 0.01. Upon executing a permutation test 5,000 times, we obtained a p-value of 0.0112. As this value is above our significance level, we cannot reject the null hypothesis, suggesting that our model is fair based on the R-squared analysis. However, it is worth noting that this p-value is very close to our set significance level. Given that permutation test results can vary with each iteration, we cannot confidently assert that our model is fair based on the R-squared fairness analysis alone.

<iframe src="asset/fiarness1.html" width=800 height=600 frameBorder=0></iframe>

---