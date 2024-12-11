<h1 class="custom-title">An Overlooked Metric: How Vision Score Impacts Winning in League of Legends</h1>

A UCSD DSC80 project focused on analyzing the impact of vision score in League of Legends

## Introduction

Welcome to the world of Vision Score! League of Legends (LOL) is a massively popular multiplayer online battle game (MOBA) with millions of players across the globe. With so many players actively playing the game League of Legends provides us Data Scientists with a treasure trove of data for us to work with and to engineer and analyze many different dynamics of the game.

League of Legends is a highly competitive game, where players naturally strive to secure victory. In each game, two teams of five players face off, aiming to destroy the opposing team’s central base, known as the Nexus.This brings us to the question, what is the most effective strategy to achieve this goal? League of Legends isn’t just about racking up kills—victory often hinges on less obvious factors like Vision Score. This crucial yet often overlooked metric helps teams gather vital map information, making it easier to track enemy movements and objectives, which is essential for strategic gameplay. That said, how crucial is Vision Score when it comes to securing victory? **To answer this question, I conducted a detailed data analysis to uncover the impact of Vision Score across different roles and its significance in shaping the outcome of a match.**

For my analysis, I used the 2022 League of Legends Esports Stats dataset from Oracle’s Elixir, a platform offering many different advanced statistics for the game. This dataset includes detailed performance data from thousands of professional matches across various tournaments, providing insights into players, teams, and outcomes.

# Introduction of Dataset and Columns

Our original dataset contained **150,180 rows** and **161 columns**, however I cleaned the dataset to only include relevant information that is needed for our analysis. The resulting data frame consists of **150,168 rows** and **13 columns**
The table below shows the columns and their definitions.

 Column Name        | Definition                                |
:-------------------|:------------------------------------------|
`gameid`            | game ID                                   |
`position`           | player's position                         |
`result`             | outcome of the match (1 for 1 0 for loss) |
`league`             | league tournament where it took place     |
`vspm`               | visions core per minute                    |
`visionscore`        | total vision score                        |
`controlwardsbought` | amount of control wards bought            |
`wardskilled`        | total wards killed                        |
`wpm`                | wards placed per minute                   |
`wardsplaced`        | wardsplaced                               |
`gamelength`         | length of the game (minutes)              |
`pentakills`         | number of pentakills              |


# Data Cleaning and EDA (Exploratory Data Analysis)

## Data Cleaning

To clean and preprocess the data, my first step was to first identify missing values in the data. I discovered that for the columns related to vision like `visionscore` and `vspm` had 12 missing values when the `datacompletness` was "partial" and when the specific game id was `8479-8479_game_1`. Given that this represents a very small subset of the data, I opted for listwise deletion to remove these rows before proceeding to the next steps.


Next, I created a new column to assess vision efficiency by calculating the average team vision score per minute `vspm` for each `teamid`. Using this column, I then added a binary above mean column to indicate whether a team's vision score was above `1` or below `0` the mean.

After I also encoded the result column to make 1s represent `Win` and 0s represent `Loss`

I then standardized all numerical columns using a custom helper function with StandardScaler to ensure the data was properly scaled for analysis.

