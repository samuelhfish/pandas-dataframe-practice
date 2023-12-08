# pandas-dataframe-practice
Practicing using Pandas DataFrames with school and standardized test data.

## The goals of this exercise are to:
- Analyze district-wide standardized test results.
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

## Code Excerpts

```python
# Dependencies and Setup
import pandas as pd

# File to Load
school_data_to_load = "Resources/schools_complete.csv"
student_data_to_load = "Resources/students_complete.csv"

# Read School and Student Data File and store into Pandas DataFrames
school_data = pd.read_csv(school_data_to_load)
student_data = pd.read_csv(student_data_to_load)

# Combine the data into a single dataset.  
school_data_complete = pd.merge(student_data, school_data, how="left", on=["school_name", "school_name"])
school_data_complete.head()

```
<img width="741" alt="Screenshot 2023-12-08 at 2 02 08 PM" src="https://github.com/samuelhfish/pandas-dataframe-practice/assets/125224990/b6271fb3-ed5a-4952-8d71-de60e283843b">

```python
# Several methods for counting
# Isolate variable and calculate the total number of unique schools
school_count = school_data["school_name"].count()
school_count
```

```
School ID      15
school_name    15
type           15
size           15
budget         15
dtype: int64
```
```python
# Calculate the total number of unique schools
school_count = school_data_complete["school_name"].nunique()
school_count

# Another option for calculate the total number of unique schools
school_count = len(school_data_complete["school_name"].unique())
school_count
```
```
15
15
```
```python
# Calculate the total number of students
student_count = school_data_complete.count()
student_count
```
```
Student ID       39170
student_name     39170
gender           39170
grade            39170
school_name      39170
reading_score    39170
math_score       39170
School ID        39170
type             39170
size             39170
budget           39170
dtype: int64
```
```python
# Calculate the average (mean) math score
average_math_score = school_data_complete["math_score"].mean()
average_math_score
```
```
78.98537145774827
```
```python
# Calculate the percentage of students who passeed reading (hint: look at how the math percentage was calculated)  
passing_reading_count = school_data_complete[(school_data_complete["reading_score"] >= 70)].count()["student_name"]
passing_reading_percentage = passing_reading_count / float(student_count) * 100
passing_reading_percentage
```
```
85.80546336482001
```
```python
# Create a high-level snapshot of the district's key metrics in a DataFrame
district_summary = pd.DataFrame({
    "Total Schools": [school_count],
    "Total Students": [student_count],
    "Total Budget": [total_budget],
    "Average Math Score": [average_math_score], 
    "Average Reading Score": [average_reading_score],
    "% Passing Math": [passing_math_percentage],
    "% Passing Reading": [passing_reading_percentage],
    "% Overall Passing": [overall_passing_rate]
    }
)

# Formatting
district_summary["Total Students"] = district_summary["Total Students"].map("{:,}".format)
district_summary["Total Budget"] = district_summary["Total Budget"].map("${:,.2f}".format)

# Display the DataFrame
district_summary
```
<img width="835" alt="Screenshot 2023-12-08 at 2 25 09 PM" src="https://github.com/samuelhfish/pandas-dataframe-practice/assets/125224990/3b7ff675-2249-4107-a8ef-8d71d0eb2def">

