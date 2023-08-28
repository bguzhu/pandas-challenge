# pandas-challenge
Module 4 challenge for Python using Pandas

#!/usr/bin/env python
# coding: utf-8

# # PyCity Schools Analysis
# 
# - Your analysis here
#   
# Written analysis/report
# 
# After analyzing the PyCity School datasets, one conclusion drawn is schools that spend significantly more overall as well as for each student generally have lower passing rates for math and reading. Even though the money is possibly used for more learning resources and better student support, school performances tend to worsen for the student population as a whole. Another conclusion is that charter schools, regardless if the student population is large or not, have better student performances compared to district schools. Wilson High School is the only charter school with a large student population that still has a high percentage of students passing math and reading. Given the trends and patterns within the datasets and to summarize this analysis, enrolling students in charter schools, preferably where the future school budgets will hopefully result in an improved student learning experience, can most likely lead to great education and success later in life.
# 

# In[ ]:


#Use the appropriate Panda library
import pandas as pd

#Load the csv files to eventually read
school_data_load = "./Resources/schools_complete.csv"
student_data_load = "./Resources/students_complete.csv"

# Read the files and put into a pandas dataframe
school_data_read = pd.read_csv(school_data_load)
student_data_read = pd.read_csv(student_data_load)

# Merge the data from the read files  
school_data = pd.merge(student_data_read, school_data_read)

#Display the first 5 rows of the school_data dataframe
school_data.head()


# ## District Summary

# In[ ]:


#Get the total number of unique schools
unique_school_count = len(school_data["school_name"].unique())
unique_school_count


# In[ ]:


#Get the total number of students through each Student ID.
student_total = len(school_data["Student ID"].unique())
student_total


# In[ ]:


#Get the total budget using school_data_read since the merged school_data creates budget duplicates for multiple students at the same school
budget_total = school_data_read["budget"].sum()
budget_total


# In[ ]:


#Get the average math score
average_math = school_data["math_score"].mean()
average_math


# In[ ]:


#Get the average (mean) reading score
average_reading = school_data["reading_score"].mean()
average_reading


# In[ ]:


# Takes all students that have a greater or equal to 70 math score and counts them in the school data dataframe. Also, gets the student name.
passing_math = school_data[(school_data["math_score"] >= 70)].count()["student_name"]
passing_math_percent = passing_math / student_total * 100
passing_math_percent


# In[ ]:


# Takes all students that have a greater or equal to 70 reading score and counts them in the school data dataframe by pooling filter out. Also, gets the student name.
passing_reading = school_data[(school_data["reading_score"] >= 70)].count()["student_name"]
passing_reading_percent = passing_reading / student_total * 100
passing_reading_percent


# In[ ]:


#Finds how many passed both math and reading and gets a percentage
passing_math_reading = school_data[
    (school_data["math_score"] >= 70) & (school_data["reading_score"] >= 70)
    ].count()["student_name"]
overall_pass_rate = passing_math_reading / student_total * 100
overall_pass_rate


# In[ ]:


# Create a dataframe with the above metrics
district_summary = pd.DataFrame(
    {
        "Total Number of Unique Schools": [unique_school_count],
        "Total Students": [student_total],
        "Total Budget": [budget_total],
        "Average Math Score": [average_math],
        "Average Reading Score": [average_reading],
        "Percentage of Math Passed": [passing_math_percent],
        "Percentage of Reading Passed": [passing_reading_percent],
        "Percentage of Math and Reading Passed": [overall_pass_rate] 
    }
)

#Make the Total Students include a comma and the Total Budgets have a dollar sign and commas
district_summary["Total Students"] = district_summary["Total Students"].map("{:,}".format)
district_summary["Total Budget"] = district_summary["Total Budget"].map("${:,.2f}".format)

#Show the final dataframe
district_summary


# ## School Summary

# In[ ]:


#Get the school types
school_types = school_data_read.set_index(["school_name"])["type"]
school_types                                                   


# In[ ]:


#Get the total student count per school
per_school_counts = school_data["school_name"].value_counts()
per_school_counts


# In[ ]:


#Use groupby to show each unique school and the budget mean
per_school_budget = school_data.groupby(["school_name"]).mean()["budget"]
per_school_capita = per_school_budget / per_school_counts


# In[ ]:


#Get the average test scores per school
per_school_math = school_data.groupby(["school_name"]).mean()["math_score"]
per_school_reading = school_data.groupby(["school_name"]).mean()["reading_score"]


