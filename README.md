# Overview
Welcome to my data analysis project, where I examine the data job market and visualize key patterns and trends in top-paying jobs and in-demand skills to help find optimal job opportunities for data analysts.

The data is sourced from [Luke Barousse's Python Course](https://www.youtube.com/watch?v=wUSDVGivd-8) which can be found on YouTube. Through a series of Python scripts, I explore key questions such as the most demanded skills, salary trends, and the intersection of demand and salary in data analytics.

# The Questions

Below are the questions I want to answer in my project:

1. What are the most demanded skills in top data roles?
2. How are in-demand skills trending?
3. How well do Data Analyst positions pay?
4. How well does top skills pay?
5. What are the optimal skills to learn that are both high demand and high paying?


# The Tools I Used
- **Python**: Base language
    - **Matplotlib**: Data visualisation
    - **Pandas**: Data cleaning/wrangling
    - **Seaborn**: Advanced data visualisation
- **Jupyter Notebooks in Visual Studio Code**: Markdown in conjunction with code
- **Git and GitHub**: Version control

# Data Preparation and cleanup
In my Jupyter Notebooks, the following code appears in the first code box everytime: 
```python
# Importing Libraries
import pandas as pd
from datasets import load_dataset
import matplotlib.pyplot as plt
import datetime as dt
import ast
import seaborn as sns

# Loading Data
dataset = load_dataset('lukebarousse/data_jobs')
df = dataset['train'].to_pandas()

# Data Cleanup
df['job_posted_date'] = pd.to_datetime(df['job_posted_date'])
df['job_skills'] = df['job_skills'].apply(lambda x: ast.literal_eval(x) if pd.notna(x) else x)
```
This code was used to import the relevant libraries, datasets and clean it up for analysis. 

# The Analysis

## 1. What are the most demanded skills for the top 3 most popular data roles?

To find the most demanded skills, I filtered and retrieved the top 5 skills for each of the top 3 most popular data roles.

View my notebook with detailed steps here:
[2_Skill_Demand.ipynb](3_Project/2_Skill_Demand.ipynb)

### Visualize Data
```python
fig, ax = plt.subplots(len(job_titles), 1)

sns.set_theme(style = 'ticks')

for i, job_title in enumerate(job_titles):
    df_plot = df_skills_perc[df_skills_perc['job_title_short'] == job_title].head(5)
    # df_plot[::-1].plot(x = 'job_skills', y = 'skill_percent', kind = 'barh', ax = ax[i], title = job_title)
    sns.barplot(data = df_plot, x = 'skill_percent', y = 'job_skills', ax = ax[i], hue = 'skill_count', palette = 'dark:b_r')
    # ax[i].invert_yaxis()
    ax[i].set_title(job_title)
    ax[i].set_ylabel('')
    ax[i].set_xlabel('')
    ax[i].legend().set_visible(False)
    ax[i].set_xlim(0, 78)

    for index, value in enumerate(df_plot['skill_percent']):
        ax[i].text(value + 1, index, f'{value:.0f}%', va = 'center')
    if i != len(job_titles) - 1:
        ax[i].set_xticks([])


fig.suptitle('Likelihood of Skills Requested in US Job Postings', fontsize = 15)
fig.tight_layout()
plt.show() 
```

### Results
![Visualization of Top Skills](3_Project/Images/skill_demand_all_data_roles.png)

### Insights
- Python and SQL are the most requested skills overall.
- For Data Engineers, a larger amount of cloud skills are requested.

## 2. How are in-demand skills trending for Data Analysts?

To find the trends for in-demand skills over time, I filtered job postings by skills required and calculated them as a percentage of all job postings for each month in 2023. I then visualized it with a line plot in Seaborn. 

View my notebook with detailed steps here: [3_Skills_Trend.ipynb](3_Project/3_Skills_Trend.ipynb)

### Visualize Data
```python
df_plot = df_DA_US_percent.iloc[:, :5]
from adjustText import adjust_text

sns.lineplot(data = df_plot, dashes = False, palette = 'tab10')
sns.set_theme(style = 'ticks')
sns.despine()

plt.title('Trending Top Skills for Data Analysts in the US')
plt.ylabel('Likelihood in Job Posting')
plt.xlabel('2023')
plt.legend().remove()

from matplotlib.ticker import PercentFormatter
ax = plt.gca()
ax.yaxis.set_major_formatter(PercentFormatter(decimals=0))

texts = []
for i in range(5):
    texts.append(plt.text(11.2, df_plot.iloc[-1, i], df_plot.columns[i]))

adjust_text(texts)


plt.show()
```

### Results
![Visualization of Skill Trends](3_Project/Images/skill_trends.png)
*Line plot visualizing trending top skills for Data Analysts in the US in 2023*

### Insights
- Demand for SQL and Excel knowhow is decreasing
- Demand for Python, Tableau and Sas is relatively stable


## 3. How well do jobs pay for Data Analysts?

I grouped jobs together by their job titles and sorted and visualized their salary distributions in order to see what types of data jobs payed the best.

View my notebook with detailed steps here: [4_Salary_Analysis.ipynb](3_Project/4_Salary_Analysis.ipynb)

### Visualize Data
```python
# Finding order of medians to make it look nicer
job_order = df_US_top6.groupby('job_title_short')['salary_year_avg'].median().sort_values(ascending = False).index

# Box plots in seaborn

sns.boxplot(df_US_top6, x = 'salary_year_avg', y = 'job_title_short', order = job_order)
# plt.boxplot(job_list, labels = job_titles, vert = False)
plt.title('Salary Distribution in the United States')
plt.ylabel('')
plt.xlabel('Yearly Salary ($USD)')
ax = plt.gca()
ax.xaxis.set_major_formatter(plt.FuncFormatter(lambda x, pos: f'${int(x/1000)}K'))
plt.xlim(0, 600000)
plt.show()
```

### Results 
![Visualization of salaries with respect to job titles](3_Project/Images/salary_analysis_job_titles.png)
*Box plot visualizing top 6 data jobs with their salary distributiions*

### Insights
- Senior data jobs pay more than their respective counterparts as expected, but senior Data Analysts seem to be paid less than their junior engineer and scientist counterparts, which is surprising. 
- Data Scientists are paid the best by a decent margin (up to $600k).

## 4. How well do skills pay for Data Analysts?
I did the same for the data technical skills to see which skills payed the best.

View my notebook with detailed steps here: [4_Salary_Analysis.ipynb](3_Project/4_Salary_Analysis.ipynb)

### Visualize Data
```python
fig, ax  = plt.subplots(2, 1)

# Graphing highest paid skills
sns.barplot(data = df_DA_top_pay, x = 'median', y = df_DA_top_pay.index, ax = ax[0], hue = 'median', palette = 'dark:b_r') # r is to reverse order
ax[0].set_title('Top 10 Highest Paid Skills for Data Analysts')
ax[0].set_ylabel('')
ax[0].set_xlabel('')
ax[0].legend().remove()

# Graphing Median Salary against most demanded skills
sns.barplot(data = df_DA_skills, x = 'median', y = df_DA_skills.index, ax = ax[1], hue = 'median', palette = 'light:b')
ax[1].set_title('Top 10 Most In-Demand Skills for Data Analysts')
ax[1].set_ylabel('')
ax[1].set_xlabel('Median Salary ($USD)')
ax[1].legend().remove()

# Making xlims same
ax[1].set_xlim(ax[0].get_xlim())

fig.tight_layout()
```
### Results
![Visualisation of salaries against job skills](3_Project/Images/salary_analysis_skills.png)
*Bar plot visualising median salaries for top job skills and top median salaries with their skills*

### Insights
- It pays more to know programmatic visualization language rather than user-friendly softwares such as Excel or Word 
- There are skills that pay more than most demanded programming languages, however the demand for these niche languages in general are very low

## 5. What are the most optimal skills to learn for Data Analysts?
I grouped skills based on demand and pay in a single DataFrame and visualized these attributes together in order to find the most optimal skills


### Visualize Data
``` python
from adjustText import adjust_text

sns.scatterplot(data = df_plot, x = 'skill_perc', y = 'median_salary', hue = 'technology')

sns.despine()
sns.set_theme(style = 'ticks')

texts = []
for i, txt in enumerate(df_DA_US_high_demand.index):
    texts.append(plt.text(df_DA_US_high_demand['skill_perc'].iloc[i], df_DA_US_high_demand['median_salary'].iloc[i], txt))

adjust_text(texts, arrowprops = dict(arrowstyle = '->', color = 'gray', lw = 0.5)) # Meant to space out scatter labels


ax = plt.gca()
from matplotlib.ticker import PercentFormatter
ax.yaxis.set_major_formatter(plt.FuncFormatter(lambda y, pos: f'${int(y/1000)}K'))
ax.xaxis.set_major_formatter(PercentFormatter(decimals = 0))

plt.xlabel('Percent of Job Postings')
plt.ylabel('Median of Yearly Salary ($USD)')
plt.title('Most optimal skills for Data Analysts in the US')
plt.tight_layout()
plt.show()
```

### Results
![Visualisation of salaries and likelihood of postings against job skills](3_Project/Images/optimal_skills.png)
*Scatter plot visualising top skills with their percentage demand in job posting in the US and median pay*

### Insights
- Python, SQL and Tableau are the most optimal empirically.
- Programming technologies and analyst tools are more optimal than other technology types.

# What I Learned
Throughout this project, I uncovered key patterns in the data job market and gained significant understanding in using python for data analysis and visualisation. This includes the following: 

- **Python Libraries**: Through engaging with data at a deeper level than ever, I learnt how to use key libraries and tools such as Pandas to perform data operations more efficiently. I learnt that data cleaning is crucial before any significant insights can be extracted. 
- **Skill Demand and Market Trends**: There is a clear correlation between salary and demand for data job skills. Advanced and specialized skills such as Python and Oracle often lead to higher salaries. 
- **Data Analysis**: I learnt how to handle inconsistent data and key considerations in data cleaning and visualisation. I learnt that knowing what to visualise rather than just how to visualise can be key to generating the best insights.

# Conclusion
This data analysis project has highlighted critical skills and trends that are prevalent in the job market and can be used as a guide for aspiring data analysts for what opportunities they should look for first. Continuous adaptation on job skills are needed as skill demand changes in this field over time. 