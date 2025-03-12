# Analysis on the Relationship Between Amount of Sodium on Recipe Rating
An analysis of the relationship between amount of sodium and recipe ratings for our DSC 80 project at UCSD. By Karina Shah and Audrey Meredith.

## Overview

Our data science project, which we carried out at UCSD focuses on exploring the relationship between the rating of a recipe and the proportion of sodium that contributed to the total calorie amount in any specific recipe. 

## Introduction

Food is an essential part of our daily lives, and cooking is a hobby that brings joy and satisfaction to many. Sodium intake is a crucial factor that impacts our health. Excessive sodium consumption has been linked to serious health risks such as high blood pressure, heart disease, and stroke. According to the Centers for Disease Control and Prevention, nearly half of U.S. adults have high blood pressure, a condition worsened by excessive sodium intake. With this in mind, we aim to explore the relationship between recipe ratings and sodium content. **We wonder if people tend to rate recipes with high sodium content lower due to health concerns.** To investigate this, we are analyzing a dataset containing recipes and ratings from [food.com](https://www.food.com/). This dataset was originally scraped and used by the authors of the recommender systems paper, [Generating Personalized Recipes from Historical User Preferences](https://cseweb.ucsd.edu/~jmcauley/pdfs/emnlp19c.pdf) by Majumder et al. Specifically, we are working with a subset of the original dataset that includes only recipes and reviews posted since 2008. By analyzing these datasets, we aim to determine whether sodium content influences how people rate recipes and whether health concerns play a role in these ratings.

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

With these datasets, we aim to examine whether people rate high-sodium and low-sodium recipes differently. To support this analysis, we extracted individual nutrient values from the `nutrition` column into distinct categories such as `calories (#)`, `total fat (PDV)`, and `sodium (PDV)`. The PDV, or percent daily value, represents the percentage a nutrient contributes to a typical daily diet. 

Furthermore, we calculated the proportion of sodium relative to the total calories in a recipe and stored this value in a new column, `prop_sodium`. Recipes classified as high-sodium are those with a `prop_sodium` value exceeding the average `prop_sodium` across all recipes.

The key columns relevant to our analysis include:
- `calories (#)`: The total calorie content of the recipe.
- `sodium (PDV)`: The percent daily value of sodium.
- `prop_sodium`: The proportion of sodium relative to total calories.
- `rating`: The rating a user assigned to a recipe.
- `avg_rating`: The average of all ratings for each unique recipe.

Through this investigation, we hope to gain insights into how sodium content influences user ratings. These findings could help Food.com recipe contributors adjust their recipes to better meet consumer needs. Additionally, this study may serve as a foundation for future research on public awareness regarding the health risks associated with excessive sodium intake.

## Data Cleaning and Exploratory Data Analysis


