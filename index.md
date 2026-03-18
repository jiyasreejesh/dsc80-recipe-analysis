
# dsc80-recipe-analysis

# What Makes a Recipe Highly Rated?

**Name(s):** Harshini Kanakala and Jiya Sreejesh
**Dataset:** Food.com Recipes and Ratings  

---

## Introduction

When choosing a recipe, people often rely on ratings to decide whether something is worth making. But what actually influences these ratings? Are more complex recipes better, or do simpler ones perform just as well?

In this project, we analyze a dataset of recipes and user ratings from Food.com to understand what factors influence a recipe’s average rating.

Our central question is:  
**Do recipes with more ingredients receive different ratings than simpler recipes?**

The dataset contains:
- 83,782 recipes  
- 731,927 user interactions  

We focus on key features such as:
- `minutes`: preparation time  
- `n_ingredients`: number of ingredients  
- `n_steps`: number of steps  
- `avg_rating`: average user rating  

---

## Data Cleaning and Exploratory Data Analysis

To prepare the data, we merged the recipes and interactions datasets using recipe IDs. We replaced ratings of 0 with missing values, since these represent users who did not provide a rating.

We then computed the **average rating per recipe**, which is the main variable used throughout our analysis.

### Distribution of Ratings

<iframe src="assets/rating_dist.html" width="800" height="500" frameborder="0"></iframe>

Most recipes have high ratings, with values concentrated between 4 and 5. This suggests that users generally rate recipes positively.

---

### Ingredients vs Ratings

<iframe src="assets/ingredients_scatter.html" width="800" height="500" frameborder="0"></iframe>

There is no strong relationship between the number of ingredients and rating. Recipes with both few and many ingredients receive similar ratings.

---

### Cooking Time vs Ratings

<iframe src="assets/time_scatter.html" width="800" height="500" frameborder="0"></iframe>

Cooking time also shows a weak relationship with ratings. Most recipes are shorter in duration, but longer recipes are not necessarily rated higher.

---

### Aggregate Trends

<iframe src="assets/aggregate_plot.html" width="800" height="500" frameborder="0"></iframe>

Recipes with a moderate number of ingredients tend to have slightly higher ratings, while very simple or very complex recipes show more variability.

---

## Assessment of Missingness

The primary missing values in our dataset occur in the `avg_rating` column. These represent recipes that have not received ratings.

We tested whether missingness depends on the number of ingredients using a permutation test.

<iframe src="assets/missingness_plot.html" width="800" height="500" frameborder="0"></iframe>

The results suggest that missingness **does not depend on the number of ingredients**, meaning ratings are missing independently of recipe complexity.

It is also possible that this data is **Missing Not At Random (MNAR)**. Recipes that receive little attention or engagement may be less likely to be rated.

---

## Hypothesis Testing

We tested whether recipes with more than 10 ingredients have different rating distributions compared to simpler recipes.

- **Null Hypothesis:** The distributions are the same  
- **Alternative Hypothesis:** The distributions are different  

We used the Kolmogorov–Smirnov (KS) test.

The results indicate that we **fail to reject the null hypothesis**, suggesting no significant difference in rating distributions.

---

## Framing a Prediction Problem

We aim to **predict the average rating of a recipe**, which is a regression problem.

At the time of prediction, we would know:
- preparation time  
- number of ingredients  
- number of steps  

We evaluate our models using **Root Mean Squared Error (RMSE)**.

---

## Baseline Model

Our baseline model uses:
- `minutes`
- `n_ingredients`

We trained a Linear Regression model with standardized features.

This model provides a simple starting point for predicting ratings.

---

## Final Model

We improved the model by adding new features:
- `n_steps`
- `steps_per_minute` (captures recipe complexity)

We also used **GridSearchCV** to tune hyperparameters.

The final model achieved a lower RMSE, indicating improved predictive performance.

---

## Fairness Analysis

We evaluated whether the model performs differently for:
- simple recipes (≤10 ingredients)
- complex recipes (>10 ingredients)

We compared RMSE across groups and conducted a permutation test.

The results show **no significant difference in performance**, suggesting that the model is fair across these groups.

---

## Conclusion

Overall, recipe ratings are not strongly influenced by simple measures of complexity like ingredients or cooking time. While moderate complexity may slightly improve ratings, the effect is not statistically significant.

This suggests that other factors — such as taste, presentation, or user preferences — likely play a larger role in determining how recipes are rated.
