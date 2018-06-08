

```python
#Assigment summary
#1.Large school produce lower scores
#2.Charter schools produce better scores
#3.Bigger budgets per student does not equal better score results
```


```python
import pandas as pd
import numpy as np
import os
os.getcwd()
import glob
import ast

glob.glob('*csv')
```




    ['schools_complete.csv', 'students_complete.csv']




```python
file = "schools_complete.csv"
School_df = pd.read_csv(file)
School_df.head()

file = "students_complete.csv"
Students_df = pd.read_csv(file)
```


```python
School_total=School_df.name.count()
School_budget=School_df.budget.sum()
Ave_math=Students_df['math_score'].mean()
Ave_read=Students_df['reading_score'].mean()
Students_total=Students_df['Student ID'].count()
```


```python
Students_df['Overall_Score'] = Students_df['reading_score'] + Students_df['math_score']
Students_df['Overall_Ave'] = Students_df['Overall_Score'] /2
Students_df['Passing_math']=Students_df['math_score'].map(lambda x: 'Fail' if x < 70 else 'Pass')
Students_df['Passing_read']=Students_df['reading_score'].map(lambda x: 'Fail' if x < 70 else 'Pass')
Students_df['Overall_Pass']=Students_df['Overall_Ave'].map(lambda x: 'Fail' if x < 70 else 'Pass')
```


```python
Students_df['Passing_rno']=Students_df['Passing_read'].map(lambda x: '1' if x == "Pass" else '0')
Students_df['Passing_mno']=Students_df['Passing_math'].map(lambda x: '1' if x == "Pass" else '0')
Students_df['Passing_rmo']=Students_df['Overall_Pass'].map(lambda x: '1' if x == "Pass" else '0')
```


```python
Students_df['Passing_rno'] = Students_df['Passing_rno'].astype(float)
Students_df['Passing_mno'] = Students_df['Passing_mno'].astype(float)
Students_df['Passing_rmo'] = Students_df['Passing_rmo'].astype(float)
```


```python
Passing_Read=Students_df.Passing_rno.sum()
Passing_Math=Students_df.Passing_mno.sum()
Passing_All=Students_df.Passing_rmo.sum()
```


```python
Percentage_Pass_Math=Passing_Math/Students_total
Percentage_Pass_Read=Passing_Read/Students_total
Percentage_Pass_All=Passing_All/Students_total
```


```python
district=[School_total,Students_total,School_budget,Ave_math,Ave_read,Percentage_Pass_Math,Percentage_Pass_Read,Percentage_Pass_All]
cols = ['No Schools','No of Students','School Budget','Ave Math','Ave Reading','% Passing Math','% Passing Reading','Overall Passing %']
district_df = pd.DataFrame([district], columns=cols)
```