After completing the data cleaning process, I divided the main dataset into two subsets: one containing team-level data (when `position == team`) and the other containing player-level data (`position != team`).
The first few rows of the combined dataframe are down below:
<div style="overflow-x: auto; max-width: 100%;">
  <table>
    <thead>
      <tr>
        <th>gameid</th>
        <th>datacompleteness</th>
        <th>position</th>
        <th>result</th>
        <th>league</th>
        <th>vspm</th>
        <th>visionscore</th>
        <th>controlwardsbought</th>
        <th>wardskilled</th>
        <th>wpm</th>
        <th>wardsplaced</th>
        <th>gamelength</th>
        <th>above_mean</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td>ESPORTSTMNT01_2690210</td>
        <td>complete</td>
        <td>top</td>
        <td>Loss</td>
        <td>LCKC</td>
        <td>-0.635957</td>
        <td>-0.641883</td>
        <td>-0.563322</td>
        <td>-0.542525</td>
        <td>-0.708221</td>
        <td>-0.698117</td>
        <td>-0.53657</td>
        <td>-1.05895</td>
      </tr>
      <tr>
        <td>ESPORTSTMNT01_2690210</td>
        <td>complete</td>
        <td>jng</td>
        <td>Loss</td>
        <td>LCKC</td>
        <td>-0.29221</td>
        <td>-0.35334</td>
        <td>-0.487438</td>
        <td>0.219722</td>
        <td>-0.775999</td>
        <td>-0.75545</td>
        <td>-0.53657</td>
        <td>-1.05895</td>
      </tr>
      <tr>
        <td>ESPORTSTMNT01_2690210</td>
        <td>complete</td>
        <td>mid</td>
        <td>Loss</td>
        <td>LCKC</td>
        <td>-0.589075</td>
        <td>-0.602536</td>
        <td>-0.411555</td>
        <td>-0.479004</td>
        <td>-0.335154</td>
        <td>-0.382781</td>
        <td>-0.53657</td>
        <td>-1.05895</td>
      </tr>
      <tr>
        <td>ESPORTSTMNT01_2690210</td>
        <td>complete</td>
        <td>bot</td>
        <td>Loss</td>
        <td>LCKC</td>
        <td>-0.65157</td>
        <td>-0.654999</td>
        <td>-0.639205</td>
        <td>-0.542525</td>
        <td>-0.572569</td>
        <td>-0.583449</td>
        <td>-0.53657</td>
        <td>-1.05895</td>
      </tr>
      <tr>
        <td>ESPORTSTMNT01_2690210</td>
        <td>complete</td>
        <td>sup</td>
        <td>Loss</td>
        <td>LCKC</td>
        <td>0.0358807</td>
        <td>-0.0779132</td>
        <td>-0.108022</td>
        <td>-0.03436</td>
        <td>0.00402423</td>
        <td>-0.0961115</td>
        <td>-0.53657</td>
        <td>-1.05895</td>
      </tr>
    </tbody>
  </table>
</div>

       



## Univariate Analysis
"For our univariate analysis, I focused on the vision score distributions of `vspm` (Vision Score Per Minute) for junglers and the entire team. To create these plots, I first filtered the data for jungle-specific and team-wide entries in the `position` column, then extracted the vspm column for analysis."

<iframe
  src="assets/VSPM_JNG.html"
  width="750"
  height="500"
  frameborder="0"
></iframe>

<iframe
  src="assets/VSPM_All.html"
  width="750"
  height="500"
  frameborder="0"
></iframe>

The histograms for all roles reveal a distinct right-skewed distribution, which aligns with domain knowledge from League of Legends. This skewness can be attributed to the responsibilities and playstyles associated with specific roles. Roles such as jungle and support, for instance, place a higher emphasis on **map control** and **vision management**, as these are critical to their team's strategic success which contributes to their higher vision stats.


## Bivariate Analysis

In this section, I consolidated the average z-scores for all positions into a single bar graph to facilitate easier comparisons across roles. Additionally, I created overlay histograms to provide a deeper analysis of the underlying distributions, enabling a more comprehensive understanding of how each role's performance metrics vary.

<iframe
  src="assets/mean_z.html"
  width="750"
  height="500"
  frameborder="0"
></iframe>

The bar graph above displays the average z-scores for each position across key vision-related metrics, including `vision score per minute` (vspm), `wards placed per minute` (wpm), `wards killed`, and `controlwardsbought`. The x-axis represents the positions, and the y-axis indicates the average z-scores. 

From the visualization, we can see that the support role consistently exhibits the highest average z-scores, particularly excelling in metrics like `wpm`, highlighting its critical role in map control and team vision. In contrast, top lane and mid lane show the lowest average z-scores across these metrics, reflecting their focus on individual lane control and resource accumulation rather than team-oriented vision contributions. 

Junglers, however, fall somewhere in the middle, as maintaining awareness of the enemy jungler’s location is crucial for their role. Notably we cab see that the mean z-score for wards killed for junglers is higher than for supports which is attributed to the fact that their role in traversing the whole map multiple times throughout the game. This wider coverage of the map naturally leads to more opportunities for destroying enemy wards. This side-by-side comparison provides a clear understanding of how each role contributes to vision-based objectives in League of Legends.

## Interesting Aggregates

To delve deeper into the distribution of vspm (Vision Score Per Minute) and its relationship with player performance, we plotted overlaid histograms of all players' vspm values and a pivot table representing average z-score of `vspm` of all positions that win or lose.

