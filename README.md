# Project Overview
This project is inspired by the Practice and Application of Data Science course DSC80 (https://dsc80.com/), specifically Project 3 – Exploratory Data Analysis assignment.

Hi, Adventurous Cooking Heroes! If you want to get a higher average rating with the recipes you share on food.com, it is likely that it does not matter how long the recipe takes to prepare. That's why this project recommends you share any recipe you want, simple or complex. Want to learn more, continue reading.

by Mert Ozer

---

# Introduction

## Introduction and Question Identification

Why does preparation time matter in the world of recipes and cooking? This pivotal question resonates with everyone from home cooks to professional chefs. As we explore whether the time invested in a recipe influences its average ratings, we unlock insights crucial for anyone passionate about culinary arts. This analysis will specifically answer the question: Is preparation time of a recipe important factor for average ratings?

## Introduction and Question Identification
The dataset used in this research contains recipes and ratings from food.com. It was originally scraped and used by the authors of this (https://cseweb.ucsd.edu/~jmcauley/pdfs/emnlp19c.pdf) recommender systems paper.
The data has two csv files:
-   RAW_recipes.csv: contains 83782 recipes
-	RAW_interactions.csv: contains 83782 reviews and ratings submitted for the recipes in RAW_recipes.csv

A subset of the raw data containing only the recipes and reviews posted since 2008 will be used in this research, since the original data is quite large.
Even though the data contains more columns for the purpose of this research only the following columns of each dataset will be used.

| Columns used   | Explanation of the columns                                                                                                                        | CSV file column is taken from |
|----------------|----------------------------------------------------------------------------------------------------------------------------------|-------------------------------|
| Minutes        | Minutes to prepare recipe                                                                                                                         | RAW_recipes.csv               |
| Nutrition      | Nutrition information in the form [calories (#), total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), carbohydrates (PDV)] | RAW_recipes.csv               |
| N_steps        | Number of steps in recipe                                                                                                                         | RAW_recipes.csv               |
| N_ingredients  | Number of ingredients used in recipe                                                                                                              | RAW_recipes.csv               |
| rating         | Rating given by user                                                                                                                              | RAW_interactions.csv          |



---

# Cleaning and Exploratory Data Analysis

## Data Cleaning

**Left Merge the recipes and interactions DataFrames:** This merge will combine the two datasets based on a common column, preserving all rows from the recipes DataFrame and only those from interactions that have matching values in the common column. In our case, the common column is “id” in “recipes” and “recipe_id” in “interactions”.

**Fill Ratings of 0 with np.nan:** Replacing 0 ratings with NaN (Not a Number) is a common approach in data analysis, especially when 0 can be misleading. For instance, a 0 rating might be used to indicate missing data or a non-applicable scenario, rather than an actual rating of zero. By converting these to NaN, we can more accurately compute statistics like mean, as NaN values are generally ignored in such calculations.

**Find the Average Rating Per Recipe:** This involves grouping the merged data by recipe and calculating the mean of the ratings.

**Add the Average Rating Series Back to the recipes DataFrame:** We can merge this new Series containing the average ratings back into the original recipes DataFrame. This can be done by merging on the recipe ID.

**Removing Recipes with 0 Minutes Preparation Time:** By removing the single instance where a recipe has a preparation time of 0 minutes, we’ve eliminated what is likely to be a data entry error or an outlier that could skew results. Given that it's a single data point, the impact on the overall analysis would be minimal but ensures the integrity of the data.

**Considering Recipes Taking More Than 3 Hours as Outliers:** Setting a threshold of 3 hours to exclude recipes assumes that anything beyond this is atypical for our dataset's context. While this step removed approximately 7% of the data, it helps focus the analysis on a range that is more representative of the bulk of the data, avoiding skew from extreme values. This can lead to a more accurate understanding of the typical time investment users can expect from the recipes. The effect of these decisions is that the dataset now likely represents a more 'everyday' scenario of cooking, removing rare and extreme cases. However, it's important to note that in doing so, we might have excluded legitimate recipes that do require a long preparation time, such as marinades, slow-cooked meals, or ferments, which could be relevant for a different type of analysis. Therefore, while the current analysis becomes more focused and probably more relevant for quick-to-prepare meals, it may not capture the full spectrum of cooking practices for those who are interested in more time-intensive recipes.

**New Column, Percentage of Daily Sodium Value:** The string within the nutrition column for each recipe was parsed to isolate the sodium value. The dataset now has a clear, discrete column for sodium, which can be directly used in statistical analyses.

**Categorizing the Preparation Time:** This column will be used in aggregate analysis and answering research question. The selected bins are ['0-30 min', '30-60 min', '60-90 min', '90-120 min', '120-150 min', '150+ min'].

**Drop unnecessary columns:** We will be dropping columns that will not be used in this research.

Here is the resulting DataFrame:
|   minutes | nutrition                                     |   n_steps |   n_ingredients |   average_rating |   sodium | time_interval   |
|----------:|:----------------------------------------------|----------:|----------------:|-----------------:|---------:|:----------------|
|        40 | [138.4, 10.0, 50.0, 3.0, 3.0, 19.0, 6.0]      |        10 |               9 |                4 |        3 | 30-60 min       |
|        45 | [595.1, 46.0, 211.0, 22.0, 13.0, 51.0, 26.0]  |        12 |              11 |                5 |       22 | 30-60 min       |
|        40 | [194.8, 20.0, 6.0, 32.0, 22.0, 36.0, 3.0]     |         6 |               9 |                5 |       32 | 30-60 min       |
|       120 | [878.3, 63.0, 326.0, 13.0, 20.0, 123.0, 39.0] |         7 |               7 |                5 |       13 | 120-150 min     |
|        90 | [267.0, 30.0, 12.0, 12.0, 29.0, 48.0, 2.0]    |        17 |              13 |                5 |       12 | 90-120 min      |

## Univariate Analysis

Since we will be dealing with the average ratings and minutes to prepare the recipe, in this section we will be analyzing the distribution of those values.
Let start by analyzing the average ratings.

<iframe src="assets/graph1.html" width=800 height=600 frameBorder=0></iframe>

The histogram displays a clear skew towards the higher end of the rating scale, with the most frequent average rating being 5. This suggests that the majority of recipes receive very high ratings, which could indicate a tendency among users to rate recipes favorably or could be reflective of a selection bias where only the most liked recipes are rated. The presence of fewer ratings towards the lower end of the scale further supports the idea of a positive skew in user ratings.
Next, we will analyze the minutes to prepare the recipe.

<iframe src="assets/graph2.html" width=800 height=600 frameBorder=0></iframe>

The histogram indicates that most recipes require relatively short preparation times, with a significant number of recipes concentrated around 20 to 30 minutes, reflecting a common preference or convenience for quicker meal options. There is a gradual decrease in the number of recipes as the preparation time increases, showing that fewer recipes require longer to prepare, with those requiring more than 60 minutes being comparatively rare in this dataset.
The histogram also shows periodic spikes at regular intervals, particularly noticeable at multiples of 10 and 30 minutes. This pattern suggests that recipe preparation times are commonly rounded to these intervals.

## Bivariate Analysis

To see the relationship between average_rating and minutes (Minutes to prepare recipe). We can graph a scatter plot with a heat map.Graph the relationship.

<iframe src="assets/graph3.html" width=800 height=600 frameBorder=0></iframe>


The scatter plot shows a dense concentration of data points with high average ratings clustered around the shorter preparation times, suggesting that recipes with less prep time are more frequently rated and tend to receive higher ratings. There's no clear trend indicating that longer preparation times correlate with higher or lower ratings, as data points are widely dispersed across all ranges of preparation time without a distinct pattern. This could imply that the quality of a recipe, as perceived by users, is not strongly dependent on the time required to prepare it.

## Interesting Aggregates

To further analyze the recipes by preparation time, we can group them into different time intervals and then calculate the average rating for each interval. Additionally, we can look at the average number of ingredients and steps to see if there's a correlation with preparation time. This will give us a DataFrame where we can see if, on average, recipes with certain preparation times have higher or lower ratings, more or fewer ingredients, and more or fewer steps.

| Time Interval   |   Average Rating (mean) |   Number of Ingredients (mean) |   Number of Steps (mean) |
|:----------------|------------------------:|-------------------------------:|-------------------------:|
| 0-30 min        |                 4.64929 |                        7.42541 |                  7.09579 |
| 30-60 min       |                 4.61215 |                        9.85042 |                 10.9578  |
| 60-90 min       |                 4.61778 |                       10.7028  |                 12.5433  |
| 90-120 min      |                 4.63058 |                       11.4986  |                 14.5059  |
| 120-150 min     |                 4.62522 |                       10.3669  |                 13.0261  |
| 150+ min        |                 4.6318  |                       11.8288  |                 15.958   |

The resulting DataFrame shows that:
**Average Rating (mean):** Ratings are relatively consistent across all time intervals, suggesting that the length of time to prepare a recipe does not have a significant impact on how users rate them. The slight variation in ratings is probably not statistically significant.
**Number of Ingredients (mean):** There is a general upward trend in the number of ingredients as the preparation time increases, which could indicate that recipes requiring longer preparation times are more complex and involve more components. However, this trend appears to plateau and even slightly reverse for recipes taking over 120 minutes, which may suggest a point of diminishing returns in terms of ingredient variety.
**Number of Steps (mean):** Similar to the number of ingredients, there is an upward trend in the number of steps with longer preparation times. This trend continues steadily even past the 120-minute mark, suggesting that recipes with longer prep times are more complex in terms of the cooking process, not just in the variety of ingredients.


---

# Assessment of Missingness

## NMAR Analysis

In the context of missing data, NMAR (Not Missing At Random) refers to a situation where the missingness is related to the reason the data is missing.
Based on the descriptions we have of our dataset, a likely candidate for NMAR could be the “rating” column. For example, users may choose not to leave a rating because they had a negative experience with the recipe. If users are less likely to rate recipes that they had a poor experience with, the missingness in the rating column is related to the unobserved variable, which is the user's satisfaction with the recipe. This unobserved satisfaction is causing the missingness in the ratings, making the data NMAR.
To better explain the missingness and potentially convert the situation to MAR (Missing At Random), where the missingness can be fully explained by observed data, we might want to obtain additional data such as:
-	User engagement metrics (e.g., if the user printed or saved the recipe)
-	User's history of rating other recipes (to see if there is a pattern in their behavior)
-	The time spent on the recipe page (a measure for user interest or intention to cook)
By incorporating these additional data points, we may be able to explain why a rating is missing based on the user's interaction with the recipe, thus providing insight into the missingness and making it MAR, where the probability of missingness is independent of the missing data itself but is related to the observed data.

## Missingness Dependency

In this section, the relationships between the missing values in the average_rating column and n_steps and will be examined in order to provide information that may shed light on the continuation of the research. The determined significance level is 0.05 for both of the tests.

### Average Rating and Preparations Minutes

**Null Hypothesis:** The missingness of average_rating is independent of the preparation minutes (“minutes”) a recipe has.

**Alternative Hypothesis:** The missingness of average_rating is not independent of “minutes”.

**Test Statistic:** The test statistic used here is the absolute difference in mean minutes between the two groups (recipes with missing average_rating vs. those with present average_rating).

Here is the result of the permutation test:

<iframe src="assets/graph4.html" width=800 height=600 frameBorder=0></iframe>

The histogram represents the distribution of permutation statistics for the absolute difference in the mean number of preparation minutes (“minutes”) between recipes with and without an average rating. The observed statistic is marked on the histogram as well. 
In our results, the observed statistic is approximately “1.30”, which is quite far to the right of the distribution of permutation statistics, as shown by the vertical dashed line. Since our p-value is 0.0, it means that none of the permutation statistics were as extreme or more extreme than the observed statistic. This suggests that the observed difference in means is statistically significant, and we can reject the null hypothesis that the missingness of average_rating is independent of “minutes”. So, the missingness of average rating depend on “minutes”. This type of missingness, is referred to as Missing At Random (MAR).
Interpreting this in the context of our data, it implies that there is a statistically significant association between the preparation time of a recipe and the likelihood of its average rating being missing. However, without further context, we can't determine causality or the exact nature of this association—it could be influenced by other factors not accounted for in this analysis.

### Average Rating and Preparations Minutes

**Null Hypothesis:** The missingness of average_rating is independent of the percentage of daily sodium value (“sodium”) a recipe has.

**Alternative Hypothesis:** The missingness of average_rating is not independent of “sodium”.

**Test Statistic:** The test statistic used here is the absolute difference in mean “sodium” between the two groups (recipes with missing average_rating vs. those with present average_rating).

Here is the result of the permutation test:

<iframe src="assets/graph5.html" width=800 height=600 frameBorder=0></iframe>

The permutation test for the missingness of average_rating dependent on sodium content (as a percentage of daily value) shows that the observed statistic is approximately 1.54. This statistic represents the absolute difference in the mean sodium percentage between recipes with missing average_rating and those with a present average_rating.
Given a p-value of approximately 0.47, we do not have strong evidence to reject the null hypothesis. This high p-value indicates that the observed statistic could quite ordinarily occur under the null hypothesis, which states that the missingness of average_rating is independent of a recipe's sodium content. 
Since the p-value is significantly higher than our significance level of 0.05, we conclude that there is no statistically significant evidence that the missingness of average_rating depends on the sodium content of the recipe. This type of missingness, where the missing data is independent of both observed and unobserved data, is referred to as Missing Completely At Random (MCAR).


---

## Hypothesis Testing

### Concerns about Missingness of Hypothesis Data

I want to address a valid concern regarding the missingness of average ratings and its dependence on the preparation time of recipes. Indeed, our permutation tests have revealed that the likelihood of a recipe's rating being missing is not independent of its preparation time. Such a finding may raise questions about the integrity of the analysis that seeks to correlate these very two variables.
However, I would argue that this exact relationship between missing ratings and preparation time underscores the importance of our research. It is precisely because there is a pattern to the missing data that we must delve deeper. If ratings are systematically absent for recipes with certain preparation times, it might suggest an underlying bias in the feedback mechanism. For instance, users may not leave ratings for recipes that are too quick to prepare, perhaps due to perceived simplicity, or for those that are too time-consuming, possibly due to the investment required to make them.
Our objective is not merely to affirm if shorter preparation times correlate with higher ratings, but to understand the full narrative behind recipe ratings. By investigating this relationship, we have the opportunity to reveal and address potential biases in user engagement and feedback. We can uncover whether there is a sweet spot in preparation time that encourages users to share their experiences, or whether extreme preparation times deter them.

### Hypothesis Testing

To address the question of whether there is a significant difference in the distribution of average ratings across various recipe preparation time intervals, we can establish the following hypotheses:
**Null Hypothesis:** The distribution of average ratings is the same across all preparation time intervals. Any observed differences in the distribution of ratings between time intervals are due to random chance.
**Alternative Hypothesis:** The distribution of average ratings differs significantly across at least one pair of preparation time intervals. This implies that preparation time influences the ratings a recipe receives.
**Significance Level:** 0.05 is chosen as it a common level in many researches
**Test Statistic:** The Total Variation Distance (TVD) is an ideal test statistic for our study as it offers a method for comparing the distributions of average ratings across predefined recipe preparation time intervals. 
**Observed TVD:** approximately 0.5
**P-value:** 0.92
Accordingly, we arrive at the following conclusion that the observed TVD suggests there is a difference in the distribution of average ratings across various recipe preparation time intervals. However, the high p-value indicates that the observed difference in distributions is not statistically significant at the significance level 0.05. Therefore, we do not have sufficient evidence to reject the null hypothesis.
Based on this analysis, we conclude that there is no statistically significant evidence to support the claim that recipes with certain amount of preparation times receive higher ratings. The preparation time of the recipes, categorized into intervals, does not seem to have a significant impact on how the recipes are rated. This finding is important as it suggests that factors other than preparation time may be more influential in determining the ratings that recipes receive.


---