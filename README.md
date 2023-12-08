# pandas-dataframe-practice
Practicing using Pandas DataFrames with school and standardized test data.

## The goals of this exercise are to:
- Analyze district-wide standardized test results.\
- Aggregate data to observe any trends in school performance.
- Identify total number of schools, students, budget.
- Calculate average scores.
- Calcultate percentage of students passing.

All code and data visualizations are viewable in "PyCitySchools" folder.

Original data sources are located in the "Resources" folder.

The jupyter notebook is "pandas-dataframe-practice/PyCitySchools/main.ipynb".

## Written Report

Our report covered a total of 15 different schools with 39,170 students and a total budget $24,649,428.00 and we were able to calculate, amongst many data points, average scores, average passing rates, school sizes and budget per individual school and student.

Within this data, there were several categories that we were able to base interesting filters which included the school size, the school type and the budget per student, which we cross-referenced against scores.

A surprising takeaway from this was that there is actually a reverse correlation with amount spent per student and % of overall students passing. However, this makes more sense when you see that the largest schools had the largest total budgets and budget per student.

The smaller size schools directly correlated with higher passing rates which is very interesting insight. Similarly, charter schools have the same correlation. While this could be attributable  to factors such as teaching style and curriculum, our data shows this could be attributable to the fact the class sizes in charter schools are simply smaller.
