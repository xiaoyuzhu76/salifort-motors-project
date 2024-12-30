# Salifort Motors: Predict and Improve Employee Retention
a capstone project for Google Advanced Data Analytics Certificate program

## Executive Summary

* An XGBoost model can quite effectively identify an employee who is likely to leave the company. The recall rate of the model is 91% (of all the employees who left, the model was able to identify 91% of them), and a precision rate of 96% (of all the employees predicted by the model as "will leave", 96% is correct). This model can be deployed to help Salifort identify employee turnover before it happened.
* We have a serious issue with workload - the majority of workers are working more than 40 hours a week, and this is the single most importance factor. Management and HR need to better understand why this is the case, and reduce workload and/or improve efficiency.
* Hardworking employees should be recognized and rewarded. Only 1% of the employees was ever promoted in the last 5 years. This by itself is an astonishing number.
* Satisfaction level is a good predictor of employee turnover, while HR could gain more insights as to why employees are dissatisfied.

## Overview

### About the company

Salifort Motors is a fictional French-based alternative energy vehicle manufacturer. Its global workforce of over 100,000 employees research, design, construct, validate, and distribute electric, solar, algae, and hydrogen-based vehicles. Salifort’s end-to-end vertical integration model has made it a global leader at the intersection of alternative energy and automobiles.        

### Business case

Currently, there is a high rate of turnover among Salifort employees. (Note: In this context, turnover data includes both employees who choose to quit their job and employees who are let go). Salifort’s senior leadership team is concerned about how many employees are leaving the company. Salifort strives to create a corporate culture that supports employee success and professional development. Further, the high turnover rate is costly in the financial sense. Salifort makes a big investment in recruiting, training, and upskilling its employees. 

If Salifort could predict whether an employee will leave the company, and discover the reasons behind their departure, they could better understand the problem and develop a solution. 

As a first step, the leadership team asks Human Resources to survey a sample of employees to learn more about what might be driving turnover. As a data specialist working for Salifort Motors, I have received the results of a recent employee survey. The senior leadership team has asked to analyze the data to come up with ideas for how to increase employee retention. To help with this, I am asked to design a model that predicts whether an employee will leave the company based on their department, number of projects, average monthly hours, and any other data points that may be helpful. A good model will help the company increase retention and job satisfaction for current employees, and save money and time training new employees. 

## Reflection before analysis

The primary stakeholder of the project is leadership team of Salifort. The HR department is also an important stakeholder. On one hand, they designed and implemented the survey, and will be an important resource for us to gain business insights. On the other hand, HR department is also a critical player in the execution if any findings from our analyses result in business actions. 

The goal of this project is to predict whether an employee will leave the company, and discover the reasons behind their departure. In every step of the way, we should be wary of any ethical concerns that may arise. It is important that our recommendations do not result in any unfair treatments to a certain group of employees.

## Exploratory Data Analysis

### Data dictionary

