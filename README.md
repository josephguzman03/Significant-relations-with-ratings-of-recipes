# Significant Relations With Ratings of Recipes

Welcome to our Data Science 80 (DSC80) project at UC San Diego, where we delve into the captivating realm of culinary exploration. Our project centers around unraveling the intricate relationship between the caloric content of recipes and their corresponding average ratings. This research unfolds across four distinct sections: *Introduction*, *Cleaning and Exploratory Data Analysis (EDA)*, *Assessment of Missingness*, and *Hypothesis Testing*.

In a world where culinary experiences intertwine with daily life, understanding the dynamics of the food we consume becomes paramount. Our curiosity was piqued when we pondered the possibility of a connection between the caloric value of recipes and the subjective measure of their enjoyment—the average rating. It dawned on us that recipes, as a tangible source of consumption, might share similarities with platforms like Yelp reviews and food blogs, where qualitative assessments often intertwine with quantitative elements.

Food is not merely a means of sustenance; it is a cultural, social, and sensory experience. By exploring the correlation between caloric value and ratings, we aim to uncover patterns that extend beyond the plate. This analysis may provide insights into dietary preferences, nutritional awareness, and the complex interplay between flavor and health.

Our journey begins with a meticulous exploration of the dataset, unraveling its nuances to form a cohesive narrative. As we navigate through the stages of data cleaning, EDA, assessment of missingness, and hypothesis testing, we aim to unearth compelling findings that resonate with both food enthusiasts and data aficionados alike.

Writen by Aryan Shah & Joseph Guzman

---

## Introduction

Our project centers around a comprehensive exploration of the `recipe` dataset, a diverse repository encompassing user-provided recipes along with associated metadata, including the date of publishing, nutritional value, and average ratings. Amidst the myriad of possibilities, our quest for a meaningful question led us to unravel a profound aspect not only concerning the dataset but also shedding light on the intricacies of human behavior in the realm of culinary choices.

In our pursuit of a question, we meticulously examined the dataset, aiming to pose an inquiry that could unravel deeper insights into the dynamics of recipe selection. The question that emerged as the focal point of our investigation is:

**Does the specified caloric content of a recipe influence its average ratings?**

This inquiry delves into the nexus between the nutritional aspect of a recipe, as quantified by its caloric value, and the subjective assessment of its appeal measured by average ratings. As we navigate through this exploration, we aim to decipher whether the nutritional profile of a recipe plays a discernible role in shaping the overall perception and appreciation of the culinary creation.

Initially comprising 83,782 rows and 13 columns, our dataset underwent meticulous data cleansing to refine its essence. The distilled dataset now features 8 key columns, each contributing unique facets to our analysis:
- `name` : The distinctive name assigned to each recipe.
- `id` : A unique identifier tagging each recipe for reference.
- `minutes` : The time required for the execution of the recipe.
- `contributor_id` : A personal identifier linking each recipe to its contributor.
- `n_steps` : The number of steps involved in the recipe's execution.
- `n_ingredients` : The count of ingredients essential for the recipe.
- `average_rating` : The average rating garnered by the recipe on a scale ranging from 0 to 5.
- `calories` : The caloric content per serving of the recipe.

As we embark on this analytical journey, these refined attributes serve as the foundation for unraveling the intricate relationship between caloric value and average ratings, providing a nuanced understanding of the interplay between nutritional components and culinary acclaim.

---

## Cleaning and EDA

**Data Cleaning**

To enhance the clarity and efficiency of our dataframe, we initiated a strategic refinement process aimed at excluding columns unrelated to the specific focus of our project. This not only streamlined our interaction with the dataframe but also contributed to a noticeable improvement in its processing speed, consequently optimizing our time utilization.

The removed columns encompassed elements deemed irrelevant to our analytical endeavors, such as:

- `description` : Providing a brief overview of the recipe, potentially aiding users in preparation.
- `steps` : Outlining the procedural details for crafting the recipe.
- `submitted` : Indicating the date of recipe publication.
- `tags` : Gives a slogan under which the recipe comes under
- `ingredients` : Enumerating the necessary components for completing the recipe.

Evidently, as articulated in the following description, none of the excised columns bore any relevance to the core objective of our *project*, as they failed to exhibit any correlation with the caloric content of the recipes.

Having successfully pruned the dataframe, our attention turned to the creation of a new column, namely `first_nutrition`. This column extracted the initial value from the `nutrition` column for each respective row. With this addition, the `nutrition` column became redundant, prompting its removal.

