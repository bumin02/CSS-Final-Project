# CSS Final Project

<body style="background-color:#1F1F1F;">


<!-- 1. A **demographic parity table** was 

    Confusion Matrix -->
<!-- $$ Accuracy = {TP + TN \over TP + FP + TN + FN} $$ -->

## Part 1

<span style="color:skyblue">**Demographic Parity Table Code**</span>

<span style="color:white">A demographic parity table is a data analysis tool used to examine whether a particular outcome (the treatment variable "T") is distributed equitably across various demographic groups. Below is the code and respective output of a demographic parity table performed on data_2023.csv.</span>

```python
import pandas as pd
import numpy as np

file_path = 'data_2023.csv'
data = pd.read_csv(file_path)

column_names = ['Gender', 'Region', 'Education', 'HouseholdMembers', 'Treatment', 'ignore']
data.columns = column_names

# ignore last column for now
data.drop(columns=[data.columns[-1]], inplace=True)

# group by category and calculate treatment rate
gender_treatment_rate = data.groupby('Gender')['Treatment'].mean()
region_treatment_rate = data.groupby('Region')['Treatment'].mean()

# categorize education
bins = [4, 13, 17, 23]  # education ranges from 4 to 22 years
labels = ['Primary_Secondary', 'HigherSecondary_Undergrad', 'Postgrad_Above']
data['EducationLevel'] = pd.cut(data['Education'], bins=bins, labels=labels, right=False)
education_treatment_rate = data.groupby('EducationLevel')['Treatment'].mean()

# categorize household size
bins = [1, 5, 9, 13]  # household size ranges from 1 to 12 members
labels = ['Small', 'Medium', 'Large']
data['HouseholdSize'] = pd.cut(data['HouseholdMembers'], bins=bins, labels=labels, right=False)
household_treatment_rate = data.groupby('HouseholdSize')['Treatment'].mean()
```
\
<span style="color:skyblue">**Code Output**</span>
| Demographic Group | Treatment Rate |
|-------------------|----------------:|
| <span style="color:pink">**Gender**</span>        |                |
|   Female (1)        | 40.7%       |
|   Male (0)          | 41.3%       |
| <span style="color:pink">**Region**</span>         |                |
| Metropolitan (1) | 54.7%       |
| Non-Metropolitan (0)  | 9.3%           |
| <span style="color:pink">**Education Level**</span>  |              |
| Primary to Secondary (4-12 years)   | 37.9%          |
| Undergraduate (13-16 years) | 46.6%        |
| Postgraduate (17-22 years)| 52.3%          |
| <span style="color:pink">**Household Size**</span>  |               |
| Small (1-4 members)       | 41.0% |
| Medium (5-8 members)       | 40.7% |
| Large (9-12 members)       | 50.5% |

\
<span style="color:skyblue">**Interpretation**</span>

- <span style="color:pink">**Gender**</span>: Treatment rates between males and females are quite similar, with a slight difference (41.3% for males vs. 40.7% for females).
- <span style="color:pink">**Region**</span>: There's a significant disparity between metropolitan (54.7%) and non-metropolitan areas (9.3%). This suggests potential regional bias in treatment selection.
- <span style="color:pink">**Education Level**</span>: There is a noticeable increase in treatment rate with higher education levels (37.9% $\rightarrow$ 46.6% $\rightarrow$ 52.3%).
- <span style="color:pink">**Household Size**</span>: Treatment rates are fairly consistent for small and medium households though larger households experience 10% more treatment allocation.

Therefore, it may be plausible for us to conclude that there may be systematic bias especially for the <span style="color:pink">**region**</span> demographic, but there are a few problems we must address:
* <span style="color:yellow">**Oversimplification:**</span> DPT does not account for the underlying reasons why some groups may have higher or lower rates of the outcome. It also looks at the demographic characteristics individually instead of holistically, which fails to observe the combined effects of characteristics on treatment allocation (e.g. non-metropolitan and woman).
* <span style="color:yellow">**Demographic parity does not imply causation:**</span> Similar rates across groups do not mean the process is fair, nor do different rates prove bias; they merely suggest we should look further.
* <span style="color:yellow">**... Which implies the importance of pairing with other methods:** </span>We could potentially pair the demographic parity table analysis with a logistic regression to help infer causal relationships, determine statistical significance, and potentially model for predicting treatment outcomes based on new demographic information. 

\
<span style="color:skyblue">**DPT Quantitative Clarification**</span>
\
For each unique demographic $G$, we calculate the treatment rate given to the sample:

<!-- $$ R_G = { | \space G \space | \space T=1 \space | \over | \space G \space |} $$ -->

<!-- ```math
 R_G = N_G, T=1 / N_G
 ``` -->
$$R_G = { N_{G, T=1} \over N_G}$$

Where, 
- $R_G :$ The rate at which the treatment was given to individuals in a particular group $G$
- $N_{G, T=1} :$ The number of individuals in group $G$ who were given the treatment
- $N_G :$ Total number of individuals in group $G$

Upon calculating $R_G$ for each demographic, we can assess the fairness of the treatment allocation by looking for large disparities that exist within $G$. However, what does "fairness" mean and what quantitative methods can we employ to test the statistical significance of a disparity?
1. <span style="color:yellow">**Confirm bias with statistical significance testing or other models**</span>: Conduct statistical tests (such as chi-square tests or z-tests for proportion differences) to determine whether observed differences in treatment rates across demographic groups are statistically significant or could have occurred by chance.
2. <span style="color:yellow">**Analyze confounding variables**</span>: For instance, in the disparity between metropolitan and non-metropolitan demographics, we must consider confounding factors such as differences in SES and trending health conditions. Would adjusting the treatment accordingly fit under "fair" treatment? (internal validity)

We obtain an average demographic parity of $0.41497$, calculated with:
$$\bar{x} = \displaystyle {\sum_i R_{G_i} \over N}$$

![\Large x=\frac{-b\pm\sqrt{b^2-4ac}}{2a}]