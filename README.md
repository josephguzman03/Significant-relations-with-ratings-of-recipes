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



**Univariate Analysis**


**Bivariate Analysis**

In our bivarate analysis, we wanted to see if there were any correlations from our caloric value and number of ingredients to its average rating. We decided to conduct our analysis oin scatter plots because it allowed us a illustrate a dimensional relationships between our comparison. 

<iframe src="assets/BA_Correlation_between_Average_Rating_and_Caloric_Value.html" width=800 height=600 frameBorder=0></iframe>

First off, we noticed that the data is thinly scattered across the axis, as there is few points at the top and bottom left. It's very dominate when we recieve a lower caloric value and high average rating. 

<iframe src="assets/BA_Correlation_between_Average_Rating_and_Number_of_Ingredients.html" width=800 height=600 frameBorder=0></iframe>

Secondly, from the above data,there happens to be a big cluster of data when the average rating is higher, and the number of ingredents is lower. It happens to be almost evened out acorss both axis, but definetly a trend is occuring.


**Interesting Aggregates**

---

## Assessment of Missingness

**NMAR Analysis**

**Missing Depedency**

---

## Hypothesis Testing


<iframe src="assets/comparison.html" width=800 height=600 frameBorder=0></iframe>


<iframe src="assets/empirical_distribution.html" width=800 height=600 frameBorder=0></iframe>


---
