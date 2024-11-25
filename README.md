# Project Recipe Model
*Author: Hunter Flores*

> This is a project from the course DSC 80 at UC San Diego during the Fall 2024 quarter.

## Minute Graph
Here is the code that created the graph
```py
def graph_without_outliers(col, cap, sample_size=10000):
    under_cap = unique_recipes_df[unique_recipes_df[col] < cap]

    under_mean = under_cap[col].mean()
    full_mean = unique_recipes_df[col].mean()
    
    under_median = under_cap[col].median()
    full_median = unique_recipes_df[col].median()
    
    removed_value_count = unique_recipes_df.shape[0] - under_cap.shape[0]
    percent_removed = removed_value_count / unique_recipes_df.shape[0] * 100
    
    print(f'Mean of recipes under {cap} {col}: {round(under_mean, 2)}, Mean of all recipes: {round(full_mean, 2)}')
    print(f'Median of recipes under {cap} {col}: {round(under_median, 2)}, Median of all recipes: {round(full_median, 2)}')
    print(f'Removed values from full DataFrame: {removed_value_count}, Percent: {round(percent_removed, 2)}%')
    
    fig = px.histogram(under_cap.sample(sample_size), x=col)
    
    fig.add_trace(go.Scatter(x=[under_mean, under_mean], y=[0, sample_size/10], mode='lines', name='Under Cap Mean', line=dict(color='red', dash='dash')))
    fig.add_trace(go.Scatter(x=[full_mean, full_mean], y=[0, sample_size/10], mode='lines', name='Full Mean', line=dict(color='orange', dash='dash')))
    
    fig.show()
    return fig

    fig = graph_without_outliers('minutes', 300)
```
Depicted is a histogram of all the recipes that take less than $300$ minutes in order to gain a graph that is more readable and usable for humans. Included are some statistics about the dataset and graph.

```
Mean of recipes under 300 minutes: 47.31, Mean of all recipes: 115.1
Median of recipes under 300 minutes: 35.0, Median of all recipes: 35.0
Removed values from full DataFrame: 3427, Percent: 4.1%
```
<iframe
  src="assets/test-graph.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>
