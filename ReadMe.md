

```python
import pandas as pd
import numpy as np

```


```python
csv_file = "raw_data/schools_complete.csv"
csv_file_1 = "raw_data/students_complete.csv"

school_df = pd.read_csv(csv_file)
students_df = pd.read_csv(csv_file_1)

#print(students_df.head())
#school_df.head()
```


```python
students_df_renamed = students_df.rename(columns={"name":"Student Name"})
school_df_renamed = school_df.rename(columns={"name":"school"})


#print(students_df_renamed)
#school_df_renamed

#students_df_renamed.head()
#school_df_renamed.head()
```


```python
#Merge the data sets using an inner merge 
merge_table = pd.merge(school_df_renamed,students_df_renamed ,on = "school", how = "inner")


#merge_table.head(50000)
```


```python
#Make calculations based on the data

total_schools_count = merge_table["school"].unique()

total_schools = len(total_schools_count)

total_students = len(merge_table["Student Name"])

total_budget = merge_table["budget"].sum()

avg_math_score = merge_table["math_score"].mean()

avg_reading_score = merge_table["reading_score"].mean()

passing_math = merge_table.loc[merge_table["math_score"]>=70]["math_score"].count()
percent_math_pass = passing_math / total_students

passing_reading = merge_table.loc[merge_table["reading_score"]>=70]["reading_score"].count()
percent_reading_pass = passing_reading / total_students

overall_pass = merge_table.loc[(merge_table["math_score"]>=70) & (merge_table["reading_score"]>=70)]["Student Name"].count()
overall_pass_percent = overall_pass / total_students



```


```python
#Create a new Dataframe

district_info_df = pd.DataFrame([{
    
"Total Schools":total_schools,
"Total Students":total_students,
"Total Budget":total_budget,
"Average Math Score":avg_math_score,
"Average Reading Score":avg_reading_score,
"% Passing Math":percent_math_pass,
"% Passing Reading":percent_reading_pass,
"% Overall Passing Rate":overall_pass_percent
}])

#change the order 

district_summary_df = district_info_df[["Total Schools",
                                       "Total Students",
                                       "Total Budget",
                                       "Average Math Score",
                                       "Average Reading Score",
                                       "% Passing Math",
                                       "% Passing Reading",
                                       "% Overall Passing Rate"
    
                                                            ]]
#Format the data frame 

district_summary_df.style.format({"Total Budget": "${:,.2f}", 
                       "Average Math Score": "{:.2f}", 
                       "Average Reading Score": "{:.2f}", 
                       "% Passing Math": "{:.2%}", 
                       "% Passing Reading": "{:.2%}", 
                       "% Overall Passing Rate": "{:.2%}"})



#district_summary_df.head()
```




<style  type="text/css" >
</style>  
<table id="T_f64683c2_75b8_11e8_9070_a0999b14a385" > 
<thead>    <tr> 
        <th class="blank level0" ></th> 
        <th class="col_heading level0 col0" >Total Schools</th> 
        <th class="col_heading level0 col1" >Total Students</th> 
        <th class="col_heading level0 col2" >Total Budget</th> 
        <th class="col_heading level0 col3" >Average Math Score</th> 
        <th class="col_heading level0 col4" >Average Reading Score</th> 
        <th class="col_heading level0 col5" >% Passing Math</th> 
        <th class="col_heading level0 col6" >% Passing Reading</th> 
        <th class="col_heading level0 col7" >% Overall Passing Rate</th> 
    </tr></thead> 
<tbody>    <tr> 
        <th id="T_f64683c2_75b8_11e8_9070_a0999b14a385level0_row0" class="row_heading level0 row0" >0</th> 
        <td id="T_f64683c2_75b8_11e8_9070_a0999b14a385row0_col0" class="data row0 col0" >15</td> 
        <td id="T_f64683c2_75b8_11e8_9070_a0999b14a385row0_col1" class="data row0 col1" >39170</td> 
        <td id="T_f64683c2_75b8_11e8_9070_a0999b14a385row0_col2" class="data row0 col2" >$82,932,329,558.00</td> 
        <td id="T_f64683c2_75b8_11e8_9070_a0999b14a385row0_col3" class="data row0 col3" >78.99</td> 
        <td id="T_f64683c2_75b8_11e8_9070_a0999b14a385row0_col4" class="data row0 col4" >81.88</td> 
        <td id="T_f64683c2_75b8_11e8_9070_a0999b14a385row0_col5" class="data row0 col5" >74.98%</td> 
        <td id="T_f64683c2_75b8_11e8_9070_a0999b14a385row0_col6" class="data row0 col6" >85.81%</td> 
        <td id="T_f64683c2_75b8_11e8_9070_a0999b14a385row0_col7" class="data row0 col7" >65.17%</td> 
    </tr></tbody> 
</table> 




