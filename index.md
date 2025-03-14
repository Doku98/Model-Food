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