```python
#Question 1: District Summary
district_df
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
      <th>No Schools</th>
      <th>No of Students</th>
      <th>School Budget</th>
      <th>Ave Math</th>
      <th>Ave Reading</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>Overall Passing %</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>15</td>
      <td>39170</td>
      <td>24649428</td>
      <td>78.985371</td>
      <td>81.87784</td>
      <td>0.749809</td>
      <td>0.858055</td>
      <td>0.893873</td>
    </tr>
  </tbody>
</table>
</div>




```python
grouped_schools = Students_df.groupby(['school'],as_index=False).mean()
```


```python
School_df.rename(columns={'name':'school'}, inplace=True)
School_df['Budget per student'] = School_df['budget']/School_df['size']
```


```python
Merged_summary = pd.merge(left =School_df,right=grouped_schools, how='left',left_on='school',right_on='school')
```


```python
Merged_summary.drop(Merged_summary.columns[[0,6,9,10]], axis=1, inplace=True)
```


```python
Merged_summary.rename(columns={'Passing_mno':'% Passing Math'}, inplace=True)
Merged_summary.rename(columns={'Passing_rno':'% Passing Reading'}, inplace=True)
Merged_summary.rename(columns={'reading_score':'Average Reading Score'}, inplace=True)
Merged_summary.rename(columns={'math_score':'Average Math Score'}, inplace=True)
Merged_summary.rename(columns={'Passing_rmo':'% Overall Pass'}, inplace=True)
```


```python
#Question 2:School Summary (remove rank and funding score)
Merged_summary
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
      <th>school</th>
      <th>type</th>
      <th>size</th>
      <th>budget</th>
      <th>Budget per student</th>
      <th>Average Reading Score</th>
      <th>Average Math Score</th>
      <th>% Passing Reading</th>
      <th>% Passing Math</th>
      <th>% Overall Pass</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Huang High School</td>
      <td>District</td>
      <td>2917</td>
      <td>1910635</td>
      <td>655.0</td>
      <td>81.182722</td>
      <td>76.629414</td>
      <td>0.813164</td>
      <td>0.656839</td>
      <td>0.849846</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Figueroa High School</td>
      <td>District</td>
      <td>2949</td>
      <td>1884411</td>
      <td>639.0</td>
      <td>81.158020</td>
      <td>76.711767</td>
      <td>0.807392</td>
      <td>0.659885</td>
      <td>0.846728</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Shelton High School</td>
      <td>Charter</td>
      <td>1761</td>
      <td>1056600</td>
      <td>600.0</td>
      <td>83.725724</td>
      <td>83.359455</td>
      <td>0.958546</td>
      <td>0.938671</td>
      <td>0.993754</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Hernandez High School</td>
      <td>District</td>
      <td>4635</td>
      <td>3022020</td>
      <td>652.0</td>
      <td>80.934412</td>
      <td>77.289752</td>
      <td>0.808630</td>
      <td>0.667530</td>
      <td>0.848759</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Griffin High School</td>
      <td>Charter</td>
      <td>1468</td>
      <td>917500</td>
      <td>625.0</td>
      <td>83.816757</td>
      <td>83.351499</td>
      <td>0.971390</td>
      <td>0.933924</td>
      <td>0.994550</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Wilson High School</td>
      <td>Charter</td>
      <td>2283</td>
      <td>1319574</td>
      <td>578.0</td>
      <td>83.989488</td>
      <td>83.274201</td>
      <td>0.965396</td>
      <td>0.938677</td>
      <td>0.992554</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Cabrera High School</td>
      <td>Charter</td>
      <td>1858</td>
      <td>1081356</td>
      <td>582.0</td>
      <td>83.975780</td>
      <td>83.061895</td>
      <td>0.970398</td>
      <td>0.941335</td>
      <td>0.995694</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Bailey High School</td>
      <td>District</td>
      <td>4976</td>
      <td>3124928</td>
      <td>628.0</td>
      <td>81.033963</td>
      <td>77.048432</td>
      <td>0.819333</td>
      <td>0.666801</td>
      <td>0.851889</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Holden High School</td>
      <td>Charter</td>
      <td>427</td>
      <td>248087</td>
      <td>581.0</td>
      <td>83.814988</td>
      <td>83.803279</td>
      <td>0.962529</td>
      <td>0.925059</td>
      <td>0.985948</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Pena High School</td>
      <td>Charter</td>
      <td>962</td>
      <td>585858</td>
      <td>609.0</td>
      <td>84.044699</td>
      <td>83.839917</td>
      <td>0.959459</td>
      <td>0.945946</td>
      <td>0.991684</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Wright High School</td>
      <td>Charter</td>
      <td>1800</td>
      <td>1049400</td>
      <td>583.0</td>
      <td>83.955000</td>
      <td>83.682222</td>
      <td>0.966111</td>
      <td>0.933333</td>
      <td>0.992222</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Rodriguez High School</td>
      <td>District</td>
      <td>3999</td>
      <td>2547363</td>
      <td>637.0</td>
      <td>80.744686</td>
      <td>76.842711</td>
      <td>0.802201</td>
      <td>0.663666</td>
      <td>0.847462</td>
    </tr>
    <tr>
      <th>12</th>
      <td>Johnson High School</td>
      <td>District</td>
      <td>4761</td>
      <td>3094650</td>
      <td>650.0</td>
      <td>80.966394</td>
      <td>77.072464</td>
      <td>0.812224</td>
      <td>0.660576</td>
      <td>0.849821</td>
    </tr>
    <tr>
      <th>13</th>
      <td>Ford High School</td>
      <td>District</td>
      <td>2739</td>
      <td>1763916</td>
      <td>644.0</td>
      <td>80.746258</td>
      <td>77.102592</td>
      <td>0.792990</td>
      <td>0.683096</td>
      <td>0.847755</td>
    </tr>
    <tr>
      <th>14</th>
      <td>Thomas High School</td>
      <td>Charter</td>
      <td>1635</td>
      <td>1043130</td>
      <td>638.0</td>
      <td>83.848930</td>
      <td>83.418349</td>
      <td>0.973089</td>
      <td>0.932722</td>
      <td>0.990826</td>
    </tr>
  </tbody>
