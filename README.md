# Significant relations with ratings of recipes
This is a DSC80 project at UC San Diego. Which focus on finding a relationship between the caloric value and its average rating. As well as that of the average number of recipes required and its average rating. 
written by Aryan Shah & Joseph Guzman

---

## Introduction

Our project focuses on a dataset 'recipe' which, which takes into account the recipe provided by a user, along with any information attached to it, such as: the date of publishing, nutritional value, average ratings etc. Throughout the process of this experiment, we kept on digging deeper to find a question that would help answer an important question, that is not only related to the dataframe, but also helps analyze the human behavior when it came to choosing between recipes. We finally narrowed down to our Question of Interest which is:

**Does the calories specified of a recipe, affect the average ratings of that recipe itself?**

Our dataset initially consisted of 83,782 rows and 13 columns. Through through the process of data cleansing however, we narrowed it down to 8 columns of which are:
- `name` : The name of the recipe provided
- `id` : The id tag provided for each recipe
- `minutes` : The time taken to execute the recipe
- `contributor_id` : The personal id for the individual
- `n_steps` : Takes into account the number of steps needed to execute the recipe
- `n_ingredients` : The number of ingredients required for the recipe
- `average_rating` : The average rating the recipe received on a scale of 0 to 5
- `calories` : The calories present per recipe

---

## Cleaning and EDA

**Data Cleaning**

In order to make the dataframe more easily understandable, as well as easier to work with, we began by removing columns that were irrelevent to the topic that we were working on. This not only made it easier to work with the dataframe, but it also helped in making the dataframe run faster, thus saving us more time.
We columns which we removed were:
- `description` : Gives a breif description of the recipe which the user might prepare
- `steps` : Gives an overview of the process in order to make the recipe
- `submitted` : Gives the date of publishing
- `tags` : Gives a slogan under which the recipe comes under
- `ingredients` : Gives a list of ingredients one may need to complete the recipe

As you can see from the desctiption below, none of the columns which we've dropped have anything to do with *our project*, since none of them coorelate with the calories of the recipe.

Now that we finished dropping the columns, we also needed to form a new column `first_nutrition`, which takes the `nutrition` column, and returns the first value of the list for each individual row. No that this new column is formed, there no use for the `nutrition` column anymore, so we can remove that too.

After we form the nutrition column, we need to ensure that there are no duplicates in the column, as to not cause any faulty reading, however, before doing that, we need to take into account the average rating, to ensure that the mean of all the ratings per recipe are taking into account. Moreover, we will also replace all values marked as **NaN** with a 0, to ensure that no value is left out.

```py
# First we had to combined both the csv files into one dataset. 
recipes = pd.read_csv('food_data/RAW_recipes.csv')
interactions = pd.read_csv('food_data/RAW_interactions.csv')

merged = pd.merge(recipes, interactions, left_on='id', right_on='recipe_id', how='left')

merged['rating'].replace(0, np.nan, inplace=True) 
average_rating_per_recipe = merged.groupby('id')['rating'].mean()
recipes = pd.merge(recipes, average_rating_per_recipe, left_on='id', right_index=True, how='left')
recipes.rename(columns={'rating': 'average_rating'}, inplace=True)

# recipes is a dataset that has both recipes and interactions
recipes

# Now we had to clean and appropiate our data with the necessary items for our research. 
recipes_cleaned = recipes.drop(columns=['description','steps','submitted','tags','ingredients'])
recipes_cleaned['calories'] = recipes_cleaned['nutrition'].apply(lambda x: x.split(',')[0][1:]).astype(float)
recipes_cleaned = recipes_cleaned.drop(columns = ['nutrition'])
average_rating_mean = recipes_cleaned['average_rating'].mean()
recipes_cleaned['average_rating'].fillna(average_rating_mean, inplace=True)
recipes_cleaned = recipes_cleaned.drop_duplicates()
recipes_cleaned['name'].fillna(np.nan, inplace=True)

# Finally, recipes_cleaned is the final output of data.
recipes_cleaned.head()
```

