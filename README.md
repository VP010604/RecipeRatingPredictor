# Recipe Ratings Predictor

by Raymond Williams, Varun Pabreja (r7williams@ucsd.edu, vpabreja@ucsd.edu)

---

## Introduction

One of the most important things in any given person’s life is the food they eat. Every day we try to make new meals and keep things fresh, for otherwise food tastes bland and the flavors get boring. The most common way people get new meal ideas to cook at home is through online recipes. As such this project looks to provide a data scientist’s point of view into what makes a given recipe highly rated, and popular.

The data from this project is provided by food.com, a digital brand, and online social networking service featuring recipes from home cooks and celebrity chefs. To start with, there were two datasets, one that detailed the recipes (we’ll call this **recipes**) and another that detailed the ratings (we’ll call this **ratings**). 

**Recipes** had one column for each of the following - the name of a recipe, the recipe’s unique recipe ID, the minutes it took to prepare the recipe, the user ID of the creator of the recipe, the date the recipe was submitted, the food.com tags for the recipe, the recipe’s nutrition information (included in the form of a list),the number of steps the recipe took to make, the recipe steps in order, and one for a user-provided description. 

**Ratings** had one column for each of the following - the user ID, the recipe ID, the date of interaction with the recipe, the rating given to the recipe, and the text included within the review. 

The recipes data frame contained 83782 rows or recipes, and the ratings data frame contained 731927 rows or ratings.

For the sake of our analysis, we **merged** the two data frames into one containing all the information found within the recipes as well as the average rating for that recipe. This final data frame had 83782 rows, with 13 columns - all the ones from **recipes**, and the average rating for each recipe from **ratings** (the description of this process is provided in the section entitled, "Data Cleaning and Exploratory Data Analysis"). From this point onwards we will refer to this final data frame as **Recipe Ratings**.

The broad question we decided to focus on was - "What influences the rating of a recipe?". 
The first 4 parts of the project are dedicated towards the following question - "What is the relationship between the cooking time and average rating of recipes?"
The final 4 parts of the project are dedicated towards the following question - "Based on the data available, is it possible to accurately predict the rating of a recipe?"

Why should you care about this project? In today's day and age, individuals learn everything online - including how to cook food. This investigation deep-dives into what factors make a recipe great. It helps us understand why we rate recipes low or high, and what should **you** look for when picking a recipe for a meal.

The descriptions of the dataset has been provided above. As of now, all the columns are relevant to our analysis. Before each step begins, we shall **highlight** the relevant columns that we use for our analysis in that step.

This marks the end of Section 1 - Introduction

---

## Data Cleaning and Exploratory Data Analysis

As we mentioned above, the first step of cleaning our data was retrieving the relevant information and merging it into one data frame: the **Recipe Ratings** data frame. To do this we first started by **left joining** the Recipes and the Ratings dataframe. Since we conducted a left join, there were plenty of missing values, namely recipes that had a rating of 0 in the ratings data frame. 

As a next step, we changed all of the aforementioned 0 values to NaN, since our rating scale goes from one to five and the presence of 0’s would skew our analysis since any aggregation methods would interpret a rating of 0 as an actual rating rather than a missing rating leading to bias in our analysis. On the flip side, NaN values are handled as missing, not actual data points. After we took care of getting rid of those 0’s we found the average rating for each recipe and created the **Recipe Ratings** data frame.

Following the creation of the Recipe ratings data frame, there were a few main data cleaning points that we had to take care of. The first of which was winsorizing some of the major columns that had unrealistic outliers. Winsorizing is the process of adjusting the most extreme outliers in our data (the data points in the 99th percentile) to the nearest percentile thresholds, thus reducing the impact of outliers and allowing us to preserve the distribution of the bulk of the data while staying robust to outliers. In our dataset, the minutes column had some unrealistic outliers that just didn't make sense in the context of the data, so we winsorized that column. An example of this was some dishes that would take upwards of 1M+ minutes - which does not make sense in the context of our research given the improbability of someone cooking a dish that would take ~2 years. Other such unrealistic examples were winsorized to the 99th percentile.

