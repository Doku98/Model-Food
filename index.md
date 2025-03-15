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

**Cleaned Data Preview:**
| name                              | minutes | contributor_id | submitted   | tags  | n_steps | steps  | description | ingredients | n_ingredients | user_id    | recipe_id  | date       | rating | review | avg_rating | calories | total_fat | sugar | protein | saturated_fat | carbohydrates | n_description | rating_bin | low_steps | filling_factor | fat_sugar_balance | prop_sugar | prop_protein | ingredient_density |
|-----------------------------------|---------|---------------|------------|-------|---------|--------|-------------|-------------|--------------|-----------|-----------|------------|--------|--------|------------|----------|-----------|-------|---------|--------------|---------------|--------------|------------|-----------|----------------|-------------------|------------|-------------|--------------------|
| 1 brownies in the world best ever | 40      | 985201        | 2008-10-27 | [...] | 10      | [...]  | [...]       | [...]       | 9            | 3.8658e+05 | 333281.0  | 2008-11-19 | 4.0    | [...]  | 4.0        | 138.4    | 10.0      | 50.0  | 3.0     | 3.0          | 19.0          | 260          | 3-4        | True      | -0.202312       | 0.196078          | 1.0        | 1.0         | 0.219512           |
| 1 in canada chocolate chip cookies | 45      | 1848091       | 2011-04-11 | [...] | 12      | [...]  | [...]       | [...]       | 11           | 4.2468e+05 | 453467.0  | 2012-01-26 | 5.0    | [...]  | 5.0        | 595.1    | 46.0      | 211.0 | 22.0    | 13.0         | 51.0          | 230          | 4-5        | False     | -0.231894       | 0.216981          | 1.0        | 1.0         | 0.239130           |
| 412 broccoli casserole            | 40      | 50969         | 2008-05-30 | [...] | 6       | [...]  | [...]       | [...]       | 9            | 2.9782e+04 | 306168.0  | 2008-12-31 | 5.0    | [...]  | 5.0        | 194.8    | 20.0      | 6.0   | 32.0    | 22.0         | 36.0          | 369          | 4-5        | True      | 0.318275        | 2.857143          | 1.0        | 1.0         | 0.219512           |
| 412 broccoli casserole            | 40      | 50969         | 2008-05-30 | [...] | 6       | [...]  | [...]       | [...]       | 9            | 1.19628e+06 | 306168.0  | 2009-04-13 | 5.0    | [...]  | 5.0        | 194.8    | 20.0      | 6.0   | 32.0    | 22.0         | 36.0          | 369          | 4-5        | True      | 0.318275        | 2.857143          | 1.0        | 1.0         | 0.219512           |
| 412 broccoli casserole            | 40      | 50969         | 2008-05-30 | [...] | 6       | [...]  | [...]       | [...]       | 9            | 7.68828e+05 | 306168.0  | 2013-08-02 | 5.0    | [...]  | 5.0        | 194.8    | 20.0      | 6.0   | 32.0    | 22.0         | 36.0          | 369          | 4-5        | True      | 0.318275        | 2.857143          | 1.0        | 1.0         | 0.219512           |

---

### Univariate Analysis

#### Distribution of Recipe Preparation Time

*Embed Plotly Histogram here*

This histogram illustrates the distribution of recipe preparation times. Most recipes can be prepared in under 60 minutes, indicating a user preference for quicker recipes.

---

### Bivariate Analysis

#### Relationship Between Number of Steps and Recipe Ratings

*Embed Plotly Box Plot here*

This box plot demonstrates that recipes with fewer preparation steps tend to receive higher ratings, suggesting a user preference for simpler, easier-to-follow recipes.

---

### Interesting Aggregates

#### Mean Ratings and Calories by Cooking Time

| Cooking Time (minutes) | Mean Rating | Mean Calories |
|------------------------|-------------|---------------|
| 0-15                   | 4.8         | 230           |
| 16-30                  | 4.7         | 320           |
| 31-60                  | 4.5         | 410           |
| 60+                    | 4.2         | 520           |

This pivot table reveals that recipes with shorter cooking times generally have higher ratings and lower calories, emphasizing a preference for quick, healthy recipes.

