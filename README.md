## Project Overview

This project analyzes Food.com recipes (2008–2018) and their user reviews to understand what makes recipes popular and how content features relate to preparation time and user satisfaction. By examining over 230,000 recipes and 1.1 million user interactions, we investigate the drivers behind recipe success and build predictive models for cooking duration.

## Dataset

- **RAW_recipes.csv**: ~230K recipes with metadata including name, tags, ingredients, steps, nutritional information, preparation time, and submission date
- **RAW_interactions.csv**: ~1.1M user interactions with ratings (1-5 stars) and free-text reviews linked to recipes

After data cleaning and filtering (removing outliers in preparation time and calories, filtering unrealistic nutritional values), the final dataset contains approximately 170,000 interactions.

## Research Questions

1. **Do nutritional attributes—specifically protein content—correspond to meaningful differences in calorie distributions among recipes?**
2. **Which recipe features most strongly determine preparation time, and how accurately can we predict cooking duration using these features?**

## Key Findings

### 1. Exploratory Data Analysis

**Recipe Characteristics:**
- Most recipes take less than 400 minutes to prepare, with a clear concentration under 200 minutes
- Calorie distribution peaks between 100-1000 calories, consistent with typical recipes
- Nutritional values (protein, fat, sodium, carbs) vary widely but most recipes fall within reasonable ranges

[**INSERT IMAGE**: Distribution of Preparation Time]

[**INSERT IMAGE**: Distribution of Calories]

**Text Analysis:**
- Most frequent unigrams in recipe names: "chicken," "easy," "best," "sauce," "quick"
- Most frequent bigrams: "crock pot," "peanut butter," "gluten free"
- Top tags: "easy," "quick," "30-minutes-or-less," "healthy," "vegetarian," "desserts"

This suggests users prioritize recipes that are quick, simple, and feature popular ingredients.

[**INSERT IMAGE**: Top 10 Unigrams in Recipe Names]

[**INSERT IMAGE**: Top 10 Tags]

### 2. Bivariate Analysis

**Rating Relationships:**
- No significant relationship between calories and user ratings—recipes of all calorie levels receive similar ratings
- Recipe complexity (number of steps and ingredients) shows weak correlation with ratings
- Recipes submitted per year show a clear declining trend from 2008 to 2018, indicating decreasing platform engagement

[**INSERT IMAGE**: Calories vs Ratings (Box Plot)]

[**INSERT IMAGE**: Recipes Submitted Per Year]

**Feature Correlations:**
- Moderate positive correlation (0.39) between number of ingredients and number of steps, suggesting complex recipes tend to have both more ingredients and steps
- Nutritional attributes show moderate intercorrelation, indicating recipes higher in one nutrient tend to be higher in others as well

[**INSERT IMAGE**: Feature Correlation Matrix]

### 3. Hypothesis Testing: Protein Content and Calorie Distribution

**Question**: Do high-protein and low-protein recipes have the same calorie distribution?

**Method**: Two-sample Kolmogorov–Smirnov (K-S) test comparing empirical cumulative distribution functions

**Results**:
- Test p-value: **< 0.001** (highly significant)
- **Conclusion**: Reject null hypothesis. High-protein and low-protein recipes have **statistically distinct** calorie distributions.
- High-protein recipes (≥16% protein) tend to cluster at different calorie levels than low-protein recipes (<16% protein)

[**INSERT IMAGE**: ECDF Comparison - High vs Low Protein Recipes]

### 4. Assessment of Missingness

**Finding**: Approximately 2,000 recipes (~3.6%) are missing average ratings.

**Permutation Test for Missingness Dependency**:
- Null Hypothesis: Missingness is independent of recipe duration (MCAR)
- Test statistic: Absolute difference in mean minutes between missing and present ratings
- **Result**: p-value < 0.001 (strong evidence of MAR)
- **Conclusion**: Missingness depends on recipe duration—longer recipes are less likely to be rated

**Imputation Strategy**: Missing ratings were imputed using the mean rating for each duration quartile, accounting for the observed dependency.

### 5. Prediction Model: Estimating Recipe Duration

**Problem Framing**: Regression task to predict recipe preparation time (`minutes`) using recipe features

**Feature Engineering**:
- Numeric features: number of steps, number of ingredients
- Categorical features: tags transformed into binary indicators (preparation, dietary, easy, occasion, main-dish, vegetables, meat)
- Advanced features: polynomial interactions, engineered ratios (steps/ingredients)

**Model Comparison**:

| Model | RMSE |
|-------|------|
| Baseline Linear Regression | 53.0 |
| Linear + Feature Engineering | 52.2 |
| Polynomial Regression | 52.5 |
| **Random Forest (Final)** | **47.0** |

**Best Model**: Random Forest Regressor (200 estimators, max depth 10)

- Achieves **RMSE of 47 minutes** on test set
- Substantially outperforms linear approaches, indicating recipe duration involves complex, nonlinear relationships
- Categorical features (tags) are more predictive than numeric features alone

[**INSERT IMAGE**: Actual vs Predicted Duration]

### 6. Fairness Analysis

**Question**: Does the model perform equitably across different recipe types?

**Method**: Compared model performance for meat-based vs. vegetable-based recipes using permutation testing

**Results**:
- **Meat-based recipes RMSE**: 66.3 minutes
- **Vegetable-based recipes RMSE**: 44.8 minutes
- **Observed difference**: 21.5 minutes
- **Permutation test p-value**: < 0.001 (statistically significant)

**Conclusion**: The model exhibits **significant fairness concerns**. It predicts duration much less accurately for meat-based recipes, suggesting the model may be biased toward vegetable-based recipes. Further investigation and mitigation strategies are recommended.

[**INSERT IMAGE**: Prediction Error Distribution by Recipe Type]

## Key Insights

1. **User Preferences**: Recipe popularity is driven more by perceived ease and ingredient appeal than by nutritional content
2. **Protein-Calorie Relationship**: Protein content is a meaningful predictor of calorie distribution, with clear statistical significance
3. **Preparation Time Drivers**: Complex, nonlinear interactions between recipe features determine cooking duration—tree-based models capture these better than linear approaches
4. **Fairness Issues**: The prediction model has systematic bias against meat-based recipes, highlighting the importance of fairness evaluation in ML systems

## Files

- `template.ipynb`: Full analysis notebook with code, visualizations, and detailed explanations
- `dsc80_utils.py`: Utility functions for analysis
- `RAW_recipes.csv`: Recipe metadata
- `RAW_interactions.csv`: User ratings and reviews
- `README.md`: This summary document

## Website
For an interactive version of this analysis, visit: [https://r-marda.github.io/analysis-of-foodcom-recipes-interactions/](https://r-marda.github.io/analysis-of-foodcom-recipes-interactions/)
