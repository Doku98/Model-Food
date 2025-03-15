# Investigation of Recipe Ratings and Features

**Author**: Christian Kumagai  

---

## Overview

Cooking is an essential part of daily life, yet consistently preparing delicious dishes can be challenging. According to a survey by the New York Post, the average American spends over 37 minutes daily cooking, highlighting significant effort and investment in meal preparation. Given this, I aim to uncover which recipe attributes most strongly influence user ratings by analyzing two large datasets of recipes and ratings from Food.com.

## Introduction

We love good food, but creating consistently delicious dishes is challenging. According to a recent survey by the New York Post, Americans spend over 37 minutes each day cooking, showcasing the considerable effort involved in daily meal preparation. Recognizing this investment of time and effort, I aim to discover which specific recipe attributes significantly influence user ratings.  

This project utilizes two extensive datasets from Food.com to explore this relationship:

### Recipes Dataset (83,782 rows)

| Column            | Description                                                    |
|-------------------|-----------------------------------------------------------------|
| `name`            | Recipe name                                                     |
| `id`              | Unique recipe identifier                                         |
| `minutes`         | Minutes required to prepare the recipe                           |
| `contributor_id`  | ID of the user who submitted the recipe            |
| `submitted`     | Date the recipe was submitted                                   |
| `tags`                | Tags associated with the recipe                             |
| `nutrition`        | Nutritional information [calories, fat, sugar, sodium, protein, etc.]  |
| `n_steps`       | Number of steps in the recipe                               |
| `steps`             | Instructions for preparing the recipe                       |
| `description`    | User-provided description                                  |
| `ingredients`     | Ingredients used in the recipe                                  |
| `n_ingredients` | Total number of ingredients                                       |

### Interactions Dataset (731,927 rows)

| Column | Description |
|--------|-------------|
| `user_id` | ID of user who left review |
| `recipe_id` | ID of the reviewed recipe |
| `date` | Date review was made |
| `rating` | User-given rating (1-5) |
| `review` | Textual review |

Through this analysis, I investigated the effects of the following features on recipe ratings:

- Number of preparation steps (`n_steps`)
- Cooking duration (`minutes`)
- Ingredient density (`ingredient_density`)
- Fat-to-sugar ratio (`fat_sugar_balance`)
- Filling factor (`filling_factor`)

Insights from this analysis could help recipe creators optimize their dishes to better meet user preferences, ultimately saving valuable cooking time while maximizing satisfaction.

## Step 2: Data Cleaning and Exploratory Data Analysis

### Data Cleaning

The analysis began by merging the `recipes` and `interactions` datasets based on unique recipe IDs, linking each recipe with its corresponding ratings and reviews. The following cleaning steps were applied:

- **Merging Datasets**:  
  A left merge was performed between the recipes dataset and the interactions dataset using recipe_id as the key. This ensured that all recipes were retained, even if they had no associated ratings or reviews.

- **Handling Missing Ratings**:  
  Ratings initially set as `0` were replaced with `NaN`, as valid ratings range from 1 to 5. A `0` indicates missing data.

- **Average Rating Calculation**:  
  An `avg_rating` column was created to summarize user satisfaction for each recipe.

- **Nutrition Data Extraction**:  
  The `nutrition` column was split into separate numeric columns (e.g., calories, total fat, sugar, protein, saturated fat, carbohydrates) for detailed nutritional analysis.

- **Datetime Conversion**:  
  Converted `submitted` and `date` from strings to datetime objects, enabling time-based trend analysis.

- **Outlier Removal**:  
  Recipes with preparation times over 500 minutes or more than 50 steps were removed to avoid skewed analyses.