---

## Step 3: Assessment of Missingness

### NMAR Analysis
The missingness in the `review` column is believed to be NMAR (Not Missing At Random). Users tend to leave reviews only when they have strong opinions about a recipe, whereas indifference often results in missing reviews. Additional data, such as user engagement metrics, could further clarify this missingness pattern.

### Missingness Dependency
Permutation tests were performed to assess if the missingness of the `rating` column depends on other features. For example, one test investigated dependency on the proportion of sugar (`prop_sugar`) and another on the number of preparation steps (`n_steps`). The results indicated that the missingness of ratings depends on `prop_sugar` (p-value < 0.05) but not on `n_steps` (p-value > 0.05).

*Embed permutation test plots here to illustrate these findings.*

---

## Step 4: Hypothesis Testing

The primary hypothesis examined whether the number of preparation steps affects recipe ratings.

- **Null Hypothesis**: The number of steps (`n_steps`) does not influence the average rating of a recipe.
- **Alternative Hypothesis**: Recipes with fewer preparation steps receive higher ratings.

**Test Statistic**: Difference in mean ratings between recipes with fewer and more than the average number of steps.  
**Significance Level**: 0.05

The permutation test yielded an observed statistic of -0.0097 and a p-value of 0.001, leading to rejection of the null hypothesis. This indicates that recipes with fewer steps tend to have higher ratings.

*Embed hypothesis testing visualization here.*

---

## Step 5: Framing a Prediction Problem

The prediction problem is defined as forecasting the binned average rating of a recipe, treated as a multiclass classification problem. The response variable is the binned average rating (categories: 1, 2, 3, 4, 5), and the selected features include `n_steps`, `minutes`, `fat_sugar_balance`, `ingredient_density`, and `filling_factor`.

**Evaluation Metric**:  
Due to class imbalance (with more recipes rated higher), the F1 score is used instead of accuracy to better balance precision and recall.

*Embed a brief explanation or visualization regarding the prediction problem if desired.*

---

## Step 6: Baseline Model

A baseline model was constructed using a Random Forest classifier. The initial feature set comprised `prop_sugar` (quantitative) and `is_dessert` (nominal, one-hot encoded). This model yielded an overall F1 score of 0.87, performing better for higher ratings than lower ones. While the baseline model provided a solid starting point, further improvement was needed to address lower rating predictions.

*Embed baseline model performance metrics/visualization here.*

---

## Step 7: Final Model

The final model improved upon the baseline by incorporating additional engineered features: `fat_sugar_balance` and `filling_factor`. The final feature set included `is_dessert`, `minutes`, `calories (#)`, `submitted` (transformed to extract the year), and `prop_sugar`. A Random Forest classifier was used, with hyperparameters tuned via GridSearchCV. The optimal parameters were found to be `max_depth=42` and `n_estimators=142`.

The final model achieved an overall F1 score of 0.92, with improved performance across all rating categories, particularly for the lower ratings.

*Embed final model performance visualization (e.g., a confusion matrix or feature importance plot) here.*

---

## Step 8: Fairness Analysis

Fairness analysis was conducted to determine if the final model performs equitably across different groups. In this case, recipes were divided based on calorie content, using the median value (301.1 calories) as a threshold:

- **Low-Calorie Group**: Recipes with ≤ 301.1 calories.
- **High-Calorie Group**: Recipes with > 301.1 calories.

**Evaluation Metric**: Precision was used because mislabeling (false positives) can mislead users about recipe quality.

**Hypotheses**:
- **Null Hypothesis**: The model's precision for low-calorie and high-calorie recipes is roughly the same (differences are due to random chance).
- **Alternative Hypothesis**: The model's precision for low-calorie recipes is lower than for high-calorie recipes.

A permutation test was performed by shuffling the `is_high_calories` labels 1,000 times and calculating the difference in precision between the two groups. The observed difference was -0.023 with a p-value of 0.0, leading to rejection of the null hypothesis. This indicates that the model's precision for low-calorie recipes is significantly lower, suggesting a fairness concern.

*Embed fairness analysis permutation test visualization here.*

