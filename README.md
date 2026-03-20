# dsc80-recipe-analysis

# What Makes a Recipe Highly Rated?

**Name(s):** Harshini Kanakala and Jiya Sreejesh
**Dataset:** Food.com Recipes and Ratings  

---

## Introduction

When choosing a recipe, people often rely on ratings to decide whether something is worth making. However, it is not always clear what factors actually influence these ratings. Do more complex recipes receive better ratings, or are simpler recipes just as successful?

In this project, we analyze a dataset of recipes and user ratings from Food.com to understand what influences a recipe’s average rating.

Our central question is:
**Do recipes with more ingredients receive different ratings than simpler recipes?**

The dataset contains:
- **83,782 recipes**
- **731,927 user interactions**

We focus on the following key features:
- `minutes`: preparation time  
- `n_ingredients`: number of ingredients  
- `n_steps`: number of steps  
- `avg_rating`: average user rating per recipe  

Understanding these relationships can help users choose recipes more effectively and provide insight into what makes recipes successful.

---

## Data Cleaning and Exploratory Data Analysis

### Data Cleaning

To prepare the dataset, we merged the recipes and interactions datasets using recipe IDs. Since a rating of 0 indicates that a user did not provide a rating, we replaced all ratings of 0 with missing values (`NaN`).

We then computed the **average rating per recipe**, which serves as the main variable for our analysis.

After cleaning:
- The dataset contains **83,782 recipes**
- The `avg_rating` column contains **2,609 missing values**, corresponding to recipes with no ratings

These missing values are important for our later missingness analysis.

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

We grouped recipes by ingredient count to analyze trends in ratings.

Recipes with a moderate number of ingredients tend to have slightly higher average ratings. However, recipes with very few or very many ingredients show greater variability in ratings.

This suggests that while moderate complexity may be slightly preferred, complexity alone is not a strong driver of ratings.
<iframe src="assets/aggregate_plot.html" width="800" height="500" frameborder="0"></iframe>

---

## Assessment of Missingness

The primary missing values occur in the `avg_rating` column, representing recipes that have not received any ratings.

We performed a permutation test to determine whether missingness depends on the number of ingredients.

The results show that **missingness does not depend on the number of ingredients**, indicating that recipes are equally likely to be unrated regardless of their complexity.

However, it is plausible that this data is **Missing Not At Random (MNAR)**. Recipes that receive less visibility or engagement may be less likely to receive ratings. Additional data, such as how often a recipe is viewed or recommended, would help better explain this missingness.

<iframe src="assets/missingness_plot.html" width="800" height="500" frameborder="0"></iframe>

---

## Hypothesis Testing

We tested whether recipes with more than 10 ingredients have different rating distributions compared to simpler recipes.

- **Null Hypothesis (H₀):** The distributions of ratings are the same  
- **Alternative Hypothesis (Hₐ):** The distributions are different  

We used the **Kolmogorov–Smirnov (KS) test**.

The results show that we **fail to reject the null hypothesis**, indicating that there is no statistically significant difference in rating distributions between simple and complex recipes.

This suggests that ingredient count alone does not meaningfully impact how users rate recipes.

---

## Framing a Prediction Problem

We aim to predict the **average rating of a recipe**, making this a **regression problem**.

At the time of prediction, we would have access to:
- preparation time (`minutes`)
- number of ingredients (`n_ingredients`)
- number of steps (`n_steps`)

We evaluate our models using **Root Mean Squared Error (RMSE)**, since it penalizes large errors and is interpretable in the same units as the response variable.
---

## Baseline Model

Our baseline model uses:
- `minutes`
- `n_ingredients`

Both features are quantitative and were standardized before training a **Linear Regression model**.

This model provides a simple starting point for predicting ratings. While it captures basic relationships in the data, it does not fully account for recipe complexity or structure.
---

## Final Model

We improved our model by adding:
- `n_steps`
- `steps_per_minute` (a feature capturing recipe complexity)

These features provide additional information about how detailed and time-intensive a recipe is.

We also used **GridSearchCV** to tune model parameters, improving performance over the baseline model.

The final model achieved a lower RMSE, indicating improved predictive accuracy. This suggests that incorporating additional structural features of recipes helps better estimate ratings.
---

## Fairness Analysis

We evaluated whether the model performs differently for:
- **simple recipes** (≤10 ingredients)
- **complex recipes** (>10 ingredients)

We compared RMSE across these groups and conducted a permutation test.

The results show **no significant difference in model performance**, suggesting that the model is fair across these groups.

This indicates that the model does not systematically favor or disadvantage recipes based on their complexity.

---

## Conclusion

Overall, recipe ratings are not strongly influenced by simple measures of complexity such as ingredient count or cooking time. While moderate complexity may slightly improve ratings, the effect is not statistically significant.

This suggests that other factors — such as taste, presentation, or user preferences — likely play a larger role in determining how recipes are rated.

From a modeling perspective, incorporating additional structural features improves predictive performance, but predicting ratings remains challenging due to the subjective nature of user feedback.