# In[ ]:


#Get the number of students per school with math scores of 70 or higher
students_passing_math = school_data[school_data["math_score"] >= 70]


# In[ ]:


#Get the number of students per school with reading scores of 70 or higher
students_passing_reading = school_data[school_data["reading_score"] >= 70]


# In[ ]:


#Gets the number of students per school that passed both math and reading with scores of 70 or higher
pass_math_and_reading = school_data[
    (school_data["reading_score"] >= 70) & (school_data["math_score"] >= 70)
]


# In[ ]:


#Get the passing rates
per_school_passing_math = students_passing_math.groupby(["school_name"]).count()["student_name"] / per_school_counts * 100
per_school_passing_reading = students_passing_reading.groupby(["school_name"]).count()["student_name"] / per_school_counts * 100
overall_passed_rate = pass_math_and_reading.groupby(["school_name"]).count()["student_name"] / per_school_counts * 100


# In[ ]:


# Create a DataFrame with the above metrics
per_school_summary = pd.DataFrame(
    {
        "School Types": school_types,
        "Total Student Count": per_school_counts,
        "Total School Budget": per_school_budget,
        "Per Capita Spending": per_school_capita,
        "Average Math Score": per_school_math,
        "Average Reading Score": per_school_reading,
        "Percentage of Schools with Math Passed": per_school_passing_math,
        "Percentage of Schools with Reading Passed": per_school_passing_reading,
        "% Overall Passing": overall_passed_rate
    }
)

#Include $ signs to the Total School Budget and Per Capita Spending columns as well as commas to the Total School Budget column
per_school_summary["Total School Budget"] = per_school_summary["Total School Budget"].map("${:,.2f}".format)
per_school_summary["Per Capita Spending"] = per_school_summary["Per Capita Spending"].map("${:,.2f}".format)

# Display the DataFrame
per_school_summary


# ## Highest-Performing Schools (by % Overall Passing)

# In[ ]:


# Sort by descending order for Overall passing % 
top_schools = per_school_summary.sort_values(["% Overall Passing"], ascending=False)
top_schools.head()


# ## Bottom Performing Schools (By % Overall Passing)

# In[ ]:


#Sort by ascending order for Overall passing %
bottom_schools = per_school_summary.sort_values(["% Overall Passing"], ascending=True)
bottom_schools.head()


# ## Math Scores by Grade

# In[ ]:


#Checks for each grade
freshmen = school_data[(school_data["grade"] == "9th")]
sophomore = school_data[(school_data["grade"] == "10th")]
junior = school_data[(school_data["grade"] == "11th")]
senior = school_data[(school_data["grade"] == "12th")]

#Use groupby to look at each unique school name and gets the average math score
freshmen_math_scores = freshmen.groupby(["school_name"]).mean()["math_score"]
sophomore_math_scores = sophomore.groupby(["school_name"]).mean()["math_score"]
junior_math_scores = junior.groupby(["school_name"]).mean()["math_score"]
senior_math_scores = senior.groupby(["school_name"]).mean()["math_score"]

#Puts each math score above to a new dataframe to distinguish each grade
math_scores_by_grade = pd.DataFrame(
    {
        "Freshmen Math Scores" : freshmen_math_scores,
        "Sophomore Math Scores" : sophomore_math_scores,
        "Junior Math Scores" : junior_math_scores,
        "Senior Math Scores" : senior_math_scores,
    }
)

# Show the dataframe
math_scores_by_grade


# ## Reading Score by Grade 

# In[ ]:


#Checks for each grade
freshmen = school_data[(school_data["grade"] == "9th")]
sophomore = school_data[(school_data["grade"] == "10th")]
junior = school_data[(school_data["grade"] == "11th")]
senior = school_data[(school_data["grade"] == "12th")]

#Use groupby to look at each unique school name and gets the average math score
freshmen_reading_scores = freshmen.groupby(["school_name"]).mean()["reading_score"]
sophomore_reading_scores = sophomore.groupby(["school_name"]).mean()["reading_score"]
junior_reading_scores = junior.groupby(["school_name"]).mean()["reading_score"]
senior_reading_scores = senior.groupby(["school_name"]).mean()["reading_score"]