```python
#Group the merged data frame by column "school"

school_list = merge_table.set_index('school').groupby(["school"])

#Begin performing calculations based on the criteria 

sch_type = school_df_renamed.set_index('school')["type"]

stu_per_school = school_list["Student Name"].count()

budget_per_sch = school_df_renamed.set_index('school')["budget"]

per_stu_budget = (budget_per_sch) / school_df_renamed.set_index('school')["size"]

stu_math_score = school_list["math_score"].mean()

stu_read_score = school_list["reading_score"].mean()

stu_pass_math = merge_table[merge_table["math_score"]>=70].groupby("school")["Student Name"].count()/stu_per_school

stu_pass_reading = merge_table[merge_table["reading_score"]>=70].groupby("school")["Student Name"].count()/stu_per_school

stu_overall_pass = merge_table[(merge_table["math_score"]>=70) & (merge_table["reading_score"]>=70)].groupby("school")["Student Name"].count()
stu_overall_pass_percent = stu_overall_pass / stu_per_school

#Create a new DataFrame

school_summary = pd.DataFrame ({
    
"School Type":sch_type,
"Total Students":stu_per_school,
"Total School Budget":budget_per_sch,
"Per Student Budget":per_stu_budget,
"Average Math Score":stu_math_score,
"Average Reading Score":stu_read_score,
"% Passing Math":stu_pass_math,
"% Passing Reading":stu_pass_reading,
"% Overall Passing Rate":stu_overall_pass_percent         
})

school_summary = school_summary[[      "School Type",
                                       "Total Students",
                                       "Total School Budget",
                                       "Per Student Budget",
                                       "Average Math Score",
                                       "Average Reading Score",
                                       "% Passing Math",
                                       "% Passing Reading",
                                       "% Overall Passing Rate"
]]

school_summary.style.format({"Total School Budget": "${:,.2f}", 
                             "Per Student Budget": "${:,.2f}", 
                             "Average Math Score": "{:.2f}", 
                             "Average Reading Score": "{:.2f}", 
                             "% Passing Math": "{:.2%}", 
                             "% Passing Reading": "{:.2%}", 
                             "% Overall Passing Rate": "{:.2%}"})
```




<style  type="text/css" >
</style>  
<table id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385" > 
<thead>    <tr> 
        <th class="blank level0" ></th> 
        <th class="col_heading level0 col0" >School Type</th> 
        <th class="col_heading level0 col1" >Total Students</th> 
        <th class="col_heading level0 col2" >Total School Budget</th> 
        <th class="col_heading level0 col3" >Per Student Budget</th> 
        <th class="col_heading level0 col4" >Average Math Score</th> 
        <th class="col_heading level0 col5" >Average Reading Score</th> 
        <th class="col_heading level0 col6" >% Passing Math</th> 
        <th class="col_heading level0 col7" >% Passing Reading</th> 
        <th class="col_heading level0 col8" >% Overall Passing Rate</th> 
    </tr></thead> 
