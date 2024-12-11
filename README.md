# Project Recipe Model
*Author: Hunter Flores*

> This is a project from the course DSC 80 at UC San Diego during the Fall 2024 quarter.

<!-- ## Minute Graph
Here is the code that created the graph. This graph was purely for learning more about the data.
```py
def graph_without_outliers(col, cap, sample_size=10000):
    under_cap = unique_recipes_df[unique_recipes_df[col] < cap]

    under_mean = under_cap[col].mean()
    full_mean = unique_recipes_df[col].mean()
    
    under_median = under_cap[col].median()
    full_median = unique_recipes_df[col].median()
    
    removed_value_count = unique_recipes_df.shape[0] - under_cap.shape[0]
    percent_removed = removed_value_count / unique_recipes_df.shape[0] * 100
    
    print(f'Mean of recipes under {cap} {col}: {round(under_mean, 2)},
        Mean of all recipes: {round(full_mean, 2)}')
    print(f'Median of recipes under {cap} {col}: {round(under_median, 2)},
        Median of all recipes: {round(full_median, 2)}')
    print(f'Removed values from full DataFrame: {removed_value_count},
        Percent: {round(percent_removed, 2)}%')
    
    fig = px.histogram(under_cap.sample(sample_size), x=col)
    
    fig.add_trace(go.Scatter(x=[under_mean, under_mean],
        y=[0, sample_size/10], mode='lines', name='Under Cap Mean',
        line=dict(color='red', dash='dash')))
    fig.add_trace(go.Scatter(x=[full_mean, full_mean], y=[0, sample_size/10],
        mode='lines', name='Full Mean', line=dict(color='orange', dash='dash')))
    
    fig.show()
    return fig

fig = graph_without_outliers('minutes', 300)
```
Depicted is a histogram of all the recipes that take less than $300$ minutes in order to gain a graph that is more readable and usable for humans. Included are some statistics about the dataset and graph.



DataFrame Example including 'name', 'minutes', 'rating', and 'calories'

| name                                 |   minutes |   rating |   calories |
|:-------------------------------------|----------:|---------:|-----------:|
| 1 brownies in the world    best ever |        40 |        4 |      138.4 |
| 1 in canada chocolate chip cookies   |        45 |        5 |      595.1 |
| 412 broccoli casserole               |        40 |        5 |      194.8 |
| 412 broccoli casserole               |        40 |        5 |      194.8 |
| 412 broccoli casserole               |        40 |        5 |      194.8 | -->

## Introduction


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
1. Mergeing both sets of data
2. Creating the `avg_rating` column
3. Pulling the data out of the 'nutrition' column into thier own columns
4. Keeping only possibly relevent columns in the final DataFrame, and creating 
and additional DataFrame that only contains the unique recipes

### Univariate Analysis
#### Minute Graph
This graph removes the outliers to imporve readabliltiy of the distrubution as 
as before removing such outliers it's very hard to read.

Below is a print statement from the graph function including the means and medians of both 
the original distrubtion and the distrubution without the outliers. Of note, 
the medians are the same and the final line inclues how many values were removed from the 
original dataset in the making of the graph.
```
Mean of recipes under 300 minutes: 47.31, Mean of all recipes: 115.1
Median of recipes under 300 minutes: 35.0, Median of all recipes: 35.0
Removed values from full DataFrame: 3427, Percent: 4.1%
```
<iframe
  src="assets/minute-graph.html"
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
**Question**: Does sugar play a role in a rating being highly rated?
- **Null Hypothesis**: The differences between the means of highly rated recipes (5-star) 
and the difference between the means of the low rated recipes (1-4 stars) is Zero
- **Alternative Hypothesis**: The differences between the means of of highly rated recipes (5-star) 
and the difference between the means of the low rated recipes (1-4 stars) is *not* Zero
<iframe
  src="assets/permutation-sugar-graph.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

## Framing a Prediction Problem
The remainer of this report will focus on creating a couple models in order to 
predict the ratings of the recipes. This will be done using a handful of different 
features

## Baseline Model

## Final Model

## Fairness Analysis