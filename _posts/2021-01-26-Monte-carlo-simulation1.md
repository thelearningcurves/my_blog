---
layout: post
title:  "Monte Carlo Simulation"
date:   2021-01-26 15:51:38 +0545
categories: General Optimization
mathjax: true
---

# Monte Carlo Simulation for spring bead system

# Steps
 ### The libraries required for the MC simulation are to be imported. Now we define functions for the simulation.


```python
import numpy as np
import matplotlib.pyplot as plt
import random

def state_of_x():
    return random.randrange(0,25)
def energy_State(x0,x,k):
    return 0.5*k*((x-x0)**2)

```

#### We have defined two functions, one to generate value of x ( the distance between two beads ) from the range given in the question. According to first part the extreme values are : $$  x_0  and \ 0  $$ and x runs from $$ 5x_0 \\ to \ 0 $$


```python
#monte carlo simulation
list1 =[]
list2 = []

def monte_carlo(n):
    results = 0
    energies = 0
    mean_x = 0
    for i in range(n):
        x_results=state_of_x()
        results = results + x_results
        energy = energy_State(2,x=x_results,k=1)
        energies = energies + energy
        
        #calculate the probabilities
        prob_value = results/(i+1)
        energy_prob = energy/(i+1)
        list1.append(prob_value)
        list2.append(energy_prob)
        
    return results/n , energies/n, mean_x
    
```


```python
def plot_mc(x_list,mean):
    plt.xlabel('Monte Carlo Steps')
    plt.ylabel('values of x')
    plt.plot(x_list)
    plt.axhline(y = mean, color ='green', linestyle = '-')
    print( " The average value <x> is ::", mean)
monte_carlo(1000)
plot_mc(list1,np.mean(list1))
```

     The average value <x> is :: 12.215287730506006
    


![png](https://github.com/thelearningcurves/my_blog/blob/master/images/mc1.png?raw=true)


#### In the plot above, we see in 1000 repetitions, the value of x gets close to it mean value. The mean value is represented by the green line which is around 11.72.

 

 #### In part 2: we take $$ x_0 = 2 \ and \ x_0 = 5 $$ and we need to plot the equilibrium of <x> 


```python
# for x0 = 5 and x0 =2
x01 = 5
x02 = 2

def state_of_x1():
    return random.randrange(0,5*x01)
def state_of_x2():
    return random.randrange(0,5*x02)


```


```python
 #monte carlo simulation
list_x1 =[]
list_x2 = []

def monte_carlo_xx(n):
    results1 = 0
    results2 = 0
    mean_x1 = 0
    mean_x2= 0
    for i in range(n):
        x1_results=state_of_x1()
        x2_results = state_of_x2()
        results1 = results1 + x1_results
        results2 = results2 + x2_results
        
        
        #calculate the probabilities
        prob_value1 = results1/(i+1)
        prob_value2 = results2/(i+1) 
        list_x1.append(prob_value1)
        list_x2.append(prob_value2)
        
    return results1/n , results2/n  
    
```


```python
monte_carlo_xx(500)
```




    (12.102, 4.616)




```python
plot_mc(list_x1,np.mean(list_x1))
plot_mc(list_x2,np.mean(list_x2))

```

     The average value <x> is :: 12.126648762125622
     The average value <x> is :: 4.577300638660278
    


![png](https://github.com/thelearningcurves/my_blog/blob/master/images/mc2.png?raw=true)


#### For two different values of $$ x_0 $$ we see that when the extreme position is small, it gets closer to its mean value lot more quicker than the high value.

## Next, we define a variance function that takes a list or array as an input


```python
def variance (List):
    squares = [x**2 for x in List] 
    variance =np.mean(squares) - ((np.mean(List))**2)
    return variance
```


```python
variance(list_x1)

```




    0.41210616831506286




```python
np.var(list_x1)
```




    0.41210616831508073




```python
list_e1 = []
list_e2 = []
list_e3 = []
def monte_carlo_energy(n):
     
    energies1 = 0
    energies2 = 0
    energies3 = 0
    for i in range(n):
        x_results=state_of_x()
         
        energy = energy_State(2,x=x_results,k=0.1)
        energies1 = energies1 + energy
        #calculate the probabilities
      
        energy_prob = energy/(i+1)
      
        list_e1.append(energy_prob)
    for i in range(n):
        x_results=state_of_x()
        
        energy = energy_State(2,x=x_results,k=1)
        energies2 = energies2 + energy
        #calculate the probabilities
      
        energy_prob = energy/(i+1)
      
        list_e2.append(energy_prob)
    for i in range(n):
        x_results=state_of_x()
         
        energy = energy_State(2,x=x_results,k=10)
        energies3 = energies3 + energy
        #calculate the probabilities
      
        energy_prob = energy/(i+1)
      
        list_e3.append(energy_prob)
        
    return   energies1/n, energies2/n ,energies3/n 
    
```

## For three different values of spring constant (k) viz. 0.1 , 1.0, 10 we collect the random energy states for a fixed value of x0 and x.


```python
monte_carlo_energy(100)
```




    (7.0009999999999994, 77.785, 560.6)




```python
variances = [variance(i) for i in [list_e1,list_e2,list_e3]]
variances
```




    [7.278378251814802, 48.54297553042784, 3656.340077835281]




```python
K = [0.1,1,10]
plt.plot(K , variances)

```




    [<matplotlib.lines.Line2D at 0x2a09c9e7088>]




![png](https://github.com/thelearningcurves/my_blog/blob/master/images/mc3.png?raw=true)


### We see that as the value of the spring constant increases the variance


```python
 
```


```python
np.var (list_e3)
```




    3656.340077835282




```python

```