<tbody>    <tr> 
        <th id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385level0_row0" class="row_heading level0 row0" >Bailey High School</th> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row0_col0" class="data row0 col0" >District</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row0_col1" class="data row0 col1" >4976</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row0_col2" class="data row0 col2" >$3,124,928.00</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row0_col3" class="data row0 col3" >$628.00</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row0_col4" class="data row0 col4" >77.05</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row0_col5" class="data row0 col5" >81.03</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row0_col6" class="data row0 col6" >66.68%</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row0_col7" class="data row0 col7" >81.93%</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row0_col8" class="data row0 col8" >54.64%</td> 
    </tr>    <tr> 
        <th id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385level0_row1" class="row_heading level0 row1" >Cabrera High School</th> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row1_col0" class="data row1 col0" >Charter</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row1_col1" class="data row1 col1" >1858</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row1_col2" class="data row1 col2" >$1,081,356.00</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row1_col3" class="data row1 col3" >$582.00</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row1_col4" class="data row1 col4" >83.06</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row1_col5" class="data row1 col5" >83.98</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row1_col6" class="data row1 col6" >94.13%</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row1_col7" class="data row1 col7" >97.04%</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row1_col8" class="data row1 col8" >91.33%</td> 
    </tr>    <tr> 
        <th id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385level0_row2" class="row_heading level0 row2" >Figueroa High School</th> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row2_col0" class="data row2 col0" >District</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row2_col1" class="data row2 col1" >2949</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row2_col2" class="data row2 col2" >$1,884,411.00</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row2_col3" class="data row2 col3" >$639.00</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row2_col4" class="data row2 col4" >76.71</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row2_col5" class="data row2 col5" >81.16</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row2_col6" class="data row2 col6" >65.99%</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row2_col7" class="data row2 col7" >80.74%</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row2_col8" class="data row2 col8" >53.20%</td> 
    </tr>    <tr> 
        <th id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385level0_row3" class="row_heading level0 row3" >Ford High School</th> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row3_col0" class="data row3 col0" >District</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row3_col1" class="data row3 col1" >2739</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row3_col2" class="data row3 col2" >$1,763,916.00</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row3_col3" class="data row3 col3" >$644.00</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row3_col4" class="data row3 col4" >77.10</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row3_col5" class="data row3 col5" >80.75</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row3_col6" class="data row3 col6" >68.31%</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row3_col7" class="data row3 col7" >79.30%</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row3_col8" class="data row3 col8" >54.29%</td> 
    </tr>    <tr> 
        <th id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385level0_row4" class="row_heading level0 row4" >Griffin High School</th> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row4_col0" class="data row4 col0" >Charter</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row4_col1" class="data row4 col1" >1468</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row4_col2" class="data row4 col2" >$917,500.00</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row4_col3" class="data row4 col3" >$625.00</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row4_col4" class="data row4 col4" >83.35</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row4_col5" class="data row4 col5" >83.82</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row4_col6" class="data row4 col6" >93.39%</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row4_col7" class="data row4 col7" >97.14%</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row4_col8" class="data row4 col8" >90.60%</td> 
    </tr>    <tr> 
        <th id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385level0_row5" class="row_heading level0 row5" >Hernandez High School</th> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row5_col0" class="data row5 col0" >District</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row5_col1" class="data row5 col1" >4635</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row5_col2" class="data row5 col2" >$3,022,020.00</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row5_col3" class="data row5 col3" >$652.00</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row5_col4" class="data row5 col4" >77.29</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row5_col5" class="data row5 col5" >80.93</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row5_col6" class="data row5 col6" >66.75%</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row5_col7" class="data row5 col7" >80.86%</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row5_col8" class="data row5 col8" >53.53%</td> 
    </tr>    <tr> 
        <th id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385level0_row6" class="row_heading level0 row6" >Holden High School</th> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row6_col0" class="data row6 col0" >Charter</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row6_col1" class="data row6 col1" >427</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row6_col2" class="data row6 col2" >$248,087.00</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row6_col3" class="data row6 col3" >$581.00</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row6_col4" class="data row6 col4" >83.80</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row6_col5" class="data row6 col5" >83.81</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row6_col6" class="data row6 col6" >92.51%</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row6_col7" class="data row6 col7" >96.25%</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row6_col8" class="data row6 col8" >89.23%</td> 
    </tr>    <tr> 
        <th id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385level0_row7" class="row_heading level0 row7" >Huang High School</th> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row7_col0" class="data row7 col0" >District</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row7_col1" class="data row7 col1" >2917</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row7_col2" class="data row7 col2" >$1,910,635.00</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row7_col3" class="data row7 col3" >$655.00</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row7_col4" class="data row7 col4" >76.63</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row7_col5" class="data row7 col5" >81.18</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row7_col6" class="data row7 col6" >65.68%</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row7_col7" class="data row7 col7" >81.32%</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row7_col8" class="data row7 col8" >53.51%</td> 
    </tr>    <tr> 
        <th id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385level0_row8" class="row_heading level0 row8" >Johnson High School</th> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row8_col0" class="data row8 col0" >District</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row8_col1" class="data row8 col1" >4761</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row8_col2" class="data row8 col2" >$3,094,650.00</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row8_col3" class="data row8 col3" >$650.00</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row8_col4" class="data row8 col4" >77.07</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row8_col5" class="data row8 col5" >80.97</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row8_col6" class="data row8 col6" >66.06%</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row8_col7" class="data row8 col7" >81.22%</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row8_col8" class="data row8 col8" >53.54%</td> 
    </tr>    <tr> 
        <th id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385level0_row9" class="row_heading level0 row9" >Pena High School</th> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row9_col0" class="data row9 col0" >Charter</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row9_col1" class="data row9 col1" >962</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row9_col2" class="data row9 col2" >$585,858.00</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row9_col3" class="data row9 col3" >$609.00</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row9_col4" class="data row9 col4" >83.84</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row9_col5" class="data row9 col5" >84.04</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row9_col6" class="data row9 col6" >94.59%</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row9_col7" class="data row9 col7" >95.95%</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row9_col8" class="data row9 col8" >90.54%</td> 
    </tr>    <tr> 
        <th id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385level0_row10" class="row_heading level0 row10" >Rodriguez High School</th> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row10_col0" class="data row10 col0" >District</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row10_col1" class="data row10 col1" >3999</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row10_col2" class="data row10 col2" >$2,547,363.00</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row10_col3" class="data row10 col3" >$637.00</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row10_col4" class="data row10 col4" >76.84</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row10_col5" class="data row10 col5" >80.74</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row10_col6" class="data row10 col6" >66.37%</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row10_col7" class="data row10 col7" >80.22%</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row10_col8" class="data row10 col8" >52.99%</td> 
    </tr>    <tr> 
        <th id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385level0_row11" class="row_heading level0 row11" >Shelton High School</th> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row11_col0" class="data row11 col0" >Charter</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row11_col1" class="data row11 col1" >1761</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row11_col2" class="data row11 col2" >$1,056,600.00</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row11_col3" class="data row11 col3" >$600.00</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row11_col4" class="data row11 col4" >83.36</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row11_col5" class="data row11 col5" >83.73</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row11_col6" class="data row11 col6" >93.87%</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row11_col7" class="data row11 col7" >95.85%</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row11_col8" class="data row11 col8" >89.89%</td> 
    </tr>    <tr> 
        <th id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385level0_row12" class="row_heading level0 row12" >Thomas High School</th> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row12_col0" class="data row12 col0" >Charter</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row12_col1" class="data row12 col1" >1635</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row12_col2" class="data row12 col2" >$1,043,130.00</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row12_col3" class="data row12 col3" >$638.00</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row12_col4" class="data row12 col4" >83.42</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row12_col5" class="data row12 col5" >83.85</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row12_col6" class="data row12 col6" >93.27%</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row12_col7" class="data row12 col7" >97.31%</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row12_col8" class="data row12 col8" >90.95%</td> 
    </tr>    <tr> 
        <th id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385level0_row13" class="row_heading level0 row13" >Wilson High School</th> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row13_col0" class="data row13 col0" >Charter</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row13_col1" class="data row13 col1" >2283</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row13_col2" class="data row13 col2" >$1,319,574.00</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row13_col3" class="data row13 col3" >$578.00</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row13_col4" class="data row13 col4" >83.27</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row13_col5" class="data row13 col5" >83.99</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row13_col6" class="data row13 col6" >93.87%</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row13_col7" class="data row13 col7" >96.54%</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row13_col8" class="data row13 col8" >90.58%</td> 
    </tr>    <tr> 
        <th id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385level0_row14" class="row_heading level0 row14" >Wright High School</th> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row14_col0" class="data row14 col0" >Charter</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row14_col1" class="data row14 col1" >1800</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row14_col2" class="data row14 col2" >$1,049,400.00</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row14_col3" class="data row14 col3" >$583.00</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row14_col4" class="data row14 col4" >83.68</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row14_col5" class="data row14 col5" >83.95</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row14_col6" class="data row14 col6" >93.33%</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row14_col7" class="data row14 col7" >96.61%</td> 
        <td id="T_f65cdfbe_75b8_11e8_8ac8_a0999b14a385row14_col8" class="data row14 col8" >90.33%</td> 
    </tr></tbody> 
