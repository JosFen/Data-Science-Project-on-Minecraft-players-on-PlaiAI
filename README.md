# Analyzing Minecraft Player Engagement: Predicting High Data Contributors with KNN

As part of a research project led by a UBC Computer Science group, data is being collected from a Minecraft server to understand player behavior. The research team faces an important question: *Which types of players are most likely to contribute substantial amounts of gameplay data?* This is crucial for resource planning and recruitment, as understanding which players generate the most data allows for better targeting of efforts and more effective management of server resources. In this post, we describe how we used machine learning, specifically K-Nearest Neighbors (KNN), to predict which players would contribute the most data, based on their demographics and in-game behaviors.

## Problem Overview: Targeting High Data Contributors

The problem we set out to solve was to identify characteristics of players most likely to contribute large amounts of gameplay data. With this knowledge, the research group can better plan for future recruitment campaigns, target resources more effectively, and ensure the server can handle a growing number of active users. We hypothesized that certain demographics, like age, experience level, and subscription status, might correlate with higher playtime and therefore greater data contributions.

## Dataset Overview: Players and Sessions

We worked with two main datasets, `players.csv` and `sessions.csv`, each capturing different aspects of player activity.

- **Players Data (196 rows, 9 variables)**: This dataset contained player-level information, including:
  - `experience`: the player's experience level (e.g., "Amateur", "Regular").
  - `subscribe`: whether the player subscribed to email updates.
  - `gender`: the player's gender.
  - `played_hours`: total playtime of the player.
  - `age`: the player's age.
  
  Key data points like `hashedEmail` helped link player actions across datasets.

- **Sessions Data (1535 rows, 5 variables)**: This dataset recorded detailed session-level information, such as:
  - `start_time` and `end_time`: timestamps of when a session started and ended.
  - `original_start_time` and `original_end_time`: numeric timestamps to calculate session durations.

Upon cleaning the data, we focused on aggregating session data to compute each player's total playtime. We identified discrepancies between total hours played in the `players.csv` and `sessions.csv` datasets, and opted to use the `played_hours` from `players.csv` as it was more reliable. We also noticed issues with missing values in columns like `individualId` and `organizationName`, and handled this by removing those variables from our analysis.

## Data Wrangling and Exploration

Data wrangling is crucial in transforming raw data into a useful format. To prepare for the KNN model, we performed several steps:

1. **Handling Missing Data**: We removed irrelevant variables (`individualId`, `organizationName`) with excessive missing data.
2. **Summarizing Player Behavior**: We aggregated total playtime (`played_hours_sum`) and session counts (`sessions_played`) by player (`hashedEmail`).
3. **Transforming Variables**: We converted categorical variables like `experience`, `gender`, and `subscribe` into factors suitable for KNN classification.
4. **Creating a Binary Target**: To classify players into high or low contributors, we created a binary column `high_contribution`, setting a threshold at the mean of `played_hours` (players with hours above the mean were labeled as "high contributors").

Once the data was cleaned and transformed, we performed some exploratory data analysis (EDA) to visualize the key relationships.

## Exploratory Data Analysis

Exploratory analysis allowed us to uncover patterns and better understand the data before applying machine learning models.

1. **Playtime Distribution**: A histogram of total playtime (`played_hours`) showed that most players contributed very little playtime, with a small group contributing significantly more hours. This confirmed that there was a long-tail distribution, with a few high contributors and many low ones.

   *Figure 1: Distribution of Total Playtime*

2. **Sessions vs. Playtime**: A scatterplot of `sessions_played` vs. `played_hours` (log-transformed for clarity) revealed a strong positive relationship: more sessions generally led to more total playtime. This indicated that players with higher engagement (more sessions) were more likely to contribute large amounts of data.

   *Figure 2: Sessions Played vs. Total Playtime*

3. **Demographics vs. Playtime**: We also examined how different demographic factors influenced playtime. Key findings include:
   - **Experience Level**: Players who identified as "Amateur" or "Regular" tended to log more hours, suggesting a correlation between experience and playtime.
   - **Subscription Status**: Players who subscribed to the server's email updates contributed significantly more playtime compared to non-subscribed players.
   - **Age and Gender**: Younger players (under 30) were more likely to contribute more data. Males generally had higher playtime, though females were also well represented among the higher playtime contributors.

   *Figure 3: Playtime by Age and Gender*

## KNN Classification: Predicting High Contributors

To address the core question—*which players are likely to contribute the most data?*—we applied K-Nearest Neighbors (KNN) classification. KNN is a non-parametric algorithm that classifies players into "high" or "low" contributors based on their similarity to others in the dataset.

### Steps in our KNN Analysis:

1. **Feature Scaling**: Since KNN relies on distance metrics, we standardized numerical features like `age` and `played_hours` to ensure they were on the same scale.
2. **Model Tuning**: We performed cross-validation to select the optimal number of neighbors (`k`) for our model. After experimenting with several values of `k`, we found that `k = 5` provided the best balance between model complexity and accuracy.
3. **Model Evaluation**: The model's performance was evaluated using accuracy, precision, and recall. The best model achieved an accuracy of **78%** in predicting high data contributors.

## Results: Insights from the Model

The KNN model provided useful insights into the factors that predict high data contributors:

1. **Experience**: Players categorized as "Amateur" or "Regular" contributed the most hours, while "Veterans" and "Pros" contributed less than expected. This was a surprising finding, as we expected more experienced players to be more engaged.
2. **Subscription**: Subscribed players were more likely to be high contributors, suggesting that email engagement boosts player participation.
3. **Age**: Players aged 14–25 were most likely to contribute significant data, confirming that younger players tend to spend more time on the server.
4. **Gender**: Males generally contributed more data, though females were notably present among the highest contributors.

## Caveats and Limitations

While the results were insightful, several caveats should be considered:

1. **Class Imbalance**: The dataset was highly imbalanced, with many players contributing zero or very few hours. This could have led to biased predictions, especially for the "high contributor" class. We addressed this by oversampling, but the model may still be biased toward predicting low contributors.
2. **Missing Data**: Certain columns (e.g., `individualId`) were removed due to missing values. This could have excluded useful information that might have improved the model’s accuracy.
3. **Arbitrary Threshold**: The binary classification of high vs. low contributors based on the mean of `played_hours` is somewhat arbitrary. A more nuanced approach, such as predicting continuous playtime, could offer a better understanding of player engagement.

## Conclusion: Impact and Future Directions

The analysis reveals that **younger players**, especially those with **amateur or regular experience levels** and those who are **subscribed to updates**, are more likely to contribute significant data. This insight can guide the research group’s recruitment efforts, helping them target the most engaged players for their study. However, the findings also raise some questions, particularly why more experienced players contributed less than expected and how the increasing proportion of female gamers might change engagement patterns in the future.

As the research evolves, future analyses could explore why certain experience levels contribute less data, and how targeted interventions could further increase player engagement.
