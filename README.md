# project-Python-04-A-B-testing

__Case-I.__ More 'Click-Through-Rate' in the new website ? Does the experiment webpage drive higher traffic than the control webpage ? 
 - package: Pandas, Numpy, Matplotlib, Seaborn 
 - func:

__Case-II.__ More Career focused description of the course lead more success for both the website and the student ?
 - package: Pandas, Numpy, Matplotlib, Seaborn 
 - func:

__Case-III.__ 
 - package: 
 - func: 
-------------------------------------------------------------------------------------------------------------------------------------  
# [Case I]. Does the 'experiment' webpage drive higher traffic than the 'control' webpage ? Which one is better ? 

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

__Story:__
 - Definition of 'Click-Through-Rate' (CTR)
   - `The No.of unique visitors who click at least once / The No.of unique visitors who view the page` 
 - Why would we use 'Click-Through-Rate' instead of number of clicks to compare the performances of control and experiment pages? Because... Getting the proportion of the users who click is more effective than getting the number of users who click when comparing **groups of different sizes**. ie..more total clicks could occur in one version, even if there is a greater percentage of clicks in the other version (simpson's paradox).
 - Steps to analyze the results of this A/B test.
   - 1. We computed the observed difference between the metric, CTR, for the control and experiment group.
   - 2. We simulated the sampling distribution for the difference in proportions (or difference in CTR).
   - 3. We used this sampling distribution to **simulate the distribution under the null hypothesis**, by creating a random normal distribution centered at 0 with the same spread and size.
   - 4. We computed the p-value by finding the proportion of values in the null distribution that were greater than our observed difference.
   - 5. We used this p-value to determine the statistical significance of our observed difference.

<img src="https://user-images.githubusercontent.com/31917400/34457350-2dadfb60-eda6-11e7-9a81-c1d784ad2263.jpg" />

 - Extract all the actions from 'control' group + compute CTR - 0.2797118847539016
```
control_df = df.query('group == "control"')
control_ctr = control_df.query('action == "click"').id.nunique() / control_df.query('action == "view"').id.nunique(); 
```
 - Extract all the actions from 'experiment' group + compute CTR - 0.3097463284379172
```
experiment_df = df.query('group == "experiment"')
experiment_ctr = experiment_df.query('action == "click"').id.nunique() / experiment_df.query('action == "view"').id.nunique(); 
```

### Observed Difference in CTR - 0.030034443684015644
```
obs_diff = experiment_ctr - control_ctr; obs_diff
```
#### See if this difference is significant!. 
 - We are using 'Normal Dist of the Null' instead of 'C.I' because it's one-tailed. 
 - First, BOOTSTRAPPING to simulate the sampling distribution for the difference in proportions - CTRs !
```
diffs = []

for _ in range(10000):
    b_samp = df.sample(df.shape[0], replace=True)
    control_df = b_samp.query('group == "control"')
    experiment_df = b_samp.query('group == "experiment"')
    control_ctr = control_df.query('action == "click"').id.nunique() / control_df.query('action == "view"').id.nunique()
    experiment_ctr = experiment_df.query('action == "click"').id.nunique() / experiment_df.query('action == "view"').id.nunique()
    diffs.append(experiment_ctr - control_ctr)
    
diffs = np.array(diffs)    
```
#### Simulating From the Null Hypothesis
 - Simulate distribution under the null hypothesis
```
null_vals = np.random.normal(0, diffs.std(), diffs.size)
```
 - Plot null distribution and line at our observed differece
```
plt.hist(null_vals)
plt.axvline(x=obs_diff, color='red');
```
<img src="https://user-images.githubusercontent.com/31917400/34457559-45c6b4b6-edac-11e7-843b-0c4ba79d4204.jpg" width="400" height="180" />

 - Compute p-value for (H0: diff <= 0) 
``` 
(null_vals > obs_diff).mean()
```
It is 0.0044 so we reject H0.

-------------------------------------------------------------------------------------------------------------------
# [Case II]. Playing with metrics. More Career focused description of the course lead more success for both the website and the student ?

__Data:__ In the Online Education Website, they made the second change that is a more career focused description(ad) on a course overview page in the hope that this change may encourage more users to enroll and complete this course. In this experiment, we’re going to analyze the following metrics:
 - Enrollment Rate: Click through rate for the Enroll button the course overview page
 - Average Reading Duration: Average number of seconds spent on the course overview page
 - Average Classroom Time: Average number of days spent in the classroom for students enrolled in the course
 - Completion Rate: Course completion rate for students enrolled in the course

__>Dataset A__
 - timestamp
 - id
 - group: control / experiment
 - action: view / enroll
 - duration
<img src="https://user-images.githubusercontent.com/31917400/34457690-073e2828-edb1-11e7-9a79-8077564d55a7.jpg" width="700" height="180" />

__>Dataset B__
 - timestamp
 - id
 - group: control / experiment
 - total_days
 - completed: True / False
<img src="https://user-images.githubusercontent.com/31917400/34457735-490642da-edb2-11e7-946c-d412204ec069.jpg" width="700" height="180" />

### *Let's determine if the difference observed for each metric is statistically significant individually.

__Metric 1. Enrollment Rate__
 - Compute 'Click-Through-Rate' for control group - 0.2364438839848676
```
control_df = df.query('group == "control"')
control_ctr = control_df.query('action == "enroll"').id.nunique() / control_df.query('action == "view"').id.nunique()
```
 - Compute 'Click-Through-Rate' for experiment group - 0.2668693009118541
```
experiment_df = df.query('group == "experiment"')
experiment_ctr = experiment_df.query('action == "enroll"').id.nunique() / experiment_df.query('action == "view"').id.nunique()
```
 - **Compute the observed difference in CTR** - 0.030425416926986526
```
obs_diff = experiment_ctr - control_ctr
```
 - Create a sampling distribution of the difference in proportions with bootstrapping
```
diffs = []
for i in range(10000):
    b_samp = df.sample(df.shape[0], replace =True)
    ctrl_df = b_samp.query('group == "control"')
    exp_df = b_samp.query('group == "experiment"')
    ctrl_ctr = ctrl_df.query('action == "enroll"').id.nunique() / ctrl_df.query('action == "view"').id.nunique()
    exp_ctr = exp_df.query('action == "enroll"').id.nunique() / exp_df.query('action == "view"').id.nunique()
    diffs.append(exp_ctr - ctrl_ctr)

diffs = np.array(diffs)
diffs.shape, diffs.size, diffs.mean(), diffs.std()
```
 - Simulate distribution under the null hypothesis
```
null_vals = np.random.normal(0, diffs.std(), diffs.size)
```
 - Plot observed statistic with the null distibution
```
plt.hist(null_vals);
plt.axvline(obs_diff, c='red')
```
<img src="https://user-images.githubusercontent.com/31917400/34458275-c12b9a06-edc3-11e7-9d60-33cbca8937a2.jpg" width="400" height="180" />

 - Compute p-value 
```
(null_vals > obs_diff).mean()
```
Reject H0! The possibility of the No difference is 0.022 ! so the difference is significant! With a type I error rate of 0.05, we have evidence that the enrollment rate for this course increases when using the experimental description on its overview page. 

__Metric 2. Average Reading Duration__
```
control_mean = df.query('group == "control"').duration.mean()
experiment_mean = df.query('group == "experiment"').duration.mean()
obs_diff = experiment_mean - control_mean

diffs = []
for _ in range(10000):
    b_samp = df.sample(df.shape[0], replace=True)
    control_mean = b_samp.query('group == "control"').duration.mean()
    experiment_mean = b_samp.query('group == "experiment"').duration.mean()
    diffs.append(experiment_mean - control_mean)

diffs = np.array(diffs)
null_vals = np.random.normal(0, diffs.std(), diffs.size)
plt.hist(null_vals)
plt.axvline(x=obs_diff, color='red')

(null_vals > obs_diff).mean()
```
<img src="https://user-images.githubusercontent.com/31917400/34458339-a588f634-edc5-11e7-8102-aacad270467f.jpg" width="400" height="180" />

Reject H0! The possibility of the No difference is 0.0 ! so the difference is significant! With a type I error rate of 0.05, we have evidence that the average reading duration for this course increases when using the experimental description on its overview page. 

__Metric 3. Average Classroom Time__
```
control_mean = df.query('group == "control"').total_days.mean()
experiment_mean = df.query('group == "experiment"').total_days.mean()
obs_diff = experiment_mean - control_mean

diffs = []
size = df.shape[0]
for _ in range(10000):
    b_samp = df.sample(size, replace=True)
    control_mean = b_samp.query('group == "control"').total_days.mean()
    experiment_mean = b_samp.query('group == "experiment"').total_days.mean()
    diffs.append(experiment_mean - control_mean)

diffs = np.array(diffs)
null_vals = np.random.normal(0, diffs.std(), diffs.size)
plt.hist(null_vals)
plt.axvline(obs_diff, c='red')

(null_vals > obs_diff).mean()
```
<img src="https://user-images.githubusercontent.com/31917400/34458357-bbf1a500-edc6-11e7-8b92-42a78083b491.jpg" width="400" height="180" />

Reject H0! The possibility of the No difference is 0.0321 ! so the difference is significant! With a type I error rate of 0.05, we have evidence that the average reading duration for this course increases when using the experimental description on its overview page. 

__Metric 4. Completion Rate__
```
control_df = df.query('group == "control"')
control_ctr = control_df.query('completed == True').id.nunique() / control_df.id.nunique()
experiment_df = df.query('group == "experiment"')
experiment_ctr = experiment_df.query('completed == True').id.nunique() / experiment_df.id.nunique()

obs_diff = experiment_ctr - control_ctr

diffs = []
size = df.shape[0]
for _ in range(10000):
    b_samp = df.sample(size, replace=True)
    control_df = b_samp.query('group == "control"')
    experiment_df = b_samp.query('group == "experiment"')
    control_ctr = control_df.query('completed == True').id.nunique() / control_df.id.nunique()
    experiment_ctr = experiment_df.query('completed == True').id.nunique() / experiment_df.id.nunique()
    diffs.append(experiment_ctr - control_ctr)

diffs = np.array(diffs)
null_vals = np.random.normal(0, diffs.std(), diffs.size)
plt.hist(null_vals)
plt.axvline(obs_diff, c='red')

(null_vals > obs_diff).mean()
```
<img src="https://user-images.githubusercontent.com/31917400/34458369-8afbd46a-edc7-11e7-9ab7-beecfa8e9160.jpg" width="400" height="180" />

Reject H0! The possibility of the No difference is 0.039 ! so the difference is significant! With a type I error rate of 0.05, we have evidence that the completion rate for this course increases when using the experimental description on its overview page. 

## Analyzing Multiple Metrics
 - The more metrics you evaluate, the more likely you are to observe significant differences just by chance. Luckily, this multiple comparisons problem can be handled in several ways. The probability of any false Positive (alpha) increases as we increase the number of metrics - Bonferroni Correction
 - Here are the p-values computed for the four metrics in this experiment
   - 1.Enrollment Rate: 0.022
   - 2.Average Reading Duration: 0
   - 3.Average Classroom Time: 0.032
   - 4.Completion Rate: 0.039 
 - If our original alpha value was 0.05, Bonferroni corrected alpha value is 0.0125. so.. With the Bonferroni corrected alpha value, statistically significant metrics are...only Average Reading Duration. 
 - Since the Bonferroni method is too conservative when we expect correlation among metrics, we can better approach this problem with more sophisticated methods, such as: the closed testing procedure, Boole-Bonferroni bound, the Holm-Bonferroni method.

## Note
 - When designing an A/B test and drawing conclusions based on its results, there are some common ones to consider.
   - Novelty effect and change aversion when existing users first experience a change
   - Sufficient traffic and conversions to have significant and repeatable results
   - Best metric choice for making the ultimate decision (eg. measuring revenue vs. clicks)
   - Long enough run time for the experiment to account for changes in behavior based on time of day/week or seasonal events.
   - Practical significance of a conversion rate (the cost of launching a new feature vs. the gain from the increase in conversion)
   - Consistency among test subjects in the control and experiment group (imbalance in the population represented in each group can lead to situations like Simpson's Paradox)
 
-------------------------------------------------------------------------------------------------------------------
# [Case III]. 

