</table> 




```python
#Find the top 5 schools by Passing rate 

top_performers = school_summary.sort_values(["% Overall Passing Rate"],ascending=False)
top_performers.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>School Type</th>
      <th>Total Students</th>
      <th>Total School Budget</th>
      <th>Per Student Budget</th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>% Overall Passing Rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Cabrera High School</th>
      <td>Charter</td>
      <td>1858</td>
      <td>1081356</td>
      <td>582.0</td>
      <td>83.061895</td>
      <td>83.975780</td>
      <td>0.941335</td>
      <td>0.970398</td>
      <td>0.913348</td>
    </tr>
    <tr>
      <th>Thomas High School</th>
      <td>Charter</td>
      <td>1635</td>
      <td>1043130</td>
      <td>638.0</td>
      <td>83.418349</td>
      <td>83.848930</td>
      <td>0.932722</td>
      <td>0.973089</td>
      <td>0.909480</td>
    </tr>
    <tr>
      <th>Griffin High School</th>
      <td>Charter</td>
      <td>1468</td>
      <td>917500</td>
      <td>625.0</td>
      <td>83.351499</td>
      <td>83.816757</td>
      <td>0.933924</td>
      <td>0.971390</td>
      <td>0.905995</td>
    </tr>
    <tr>
      <th>Wilson High School</th>
      <td>Charter</td>
      <td>2283</td>
      <td>1319574</td>
      <td>578.0</td>
      <td>83.274201</td>
      <td>83.989488</td>
      <td>0.938677</td>
      <td>0.965396</td>
      <td>0.905826</td>
    </tr>
    <tr>
      <th>Pena High School</th>
      <td>Charter</td>
      <td>962</td>
      <td>585858</td>
      <td>609.0</td>
      <td>83.839917</td>
      <td>84.044699</td>
      <td>0.945946</td>
      <td>0.959459</td>
      <td>0.905405</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Find the bottom 5 schools by passing rate

bottom_performers = top_performers.tail()
bottom_performers = bottom_performers.sort_values("% Overall Passing Rate")
bottom_performers
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>School Type</th>
      <th>Total Students</th>
      <th>Total School Budget</th>
      <th>Per Student Budget</th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>% Overall Passing Rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Rodriguez High School</th>
      <td>District</td>
      <td>3999</td>
      <td>2547363</td>
      <td>637.0</td>
      <td>76.842711</td>
      <td>80.744686</td>
      <td>0.663666</td>
      <td>0.802201</td>
      <td>0.529882</td>
    </tr>
    <tr>
      <th>Figueroa High School</th>
      <td>District</td>
      <td>2949</td>
      <td>1884411</td>
      <td>639.0</td>
      <td>76.711767</td>
      <td>81.158020</td>
      <td>0.659885</td>
      <td>0.807392</td>
      <td>0.532045</td>
    </tr>
    <tr>
      <th>Huang High School</th>
      <td>District</td>
      <td>2917</td>
      <td>1910635</td>
      <td>655.0</td>
      <td>76.629414</td>
      <td>81.182722</td>
      <td>0.656839</td>
      <td>0.813164</td>
      <td>0.535139</td>
    </tr>
    <tr>
      <th>Hernandez High School</th>
      <td>District</td>
      <td>4635</td>
      <td>3022020</td>
      <td>652.0</td>
      <td>77.289752</td>
      <td>80.934412</td>
      <td>0.667530</td>
      <td>0.808630</td>
      <td>0.535275</td>
    </tr>
    <tr>
      <th>Johnson High School</th>
      <td>District</td>
      <td>4761</td>
      <td>3094650</td>
      <td>650.0</td>
      <td>77.072464</td>
      <td>80.966394</td>
      <td>0.660576</td>
      <td>0.812224</td>
      <td>0.535392</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Use Loc to find grade, then group by school to display school scores by grade

ninth_grade_math = merge_table.loc[merge_table["grade"]== "9th"].groupby("school")["math_score"].mean()

tenth_grade_math = merge_table.loc[merge_table["grade"]== "10th"].groupby("school")["math_score"].mean()

ele_grade_math = merge_table.loc[merge_table["grade"]== "11th"].groupby("school")["math_score"].mean()

twe_grade_math = merge_table.loc[merge_table["grade"]== "12th"].groupby("school")["math_score"].mean()

#Create a new DataFrame
math_by_grade = pd.DataFrame ({
    
"9th":ninth_grade_math,
"10th":tenth_grade_math,
"11th":ele_grade_math,
"12th":twe_grade_math,        
})

#reorder dataframe

math_by_grade = math_by_grade[[
"9th",
"10th",
"11th",
"12th",   
]]

math_by_grade
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>9th</th>
      <th>10th</th>
      <th>11th</th>
      <th>12th</th>
    </tr>
    <tr>
      <th>school</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Bailey High School</th>
      <td>77.083676</td>
      <td>76.996772</td>
      <td>77.515588</td>
      <td>76.492218</td>
    </tr>
    <tr>
      <th>Cabrera High School</th>
      <td>83.094697</td>
      <td>83.154506</td>
      <td>82.765560</td>
      <td>83.277487</td>
    </tr>
    <tr>
      <th>Figueroa High School</th>
      <td>76.403037</td>
      <td>76.539974</td>
      <td>76.884344</td>
      <td>77.151369</td>
    </tr>
    <tr>
      <th>Ford High School</th>
      <td>77.361345</td>
      <td>77.672316</td>
      <td>76.918058</td>
      <td>76.179963</td>
    </tr>
    <tr>
      <th>Griffin High School</th>
      <td>82.044010</td>
      <td>84.229064</td>
      <td>83.842105</td>
      <td>83.356164</td>
    </tr>
    <tr>
      <th>Hernandez High School</th>
      <td>77.438495</td>
      <td>77.337408</td>
      <td>77.136029</td>
      <td>77.186567</td>
    </tr>
    <tr>
      <th>Holden High School</th>
      <td>83.787402</td>
      <td>83.429825</td>
      <td>85.000000</td>
      <td>82.855422</td>
    </tr>
    <tr>
      <th>Huang High School</th>
      <td>77.027251</td>
      <td>75.908735</td>
      <td>76.446602</td>
      <td>77.225641</td>
    </tr>
    <tr>
      <th>Johnson High School</th>
      <td>77.187857</td>
      <td>76.691117</td>
      <td>77.491653</td>
      <td>76.863248</td>
    </tr>
    <tr>
      <th>Pena High School</th>
      <td>83.625455</td>
      <td>83.372000</td>
      <td>84.328125</td>
      <td>84.121547</td>
    </tr>
    <tr>
      <th>Rodriguez High School</th>
      <td>76.859966</td>
      <td>76.612500</td>
      <td>76.395626</td>
      <td>77.690748</td>
    </tr>
    <tr>
      <th>Shelton High School</th>
      <td>83.420755</td>
      <td>82.917411</td>
      <td>83.383495</td>
      <td>83.778976</td>
    </tr>
    <tr>
      <th>Thomas High School</th>
      <td>83.590022</td>
      <td>83.087886</td>
      <td>83.498795</td>
      <td>83.497041</td>
    </tr>
    <tr>
      <th>Wilson High School</th>
      <td>83.085578</td>
      <td>83.724422</td>
      <td>83.195326</td>
      <td>83.035794</td>
    </tr>
    <tr>
      <th>Wright High School</th>
      <td>83.264706</td>
      <td>84.010288</td>
      <td>83.836782</td>
      <td>83.644986</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Use Loc to find grade, then group by school to display school scores by grade

ninth_grade_reading = merge_table.loc[merge_table["grade"]== "9th"].groupby("school")["reading_score"].mean()

tenth_grade_reading = merge_table.loc[merge_table["grade"]== "10th"].groupby("school")["reading_score"].mean()

ele_grade_reading = merge_table.loc[merge_table["grade"]== "11th"].groupby("school")["reading_score"].mean()

twe_grade_reading = merge_table.loc[merge_table["grade"]== "12th"].groupby("school")["reading_score"].mean()

#Create a new DataFrame

reading_by_grade = pd.DataFrame ({
    
"9th":ninth_grade_reading,
"10th":tenth_grade_reading,
"11th":ele_grade_reading,
"12th":twe_grade_reading,        
})

#reorder the dataframe 

reading_by_grade = reading_by_grade[[
"9th",
"10th",
"11th",
"12th",   
]]

reading_by_grade
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>9th</th>
      <th>10th</th>
      <th>11th</th>
      <th>12th</th>
    </tr>
    <tr>
      <th>school</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Bailey High School</th>
      <td>81.303155</td>
      <td>80.907183</td>
      <td>80.945643</td>
      <td>80.912451</td>
    </tr>
    <tr>
      <th>Cabrera High School</th>
      <td>83.676136</td>
      <td>84.253219</td>
      <td>83.788382</td>
      <td>84.287958</td>
    </tr>
    <tr>
      <th>Figueroa High School</th>
      <td>81.198598</td>
      <td>81.408912</td>
      <td>80.640339</td>
      <td>81.384863</td>
    </tr>
    <tr>
      <th>Ford High School</th>
      <td>80.632653</td>
      <td>81.262712</td>
      <td>80.403642</td>
      <td>80.662338</td>
    </tr>
    <tr>
      <th>Griffin High School</th>
      <td>83.369193</td>
      <td>83.706897</td>
      <td>84.288089</td>
      <td>84.013699</td>
    </tr>
    <tr>
      <th>Hernandez High School</th>
      <td>80.866860</td>
      <td>80.660147</td>
      <td>81.396140</td>
      <td>80.857143</td>
    </tr>
    <tr>
      <th>Holden High School</th>
      <td>83.677165</td>
      <td>83.324561</td>
      <td>83.815534</td>
      <td>84.698795</td>
    </tr>
    <tr>
      <th>Huang High School</th>
      <td>81.290284</td>
      <td>81.512386</td>
      <td>81.417476</td>
      <td>80.305983</td>
    </tr>
    <tr>
      <th>Johnson High School</th>
      <td>81.260714</td>
      <td>80.773431</td>
      <td>80.616027</td>
      <td>81.227564</td>
    </tr>
    <tr>
      <th>Pena High School</th>
      <td>83.807273</td>
      <td>83.612000</td>
      <td>84.335938</td>
      <td>84.591160</td>
    </tr>
    <tr>
      <th>Rodriguez High School</th>
      <td>80.993127</td>
      <td>80.629808</td>
      <td>80.864811</td>
      <td>80.376426</td>
    </tr>
    <tr>
      <th>Shelton High School</th>
      <td>84.122642</td>
      <td>83.441964</td>
      <td>84.373786</td>
      <td>82.781671</td>
    </tr>
    <tr>
      <th>Thomas High School</th>
      <td>83.728850</td>
      <td>84.254157</td>
      <td>83.585542</td>
      <td>83.831361</td>
    </tr>
    <tr>
      <th>Wilson High School</th>
      <td>83.939778</td>
      <td>84.021452</td>
      <td>83.764608</td>
      <td>84.317673</td>
    </tr>
    <tr>
      <th>Wright High School</th>
      <td>83.833333</td>
      <td>83.812757</td>
      <td>84.156322</td>
      <td>84.073171</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Create bins

bins = [0,584.99,614.99,644.99,2000]

group_names = ["<$585","$585-615","$615-645","$645-675"]

merge_table["budget_bins"] = pd.cut(merge_table["budget"]/merge_table["size"], bins, labels = group_names)

#group by spending
by_budget = merge_table.groupby('budget_bins')

#perform calculations
avg_math = by_budget['math_score'].mean()
avg_read = by_budget['reading_score'].mean()
pass_math = merge_table[merge_table['math_score'] >= 70].groupby('budget_bins')['Student Name'].count()/by_budget['Student Name'].count()
pass_read = merge_table[merge_table['reading_score'] >= 70].groupby('budget_bins')['Student Name'].count()/by_budget['Student Name'].count()
overall = merge_table[(merge_table['reading_score'] >= 70) & (merge_table['math_score'] >= 70)].groupby('budget_bins')['Student Name'].count()/by_budget['Student Name'].count()

            
# build a new dataframe           
scores_by_spend = pd.DataFrame({
    "Average Math Score": avg_math,
    "Average Reading Score": avg_read,
    '% Passing Math': pass_math,
    '% Passing Reading': pass_read,
    "Overall Passing Rate": overall
            
})
            
#reorder columns in dateframe
scores_by_spend = scores_by_spend[[
    "Average Math Score",
    "Average Reading Score",
    '% Passing Math',
    '% Passing Reading',
    "Overall Passing Rate"
]]

scores_by_spend.index.name = "Per Student Budget"
scores_by_spend = scores_by_spend.reindex(group_names)

#format and display the dataframe
scores_by_spend.style.format({'Average Math Score': '{:.1f}', 
                              'Average Reading Score': '{:.1f}', 
                              '% Passing Math': '{:.1%}', 
                              '% Passing Reading':'{:.1%}', 
                              'Overall Passing Rate': '{:.1%}'})

```