- **Feature Engineering**:  
  - **`filling_factor`**: Computed using protein, carbohydrates, and sugar relative to calories, estimating recipe satiety.  
  - **`fat_sugar_balance`**: Calculated as the ratio of total fat to sugar, indicating nutritional balance.  
  - **`ingredient_density`**: Defined as the number of ingredients divided by preparation time, reflecting recipe complexity.  
  - **`rating_bin`**: Recipes were categorized into bins (`0-1`, `1-2`, `2-3`, `3-4`, `4-5`) based on `avg_rating` for streamlined analysis.

**Cleaned Data Preview:** (230,853 rows)
Note: This dataset preview has been split into two tables for better readability. However, all data belongs to a single table in the cleaned dataset. The first section contains general recipe information, while the second section includes ratings and engineered features.

| name                         | minutes | contributor_id | submitted  | tags        | n_steps | steps |
|------------------------------|---------|---------------|------------|------------|---------|-------|
| World's Best Brownies        | 40      | 985201        | 2008-10-27 | [...]      | 10      | [...] |
| Chocolate Chip Cookies       | 45      | 1848091       | 2011-04-11 | [...]      | 12      | [...] |
| Easy Broccoli Casserole      | 40      | 50969         | 2008-05-30 | [...]      | 6       | [...] |

| avg_rating | calories | total_fat | sugar | protein | saturated_fat | carbohydrates | filling_factor | fat_sugar_balance | ingredient_density |
|------------|----------|-----------|-------|---------|---------------|--------------|----------------|-------------------|--------------------|
| 4.0        | 138.4    | 10.0      | 50.0  | 3.0     | 3.0           | 19.0         | 0.196078       | 1.0               | 0.219512          |
| 5.0        | 595.1    | 46.0      | 211.0 | 22.0    | 13.0          | 51.0         | 0.216981       | 1.0               | 0.239130          |
| 5.0        | 194.8    | 20.0      | 6.0   | 32.0    | 22.0          | 36.0         | 2.857143       | 1.0               | 0.219512          |

### Univariate Analysis

#### Distribution of Recipe Preparation Time

<iframe
  src="assets/avg_rating_histagram.html"
  width="800"
  height="600"
  frameborder="0">
</iframe>


The histogram shows that most recipes have fewer than 15 steps, with a steep drop-off beyond this point. This suggests that users and contributors prefer simpler recipes with fewer steps, which may be more convenient for cooking.

---

### Bivariate Analysis

#### Relationship Between Number of Steps and Recipe Ratings

<iframe
  src="assets/n_step_histagram.html"
  width="800"
  height="600"
  frameborder="0">
</iframe>

The distribution of average ratings is skewed toward higher values, with the majority of recipes receiving ratings close to 5. This indicates a positive bias in user ratings, suggesting that users are generally satisfied with the recipes they review.

---

### Interesting Aggregates

#### Average Number Of Steps vs. Average Rating
<iframe
  src="assets/n_steps_on_rating.html"
  width="800"
  height="600"
  frameborder="0">
</iframe>
This graph shows the relationship between the number of steps in a recipe and its average rating. While some variations exist, there is no clear trend indicating that recipes with more or fewer steps receive consistently higher ratings. The lack of a strong correlation suggests that step count alone may not be a key determinant of user preferences, and more complex features might be needed for a predictive model.
<iframe
  src="assets/n_step_binnded_rating_avg.html"
  width="800"
  height="600"
  frameborder="0">
</iframe>
The box plot categorizes recipes based on binned rating groups and compares their number of steps. The distribution across bins appears relatively uniform, with no significant shift indicating that highly-rated recipes have a distinct number of steps. This further supports the notion that step count alone is not a major factor in determining recipe ratings.

#### Average Cooking Time vs. Average Rating
<iframe
  src="assets/cooking_time_on_rating.html"
  width="800"
  height="600"
  frameborder="0">
</iframe>
This plot examines whether longer cooking times are associated with higher or lower ratings. The data is highly scattered, and no clear trend emerges, meaning that cooking time does not have a strong influence on ratings. This suggests that additional features, such as ingredient composition or preparation complexity, may be more relevant for predicting user satisfaction.
<iframe
  src="assets/minutes_binnded_rating_avg.html"
  width="800"
  height="600"
  frameborder="0">