Subsequent to the introduction of the `first_nutrition` column, a meticulous quality check ensued. This involved the elimination of duplicates to prevent inaccuracies in data interpretation. However, prior to this step, consideration was given to the `average_rating` column to ensure the inclusion of its influence on the mean of all ratings per recipe. Additionally, any instances marked as **NaN** were replaced with 0 to prevent any inadvertent exclusion of values from our analysis. This comprehensive approach ensures the integrity and reliability of our dataset as we delve into the intricate relationship between caloric content and recipe ratings.

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


**Exploratory Data Analysis (EDA)**

**Univariate Analysis**

For the univariate analysis, we opted for a multifaceted approach, conducting the examination through three distinct forms. Each graph is accompanied by a descriptive analysis, elucidating its relevance to our research and the insights it contributes. The choice of utilizing bar graphs was deliberate, as this visualization method provided a nuanced perspective, enhancing our comprehension of the distributional dynamics inherent in the dataset. This deliberate selection of graphical representation aimed to unravel and articulate the intricate relationships embedded within the data, contributing to a more comprehensive understanding of our research objectives.


<iframe src="assets/UA_Distribution_of_Caloric_Values_in_Recipes.html
" width=800 height=600 frameBorder=0></iframe>


Initiating our exploration, we investigated the distribution of caloric values in recipes, revealing a predominant concentration of lower caloric values. Despite a seemingly sparse dataset with a bin size of 100, discernible correlations emerged, hinting at an underlying relationship and prompting further exploration into the nuanced dynamics of caloric values within our dataset.


<iframe src="assets/UA_new_Distribution_of_Recipe_Ratings.html" width=800 height=600 frameBorder=0></iframe>


Continuing our exploration, we begain to examine the distribution of recipe ratings, revealing a substantial majority with higher ratings. This intriguing observation prompts further investigation into the factors contributing to the prevalent trend of elevated ratings within our dataset.

<iframe src="assets/UANew_Distribution_of Number_of_Ingredients_in_Recipes.html" width=800 height=600 frameBorder=0></iframe>


Concluding our univariate analysis, we observed a bell-shaped trend in the distribution of the number of ingredients, with the peak indicating the average number per recipe. This distinctive pattern invites further scrutiny, prompting exploration into the significance and implications of this central tendency within our dataset.


**Bivariate Analysis**

Transitioning into our bivariate analysis, our objective was to unveil potential correlations between caloric value, the number of ingredients, and their collective impact on average ratings. To effectively depict the intricate relationships within these variables, we opted for the use of scatter plots. This graphical approach affords us the ability to visually articulate the dimensional interplay between our chosen parameters, facilitating a nuanced exploration of their mutual influence on recipe ratings.

<iframe src="assets/BA_Correlation_between_Average_Rating_and_Caloric_Value.html" width=800 height=600 frameBorder=0></iframe>

Initially, our observation of the scatter plots revealed a sparse distribution of data points across the axes. Particularly notable is the prevalence of points in the lower left quadrants, indicating a distinct count pattern where lower caloric values align with higher average ratings. 

<iframe src="assets/BA_Correlation_between_Average_Rating_and_Number_of_Ingredients.html" width=800 height=600 frameBorder=0></iframe>

Additionally, upon closer examination of the data, a significant cluster becomes apparent, particularly in scenarios where the average rating is higher and the number of ingredients is lower. Although the distribution appears spanning across both axes, a subtle underlying trend is discernible.


**Interesting Aggregates**

Concluding our analysis, during the aggregation of our dataset, we directed our attention to exploring the relationship between `n_ingredients` and `first_nutrition` with `average_rating` to delve deeper into the realm of numeric data. This strategic focus aims to unravel additional layers of insights embedded within the dataset, offering a more comprehensive understanding of the numerical dynamics at play.

```py
agg_by_ingredients = recipes_cleaned.groupby('n_ingredients')['average_rating'].agg(['mean', 'count']).reset_index()
agg_by_ingredients.head()
```

