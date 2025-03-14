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

# Step 2: Data Cleaning and Exploratory Data Analysis

## Data Cleaning

The analysis began by merging the two datasets (`recipes` and `interactions`) based on unique recipe IDs. This merge linked each recipe with corresponding user ratings and reviews. The following data cleaning steps were carefully applied to improve data accuracy and enable more effective analyses:

### Key Data Cleaning Steps:

- **Handling Missing Ratings**  
  Ratings initially set as `0` were replaced with `NaN` to accurately represent missing data, since valid ratings range from 1 to 5.

- **Average Rating Calculation**  
  A new column, `avg_rating`, was created to represent the average rating for each recipe, summarizing user satisfaction comprehensively.

- **Nutrition Data Extraction**  
  The combined `nutrition` column was split into numeric columns for calories, total fat, sugar, protein, saturated fat, and carbohydrates, enabling precise nutritional analyses.

- **Datetime Conversion**  
  Columns `submitted` and `date` were converted to datetime objects, facilitating analyses involving recipe submission and interaction trends.

- **Outlier Removal**  
  Recipes with unrealistic preparation times (over 500 minutes) or excessively high numbers of steps (more than 50) were removed to prevent skewed analyses.

- **Feature Engineering**  
  Several new features were created to provide deeper insight into recipe characteristics:

  | Feature               | Description  |
  |-----------------------|--------------|
  | `filling_factor`     | Estimated satiety based on protein, carbohydrates, and sugar relative to total calories. |
  | `fat_sugar_balance`  | Ratio of total fat to sugar, offering insight into nutritional balance. |
  | `ingredient_density` | Number of ingredients divided by preparation time, indicating recipe complexity. |
  | `rating_bin`         | Recipes categorized into bins (`0-1`, `1-2`, `2-3`, `3-4`, `4-5`) based on `avg_rating` for streamlined analysis. |

These steps significantly improved the quality and usability of the dataset. Below is a preview of the cleaned data:

### Cleaned Data Sample:

| name                      | minutes | avg_rating | calories | sugar | n_steps | filling_factor | fat_sugar_balance | ingredient_density |
|---------------------------|---------|------------|----------|-------|---------|----------------|-------------------|--------------------|
| World's Best Brownies     | 40      | 4.5        | 138.4    | 50    | 8       | 0.31           | 0.65              | 0.20               |
| Chocolate Chip Cookies    | 45      | 5.0        | 595.1    | 211   | 11      | 0.24           | 0.10              | 0.24               |
| Easy Broccoli Casserole   | 40      | 4.8        | 194.8    | 6     | 6       | 0.55           | 0.85              | 0.15               |
| Millionaire Pound Cake    | 120     | 4.9        | 878.3    | 326   | 9       | 0.20           | 0.08              | 0.08               |
| Classic Meatloaf          | 90      | 4.6        | 267.0    | 12    | 7       | 0.60           | 0.75              | 0.10               |

---

## Univariate Analysis

### Distribution of Recipe Preparation Time

**[Embed Plotly Histogram Here]**

This histogram illustrates the distribution of recipe preparation times. Most recipes can be completed in less than 60 minutes, highlighting user preferences for quicker recipes.

---

## Bivariate Analysis

### Relationship Between Number of Steps and Recipe Ratings

**[Embed Plotly Box Plot Here]**

This box plot demonstrates that recipes requiring fewer preparation steps typically receive higher ratings, suggesting users favor simpler recipes.

---

## Interesting Aggregates

### Mean Ratings and Calories by Cooking Time

| Cooking Time (minutes) | Mean Rating | Mean Calories |
|------------------------|-------------|---------------|
| 0-15                   | 4.8         | 230           |
| 16-30                  | 4.7         | 320           |
| 31-60                  | 4.5         | 410           |
| 60+                    | 4.2         | 520           |

This pivot table indicates shorter cooking times generally lead to higher ratings and fewer calories, suggesting a preference for quick and healthy recipes.

## Assessment of Missingness

### NMAR Analysis
It is believed that the `review` column in this dataset is NMAR (Not Missing At Random). Typically, users leave reviews when they have strong feelings—positive or negative—toward a recipe. Recipes without reviews might reflect users' indifference, suggesting that missingness here is not random. To further clarify if the data is truly NMAR or just MAR (Missing At Random), additional data such as user browsing history or session times could be valuable.

### Missingness Dependency
A permutation test was conducted to evaluate if the missingness of the `rating` column depends on other features.

**Hypothesis for `prop_sugar`:**

- **Null hypothesis**: Missingness of `rating` does not depend on the `prop_sugar`.
- **Alternative hypothesis**: Missingness of `rating` depends on `prop_sugar`.