</iframe>
Similar to the steps-based box plot, this visualization groups recipes by binned ratings and compares their cooking times. The median and interquartile ranges remain relatively stable across all bins, reinforcing the idea that cooking duration does not significantly impact how a recipe is rated. More nuanced features will likely be needed for an effective predictive model.



---

## Step 3: Assessment of Missingness

### NMAR Analysis
Based on the data and its generating process, the missingness in 'description' and 'review' is likely NMAR, meaning that the reason for missing values is inherent to the unobserved factors related to the missing values themselves rather than the observed data.

#### Missingness in 'description' (Likely NMAR)
The 'description' column is likely NMAR because the decision to include a description may depend on the complexity of the recipe itself. More complex recipes with many steps may require a description to clarify important details, while very simple recipes (e.g., a two-ingredient dish) may not need one at all. If this is the case, the missingness is related to an intrinsic factor (recipe complexity) rather than an observed variable in the dataset.

To determine if the missingness could instead be MAR, additional data on user engagement and recipe complexity could be useful. For instance, if users who submit longer recipes (in terms of 'n_steps' or 'minutes') are more likely to provide a description, then missingness in 'description' could be explained through an observed variable and would be MAR instead of NMAR.

#### Permutation Test: Missingness in 'description' vs. 'n_ingredients'
<iframe
  src="assets/n_desc_n_ingred.html"
  width="800"
  height="600"
  frameborder="0">
</iframe>
The test examines whether missing values in the 'description' column are dependent on the number of ingredients in a recipe. The observed difference of -1.08 suggests that recipes missing a description tend to have fewer ingredients on average. The low p-value (0.001) provides strong evidence against the null hypothesis, indicating that the missingness in 'description' is not completely random and is likely Missing At Random (MAR) with respect to 'n_ingredients'

#### Missingness in 'review' (Likely NMAR)
The 'review' column is also likely NMAR, as users may be selectively choosing whether to leave a review based on their experience with the recipe. If users are only motivated to leave a review when they feel strongly (either positive or negative) about a recipe, while those with neutral opinions remain silent, then missingness in 'review' is dependent on an unobserved factor—personal sentiment—making it NMAR.

However, to determine if the missingness is MAR, further information about user behavior would be necessary. For example, if missing reviews are more common among users who provide fewer total ratings, then missingness could be related to an observed user engagement pattern. Additionally, the amount of carbohydrates in a recipe could influence whether a user leaves a review, as people who follow certain diets (e.g., low-carb or high-carb diets) may be more inclined to leave feedback based on their dietary preferences.

<iframe
  src="assets/reivew_carbs.html"
  width="800"
  height="600"
  frameborder="0">
</iframe>
#### Permutation Test: Missingness in 'review' vs. 'carbohydrates'
This test investigates whether missing values in the 'review' column are related to the carbohydrate content of recipes. The observed difference of 6.89 shows a slightly higher mean carbohydrate value for recipes with missing reviews. However, the high p-value (0.412) suggests that this difference is not statistically significant, meaning that missingness in 'review' is likely independent of carbohydrate content and could be considered Missing Completely At Random (MCAR).


---

## Step 4: Hypothesis Testing

The primary hypothesis examined whether the number of preparation steps affects recipe ratings.

- **Null Hypothesis**: The number of steps (`n_steps`) does not influence the average rating of a recipe.
- **Alternative Hypothesis**: Recipes with fewer preparation steps receive higher ratings.

**Test Statistic**: The difference in mean ratings between recipes with fewer and more than the average number of steps. This statistic was chosen because it effectively captures user preferences for simpler recipes.
**Significance Level**: 0.05


<iframe
  src="assets/n_desc_n_ingred.html"
  width="800"
  height="600"
  frameborder="0">