<style  type="text/css" >
</style>  
<table id="T_f69d04a4_75b8_11e8_93b1_a0999b14a385" > 
<thead>    <tr> 
        <th class="blank level0" ></th> 
        <th class="col_heading level0 col0" >Average Math Score</th> 
        <th class="col_heading level0 col1" >Average Reading Score</th> 
        <th class="col_heading level0 col2" >% Passing Math</th> 
        <th class="col_heading level0 col3" >% Passing Reading</th> 
        <th class="col_heading level0 col4" >Overall Passing Rate</th> 
    </tr>    <tr> 
        <th class="index_name level0" >Per Student Budget</th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
    </tr></thead> 
<tbody>    <tr> 
        <th id="T_f69d04a4_75b8_11e8_93b1_a0999b14a385level0_row0" class="row_heading level0 row0" ><$585</th> 
        <td id="T_f69d04a4_75b8_11e8_93b1_a0999b14a385row0_col0" class="data row0 col0" >83.4</td> 
        <td id="T_f69d04a4_75b8_11e8_93b1_a0999b14a385row0_col1" class="data row0 col1" >84.0</td> 
        <td id="T_f69d04a4_75b8_11e8_93b1_a0999b14a385row0_col2" class="data row0 col2" >93.7%</td> 
        <td id="T_f69d04a4_75b8_11e8_93b1_a0999b14a385row0_col3" class="data row0 col3" >96.7%</td> 
        <td id="T_f69d04a4_75b8_11e8_93b1_a0999b14a385row0_col4" class="data row0 col4" >90.6%</td> 
    </tr>    <tr> 
        <th id="T_f69d04a4_75b8_11e8_93b1_a0999b14a385level0_row1" class="row_heading level0 row1" >$585-615</th> 
        <td id="T_f69d04a4_75b8_11e8_93b1_a0999b14a385row1_col0" class="data row1 col0" >83.5</td> 
        <td id="T_f69d04a4_75b8_11e8_93b1_a0999b14a385row1_col1" class="data row1 col1" >83.8</td> 
        <td id="T_f69d04a4_75b8_11e8_93b1_a0999b14a385row1_col2" class="data row1 col2" >94.1%</td> 
        <td id="T_f69d04a4_75b8_11e8_93b1_a0999b14a385row1_col3" class="data row1 col3" >95.9%</td> 
        <td id="T_f69d04a4_75b8_11e8_93b1_a0999b14a385row1_col4" class="data row1 col4" >90.1%</td> 
    </tr>    <tr> 
        <th id="T_f69d04a4_75b8_11e8_93b1_a0999b14a385level0_row2" class="row_heading level0 row2" >$615-645</th> 
        <td id="T_f69d04a4_75b8_11e8_93b1_a0999b14a385row2_col0" class="data row2 col0" >78.1</td> 
        <td id="T_f69d04a4_75b8_11e8_93b1_a0999b14a385row2_col1" class="data row2 col1" >81.4</td> 
        <td id="T_f69d04a4_75b8_11e8_93b1_a0999b14a385row2_col2" class="data row2 col2" >71.4%</td> 
        <td id="T_f69d04a4_75b8_11e8_93b1_a0999b14a385row2_col3" class="data row2 col3" >83.6%</td> 
        <td id="T_f69d04a4_75b8_11e8_93b1_a0999b14a385row2_col4" class="data row2 col4" >60.3%</td> 
    </tr>    <tr> 
        <th id="T_f69d04a4_75b8_11e8_93b1_a0999b14a385level0_row3" class="row_heading level0 row3" >$645-675</th> 
        <td id="T_f69d04a4_75b8_11e8_93b1_a0999b14a385row3_col0" class="data row3 col0" >77.0</td> 
        <td id="T_f69d04a4_75b8_11e8_93b1_a0999b14a385row3_col1" class="data row3 col1" >81.0</td> 
        <td id="T_f69d04a4_75b8_11e8_93b1_a0999b14a385row3_col2" class="data row3 col2" >66.2%</td> 
        <td id="T_f69d04a4_75b8_11e8_93b1_a0999b14a385row3_col3" class="data row3 col3" >81.1%</td> 
        <td id="T_f69d04a4_75b8_11e8_93b1_a0999b14a385row3_col4" class="data row3 col4" >53.5%</td> 
    </tr></tbody> 
