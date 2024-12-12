
# Using Generalized Estimating Equations (GEE) to identify lifestyle predictors of Hypertension  
## Project Overview
- **Authors:** Michelle Zong and Zeke Johansson
- This investigation aims to explore the impact of drinking, smoking, exercise, and 
several demographic factors on the likelihood of developing hypertension. It is medically established that smoking and drinking elevate the risk of hypertension, whereas regular exercise reduces it. Based on this knowledge, we hypothesize that smoking will emerge as the strongest predictor, followed by exercise, and drinking; all exhibiting a similar directional effect as is established in medical research.
- **Statistical Methods**: Generalized Estimating Equations (GEE), Quasi-likelihood under the Independence Model Criterion (QIC)
- **Visualizations:** Embedded Box Plots within Violin Plots overlayed with hypertension risk categories, Spaghetti Plots, Aggregate Line Plot, Histograms, Faceted-area plots, Box and Jitter Plots

#### Files and Structure
- `KoreanHealth_Analysis.html`: Knitted R Markdown file post analysis with visualizations. 
- `KoreanHealth_Analysis.pdf`: Knitted R Markdown file post analysis with visualizations. 
- `KoreanHealth_Analysis.Rmd`: Main R Markdown file containing the analysis script and outputs.
- `KoreanHealthRecords/`: Directory containing raw datasets.

#### Dataset Information
- **Source:** [The Korean Genome and Epidemiology Study (KoGES)](https://www.kaggle.com/datasets/junsoopablo/korean-genome-and-epidemiology-study-koges)
- **Description:** This study was conducted by a joint effort of 3 entities in Korean government, 
the National Research Institute of Health (NIH), the Centers for Disease Control and Prevention, and 
the Ministry of Health and Welfare, Korea
- **Sample size:** Longitudinal data, randomly sampled 1,000 Koreans. Each participant was tested and surveyed 5 times over the course of 15 years. In other words, data was collected every 3 years.


## Data Manipulation
The data underwent the following processing steps:

#### Normalization:
- The 5 separate data files, for each year participants were tested and surveyed, was read into R. Column names accross files were normalized and all 5 files were merged into 1 based on column name.
- Various different entries for NA data were normalized.
- Where appropriate, categorical proxies were created (SBP, Age, etc.)
- Date cast into a date type from a string type
- Within the same ID, missing demographic data (sex, EDU, etc.) was filled in horizontally accross the dataset based an existing value

#### Filtering:
- Created a new data subset including only relevant cleaned columns
- Within the subset, excluded observations with missing data
- Data was sorted by ID, and then by date. **This is a necessary step for GEE analysis.** 

#### Resulting Dataset
The resulting dataset used in analysis included the following variables.
- **ID:** Unique numerical identifyer of participant
- **EDate:** Date of data collection
- **Year:** Year of data collection

#### Independent Variables:
- **Drinking Behavior:** 1: Non-drinker, 2: Formal drinker, 3: Regular drinker
- **Smoking Behavior:** 1: Non-smoker, 2: Formal smoker, 3: regular smoker
- **Exercise Behavior:** 0: Does NOT do frequent high-intensity exercise, 1: Does do frequent high-intensity exercise
- **Age:** Continuous integer
- **Age category:** Children under 18, Adults 18 to 39, Adults 40-59, Adults over 60
- **Sex:** 1: Male, 2: Female
- **Education:** 1: Elementary or less, 2: Middle School, 3: High School, 4: Bachelor's Degree, 5: Graduate School

#### Dependent Variable:
- **SBP (Systolic Blood Pressure):** Continuous integer
- **SBP category:** (<= 119) Healthy, (120 - 139) Pre-hypertension, (>=140) Hypertension


## EDA and Visualization
This project conducted the following exploratory data analysis and visualizations. 
- **Violin and Box Plots:** overlayed with hypertension risk levels to show the distribution of dataset accross 3 categories of hypertension risk. Green is healthy (<=119), Orange is pre-hypertensive (=120-139), Red is hypertensive (>=140).
    - **Distributions:** similar across all categories of variables.
    - **Median:** lower SBP for formal and regular smokers and drinkers; lower SBP for those who do high-intensity exercise.
    - **Size:** significantly more non-smokers and non-drinkers than other categories.
- **Spaghetti Plots:** of each individual observation over time, too many observations to interpret anything.
- **Longitudinal Line Graph:** of average SBP within each predictor category over time. 
    - **Note:** Very large decrease in formal drinking in 2008. We speculate it is because of the 2008 financial crisis in Korea which may have meant less business meetings and thus less required drinking. 
- **Histograms:** overlayed by predictor category.
- **Faceted-area Plots:** Not more informative than the violin and box plots.
- **Box and Jitter Plots:** overlayed with hypertension risk levels. Not as informative as violin and box plots.


## Statistical Methods
The project utilized Generalized Estimating Equations (GEE). The following is a desciription of GEE through comparison to Linear and Generalized Linear Models. 
- **Linear model:** Observations are independent identically distributed, Y’s come from normal distribution, mean is a linear function of the covariates (x’s).
- **Generalised linear models:** Observations are iid, y’s have any distribution from exponential family, there could be some function of the mean that gives us the covariates.
- **GEE:** Observations are not independent! They are expected to be correlated in some way. 
No specific distribution of y, just specify a mean-variance structure, some dispersion parameter, and within group correlation structure [R(alpha)]. 
    - GEE is a quasi-likelihood approach/theory since we don’t start with a probability model and no assumption is made about the distribution of Y’s observations; instead we start with the likelihood equations with the specified correlation structure (in our case, within ID’s).
    - GEE needs a lot of clusters, but a small size of cluster, which is why it was a good fit for our data that has cluster size 5 and around 1,000 clusters. 

#### Measuring Goodness of Fit with QIC
The *Quasi-likelihood under the Independence Model Criterion (QIC)* was used to assess the relative fit of different GEE models on the same data set. 4 types of correlation matrices (Exchangeable, Independent, AR1, Unstructured) through the QIC analysis and compared. A lower QIC, or the smallest measure, indicates a better fit, which was the *Independent* correlation matrix. 


## Results Summary
- No significant results from lifestyle factors (smoking, drinking, exercise) on SBP.
- Very low p-value (very high significance) for Age and Education.
    - **Age:** For a one year increase in age there is an increase of 0.40 in SBP (p-value: <2e-16).
    - **Education:** For every categorical increase in education, there is an incremental decrease in SBP, compared to EDU1 (elementary school or less). Graduate school decreases systolic by almost 10 compared to elementary school education level (p-value: 0.00100).

#### Future Work
- Perform deeper exploration into causal relationships. Correlation does not mean causation. Is education the best preventative medicine against hypertension? There’s still so much to look into with this dataset. How do health behaviors correlate with education level? Is education a proxy for exercise? Is education enough to mitigate the negative health effects of smoking and drinking?


## General Information
#### Requirements
- R
- RStudio (recommended)
- Required packages: `tidyr`, `ggplot2`, `viridis`, `thematic`, `tab`, `tidyverse`, `geepack`, `dplyr`, `sjmisc`, `psych`

#### How to Run the Analysis
1. Fork the repository.
   ```bash
   git fork <repository_url>
   ```
2. Open `KoreanHealth_Analysis.Rmd` in RStudio.
3. Update the setwd filepath to one that matches your local directory.
4. Run the R Markdown file to generate the analysis report.

#### Contributions
Contributions and suggestions are welcome. Please open an issue or submit a pull request for any enhancements or corrections.
