# Report: Predict Bike Sharing Demand with AutoGluon Solution
#### Svetlana Bogdanova

## Initial Training
### What did you realize when you tried to submit your predictions? What changes were needed to the output of the predictor to submit your results?
I had to  drop columns=['casual', 'registered'] from the input set as they were not present in test set
Predictions shoulld not  have values  <0 , so I set all negative values  to 0. There were 2 at that time


### What was the top ranked model that performed?
WeightedEnsemble_L3  -55.123669

## Exploratory data analysis and feature creation
### What did the exploratory analysis find and how did you add additional features?
There was a correlation between bike counts and weather parameters, day of the week, holiday / workday. Being a competitive bike rider, I felt that the most correlations I need to consider were 
1. hour of the day.
2. day of the week
3. weather - correlates with atemp (feels like) that I used to engineer new features.
4. season - making it as Category feature

   Changing weather / season to a categorical type helps the model recognize patterns more accurately and therefore performance is improved.   It is very important for datetime features to use  hour or the day (HOD) or day of the week (DOW) as features as bike rentals strongly correlate with them . They are non-linear by nature 


### How much better did your model preform after adding additional features and why do you think that is?
That improved the model significantly from 1.84 Kaggle score with features intact to 0.75 with HOD / DOW / season (feature set 2) engineered to 0.68 with adding engineered weather (feature set 1). All hyperparameter tuning was done with feature set 1.

## Hyper parameter tuning
### How much better did your model preform after trying different hyper parameters?
Tuning hyperparameters improved the model, however the effect was not as pronounced, AutoGluon already optimizes the models by default. The most obvious improvements were to remove low performing models from optimization set. Adding more time to train did not bring much improvements. HPO improvements in my experiments resulted in Kaggle score of 0.48


### If you were given more time with this dataset, where do you think you would spend more time?
1. I will work on the new features that inlude logging historic data into the model and taking into account rental moving averages
2. I will look into heatmaps to see what features are the most important and focus on engineering them
3. I will add holiday / workday categories
4. I will experiment with weather more and use humidity to indicate if it was raining
5. I will explore individual models and try to tune them and use later top 3 performers as ensamble model
6. I will spend more time on data analysys trying to understand if there is overfit 
7. Once I get better feaures and models configurations, I will try to extend training times.
   

### Create a table with the models you ran, the hyperparameters modified, and the kaggle score.
    
 	experiments models_used_in_AutoGluon    new/engineered features kwargs  \
	0      original       AutoGluon standard                       none     no   
	1  new_feature2       AutoGluon standard           season, HOD, DOW     no   
	2  new_feature1       AutoGluon standard  weather, season, HOD, DOW     no   
	3          hpo1              exclude KNN  weather, season, HOD, DOW     no   
	4          hpo5     GBM,CAT,XGB,NN_TORCH  weather, season, HOD, DOW     no   
	5          hpo2             GBM, RF, CAT  weather, season, HOD, DOW     no   
	6          hpo4                 GBM, CAT  weather, season, HOD, DOW     no   
	7          hpo6             GBM, RF, CAT  weather, season, HOD, DOW    yes   
	8      hpo6-log             GBM, RF, CAT  weather, season, HOD, DOW    yes   

 	score  
	0  1.84061  
	1  0.75401  
	2  0.68507  
	3  0.58200  
	4  0.61440  
	5  0.56763  
	6  0.51728  
	7  0.48480  
	8  0.48414  

	hyperparameter_tune_kwargs = {	
		'num_trials': 20, # Try 20 different configurations	
		'scheduler': 'local', # Run on local machine	
		'searcher': 'random', # Random search for hyperparameters	
		'max_t': 600 # Max time for each trial is 600 seconds	
		}	
    
There were slight variations in hyperparameters per model (values used are seen in the source code), no dramatic change. 
All experiments  ran 600 seconds,  hpo6-long ran 2100 seconds

	HOD = Hour of the day
 	DOW = Day of the week

### Create a line plot showing the top model score for the three (or more) training runs during the project.


![model_train_score.png](img/model_train_score.png)

### Create a line plot showing the top kaggle score for the three (or more) prediction submissions during the project.


![model_test_score.png](img/model_test_score.png)

## Summary
Through feature engineering and model tuning, I was able to significantly improve prediction accuracy. Future work could focus on  engineering more features based on the feaure set, adding additional data. The further model tuning will offer improvemnt in performace as well. 