</table> 




```python
#create bins
bins = [0,999,1999,10000]
group_names = ["Small(<1000)","Medium(1000-2000)","Large(2000-5000)"]

merge_table["size_bins"] = pd.cut(merge_table["size"], bins, labels = group_names)

#groupby school size

by_size = merge_table.groupby('size_bins')

# perform calculations 

avg_math_size = by_size['math_score'].mean()
avg_read_size = by_size['reading_score'].mean()
pass_math_size = merge_table[merge_table['math_score'] >= 70].groupby('size_bins')['Student Name'].count()/by_size['Student Name'].count()
pass_read_size = merge_table[merge_table['reading_score'] >= 70].groupby('size_bins')['Student Name'].count()/by_size['Student Name'].count()
overall_size = merge_table[(merge_table['math_score'] >= 70) & (merge_table['reading_score'] >= 70)].groupby('size_bins')['Student Name'].count()/by_size['Student Name'].count()


            
# create anew dataframe

scores_by_size = pd.DataFrame({
    "Average Math Score": avg_math_size,
    "Average Reading Score": avg_read_size,
    '% Passing Math': pass_math_size,
    '% Passing Reading': pass_read_size,
    "Overall Passing Rate": overall_size
            
})
            
#reorder columns

scores_by_size = scores_by_size[[
    "Average Math Score",
    "Average Reading Score",
    '% Passing Math',
    '% Passing Reading',
    "Overall Passing Rate"
]]

#create an index row 

scores_by_size.index.name = "School Size"
scores_by_size = scores_by_size.reindex(group_names)

#display and sort dataframe
scores_by_size.style.format({'Average Math Score': '{:.2f}', 
                              'Average Reading Score': '{:.2f}', 
                              '% Passing Math': '{:.2%}', 
                              '% Passing Reading':'{:.2%}', 
                              'Overall Passing Rate': '{:.2%}'})

```




