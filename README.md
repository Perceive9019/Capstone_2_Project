
# Prediciting Airbnb Prices in San Diego

![San Diego](https://github.com/Perceive9019/Capstone_2_Project/assets/115586607/6a18b592-8fc5-48c4-ac93-177245dbf213)




## Introduction

Airbnb, with its 7 million listings globally, offers a widely-used alternative to conventional hotel stays, posing a challenge to the traditional hotel industry. The pricing of Airbnb properties, however, can greatly differ due to factors like location, amenities, and availability. Accurately forecasting these prices can assist hosts in setting competitive rates and help guests find options that align with their budget.
## Data

Our dataset is publicly accessible from Inside [AirBnB](http://insideairbnb.com/get-the-data/).
listings.csv: Summary information and metrics for listings in San Diego (good for visualizations). The file gives insights about a host, location, review rating score, room, and property type.
calendar.csv: Summary information about availability and price per day in 2022–2023 years. Based on 'listing_id' this file will be merged with listings.csv.
reviews.csv: Summary review data for the listings. This dataset won't be used in this analysis.




## Methodology
We chose the San Diego dataset and will take the following steps to make conclusions about what factors affect price listings:
- First Look at the Data:
    - What information is present? What information is missing? Discover general facts such as host listings count.
- Initial Data Preparation:
    - Remove irrelevant information, reformat the data, and input missing values.
- Find high level trends and correlations.
- Use Machine Learning for further analysis:
    - Encode categorical features correctly.
    - Train more than one model and look for feature importance and model prediction.

## Data Wrangling

The data cleaning process involved addressing several issues:

1. **Amenities Column**:
   - The values were split using `.str.split(",")` to separate amenities listed within a string.
   - A new feature was created that represents the number of amenities listed for each row.
   - A "total_amentities" column was added, capturing the count of amenities for each row.

2. **Date Column**:
   - A new 'month' column was introduced, extracting the month from the date using a lambda function. This function splits the date on the “-” character and picks the month.

3. **Price Feature**:
   - The data type of the price column was first converted to a string.
   - Dollar signs were then removed from the price.
   - Comma separators in the price values (indicating thousands) were also removed.

4. Any column that had 70% or more missing values was dropped.

5. Missing values in the remaining columns were replaced with the column mean.

By implementing these steps, the data was successfully cleaned and structured for further analysis.
## Exploratory Data Analysis

To identify factors that influence Airbnb Prices in San Diego, we sought to find out which months and neighborhoods had the highest prices. 

![Figure 1: Mean Daily Prices over 12 Months](https://github.com/Perceive9019/Capstone_2_Project/blob/main/images/average_prices_over_12_months.png)

Figure 1 illustrates how prices in San Diego fluctuate throughout the year, with the highest prices observed from July to September, which are the peak months.

![Figure 2: Number of Available Listings per Month of Year](https://github.com/Perceive9019/Capstone_2_Project/blob/main/images/count_of_listings_per_month.png)

Figure 2 reveals that August and September have the most listings available. Yet, a greater number of listings doesn't always mean lower rental prices. Prices during these months might be influenced by other elements like location and amenities.

![Figure 3: Mean Daily Price Depending on Month of Year and Neighborhood](https://github.com/Perceive9019/Capstone_2_Project/blob/main/images/mean_price_per_month_neighbourhood.png)

Figure 3 displays the average listing prices for the top 15 neighborhoods in San Diego. The neighborhoods commanding the highest rental prices include Gaslamp Quarter and La Jolla (both averaging $850), followed by Mission Bay ($690), Old Town (~$650), Little Italy (ranging from $600 to $780), and Pacific Beach (between $500 and $600). It's notable that these neighborhoods also have a significant number of listings. Prices rose sharply in July or between month 6 and month 8.

![Figure 4: Correlation Matrix of Numerical Features](https://github.com/Perceive9019/Capstone_2_Project/blob/main/images/correlation/correlation_heatmap.png)

Figure 4 indicates a strong correlation between the target variable (price) and several features, including accommodates, bedrooms, beds, bathrooms, and total_rooms (a combined count of bathrooms and bedrooms).



## Machine Learning Models and Results

- We evaluated three models: Random Forest Regression, XGBoost Regression, and Light Gradient Boosting Machine to identify the key features.
- To prevent overfitting, we employed 5-fold cross-validation, eliminated less significant features, and removed features with high correlation.
- For better model performance, we transformed categorical variables using one-hot encoding and then trained and made predictions with each of the three models.

|      Model    | Cross-validation Score| R^2 Score|
|---------------|-----------------------|----------|
| Random Forest |          -8%          |    52%   |
|      LGBM     |          20%          |    49%   |
|    XGBoost    |         -15%          |    61%   |

#### Analyzing Model Evaluation Results

|      Model    | Cross-validation Score| R^2 Score|
|---------------|-----------------------|----------|
| Random Forest |         -8% suggests poor model performance because it’s not capturing the underlying patterns in the training data effectively and may be underfitting          |    52% suggests that the model explains about 52% of the variance in the dependent variable   |
|      LGBM     |          20% indicates that the model explains approximately 20% of the variance in the target variable          |    49% of the variability in the outcome can be attributed to the model, while 51% remains unexplained.   |
|    XGBoost    |         -15% suggests that the model is performing quite poorly, and maybe underfitting, and not capturing the underlying patterns in the training data          |    61% implies that the model explains around 61% of the variance in the dependent data.   |

**Note**:

    - The cross-validation score -assesses how well the model is likely to perform on unseen data. 
    - The R^2 score measures how well the model explains the variability in your target variable.
    - High values for both cross-validation scores and R^2 scores generally indicate a model that is performing well but be cautious about overfitting.


## Predictions

A higher R^2 score indicates better predictive accuracy, making the XGBoost model the preferred choice. However, its lower cross-validation score signals potential challenges with its performance on new data. Strategies to remedy this will be discussed later.

What we can see however, is that the LGBM model selects a broad set of features that are important: month, longitude, total_amenities, beds, bedrooms, host_total_listings_count, bathrooms, availability_90 and number_of_reviews.

![LGBM Feature Importance](https://github.com/Perceive9019/Capstone_2_Project/blob/main/images/features/LGBM%20model%20with%20numerical%20and%20encoded%20categorical%20features%20together.png)

In contrast, XGBoost model, the target variable(price) is dependent on a small set of dominant features: property_type_Privateroominrentalunit, host_since_2020, neighborhood_cleansed_MissionBay, bathrooms, and instant_bookable_f.

![XGBoost Feature Importance](https://github.com/Perceive9019/Capstone_2_Project/blob/main/images/features/XGBoost%20model%20with%20numerical%20and%20encoded%20categorical%20features%20together.png)

The Random Forest model selects a smaller set of dominant features: bathrooms, property_type_Privateroominrentalunit, host_verifications_phone, and host_since_2020.

![Random Forest Features](https://github.com/Perceive9019/Capstone_2_Project/blob/main/images/features/Random%20Forest%20model%20with%20numerical%20and%20encoded%20categorical%20features%20together.png)
## Next Steps
Given that the Random Forest and XGBoost models were not able to capture the underlying patterns in training data, more tuning of these models is required. This means revisiting feature selection and selecting only the import features to help reduce model complexity. 

Note: the Light Gradient Boost Machine Model only explained 20% of the variance in the training data during validation, this shows that it too would benefit from more tuning.

#### Recommendations:

1. **Feature Selection and Hyperparameter tuning**:
    - Use feature selection techniques to identify and retain the most relevant features while discarding irrelevant or redundant ones. e.g. PCA (Principal Component Analysis) 
    - Experiment with different hyperparameters for your model, such as learning rate, regularization strength, or tree depth, and use techniques like grid search or random search to find optimal settings.

2. **Feature Engineering and Cross-Validation**:
    - Add more relevant features to your dataset that might help the model learn the underlying patterns.
    - Collect more training data if possible. A larger dataset can help the model learn better representations and reduce underfitting.
    - Use techniques like k-fold cross-validation to assess your model's performance on different subsets of the data. This can help you identify if underfitting is a consistent issue.

## Conclusion

In our analysis, we looked at which features greatly influenced Airbnb price listings in San Diego in order to help hosts set competitive prices to attract customers.

We found that:
- Airbnb prices are highly dependent on the time of year and neighborhood. The highest prices are charged in the month of August.
    - The most expensive neighborhoods are:
        - Gaslamp Quarter which has a prominent nightlife i.e. clubs, restaurants, theaters, etc.
        - La Jolla, Pacific Beach, and Mission Bay which are by the ocean side.
        - Old Town and Little Italy, which have attractions such as boutiques, restaurants, hotels, Museums, etc.

- The XGBoost model was the most robust predictive model but it was not able to capture the underlying patterns in the training data. 
- This suggests that more tuning of these models is required. For Example:  revisiting feature selection and hyperparameter tuning.