Another major data transformation that we completed was creating a new column that categorized the ratings column by rounding ratings either up or down. While we will still continue to use the averaged rating column (entitled "average_ratings" in the dataframe snippet shown below) for some of our analysis, the categorized column allows us to preserve the initial categorical nature of a 1-5 rating from the **Ratings** data frame. Furthermore, this will become necessary later down the line when conducting analysis, as while the averages of the ratings are a continuous data type, the distribution when plotted indicates that the data is in fact categorical in nature.

This concludes the majority of the data cleaning needed in our data set. A snippet of the cleaned dataframe is provided below:

|&nbsp;minutes&nbsp;|&nbsp;n_steps&nbsp;|&nbsp;n_ingredients&nbsp;|&nbsp;average_rating&nbsp;|&nbsp;minutes_winsorized&nbsp;|&nbsp;average_rating_aggregated&nbsp;|&nbsp;calories&nbsp;|&nbsp;calories_winsorized&nbsp;|
|----------:|----------:|----------------:|-----------------:|---------------------:|----------------------------:|-----------:|----------------------:|
|        40 |        10 |               9 |                4 |                   40 |                           4 |      138.4 |                 138.4 |
|        45 |        12 |              11 |                5 |                   45 |                           5 |      595.1 |                 595.1 |
|        40 |         6 |               9 |                5 |                   40 |                           5 |      194.8 |                 194.8 |
|       120 |         7 |               7 |                5 |                  120 |                           5 |      878.3 |                 878.3 |
|        90 |        17 |              13 |                5 |                   90 |                           5 |      267   |                 267   |

Note that some of the columns were irrelevant - this included the 'name', 'id', 'contributor_id', 'submitted', 'tags', 'nutrition', 'steps', 'description', 'ingredients' columns. These columns have been removed from the above display, so as to ensure only the relevant information has been displayed.
Further note that the 'calories' and 'calories_winsorized' columns have been obtained from the 'nutrition' column from **Recipe Ratings** and appropriately winsorizing this column respectively.

We will now move on to the univariate, bivariate, and display some interesting aggregations on our dataframe.

For our univariate analysis, we will examine perhaps the most important column in the dataset, the **average rating** column. For the sake of this analysis, we have used the actual average rating column, not the categorized one. This allows us to see the range of the distrbution, including the intermediate rating values.

<iframe
  src="assets/plot2.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

From the graph, we see that the distribution is **heavily negatively skewed** with the vast majority of our values lying within the 5-star range and minimal values in the lower bins. From this, we can conclude that most ratings are five-star ratings.

For our bivariate analysis, we look at the distributions of steps as the number of ingredients increases. We do this by using box plots so we can visualize it all at once. 

<iframe
  src="assets/plot4.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

From the graph, we see that as the number of ingredients increases, in general, the number of steps does too. This is evident by the vertical shifting of the box plots as we move right on the graph. It also makes sense, logically, as we increase the number of ingredients, it will take more steps to incorporate all of them. 

One interesting aggregation we utilized was to help understand if there are any relationships present between ingredients, minutes, and ratings. In the table, the data is grouped by ingredients, and the minutes are aggregated by both mean and median. In addition, the average rating column is aggregated by mean for each ingredient group.

The whole table had 34 rows and 4 columns. For simplicity's sake, the first 7 and the final 7 have been shown.

The first 7:

|&nbsp;Index&nbsp;|&nbsp;n_ingredients&nbsp;|&nbsp;Minutes_Winsorized_Mean&nbsp;|&nbsp;Minutes_Winsorized_Median&nbsp;|&nbsp;Average_Rating_Mean&nbsp;|
|-------|---------------|-------------------------|---------------------------|---------------------|
| 0     | 1             | 39.15                   | 12.0                      | 4.86                |
| 1     | 2             | 57.13                   | 10.0                      | 4.69                |
| 2     | 3             | 47.82                   | 10.0                      | 4.66                |
| 3     | 4             | 41.94                   | 15.0                      | 4.63                |
| 4     | 5             | 49.52                   | 20.0                      | 4.65                |
| 5     | 6             | 53.36                   | 25.0                      | 4.63                |
| 6     | 7             | 57.72                   | 30.0                      | 4.62                |


