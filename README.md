# SENSORS_Data - User_Behavior_Analytics_and_Conversion_Prediction

<img src="https://github.com/will-zw-wang/Sensors_Data-User_Behavior_Analytics_and_Conversion_Prediction/blob/master/images/Sensors_Data_Image.jpg" width="600" height="240">

- [**Report**](https://github.com/will-zw-wang/Sensors_Data-User_Behavior_Analytics_and_Conversion_Prediction/blob/master/Sensors_Data_Project_Report.pdf)
- [**Code**](https://github.com/will-zw-wang/Sensors_Data-User_Behavior_Analytics_and_Conversion_Prediction/tree/master/code)

## Project Objectives

Sensorsdata is a leading China-based and rapidly growing big data company and interested in **User-behavior Analytics and Conversion Prediction**. To help explore this question, they provided log data containing tens of thousands of log information of Sensorsdata main webpage for a week. 

## Dataset Description

- Dataset contains cache log information of Sensorsdata main webpage for a week.
- Dataset contains 150+ features in raw user log json file containing categorical, numerical and time series data.
  - Like actions on leaving the webpage, click a button, send verification code, apply for account, etc.   
- [**Log Description**](https://github.com/will-zw-wang/Sensors_Data/tree/master/log_description)

## Analysis Structure

1. Log Data Processing
2. EDA
3. Feature Engineering
4. Model Fitting, Models Comparison and HyperParameter Tuning
5. Analysis, Insights and Recommendations
6. Next Step


## Analysis Details

### 1. Log Data Processing
- **Identifed types of events**
- **Identifid properties of events**
- **Selected related properties to generate Dataframe**
- [**Detailed Code and Plotting**](https://github.com/will-zw-wang/Sensors_Data-User_Behavior_Analytics_and_Conversion_Prediction/blob/master/code/1_Log_Data%20Processing.ipynb)

### 2. EDA
- **Selected identifier**
- **Numeric variables**
  - **page_stayTime**: 
    - Insights: Only 11.41 % of the people who clicked the functional pages stayed more than 3 seconds.
    - Created feature **page_stayTime**: Summed up the total stay time by pages.
- **Categorical variables**
  - **event**: 
    - Insights: Most users did not attempt to submit a form.
    - Sign_up Funnel: page view(32620) -> btnClick(13866) -> click_send_cellphone(600) -> verify_cellphone_code(563) -> clickSubmit(513) -> formSubmit(791)
  - **day**: 
    - Insights: 2017-03-11 and 2017-03-12 were weekends and had the least number of counts, perhaps users visited our website more for work purposes.
    - Created feature **weekend**: With values: '1' for weekend, '0' and weekdays.
  - **title**:
    - Insights: 
      - Users visited our ‘demo’ page, ‘user manual’ page and ‘product’ page most frequently. 
      - If we plan to improve the layout of our pages, these three pages should have high priority.
  - **latest_referrer_host**:
    - Insights: 
      - Most of empty ‘latest_referrer_host’ were directly from sensordata website. 
      - The other users were referred mostly from 'baidu', '36kr', 'sogou' and 'google', especially 'baidu' which contributed times of referred users than the other hosts. 
      - Thus, if we want to run marketing campaign, 'baidu' should be allocated more budget to.
    - Created feature **latest_referrer_host_bin**: With values: 'sensordata', 'baidu' and 'others'.
  - **browser**:
    - Insights: Chrome was the most frequently used browser, much more frequently than the others.
    - Created feature **browser_bin**: With values: 'chrome' and 'others'.
  - **browser**:
    - Insights: Most of the users used 'windows' and 'macosx'.
    - Created feature **os_bin**: With values 'windows', 'macosx' and 'others'.
  - **ip**:
    - Insights: 
      - The same ip might be used by several users.
      - We mapped the ip to specific cities and notice most of the users came from 'Beijing', 'Shanghai', 'Shenzhen' and 'Guangzhou'.
    - Created feature **city_bin**: With values 'Beijing', 'Shanghai', 'Shenzhen', 'Guangzhou' and 'others'.
  - **button name**:
    - Insights: 
      - The mostly clicked buttons were 'request', 'demo', 'document' and 'product'.   
      - If we plan to improve the layout of our pages, the pages these buttons link to should have high priority.

- **Selected features**
- [**Detailed Code and Plotting**](https://github.com/will-zw-wang/Sensors_Data-User_Behavior_Analytics_and_Conversion_Prediction/blob/master/code/2_Data_Exploration_and_Cleaning.ipynb)

### 3. User Level Feature Engineering
- **Feature generation**
  - Created feautres **pages_total_stayTime**: Summed up the total stay time by pages per ‘dist_id’.
  - Created feautres **click_counts**: Summed up the click times per ‘dist_id’.
  - Created feautres **pages_viewed_counts**: Summed up the page viewed count per ‘dist_id’.
  - Created feautres **is_first_day**, **is_first_time** and **weekend**: corresponding median value per 'dist_id'.
  - Created feautres **latest_referrer_host_bin**, **latest_utm_source_bin**, **browser_bin**, **city_bin** and **model_bin**: corresponding the first value per 'dist_id'.
- **Transformed data and defined label**
  - Converted categorical variables into dummy variables
  - Defined label: We defined 'signup' with the action 'click_send_cellphone', which meant 'dist_id' attempts to sign up an account.
- **EDA with label**
  - Scatter_matrix: 'click_counts' and 'pages_viewed_counts' were highly correlated with 'sign_up'.
- **Explored sign up rate split by features**
  - **is_first_time**
    - Insights: All users with 'is_first_time' value '1' did not sign_up, which meant highly interested users would come to register another time.
  - **weekend**
    - Insights: Sign_up rate of 'weekend' was obviously lower than that of 'weekdays', perhaps users visited our website more for work purposes.  
  - **latest_utm_source_bin**
    - Insights: Sign_up rate of 'latest_utm_source_bin' with value 'baidu' was obviously higher than those of others, if we want to run marketing campaign, 'baidu' should be allocated more budget to.
  - **browser_bin**
    - Insights: Sign_up rate of 'browser_bin' with value 'chrome' was obviously higher than those of others.    
  - **city_bin**
    - Insights: Sign_up rate of 'city_bin' with value 'Shenzhen' was slightly higher than those of others.  
  - value distribution of numerical features of ‘sign_up_users’ vs that of ‘not_sign_up_users’
    - Insights: Distribution of 'click_counts' of 'sign_up_users' and 'not_sign_up_users' were obviously different, the same idea with 'pages_viewed_counts'.
- [**Detailed Code and Plotting**](https://github.com/will-zw-wang/Sensors_Data-User_Behavior_Analytics_and_Conversion_Prediction/blob/master/code/3_Feature_Engineering.ipynb)  

### 4. Model Fitting, Models Comparison and HyperParameter Tuning
- **Models comparison and reasoning**
  - **Logistic Regression**
    - Model Performance
      - <img src="https://github.com/will-zw-wang/Sensors_Data-User_Behavior_Analytics_and_Conversion_Prediction/blob/master/images/Model_Performance_LR.png" width="360" height="360">
    - **AUC** of test data was **0.9156** with **Logistic Regression**.
    - We tried to improve model performance with **Random Forest**.
  - **Random Forest**
    - Model Performance
      - <img src="https://github.com/will-zw-wang/Sensors_Data-User_Behavior_Analytics_and_Conversion_Prediction/blob/master/images/Model_Performance_RF.png" width="360" height="360">
    - **AUC** of test data was **0.9591** with **Random Forest**, better than that of **Logistic Regression** with **0.9156**.
      - **Reason**: There were feature interaction and non-linearity relationship between features and target in our data set, trees algorithms could deal with these problems while logistic regression cannot.
    - We tried to further improve model performance with **Gradient Boosting Trees**.
      - **Reason**: In general, **Gradient Boosting Trees** can perform better than **Random Forest**, because it additionally tries to find optimal linear combination of trees (assume final model is the weighted sum of predictions of individual trees) in relation to given train data. This extra tuning may lead to more predictive power.
  - **Gradient Boosting Trees**
    - Model Performance
      - <img src="https://github.com/will-zw-wang/Sensors_Data-User_Behavior_Analytics_and_Conversion_Prediction/blob/master/images/Model_Performance_GDBT.png" width="360" height="360">
    - **AUC** of test data was **0.9586** with **Gradient Boosting Trees**, was close to that of **Random Forest** with **0.9591**.
      - **Reason**: Our **Random forest** had already performed greatly in this dataset and hard for **Gradient Boosting Trees** to perform better.
    - Thus, we chose **Random Forest** as our preferred model.
    - Then we tried to implement **Random Forest with bootstraps for imbalance dataset** to check if balance dataset can improve model performance?
  - **Random Forest with bootstraps for imbalance dataset**
    - Model Performance
      - <img src="https://github.com/will-zw-wang/Sensors_Data-User_Behavior_Analytics_and_Conversion_Prediction/blob/master/images/Model_Performance_RF_Bootstrap.png" width="360" height="360">
    - **AUC** of test data was **0.9595** with **Random Forest with bootstraps for imbalance dataset**, was close to that of **Random Forest with the imbalance dataset** with **0.9591**.  
      - **Reason**: Our Random forest had already performed greatly with the imbalance dataset and did not need to balance the dataset.
    - Then we tried HyperParameter Tuning with Grid Search for Random Forest, to figure out whether we can do better.
  - **Random Forest HyperParameter Tuning with Grid Search**
    - Model Performance
      - <img src="https://github.com/will-zw-wang/Sensors_Data-User_Behavior_Analytics_and_Conversion_Prediction/blob/master/images/Model_Performance_RF_GS.png" width="360" height="360">
    - **AUC** of test data was **0.9617** with **Random Forest HyperParameter Tuning with Grid Search**, was slightly better than that of previous **Random Forest** with **0.9591**
    - we selected this model to explore the features importance to get some insights.
- [**Detailed Code and Plotting**](https://github.com/will-zw-wang/Sensors_Data-User_Behavior_Analytics_and_Conversion_Prediction/blob/master/code/4_Model_Fitting_and_Insights.ipynb) 

### 5. Analysis, Insights and Recommendations

- **Top 10 features analysis**
  - <img src="https://github.com/will-zw-wang/Sensors_Data-User_Behavior_Analytics_and_Conversion_Prediction/blob/master/images/Ranked_Feature_Importance_Generated_by_Random_Forest.png"> 
  - **demo_page_total_stayTime**: the longer time a user spent in ‘demo’ page was, the more likely the user would sign up. And its feature importance was larger than those of 'index_page_total_stayTime', 'about_page_total_stayTime' and courses_page_total_stayTime', which shows user was more likely to visit our 'demo' page than the others.
  - **index_page_total_stayTime**: the same idea with 'demo_page_total_stayTime', and its feature importance was larger than those of 'about_page_total_stayTime' and courses_page_total_stayTime'.
  - **pages_viewed_counts**: the more pages a user viewed, the more likely the user would sign up.
  - **click_counts**: the more click a user performed, the more likely the user would sign up.
  - **about_page_total_stayTime**: the same idea with 'demo_page_total_stayTime'.
  - **courses_page_total_stayTime**: the same idea with 'demo_page_total_stayTime'.
  - **latest_referrer_host_bin_baidu**: Users referred by 'baidu' were more likely to sign up than users referred by other channels.
  - **is_first_time**: As we notice in the previous 'feature exploration' part, all users with 'is_first_time' value '1' did not sign up, which meant highly interested users would come to register another time, we should give users more times to contract with us to make them sign up.
  - **latest_referrer_host_bin_sensordata**: Most of users visited our pages from sensordata website without any 'referrer_host', which meant most of campaigns except 'baidu' had no positive effects.
  - **city_bin_others**: the feature importance of 'city_bin_others' was larger than those of other 'city_bin' values, liked 'city_bin_Beijing', 'city_bin_Shanghai', which meant signup rate might be random among cities.

- **Funnel Analysis**
  - <img src="https://github.com/will-zw-wang/Sensors_Data-User_Behavior_Analytics_and_Conversion_Prediction/blob/master/images/Conversion_Funnel.png">  
  - **button_click_rate** was only 38.85%, most users did not click buttons on pages, perhaps the wording or color of our buttons were not attractive enough.    
  - **apply_for_trial_rate** was only 18.18%, most users did not click 'request' buttons on pages, perhaps our service description was not attractive enough.    
  - **signup_to_apply_for_trial_rate** was only 23.79%, most users who clicked 'request' button at the beginning did not apply when they were asked to provide phone number, perhaps the users cared about personal privacy.   
  - **signup_rate** was only 4.32%, most users who viewed the webpage did not attempt to sign up, the same idea with 'apply_for_trial_rate'.
  - **successfully_signup_rate** was 91.22%, we lost nearly 9% of users who attempting to signup, perhaps the efficiency of our sign up process still need to improve.
  - **note**: 
    - Here we defined **signup** with the action 'click_send_cellphone', which meant 'dist_id' attemps to sign up an account.
    - We defined **signup successfully** with 'isSuccess' property of 'formSubmit' was 'True'.
    - We defined **apply_for_trial** with 'name' property of 'btnClick' was 'request'.
  
- **Page_stayTime Analysis**
  - <img src="https://github.com/will-zw-wang/Sensors_Data-User_Behavior_Analytics_and_Conversion_Prediction/blob/master/images/page_stayTime.png"> 
  - The plot shows that most of user stayed in the page for less than 1 second(10^-1), and the 75 percentile 'page_stayTime' value is 0.226 second, which meant most of the users might leave when the page was loading.
  - Only 11.41 % of the people who clicked the functional pages stayed more than 3 seconds

- **Insights and Recommendations**
  - Improve our page quality, given the high feature importance of 'pages_total_stayTime' and very low percent of users stayed in our pages more than 3 seconds.
    - Like: hire web UX designer to improve the layout of our pages, especially 'demo' page and 'index' page, modify the wording or color of our buttons, polish our service description.
  - Consider providing other registration options and improve efficiency of our sign up process, given low 'signup_to_apply_for_trial_rate' and low 'successfully_signup_rate'.
    - Like: allow users to sign up with e-mail or social network accounts.
  - Make adjustment to product promotion and campaign strategy, given most our campaigns have no significant effect.
    - Note: as we don't know the current product promotion and campaign strategy of sensordata, we analyze two different scenarios as below:
      - 1/ if sensordata had already invested lots of money to product promotion and campaign, it should adjust the investment allocation and invest more budget in 'Baidu', which has relatively better performance than the medias.
      - 2/ if sensordata did not invest much product promotion and campaign before, it should allocate more budget in this area, 'baidu', '36kr', 'sogou' and 'google' would be good choices, especially 'baidu' which contributed times of referred users than the other hosts.


### 6. Next Step
- Besides the insights mentioned above, I think there are aspects we can further dive deep, like:
  - Detailed analysis on different medium and campaign contributions, try to figure out which channels to invest and how to allocate the budgets.
  - Detailed analysis on user behavior on specific pages, try to figure out which part of the page users pay most attention to, improve the content of interest and redesign the sections that are not valued.
  - It’s also important to track performance over time. If we have more data, we can see whether we’re improving or not, by comparing funnels for each month.
