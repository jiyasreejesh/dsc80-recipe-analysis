# dsc80-recipe-analysis

# What Makes a Recipe Highly Rated?

**Name(s):** Harshini Kanakala and Jiya Sreejesh  

**Dataset:** Food.com Recipes and Ratings  

---

## Introduction

When choosing a recipe, users often rely heavily on ratings to decide whether something is worth making. However, it is not always clear what factors actually influence these ratings. Do more complex recipes receive better ratings, or are simpler recipes just as successful?

In this project, we analyze a dataset of recipes and user ratings from Food.com to understand what influences a recipe’s average rating. Specifically, we investigate whether recipe complexity—measured by the number of ingredients—affects how users rate recipes.

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

---

## Data Cleaning and Exploratory Data Analysis

### Data Cleaning

We merged the recipes and interactions datasets using recipe IDs. Since a rating of 0 indicates that a user did not provide a rating, we replaced all such values with missing values (`NaN`).

We then computed the **average rating per recipe**, which serves as the main variable throughout our analysis.

After cleaning:
- The dataset contains **83,782 recipes**
- The `avg_rating` column contains **2,609 missing values (~3.1%)**

These missing values correspond to recipes that have not received any ratings, which is important for our missingness analysis.

---

### Distribution of Ratings

<iframe src="assets/rating_dist.html" width="800" height="500"></iframe>

The distribution of ratings is highly concentrated between **4 and 5**, indicating that most recipes are rated positively. This creates a **left-skewed distribution**, where lower ratings are relatively rare.

This pattern suggests that user ratings may be biased toward positive experiences, which can make it harder to detect differences between groups.

---

### Ingredients vs Ratings

<iframe src="assets/ingredients_scatter.html" width="800" height="500"></iframe>

The scatter plot shows that ratings remain consistently high across all values of `n_ingredients`. There is no visible upward or downward trend, indicating **little to no correlation** between ingredient count and rating.

Even recipes with very few ingredients (e.g., 1–5) can achieve ratings near 5, while more complex recipes do not consistently outperform simpler ones. The high density of points near rating = 5 further reinforces that ratings are clustered regardless of complexity.

---

### Cooking Time vs Ratings

<iframe src="assets/time_scatter.html" width="800" height="500"></iframe>

Most recipes cluster at lower cooking times (under ~200 minutes), with very few extremely long recipes. Despite this variation in cooking time, ratings remain consistently high.

There is no clear relationship between cooking time and rating. This suggests that **longer or more time-intensive recipes do not necessarily lead to better user experiences**.

---

### Aggregate Trends

<iframe src="assets/aggregate_plot.html" width="800" height="500"></iframe>

We grouped recipes by ingredient count and computed average ratings. The results are:

- **0–5 ingredients:** 4.65 (13,719 recipes)  
- **6–10 ingredients:** 4.62 (40,373 recipes)  
- **11–15 ingredients:** 4.62 (22,103 recipes)  
- **16–20 ingredients:** 4.63 (4,333 recipes)  
- **20+ ingredients:** 4.70 (645 recipes) :contentReference[oaicite:0]{index=0}  

While recipes with **20+ ingredients** have the highest average rating (4.70), this group contains significantly fewer recipes, making it less reliable.

Overall, the differences between groups are extremely small (within ~0.08), suggesting that **ingredient count has minimal practical impact on ratings**.

---

## Assessment of Missingness

The primary missing values occur in the `avg_rating` column, representing recipes that have not received ratings.

We conducted a permutation test to determine whether missingness depends on the number of ingredients.

- **P-value = 0.001** :contentReference[oaicite:1]{index=1}  

Since the p-value is **less than 0.05**, we reject the null hypothesis.

Conclusion:  
Missingness **does depend on the number of ingredients**, meaning that recipes with different levels of complexity are not equally likely to receive ratings.

This suggests that the missingness is **not completely random (not MCAR)**. A plausible explanation is that more complex recipes may receive fewer ratings due to higher effort or lower accessibility, or that simpler recipes are more frequently attempted and rated.

It is also possible that missingness is **MNAR**, since user engagement (which is unobserved) likely influences whether a recipe receives ratings.

---

## Hypothesis Testing

We tested whether recipes with more than 10 ingredients have different rating distributions compared to simpler recipes.

- **Null Hypothesis (H₀):** The distributions are the same  
- **Alternative Hypothesis (Hₐ):** The distributions are different  
- **Test Statistic:** Kolmogorov–Smirnov (KS)

**Results:**
- **KS Statistic = 0.0057**
- **P-value = 0.5912** :contentReference[oaicite:2]{index=2}  

Since the p-value is **greater than 0.05**, we fail to reject the null hypothesis.

Conclusion:  
There is **no statistically significant difference** in rating distributions between simple and complex recipes.

This reinforces our earlier findings that **ingredient count does not meaningfully affect user ratings**.

---

## Framing a Prediction Problem

We aim to predict `avg_rating`, making this a **regression problem**.

At the time of prediction, we would know:
- `minutes`
- `n_ingredients`
- `n_steps`

We evaluate performance using **Root Mean Squared Error (RMSE)**, which penalizes large errors and is interpretable in rating units.

---

## Baseline Model

We trained a **Linear Regression model** using:
- `minutes`
- `n_ingredients`

Both features were standardized.

**Performance:**
- **Test RMSE = 0.636** :contentReference[oaicite:3]{index=3}  

This means predictions are off by about **0.64 rating points on average**, which is relatively large given that ratings range from 1 to 5.

---

## Final Model

We improved the model by adding:
- `n_steps`
- `steps_per_minute` (captures recipe complexity)

We also used **GridSearchCV** to tune hyperparameters.

**Performance:**
- **Final RMSE = 0.631** :contentReference[oaicite:4]{index=4}  
- Improvement ≈ **0.005**

While the improvement is small, it shows that incorporating additional structural features provides **marginal gains**.

This limited improvement suggests that ratings are inherently difficult to predict due to their **subjective nature**.

---

## Fairness Analysis

We evaluated model performance across:
- **Simple recipes (≤10 ingredients)**
- **Complex recipes (>10 ingredients)**

**Results:**
- RMSE (simple) = **0.638**  
- RMSE (complex) = **0.617**  
- **P-value = 0.562** :contentReference[oaicite:5]{index=5}  

Since the p-value is **greater than 0.05**, we fail to reject the null hypothesis.

Conclusion:  
There is **no significant difference in model performance**, indicating that the model is **fair across recipe complexity groups**.

---

## Conclusion

Our analysis shows that recipe ratings are **not strongly influenced by simple measures of complexity**, such as ingredient count or cooking time.

Although recipes with more ingredients may appear slightly higher rated in aggregate, these differences are extremely small and not statistically significant. The hypothesis test further confirms that rating distributions are nearly identical across groups.

From a modeling perspective, predicting ratings remains challenging. Even with additional features, improvements in RMSE are minimal, suggesting that ratings are driven by factors not captured in the dataset—such as taste, presentation, or individual user preferences.

Overall, this project highlights that while recipe complexity may influence user perception slightly, it is **not a primary driver of ratings**, and more nuanced factors likely play a larger role.