<iframe
  src="assets/all_mean.html"
  width="700"
  height="500"
  frameborder="0"
></iframe>

This visualization provides valuable insight into the distribution of `vspm` (Vision Score Per Minute)  among players, highlighting differences in performance relative to the mean. The histogram reveals that there are noticeably more players with a vision score above the mean compared to those below, suggesting a skewed distribution favoring higher performance in vision-related metrics.

<iframe
  src="assets/all_mean.html"
  width="700"
  height="500"
  frameborder="0"
></iframe>

Building on the previous plot, this visulization shows us the `vspm` (Vision Score Per Minute) of all players, categorized by win or loss. We observe that players who win tend to higher higher `vspm`. Additionally, the shapes between this plot and the previous plot are similar which can indicate that for players with a `vspm` above they are more likly to win.

The table below represents an aggregate of the vspm (Vision Score Per Minute) z-score distributions for each position, categorized by whether the players win or lose:

| result   |       top |       jng |       mid |       bot |       sup |
|:---------|----------:|----------:|----------:|----------:|----------:|
| Loss     | -0.600672 | -0.450003 | -0.571546 | -0.529343 | 0.0119695 |
| Win      | -0.55555  | -0.385083 | -0.508304 | -0.443249 | 0.100207  |

In order to generate this table, I created a pivot table with:
- **Values:** `visonscore`
- **Index:** `result` (Win/Loss)
- **Columns:** `position` (top, jng, mid, bot, sup)
- **Aggregation function:** Mean

The pivot table reveals that the `visonscore` z-scores are generally higher for players who win compared to those who lose, across all positions, fortifying the previous asumptions. Notably:
- Support players exhibit a positive `visonscore` z-score in both wins and losses, with a significant increase in wins.
- The jungle role shows the largest improvement in `visonscore` from losses to wins.
- Across all positions, winning players tend to achieve closer-to-average or above-average vision scores.

# Assessment of Missingness

## NMAR Analysis

I suspect that the missing values in the `ban5` column of our dataset are not missing at random (NMAR). The absence of values in this column does not appear to follow any identifiable pattern, suggesting that some teams may have either chosen not to ban a fifth champion or forgotten to do so. This implies that the missingness in ban5 depends on the actual value of the missing data itself, which is characteristic of NMAR missingness.

## Missingness Dependecy

Looking throughout the dataset I was particuarally intered in the column `pentakills`. I noticed that a lot of these values were missing, which lead me to decide to test the missingness dependecy of this column.

The first column I decided to test  missingness aginast was the `gamelength` column

**Null Hypothesis**: Distribution of `'gamelength'` when `'pentakill'` is missing is the same as the distribution of `'gamelength'` when `'pentakill'` is not missing.

**Alternative Hypothesis**: Distribution of `'gamelength'` when `'pentakill'` is missing is not the same as the distribution of `'gamelength'` when `'pentakill'` is not missing.
- Test statistic: Absolute Difference in Means
- Significance level: 5% (0.05)

We performed a permutation test to determine whether or not the missingness of `pentakill` was dependent on `gamelength` using Aboslute Difference in Means as our test statistic. The plot below illustrates the empiricall distirbution of the Absolute Difference in means, where are observed value is the red vertical line.

**Results:**
- **Observed**: 0.0
- **P-value**: 0.0
<iframe
  src="assets/missing_reject1.html"
  width="700"
  height="500"
  frameborder="0"
></iframe>

We caluclaed our p-value and then compared it agianst the observed Absolute Difference in Means through permuation testing, and the resulting p-value I got was 0. Since our p-value is less than our set significance level of 0.05, it leads us to **reject** the null hypothesis and therefore it is highly likely that the missingness in `pentakill` depends on `gamelangth`

The second column I chose to test missingness on `result`.

**Null Hypothesis**: Distribution of `result` when `pentakill` is missing is the same as the distribution of `result` when `pentakill` is not missing.

**Alternative Hypothesis**: Distribution of `result` when `pentakill` is missing is not the same as the distribution of `result` when `pentakill` is not missing.
- Test statistic: Total Variation Distance (TVD)
- Significance level: 5% (0.05)

We performed a permutation test to determine whether or not the missingness of `pentakill` was dependent on `result` total variation distance (TVD) as our test statistic. The plot below illustrates the empircal distirbution of the TVDs, where are observed value is the red vertical line.