This project uses a dataset called **HR_capstone_dataset.csv**. It represents 10 columns of self-reported information from employees of a multinational vehicle manufacturing corporation. The dataset can be found [here](https://www.kaggle.com/datasets/mfaisalqureshi/hr-analytics-and-job-prediction?select=HR_comma_sep.csv) on Kaggle.

The dataset contains: 14,999 rows – each row is a different employee’s self-reported information; and 10 columns.

| Column name           | Type  | Description                                                       |
|-----------------------|-------|-------------------------------------------------------------------|
| satisfaction_level    | int64 | The employee’s self-reported satisfaction level [0-1]             |
| last_evaluation       | int64 | Score of employee's last performance review [0–1]                 |
| number_project        | int64 | Number of projects employee contributes to                        |
| average_monthly_hours | int64 | Average number of hours employee worked per month                 |
| time_spend_company    | int64 | How long the employee has been with the company (years)           |
| work_accident         | int64 | Whether or not the employee experienced an accident while at work |
| left                  | int64 | Whether or not the employee left the company                      |
| promotion_last_5years | int64 | Whether or not the employee was promoted in the last 5 years      |
| department            | str   | The employee's department                                         |
| salary                | str   | The employee's salary (low, medium, or high)                      |

### Inspecting the data

There is no missing value in the dataset, but there are quite a few duplicated rows. Considering many variables (such as satisfaction level, last evaluation) are continuous variables that could take any value between 0 and 1, it is not likely that two or more individuals have the same values across all variables. We removed these duplicates.

![image](https://github.com/user-attachments/assets/250d6d2f-e9b5-40c0-9900-0f555514ee74)

**Outliers:** We found potential outliers in `time_spend_company` which is the tenure an employee stayed with Salifort. Close to 95% of the surveyed employees have been with the company for under 6 years. The other numeric variables seem to be symmetrically distributed.

**Class imbalance:** All the categorical variables show class imbalance. When constructing a model, we need to make sure the minority class is represented in the train and test datasets.
* 16.6% of the survey participants have left the company. There may be survivor bias, as it is much more difficult to collect information from those who already left the company at the time of the survey.
* 15% had experienced a work accident.
* Only 1% of the surveyed employees have had a promotion in the past 5 years. This immediately strikes us as a red flag for career development and recognition.
* Only 8% were in a high salary grade.
* The sales department represents 25% of the survey participants. It is worth checking with HR department whether this reflect the true population of the company.

### Key findings

After inspecting the pairwise relationships between whether an employee left the company and all the other potential explanatory variables, we have the following findings：
* Not surprisingly, those having lower satisfaction level with the company are more likely to leave.
* Those who worked longer hours and those who received better performance evaluations are more likely to leave on average. This is not a good sign as we are losing hardworking top performers! We may also spot more dispersion in the group who left. This is worth looking into.
* Those who have been with the company longer are more likely to leave. This by itself may not be alarming for Salifort, because it may as well be true for all companies.
* Those who contributed to more projects are more likely to leave. There may be collinearity between this variable and work tenure - the longer the person has been with the company, the more projects they have worked on.
 
![image](https://github.com/user-attachments/assets/ef0d5bbe-e671-413f-8195-bb179c2b36db)

* Those who had experienced an accident at work are more likely to stay. This may have something to do with Salifort's corporate policies how they take care of the injured. It may not be relevant to our current project, but a 15% accident rate seems to be pretty high.
* We noticed that there is only about 1% of the surveyed employees who got a promotion in the last 5 years, and it appears to be an important factor that impacts our employee turnover. Those who got promoted were much more likely to stay with the company.
* It is also not surprising that the higher the salary grade, the more likely an employee will stay with the company.
* R&D and management are two departments where team members are more likely to stay. This may be related to the salary and promotion factor, and it could also be that direct participation in the company's core business is motivational. The difference between departments 

![image](https://github.com/user-attachments/assets/20d2c606-c415-450c-80ff-c883d34c88a7)

* In general, most employees at Salifort were heavily overworked. This chart shows that majority of them worked longer than a typical 40-hour work week (equating 167 hours per month), and many worked more than 60 hours per week. Majority of those who left are those who worked the longest hours, performance evaluations were good, but did not receive any promotion in the past 5 years. There is a lot can be done to understand why everyone is working long hours, and why those who worked long with good performance were not recognized and rewarded.

![image](https://github.com/user-attachments/assets/a2b40905-d4ac-405a-9681-2b323411638f)

All the explanatory variables seems to be good candidate to predict whether or not an employee will leave the company. And unlike what we suspected, we do not see strong evidence of pairwise correlation that could lead to multicollinearity. Next, we will use all variables as predictors of whether an employee will leave the company, and construct a model. 

![correlation](https://github.com/user-attachments/assets/fd19ab74-364d-4040-a548-28f982255e15)

The outcome we are trying to predict is binary, therefore, we can either use a logistic regression or a tree-based model. Let's construct both models and evaluate them.

## Model construction and selection

Prior to building any model, we first establish that a high recall score will be the criteria for model selection. A good recall rate means that the model is good at spotting someone who is likely to leave the company, so the leadership team and HR department can take action to retain valuable employees. Of course, we do not want to achieve high recall at the cost of precision, which would incur a lot of cost to the company. Why? Because simply labelling everyone as "will leave" can boost recall to 100%, while destroying precision.

### Logistic regression model

We first start with building a logistic regression model. The beauty of this model is that it is simple and easy to interpret - we can see which explanatory variables are statistically significant in predicting employee turnover, and we can quatify the relationship. The first thing we need to be cautious about is that logistic regression is highly sensitive to outliers, which we did observe in employee tenure. Therefore, outliers were removed prior to running the logistic regression model.

As it turns out, although we found meaningful explanatory variables in the EDA, such as hours worked, the model still does not do a good job at making predictions - the recall rate is merely 18%. Of all the employees who left, the model was only able to identify 18%. That would not be very helpful.

### Random forest with cross validation

Next we try a random forest model, and tune the hyperparameters with `GridSearch`. When building the random forest model, as well as the XGBoost model in the next step, we revert back to the dataset with outliers to have more information at hand. Tree-based models are not highly sensitive with outliers. 

As a result, recall improved significantly from 18% to 82%! Precision is also improved to 97.5%. We could stop here and use this model, but I still want to try the gradient boosting.

### Extreme gradient boosting (XGB)

While the random forest model trains base learners simultaneously, the XGB model does so sequentially. It turns out that the XGB model improved recall rate by another 9% to 91.4%, while maintaining a high precision score.

### Model selection

Here is a comparison of how the models perform on test data.

|               Model | Accuracy | Precision |   Recall |       F1 |
|--------------------:|---------:|----------:|---------:|---------:|
| Logistic regression | 0.833556 |  0.497207 | 0.178715 | 0.262925 |
|    Random forest CV | 0.967419 |  0.975391 | 0.824503 | 0.893593 |
|             XGBoost | 0.980874 |  0.968250 | 0.914935 | 0.940767 |

![image](https://github.com/user-attachments/assets/37f480cd-d848-4bbc-9411-26300cc1f744)

Our champion model is the tuned XGBoost model. While this tree-based model is not as easy to interpret as the logistic regression model, we can still find out which features may be most relevant in predicting employee turnover from this feature importance chart.

![image](https://github.com/user-attachments/assets/df64f922-f79a-474f-92bf-d7b5d4abbdd6)

## Conclusion

With an XGBoost model, we can identify employees who will likely leave the company with strong accuracy and confidence, so management and HR department can take action when the model identifies someone who will likely leave. There are a few areas the management team should pay special attention to:
* **Workload**: A significant number of employees are working more than 40 hours a week, and those who worked most have all left the company. Is the workload reasonable? Should we hire more workers? Is the goal or KPI well defined so that everyone is working efficiently? Is there adequate training or process improvement that can reduce everyone's hours?
* **Recognition**: It goes without saying that hardwork should be rewarded with recognition, financially and non-financially.
* **Satisfaction**: Confirming our findings in EDA, satisfaction level has a direct relationship to employee turnover. However, when an employee is already dissatisfied, it may be too late. It is also a very complex and comprehensive indicator, because it could be linked to a lot of different aspects in workplace, e.g. safety, recognition, financial return, etc. This may explain why work accidents and promotion do not rank high in feature importance - does not mean they are not important! HR department need to continue monitor employee satisfaction, and gain more insights as to why are employees satisfied or dissatisfied.
