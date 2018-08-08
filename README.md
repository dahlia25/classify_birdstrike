# Project: Avians Strike Avians

## Problem:

Classify birds striking aircraft based on striked/damaged aircraft parts.

## Data:

Data was obtained from Kaggle, which originated from the FAA (Federal Aviation Administration). 

The dataset consists of about 66 columns and over 170K observations. The dataset contains records of birds striking military, commercial and civil aircraft between 1990 and 2015.

You can find the dataset [here](https://www.kaggle.com/faa/wildlife-strikes/home).

## Project Design

**Data Cleaning:**

The dataset did not only consist of bird species, but also other types of wildlife. To subset all bird observations, I used Selenium and BeautifulSoup to search Google, and used Google as a wildlife dictionary to find all my bird species. 

**Data Engineering:**

After subsetting only the birds data, I still had over 160K observations. I had over 500 bird species, so I categorized all species into 3 size categories: 

* Small (<9 in. long)
* Medium (9-16 in. long) 
* Large (>16 in. long) 

I used the website, http://www.whatbird.com/, to categorize the bird species.

**Hypothesis:**
I performed *groupby* on the dataset to do some data exploration, and found the pattern that larger birds did less strikes but more damage, and vice versa for smaller birds. Hence, I was convinced that I could just use striked and damaged plane parts to classify bird size.

### Models & Algorithms used:

To find a base model, I tried the following models:

| Models | Train Score |
|--------|-------------|
| Logistic Regression | 52.7 % |
| Naive Bayes Gaussian | 41.6 % |
| SGD Classifier | 50.4 % |
|RandomForest | Classifier 87.5 % |
| XGBoost | 45.7% |
| SVM | Time cost too high |
| KNeighbors | Time cost too high |

As shown in the table above, the *RandomForest Classifier* was my best base model.

At first, I only used the striked/damaged plane parts as features. These features are binary labeled, so 0=the specified plane part did not get striked/damaged, and 1=it did. With just these features, the train score was only about 40%. Then I added the following features: Incident Month, Aircraft Mass, Visibility, and Plane Phase. And it boosted the train score to about 87%. However, the cross-validation score was only 62%, and the test score was 50%. So at this point, my model was just guessing rather than predicting and classifying. This showed me that my model was overfitting to my training data.

### Attempts to reduce overfitting

**1. Reduce number of features:** I first fed about 30 features into the RandomForest model. Then I picked the top 10 features (using the most important features of RandomForest) and fed it to my model. But the accuracy decreased by about 10%. 

**2. Control class weights:** It seemed that my model often misclassified small and medium-size birds. The recall rate for small birds was higher than that of medium-size birds, so I added more weight (tried weights from a range of 1.1 - 10) to the medium class. But this also did not improve my model.

**3. Add regularization parameters:** I added and controlled parameters such as max_depth and impurity, but also decreased accuracy. 

**4. Tuning parameters:** I used GridSearchCV hoping it would perform its magic to help my model. But it also failed and decreased the accuracy of my model. 

After all the failed attempts, this made me realize that I was assuming too much with the classifier. I assumed that the classifier could capture the extent/degree of strikes and damages made by each species. And this is where I noticed the limitation of binary labeled data. Binary labeled data does not capture this degree of the data. It captures 1/0 (presence/absence) of a feature, literally.

## Improvements & Future Ideas:

If I had more time, I would engineer my features (damaged/striked plane parts) by calculating the strikes per damage ratio for each species, and then see if my model predictions are more accurate. 

An interesting problem that I also wanted to look at was whether certain pairs of airlines and birds are prominent. But this problem would be for the future.

