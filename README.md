# Analysis on the Relationship Between Amount of Carbohydrates on Recipe Rating
An analysis of the relationship between amount of Carbohydrates and recipe ratings for our DSC 80 project at UCSD. By Karina Shah and Audrey Meredith.

## Overview

Our data science project, which we carried out at UCSD focuses on exploring the relationship between the rating of a recipe and the proportion of carbohydrates that contributed to the total calorie amount in any specific recipe. 

## Introduction

Food is an essential part of our daily lives, and cooking is a hobby that brings joy and satisfaction to many. Carbohydrates play a significant role in our diets, serving as a primary energy source. However, excessive carbohydrate consumption, particularly refined carbs and added sugars, has been associated with health concerns such as obesity, diabetes, and heart disease. According to the Centers for Disease Control and Prevention, high carbohydrate intake, especially from processed foods, contributes to rising rates of metabolic disorders. With this in mind, we aim to explore the relationship between recipe ratings and carbohydrate content. **We wonder if people tend to rate recipes with higher carbohydrate content lower due to health concerns or dietary preferences.** To investigate this, we are analyzing a dataset containing recipes and ratings from [food.com](https://www.food.com/). This dataset was originally scraped and used by the authors of the recommender systems paper, [Generating Personalized Recipes from Historical User Preferences](https://cseweb.ucsd.edu/~jmcauley/pdfs/emnlp19c.pdf) by Majumder et al. Specifically, we are working with a subset of the original dataset that includes only recipes and reviews posted since 2008. By analyzing these datasets, we aim to determine whether carbohydrate content influences how people rate recipes and whether health concerns or dietary trends, such as low-carb or keto diets, play a role in these ratings.

The first dataset, `RAW_recipes.csv`, has **83,782** rows, representing unique recipes, with the following 12 columns:

| Column             | Description |
|--------------------|-------------|
| **`name`**         | Recipe name |
| **`id`**           | Recipe ID |
| **`minutes`**      | Minutes to prepare the recipe |
| **`contributor_id`** | User ID who submitted the recipe |
| **`submitted`**    | Date the recipe was submitted |
| **`tags`**         | Food.com tags for the recipe |
| **`nutrition`**    | Nutrition info as `[calories (#), total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), carbohydrates (PDV)]` (PDV = percentage of daily value) |
| **`n_steps`**      | Number of steps in the recipe |
| **`steps`**        | Text for recipe steps, in order |
| **`description`**  | User-provided description |
| **`ingredients`**  | List of ingredients used |
| **`n_ingredients`** | Number of ingredients in the recipe |

The second dataset, `RAW_interactions.csv`, has **731,927** rows, representing unique reviews for a given recipe (there are more than 1 reviews per individual recipe in the dataframe). This dataframe has 5 columns:

| Column             | Description |
|--------------------|-------------|
| **`user_id`**      | User ID |
| **`recipe_id`**    | Recipe ID |
| **`date`**         | Date of Interaction |
| **`rating`**       | Rating given |
| **`review`**       | Review text |

With these datasets, we aim to examine whether people rate high-carbohydrates and low-carbohydrates recipes differently. To support this analysis, we extracted individual nutrient values from the `nutrition` column into distinct categories such as `calories (#)`, `total fat (PDV)`, and `carbohydrates (PDV)`. The PDV, or percent daily value, represents the percentage a nutrient contributes to a typical daily diet. 

Furthermore, we calculated the proportion of carbohydrates relative to the total calories in a recipe and stored this value in a new column, `prop_carbohydrates`. Recipes classified as high-carbohydrates are those with a `prop_carbohydrates` value exceeding the average `prop_carbohydrates` across all recipes.

The key columns relevant to our analysis include:
- `calories (#)`: The total calorie content of the recipe.
- `carbohydrates (PDV)`: The percent daily value of carbohydrates.
- `prop_carbohydrates`: The proportion of carbohydrates relative to total calories.
- `rating`: The rating a user assigned to a recipe.
- `avg_rating`: The average of all ratings for each unique recipe.

Through this investigation, we hope to gain insights into how carbohydrates content influences user ratings. These findings could help Food.com recipe contributors adjust their recipes to better meet consumer needs. Additionally, this study may serve as a foundation for future research on public awareness regarding the health risks associated with excessive carbohydrate intake.

## Data Cleaning and Exploratory Data Analysis

In our analysis, we aim to explore the relationship between recipe ratings and nutritional content, with a specific focus on carbohydrates. To standardize comparisons across recipes, we transformed the raw nutritional data into a proportion-based format. Specifically, we calculated `prop_carbohydrates`, which represents the proportion of calories in a recipe that come from carbohydrates. This transformation allows us to assess carbohydrate content relative to the overall caloric composition of a recipe, making it easier to compare across recipes with varying portion sizes and total calorie counts.  

1. **Replace All Ratings of 0 with `NaN`**  
   - Recipe ratings are typically on a scale from **1 to 5**, where **1** represents the lowest rating and **5** represents the highest.  
   - A rating of **0** does not indicate a valid user rating but instead represents missing data.  
   - To prevent bias in our analysis, we replaced all **0 ratings** with `NaN`, ensuring that they do not skew the results.  

2. **Add an `avg_rating` Column**  
   - Since each recipe can receive multiple ratings from different users, we calculated the **average rating per recipe**.  
   - This allows us to gain a more comprehensive understanding of a recipe's overall reception rather than relying on individual ratings.  

3. **Extract Nutrient Values into Separate Columns**  
   - The `nutrition` column contains multiple nutrient values enclosed in brackets, but these values are stored as objects (similar to strings) rather than numerical data.  
   - Using the dataset’s column description, we identified what each value represents and split the `nutrition` column into **separate numerical columns**.  
   - We applied a lambda function to extract the values and converted them to **floats**, enabling numerical calculations and further analysis.  

4. **Computing`prop_carbohydrates` column** 
   - The dataset provides the carbohydrate content as a **percent daily value (PDV)** based on a standard 2,000-calorie diet.  
   - Since PDV is given as a percentage, we first converted it into decimal form:  
     ```
     carbohydrates (decimal) = carbohydrates (PDV) / 100
     ```

   - The Dietary Guidelines for Americans recommend **275g** of carbohydrates per day as the **100% daily value** for a 2,000-calorie diet.  
   - We multiplied the decimal value by **275** to estimate the grams of carbohydrates in a recipe:  
     ```
     carbohydrates (g) = carbohydrates (decimal) × 275
     ```

   - Since **1 gram of carbohydrates provides 4 calories**, we converted the carbohydrate content from grams to calories:  
     ```
     carbohydrate calories = carbohydrates (g) × 4
     ```

   - To standardize across recipes, we computed the proportion of total calories in the recipe that come from carbohydrates:  
     ```
     prop_carbohydrates = carbohydrate calories / total calories
     ```
   - After computing `prop_carbohydrates` we noticed that some values happened to be greater than 1, we believe this could be due to an error when the data was recorded (for example if PDV or total calories were slightly off), this could cause an issue with the proportion. Since there were only 140 rows that had a proportion greater than 1, we dropped these rows since that is a very small proportion of our data and can be disregarded.
   - After these steps the result of this column is values between 0 and 1, representing the fraction of total calories attributed to carbohydrates.

5. **Dropping Duplicates**
   - We then dropped duplicates so that there is only one row corresponding to each recipe 
     
The following table contains the columns of the cleaned dataframe. 
   
| Column              | Description               |
|---------------------|---------------------------|
| `name`             | `<class 'str'>`           |
| `id`               | `<class 'numpy.int64'>`   |
| `minutes`          | `<class 'numpy.int64'>`   |
| `contributor_id`   | `<class 'numpy.int64'>`   |
| `submitted`        | `<class 'str'>`           |
| `tags`             | `<class 'str'>`           |
| `nutrition`        | `<class 'list'>`          |
| `n_steps`         | `<class 'numpy.int64'>`   |
| `steps`           | `<class 'str'>`           |
| `description`     | `<class 'str'>`           |
| `ingredients`     | `<class 'str'>`           |
| `n_ingredients`   | `<class 'numpy.int64'>`   |
| `user_id`         | `<class 'numpy.float64'>` |
| `recipe_id`       | `<class 'numpy.int64'>`   |
| `date`           | `<class 'str'>`           |
| `rating`         | `<class 'numpy.float64'>` |
| `review`         | `<class 'str'>`           |
| `avg_rating`     | `<class 'numpy.float64'>` |
| `calories`       | `<class 'numpy.float64'>` |
| `total fat`      | `<class 'numpy.float64'>` |
| `sugar`         | `<class 'numpy.float64'>` |
| `sodium`        | `<class 'numpy.float64'>` |
| `protein`       | `<class 'numpy.float64'>` |
| `saturated fat` | `<class 'numpy.float64'>` |
| `carbohydrates` | `<class 'numpy.float64'>` |
| `prop_carbohydrates` | `<class 'numpy.float64'>` |

Our cleaned DataFrame ended up having **83692** rows and **26** columns. Here are the first 5 rows of our DataFrame, with relevant columns to our analysis since there are more than 25 columns and we might not need to use all of them:

| Name                                    | ID      | Minutes | Submitted           | Rating | Avg Rating | Calories (#) | Carbohydrates (PDV)|prop_carbohydrates|
|-----------------------------------------|---------|---------|---------------------|--------|------------|--------------|--------------------|------------------|
| 1 Brownies in the World Best Ever       | 333281  | 40      | 2008-10-27 00:00:00 | 3      | 4.0        | 138.4        | 6.0                | 0.476879         |
| 1 in Canada Chocolate Chip Cookies      | 453467  | 45      | 2011-04-11 00:00:00 | 5      | 5.0        | 595.1        | 26.0               | 0.480591         |
| Millionaire pound cake                  | 286009  | 120     | 2008-02-12 00:00:00 | 4      | 5.0        | 878.3        | 39.0               | 0.488444         |
| 412 Broccoli Casserole                  | 306168  | 40      | 2008-05-30 00:00:00 | 5      | 5.0        | 194.8        | 20.0	              | 0.169405         |
| 2000 Meatloaf                           | 306168  | 30      | 2008-05-30 00:00:00 | 4      | 5.0        | 267.0        | 2.0                | 0.082397         |


### Univariate Analysis
We created a plot representing the distribution of the proportion of carbohydrates in the recipes. As the plot shows, the distribution is skewed right which illustrates how the majority of recipes have a low proportion of carbohydrates.

<iframe
  src="assets/univariate_plot_final.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

### Bivariate Analysis
We examined the distribution of the rating of the recipe conditioned on whether or not we classified it as being high in carbs. We used a binarizer from Sklearn with a threshold of the mean of the `carbohydrate` column to create a new dataframe that classifies the recipe as being `True` if the `carbohydrate` amount associated with that recipe is above the threshold, and classifies the recipe as `False` if it is below the threshold. The graph below shows that the recipes with a rating of 4 and 5 are more likely to occur regardless if it is high in carbs or not. 

<iframe
  src="assets/final_bivariate_plot.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

### Interesting Aggregates

For this section, we looked at the relationship between the time it takes to make the recipe, in minutes, and the number of steps for a given recipe. We made this new DataFrame, that groups recipes by their total time and calculates the average number of steps for each time interval:

| minutes | n_steps   |
|---------|----------|
| 0       | 1.000000 |
| 1       | 3.744493 |
| 2       | 3.233593 |
| 3       | 3.742204 |
| 4       | 4.807547 |
| ...     | ...      |
| 127     | 8.111111 |
| 128     | 12.181818 |
| 129     | 17.750000 |
| 130     | 11.068396 |
| 132     | 12.210526 |

From this data, we observe that shorter recipes generally have fewer steps, while longer recipes tend to have more steps. However, the relationship is not always linear—some recipes with high time durations (e.g., 129 minutes) have significantly more steps than others in similar ranges. We visualized this trend using data visualization techniques to better understand the distribution. The interactive plot below illustrates this relationship:

<iframe
  src="assets/pivot_table_plot.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

## Assessment of Missingness

There are 3 columns in the merged dataframe that have missing values: `date`, `rating`, and `review`. In the following section we will be analzying the missigness of these columns. To evaluate the missingness, we used the dataframe before dropping the non-unique rows so that we have all of the information of every review for every recipe. 

## NMAR Analysis

We believe the missingness of the `review` column is NMAR because people who feel neutral about a recipe are less inclined to leave a review, as they may not see a reason to share their thoughts. Reviews are usually written by those who have strong reactions—either positive or negative—since their emotions push them to engage with the platform, complete the necessary steps, and take the time to write a review. For example, someone who loved the recipe would be more willing to go through the effort of posting positive feedback. Since the probability of missingness is directly tied to the underlying, unobserved review value, this makes the missing data NMAR.

To explain the missingness of the `review` column, and therefore make it MAR, an example of additional data we would need to obtain is Demographic or User Profile Information. If certain user demographics (e.g., first-time visitors vs. regular users, experienced cooks vs. beginners) influence whether they leave reviews, then missingness might be explained by these factors rather than by the sentiment of the review itself.

## Missingness Dependency

We continued by analyzing the missingness of the `rating` column in the merged DataFrame by checking the dependency of its missingness on two other columns: `prop_carbohydrates`, the proportion of carbohydrates out of the total calories and `sodium`, the amount of sodium used in each recipe.

> Sodium and Rating

**Null Hypothesis:** The missingness of ratings does not depend on the amount of sodium in the recipe.

**Alternate Hypothesis:** The missingness of ratings does depend on the amount of sodium in the recipe.

**Test Statistic:** The absolute difference of mean in amount of sodium in the recipe of the distribution of the group with missing ratings and the distribution of the group without missing ratings.

**Significance Level:** 0.05

We ran a permutation test by shuffling the missingness of rating for 1000 times to collect 1000 simulating mean differences in the two distributions as described in the test statistic.

<iframe
  src="assets/sodium_p_value.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

The observed statistic, 4.215, can be observed by the red line on the graph. Since the p-value that we found, 0.001, is less than 0.05, we reject the null hypothesis. The missingness of `rating` **does** depend on the amount of `sodium` in the recipe.

> Proportion of Carbohydrates and Rating

**Null Hypothesis:** The missingness of ratings does not depend on the proportion of carbohydrates in the recipe.

**Alternate Hypothesis:** The missingness of ratings does depend on the proportion of carbohydrates in the recipe.

**Test Statistic:** The absolute difference of mean in the proportion of carbohydrates of the distribution of the group with missing ratings and the distribution of the group without missing ratings.

**Significance Level:** 0.05

<iframe
  src="assets/distr_carb_prop_missing_rating.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

We ran another permutation test by shuffling the missingness of rating for 1000 times to collect 1000 simulating mean differences in the two distributions that are described in the test statistic.

<iframe
  src="assets/permutation_test_plot.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

The observed statistic, 0.0033, can be observed by the red line on the graph. Since the p-value that we found, 0.102, is greater than 0.05, we fail to reject the null hypothesis. The missingness of `rating` **does not** depend on the `prop_carbohydrates` in the recipe.

## Hypothesis Testing

As we mentioned before, we are interested in investigating whether users rate recipes differently based on whether or not they are high-carb recipes. To explore whether users rate high-carb recipes differently than others, we conducted a permutation test comparing the ratings of recipes with above-average carbohydrate proportions to those with below-average carbohydrate proportions.

**Null Hypothesis:** Carbohydrate content does not influence user ratings; recipes are rated the same regardless of their proportion of carbohydrates.

**Alternative Hypothesis:** High-carbohydrate recipes receive lower ratings than low-carbohydrate ones.

**Test Statistic:** We measured the difference in mean ratings between high-carbohydrate and low-carbohydrate recipes.

**Significance Level**
We set a 0.05 threshold for statistical significance.

A permutation test was chosen because we lack information about an underlying population distribution. This method allows us to determine whether the observed difference in ratings is due to chance. We hypothesized that high-carb recipes might receive lower ratings because users could associate them with unhealthy eating habits, and because of new growing trends in low-carbohydrate diets such as a keto or paleo diets, which emphasize cutting carbohydrates.

To test this, we split the dataset into two groups:

High-carb recipes: Recipes where `prop_carbohydrates` is above the average.
Low-carb recipes: Recipes where `prop_carbohydrates` is below or equal to the average.

We calculated an observed difference in mean rating of -0.0317. Then, we randomly shuffled the ratings 1,000 times to generate a null distribution.

The following histogram shows the distribution of mean rating differences under the null hypothesis. The red line represents the observed difference in our dataset:

<iframe
  src="assets/test_perm_test_carb_final.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Since the p-value we obtained (0.0001) is below the significance level of 0.05, we reject the null hypothesis. This suggests that people do not rate all recipes uniformly and tend to give lower ratings to recipes with more carbohydrates. A possible explanation for this finding is that individuals may be mindful of health risks associated with higher-carb foods, and might even prefer diets such as a keto or paleo diet where carbohydrates are heavily cut down on.

## Framing a Prediction Problem

We aim to develop a model that predicts the **average rating (`avg_rating`)** of a recipe based on its nutritional composition and other recipe attributes. Understanding how different factors influence user ratings can help recipe creators optimize their dishes to better align with consumer preferences. We also previously identified a correlation between ratings and recipes with a carbohydrate proportion higher than the average. This suggests that the proportion of carbohydrates may be a good predictor of a recipe's rating.

This is a **multiclass classification problem**, as the target variable, `avg_rating`, is a categorical, ordinal variable and can take one of five possible values: **1, 2, 3, 4, or 5**. Our response variable is `avg_rating`, representing the overall user rating for a recipe. We chose to predict this variable instead of individual ratings to reduce noise and ensure a more reliable measure of recipe quality. 

To evaluate our model, we will use the F1 score instead of accuracy, since the distribution of ratings is going to be highly skewed to the left, since most of the ratings in the dataset are 4's and 5's. Using accuracy might make the model's performance biased since there would be imbalanced classes.

Before making our predictions, we have access to all the columns in the `rating` dataset, as listed in the introduction. These columns serve as features related to the recipes themselves, meaning they are available even if no ratings or reviews have been submitted.

## Baseline Model
For our baseline model we decided to use a random forest classifier and split the data into a training and testing set. For this model we have decided to use `prop_carbohydrates`, a column containing numerical data representing the proportion of calories in the recipe that come from carbohydrates and `minutes`, a column also containing numerical data representing how long a recipe takes as features for predicting `avg_rating`. We dropped the rows that had an `avg_rating` of NaN because this data is not useful to our model. 

We chose to use F1 score as the metric for measuring our models performance becasue F1 score is the harmonic mean of precision and recall, meaning it considers both, which accounts for class imbalance. The average weighted F1 score of this model is 0.479. We used a weighted F1 score to evaluate the average F1 score because the frequency of `avg_rating`'s of 4’s and 5’s is much higher than the other ratings. The F1 score for each avg_rating category is 0.0 for an `avg_rating` of 1, 0.0073 for an `avg_rating` of 2, 0.0497 for an `avg_rating` of 3, 0.339 for an `avg_rating` of 4, and 0.609 for an `avg_rating` of 5. From these scores we can conclude that the model makes more accurate predictions for rating 4’s and 5’s and less accurate for lower ratings. The higher F1 scores for 4’s and 5’s suggests that these ratings are the majority classes, meaning that they occur much more frequently in the data. The F1 score of 0 for an `avg_rating` of 1’s means that the model never predicted this rating, this could be due to the fact that the Random Forest is favoring classes 4 and 5 because they dominate the dataset and has too few 1’s to learn the patterns or recipes with an `avg_rating` of 1. 


## Final Model
For our final model, we decided to use `minutes`, `calories`, `n_ingredients`, `long_review`, and `prop_carbohydrates` as the features to predict `avg_rating`. 

The `minutes` column is the time each recipe takes in minutes. By calculating the mean `minutes` for each avg_rating category (1-5) we concluded that recipes with an `avg_rating` of 5 took on average much longer to make than the recipes with smaller ratings. This difference leads us to believe that including the `minutes` could potentially improve our model and help more accurately predict the `avg_rating`'s of recipes). For this feature we decided to use StandardScaler to standardize the `minutes` feature to normalize the distribution which ensures that the data from this numerical column can be used in unison with other numerical features. 

The `calories` column contains the total calories of each recipe. By calculating the mean `calories` for each avg_rating category (1-5) we concluded that recipes with less calories tended to have a higher rating (of either 4 or 5). Therefore we believe that including `calories` as a feature could be useful when predicting the `avg_rating`, since it seems likely that there is a correlation. We also concluded that there are many recipes that have a calorie count which is outside of the IQR, meaning they are considered outliers. Therefore for this feature we decided to use RobustScaler because it is more robust to outliers due to the fact it used the median and IQR to standardize the data. This is important so that the model is not influenced by the many outliers that exist in the `calories` column. 

The `n_ingredients` column contains the number of ingredients used in each recipe. We decided to use `n_ingredients` as a feature to predict the `avg_rating` because after calculating the mean of `n_ingredient` for each category in rating, there was a slight upward trend that as the `n_ingredients` increased so did the `avg_rating`. For this feature we decided to use StandardScaler to standardize the distribution which ensures that the data from this numerical column can be used in unison with the other numerical features in our model such as `minutes`. 

The `long_review` column categorizes the data as having a long review or short review by using the mean length of the `reviews` column and then using a binarizer to classify each review as being a long review or not (if the recipe had no review we used 0 as the length of the review). We chose this feature because we believed that there is likely a correlation between the length of the review and the rating it was given. People are more likely to leave a review if they have strong opinions about the recipe, and are more likely to not leave a review if they had mediocre feelings about it. We used one hot encoding for this column since it is binary.

The `prop_carbohydrates` column contains the proportion of calories that come from carbohydrates out of the total calories in the recipe. According to our hypothesis test, people rate recipes with a smaller proportion of carbohydrates higher than those with a lower proportion. Due to this result we believe this feature would be beneficial to use in our model to predict avg_ratings since there seems to be correlation between `prop_carbohydrates` and `avg_rating`. We don’t need to standardize or transform this feature because the values are in between 0 and 1.  

We decided to use a RandomForestClassifier as our modeling algorithm because we have concluded that the relationship between many of the columns and `avg_rating` is not linear and Random Forests are suited to model complex non-linear relationships. We then used GridSearchCV to figure out the best parameters for max depth and n estimators. We found that the best combination of these hyperparameters is None for the max depth and 50 for the n estimators. 

The overall  weighted F1 score of this model is 0.5, which is a .021 increase from the F1 score of the baseline model. The F1 score for a rating of 1 is 0.0, 2 is 0.0, 3 is 0.0073, 4 is 0.27, and 5 is 0.69.  

## Fairness Analysis

For this fairness analysis, we examine whether our model performs differently for **short recipes** vs. **long recipes**, using the **mean preparation time** (`minutes`) as the threshold.  

- **Group X:** Short recipes (**≤ mean preparation time**)  
- **Group Y:** Long recipes (**> mean preparation time**)  

We used the **F1-score** as our evaluation metric because:  
- It balances **precision and recall**, making it suitable for multiclass classification.  
- It helps assess whether our model systematically favors one group over another.  

- **Null Hypothesis (H₀):** The model is fair—its F1-score for short and long recipes is roughly the same, and any observed differences are due to random chance.  
- **Alternative Hypothesis (H₁):** The model is unfair—its F1-score for short recipes is lower than for long recipes, indicating worse performance on shorter recipes.  

- **Test Statistic:** The absolute difference in F1-score between short and long recipes.  
- **Significance Level (α):** 0.05 (5%)  

1. **Compute observed F1-scores** for both short and long recipes.  
2. **Shuffle the `minutes` column** randomly to break any real relationship between preparation time and F1-score.  
3. **Recalculate the F1-score difference** for each shuffled dataset.  
4. **Repeat the process 1000+ times** to build a null distribution.  
5. **Compute the p-value**: The proportion of times the simulated F1-score difference is as extreme as or more extreme than the observed difference.  

### **Results**  
- **Mean Preparation Time Threshold:** 117 minutes  
- **Observed F1-score Difference:** *0.0132*  
- **p-value:** *0.333*  

### **Conclusion**  
Since our **p-value is ≥ 0.05**, we **fail to reject the null hypothesis**, meaning there is **no significant evidence of unfairness** in the model’s performance.  

Here is a visualization of the permutation test, showing the null distribution and the observed F1-score difference:

<iframe
  src="assets/fairness_analysis.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

















