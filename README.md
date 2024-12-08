<h1 class="custom-title">League of Legends Vision Score Analysis</h1>



This repository contains the implementation and analysis of a project conducted at UC San Diego. The focus of this project is to investigate the relationship between vision score statistics and team success in League of Legends matches.

## Project Overview

The project follows the Data Science Lifecycle, breaking the analysis into multiple stages:

1. **Introduction and Question Identification**  
   - **Dataset**: A dataset containing detailed match statistics for League of Legends games.
   - **Research Question**: How does the vision score of players or teams correlate with match outcomes?  
   - **Relevance**: Vision score is a critical yet often underappreciated component of gameplay. Understanding its impact on success can offer insights into strategies for competitive play.
   - **Dataset Details**: The dataset contains X rows and Y columns, with relevant columns including:
     - `vision_score`: The total vision score for a player.
     - `team_outcome`: Whether the team won or lost the match.
     - `role`: The role of the player (e.g., ADC, Support, etc.).
   - **Descriptions of Relevant Columns**:
     - **`vision_score`**: A numerical score representing a player's contribution to vision control during the game.
     - **`team_outcome`**: A categorical variable indicating the match result (Win/Loss).
     - **`role`**: A categorical variable indicating the role of the player in the game.

2. **Data Cleaning and Exploratory Data Analysis (EDA)**  
   - Data cleaning involved handling missing values in the `vision_score` column, converting roles to a standardized format, and creating new features such as vision score per minute.
   - Univariate and bivariate analyses were conducted to explore the distribution of vision scores and their relationship with team outcomes and player roles.

3. **Assessment of Missingness**  
   - Analyzed the missingness in `vision_score` and its dependency on `role` and `team_outcome`.
   - Identified that `vision_score` missingness is **not missing at random (NMAR)**, likely influenced by data collection methods.

4. **Hypothesis Testing**  
   - **Hypotheses**:  
     - Null Hypothesis: The average vision score does not differ between winning and losing teams.  
     - Alternative Hypothesis: The average vision score differs between winning and losing teams.  
   - **Results**: Conducted permutation tests and calculated a p-value of X, leading to a conclusion regarding the relationship between vision score and match success.
