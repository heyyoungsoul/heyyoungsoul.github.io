---
layout: single
title: "One Sample T-Test"
date: 2024-01-25 09:01:00 +0900
# categories: analysis
tag: [statistics, python, jupyter]
---

<head>
  <style>
    table.dataframe {
      white-space: normal;
      width: 100%;
      height: 240px;
      display: block;
      overflow: auto;
      font-family: Arial, sans-serif;
      font-size: 0.9rem;
      line-height: 20px;
      text-align: center;
      border: 0px !important;
    }

    table.dataframe th {
      text-align: center;
      font-weight: bold;
      padding: 8px;
    }

    table.dataframe td {
      text-align: center;
      padding: 8px;
    }

    table.dataframe tr:hover {
      background: #b8d1f3;
    }

    .output_prompt {
      overflow: auto;
      font-size: 0.9rem;
      line-height: 1.45;
      border-radius: 0.3rem;
      -webkit-overflow-scrolling: touch;
      padding: 0.8rem;
      margin-top: 0;
      margin-bottom: 15px;
      font: 1rem Consolas, "Liberation Mono", Menlo, Courier, monospace;
      color: $code-text-color;
      border: solid 1px $border-color;
      border-radius: 0.3rem;
      word-break: normal;
      white-space: pre;
    }

.dataframe tbody tr th:only-of-type {
vertical-align: middle;
}

.dataframe tbody tr th {
vertical-align: top;
}

.dataframe thead th {
text-align: center !important;
padding: 8px;
}

.page\_\_content p {
margin: 0 0 0px !important;
}

.page\_\_content p > strong {
font-size: 0.8rem !important;
}

  </style>
</head>

# Data Preparation

```python
import pandas as pd
import numpy as np
```

```python
# Allow share of google sheet
url = 'https://docs.google.com/spreadsheets/d/1C9iMYbD6JMskJX3qMbA_QwBY6bsKdG8CrMkpq3mTCb4/export?format=csv'
df=pd.read_csv(url)
df.head()
```

<pre>
  group participant  c_preference  c_willingness  c_time-saving  \
0  real          P1           6.0            4.0            5.0   
1  real          P2           3.0            6.0            2.0   
2  real          P3           7.0            7.0            6.0   
3  real          P4           5.0            5.0            5.0   
4  real          P5           6.0            7.0            7.0   

   c_money-saving  c_easy_modification  c_exploration  c_floor_plan  \
0             7.0                  7.0            6.0           6.0   
1             5.0                  3.0            5.0           5.0   
2             6.0                  7.0            7.0           6.0   
3             6.0                  3.0            6.0           7.0   
4             7.0                  7.0            7.0           7.0   

   c_camera_manipulation  ...  nltx_performance  nltx_effort  \
0                    3.0  ...              10.0          8.0   
1                    2.0  ...               7.0         10.0   
2                    5.0  ...               1.0          2.0   
3                    7.0  ...               4.0          5.0   
4                    6.0  ...               4.0          2.0   

   nltx_frustration  ntlx2_mental_demand  nltx2_physical_demand  \
0               8.0                 60.0                   20.0   
1               6.0                 10.0                   15.0   
2               1.0                  5.0                    0.0   
3               2.0                 15.0                    5.0   
4               1.0                 10.0                    0.0   

   nltx2_temporal_demand  nltx2_performance  nltx2_effort  nltx2_frustration  \
0                   70.0               45.0          35.0               35.0   
1                    5.0               30.0          45.0               25.0   
2                    0.0                0.0           5.0                0.0   
3                    5.0               15.0          20.0                5.0   
4                    0.0               15.0           5.0                0.0   

   nltx2_avg  
0      44.17  
1      21.67  
2       1.67  
3      10.83  
4       5.00  

[5 rows x 37 columns]
</pre>

```python
# Split the data into two groups (real and fake)
real_data = df[df['group'] == 'real']
fake_data = df[df['group'] == 'fake']
```

```python
print("Real Data:")
print(real_data)

print("\nFake Data:")
print(fake_data)
```

# Normality Testing

- 데이터가 충분한 지 보는 것.

- 정규분포 테스트에는 shapiro test가 주로 쓰인다.

- p 값이 0.05보다 작으면 정규성 보장 x

- 즉, p 값이 0.05보다 커야 쓸 수 있는 데이터!

```python
import matplotlib.pyplot as plt
from scipy.stats import shapiro
```