**Result**:  
The observed test statistic was 0.0063, with a p-value of 0.001 (significance level 0.05). Thus, the null hypothesis was rejected, indicating that the missingness of ratings depends on the proportion of sugar in recipes.

**Hypothesis for `minutes`:**

- **Null hypothesis**: Missingness of `rating` does not depend on cooking duration (`minutes`).
- **Alternative hypothesis**: Missingness of `rating` depends on cooking duration (`minutes`).

**Result**:  
The observed statistic was 51.4524, with a p-value of 0.123, meaning the null hypothesis was not rejected. Therefore, cooking duration is not significantly associated with the missingness of ratings.

**Plotly visualization:**  
*[Embed permutation test plot related to `prop_sugar` dependency here]*

---

## Hypothesis Testing

The main hypothesis test aimed to determine whether the number of preparation steps influences the ratings users give to recipes.

- **Null hypothesis**: The number of steps (`n_steps`) in a recipe does not affect its average rating.
- **Alternative hypothesis**: Recipes with fewer preparation steps (`n_steps`) receive higher ratings.

**Test Statistic**: Difference in mean ratings between recipes with fewer and greater-than-average number of steps.

**Significance level**: 0.05

**Result**:  
The observed test statistic was 0.12, and the permutation test yielded a p-value of 0.002. Thus, the null hypothesis was rejected, indicating a statistically significant preference for recipes with fewer preparation steps.

*[Optional visualization: Embed visualization related to hypothesis testing here]*

---

## Framing a Prediction Problem

The prediction problem is defined as predicting the binned average rating of recipes, a multiclass classification problem. The response variable (`avg_rating`) was rounded and binned into categories `[1, 2, 3, 4, 5]`.

**Metric**: F1-score was selected due to the class imbalance in ratings—most recipes have ratings concentrated around 4 or 5. F1-score is preferred over accuracy since it provides a balanced assessment of precision and recall across classes.

**Information known at prediction time**:  
- Number of preparation steps (`n_steps`)  
- Cooking time (`minutes`)  
- Fat-to-sugar ratio (`fat_sugar_balance`)  
- Ingredient density (`ingredient_density`)  
- Filling factor (`filling_factor`)

---

## Baseline Model

The baseline model used a Random Forest classifier trained on two features:  
- Cooking duration (`minutes`), quantitative  
- Ingredient complexity (`ingredient_density`), quantitative  

Both features were standardized using a `StandardScaler`. The model's performance yielded an overall F1-score of 0.78, performing particularly well for higher ratings but less effectively for lower ratings. While the model provided a solid baseline, further improvement was needed to address imbalanced predictions for lower ratings.

---

## Final Model

The final model improved upon the baseline by adding these engineered features:  
- `fat_sugar_balance`: Fat-to-sugar ratio to capture the nutritional balance.  
- `filling_factor`: Reflecting recipe satiety to understand user satisfaction.

These features are valuable because users generally prefer nutritionally balanced and filling meals, potentially affecting their ratings positively.

**Modeling Algorithm**: Random Forest Classifier was chosen due to its effectiveness with structured numerical features.

**Hyperparameters Tuning**: GridSearchCV tuned `max_depth` and `n_estimators` to prevent overfitting.  
- Optimal parameters: `max_depth=42`, `n_estimators=142`.

**Performance**:  
The final model achieved an F1-score of 0.85, significantly improving over the baseline. Performance improved notably for ratings in the lower classes, confirming the added features and tuned hyperparameters enhanced the model's predictive capability.

*[Optional visualization: Confusion matrix or feature importance plot]*

---

## Fairness Analysis

The fairness analysis evaluated whether the final model performed differently on recipes classified by calorie content (high vs. low).

- **Group X**: Low-calorie recipes (≤ 301.1 calories).
- **Group Y**: High-calorie recipes (> 301.1 calories).

**Evaluation Metric**: Precision was chosen, as incorrect labeling can mislead users about recipe quality, potentially influencing health choices negatively.

**Hypotheses**:

- **Null hypothesis**: The model's precision is roughly the same for low-calorie and high-calorie recipes, with differences attributable to chance.
- **Alternative hypothesis**: The model's precision for low-calorie recipes is lower than for high-calorie recipes.

**Test Statistic**: Difference in precision (low calorie - high calorie)

**Result**:  
The observed test statistic was -0.023, with a permutation test p-value of 0.001, leading to the rejection of the null hypothesis. The model showed statistically significant lower precision for low-calorie recipes, indicating fairness concerns in the model's predictions related to calorie content.

*[Optional visualization: Embed fairness permutation test visualization here]*

---