The final 7:

|&nbsp;Index&nbsp;|&nbsp;n_ingredients&nbsp;|&nbsp;Minutes_Winsorized_Mean&nbsp;|&nbsp;Minutes_Winsorized_Median&nbsp;|&nbsp;Average_Rating_Mean&nbsp;|
|-------|---------------|-------------------------|---------------------------|---------------------|
| 27    | 28            | 102.94                  | 70.0                      | 4.86                |
| 28    | 29            | 104.60                  | 57.5                      | 4.97                |
| 29    | 30            | 122.64                  | 80.0                      | 4.87                |
| 30    | 31            | 165.00                  | 87.5                      | 5.00                |
| 31    | 32            | 55.00                   | 55.0                      | 5.00                |
| 32    | 33            | 35.00                   | 35.0                      | 5.00                |
| 33    | 37            | 240.00                  | 240.0                     | 5.00                |


From the table, we see in the data that recipes that had only one ingredient, took the shortest amount of time to prepare and (perhaps) consequently had the highest ratings. However, for the other groups, namely n_ingredients greater than 1, the minutes seem to be highly variable, and the ratings tend to decrease, although very slightly.

This marks the end of Section 2 - Data Cleaning and EDA.

---

## Assessment of Missingness

**NMAR Analysis**: While the data does have missing values namely in the name, description, and average_rating columns, we determined none of these values to be NMAR. Of those columns, only one had a significant number of missing values: the average rating column. NMAR by definition means that the value of the ratings themselves impacts their missingness. There is no value of which the average rating could be that would cause users to systematically not put down a rating. For example, if someone tried a recipe and thought it were extremely bad, we argue that they would be more likely to rate it low on the website than not rate it at all. Similarly, if someone tried a recipe and thought it was extremely good, once again, we argue that they would be more likely to go on the website and rate it than not rate it at all. It doesn’t make sense logically here. As a result, we believe if there are any missingness relationships to be had here, we believe they would have to either be MAR or MCAR.

**MAR vs MCAR Analysis (nsteps vs average rating)**: Since the average rating column is the only column in our data set within which the missingness actually holds relevance, we will conduct our missingness analysis on this column. Specifically, we will conduct our missingness analysis with respect to this column and the n-steps column.

Our null hypothesis: The distribution of n-steps is the same regardless of whether ratings is missing or not, with any differences being due to chance.

Our alternate hypothesis: The distribution of n-steps when ratings is missing differs from the distribution of n-steps when rating is present.

<iframe
  src="assets/plot5.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Test Statistic: We started off utilizing the absolute difference in means to analyze the difference between the two distributions, but then moved on to the KS statistic since we noticed upon graphing that the distributions were relatively similar in shape.

Result: In the end, we achieved a **p-value of 0.0 (3.18 x 10^-13)** meaning that the difference between the two distributions is highly unlikely to be due to chance. Therefore, we **reject the null hypothesis**. From this, we can determine that the missingness of the average ratings column is likely dependent on the n_steps column meaning that the missingness relationship present here is MAR.

MAR vs MCAR (n_ingredients vs average rating): Much like the missingness analysis test above we will now conduct another permutation test, this time between the number of ingredients and whether the ratings column is missing or not.

Null Hypothesis: The distribution of n-ingredients is the same regardless of whether ratings is missing or not, with any differences being due to chance.

Our alternate hypothesis: The distribution of n-ingredients when ratings is missing differs from the distribution of n-steps when rating is present. 

<iframe
  src="assets/plot6.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Test Statistic: Much like the other test, we started off utilizing the absolute difference in means to analyze the difference between the two distributions, but then moved on to the KS statistic since we noticed upon graphing that the distributions were relatively similar in shape. So while both tests yielded the same results in the end we felt it appropriate to use the KS statistic.

Result: In the end, we achieved a **p-value of 0.11 (0.11232147746623422)** meaning that we **failed to reject the null hypothesis** and that the difference in the two distributions can be attributed to chance. Since statistically the distributions are similar, we can say that the data doesn’t really change regardless of whether minutes are missing or not as a result of the missingness relationship between n_ingredients and average_ratings being MCAR.