```python
custom_variables = ['c_preference', 'c_willingness', 'c_time-saving', 'c_money-saving', 'c_easy_modification', 'c_exploration', 'c_floor_plan', 'c_camera_manipulation', 'c_issue_detection', 'c_collaboration']
custom_average = ['c_avg']
sus_variables = ['sus_1_frequent','sus_2_complex','sus_3_easy','sus_4_technical','sus_5_integrated','sus_6_inconsistent','sus_7_learn_quick','sus_8_cumbersome','sus_9_confident','sus_10_need_learn']
sus_total = ['sus_total']
ntlx_variables = ['ntlx2_mental_demand', 'nltx2_physical_demand', 'nltx2_temporal_demand', 'nltx2_performance', 'nltx2_effort','nltx2_frustration']
ntlx_average = ['nltx2_avg']

variables_to_analyze = custom_variables + custom_average + sus_total + ntlx_average
```

```python
# Dictionary to store results
results = {}

for variable in variables_to_analyze:
    # Split the data into two groups (real and fake)
    real_data = df[df['group'] == 'real']
    fake_data = df[df['group'] == 'fake']

    # Plot histograms for each group
    plt.figure(figsize=(12, 6))

    plt.subplot(1, 2, 1)
    plt.hist(real_data[variable], bins=10, edgecolor='black', alpha=0.7)
    plt.title(f'Histogram - Real Data ({variable})')
    plt.xlabel(variable)
    plt.ylabel('Frequency')

    plt.subplot(1, 2, 2)
    plt.hist(fake_data[variable], bins=10, edgecolor='black', alpha=0.7)
    plt.title(f'Histogram - Fake Data ({variable})')
    plt.xlabel(variable)
    plt.ylabel('Frequency')

    plt.tight_layout()
    plt.show()

    # Conduct the Shapiro-Wilk normality test for each group
    stat_real, p_value_real = shapiro(real_data[variable])
    stat_fake, p_value_fake = shapiro(fake_data[variable])

    # Display the results
    print(f"Shapiro-Wilk test for real {variable}:")
    print(f"Statistic: {stat_real}, p-value: {p_value_real}")
    if p_value_real > 0.05:
        print("The data appears to be normally distributed.")
    else:
        print("The data does not appear to be normally distributed.")

    print(f"\nShapiro-Wilk test for fake {variable}:")
    print(f"Statistic: {stat_fake}, p-value: {p_value_fake}")
    if p_value_fake > 0.05:
        print("The data appears to be normally distributed.")
    else:
        print("The data does not appear to be normally distributed.")

    # Store results in the dictionary
    results[variable] = {
        'real_data': {'normal_distribution': p_value_real > 0.05, 'shapiro_stat': stat_real, 'p_value': p_value_real},
        'fake_data': {'normal_distribution': p_value_fake > 0.05, 'shapiro_stat': stat_fake, 'p_value': p_value_fake}
    }
```