#Puts each reading score above to a new dataframe to distinguish each grade
reading_scores_by_grade = pd.DataFrame(
    {
        "Freshmen Reading Scores" : freshmen_reading_scores,
        "Sophomore Reading Scores" : sophomore_reading_scores,
        "Junior Reading Scores" : junior_reading_scores,
        "Senior Reading Scores" : senior_reading_scores,
    }
)

#Show the DataFrame
reading_scores_by_grade


# ## Scores by School Spending

# In[ ]:


#Make the bins
spending_bins = [0, 585, 630, 645, 680]
labels = ["<$585", "$585-630", "$630-645", "$645-680"]


# In[ ]:


# Create a copy of the school summary since it has the "Per Student Budget" 
school_spending_df = per_school_summary.copy()


# In[ ]:


#Use pd.cut() to get a Data Series and while using bins and labels
school_spending_df["Spending Ranges (Per Student)"] = pd.cut(per_school_capita, bins=spending_bins, labels=labels)


# In[ ]:


#Get averages 
spending_math_scores = school_spending_df.groupby(["Spending Ranges (Per Student)"])["Average Math Score"].mean()
spending_reading_scores = school_spending_df.groupby(["Spending Ranges (Per Student)"])["Average Reading Score"].mean()
spending_passing_math = school_spending_df.groupby(["Spending Ranges (Per Student)"])["Percentage of Schools with Math Passed"].mean()
spending_passing_reading = school_spending_df.groupby(["Spending Ranges (Per Student)"])["Percentage of Schools with Reading Passed"].mean()
overall_passing_spending = school_spending_df.groupby(["Spending Ranges (Per Student)"])["% Overall Passing"].mean()


# In[ ]:


#Create the dataframe
spending_summary = pd.DataFrame(
    {
        "Average Math Score": spending_math_scores,
        "Average Reading Score": spending_reading_scores,
        "% passing math": spending_passing_math,
        "% passing reading": spending_passing_reading,
        "% overall passing": overall_passing_spending,
    }
)

# Display results
spending_summary


# ## Scores by School Size

# In[ ]:


#Make the bins
size_bins = [0, 1000, 2000, 5000]
labels = ["Small (<1000)", "Medium (1000-2000)", "Large (2000-5000)"]


# In[ ]:


#Use pd.cut() to get a Data Series and while using bins and labels

per_school_summary["School Size"] = pd.cut(per_school_summary["Total Student Count"], size_bins, labels=labels)
per_school_summary


# In[ ]:


# Calculate averages for the desired columns. 
size_math_scores = per_school_summary.groupby(["School Size"])["Average Math Score"].mean()
size_reading_scores = per_school_summary.groupby(["School Size"])["Average Reading Score"].mean()
size_passing_math = per_school_summary.groupby(["School Size"])["Percentage of Schools with Math Passed"].mean()
size_passing_reading = per_school_summary.groupby(["School Size"])["Percentage of Schools with Reading Passed"].mean()
size_overall_passing = per_school_summary.groupby(["School Size"])["% Overall Passing"].mean()


# In[ ]:


# Create the dataframe
size_summary = pd.DataFrame(
    {
        "Average Math Score": size_math_scores,
        "Average Reading Score": size_reading_scores,
        "% passing math": size_passing_math,
        "% passing reading": size_passing_reading,
        "% overall passing": size_overall_passing,
    }
)

# Display results
size_summary


# ## Scores by School Type

# In[ ]:


# Use groupby to group the per_school_summary DataFrame by "School Type" and average the results.
average_math_score_by_type = per_school_summary.groupby(["School Types"])["Average Math Score"].mean()
average_reading_score_by_type = per_school_summary.groupby(["School Types"])["Average Reading Score"].mean()
average_percent_passing_math_by_type = per_school_summary.groupby(["School Types"])["Percentage of Schools with Math Passed"].mean()
average_percent_passing_reading_by_type = per_school_summary.groupby(["School Types"])["Percentage of Schools with Reading Passed"].mean()
average_percent_overall_passing_by_type = per_school_summary.groupby(["School Types"])["% Overall Passing"].mean()


# In[ ]:


#Create type_summary dataframe
type_summary = pd.DataFrame(
    {
        "Average Math Score": average_math_score_by_type,
        "Average Reading Score": average_reading_score_by_type,
        "% passing math": average_percent_passing_math_by_type,
        "% passing reading": average_percent_passing_reading_by_type,
        "% overall passing": average_percent_overall_passing_by_type,
    }
)

#Show the dataframe
type_summary