| name                                 |     id |   minutes |   contributor_id |   n_steps |   n_ingredients |   average_rating |   first_nutrition |
|:-------------------------------------|-------:|----------:|-----------------:|----------:|----------------:|-----------------:|------------------:|
| 1 brownies in the world    best ever | 333281 |        40 |           985201 |        10 |               9 |                4 |             138.4 |
| 1 in canada chocolate chip cookies   | 453467 |        45 |          1848091 |        12 |              11 |                5 |             595.1 |
| 412 broccoli casserole               | 306168 |        40 |            50969 |         6 |               9 |                5 |             194.8 |
| millionaire pound cake               | 286009 |       120 |           461724 |         7 |               7 |                5 |             878.3 |
| 2000 meatloaf                        | 475785 |        90 |          2202916 |        17 |              13 |                5 |             267   |

---

**Univariate Analysis**

In the univariate analysis, we decided to conduct analysis in three different forms. Each graph, is given a description of the analysis and how it interprets to our research. We decided to conduct our analysis with bar graphs because it allowed us to better understand the relationships of the distributions. 


<iframe src="assets/UA_Distribution_of_Caloric_Values_in_Recipes.html
" width=800 height=600 frameBorder=0></iframe>


To start off, we measured the distribution of calorical values within the amount of recipes and we found out that there was a higher count of recipes of lower calorical value. Although data seems slim, due to the bin size being 100, there is still some sort of correlation happening. 


<iframe src="assets/UA_new_Distribution_of_Recipe_Ratings.html" width=800 height=600 frameBorder=0></iframe>


Next, we decided to measure the distribution of the recipe ratings within the amount of recpies and it was interesting to point out, that majority of these recipies ranked higher. 


<iframe src="assets/UANew_Distribution_of Number_of_Ingredients_in_Recipes.html" width=800 height=600 frameBorder=0></iframe>


Lastly, we decided to measure the distribution of the number of ingredients within the amount of recipies and we notice that there is a bell-shape trend. Pin-pointing the highest to be the average of the number of ingrediants. 



**Bivariate Analysis**

In our bivarate analysis, we wanted to see if there were any correlations from our caloric value and number of ingredients to its average rating. We decided to conduct our analysis oin scatter plots because it allowed us a illustrate a dimensional relationships between our comparison. 

<iframe src="assets/BA_Correlation_between_Average_Rating_and_Caloric_Value.html" width=800 height=600 frameBorder=0></iframe>

First off, we noticed that the data is thinly scattered across the axis, as there is few points at the top and bottom left. It's very dominate when we recieve a lower caloric value and high average rating. 

<iframe src="assets/BA_Correlation_between_Average_Rating_and_Number_of_Ingredients.html" width=800 height=600 frameBorder=0></iframe>

Secondly, from the above data,there happens to be a big cluster of data when the average rating is higher, and the number of ingredents is lower. It happens to be almost evened out acorss both axis, but definetly a trend is occuring.


**Interesting Aggregates**

```py
agg_by_ingredients = recipes_cleaned.groupby('n_ingredients')['average_rating'].agg(['mean', 'count']).reset_index()
agg_by_ingredients
```


|   n_ingredients |    mean |   count |\n|----------------:|--------:|--------:|\n|               1 | 4.84467 |      14 |\n|               2 | 4.69042 |     747 |\n|               3 | 4.66106 |    2342 |\n|               4 | 4.63369 |    4481 |\n|               5 | 4.64668 |    6580 |\n|               6 | 4.63288 |    7524 |\n|               7 | 4.62411 |    8515 |\n|               8 | 4.61193 |    8923 |\n|               9 | 4.60693 |    8628 |\n|              10 | 4.61073 |    8033 |\n|              11 | 4.62284 |    6965 |\n|              12 | 4.61701 |    5722 |\n|              13 | 4.63165 |    4491 |\n|              14 | 4.61667 |    3234 |\n|              15 | 4.63043 |    2398 |\n|              16 | 4.62502 |    1691 |\n|              17 | 4.63233 |    1143 |\n|              18 | 4.68577 |     777 |\n|              19 | 4.61218 |     510 |\n|              20 | 4.60867 |     381 |\n|              21 | 4.65744 |     220 |\n|              22 | 4.69002 |     143 |\n|              23 | 4.7646  |      99 |\n|              24 | 4.60523 |      74 |\n|              25 | 4.69579 |      43 |\n|              26 | 4.75468 |      29 |\n|              27 | 4.60973 |      23 |\n|              28 | 4.84625 |      18 |\n|              29 | 4.96571 |      10 |\n|              30 | 4.84795 |      12 |\n|              31 | 5       |       8 |\n|              32 | 5       |       2 |\n|              33 | 5       |       1 |\n|              37 | 5       |       1 |

