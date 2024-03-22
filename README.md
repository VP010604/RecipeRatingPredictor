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

|   minutes |   n_steps |   n_ingredients |   average_rating |   minutes_winsorized |   average_rating_aggregated |   calories |   calories_winsorized |
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

| Index | n_ingredients | Minutes_Winsorized_Mean | Minutes_Winsorized_Median | Average_Rating_Mean |
|-------|---------------|-------------------------|---------------------------|---------------------|
| 0     | 1             | 39.15                   | 12.0                      | 4.86                |
| 1     | 2             | 57.13                   | 10.0                      | 4.69                |
| 2     | 3             | 47.82                   | 10.0                      | 4.66                |
| 3     | 4             | 41.94                   | 15.0                      | 4.63                |
| 4     | 5             | 49.52                   | 20.0                      | 4.65                |
| 5     | 6             | 53.36                   | 25.0                      | 4.63                |
| 6     | 7             | 57.72                   | 30.0                      | 4.62                |

The final 7:

| Index | n_ingredients | Minutes_Winsorized_Mean | Minutes_Winsorized_Median | Average_Rating_Mean |
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

## Data Cleaning and Exploratory Data Analysis

**NMAR Analysis**: While the data does have missing values namely in the name, description, and average_rating columns, we determined none of these values to be NMAR. Of those columns, only one had a significant number of missing values: the average rating column. NMAR by definition means that the value of the ratings themselves impacts their missingness. There is no value of which the average rating could be that would cause users to systematically not put down a rating. For example, if someone tried a recipe and thought it were extremely bad, we argue that they would be more likely to rate it low on the website than not rate it at all. Similarly, if someone tried a recipe and thought it was extremely good, once again, we argue that they would be more likely to go on the website and rate it than not rate it at all. It doesn’t make sense logically here. As a result, we believe if there are any missingness relationships to be had here, we believe they would have to either be MAR or MCAR.

**MAR vs MCAR Analysis (nsteps vs average rating)**: Since the average rating column is the only column in our data set within which the missingness actually holds relevance, we will conduct our missingness analysis on this column. Specifically, we will conduct our missingness analysis with respect to this column and the n-steps column.

Our null hypothesis: The distribution of n-steps is the same regardless of whether ratings is missing or not, with any differences being due to chance.

Our alternate hypothesis: The distribution of n-steps when ratings is missing differs from the distribution of n-steps when rating is present. 

Test Statistic: We started off utilizing the absolute difference in means to analyze the difference between the two distributions, but then moved on to the KS statistic since we noticed upon graphing that the distributions were relatively similar in shape. So while both tests yielded the same results in the end we felt it appropriate to use the KS statistic.

Result: In the end, we achieved a **p-value of 0.0 (3.18 x 10^-13)** meaning that the difference between the two distributions is highly unlikely to be due to chance. Therefore, we **reject the null hypothesis**. From this, we can determine that the missingness of the average ratings column is likely dependent on the n_steps column meaning that the missingness relationship present here is MAR.

MAR vs MCAR (n_ingredients vs average rating): Much like the missingness analysis test above we will now conduct another permutation test, this time between the number of ingredients and whether the ratings column is missing or not.

Null Hypothesis: The distribution of n-ingredients is the same regardless of whether ratings is missing or not, with any differences being due to chance.

Our alternate hypothesis: The distribution of n-ingredients when ratings is missing differs from the distribution of n-steps when rating is present. 

Test Statistic: Much like the other test, we started off utilizing the absolute difference in means to analyze the difference between the two distributions, but then moved on to the KS statistic since we noticed upon graphing that the distributions were relatively similar in shape. So while both tests yielded the same results in the end we felt it appropriate to use the KS statistic.

Result: In the end, we achieved a **p-value of 0.11 (0.11232147746623422)** meaning that we **failed to reject the null hypothesis** and that the difference in the two distributions can be attributed to chance. Since statistically the distributions are similar, we can say that the data doesn’t really change regardless of whether minutes are missing or not as a result of the missingness relationship between n_ingredients and average_ratings being MCAR.

