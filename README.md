# Recipe Ratings Predictor

by Raymond Williams, Varun Pabreja (r7williams@ucsd.edu, vpabreja@ucsd.edu)

---

## Introduction

One of the most important things in any given person’s life is the food they eat. Every day we try to make new meals and keep things fresh, for otherwise food tastes bland and the flavors get boring. The most common way people get new meal ideas to cook at home is through online recipes. As such this project looks to provide a data scientist’s point of view into what makes a given recipe highly rated, and popular.

The data from this project is provided by food.com, a digital brand, and online social networking service featuring recipes from home cooks and celebrity chefs. To start with, there were two datasets, one that detailed the recipes (we’ll call this **recipes**) and another that detailed the ratings (we’ll call this **ratings**). 

**Recipes** had one column for each of the following - the name of a recipe, the recipe’s unique recipe ID, the minutes it took to prepare the recipe, the user ID of the creator of the recipe, the date the recipe was submitted, the food.com tags for the recipe, the recipe’s nutrition information (included in the form of a list),the number of steps the recipe took to make, the recipe steps in order, and one for a user-provided description. 

**Ratings** had one column for each of the following - the user ID, the recipe ID, the date of interaction with the recipe, the rating given to the recipe, and the text included within the review. 

The recipes data frame contained 83782 rows or recipes, and the ratings data frame contained 731927 rows or ratings. 

---

## Data Cleaning and Exploratory Data Analysis

For the sake of our analysis, we **merged** the two data frames into one containing all the information found within the recipes as well as the average rating for that recipe. This final data frame had 83782 rows. From this point onwards we will refer to this final data frame as **Recipe Ratings**.

