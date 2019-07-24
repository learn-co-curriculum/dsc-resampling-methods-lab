
# Resampling Methods - Lab

## Introduction

Now that you have some preliminary background on bootstrapping, jackknife and permutation tests, its time to practice those skills by coding them into functions. You'll then apply these tests to a hypothesis test and compare the results to a parametric t-test.

## Objectives

You will be able to:
* Understand permutation testing
* Understand what jackknife is
* Understand what bootstrapping is

## Bootstrapping

Write a function that takes a sample and generates n additional samples of the same size using bootstrapping. (Recall that bootstrapping creates additional sets by sampling with replacement.)


```python
def bootstrap():
    #Your code here
```

## Jackknife 

Write a function that creates additional samples by removing one element at a time. The function should do this for each of the n items in the original sample, returning n samples, each with n-1 members.


```python
def jack1():
    """This function should take in a list of n observations and return n lists
    each with one member (presumably the nth) removed."""
    # Your code here
```

## Permutation Testing

Define a function that generate all possible, equally sized, two set splits of two sets A and B. Sets A and B need not be the same size, but all of the generate two set splits should be of equal size. For example, if we had a set with 5 members and a set with 7 members, the function would return all possible 5-7 ordered splits of the 12 items. 

> Note that these are actually combinations! However, as noted previously, permutation tests really investigate possible regroupings of the data observations, so calculating combinations is a more efficient approach!


Here's a more in depth example:  
```python
>>> A = [1,2,2]
>>> B = [1,3]
>>> combT(A, B) 
[([1,2,2], [1,3]),
 ([1,2,3], [1,2]),
 ([1,2,1], [2,3]),
 ([1,1,3], [2,2]),
 ([2,2,3], [1,1])]
               
```  
These are all the possible 3-2 member splits of the 5 elements : 1,1,2,2,3.


```python
def comb(a,b):
    # Your code here
```

## Permutation Testing in Practice
Let's further investigate the scenario proposed in the previous lesson. Below are two samples A and B. The samples are mock data for the blood pressure of sample patients. The research study is looking to validate whether there is a statistical difference in the blood pressure of these two groups using a 5% significance level.  First, calculate the mean blood pressure of each of the two samples. Then, calculate the difference of these means. From there, use your `combT()` function, defined above, to generate all the possible combinations of the entire sample data into A-B splits of equivalent sizes as the original sets. For each of these combinations, calculate the mean blood pressure of the two groups and record the difference between these sample means. The full collection of the difference in means between these generated samples will serve as the denominator to calculate the p-value associated with the difference between the original sample means.

For example, in our small handwritten example above:

$\mu_a = \frac{1+2+2}{3} = \frac{5}{3}$  
and  
$\mu_b = \frac{1+3}{2} = \frac{4}{2} = 2$  

Giving us

$\mu_a - \mu_b = \frac{5}{3} - 2 = \frac{1}{2}$

In comparison, for our various combinations we have:

([1,2,2], [1,3]):  $\mu_a - \mu_b = \frac{5}{3} - 2 = \frac{1}{2}$  
([1,2,3], [1,2]):  $\mu_a - \mu_b = 2 - \frac{3}{2} = \frac{1}{2}$  
([1,2,1], [2,3]):  $\mu_a - \mu_b = \frac{4}{3} - \frac{5}{3} = -\frac{1}{2}$  
([1,1,3], [2,2]):  $\mu_a - \mu_b = \frac{5}{3} - 2 = \frac{1}{2}$  
([2,2,3], [1,1]):  $\mu_a - \mu_b = \frac{7}{3} - 1 = \frac{4}{3}$  

A standard hypothesis test for this scenario might be:

$h_0: \mu_a = \mu_b$  
$h_1: \mu_a < \mu_b$  
  
Thus comparing our sample difference to the differences of our possible combinations, we look at the number of experiments from our combinations space that were the same or greater then our sample statistic, divided by the total number of combinations. In this case, 4 out of 5 of the combination cases produced the same or greater differences in the two sample means. This value .8 is a strong indication that we cannot refute the null hypothesis for this instance.


```python
a = []
b = []
```


```python
# Your code here
```

## T-test Revisited

The parametric statistical test equivalent to our permutation test above would be a t-test of the two groups. Perform a t-test on the same data above in order to calculate the p-value. How does this compare to the above results?


```python
# Your code here
```

## Bootstrap Applied

Use your code above to apply the bootstrap technique to this hypothesis testing scenario. In other words, similar to the permutation testing you performed above, compute additional samples (arbitrarily let's say 1000) of the same size as the original sample, with replacement. For each of these additional samples, compute whether the difference in sample means is the same or greater then that of the original samples. Use this to calculate an overall p-value for the null hypothesis.


```python
# Your code here
```

## Summary

Well done! In this lab you practice coding modern statistical resampling techniques of the 20th century! You also started to compare these non-parametric methods to other parametric methods such as the t-test that we previously discussed.
