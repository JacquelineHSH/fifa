# FIFA
Decoding FIFA players performance score using different regression models

## Goal 
To compare the performance of different regression models predicting FIFA players' overall performance score. 

## Some Lesson learnt from this project : 
### 1.	About significantly heterogenousity 

Ideally, we should randomly choose sample from the **significantly heterogenous dataset** to build a model, so that the sample we choose could represent the original dataset as much as possible, then the estimators we get will be more accurate to predict the test dataset. Alternatively, **stratified sampling** can be utilized if the heterogeneity is from a specific variable.We can stratify the dataset according to different levels of this variable, and choose sample proportionally from each level. 
In addition, we could use cross validation to enhance our model. Cross validation guarantees each observation is left-out for validation in at least one fold, and lowers the sampling variance of CV model selection. 

*We made some changes to the original model.*

Firstly, we treated 'International Reputation', 'Weak Foot', 'Skill Moves', 'Contract Valid Until' as continuous variable instead of categorical variable to put into the model. Because their numbers are corresponding to their levels. 

Secondly, we cleaned ‘Value’ and ‘Wage’ columns, and put them into our model because it is highly possible these variables are correlated to overall score. It’s reasonable to assume that value and wage are correlated with the overall score of the player. We don’t have to worry about **multicollinearity** too much because Lasso will take care of this.

Then we split the data into training and test set, and used training set to fit a simple linear regression model. There are 77 features in the model and the OOS R^2 is 0.894, which improved 0.004 compared to the original model.
			
### 2.	Cross-validation on simple linear regression

After implementing **5-fold cross validation** to the test dataset, the R^2 improved to 0.899. While the basic linear regression model used training data set to fit the model and then predicted test dataset, the cross validation repeated this process 5 times using test dataset and gave overall accuracy of the model. For FIFA dataset, the R^2 of cross validation improved slightly because it lowers the sampling variance, and it shows that the model's ability to predict new data is quite good, especially because we have a heterogeneous data set.

### 3.	Lasso Regression

After fitting the **Lasso regression model**, the R^2 dropped a little to 0.862 compared to 0.894 in basic linear regression. However, Lasso only used 26 features in the model instead of 77 features in the basic model. We can see that Lasso works well in this dataset that it reduced 51 features but the R^2 only dropped 0.032. It helped us to simplify the model and keep a high predicting accuracy at the samein the meantime.
	
### 4.	What if using ridge- or log- instead of lasso- penalties? 
                           
Ridge regression use 𝛽^2 in the penalty term  instead of |𝛽| as in lasso regression. As the alpha increases, Ridge regression will shrink the coefficients but not that aggressively to zero like lasso. So the **number of features** used by the model will greatly increase if we use ridge regression. 

As the Ridge regression will keep more variables, it will yield a higher R^2 almost certainly, because R^2 always increases as more variables are put into the model with the risk of overfitting. 

The log- regularization will result in even fewer features as compared to lasso as it is more aggressive in making betas to zero.
		
### 5.	Lasso regression with an ideal value for alpha

Ideal alpha is 0.01.  53 features are being used. 

R^2  dropped 0.0003, very slightly from  0.8946 to 0.8943. This means most variables dropped weren’t playing a big role in the linear regression model. And the lasso regression model is better because the complexity is reduced without compromising the model fit. This is inline with getting a parsimonious model. 

### 6.	To use AIC and BIC to evaluate

Linear regression model: AIC -26576, BIC-27177, Corrected AIC- 26576

Lasso regression model: AIC- 26567, BIC- 26983, Corrected AIC- 26567

The model with lower AIC is Lasso and is better. 

BIC is usually higher than AIC, but when sample size is less than 7 BIC is more tolerant and is small. But sample size less than 7  will be an uninformative dataset.

Corrected AIC is almost same as AIC because for higher sample size ( >40 times the number of parameters) both AIC and corrected AIC converge to the same value.

### 7.	ICs comparing to CVs. 

ICs and CV both have their pros and cons.

**AIC** is best for prediction as it is asymptotically equivalent to cross-validation.We prefer correct AIC when the number of observations is small (<40 times the number of parameters). **BIC** is best for explanation as it is allows consistent estimation of the underlying data generating process.

**AIC** always has a chance of choosing too big a model, regardless of n. **BIC** has very little chance of choosing too big a model if n is sufficient, but it has a larger chance than AIC, for any given n, of choosing too small a model.

**AIC** is better in situations when a false negative finding would be considered more misleading than a false positive, and BIC is better in situations where a false positive is as misleading as, or more misleading than, a false negative.

Cross validation is time consuming as compared to ICs but is the true test for out of sample performance.It is mainly used in settings where the goal is prediction, and one wants to estimate how accurately a predictive model will perform in practice.

Formula- 
AIC = -2*ln(likelihood) + 2*k,
BIC = -2*ln(likelihood) + ln(N)*k, 
AICc= AIC+ 2k(k+1)/(n-k-1)
where: k =number of parameters estimated by model, N = number of observations