|   n_ingredients |    mean |   count |
|----------------:|--------:|--------:|
|               1 | 4.84467 |      14 |
|               2 | 4.69042 |     747 |
|               3 | 4.66106 |    2342 |
|               4 | 4.63369 |    4481 |
|               5 | 4.64668 |    6580 |
|               6 | 4.63288 |    7524 |
|               7 | 4.62411 |    8515 |
|               8 | 4.61193 |    8923 |
|               9 | 4.60693 |    8628 |
|              10 | 4.61073 |    8033 |
|              11 | 4.62284 |    6965 |
|              12 | 4.61701 |    5722 |
|              13 | 4.63165 |    4491 |
|              14 | 4.61667 |    3234 |
|              15 | 4.63043 |    2398 |
|              16 | 4.62502 |    1691 |
|              17 | 4.63233 |    1143 |
|              18 | 4.68577 |     777 |
|              19 | 4.61218 |     510 |
|              20 | 4.60867 |     381 |
|              21 | 4.65744 |     220 |
|              22 | 4.69002 |     143 |
|              23 | 4.7646  |      99 |
|              24 | 4.60523 |      74 |
|              25 | 4.69579 |      43 |
|              26 | 4.75468 |      29 |
|              27 | 4.60973 |      23 |
|              28 | 4.84625 |      18 |
|              29 | 4.96571 |      10 |
|              30 | 4.84795 |      12 |
|              31 | 5       |       8 |
|              32 | 5       |       2 |
|              33 | 5       |       1 |
|              37 | 5       |       1 |

The presented dataframe illustrates the distribution of the number of ingredients in relation to the mean rating, offering insights into the aggregation of means across the board. It reveals a consistent pattern in mean values, even as the number of recipes varies, echoing the stability observed in the scatter plot.


```py
agg_by_calories = recipes_cleaned.groupby('first_nutrition')['average_rating'].agg(['mean', 'count']).reset_index()
agg_by_calories.head()
```

|   first_nutrition |    mean |   count |
|------------------:|--------:|--------:|
|               0   | 4.47022 |      26 |
|               0.1 | 4.56319 |       7 |
|               0.2 | 5       |       4 |
|               0.3 | 4.47273 |      11 |
|               0.4 | 4.6875  |       8 |


The presented dataframe provides valuable insights into the distribution of calories relative to the mean rating. This analysis serves as a pivotal tool for understanding the popularity and reception of different recipe types based on their caloric levels. It enables us to discern patterns in the usage and ratings of recipes, shedding light on whether higher calorie meals are more favorably received or if there is a preference for lower calorie options.


---

## Assessment of Missingness

**NMAR Analysis**

In the course of our NMAR Analysis, we were attuned to the potential impact of missing data within columns on the outcomes of other columns. Upon scrutinizing our datasets, we identified `average_ratings` as a column susceptible to NMAR, as it was one of only two columns with possible null values, the other being `name`. After a meticulous examination, we chose `average_ratings` to be NMAR. Additionally, our Univariate and Bivariate Analysis highlighted a clustering of points within the scatterplots, reinforcing our belief that the `average_ratings` column exhibited a correlation with its dependencies.

**Missing Depedency**

To initiate our analysis, we embarked on a comparison between null and non-null values within the `first_nutrition` distributions for ratings. This necessitated the segregation of our data into distinct datasets, as mentioned previously, to facilitate the replacement of null values for average ratings in preparation for our analysis. Opting to categorize our caloric values, we aimed to enhance the clarity of the comparison and glean insights into the nuanced relationship between calories and ratings.

```py
def group(calorie):
    if calorie <= 100:
        return 'Too Low'
    if 100 < calorie <= 300:
        return 'Low'
    elif 300 < calorie <= 800:
        return 'Healthy'
    elif 800 < calorie <= 1200:
        return 'High'
    else:
        return 'Excessive'
```
Now we cleaned up our dataset again, without filling in `np.nan`. 

```py
missing_rating = recipes.drop(columns=['description','steps','submitted','tags','ingredients'])
missing_rating['first_nutrition'] = missing_rating['nutrition'].apply(lambda x: x.split(',')[0][1:]).astype(float)
missing_rating = missing_rating.drop(columns = ['nutrition'])
missing_rating['tracker'] = missing_rating['first_nutrition'].apply(group)
missing_rating['missing_rating'] = pd.isna(missing_rating['average_rating'])
missing_rating.head()
```