```py
agg_by_calories = recipes_cleaned.groupby('first_nutrition')['average_rating'].agg(['mean', 'count']).reset_index()
agg_by_calories.head()
```

|   first_nutrition |    mean |   count |\n|------------------:|--------:|--------:|\n|               0   | 4.47022 |      26 |\n|               0.1 | 4.56319 |       7 |\n|               0.2 | 5       |       4 |\n|               0.3 | 4.47273 |      11 |\n|               0.4 | 4.6875  |       8 |

The dataframe above helps us understand the distribution of the calories with respect to the mean rating. It is a way for us to analyze what type of recipes get used the most based on its caloric levels, as well as, as to how they are rated, which lets us know if higher calorie meals are more favorable, or lesser calorie meals. The count also helps us understand if people prefer *healthier options*, or options that are more *unhealthy*.

---

## Assessment of Missingness

**NMAR Analysis**

**Missing Depedency**

---

## Hypothesis Testing

Futhering our analysis, we still yet have to get to the bottom of our question: Does the calories specified of a recipe, affect the average ratings of that recipe itself?

For the purpose of our analysis, we will consider recipies higher than or equal to 4.0, to be high-rating.

*`Null Hypothesis (*H0*)`:*
In the population, the caloric values in restaurants with less than 4 stars and those with greater than or equal to 4 stars have the same distribution.

*`Alternative Hypothesis (*H1*)`:*
In the population, recipes with a rating higher than 4 stars have a lower caloric value than recipes with a rating lower than 4 stars, on average.


*`Permutation Testing`:*
In order to find the p value, and decide whether to reject or accept the null hypothesis we used the *Absolute Mean test statistic*, since it seemed to be the best fit for this. We worked on different significant levels starting from 0.01 all the way to 0.1, and decided to stick with the significant level(α) of 0.1 since it would allow us to see if there was any possible trend between the caloric value of the recipe as well as its rating.

```py
recipies_Hyp = recipes_cleaned[['average_rating','first_nutrition']].copy()
recipies_Hyp['high_rating'] = recipies_Hyp['average_rating'] >= 4.0
recipies_Hyp['high_rating'] = recipies_Hyp['high_rating'].astype(bool)
recipies_Hyp.head()
```

|   average_rating |   first_nutrition | high_rating   |
|-----------------:|------------------:|:--------------|
|                4 |             138.4 | True          |
|                5 |             595.1 | True          |
|                5 |             194.8 | True          |
|                5 |             878.3 | True          |
|                5 |             267   | True          |


<iframe src="assets/comparison.html" width=800 height=600 frameBorder=0></iframe>

Given that this is soley for comparison, we can identify patterns within the data.

Continuing with our research, we conducted a permutation with `n_repetitions = 1000` using our testing statistic, to measure out different instances of the mean. 


```py
observed_difference = recipies_Hyp.groupby('high_rating')['first_nutrition'].mean().diff().iloc[-1]
```

The observed_difference is -15.6866. This is then stated over at our empirical distribution over our simulation. 

<iframe src="assets/empirical_distribution.html" width=800 height=600 frameBorder=0></iframe>

Therefore, The p-value which we obtained was 0.077, thus we could Reject the Null Hypothesis. With the help of this test we were able to figure that there is a relationship between the caloric value of a recipe as well as its average rating, however this is simply a trend.

It's important to emphasize that statistical significance does not imply practical significance, and the results should be interpreted in the context of the study and its limitations.
---
