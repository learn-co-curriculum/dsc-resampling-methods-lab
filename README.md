
# Resampling Methods - Lab

## Introduction

Now that you have some preliminary background on bootstrapping, jackknife and permutation tests, its time to practice those skills by coding them into functions. You'll then apply these tests to a hypothesis test and compare the results to a parametric t-test.

## Objectives

You will be able to:
- Understand permutation testing
- Understand what jackknife is
- Understand what bootstrapping is

## Bootstrapping

Write a function that takes a sample and generates n additional samples of the same size using bootstrapping. (Recall that bootstrapping creates additional sets by sampling with replacement.)


```python
import numpy as np
```


```python
def bootstrap(sample, n):
    return np.random.choice(sample, size=len(sample), replace=True)
```

## Jackknife 

Write a function that creates additional samples by removing one element at a time. The function should do this for each of the n items in the original sample, returning n samples, each with n-1 members.


```python
def jack1(sample):
    """This function should take in a list of n observations and return n lists
    each with one member (presumably the nth) removed."""
    samples = []
    for i in range(len(sample)):
        new_sample = sample[:i] + sample[i+1:]
        samples.append(new_sample)
    return samples
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
from itertools import combinations

def combT(a, b):
    union = sorted(a + b)
    all_combs = []
    for x in set(combinations(union, len(a))):
        union_copy = union.copy()
        for y in x:
            union_copy.remove(y)
        all_combs.append((list(x), list(combinations(union_copy, len(union) - len(a)))))
    return all_combs
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
a = [109.6927759 , 120.27296943, 103.54012038, 114.16555857,
       122.93336175, 110.9271756 , 114.77443758, 116.34159338,
       112.66413025, 118.30562665, 132.31196515, 117.99000948]
b = [123.98967482, 141.11969004, 117.00293412, 121.6419775 ,
       123.2703033 , 123.76944385, 105.95249634, 114.87114479,
       130.6878082 , 140.60768727, 121.95433026, 123.11996767,
       129.93260914, 121.01049611]
```


```python
# Your code here
from scipy.special import comb
n_items = len(a)+len(b)
unique = len(set(a+b))
sample_size = len(a)

print(n_items, sample_size)
print(comb(n_items, sample_size))
```

    26 12
    9657700.0



```python
# Your code here
diff_mu_a_b = np.mean(a) - np.mean(b)
combos = combT(a, b)
print("There are {} possible sample variations.".format(len(combos)))
num = 0 #Initialize numerator
for ai, bi in combos:
    diff_mu_ai_bi = np.mean(ai) - np.mean(bi)
    if diff_mu_ai_bi >= diff_mu_a_b:
        num +=1
p_val = num / len(combos)
print('P-value: {}'.format(p_val))
```

    There are 9657700 possible sample variations.
    P-value: 0.9890762811021258


## T-test Revisited

The parametric statistical test equivalent to our permutation test above would be a t-test of the two groups. Perform a t-test on the same data above in order to calculate the p-value. How does this compare to the above results?


```python
import scipy.stats as stats
```


```python
num = np.mean(a) - np.mean(b)
s = np.var(a+b)
n = len(a+b)
denom = s/np.sqrt(n)
t = num / denom
pval = stats.t.sf(np.abs(t), n-1)*2
print(pval)
```

    0.6196331755824978


## Bootstrap Applied

Use your code above to apply the bootstrap technique to this hypothesis testing scenario. In other words, similar to the permutation testing you performed above, compute additional samples (arbitrarily let's say 1000) of the same size as the original sample, with replacement. For each of these additional samples, compute whether the difference in sample means is the same or greater then that of the original samples. Use this to calculate an overall p-value for the null hypothesis.


```python
# Your code here
iterations = 10**4
diff_mu_a_b = np.mean(a) - np.mean(b)
num = 0 #Initialize numerator
for n in range(iterations):
    ai = bootstrap(a, len(a))
    bi = bootstrap(b, len(b))
    diff_mu_ai_bi = np.mean(ai) - np.mean(bi)
    if diff_mu_ai_bi >= diff_mu_a_b:
        num +=1
p_val = num / iterations
print('P-value: {}'.format(p_val))
```

    P-value: 0.5034


## Summary

Well done! In this lab you practice coding modern statistical resampling techniques of the 20th century! You also started to compare these non-parametric methods to other parametric methods such as the t-test that we previously discussed.