**Results:**
- **Observed**:0.0
- **P-value**: 1.0
<iframe
  src="assets/missing_fail_reject1.html"
  width="700"
  height="500"
  frameborder="0"
></iframe>

By comparing the TVDs through permuatation and the observed TVD I found that the resulting p-value was 1.0 which is greater than our significance level of 0.05. This means that we must **fail to reject** the null and conclude that the missingness in `pentakill` is most likely not dependent on `result`



## Hypothesis Testing

After conducting our exploratory data analysis, we chose to answer the general question: Is there a statistically significant association between the vspm values and the result column in the dataset?


**Null Hypothesis**: The vision score per minute distributions on winning and losing teams are not significantly different.

**Alternative Hypothesis**: The vision score per minute (vspm) distributions for winning and losing teams are significantly different.

- Test statistic: Difference in Means
- Significance level: 5% (0.05)

We used the difference in means as our test statistic for this test because we are comparing the average vision score per minute (vspm) between winning and losing teams. This test is suitable for continuous variables like `vspm`. We chose the most commonly used significance level, 5% (0.05), to determine statistical significance. A p-value less than 0.05 would indicate strong evidence against the null hypothesis, meaning there is less than a 5% probability of observing a difference as or more extreme than the observed result by chance.

Below is the resulting histogram representing the distribution of our test statistic during our permutation test.

**Results:**
- **Observed**:0.079
- **P-value**: 0.0
<iframe
  src="assets/hypothesis_test.html"
  width="700"
  height="500"
  frameborder="0"
></iframe>

Based on the permutation test with a p-value of 0.0, we **reject** the null hypothesis. This suggests that the distribution of vspm between winning and losing teams is significantly different, leading us to conclude that winning/losing has an impact on vspm.

# Framing a Prediction Problem

## Prediction Problem: Can we predict the vision scores of a player based on post-game statistics?

**Problem Type:** Regression

The task is to predict the **vision scores** of a player based on their post-game statistics. Since the vision score is a continuous numerical value, this is a **regression problem**.

### Response Variable

- **Variable:** Vision Score
- **Reason for Choice:** Vision score represents a player's ability to perceive and manage the game environment on the map, which is a crucial in **League of Legends**. 
Predicting this score helps in understanding how effectively players use vision, an important skill for strategic gameplay.


### Evaluation Metrics

1. **Root Mean Squared Error (RMSE):**
   - RMSE is chosen as a metric because it provides an interpretable measure of the model's prediction error in the same units as the vision score. A lower RMSE indicates better model performance.

2. **R-squared (R²) Score:**
   - R² is used to evaluate how well the model explains the variance in the vision score. It represents the proportion of the variance in the vision score that is predictable from the post-game statistics. A higher R² value indicates a better model fit.

3. **Mean Bias Error (MBE):**
   - **Mean BiasE (MBE)** Measures the average difference between the predicted values and the true values. It helps identify if the model tends to over-predict or under-predict systematically. 

**Why these metrics?**
- RMSE is preferred because it provides a direct measure of prediction error, which is important for regression tasks.
- R² is useful for measuring how well the model is fit.
- MB helps measures over-prediction or under-prediction and shows whether there is bias in your predictions.
- Metrics like **Accuracy** and **F1 Score** is not suitable in this case because it is a classification metric, which is not applicable to regression problems.

# Baseline Model

For our baseline model, we used **Lasso regression** to predict the **vision score** of a player. The model was trained on the following dataset:

- **Target Variable**: `visionscore` (the vision score of the player)
- **Features**: 
  - Numerical features: `gamelength`(Quatative, discrete), `wardsplaced`(Quantitive, nominal)

### Model Performance and Results
- **Train R²**: 0.8136
- **Test R²**: 0.8122
- **RMSE**: 0.1461
- **MBE**: -0.0022

### Model Interpretation

- The **R² score** for both the **training** and **test** sets is quite high (around 0.8136), although there is still room for improvement
  
- The **RMSE** of **0.1445** shows that the model’s predictions are relatively close to the actual values, with an average error of 0.1445 units.