<style  type="text/css" >
</style>  
<table id="T_f6b26466_75b8_11e8_8b36_a0999b14a385" > 
<thead>    <tr> 
        <th class="blank level0" ></th> 
        <th class="col_heading level0 col0" >Average Math Score</th> 
        <th class="col_heading level0 col1" >Average Reading Score</th> 
        <th class="col_heading level0 col2" >% Passing Math</th> 
        <th class="col_heading level0 col3" >% Passing Reading</th> 
        <th class="col_heading level0 col4" >Overall Passing Rate</th> 
    </tr>    <tr> 
        <th class="index_name level0" >School Size</th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
    </tr></thead> 
<tbody>    <tr> 
        <th id="T_f6b26466_75b8_11e8_8b36_a0999b14a385level0_row0" class="row_heading level0 row0" >Small(<1000)</th> 
        <td id="T_f6b26466_75b8_11e8_8b36_a0999b14a385row0_col0" class="data row0 col0" >83.83</td> 
        <td id="T_f6b26466_75b8_11e8_8b36_a0999b14a385row0_col1" class="data row0 col1" >83.97</td> 
        <td id="T_f6b26466_75b8_11e8_8b36_a0999b14a385row0_col2" class="data row0 col2" >93.95%</td> 
        <td id="T_f6b26466_75b8_11e8_8b36_a0999b14a385row0_col3" class="data row0 col3" >96.04%</td> 
        <td id="T_f6b26466_75b8_11e8_8b36_a0999b14a385row0_col4" class="data row0 col4" >90.14%</td> 
    </tr>    <tr> 
        <th id="T_f6b26466_75b8_11e8_8b36_a0999b14a385level0_row1" class="row_heading level0 row1" >Medium(1000-2000)</th> 
        <td id="T_f6b26466_75b8_11e8_8b36_a0999b14a385row1_col0" class="data row1 col0" >83.37</td> 
        <td id="T_f6b26466_75b8_11e8_8b36_a0999b14a385row1_col1" class="data row1 col1" >83.87</td> 
        <td id="T_f6b26466_75b8_11e8_8b36_a0999b14a385row1_col2" class="data row1 col2" >93.62%</td> 
        <td id="T_f6b26466_75b8_11e8_8b36_a0999b14a385row1_col3" class="data row1 col3" >96.77%</td> 
        <td id="T_f6b26466_75b8_11e8_8b36_a0999b14a385row1_col4" class="data row1 col4" >90.62%</td> 
    </tr>    <tr> 
        <th id="T_f6b26466_75b8_11e8_8b36_a0999b14a385level0_row2" class="row_heading level0 row2" >Large(2000-5000)</th> 
        <td id="T_f6b26466_75b8_11e8_8b36_a0999b14a385row2_col0" class="data row2 col0" >77.48</td> 
        <td id="T_f6b26466_75b8_11e8_8b36_a0999b14a385row2_col1" class="data row2 col1" >81.20</td> 
        <td id="T_f6b26466_75b8_11e8_8b36_a0999b14a385row2_col2" class="data row2 col2" >68.65%</td> 
        <td id="T_f6b26466_75b8_11e8_8b36_a0999b14a385row2_col3" class="data row2 col3" >82.13%</td> 
        <td id="T_f6b26466_75b8_11e8_8b36_a0999b14a385row2_col4" class="data row2 col4" >56.57%</td> 
    </tr></tbody> 