</table>
</div>




```python
Merged_summary_rank = Merged_summary.copy()
Merged_summary_rank['Passing_ranked']=Merged_summary_rank['% Overall Pass'].rank(ascending=0)
```


```python
#Question 3:Top Performing Schools (By Passing Rate)
Top_5=Merged_summary_rank.sort_values(['Passing_ranked'], ascending=[True,])
Top_5[Top_5['Passing_ranked'] <6]
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
      <th>school</th>
      <th>type</th>
      <th>size</th>
      <th>budget</th>
      <th>Budget per student</th>
      <th>Average Reading Score</th>
      <th>Average Math Score</th>
      <th>% Passing Reading</th>
      <th>% Passing Math</th>
      <th>% Overall Pass</th>
      <th>Passing_ranked</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>6</th>
      <td>Cabrera High School</td>
      <td>Charter</td>
      <td>1858</td>
      <td>1081356</td>
      <td>582.0</td>
      <td>83.975780</td>
      <td>83.061895</td>
      <td>0.970398</td>
      <td>0.941335</td>
      <td>0.995694</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Griffin High School</td>
      <td>Charter</td>
      <td>1468</td>
      <td>917500</td>
      <td>625.0</td>
      <td>83.816757</td>
      <td>83.351499</td>
      <td>0.971390</td>
      <td>0.933924</td>
      <td>0.994550</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Shelton High School</td>
      <td>Charter</td>
      <td>1761</td>
      <td>1056600</td>
      <td>600.0</td>
      <td>83.725724</td>
      <td>83.359455</td>
      <td>0.958546</td>
      <td>0.938671</td>
      <td>0.993754</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Wilson High School</td>
      <td>Charter</td>
      <td>2283</td>
      <td>1319574</td>
      <td>578.0</td>
      <td>83.989488</td>
      <td>83.274201</td>
      <td>0.965396</td>
      <td>0.938677</td>
      <td>0.992554</td>
      <td>4.0</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Wright High School</td>
      <td>Charter</td>
      <td>1800</td>
      <td>1049400</td>
      <td>583.0</td>
      <td>83.955000</td>
      <td>83.682222</td>
      <td>0.966111</td>
      <td>0.933333</td>
      <td>0.992222</td>
      <td>5.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Question 4:Top Performing Schools (By Passing Rate) -lowest 5
Lowest_5=Merged_summary_rank.sort_values(['Passing_ranked'], ascending=[False,])
Lowest_5[Lowest_5['Passing_ranked'] >10]
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
      <th>school</th>
      <th>type</th>
      <th>size</th>
      <th>budget</th>
      <th>Budget per student</th>
      <th>Average Reading Score</th>
      <th>Average Math Score</th>
      <th>% Passing Reading</th>
      <th>% Passing Math</th>
      <th>% Overall Pass</th>
      <th>Passing_ranked</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>Figueroa High School</td>
      <td>District</td>
      <td>2949</td>
      <td>1884411</td>
      <td>639.0</td>
      <td>81.158020</td>
      <td>76.711767</td>
      <td>0.807392</td>
      <td>0.659885</td>
      <td>0.846728</td>
      <td>15.0</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Rodriguez High School</td>
      <td>District</td>
      <td>3999</td>
      <td>2547363</td>
      <td>637.0</td>
      <td>80.744686</td>
      <td>76.842711</td>
      <td>0.802201</td>
      <td>0.663666</td>
      <td>0.847462</td>
      <td>14.0</td>
    </tr>
    <tr>
      <th>13</th>
      <td>Ford High School</td>
      <td>District</td>
      <td>2739</td>
      <td>1763916</td>
      <td>644.0</td>
      <td>80.746258</td>
      <td>77.102592</td>
      <td>0.792990</td>
      <td>0.683096</td>
      <td>0.847755</td>
      <td>13.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Hernandez High School</td>
      <td>District</td>
      <td>4635</td>
      <td>3022020</td>
      <td>652.0</td>
      <td>80.934412</td>
      <td>77.289752</td>
      <td>0.808630</td>
      <td>0.667530</td>
      <td>0.848759</td>
      <td>12.0</td>
    </tr>
    <tr>
      <th>12</th>
      <td>Johnson High School</td>
      <td>District</td>
      <td>4761</td>
      <td>3094650</td>
      <td>650.0</td>
      <td>80.966394</td>
      <td>77.072464</td>
      <td>0.812224</td>
      <td>0.660576</td>
      <td>0.849821</td>
      <td>11.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
grouped_math_by_grade = Students_df.groupby(['school','grade'],as_index=True).mean()
```


