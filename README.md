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


