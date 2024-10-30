1. Project Title

•	PyCity Schools Analysis

2. Purpose

•	The purpose of this project is to help the school board and mayor in the city’s school district on making strategic decisions regarding future school budget and priorities.

3. Description

•	This project analyzes school performance in the PyCity school district. The analysis includes various metrics to assess the overall performance of the schools, including average test scores, passing rates, and performance by school spending, size and type.

## Table of Contents
- [Installation Instructions] (#installation Instructions)
- [Technologies Used] (#Technologies Used) 
- [Usage](#usage)
- [Files and Setup] (# Files and Setup)
- [District Summary] (#district-summary)
- [School Summary] (#school-summary)
- [Performance by Grade] (#performance-by-grade)
- [Scores by School Spending] (#scores-by-school-spending)
- [Scores by School Size] (#scores-by-school-size)
- [Scores by School Type] (#scores-by-school-type)

# Installation Instructions
1.	Clone the repository.
-	git clone  https://github.com/Metaferya/pandas-challenge.git
2.	Navigate to the project directory.
-	pandas-challenge/blob/main/PyCitySchools
3.	Install required packages (e.g., using pip install -r requirements.txt).
-	pip install pandas
    
# Technologies Used
1-	Python
2-	Pandas
3-	Jupyter Notebook

# Usage
•	To run the analysis, open the Jupyter Notebook file (PyCitySchools.ipynb) and execute the cells. The notebook contains step-by-step instructions and code to generate the necessary metrics. 
# Files and Setup

**Files to Load**:
•	  - `schools_complete.csv`
•	  - `students_complete.csv`
## Load, read and clean the data files 
school_data_to_load = "../Resources/schools_complete.csv"
student_data_to_load = "../Resources/students_complete.csv"
school_data = pd.read_csv(school_data_to_load)
student_data = pd.read_csv(student_data_to_load)
school_data.dropna(inplace=True)
 school_data_cleaned = school_data_complete.drop_duplicates()

### Combine the data into a single dataset.
school_data_complete = pd.merge(student_data, school_data, how="left", on = "school_name")
school_data_complete.head()

# District Summary
The district summary includes calculations for:
•	Total number of unique schools
•	Total students
•	Total budget
•	Average math score
•	Average reading score
•	Percentage of students passing math
•	Percentage of students passing reading
•	Percentage of students passing both math and reading
## Perform calculations for the district summary -->
school_count = school_data_complete["school_name"].nunique()
student_count = school_data_complete["Student ID"].count()
total_budget = school_data["budget"].sum()
average_math_score = school_data_complete["math_score"].mean()
average_reading_score = school_data_complete["reading_score"].mean()
passing_math_count = school_data_complete[(school_data_complete["math_score"] >= 70)].count()["student_name"]
passing_math_percentage = passing_math_count / float(student_count) * 100
passing_reading_count = school_data_complete[(school_data_complete["reading_score"] >= 70)].count()["student_name"]
passing_reading_percentage = passing_reading_count / float(student_count) * 100
passing_math_reading_count = school_data_complete[(school_data_complete["math_score"] >= 70) & (school_data_complete["reading_score"] >= 70)].count()["student_name"]
overall_passing_rate = passing_math_reading_count / float(student_count) * 100

## Create DataFrame
district_summary = pd.DataFrame({
    'Total Schools': [school_count],
    'Total Students': [student_count],
    'Total Budget': [total_budget],
    'Average Math Score': [average_math_score],
    'Average Reading Score': [average_reading_score],
    '% Passing Math': [passing_math_percentage],
    '% Passing Reading': [passing_reading_percentage],
    '% Overall Passing': [overall_passing_rate]
})

## Formatting
district_summary["Total Students"] = district_summary["Total Students"].map("{:,}".format)
district_summary["Total Budget"] = district_summary["Total Budget"].map("${:,.2f}".format)

## Display the DataFrame
print(district_summary)

# School Summary
The school summary provides a breakdown of metrics for each school, including:
•	School type
•	Total students
•	Total school budget
•	Per student budget
•	Average math score
•	Average reading score
•	Percentage of students passing math
•	Percentage of students passing reading
•	Percentage of students passing both math and reading

## Perform calculations for the school summary
school_types = school_data.set_index(["school_name"])["type"]
per_school_counts = school_data_complete.groupby("school_name")["student_name"].count()
per_school_budget = school_data.groupby("school_name")["budget"].sum()
per_school_capita = per_school_budget / per_school_counts
per_school_math = school_data_complete.groupby("school_name")["math_score"].mean()
per_school_reading = school_data_complete.groupby("school_name")["reading_score"].mean()
students_passing_math = school_data_complete.loc[school_data_complete["math_score"] >= 70]
school_students_passing_math = students_passing_math.groupby("school_name")["student_name"].count()
students_passing_reading = school_data_complete.loc[school_data_complete["reading_score"] >= 70]
school_students_passing_reading = students_passing_reading.groupby("school_name")["student_name"].count()
students_passing_math_and_reading = school_data_complete[(school_data_complete["reading_score"] >= 70) & (school_data_complete["math_score"] >= 70)]
school_students_passing_math_and_reading = students_passing_math_and_reading.groupby(["school_name"]).size()
per_school_passing_math = school_students_passing_math / per_school_counts * 100
per_school_passing_reading = school_students_passing_reading / per_school_counts * 100
overall_passing_rate = school_students_passing_math_and_reading / per_school_counts * 100

## Create DataFrame
per_school_summary = pd.DataFrame({
    'School Type': school_types,
    'Total Students': per_school_counts,
    'Total School Budget': per_school_budget,
    'Per Student Budget': per_school_capita,
    'Average Math Score': per_school_math,
    'Average Reading Score': per_school_reading,
    '% Passing Math': per_school_passing_math,
    '% Passing Reading': per_school_passing_reading,
    '% Overall Passing': overall_passing_rate
})

## Formatting
per_school_summary["Total School Budget"] = per_school_summary["Total School Budget"].map("${:,.2f}".format)
per_school_summary["Per Student Budget"] = per_school_summary["Per Student Budget"].map("${:,.2f}".format)

## Display the DataFrame
print(per_school_summary)

# Performance by Grades

## Highest-Performing Schools
Sort the schools by % Overall Passing in descending order and display the top 5 rows.
python
top_schools = per_school_summary.sort_values(["% Overall Passing"], ascending=False).head(5)
print(top_schools)
## Lowest-Performing Schools
Sort the schools by % Overall Passing in ascending order and display the top 5 rows.
python
bottom_schools = per_school_summary.sort_values(["% Overall Passing"], ascending=True).head(5)
print(bottom_schools)
## Math Scores by Grade
Group by school_name and calculate the mean math score for each grade.
python
## Separate the data by grade
ninth_graders = school_data_complete[(school_data_complete["grade"] == "9th")]
tenth_graders = school_data_complete[(school_data_complete["grade"] == "10th")]
eleventh_graders = school_data_complete[(school_data_complete["grade"] == "11th")]
twelfth_graders = school_data_complete[(school_data_complete["grade"] == "12th")]

## Group by `school_name` and calculate the mean of the `math_score` column for each grade
ninth_grade_math_scores = ninth_graders.groupby("school_name")["math_score"].mean()
tenth_grade_math_scores = tenth_graders.groupby("school_name")["math_score"].mean()
eleventh_grade_math_scores = eleventh_graders.groupby("school_name")["math_score"].mean()
twelfth_grade_math_scores = twelfth_graders.groupby("school_name")["math_score"].mean()

## Combine the scores into a single DataFrame
math_scores_by_grade = pd.DataFrame({
    '9th': ninth_grade_math_scores,
    '10th': tenth_grade_math_scores,
    '11th': eleventh_grade_math_scores,
    '12th': twelfth_grade_math_scores
})

## Minor data wrangling
math_scores_by_grade.index.name = None

## Display the DataFrame
print(math_scores_by_grade)

## Reading Scores by Grade
Group by school_name and calculate the mean reading score for each grade.
python
## Group by `school_name` and calculate the mean of the `reading_score` column for each grade
ninth_grade_reading_scores = ninth_graders.groupby("school_name")["reading_score"].mean()
tenth_grade_reading_scores = tenth_graders.groupby("school_name")["reading_score"].mean()
eleventh_grade_reading_scores = eleventh_graders.groupby("school_name")["reading_score"].mean()
twelfth_grade_reading_scores = twelfth_graders.groupby("school_name")["reading_score"].mean()

## Combine the scores into a single DataFrame
reading_scores_by_grade = pd.DataFrame({
    '9th': ninth_grade_reading_scores,
    '10th': tenth_grade_reading_scores,
    '11th': eleventh_grade_reading_scores,
    '12th': twelfth_grade_reading_scores
})

# Minor data wrangling
reading_scores_by_grade = reading_scores_by_grade[["9th", "10th", "11th", "12th"]]
reading_scores_by_grade.index.name = None

# Display the DataFrame
print(reading_scores_by_grade)

# Scores by School Spending
This analysis categorizes schools based on per student spending and calculates performance metrics within each category.

## Establish the bins
spending_bins = [0, 585, 630, 645, 680]
labels = ["<$585", "$585-630", "$630-645", "$645-680"]
## Catagorize spending and calculate average
school_spending_df = per_school_summary.copy()
school_spending_df["Spending Ranges (Per Student)"] = pd.cut(per_school_capita, bins=spending_bins, labels=labels)
school_spending_df["Spending Ranges (Per Student)"] = school_spending_df["Spending Ranges (Per Student)"].astype(str)
spending_math_scores = school_spending_df.groupby(["Spending Ranges (Per Student)"])["Average Math Score"].mean()
spending_reading_scores = school_spending_df.groupby(["Spending Ranges (Per Student)"])["Average Reading Score"].mean()
spending_passing_math = school_spending_df.groupby(["Spending Ranges (Per Student)"])["% Passing Math"].mean()
spending_passing_reading = school_spending_df.groupby(["Spending Ranges (Per Student)"])["% Passing Reading"].mean()
overall_passing_spending = school_spending_df.groupby(["Spending Ranges (Per Student)"])["% Overall Passing"].mean()
spending_summary = pd.DataFrame({
    'Average Math Score': spending_math_scores,
    'Average Reading Score': spending_reading_scores,
    '% Passing Math': spending_passing_math,
    '% Passing Reading': spending_passing_reading,
    '% Overall Passing': overall_passing_spending
})

# Scores by School Size
This analysis categorizes schools based on size and calculates performance metrics within each category.

## Establish the bins
size_bins = [0, 1000, 2000, 5000]
labels = ["Small (<1000)", "Medium (1000-2000)", "Large (2000-5000)"]

## Catagorize spending and calculate average
school_size_df = per_school_summary.copy()
school_size_df["School Size"] = pd.cut(per_school_counts, bins=size_bins, labels=labels)
school_size_df["School Size"] = school_size_df["School Size"].astype(str)
size_math_scores = school_size_df.groupby(["School Size"])["Average Math Score"].mean()
size_reading_scores = school_size_df.groupby(["School Size"])["Average Reading Score"].mean()
size_passing_math = school_size_df.groupby(["School Size"])["% Passing Math"].mean()
size_passing_reading = school_size_df.groupby(["School Size"])["% Passing Reading"].mean()
size_overall_passing = school_size_df.groupby(["School Size"])["% Overall Passing"].mean()
size_summary = pd.DataFrame({
    'Average Math Score': size_math_scores,
    'Average Reading Score': size_reading_scores,
    '% Passing Math': size_passing_math,
    '% Passing Reading': size_passing_reading,
    '% Overall Passing': size_overall_passing
})
# Scores by School Type
This analysis categorizes schools based on type (charter or district) and calculates performance metrics within each category.

## Calculate averages by school type:
average_math_score_by_type = per_school_summary.groupby(["School Type"])["Average Math Score"].mean()
a# Credits

# Special thanks to:
•	My instructor and xpert for providing guidance and support throughout this project. The expertise and feedback have been invaluable in helping me complete this analysis.