```python
# Calculate the total student count per school (Tried to methods on this one.)
school_data_complete.groupby(["school_name"])["Student ID"].count()
```
```
school_name
Bailey High School       4976
Cabrera High School      1858
Figueroa High School     2949
Ford High School         2739
Griffin High School      1468
Hernandez High School    4635
Holden High School        427
Huang High School        2917
Johnson High School      4761
Pena High School          962
Rodriguez High School    3999
Shelton High School      1761
Thomas High School       1635
Wilson High School       2283
Wright High School       1800
Name: Student ID, dtype: int64
```
```python
# Calculate the total student count per school
per_school_counts = school_data_complete["school_name"].value_counts()
per_school_counts
```
```
Bailey High School       4976
Johnson High School      4761
Hernandez High School    4635
Rodriguez High School    3999
Figueroa High School     2949
Huang High School        2917
Ford High School         2739
Wilson High School       2283
Cabrera High School      1858
Wright High School       1800
Shelton High School      1761
Thomas High School       1635
Griffin High School      1468
Pena High School          962
Holden High School        427
Name: school_name, dtype: int64
```
```python
# Full exploration and results in "main.ipynb"
# Calculate the total school budget and per capita spending
per_school_budget = school_data_complete.groupby(["school_name"]).mean()["budget"]
per_school_capita = per_school_budget / per_school_counts

# Calculate the average test scores per school
per_school_math = school_data_complete.groupby(["school_name"]).mean()["math_score"]
per_school_reading = school_data_complete.groupby(["school_name"]).mean()["reading_score"]

# Calculate the number of schools with reading scores of 70 or higher
school_passing_math = school_data_complete[(school_data_complete["math_score"] >= 70)]

# Calculate the number of schools with reading scores of 70 or higher
school_passing_reading = school_data_complete[(school_data_complete["reading_score"] >= 70)]

# Schools that passed both math and reading with scores of 70 or higher
passing_math_and_reading = school_data_complete[
    (school_data_complete["reading_score"] >= 70) & (school_data_complete["math_score"] >= 70)
]

# Per school passing rates
per_school_passing_math = school_passing_math.groupby(["school_name"]).count()["student_name"] / per_school_counts * 100
per_school_passing_reading = school_passing_reading.groupby(["school_name"]).count()["student_name"] / per_school_counts * 100
overall_passing_rate = passing_math_and_reading.groupby(["school_name"]).count()["student_name"] / per_school_counts * 100

# DataFrame called `per_school_summary` with columns for the calculations above.
per_school_summary = pd.DataFrame({
    "School Type": school_types,
    "Total Students": per_school_counts,
    "Total School Budget": per_school_budget,
    "Per Student Budget": per_school_capita,
    "Average Math Score": per_school_math, 
    "Average Reading Score": per_school_reading,
    "% Passing Math": per_school_passing_math,
    "% Passing Reading": per_school_passing_reading,
    "% Overall Passing": overall_passing_rate
    })

# Formatting
per_school_summary["Total School Budget"] = per_school_summary["Total School Budget"].map("${:,.2f}".format)
per_school_summary["Per Student Budget"] = per_school_summary["Per Student Budget"].map("${:,.2f}".format)


# Display the DataFrame
per_school_summary
```
<img width="1074" alt="Screenshot 2023-12-08 at 2 32 34 PM" src="https://github.com/samuelhfish/pandas-dataframe-practice/assets/125224990/cf9a0365-d7d1-41e3-bc4a-715fc91d380a">