</table> 




```python
#group by type 
by_type = merge_table.groupby("type")

#perform calculations

avg_math_type = by_type['math_score'].mean()
avg_read_type = by_type['reading_score'].mean()
pass_math_type = merge_table[merge_table['math_score'] >= 70].groupby('type')['Student Name'].count()/by_type['Student Name'].count()
pass_read_type = merge_table[merge_table['reading_score'] >= 70].groupby('type')['Student Name'].count()/by_type['Student Name'].count()
overall_type = merge_table[(merge_table['reading_score'] >= 70) & (merge_table['math_score'] >= 70)].groupby('type')['Student Name'].count()/by_type['Student Name'].count()

#create a new dataframe
          
scores_by_type = pd.DataFrame({
    "Average Math Score": avg_math_type,
    "Average Reading Score": avg_read_type,
    '% Passing Math': pass_math_type,
    '% Passing Reading': pass_read_type,
    "Overall Passing Rate": overall_type
})
    
#reorder columns
scores_by_type = scores_by_type[[
    "Average Math Score",
    "Average Reading Score",
    '% Passing Math',
    '% Passing Reading',
    "Overall Passing Rate"
]]

#create an index row 
scores_by_type.index.name = "School Type"


#display and format df 
scores_by_type.style.format({'Average Math Score': '{:.2f}', 
                              'Average Reading Score': '{:.2f}', 
                              '% Passing Math': '{:.2%}', 
                              '% Passing Reading':'{:.2%}', 
                              'Overall Passing Rate': '{:.2%}'})
```




<style  type="text/css" >
</style>  
<table id="T_f6c0aad0_75b8_11e8_8735_a0999b14a385" > 
<thead>    <tr> 
        <th class="blank level0" ></th> 
        <th class="col_heading level0 col0" >Average Math Score</th> 
        <th class="col_heading level0 col1" >Average Reading Score</th> 
        <th class="col_heading level0 col2" >% Passing Math</th> 
        <th class="col_heading level0 col3" >% Passing Reading</th> 
        <th class="col_heading level0 col4" >Overall Passing Rate</th> 
    </tr>    <tr> 
        <th class="index_name level0" >School Type</th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
    </tr></thead> 
<tbody>    <tr> 
        <th id="T_f6c0aad0_75b8_11e8_8735_a0999b14a385level0_row0" class="row_heading level0 row0" >Charter</th> 
        <td id="T_f6c0aad0_75b8_11e8_8735_a0999b14a385row0_col0" class="data row0 col0" >83.41</td> 
        <td id="T_f6c0aad0_75b8_11e8_8735_a0999b14a385row0_col1" class="data row0 col1" >83.90</td> 
        <td id="T_f6c0aad0_75b8_11e8_8735_a0999b14a385row0_col2" class="data row0 col2" >93.70%</td> 
        <td id="T_f6c0aad0_75b8_11e8_8735_a0999b14a385row0_col3" class="data row0 col3" >96.65%</td> 
        <td id="T_f6c0aad0_75b8_11e8_8735_a0999b14a385row0_col4" class="data row0 col4" >90.56%</td> 
    </tr>    <tr> 
        <th id="T_f6c0aad0_75b8_11e8_8735_a0999b14a385level0_row1" class="row_heading level0 row1" >District</th> 
        <td id="T_f6c0aad0_75b8_11e8_8735_a0999b14a385row1_col0" class="data row1 col0" >76.99</td> 
        <td id="T_f6c0aad0_75b8_11e8_8735_a0999b14a385row1_col1" class="data row1 col1" >80.96</td> 
        <td id="T_f6c0aad0_75b8_11e8_8735_a0999b14a385row1_col2" class="data row1 col2" >66.52%</td> 
        <td id="T_f6c0aad0_75b8_11e8_8735_a0999b14a385row1_col3" class="data row1 col3" >80.91%</td> 
        <td id="T_f6c0aad0_75b8_11e8_8735_a0999b14a385row1_col4" class="data row1 col4" >53.70%</td> 
    </tr></tbody> 
</table> 