| name                                 |     id |   minutes |   contributor_id |   n_steps |   n_ingredients |   average_rating |   first_nutrition | tracker   | missing_rating   |
|:-------------------------------------|-------:|----------:|-----------------:|----------:|----------------:|-----------------:|------------------:|:----------|:-----------------|
| 1 brownies in the world    best ever | 333281 |        40 |           985201 |        10 |               9 |                4 |             138.4 | Low       | False            |
| 1 in canada chocolate chip cookies   | 453467 |        45 |          1848091 |        12 |              11 |                5 |             595.1 | Healthy   | False            |
| 412 broccoli casserole               | 306168 |        40 |            50969 |         6 |               9 |                5 |             194.8 | Low       | False            |
| millionaire pound cake               | 286009 |       120 |           461724 |         7 |               7 |                5 |             878.3 | High      | False            |
| 2000 meatloaf                        | 475785 |        90 |          2202916 |        17 |              13 |                5 |             267   | Low       | False            |

Then, we opted to create a pivot table of the missing calorical values for each respective `tracker`. 


| tracker   |     False |      True |
|:----------|----------:|----------:|
| Excessive | 0.0403213 | 0.0670755 |
| Healthy   | 0.413204  | 0.415102  |
| High      | 0.0538849 | 0.0678421 |
| Low       | 0.377207  | 0.340744  |
| Too Low   | 0.115383  | 0.109237  |
 

 Down below is a visual example of the table above as a bar histogram. 


<iframe src="assets/Missing_Values.html" width=800 height=600 frameBorder=0></iframe>


```py
observed_tvd = calorie_dist.diff(axis=1).iloc[:, -1].abs().sum()/ 2
 ```


The observeed TVD is 0.0426.


<iframe src="assets/Empirical_Distribution_of_the_TVD.html" width=800 height=600 frameBorder=0></iframe>


Upon gathering our permutations, we reject the null hypothesis, positing that the distribution of `tracker` when `ratings` is missing originates from the same distribution as when `ratings` is not missing. This rejection enables us to affirm that the missingness in the `ratings` column is **dependent** on `tracker`.


---

## Hypothesis Testing

Arriving to our last section of our analysis, we are yet to unravel the answer to our overarching question: Does the specified caloric content of a recipe affect its average ratings?

For the purposes of our analysis, we categorize recipes with a rating higher than or equal to 4.0 as high-rating.

*`Null Hypothesis (**H0**)`:*
In the population, the distribution of caloric values in recipes with less than 4 stars is the same as those with greater than or equal to 4 stars.

*`Alternative Hypothesis (**H1**)`:*
In the population, recipes with a rating higher than 4 stars exhibit a lower caloric value, on average, than recipes with a rating lower than 4 stars.


*`Permutation Testing`:*
To find the p-value and make a determination on whether to reject or accept the null hypothesis, we employed the *Absolute Mean test statistic*, deemed most suitable for this scenario. We explored various significance levels ranging from 0.01 to 0.1 and ultimately settled on a significance level (α) of 0.1. This choice allows us to discern potential trends between the caloric value of a recipe and its rating.

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


We decided to generate an additional dataframe that incorporates a `high_rating` column, a `boolean` indicator detecting whether the `average_rating` column is a **NaN** or not.


```py
recipies_Hyp.groupby('high_rating')['first_nutrition'].agg(['mean', 'count'])
```


| high_rating   |    mean |   count |
|:--------------|--------:|--------:|
| False         | 444.612 |    5350 |
| True          | 428.925 |   78432 |

An aggregated table indicating the mean and count for null and non-null `high_rating` values. 

<iframe src="assets/comparison.html" width=800 height=600 frameBorder=0></iframe>

Above, the diagram depicts a visual comparison of the probability and the shuffled permutation. Given that this, we can identify patterns within the data.

We then executed a permutation with `n_repetitions = 1000` employing our testing statistic, thereby generating a multitude of instances to measure variations in the mean.


```py
observed_difference = recipies_Hyp.groupby('high_rating')['first_nutrition'].mean().diff().iloc[-1]
```

The observed_difference is -15.6866. This is then stated over at our empirical distribution over our simulation. 

<iframe src="assets/empirical_distribution.html" width=800 height=600 frameBorder=0></iframe>


As a result, we obtained a p-value of 0.077, which was greater than our significant level, in which we failed to reject the Null Hypothesis. This aligns with our understanding from the missingness assessments, indicating that with proper data, we could derive more accurate insights. This test reveals a relationship between the caloric value of a recipe and its average rating. In conclusion, this finding within our findings represents more of a trend than a conclusive relationship (also stated within our EDA). 

It's important to highlight that while statistical significance is observed, its practical implications should be considered in the context of the study and its inherent limitations.
---
