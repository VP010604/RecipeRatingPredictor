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

---

## Data Cleaning and Exploratory Data Analysis

As we mentioned above, the first step of cleaning our data was retrieving the relevant information and merging it into one data frame: the **Recipe Ratings** data frame. To do this we first started by **left joining** the Recipes and the Ratings dataframe. Since we conducted a left join, there were plenty of missing values, namely recipes that had a rating of 0 in the ratings data frame. 

As a next step, we changed all of the aforementioned 0 values to NaN, since our rating scale goes from one to five and the presence of 0’s would skew our analysis since any aggregation methods would interpret a rating of 0 as an actual rating rather than a missing rating leading to bias in our analysis. On the flip side, NaN values are handled as missing, not actual data points. After we took care of getting rid of those 0’s we found the average rating for each recipe and created the **Recipe Ratings** data frame.

Following the creation of the Recipe ratings data frame, there were a few main data cleaning points that we had to take care of. The first of which was winsorizing some of the major columns that had unrealistic outliers. Winsorizing is the process of adjusting the most extreme outliers in our data (the data points in the 99th percentile) to the nearest percentile thresholds, thus reducing the impact of outliers and allowing us to preserve the distribution of the bulk of the data while staying robust to outliers. In our dataset, the minutes column had some unrealistic outliers that just didn't make sense in the context of the data, so we winsorized that column. An example of this was some dishes that would take upwards of 1M+ minutes - which does not make sense in the context of our research given the improbability of someone cooking a dish that would take ~2 years. Other such unrealistic examples were winsorized to the 99th percentile.

Another major data transformation that we completed was creating a new column that categorized the ratings column by rounding ratings either up or down. While we will still continue to use the averaged rating column (entitled "average_ratings" in the dataframe snippet shown below) for some of our analysis, the categorized column allows us to preserve the initial categorical nature of a 1-5 rating from the **Ratings** data frame. Furthermore, this will become necessary later down the line when conducting analysis, as while the averages of the ratings are a continuous data type, the distribution when plotted indicates that the data is in fact categorical in nature.

This concludes the majority of the data cleaning needed in our data set. A snippet of the cleaned dataframe is provided below:



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



From the graph, we see that as the number of ingredients increases, in general, the number of steps does too. This is evident by the vertical shifting of the box plots as we move right on the graph. It also makes sense, logically, as we increase the number of ingredients, it will take more steps to incorporate all of them. 

One interesting aggregation we utilized was to help understand if there are any relationships present between ingredients, minutes, and ratings. In the table, the data is grouped by ingredients, and the minutes are aggregated by both mean and median. In addition, the average rating column is aggregated by mean for each ingredient group.

From the table, we see in the data that recipes that had only one ingredient, took the shortest amount of time to prepare and (perhaps) consequently had the highest ratings. However, for the other groups, namely n_ingredients greater than 1, the minutes seem to be highly variable, and the ratings tend to decrease, although very slightly.


<iframe
  src="assets/plot1.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>