</iframe>
The permutation test yielded an observed statistic of -0.0097 and a p-value of 0.001, leading to rejection of the null hypothesis. This suggests that recipes with fewer steps may be slightly preferred by users and tend to receive higher ratings. However, the observed effect size is small, indicating that while preparation complexity may have some influence, other factors likely play a larger role in determining recipe popularity.


---

## Step 5: Framing a Prediction Problem

From the previous analysis, we explored various factors that may influence recipe ratings. However, rather than just identifying relationships, can we build a model that **predicts** a recipe’s rating based on its attributes? If successful, such a model could help chefs, food bloggers, and recipe developers optimize their recipes to align with user preferences.

To address this, we frame the problem as a **regression task**, aiming to predict a recipe’s **average rating** (`avg_rating`) using key recipe characteristics. This predictive approach allows us to determine whether specific features, such as preparation time or ingredient complexity, have a measurable impact on how well a recipe is received.

### Prediction Problem Statement
- **Type of Prediction:** **Regression**  
- **Target Variable:** `avg_rating` (Average user rating of a recipe)  
- **Features Used for Prediction:**
  - `n_steps` (Number of preparation steps)
  - `minutes` (Total preparation time)
  - `filling_factor` (Estimate of how filling a recipe is)
  - `fat_sugar_balance` (Ratio of fat to sugar)
  - `ingredient_density` (Complexity measure based on number of ingredients and time)

### Evaluation Metric
Since this is a regression problem, an appropriate metric is needed to assess model performance:

- **Chosen Metric:** **Root Mean Squared Error (RMSE)**
- **Justification:** RMSE is preferred as it penalizes larger errors more than Mean Absolute Error (MAE), making it effective for capturing deviations from actual ratings.

### Considerations for Prediction Time
At the time of prediction, all selected features are **available before a user rates a recipe**, making them suitable for training the model. No future-dependent data, such as user reviews, is included to avoid data leakage.

By developing this predictive model, we aim to gain deeper insights into the characteristics of highly-rated recipes and assess whether these attributes can meaningfully forecast user preferences.

---

## Step 6: Baseline Model

To establish a baseline for predicting recipe ratings, a **Linear Regression** model was trained using two fundamental features: **number of steps (`n_steps`)** and **preparation time (`minutes`)**. These features were chosen based on their potential influence on recipe complexity and user satisfaction.

### Features Used:
- **`n_steps` (Quantitative)**: Represents the total number of steps in a recipe.
- **`minutes` (Quantitative)**: Represents the total time required to prepare a recipe.

Since both features are continuous numerical variables, **no categorical encoding was necessary**. However, these features may exist on different scales, so **StandardScaler** was applied to normalize them before feeding them into the model.

### Model Implementation:
A **Linear Regression** model was implemented within a **scikit-learn pipeline**, which included:
1. **StandardScaler** – Standardizes the features to ensure consistent scaling.
2. **Linear Regression** – A simple model to establish a baseline prediction.

The dataset was split into training and testing sets (**80%-20% split**), and the model was trained using the training data.

### Baseline Model Performance:
- **Evaluation Metric:** Root Mean Squared Error (RMSE)
- **Baseline RMSE:** **0.8508**

### Analysis of Performance:
The RMSE value of **0.8508** suggests the average deviation of predicted ratings from actual user ratings. Given that recipe ratings typically range from 1 to 5, this initial model likely has **room for improvement**. The linear regression model may struggle to capture **non-linear relationships** in the data, and additional engineered features could enhance predictive performance.

In the next step, we will improve upon this baseline by incorporating more features and optimizing the model through feature engineering and hyperparameter tuning.


---

## Step 7: Final Model

From the baseline model, we identified that `n_steps` and `minutes` had limited predictive power on their own. To improve our predictions, we incorporated additional engineered features and used **Gradient Boosting Regression**, an ensemble learning technique that builds decision trees sequentially, where each tree corrects the errors of the previous ones. This method is well-suited for handling **complex, non-linear relationships** in data.

