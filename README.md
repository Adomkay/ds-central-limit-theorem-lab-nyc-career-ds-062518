
# Central Limit Theorem Lab

## Problem Description

In this lab, we'll learn how to use the Central Limit Theorem to work with non-normally distributed datasets as if they were normally distributed.  

### Objectives
* Determine if a dataset is normal or non-normal
* Create a function to sample with replacement and generate sample means
* Create a sample distribution of sample means
* Answer questions about non-normally distributed datasets by working with the normally distributed sample distribution of sample means.  

We'll need the following libraries in order to complete this lab.  Run the cell below to import them all and set the appropriate aliases. 


```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
%matplotlib inline
import seaborn as sns
import scipy.stats as st
np.random.seed(0)
```

Next, read in the dataset.  A dataset of 10,000 numbers is stored in `non_normal_dataset.csv`. Use pandas to read the data in to a series.

**_Hint:_** Any of the `read_` methods in pandas will store 1-dimensional in a Series instead of a DataFrame if passed in the optimal parameter `squeeze=True`.


```python
data = pd.read_csv('non_normal_dataset.csv', squeeze=True)
print(len(data)) # 10000
```

    10000
    

### Detecting Non-Normal Datasets

Before we can make use of the normal distribution, we need to first confirm that our data is normally distributed.  If it is not, then we'll need to use the Central Limit Theorem to create a sample distribution of sample means that will be normally distributed.  

There are two main ways to check if a sample follows the normal distribution or not.  The easiest is to simply plot the data and visually check if the data follows a normal curve or not.  

In the cell below, use `seaborn`'s `distplot` method to visualize a histogram of the distribution overlaid with the a probability density curve.  


```python
sns.distplot(data)
```




    <matplotlib.axes._subplots.AxesSubplot at 0x15157cd4f60>




![png](output_5_1.png)


As expected, this dataset is not normally distributed.  

For a more formal way to check if a dataset is normally distributed or not, we can make use of a statistical test.  There are many different statistical tests that can be used to check for normality, but we'll keep it simple and just make use the `normaltest` function from scipy--see the documentation if you have questions about how to use this method. 

In the cell below, use `normaltest()` to check if the dataset is normally distributed.  


```python
st.normaltest(data)
```




    NormaltestResult(statistic=43432.811126532004, pvalue=0.0)



The output may seem a bit hard to interpret since we haven't covered hypothesis testing and p-values yet.  However, the function tests the hypothesis that the distribution passed into the function differs from the normal distribution.  The null hypothesis would then be that the data is normally distributed.  For now, that's all you need to remember--this will make more sense once you understand p-values.  

Since our dataset is non-normal, that means we'll need to use the **_Central Limit Theorem._**

### Sampling With Replacement

In order to create a Sample Distribution of Sample Means, we need to first write a function that can sample with replacement.  

In the cell below, write a function that takes in an array of numbers `data` and a sample size `n` and returns an array that is a random sample of `data`, of size `n`.


```python
def get_sample(data, n):
    sample = []
    while len(sample) != n:
        x = np.random.choice(data)
        sample.append(x)
    
    return sample

test_sample = get_sample(data, 30)
print(test_sample[:5]) # [56, 12, 73, 24, 8] (This will change if you run it mutliple times)
```

    [56, 12, 73, 24, 8]
    

### Generating a Sample Mean

Next, we'll write another helper function that takes in a sample and returns the mean of that sample.  


```python
def get_sample_mean(sample):
    return sum(sample) / len(sample)

test_sample2 = get_sample(data, 30)
test_sample2_mean = get_sample_mean(test_sample2)
print(test_sample2_mean) # 45.3 (This will also change if you run it multiple times)
```

    49.7
    

### Creating a Sample Distribution of Sample Means

Now that we have helper functions to help us sample with replacement and calculate sample means, we just need bring it all together and write a function that creates a sample distribution of sample means!

In the cell below, write a function that takes in 3 arguments: the dataset, the size of the distribution to create, and the size of each individual sample.  The function should return a sample distribution of sample means of the given size.  


```python
def create_sample_distribution(data, dist_size=100, n=30):
    sample_dist = []
    while len(sample_dist) != dist_size:
        sample = get_sample(data, n)
        sample_mean = get_sample_mean(sample)
        sample_dist.append(sample_mean)
    
    return sample_dist

test_sample_dist = create_sample_distribution(data)
print(test_sample_dist[:5]) # [54.53333333333333, 60.666666666666664, 37.3, 39.266666666666666, 35.9]
```

    [54.53333333333333, 60.666666666666664, 37.3, 39.266666666666666, 35.9]
    

### Visualizing the Sample Distribution as it Becomes Normal

The sample distribution of sample means isn't guaranteed to be normal after it hits a magic size.  Instead, the distribution begins to approximate a normal distribution as it gets larger and larger.  Generally, 30 is accepted as the number for sample size where the Central Limit Theorem begins to kick in--however, there are no magic numbers when it comes to probability. On average, and only on average, a sample distribution of sample means where the individual sample sizes were 29 would only be slightly less normal, while one with sample sizes of 31 would likely only be slightly more normal.  

Let's create some sample distributions of different sizes and watch the Central Limit Theorem kick in as it begins to approximate a normal distribution as it grows in size.  

In the cell below, create a sample distribution from `data` of `dist_size` 10, with a sample size `n` of 3. Then, visualize this sample distribution with `distplot`.


```python
# Visualize sample distribution with n=3, 10, 30, across across mutliple iterations
sample_dist_10 = create_sample_distribution(data, 10, 3)
sns.distplot(sample_dist_10)
```




    <matplotlib.axes._subplots.AxesSubplot at 0x1515b20e5c0>




![png](output_16_1.png)


Now, let's increase the `dist_size` to 30, and `n` to 10.  Create another visualization to compare how it changes as size increases.  


```python
sample_dist_30 = create_sample_distribution(data, 30, 10)
sns.distplot(sample_dist_30)
```




    <matplotlib.axes._subplots.AxesSubplot at 0x1515b37d978>




![png](output_18_1.png)


The data is already looking much more 'normal' than the first sample distribution, and much more 'normal' that the raw non-normal distribution we're sampling from. 

In the cell below, create another sample distribution of `data` with `dist_size` 1000 and `n` of 30.  Visualize it to confirm the normality of this new distribution. 


```python
sample_dist_1000 = create_sample_distribution(data, 1000, 30)
sns.distplot(sample_dist_1000)
```




    <matplotlib.axes._subplots.AxesSubplot at 0x1515b39f208>




![png](output_20_1.png)


Great! As we can see, the dataset _approximates_ a normal distribution. It isn't pretty, but it's generally normal enough that we can use it to answer questions using z-scores and p-values.  

Another handy feature of the Central Limit Theorem is that the mean and standard deviation of the sample distribution should also approximate the population mean and standard deviation from the original non-normal dataset!  Although it's outside the scope of this lab, we could also use the same sampling methods seen here to approximate other parameters from any non-normal distribution, such as the median or mode!


### Conclusion

In this lab, we learned how to:
* Determine if a dataset is normal or non-normal
* Create a function to sample with replacement and generate sample means
* Create a sample distribution of sample means
* Answer questions about non-normally distributed datasets by working with the normally distributed sample distribution of sample means.  
