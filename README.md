# Significant relations with ratings of recipes
This is a DSC80 project at UC San Diego. Which focus on finding a relationship between the caloric value and its average rating. As well as that of the average number of recipes required and its average rating. 
written by Aryan Shah & Joseph Guzman

## Introduction

Our project focuses on a dataset 'recipe' which, which takes into account the recipe provided by a user, along with any information attached to it, such as: the date of publishing, nutritional value, average ratings etc. Throughout the process of this experiment, we kept on digging deeper to find a question that would help answer an important question, that is not only related to the dataframe, but also helps analyze the human behavior when it came to choosing between recipes. We finally narrowed down to our Question of Interest which is:

** Does the calories specified of a recipe, affect the average ratings of that recipe itself? **

Our dataset initially consisted of 83,782 rows and 13 columns. Through through the process of data cleansing however, we narrowed it down to 8 columns of which are:
- `name` : The name of the recipe provided
- `id` : The id tag provided for each recipe
- `minutes` : The time taken to execute the recipe
- `contributor_id` : The personal id for the individual
- `n_steps` : Takes into account the number of steps needed to execute the recipe
- `n_ingredients` : The number of ingredients required for the recipe
- `average_rating` : The average rating the recipe received on a scale of 0 to 5
- `calories` : The calories present per recipe