### Prediction Problem:
Our goal is to predict the **average rating** (`avg_rating`) of a recipe based on its **preparation steps, cooking time, and engineered features**.

### Features Used:
The final model expands on the baseline by including three additional engineered features:
- **`n_steps` (Quantitative)**: Number of steps in a recipe.
- **`minutes` (Quantitative)**: Total time required to prepare the recipe.
- **`fat_sugar_balance` (Quantitative)**: Ratio of total fat to sugar, reflecting nutritional composition.
- **`ingredient_density` (Quantitative)**: Number of ingredients divided by preparation time, indicating recipe complexity.
- **`filling_factor` (Quantitative)**: A derived metric estimating how filling a recipe might be based on macronutrient composition.

### Model Selection & Hyperparameter Tuning:
To optimize model performance, **RandomizedSearchCV** was used to fine-tune key hyperparameters for **Gradient Boosting Regression**:
- **`learning_rate`**: Controls the contribution of each tree (tested: 0.01, 0.1, 0.2).
- **`max_depth`**: Determines tree depth (tested: 3, 5, 10, 15).
- **`min_samples_split`**: Minimum samples needed to split a node (tested: 2, 5, 10).
- **`min_samples_leaf`**: Minimum samples required in a leaf node (tested: 1, 2, 5).

A **randomized search with 5 iterations and 3-fold cross-validation** was conducted to efficiently explore the parameter space.


### Model Performance:
- **Evaluation Metric:** Root Mean Squared Error (RMSE)
- **Baseline Model RMSE:** `0.8508`  
- **Final Model RMSE:** **`0.7783`** (Lower is better)

### Comparison with Baseline Model:
<table>
  <tr>
    <th>Model</th>
    <th>Features Used</th>
    <th>RMSE</th>
  </tr>
  <tr>
    <td><strong>Baseline</strong></td>
    <td>n_steps, minutes</td>
    <td>0.8508</td>
  </tr>
  <tr>
    <td><strong>Final Model</strong></td>
    <td>n_steps, minutes, fat_sugar_balance, ingredient_density, filling_factor</td>
    <td><strong>0.7783</strong></td>
  </tr>
</table>



### Conclusion:
The **Gradient Boosting model significantly outperformed the baseline Linear Regression model**, showing that incorporating **feature engineering and advanced modeling techniques** improved predictive accuracy. The decrease in RMSE suggests that **recipe complexity, ingredient balance, and filling factor contribute meaningful predictive value**.

---

## Fairness Analysis

To assess whether our model exhibits fairness across different rating groups, we conducted a permutation test comparing the residual errors of our final model across various rating bins.

### Choice of Groups and Evaluation Metric
- **Group X**: Recipes with lower ratings (e.g., 0-1, 1-2, 2-3)
- **Group Y**: Recipes with higher ratings (e.g., 3-4, 4-5)
- **Evaluation Metric**: Total Variation Distance (TVD) of residuals

### Hypotheses
- **Null Hypothesis**: The distribution of residuals is independent of the rating bin, meaning the model performs equally well across all rating groups.
- **Alternative Hypothesis**: The residuals differ significantly across rating bins, suggesting that the model is biased toward predicting better for some rating groups.

### Test Statistic and Significance Level
- **Test Statistic**: The observed TVD of residuals across rating bins.
- **Significance Level**: 0.05

### Results
- **Observed TVD**: 1.7112
- **P-value**: 0.0

Since the p-value is extremely low (close to 0), we reject the null hypothesis. This indicates that our model does not perform equally well across all rating groups, suggesting a fairness issue. The model tends to make larger errors in predicting lower-rated recipes compared to higher-rated ones.

### Visualization
The following histogram displays the empirical distribution of the test statistic under the null hypothesis, with the observed TVD marked in red.

<iframe src="assets/fairness_permutation_test.html" width="800" height="400" frameborder="0"></iframe>

