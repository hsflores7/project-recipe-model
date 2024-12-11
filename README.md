# Project Recipe Model
*Author: Hunter Flores*

> This is a project from the course DSC 80 at UC San Diego during the Fall 2024 quarter.

## Introduction
Sugar, a very potent factor in a food. Often sugar can be a big factor that determines
if one may like or dislike any given meal. Sometimes it's too sweet, sometimes not
sweet enough. And it all depends on the individual person as well. So the quetion
is are their trends, or predictable trends when it comes sugar content and general
enjoyment of food. Here we will explore this with a handful of different ways!

## Data Cleaning and Exploratory Data Analysis
### Data Cleaning
Here is the code used for data cleaning:
```py
# Merge
columns_to_keep = ['name', 'id', 'minutes', 'contributor_id', 'submitted', 'tags',
       'nutrition', 'n_steps', 'steps', 'description', 'ingredients',
       'n_ingredients', 'date', 'rating']
recipes_reviews = pd.merge(recipes, reviews, left_on='id', right_on='recipe_id', how='left')[columns_to_keep]
recipes_reviews = recipes_reviews.replace(0, np.nan)
avg_ratings = recipes_reviews.groupby('id')['rating'].mean()
avg_ratings.name = 'avg_rating'
df = pd.merge(recipes_reviews, avg_ratings, on='id', how='left')

# Adding the new columns from Nutrition
new_column_names = ['calories', 'total fat', 'sugar', 'sodium',
            'protein', 'saturated fat', 'carbohydrates']
values = df['nutrition']
value_list = [json.loads(value) for value in values]

for i, column_name in enumerate(new_column_names):
    df[column_name] = [row[i] for row in value_list]

df = df[['name', 'id', 'minutes', 'contributor_id', 'submitted', 'tags', 'n_steps', 
    'steps', 'description', 'ingredients', 'n_ingredients', 'date', 'rating', 
    'avg_rating', 'calories', 'total fat', 'sugar', 
    'sodium', 'protein', 'saturated fat', 'carbohydrates']]

unique_recipes_df = df.drop_duplicates(subset=['name'])
```
#### Steps
1. Merging both sets of data
2. Creating the `avg_rating` column
3. Pulling the data out of the 'nutrition' column into their own columns
4. Keeping only possibly relevant columns in the final DataFrame, and creating 
and additional DataFrame that only contains the unique recipes

### Univariate Analysis
#### Sugar Distribution
This graph removes the outliers to improve readability of the distribution as 
as before removing such outliers it's very hard to read.

Below is a print statement from the graph function including the means and medians of both 
the original distribution and the distribution without the outliers. Of note, 
the medians are about the same and the final line includes how many values were removed from the 
original dataset in the making of the graph.
```
Mean of recipes under 300 sugar: 43.05, Mean of all recipes: 68.7
Median of recipes under 300 sugar: 22.0, Median of all recipes: 23.0
Removed values from full DataFrame: 2709, Percent: 3.24%
```
<iframe
  src="assets/sugar-graph.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>


Histogram of the ratings within the data. Very skewed towards 5 star ratings. 
<iframe
  src="assets/hist-ratings-graph.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Here is the graphed difference between the 5 Star reviews and the non 5 star reviews
<iframe
  src="assets/hist-five-star-graph.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

### Bivariate Analysis
Scatter plot depicting all the average rating vs the sugar content of each unique 
recipe. There is quite a bit more 4 to 5 star rated recipes over lower ratings.
<iframe
  src="assets/scatter-rating-sugar-graph.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

### Interesting Aggregates
Pivot Table by the rating and the mean and median sugar content.

|   rating |   ('mean', 'sugar') |   ('median', 'sugar') |
|---------:|--------------------:|----------------------:|
|        1 |             88.0094 |                    28 |
|        2 |             75.1664 |                    25 |
|        3 |             65.552  |                    23 |
|        4 |             56.7858 |                    21 |
|        5 |             63.0816 |                    22 |

## Assessment of Missingness
### NMAR Analysis


### Missingness Dependency


## Hypothesis Testing
**Question**: Does sugar play a role in a recipe being highly rated?
- **Null Hypothesis**: The differences between the means of highly rated recipes (5 stars) 
and the difference between the means of the low rated recipes (1-4 stars) is Zero
- **Alternative Hypothesis**: The differences between the means of of highly rated recipes (5 stars) 
and the difference between the means of the low rated recipes (1-4 stars) is *not* Zero
<iframe
  src="assets/permutation-sugar-graph.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>
```
'p value: 0.004, Therefore reject Null Hypothesis'
```
With an alpha value of 0.01, we reject the null hypothesis and therefore we support
the alternative hypothesis and find support that the sugar does play a role
in if a recipe is rated 5 stars or not.

## Framing a Prediction Problem
The remained of this report will focus on creating a couple models in order to 
predict the ratings of the recipes. This will be done using a handful of different 
features including: sugar content, protein content,       

When first making a Model I when with a Regression, however after working on that
when deciding how to improve it I switched to a Classifier question determining 
if it would get a 5 star review or not.

## Baseline Model
To start I've created a very basic Linear Regression that inclues two features:
- Sugar
- Protein
These two have been chooses because they both tell information about the content 
of the dish of the recipe, particularly when considering the "healthiness" of the 
dish. Here is a graph depicting the 3D graph of what it will be predicting.
<iframe
  src="assets/lr-sugar-protein-graph.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

As seen in this graph the Rating axis only changes very slowly which makes sense
when we look at the coefficients and intercept
```py
lr.intercept_, lr.coef_
```
```
(np.float64(4.630782134560361), array([ 6.84636526e-06, -1.18750729e-04]))
```
Our coefficients are incredibly small, hence why the predicted ratings are minimally
changed by each feature.

Additionally, the intercept is the same as the mean! Which means that essentially,
this linear regression model is a contant model with a tiny bit of variation and 
breaks if you give it huge numbers, as the predicted values would then not be
within the expected 1 to 5 range expected when rating a recipe.

## Final Model

## Fairness Analysis