This marks the end of Section 3 - Assessment of Missingness.

---

## Hypothesis Testing

Now that we have finished our missingness analysis we can move on and work on our hypothesis test and answer some statistical questions. The big idea of this project is to analyze the effects of specific features on the average rating of any given recipe. For the purposes of this hypothesis test, we will be looking at **cooking time**.

More specifically, the question we will be asking is, as stated before **“What is the relationship between the cooking time and the average rating of recipes?”**

Null Hypothesis: There is no difference in the mean ratings between recipes with low cooking times and recipes with high cooking times. With any difference being due to chance. 

Alternate Hypothesis: Recipes with low cooking times have higher ratings than recipes with high cooking times. 

You may have noticed that there is no cooking time column in our Recipe Ratings data frame. So to create this column we must categorize the minutes column. We will do this using quantiles. Specifically, we will split the data frame into 3 quantiles and compare the distributions of ratings in the upper quantile to the bottom quantile. 

Test Statistic: Since the direction of the data is important here as we are trying to prove one group has higher ratings than the other, we need to use a test statistic that takes into account direction. Since our data is numerical there is an easy option here. Therefore we decided to use a simple difference in means specifically (high - low).

Prediction: We predict that people will likely rate the lower cooking time recipes higher since they are easier to prepare and therefore higher rated than their high cooking time counterparts.

Observed Stat: We found that the current observed statistic between high and low cooking time data was around -0.04015415725019178 meaning that the low data was rated higher by around 0.04 in our data set.

To test our hypothesis against the null we ran a permutation test 10000 times and created a distribution of the differences in high and low cooking time columns. We then compared our observed statistic to the distribution to see what proportion of times the null difference ended up larger than or equal to our observed one. 

Result: Our p-value ended up being **0.0** meaning that it is highly unlikely that our observed difference is due to chance. In such a case we can **reject the null hypothesis**. 

Interpretation: The observed difference in our data can likely be attributed to the fact that as we mentioned earlier people simply don’t have the patience for long recipes. We are all busy and at the end of the day, we all want some quick, tasty, and nutritious food to feed us. As a result, the low cooking time recipes are rated higher, and this is likely not due to chance. This is likely the reason for the discrepancy.

This marks the end of Section 4 - Hypothesis Testing.

---

## Framing a Prediction Problem

Prediction Problem: Predict ratings of recipes.

Initially, we figured this would be a cut-and-dry regression problem. The data in the average rating column was continuous in nature, and as a result, it made sense to try and predict the rating on a linear scale. However, after trying the model out and plotting the data, it became apparent that this was a classification problem disguised as a regression problem. It so happens that the data is largely clustered around the 1 through 5 whole number values. As a result, it became apparent to us that there was no simple transform to make this model work efficiently using regression and that we would have to resort to a form of classification algorithm after grouping the data back to its whole number form. 

Now that we established that we will be utilizing a classifier, it is important to understand what exactly it is that we are classifying. In this case, we will be classifying a given recipe to its rating (1,2,3,4,5). We choose this because ultimately it is the goal of this project to understand how predictable the rating of a recipe is based on the factors we are given in our dataset. Is the rating of a recipe truly something we can predict with any form of accuracy or is it something that is arbitrary? This is a question we seek to determine the answer to through training our model.

What will be using to train our model? : Within the data set there are only a certain amount of columns that we would know for sure before a person delivers their rating for any given recipe. For example, any information directly regarding the recipe and not the rating we would know before making our prediction and as a result, is fair game for our model. For the sake of our baseline model, we will try and use as many features as we can. Then down the line for our final model, if we deem certain features to not be of as much impact to our model, we will remove them. Therefore, we are starting off by using these columns as features: ['minutes_winsorized', 'n_steps', 'n_ingredients', 'average_rating_aggregated', 'calories_category']

