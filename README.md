# project-Python-04-A-B-testing

__Case-01.__ More 'Click-Through-Rate' in the new website ? Does the experiment webpage drive higher traffic than the control webpage ? 
 - package: Pandas, Numpy, Matplotlib, Seaborn 
 - func:

__Case-02.__ More Career focused description of the online-course lead more success for both the website and the student-customers ?
 - package: Pandas, Numpy, Matplotlib, Seaborn 
 - func:

__Case-03.__ 
 - package: 
 - func: 
-------------------------------------------------------------------------------------------------------------------------------------  
## Case I. 

__Data:__ 
 - timestamp
 - id
 - group: control / experiment
 - action: view / click
<img src="https://user-images.githubusercontent.com/31917400/34457310-a8536276-eda4-11e7-956e-13b52bb8e6ea.jpg" width="700" height="180" />

 - number of unique users - 6328
```
df['id'].nunique()
```
 - size of control group and experiment group - (3332, 2996)
```
df.query('group == "control"').id.nunique(), df.query('group == "experiment"').id.nunique() 
```
 - action types in this experiment
``` 
df.action.unique()
```
 - duration of this experiment - ('2016-09-24 17:42:27.839496', '2017-01-18 10:24:08.629327')
``` 
df.timestamp.min(), df.timestamp.max()
```
Why would we use click through rate instead of number of clicks to compare the performances of control and experiment pages? Because... Getting the proportion of the users who click is more effective than getting the number of users who click when comparing groups of different sizes.ie..more total clicks could occur in one version, even if there is a greater percentage of clicks in the other version (simpson's paradox).




