```python
per_school_summary_overallpass = per_school_summary.sort_values("% Overall Passing", ascending=False)
top_schools = per_school_summary_overallpass.head(5)
top_schools

# `% Overall Passing` in ascending order with the top 5 rows.
per_school_summary_overallpass = per_school_summary.sort_values("% Overall Passing")
bottom_schools = per_school_summary_overallpass.head(5)
bottom_schools

# Separate the data by grade
ninth_graders = school_data_complete[(school_data_complete["grade"] == "9th")]
tenth_graders = school_data_complete[(school_data_complete["grade"] == "10th")]
eleventh_graders = school_data_complete[(school_data_complete["grade"] == "11th")]
twelfth_graders = school_data_complete[(school_data_complete["grade"] == "12th")]

# MATH SCORE BY GRADE and SCHOOL
# Group by "school_name" and take the mean of each.
ninth_graders_math_scores = ninth_graders.groupby(["school_name"]).mean()["math_score"]
tenth_graders_math_scores = tenth_graders.groupby(["school_name"]).mean()["math_score"] 
eleventh_graders_math_scores = eleventh_graders.groupby(["school_name"]).mean()["math_score"] 
twelfth_graders_math_scores = twelfth_graders.groupby(["school_name"]).mean()["math_score"] 

# Combine each of the scores above into single DataFrame called `math_scores_by_grade`
math_scores_by_grade = pd.DataFrame({
    "9th Math": ninth_graders_math_scores,
    "10th Math": tenth_graders_math_scores,
    "11th Math": eleventh_graders_math_scores,
    "12th Math": twelfth_graders_math_scores,
    })


# Minor data wrangling
math_scores_by_grade.index.name = None

# Display the DataFrame
math_scores_by_grade

# READING SCORE BY GRADE
# Group by "school_name" and take the mean of each.
ninth_graders_reading_scores = ninth_graders.groupby(["school_name"]).mean()["reading_score"]
tenth_graders_reading_scores = tenth_graders.groupby(["school_name"]).mean()["reading_score"] 
eleventh_graders_reading_scores = eleventh_graders.groupby(["school_name"]).mean()["reading_score"] 
twelfth_graders_reading_scores = twelfth_graders.groupby(["school_name"]).mean()["reading_score"] 

# Combine each of the scores above into single DataFrame called `math_scores_by_grade`
reading_scores_by_grade = pd.DataFrame({
    "9th Reading": ninth_graders_reading_scores,
    "10th Reading": tenth_graders_reading_scores,
    "11th Reading": eleventh_graders_reading_scores,
    "12th Reading": twelfth_graders_reading_scores,
    })


# Minor data wrangling
reading_scores_by_grade.index.name = None

# Display the DataFrame
reading_scores_by_grade
```
```python

# SCORES BY SCHOOL SPENDING
# Establish the bins 
spending_bins = [0, 585, 630, 645, 680]
labels = ["<$585", "$585-630", "$630-645", "$645-680"]

# Create a copy of the school summary since it has the "Per Student Budget" 
school_spending_df = per_school_summary.copy()

# Use `pd.cut` to categorize spending based on the bins.
school_spending_df["Spending Ranges (Per Student)"] = pd.cut(per_school_capita, spending_bins, labels=labels)
school_spending_df

# Calculate averages for the desired columns. 
size_math_scores = per_school_summary.groupby(["School Size"]).mean()["Average Math Score"]
size_reading_scores = per_school_summary.groupby(["School Size"]).mean()["Average Reading Score"]
size_passing_math = per_school_summary.groupby(["School Size"]).mean()["% Passing Math"]
size_passing_reading = per_school_summary.groupby(["School Size"]).mean()["% Passing Reading"]
size_overall_passing = per_school_summary.groupby(["School Size"]).mean()["% Overall Passing"]

```
```python
# Create a DataFrame called `size_summary` that breaks down school performance based on school size (small, medium, or large).
# Use the scores above to create a new DataFrame called `size_summary`
size_summary = pd.DataFrame({
    "Average Math Score": size_math_scores,
    "Average Reading Score": size_reading_scores,
    "% Passing Math": size_passing_math,
    "% Pasing Reading": size_passing_reading,
    "% Overall Passing": size_overall_passing,
    })

# Display results
size_summary
```
<img width="667" alt="Screenshot 2023-12-08 at 2 37 58 PM" src="https://github.com/samuelhfish/pandas-dataframe-practice/assets/125224990/39667b03-9475-4b9a-b328-96e7eab5a98a">

```python
# SCORES BY SCHOOL TYPE
# Group the per_school_summary DataFrame by "School Type" and average the results.
type_math_scores = per_school_summary.groupby(["School Type"]).mean()["Average Math Score"]
type_reading_scores = per_school_summary.groupby(["School Type"]).mean()["Average Reading Score"]
type_passing_math = per_school_summary.groupby(["School Type"]).mean()["% Passing Math"]
type_passing_reading = per_school_summary.groupby(["School Type"]).mean()["% Passing Reading"]
type_overall_passing = per_school_summary.groupby(["School Type"]).mean()["% Overall Passing"]

# Assemble the new data by type into a DataFrame called `type_summary`
type_summary = pd.DataFrame({
    "Average Math Score": type_math_scores,
    "Average Reading Score": type_reading_scores,
    "% Passing Math": type_passing_math,
    "% Pasing Reading": type_passing_reading,
    "% Overall Passing": type_overall_passing,
    })

# Display results
type_summary
```
<img width="627" alt="Screenshot 2023-12-08 at 2 39 00 PM" src="https://github.com/samuelhfish/pandas-dataframe-practice/assets/125224990/53d22cfe-d959-4c6f-ac26-57433b65a6c2">

**Only key code and results are inlcuded in readme for brevity, for full code and results see "main.ipynb" notebook in "PyCitySchools" folder.**