How will we evaluate the classifier? : To evaluate our classifier we chose to use **accuracy** as our test statistic. Generally, it is a good idea to use accuracy when there is no increase in real-world danger between a false positive and a false negative. Since accuracy doesn’t differentiate between the types of misclassifications and our prediction problem doesn’t require a metric that has that function, we have gone ahead and chosen accuracy. Some possible limitations here could be that our data does have uneven groups with most of our ratings being five-star, therefore accuracy can sometimes give misleading results as to the performance of a model. Furthermore, we account for this by taking into account the model’s performance should we just predict the most common group every time and comparing our accuracy to that number.

This marks the end of Section 5 - Framing a Prediction Problem.

---

## Baseline Model

Our model is a **Decision Tree** that aims to determine the rating of a meal depending on a number of factors, the columns of which we have pulled out into a new dataframe.

For the baseline model, we thought it would be appropriate to include the following columns:

  - **Quantitative** - 'minutes_winsorized', 'n_steps', and 'n_ingredients'
  - **Ordinal** - 'calories_category'. The recipes were first divided into **0.33** percentiles, respectively turning the lowest percentile into 'Low', the middle percentile into 'Moderate', and the highest percentile into 'High'. Then, their order was preserved (as is mandated through any ordinal encoding - that the "distance" between ratings must be the same). This encoding was done through the OrdinalEncoder object as part of the pipeline of our model.
  - **Nominal** - NONE. None of the nominal columns seem to be of use for the Decision Tree - The difference between the 'name', 'id', 'contributor_id' etc. of the meals would intuitively have no impact on both the model and one's decision to cook a meal.

Note that we transformed 2 columns before encoding the baseline model - 'minutes' (into 'minutes_winsorized') and 'calories' (into 'calories_category')

The performance of our model was ~68%. We believe this is unimpressive. We do not think our baseline model performed well, since the distribution of ratings was as follows:

5.0 - 60945
4.0 - 16103
3.0 - 2875
2.0 - 660
1.0 - 590

As can be seen above, ~75% of the values are 5. This means, ~75% of the meals had ratings of 5, which indicates that our current model performs ~7% worse than if it had just predicted a rating of 5 for every meal. Therefore, we believe the baseline model is unimpressive.

This marks the end of Section 6 - Baseline Model.

---

## Final Model

In this section, we performed 2 more transformations. We first **standardized** 'n_steps', since we believed this might have had some negative effect on the performance of the algorithm. The reason we believe so is because the 'n_steps' column had a large variance albeit the minimum and maximum of the column (the minimium being 1 and the maximum being 100). Therefore, we thought standardizing the column would help the model better assess the data. Furthermore, we **binarized** the 'n_ingredients' column, the threshold being 9. The reason we did so was because we noticed the median of the n_ingredients column was 9, indicating that 50% of the dishes were below 9, and 50% above 9, indicating that it would be a fair split in the data, and would help the decision tree classify the data better at scale. We therefore chose to divide the 'n_ingredients' column by binarizing with the threshold being 9.

The modeling algorithm we chose was a Decision Tree. Here, we argue that if one has the resources, one should run a Random Forest instead of a decision tree, since this would almost certainly improve model performance. To finetune the model, we ran GridSearchCV on our model with 4 parameters, their individual choices being displayed rihgt next to the hyperparameter name:

- **Max_Depth** - 1,4,7,10,13,16,19,22,25,28,31
- **Min_Samples_split** - 2,5,10,20,50,100
- **Criterion** - gini, entropy
- **Min_Samples_Leaf** - 1,2,4,8,16,32

Running GridSearchCV with the following model hyperparameters led to our final choice of hyperparemeters being:

- **Max_Depth** - 10
- **Min_Samples_split** - 100
- **Criterion** - entropy
- **Min_Samples_Leaf** - 16

With these selections in mind, we ran the model once again, and were surprised with the results! Our model scored ~75%, roughly a 7% improvement over our baseline model. We believe standardizing the 'n_steps' column, binarizing the 'n_categories' column, and finetuning the hyperparameters worked in tandem with each other to improve our model. Although this is not _better_ than the worst case, it is at least an improvement on the baseline!

This marks the end of Section 7 - Final Model.

---

## Fairness Analysis

This marks the end of Section 8 - Fairness Analysis.

---