<pre>
<Figure size 1200x600 with 2 Axes>
</pre>
<pre>
/usr/local/lib/python3.10/dist-packages/scipy/stats/_morestats.py:1879: UserWarning: Input data for shapiro has range zero. The results may not be accurate.
  warnings.warn("Input data for shapiro has range zero. The results "
</pre>
<pre>
Shapiro-Wilk test for real c_preference:
Statistic: 0.8565592169761658, p-value: 0.17767059803009033
The data appears to be normally distributed.

Shapiro-Wilk test for fake c_preference:
Statistic: 1.0, p-value: 1.0
The data appears to be normally distributed.
</pre>
<pre>
<Figure size 1200x600 with 2 Axes>
</pre>
<pre>
Shapiro-Wilk test for real c_willingness:
Statistic: 0.8311415314674377, p-value: 0.10990755259990692
The data appears to be normally distributed.

Shapiro-Wilk test for fake c_willingness:
Statistic: 1.0, p-value: 1.0
The data appears to be normally distributed.
</pre>
<pre>
<Figure size 1200x600 with 2 Axes>
</pre>
<pre>
Shapiro-Wilk test for real c_time-saving:
Statistic: 0.8658723831176758, p-value: 0.21024180948734283
The data appears to be normally distributed.

Shapiro-Wilk test for fake c_time-saving:
Statistic: 1.0, p-value: 1.0
The data appears to be normally distributed.
</pre>
<pre>
<Figure size 1200x600 with 2 Axes>
</pre>
<pre>
Shapiro-Wilk test for real c_money-saving:
Statistic: 0.8216156959533691, p-value: 0.09113527834415436
The data appears to be normally distributed.

Shapiro-Wilk test for fake c_money-saving:
Statistic: 1.0, p-value: 1.0
The data appears to be normally distributed.
</pre>
<pre>
<Figure size 1200x600 with 2 Axes>
</pre>
<pre>
Shapiro-Wilk test for real c_easy_modification:
Statistic: 0.6398937702178955, p-value: 0.0013507531257346272
The data does not appear to be normally distributed.

Shapiro-Wilk test for fake c_easy_modification:
Statistic: 1.0, p-value: 1.0
The data appears to be normally distributed.
</pre>
<pre>
<Figure size 1200x600 with 2 Axes>
</pre>
<pre>
Shapiro-Wilk test for real c_exploration:
Statistic: 0.8531916737556458, p-value: 0.16700315475463867
The data appears to be normally distributed.

Shapiro-Wilk test for fake c_exploration:
Statistic: 1.0, p-value: 1.0
The data appears to be normally distributed.
</pre>
<pre>
<Figure size 1200x600 with 2 Axes>
</pre>
<pre>
Shapiro-Wilk test for real c_floor_plan:
Statistic: 0.8216156959533691, p-value: 0.09113527834415436
The data appears to be normally distributed.

Shapiro-Wilk test for fake c_floor_plan:
Statistic: 1.0, p-value: 1.0
The data appears to be normally distributed.
</pre>
<pre>
<Figure size 1200x600 with 2 Axes>
</pre>
<pre>
Shapiro-Wilk test for real c_camera_manipulation:
Statistic: 0.8904140591621399, p-value: 0.3203631043434143
The data appears to be normally distributed.

Shapiro-Wilk test for fake c_camera_manipulation:
Statistic: 1.0, p-value: 1.0
The data appears to be normally distributed.
</pre>
<pre>
<Figure size 1200x600 with 2 Axes>
</pre>
<pre>
Shapiro-Wilk test for real c_issue_detection:
Statistic: 0.8311415314674377, p-value: 0.10990755259990692
The data appears to be normally distributed.

Shapiro-Wilk test for fake c_issue_detection:
Statistic: 1.0, p-value: 1.0
The data appears to be normally distributed.
</pre>
<pre>
<Figure size 1200x600 with 2 Axes>
</pre>
<pre>
Shapiro-Wilk test for real c_collaboration:
Statistic: 0.6826766729354858, p-value: 0.004039337392896414
The data does not appear to be normally distributed.

Shapiro-Wilk test for fake c_collaboration:
Statistic: 1.0, p-value: 1.0
The data appears to be normally distributed.
</pre>
<pre>
<Figure size 1200x600 with 2 Axes>
</pre>
<pre>
Shapiro-Wilk test for real c_avg:
Statistic: 0.894312858581543, p-value: 0.3414052724838257
The data appears to be normally distributed.

Shapiro-Wilk test for fake c_avg:
Statistic: 1.0, p-value: 1.0
The data appears to be normally distributed.
</pre>
<pre>
<Figure size 1200x600 with 2 Axes>
</pre>
<pre>
Shapiro-Wilk test for real sus_total:
Statistic: 0.8242687582969666, p-value: 0.09605199843645096
The data appears to be normally distributed.

Shapiro-Wilk test for fake sus_total:
Statistic: 1.0, p-value: 1.0
The data appears to be normally distributed.
</pre>
<pre>
<Figure size 1200x600 with 2 Axes>
</pre>
<pre>
Shapiro-Wilk test for real nltx2_avg:
Statistic: 0.9025363922119141, p-value: 0.389139860868454
The data appears to be normally distributed.

Shapiro-Wilk test for fake nltx2_avg:
Statistic: 1.0, p-value: 1.0
The data appears to be normally distributed.
</pre>

```python
# Print the summary
for variable, data in results.items():
    print(f"{variable}:")
    print(f"Real Data: {'✔' if data['real_data']['normal_distribution'] else '❌'} "
          f"(Shapiro-Wilk test statistic: {data['real_data']['shapiro_stat']}, p-value: {data['real_data']['p_value']})")
    print(f"Fake Data: {'✔' if data['fake_data']['normal_distribution'] else '❌'} "
          f"(Shapiro-Wilk test statistic: {data['fake_data']['shapiro_stat']}, p-value: {data['fake_data']['p_value']})")
    print()
```

<pre>
c_preference:
Real Data: ✔ (Shapiro-Wilk test statistic: 0.8565592169761658, p-value: 0.17767059803009033)
Fake Data: ✔ (Shapiro-Wilk test statistic: 1.0, p-value: 1.0)

c_willingness:
Real Data: ✔ (Shapiro-Wilk test statistic: 0.8311415314674377, p-value: 0.10990755259990692)
Fake Data: ✔ (Shapiro-Wilk test statistic: 1.0, p-value: 1.0)

c_time-saving:
Real Data: ✔ (Shapiro-Wilk test statistic: 0.8658723831176758, p-value: 0.21024180948734283)
Fake Data: ✔ (Shapiro-Wilk test statistic: 1.0, p-value: 1.0)

c_money-saving:
Real Data: ✔ (Shapiro-Wilk test statistic: 0.8216156959533691, p-value: 0.09113527834415436)
Fake Data: ✔ (Shapiro-Wilk test statistic: 1.0, p-value: 1.0)

c_easy_modification:
Real Data: ❌ (Shapiro-Wilk test statistic: 0.6398937702178955, p-value: 0.0013507531257346272)
Fake Data: ✔ (Shapiro-Wilk test statistic: 1.0, p-value: 1.0)

c_exploration:
Real Data: ✔ (Shapiro-Wilk test statistic: 0.8531916737556458, p-value: 0.16700315475463867)
Fake Data: ✔ (Shapiro-Wilk test statistic: 1.0, p-value: 1.0)

c_floor_plan:
Real Data: ✔ (Shapiro-Wilk test statistic: 0.8216156959533691, p-value: 0.09113527834415436)
Fake Data: ✔ (Shapiro-Wilk test statistic: 1.0, p-value: 1.0)

c_camera_manipulation:
Real Data: ✔ (Shapiro-Wilk test statistic: 0.8904140591621399, p-value: 0.3203631043434143)
Fake Data: ✔ (Shapiro-Wilk test statistic: 1.0, p-value: 1.0)

c_issue_detection:
Real Data: ✔ (Shapiro-Wilk test statistic: 0.8311415314674377, p-value: 0.10990755259990692)
Fake Data: ✔ (Shapiro-Wilk test statistic: 1.0, p-value: 1.0)

c_collaboration:
Real Data: ❌ (Shapiro-Wilk test statistic: 0.6826766729354858, p-value: 0.004039337392896414)
Fake Data: ✔ (Shapiro-Wilk test statistic: 1.0, p-value: 1.0)

c_avg:
Real Data: ✔ (Shapiro-Wilk test statistic: 0.894312858581543, p-value: 0.3414052724838257)
Fake Data: ✔ (Shapiro-Wilk test statistic: 1.0, p-value: 1.0)

sus_total:
Real Data: ✔ (Shapiro-Wilk test statistic: 0.8242687582969666, p-value: 0.09605199843645096)
Fake Data: ✔ (Shapiro-Wilk test statistic: 1.0, p-value: 1.0)

nltx2_avg:
Real Data: ✔ (Shapiro-Wilk test statistic: 0.9025363922119141, p-value: 0.389139860868454)
Fake Data: ✔ (Shapiro-Wilk test statistic: 1.0, p-value: 1.0)

</pre>

결과: c_easy_modification, c_easy_modification 제외하면 모두 normally distributed. 하지만, 샘플 수가 너무 작아서 Parametric을 하면 안됨.

# One Sample T-Test

```python
from scipy.stats import ttest_1samp
```

```python
# Dictionary to store results
results = {}

for variable in variables_to_analyze:
    # Split the data into two groups (real and fake)
    real_data = df[df['group'] == 'real']
    fake_data = df[df['group'] == 'fake']

    # Perform one-sample t-test (using fake data as the reference value)
    t_stat_real, p_value_real_ttest = ttest_1samp(real_data[variable], fake_data[variable].mean())
    t_stat_fake, p_value_fake_ttest = ttest_1samp(fake_data[variable], real_data[variable].mean())

    # Store results in the dictionary
    results[variable] = {
        'ttest_real': {'t_statistic': t_stat_real, 'p_value': p_value_real_ttest},
        'ttest_fake': {'t_statistic': t_stat_fake, 'p_value': p_value_fake_ttest},
    }
```

<pre>
/usr/local/lib/python3.10/dist-packages/scipy/stats/_axis_nan_policy.py:523: RuntimeWarning: Precision loss occurred in moment calculation due to catastrophic cancellation. This occurs when the data are nearly identical. Results may be unreliable.
  res = hypotest_fun_out(*samples, **kwds)
</pre>

```python
# Print the summary
for variable, data in results.items():
    print(f"{variable}:")

    # Real Data
    if not pd.isna(data['ttest_real']['p_value']):
        if data['ttest_real']['p_value'] <= 0.05:
            print(f"✔{variable} is statistically significant (p: {data['ttest_real']['p_value']})")
        else:
            print(f"❌{variable} is statistically NOT significant (p: {data['ttest_real']['p_value']})")
    else:
        print(f"Error: Unable to calculate p-value for {variable} in real data.")

    # Fake Data
    if not pd.isna(data['ttest_fake']['p_value']):
        if data['ttest_fake']['p_value'] <= 0.05:
            print(f"✔{variable} is statistically significant (p: {data['ttest_fake']['p_value']})")
        else:
            print(f"❌{variable} is statistically NOT significant (p: {data['ttest_fake']['p_value']})")
    else:
        print(f"Error: Unable to calculate p-value for {variable} in fake data.")

    print()
```

<pre>
c_preference:
✔c_preference is statistically significant (p: 0.006748438223748888)
✔c_preference is statistically significant (p: 0.0)

c_willingness:
✔c_willingness is statistically significant (p: 0.0021319059412303622)
✔c_willingness is statistically significant (p: 0.0)

c_time-saving:
✔c_time-saving is statistically significant (p: 0.027429225322793283)
✔c_time-saving is statistically significant (p: 0.0)

c_money-saving:
✔c_money-saving is statistically significant (p: 0.00017094757574296357)
✔c_money-saving is statistically significant (p: 0.0)

c_easy_modification:
✔c_easy_modification is statistically significant (p: 0.025031015818452934)
✔c_easy_modification is statistically significant (p: 0.0)

c_exploration:
✔c_exploration is statistically significant (p: 0.00043497743126479045)
✔c_exploration is statistically significant (p: 0.0)

c_floor_plan:
✔c_floor_plan is statistically significant (p: 0.00017094757574296357)
✔c_floor_plan is statistically significant (p: 0.0)

c_camera_manipulation:
❌c_camera_manipulation is statistically NOT significant (p: 0.06675305498495065)
✔c_camera_manipulation is statistically significant (p: 0.0)

c_issue_detection:
✔c_issue_detection is statistically significant (p: 0.0021319059412303622)
✔c_issue_detection is statistically significant (p: 0.0)

c_collaboration:
✔c_collaboration is statistically significant (p: 1.934556185222349e-05)
✔c_collaboration is statistically significant (p: 0.0)

c_avg:
✔c_avg is statistically significant (p: 0.0008756957507416264)
✔c_avg is statistically significant (p: 0.0)

sus_total:
✔sus_total is statistically significant (p: 0.0011705923167988294)
✔sus_total is statistically significant (p: 0.0)

nltx2_avg:
✔nltx2_avg is statistically significant (p: 0.003194453069372255)
✔nltx2_avg is statistically significant (p: 0.0)

</pre>

T-test 결과: c_camera_manipulation를 제외하고는 모든 변수가 statistically significant하다.

하지만, 앞서 말한대로 샘플 수가 작아서 Non-parametric인 Wilcoxon signed-rank test를 해보기로 한다.

# Wilcoxon signed-rank test

```python
from scipy.stats import wilcoxon
```

```python
print(variables_to_analyze)
```

<pre>
['c_preference', 'c_willingness', 'c_time-saving', 'c_money-saving', 'c_easy_modification', 'c_exploration', 'c_floor_plan', 'c_camera_manipulation', 'c_issue_detection', 'c_collaboration', 'c_avg', 'sus_total', 'nltx2_avg']
</pre>

```python
# Dictionary to store results
results = {}

for variable in variables_to_analyze:
    # Split the data into two groups (real and fake)
    real_data = df[df['group'] == 'real']
    fake_data = df[df['group'] == 'fake']

    # Perform one-sample t-test (using fake data as the reference value)
    wilcoxon_stat_real, p_value_real_wilcoxon = wilcoxon(real_data[variable], fake_data[variable])
    wilcoxon_stat_fake, p_value_fake_wilcoxon = wilcoxon(fake_data[variable], real_data[variable])

    # Store results in the dictionary
    results[variable] = {
        'wilcoxon_real': {'wilcoxon_statistic': wilcoxon_stat_real, 'p_value': p_value_real_wilcoxon},
        'wilcoxon_fake': {'wilcoxon_statistic': wilcoxon_stat_fake, 'p_value': p_value_fake_wilcoxon},
    }
    print(p_value_real_wilcoxon)
```

<pre>
0.039359508888249725
0.03125
0.0625
0.03125
0.04550026389635839
0.03125
0.03125
0.0782481864330958
0.03125
0.03125
0.03125
0.03125
0.03125
</pre>
<pre>
/usr/local/lib/python3.10/dist-packages/scipy/stats/_morestats.py:4088: UserWarning: Exact p-value calculation does not work if there are zeros. Switching to normal approximation.
  warnings.warn("Exact p-value calculation does not work if there are "
/usr/local/lib/python3.10/dist-packages/scipy/stats/_morestats.py:4102: UserWarning: Sample size too small for normal approximation.
  warnings.warn("Sample size too small for normal approximation.")
</pre>

```python
# Print the summary
for variable, data in results.items():
    print(f"{variable}:")

    # Real Data
    if not pd.isna(data['wilcoxon_real']['p_value']):
        if data['wilcoxon_real']['p_value'] <= 0.05:
            print(f"✔{variable} is statistically significant (p: {data['wilcoxon_real']['p_value']})")
        else:
            print(f"❌{variable} is statistically NOT significant (p: {data['wilcoxon_real']['p_value']})")
    else:
        print(f"Error: Unable to calculate p-value for {variable} in real data.")

    # Fake Data
    if not pd.isna(data['wilcoxon_fake']['p_value']):
        if data['wilcoxon_fake']['p_value'] <= 0.05:
            print(f"✔{variable} is statistically significant (p: {data['wilcoxon_fake']['p_value']})")
        else:
            print(f"❌{variable} is statistically NOT significant (p: {data['wilcoxon_fake']['p_value']})")
    else:
        print(f"Error: Unable to calculate p-value for {variable} in fake data.")

    print()
```

<pre>
c_preference:
✔c_preference is statistically significant (p: 0.039359508888249725)
✔c_preference is statistically significant (p: 0.039359508888249725)

c_willingness:
✔c_willingness is statistically significant (p: 0.03125)
✔c_willingness is statistically significant (p: 0.03125)

c_time-saving:
❌c_time-saving is statistically NOT significant (p: 0.0625)
❌c_time-saving is statistically NOT significant (p: 0.0625)

c_money-saving:
✔c_money-saving is statistically significant (p: 0.03125)
✔c_money-saving is statistically significant (p: 0.03125)

c_easy_modification:
✔c_easy_modification is statistically significant (p: 0.04550026389635839)
✔c_easy_modification is statistically significant (p: 0.04550026389635839)

c_exploration:
✔c_exploration is statistically significant (p: 0.03125)
✔c_exploration is statistically significant (p: 0.03125)

c_floor_plan:
✔c_floor_plan is statistically significant (p: 0.03125)
✔c_floor_plan is statistically significant (p: 0.03125)

c_camera_manipulation:
❌c_camera_manipulation is statistically NOT significant (p: 0.0782481864330958)
❌c_camera_manipulation is statistically NOT significant (p: 0.0782481864330958)

c_issue_detection:
✔c_issue_detection is statistically significant (p: 0.03125)
✔c_issue_detection is statistically significant (p: 0.03125)

c_collaboration:
✔c_collaboration is statistically significant (p: 0.03125)
✔c_collaboration is statistically significant (p: 0.03125)

c_avg:
✔c_avg is statistically significant (p: 0.03125)
✔c_avg is statistically significant (p: 0.03125)

sus_total:
✔sus_total is statistically significant (p: 0.03125)
✔sus_total is statistically significant (p: 0.03125)

nltx2_avg:
✔nltx2_avg is statistically significant (p: 0.03125)
✔nltx2_avg is statistically significant (p: 0.03125)

</pre>

결과: c_time-saving, c_camera_manipulation 를 제외하고는 모든 변수가 statistically significant하다.