- The **MBE** of **-0.0022** suggests that the model has very minimal bias, indicating that the predictions are well-calibrated with no significant over- or under-prediction.
## **Conclusion**
This baseline Lasso regression model demonstrates strong performance in predicting vision scores, with high R², low RMSE, and minimal bias. While these results are promising, the model can still be improved by exploring additional features or optimizing hyperparameters

# Final Model

For our final model, we refined the previous Lasso regression model by including additional features and optimizing the hyperparameter **alpha** using **cross-validation**. The relevant features were expanded to include:

- **Features Added**:
  - `controlwardsbought`: Quantitative, discrete
  - `wardskilled`: Quantitative, discrete
  - `wpm` (wards per minute): Quantitative, continuous
  - `result` (outcome of the game): Categorical, nominal

## **Rationale for Feature Selection**

The additional features were chosen based on their relevance to vision score, as these variables are directly tied to a player’s contribution to vision control and strategy during the game:

- **`controlwardsbought` and `wardskilled`**: These metrics capture a player’s active participation in vision control by buying control wards and destroying wards, which are critical for improving the vision score.
- **`wpm` (wards per minute)**: This feature accounts for the efficiency of ward placement over the game duration which gives us normalized measure of vision contribution.
- **`result` (outcome of the game)**: From previous analysis, players who win have a higher vision score on average compared to those who lose.

By incorporating these features, the model captures imporant aspects of a player's vision-related performance, making the predictions more comprehensive and accurate.

The model was trained on the following dataset:

- **Target Variable**: `visionscore` (the vision score of the player)
- **Features**: 
  - Numerical features: `gamelength`, `wardsplaced`, `wardskilled`, `controlwardsbought`, `wpm`
  - Categorical features: `result`, `position`
  
We applied **one-hot encoding** to the categorical features and used **Lasso regression** with cross-validation to determine the optimal alpha parameter.


### Model Performance and Results

- **Best Alpha**: 0.0002
- **Train R²**: 0.9315
- **Test R²**: 0.9309
- **RMSE**: 0.0886
- **MBE**: -0.0022

### Model Interpretation

- **R² Scores**: Both the **training** and **test** R² scores are very high (around 0.931), indicating that the model explains a significant proportion of the variance in the vision score predictions. This represents a **substantial improvement** over the baseline model.
  
- **RMSE**: The **RMSE** of **0.0886** shows that the model's predictions are very close to the actual values, with an average error of only 0.0886 units, reflecting a high level of accuracy.

- **Mean Bias Error (MBE)**: The **MBE of -0.0022** indicates that the model is essentially unbiased, with very minimal deviation from the actual values in terms of prediction.

## **Conclusion**

The final Lasso regression model significantly improves upon the baseline model. The high **R² scores**, low **RMSE**, and minimal **MBE** suggest that the model is highly effective in predicting the vision score based on game-related statistics and features. The incorporation of additional features like `controlwardsbought`, `wardskilled`, and `wpm` helped improve model accuracy, and the hyperparameter tuning using cross-validation led to better generalization.

This model represents a robust predictive tool for estimating a player's vision score and can be further improved by exploring additional features or refining the model further.

# Fairness Analysis

Fairness analysis refers to the process of assessing whether a predictive model is biased or not against certain groups in a population. To evaluate the fairness of our final model, we assigned Group X to represent players in the `jungle` position and Group Y to represent players in the `support` position. We set up a null and alternative hypothesis to explore the possibility of bias:

- **Null hypothesis**: Our model is fair. The RMSE for the jungle position is roughly the same as the RMSE for the support position, and any differences are due to random chance.
- **Alternative hypothesis**: Our model is unfair (biased). The RMSE for the jungle position is lower than the RMSE for the support position.

For our fairness analysis, we chose RMSE as our evaluation metric, with the test statistic being the difference in RMSE between jungle and support predictions. Our significance level was set at 0.05, and after running 1,000 permutation tests, we got the following results:

- **Observed RMSE Difference**: -2.4441
- **P-value**: 1.0000

<iframe
  src="assets/fairness.html"
  width="750"
  height="500"
  frameborder="0"
></iframe>

### **Conclusion**
Based on the resulting p-value of 1.00 being greater than our significance level of 0.05, we fail to reject the null hypothesis. We do not have enough evidence to conclude that our model is unfair and that its RMSE for the jungle position is lower than its RMSE for the support position. Our conclusion indicates that the model appears fair, and any observed differences in RMSE are likely due to random chance.




