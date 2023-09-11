# Optimizing Real Estate Strategies in King County: A Data-Driven Approach for Haven-Kings Property Management

## Table of Contents
1. [Project Team](#project-team)
2. [Supervisor](#supervisor)
3. [Business Case](#business-case)
4. [Overview](#overview)
5. [Objective](#objective)
6. [Research Questions](#research-questions)
7. [Data Source](#data-source)
8. [Data Exploration](#data-exploration)
9. [Modeling](#modeling)
    - [House Pricing Models](#house-pricing-models)
    - [Dynamic Pricing Recommendations Models](#dynamic-pricing-recommendations-models)
10. [Conclusions](#conclusions)
11. [Recommendations and Action Points](#recommendations-and-action-points)


## Project Team
- [Julliet Iswana](https://github.com/Iswana-O)
- [Wayne Kipngeno Korir](https://github.com/waynekipngeno)
- [Eva Kiio](https://github.com/evamwende)
- [Oscar Mulei](https://github.com/omulei)

## Supervisor
- [Asha Deen](https://moringaschool.com/courses/data-science-course-part-time/)

![Project Banner](Images/Country%20Home.png)

**Business Case:**
In the thriving real estate market of King County, the capacity to make strategic pricing decisions can either enhance profits or turn assets into liabilities. Haven-Kings Property Management, with its vast portfolio, stands at a pivotal juncture where conventional pricing methods no longer suffice. The digital age demands a shift from age-old practices like the Comparative Market Analysis (CMA) and the "1% Rule" to a more agile, data-driven approach. By leveraging advanced analytical methods, such as multiple regression analysis, property management firms can tap into precise pricing strategies, enhancing revenue streams and ensuring sustainable growth.

**Overview:**
This project aims to harness the power of data analytics to revolutionize Haven-Kings Property Management's pricing strategies in King County's competitive real estate landscape.

**Objective:**
Guided by data insights and multiple regression analysis, the team sets out to:
1. **Optimize House Pricing:** Craft a model that tailors pricing based on property features, regional nuances, and emerging market trends.

2. **Dynamic Pricing Recommendations:** Pivot rental prices in real-time, aligning with market fluctuations and property specifics.

**Research Questions:**
1. **House Pricing:** 
   - Dissect key determinants shaping house prices in King County.
   - Analyze the interplay between property attributes and their market value.
   - Appraise the precision of the regression model in predicting property prices.
     
2. **Dynamic Pricing Recommendations:**
   - Construct a robust framework for real-time pricing using multiple regression analysis.
   - Spotlight pivotal factors for adaptive rental rate revisions.
   - Assess the ramifications of dynamic pricing on revenue trajectories and market stature.

**Data Source:**
The foundation of this project rests on the [kc_house_data.csv](./data/kc_house_data.csv) dataset from King County House Sales.

## Data Exploration
The exploration starts with understanding the correlation between each predictor and price, followed by visualizing these correlations.

![Correlation Plot](./Images/output_21_0.png)

There are evident multicollinearity issues that need addressing. Notably:
- `sqft_above` and `sqft_living` are closely linked. `sqft_living` is preferred due to its encompassing nature.
- `sqft_lot` is chosen over `sqft_lot15` due to its direct relation to the house.


## Modeling

To address the research questions, multiple regression models are employed, each focusing on relevant independent variables (features) to predict the dependent variable (target). The primary dependent variable for these models is `price`.

### House Pricing Models
#### Model 1: House Price Determinants
- **Objective**: Identify the primary determinants of house prices in King County.
- - **Method**: Multiple linear regression will be used to understand the contribution of each feature to the house price.
- **OLS Regression Results Interpretation for Extended Model**

The regression results provide insights into a more complex model with multiple predictors:

- **Model Scores:**
  - **Training Score (0.71):** This suggests the model explains 71% of the variance in the training data.
  - **Test Score (0.68):** The model explains 68% of the variance in the test data, which is slightly lower than the training score but still indicative of a reasonable fit.

- **Coefficients:** The coefficients represent the change in the dependent variable (price) for a one-unit change in the predictor, holding other predictors constant. For instance, every additional bedroom reduces the price by approximately $35,744, while waterfront properties increase the price by about $613,791.

- **Model Metrics:**
  - **R-squared (0.707):** The model explains about 70.7% of the variance in the price. This is consistent with the given training score.
  - **Adjusted R-squared (0.707):** Adjusted for the number of predictors in the model, the explained variance remains at 70.7%, which is a good sign.

![Model 1 Results](./Images/output_25_1.png)

#### Model 2: Impact of Living Space on House Price
**Objective**: Understand the relationship between the living space (in square feet) and the price of houses in King County.
- **Features**: `sqft_living`.
- **Target Variable**: Price.
- **Method**: Simple linear regression was used to quantify the linear relationship between living space and house price. The predictor `sqft_living` was chosen due to its high correlation coefficient of approximately \(0.702\) with the target variable, indicating a strong positive linear relationship.
- **OLS Regression Results Interpretation**

The OLS regression provides insights into the relationship between `sqft_living` and the dependent variable:

- **R-squared (0.457):** This indicates that the model explains about 45.7% of the variance in the dependent variable. While this captures a significant portion of the relationship, there's still a substantial amount of variance that remains unexplained.

- **Coefficient for sqft_living (1.54e-05):** This coefficient suggests that for every unit increase in square footage (`sqft_living`), the dependent variable increases by \(1.54 \times 10^{-5}\). The predictor's p-value is almost zero, confirming its statistical significance in the model.

![Model 2 Results](./Images/output_28_0.png)

#### Model 3: Property Attributes and Their Influence on Market Value
- **Objective:** Assess how property attributes, including geographical aspects, affect market value.
- **Features:** Variables like `grade`, `condition`, `view`, `sqft_living`, `age_house`, and others describe the property.
- **Target:** The property's market value or "price".
- **Method:** Multiple linear regression, enhanced with preprocessing techniques like Box-Cox transformation and one-hot encoding.
-**OLS Regression Results Interpretation for Transformed Price Model**

The regression results shed light on a model where the dependent variable is the `transformed_price`. 

- **Model Metrics:**
  - **R-squared (0.548):** The model explains 54.8% of the variance in the transformed price. This indicates a moderate fit, suggesting that over 45% of the variance in the transformed price remains unexplained by the predictors.

- **Coefficient Interpretations:**
  - **sqft_living (8.279e-06):** For every unit increase in `sqft_living`, the `transformed_price` increases by \(8.279 \times 10^{-6}\). This predictor is statistically significant with a p-value close to zero.
  - **Grade Variables:** Variables representing different grades (`grade_4.0` to `grade_13.0`) have coefficients indicating their impact on `transformed_price` relative to some baseline grade. However, not all of them are statistically significant. For example, `grade_9.0` to `grade_13.0` show statistical significance with p-values less than 0.05, while others like `grade_5.0` and `grade_6.0` have larger p-values, suggesting they might not be significant predictors.
  - **Condition Variables:** Variables representing different property conditions (`condition_1` to `condition_4`) show their impact on the `transformed_price`. Among these, only `condition_1` is statistically significant with a p-value less than 0.05.

![Model 3 Image](./Images/output_31_1.png)

### Dynamic Pricing Recommendations Models
#### Model 4: Dynamic Pricing Recommendations
- **Objective**: Develop a model to offer real-time pricing suggestions for rental properties under "Haven-Kings".
- **Features**: Those attributes that are deemed significant for rental pricing, such as location (`lat`, `long`), `view`, property size (`sqft_living`, `sqft_lot`), and others.
- **Target Variable**: Price.
- **Method**: Multiple linear regression will provide coefficients for each feature, guiding dynamic pricing adjustments.
OLS Regression Analysis Summary:

1. **Model Overview**:
   - **R-squared**: 0.628 - This model explains approximately 62.8% of the variance in house prices.
   - The model is statistically significant with a very low p-value, indicating a very low chance that observed relationships are due to random chance.

2. **Key Coefficients**:
   - Latitude (lat): Positive relation. An increase in latitude is associated with an increase in house price by approximately $679,800.
   - Longitude (long): Negative relation. An increase in longitude reduces the house price by approximately $216,300.
   - Sqft of Living Space (sqft_living): Positive relation. An increase in living space (in sqft) raises the house price by approximately $263.64.
   
   - View Quality: Houses with better views (especially `view_4.0` category) have significantly higher prices.
   - Renovated: Renovated houses are priced about $102,800 higher than non-renovated houses on average.

3. **Considerations**:
   - **Sqft of Lot Space (sqft_lot)**: This predictor might not be significant due to a p-value greater than 0.05.
   - **Residuals**: They might not be normally distributed.
   - **Multicollinearity**: Some predictors might be correlated, as indicated by the large condition number.

![Model 4 Results](./Images/output_34_0.png)

## Conclusions:
1. **Data-Driven Decision Making:** Given the depth and breadth of data available from King County House Sales, data-driven decision-making is not only feasible but also crucial for optimizing real estate strategies.
2. **Significance of Property Attributes:** Preliminary exploration emphasizes the significance of attributes like number of bedrooms, living area size, and location on house prices.
3. **Data Quality:** Data preprocessing steps highlighted the importance of maintaining data quality, especially in key features like 'waterfront', 'view', and 'yr_renovated'.
4. **Potential for Predictive Modeling:** The nature of the dataset, with its rich features, indicates strong potential for building predictive models for house pricing.

## Recommendations-and-Action-Points

1. **Deep Dive into EDA:** Further exploration and visualization are needed to understand the relationships between different features and house prices. This would provide more nuanced insights into how different attributes interplay to determine property prices.
2. **Focus on Location Analysis:** Given the dataset's geographical details (latitude and longitude), a deeper geographical analysis can provide insights into location-based pricing strategies.
3. **Predictive Modeling:** Regression analysis, as mentioned in the business case, should be the starting point. However, exploring other machine learning models can provide more accurate pricing predictions.
4. **Addressing Data Gaps:** Ensure routine checks for missing or inconsistent data, especially for significant features.

#### Action Points:
1. **Enhanced Data Collection:** Continuously update and expand the dataset. Include more recent sales data and potentially other external factors (e.g., economic indicators, neighborhood developments).
2. **Modeling and Validation:** Begin with regression models for price prediction, and as more data becomes available, explore complex algorithms. Regularly validate and refine these models using new data.
3. **Development of Dynamic Pricing Tools:** Build tools that leverage the predictive models. These tools should offer real-time pricing recommendations to the sales and management teams.
4. **Feedback Mechanism:** Implement a mechanism where the ground-level sales team can provide feedback on pricing recommendations. This would allow for iterative refinement of the models and strategies.
5. **Training & Workshops:** Organize regular training sessions for the sales and management teams. Ensure they understand and trust the data-driven recommendations.

By adopting these conclusions, recommendations, and action points, Haven-Kings Property Management can achieve a competitive edge in the King County real estate market, leading to increased profitability and growth.