```python
grouped_math_by_grade.drop(grouped_math_by_grade.columns[[0,1,3,4,5,6,7]], axis=1, inplace=True)
```


```python
#Question 5:Math Scores by school and Grade
grouped_math_by_grade
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
      <th></th>
      <th>math_score</th>
    </tr>
    <tr>
      <th>school</th>
      <th>grade</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th rowspan="4" valign="top">Bailey High School</th>
      <th>10th</th>
      <td>76.996772</td>
    </tr>
    <tr>
      <th>11th</th>
      <td>77.515588</td>
    </tr>
    <tr>
      <th>12th</th>
      <td>76.492218</td>
    </tr>
    <tr>
      <th>9th</th>
      <td>77.083676</td>
    </tr>
    <tr>
      <th rowspan="4" valign="top">Cabrera High School</th>
      <th>10th</th>
      <td>83.154506</td>
    </tr>
    <tr>
      <th>11th</th>
      <td>82.765560</td>
    </tr>
    <tr>
      <th>12th</th>
      <td>83.277487</td>
    </tr>
    <tr>
      <th>9th</th>
      <td>83.094697</td>
    </tr>
    <tr>
      <th rowspan="4" valign="top">Figueroa High School</th>
      <th>10th</th>
      <td>76.539974</td>
    </tr>
    <tr>
      <th>11th</th>
      <td>76.884344</td>
    </tr>
    <tr>
      <th>12th</th>
      <td>77.151369</td>
    </tr>
    <tr>
      <th>9th</th>
      <td>76.403037</td>
    </tr>
    <tr>
      <th rowspan="4" valign="top">Ford High School</th>
      <th>10th</th>
      <td>77.672316</td>
    </tr>
    <tr>
      <th>11th</th>
      <td>76.918058</td>
    </tr>
    <tr>
      <th>12th</th>
      <td>76.179963</td>
    </tr>
    <tr>
      <th>9th</th>
      <td>77.361345</td>
    </tr>
    <tr>
      <th rowspan="4" valign="top">Griffin High School</th>
      <th>10th</th>
      <td>84.229064</td>
    </tr>
    <tr>
      <th>11th</th>
      <td>83.842105</td>
    </tr>
    <tr>
      <th>12th</th>
      <td>83.356164</td>
    </tr>
    <tr>
      <th>9th</th>
      <td>82.044010</td>
    </tr>
    <tr>
      <th rowspan="4" valign="top">Hernandez High School</th>
      <th>10th</th>
      <td>77.337408</td>
    </tr>
    <tr>
      <th>11th</th>
      <td>77.136029</td>
    </tr>
    <tr>
      <th>12th</th>
      <td>77.186567</td>
    </tr>
    <tr>
      <th>9th</th>
      <td>77.438495</td>
    </tr>
    <tr>
      <th rowspan="4" valign="top">Holden High School</th>
      <th>10th</th>
      <td>83.429825</td>
    </tr>
    <tr>
      <th>11th</th>
      <td>85.000000</td>
    </tr>
    <tr>
      <th>12th</th>
      <td>82.855422</td>
    </tr>
    <tr>
      <th>9th</th>
      <td>83.787402</td>
    </tr>
    <tr>
      <th rowspan="4" valign="top">Huang High School</th>
      <th>10th</th>
      <td>75.908735</td>
    </tr>
    <tr>
      <th>11th</th>
      <td>76.446602</td>
    </tr>
    <tr>
      <th>12th</th>
      <td>77.225641</td>
    </tr>
    <tr>
      <th>9th</th>
      <td>77.027251</td>
    </tr>
    <tr>
      <th rowspan="4" valign="top">Johnson High School</th>
      <th>10th</th>
      <td>76.691117</td>
    </tr>
    <tr>
      <th>11th</th>
      <td>77.491653</td>
    </tr>
    <tr>
      <th>12th</th>
      <td>76.863248</td>
    </tr>
    <tr>
      <th>9th</th>
      <td>77.187857</td>
    </tr>
    <tr>
      <th rowspan="4" valign="top">Pena High School</th>
      <th>10th</th>
      <td>83.372000</td>
    </tr>
    <tr>
      <th>11th</th>
      <td>84.328125</td>
    </tr>
    <tr>
      <th>12th</th>
      <td>84.121547</td>
    </tr>
    <tr>
      <th>9th</th>
      <td>83.625455</td>
    </tr>
    <tr>
      <th rowspan="4" valign="top">Rodriguez High School</th>
      <th>10th</th>
      <td>76.612500</td>
    </tr>
    <tr>
      <th>11th</th>
      <td>76.395626</td>
    </tr>
    <tr>
      <th>12th</th>
      <td>77.690748</td>
    </tr>
    <tr>
      <th>9th</th>
      <td>76.859966</td>
    </tr>
    <tr>
      <th rowspan="4" valign="top">Shelton High School</th>
      <th>10th</th>
      <td>82.917411</td>
    </tr>
    <tr>
      <th>11th</th>
      <td>83.383495</td>
    </tr>
    <tr>
      <th>12th</th>
      <td>83.778976</td>
    </tr>
    <tr>
      <th>9th</th>
      <td>83.420755</td>
    </tr>
    <tr>
      <th rowspan="4" valign="top">Thomas High School</th>
      <th>10th</th>
      <td>83.087886</td>
    </tr>
    <tr>
      <th>11th</th>
      <td>83.498795</td>
    </tr>
    <tr>
      <th>12th</th>
      <td>83.497041</td>
    </tr>
    <tr>
      <th>9th</th>
      <td>83.590022</td>
    </tr>
    <tr>
      <th rowspan="4" valign="top">Wilson High School</th>
      <th>10th</th>
      <td>83.724422</td>
    </tr>
    <tr>
      <th>11th</th>
      <td>83.195326</td>
    </tr>
    <tr>
      <th>12th</th>
      <td>83.035794</td>
    </tr>
    <tr>
      <th>9th</th>
      <td>83.085578</td>
    </tr>
    <tr>
      <th rowspan="4" valign="top">Wright High School</th>
      <th>10th</th>
      <td>84.010288</td>
    </tr>
    <tr>
      <th>11th</th>
      <td>83.836782</td>
    </tr>
    <tr>
      <th>12th</th>
      <td>83.644986</td>
    </tr>
    <tr>
      <th>9th</th>
      <td>83.264706</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Question 6: Reading Scores by Grade
grouped_read_by_grade = Students_df.groupby(['school','grade'],as_index=True).mean()
grouped_read_by_grade.drop(grouped_read_by_grade.columns[[0,2,3,4,5,6,7]], axis=1, inplace=True)
grouped_read_by_grade
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
      <th></th>
      <th>reading_score</th>
    </tr>
    <tr>
      <th>school</th>
      <th>grade</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th rowspan="4" valign="top">Bailey High School</th>
      <th>10th</th>
      <td>80.907183</td>
    </tr>
    <tr>
      <th>11th</th>
      <td>80.945643</td>
    </tr>
    <tr>
      <th>12th</th>
      <td>80.912451</td>
    </tr>
    <tr>
      <th>9th</th>
      <td>81.303155</td>
    </tr>
    <tr>
      <th rowspan="4" valign="top">Cabrera High School</th>
      <th>10th</th>
      <td>84.253219</td>
    </tr>
    <tr>
      <th>11th</th>
      <td>83.788382</td>
    </tr>
    <tr>
      <th>12th</th>
      <td>84.287958</td>
    </tr>
    <tr>
      <th>9th</th>
      <td>83.676136</td>
    </tr>
    <tr>
      <th rowspan="4" valign="top">Figueroa High School</th>
      <th>10th</th>
      <td>81.408912</td>
    </tr>
    <tr>
      <th>11th</th>
      <td>80.640339</td>
    </tr>
    <tr>
      <th>12th</th>
      <td>81.384863</td>
    </tr>
    <tr>
      <th>9th</th>
      <td>81.198598</td>
    </tr>
    <tr>
      <th rowspan="4" valign="top">Ford High School</th>
      <th>10th</th>
      <td>81.262712</td>
    </tr>
    <tr>
      <th>11th</th>
      <td>80.403642</td>
    </tr>
    <tr>
      <th>12th</th>
      <td>80.662338</td>
    </tr>
    <tr>
      <th>9th</th>
      <td>80.632653</td>
    </tr>
    <tr>
      <th rowspan="4" valign="top">Griffin High School</th>
      <th>10th</th>
      <td>83.706897</td>
    </tr>
    <tr>
      <th>11th</th>
      <td>84.288089</td>
    </tr>
    <tr>
      <th>12th</th>
      <td>84.013699</td>
    </tr>
    <tr>
      <th>9th</th>
      <td>83.369193</td>
    </tr>
    <tr>
      <th rowspan="4" valign="top">Hernandez High School</th>
      <th>10th</th>
      <td>80.660147</td>
    </tr>
    <tr>
      <th>11th</th>
      <td>81.396140</td>
    </tr>
    <tr>
      <th>12th</th>
      <td>80.857143</td>
    </tr>
    <tr>
      <th>9th</th>
      <td>80.866860</td>
    </tr>
    <tr>
      <th rowspan="4" valign="top">Holden High School</th>
      <th>10th</th>
      <td>83.324561</td>
    </tr>
    <tr>
      <th>11th</th>
      <td>83.815534</td>
    </tr>
    <tr>
      <th>12th</th>
      <td>84.698795</td>
    </tr>
    <tr>
      <th>9th</th>
      <td>83.677165</td>
    </tr>
    <tr>
      <th rowspan="4" valign="top">Huang High School</th>
      <th>10th</th>
      <td>81.512386</td>
    </tr>
    <tr>
      <th>11th</th>
      <td>81.417476</td>
    </tr>
    <tr>
      <th>12th</th>
      <td>80.305983</td>
    </tr>
    <tr>
      <th>9th</th>
      <td>81.290284</td>
    </tr>
    <tr>
      <th rowspan="4" valign="top">Johnson High School</th>
      <th>10th</th>
      <td>80.773431</td>
    </tr>
    <tr>
      <th>11th</th>
      <td>80.616027</td>
    </tr>
    <tr>
      <th>12th</th>
      <td>81.227564</td>
    </tr>
    <tr>
      <th>9th</th>
      <td>81.260714</td>
    </tr>
    <tr>
      <th rowspan="4" valign="top">Pena High School</th>
      <th>10th</th>
      <td>83.612000</td>
    </tr>
    <tr>
      <th>11th</th>
      <td>84.335938</td>
    </tr>
    <tr>
      <th>12th</th>
      <td>84.591160</td>
    </tr>
    <tr>
      <th>9th</th>
      <td>83.807273</td>
    </tr>
    <tr>
      <th rowspan="4" valign="top">Rodriguez High School</th>
      <th>10th</th>
      <td>80.629808</td>
    </tr>
    <tr>
      <th>11th</th>
      <td>80.864811</td>
    </tr>
    <tr>
      <th>12th</th>
      <td>80.376426</td>
    </tr>
    <tr>
      <th>9th</th>
      <td>80.993127</td>
    </tr>
    <tr>
      <th rowspan="4" valign="top">Shelton High School</th>
      <th>10th</th>
      <td>83.441964</td>
    </tr>
    <tr>
      <th>11th</th>
      <td>84.373786</td>
    </tr>
    <tr>
      <th>12th</th>
      <td>82.781671</td>
    </tr>
    <tr>
      <th>9th</th>
      <td>84.122642</td>
    </tr>
    <tr>
      <th rowspan="4" valign="top">Thomas High School</th>
      <th>10th</th>
      <td>84.254157</td>
    </tr>
    <tr>
      <th>11th</th>
      <td>83.585542</td>
    </tr>
    <tr>
      <th>12th</th>
      <td>83.831361</td>
    </tr>
    <tr>
      <th>9th</th>
      <td>83.728850</td>
    </tr>
    <tr>
      <th rowspan="4" valign="top">Wilson High School</th>
      <th>10th</th>
      <td>84.021452</td>
    </tr>
    <tr>
      <th>11th</th>
      <td>83.764608</td>
    </tr>
    <tr>
      <th>12th</th>
      <td>84.317673</td>
    </tr>
    <tr>
      <th>9th</th>
      <td>83.939778</td>
    </tr>
    <tr>
      <th rowspan="4" valign="top">Wright High School</th>
      <th>10th</th>
      <td>83.812757</td>
    </tr>
    <tr>
      <th>11th</th>
      <td>84.156322</td>
    </tr>
    <tr>
      <th>12th</th>
      <td>84.073171</td>
    </tr>
    <tr>
      <th>9th</th>
      <td>83.833333</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Question 7: Scores by School Spending
Merged_summary_budgetbin = Merged_summary.copy()
bins = [560,600,640,680]
bin_names = ['Low', 'Average', 'High',]

pd.cut(Merged_summary_budgetbin["Budget per student"], bins, labels=bin_names)

Merged_summary_budgetbin["Funding Score"] = pd.cut(
    Merged_summary_budgetbin["Budget per student"], bins, labels=bin_names)

Merged_summary_budgetbin.drop(Merged_summary_budgetbin.columns[[1,2,3,4,]], axis=1, inplace=True)
Merged_summary_budgetbin

Merged_summary_budgetbin_group = Merged_summary_budgetbin.groupby(['Funding Score'],as_index=True).mean()
Merged_summary_budgetbin_group
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
      <th>Average Reading Score</th>
      <th>Average Math Score</th>
      <th>% Passing Reading</th>
      <th>% Passing Math</th>
      <th>% Overall Pass</th>
    </tr>
    <tr>
      <th>Funding Score</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Low</th>
      <td>83.892196</td>
      <td>83.436210</td>
      <td>0.964596</td>
      <td>0.935415</td>
      <td>0.992034</td>
    </tr>
    <tr>
      <th>Average</th>
      <td>82.441176</td>
      <td>80.202112</td>
      <td>0.888811</td>
      <td>0.800490</td>
      <td>0.920523</td>
    </tr>
    <tr>
      <th>High</th>
      <td>80.957446</td>
      <td>77.023555</td>
      <td>0.806752</td>
      <td>0.667010</td>
      <td>0.849045</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Question 8: Scores by School Size
Merged_summary_sizebin = Merged_summary.copy()

bins2 = [0,1800,3600,5400]
bin_names2 = ['Small', 'Medium', 'Large',]

pd.cut(Merged_summary_sizebin["size"], bins2, labels=bin_names2)

Merged_summary_sizebin["School Size"] = pd.cut(
    Merged_summary_sizebin["size"], bins2, labels=bin_names2)

Merged_summary_sizebin.drop(Merged_summary_sizebin.columns[[1,2,3,4,]], axis=1, inplace=True)

Merged_summary_sizebin_group = Merged_summary_sizebin.groupby(['School Size'],as_index=True).mean()
Merged_summary_sizebin_group
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
      <th>Average Reading Score</th>
      <th>Average Math Score</th>
      <th>% Passing Reading</th>
      <th>% Passing Math</th>
      <th>% Overall Pass</th>
    </tr>
    <tr>
      <th>School Size</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Small</th>
      <td>83.867683</td>
      <td>83.575787</td>
      <td>0.965187</td>
      <td>0.934942</td>
      <td>0.991497</td>
    </tr>
    <tr>
      <th>Medium</th>
      <td>82.210453</td>
      <td>79.355974</td>
      <td>0.869868</td>
      <td>0.775966</td>
      <td>0.906515</td>
    </tr>
    <tr>
      <th>Large</th>
      <td>80.919864</td>
      <td>77.063340</td>
      <td>0.810597</td>
      <td>0.664643</td>
      <td>0.849483</td>
    </tr>
  </tbody>
</table>
</div>




```python
grouped_school_type = Merged_summary.groupby(['type'],as_index=True).mean()
grouped_school_type.drop(grouped_school_type.columns[[0,1,2,]], axis=1, inplace=True)
```


```python
# Question 9: Scores by School Type (Charter vs. District)
grouped_school_type
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
      <th>Average Reading Score</th>
      <th>Average Math Score</th>
      <th>% Passing Reading</th>
      <th>% Passing Math</th>
      <th>% Overall Pass</th>
    </tr>
    <tr>
      <th>type</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Charter</th>
      <td>83.896421</td>
      <td>83.473852</td>
      <td>0.965865</td>
      <td>0.936208</td>
      <td>0.992154</td>
    </tr>
    <tr>
      <th>District</th>
      <td>80.966636</td>
      <td>76.956733</td>
      <td>0.807991</td>
      <td>0.665485</td>
      <td>0.848894</td>
    </tr>
  </tbody>
</table>
</div>


