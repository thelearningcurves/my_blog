---
layout: post
title:  "Ridge "
date:   2020-09-23 15:51:38 +0545
categories: DSP
---

Ridge Regression: Analytical way!!(Motivation:Linear Inversion in Geophysics)


```python
from sklearn import datasets
import numpy as np
from numpy import linalg

diabetes = datasets.load_diabetes()
X = diabetes.data
Y= diabetes.target
```

# This is a basic notebook that shows analytical solution of  RIDGE REGRESSION problem, the parameters calculated and compared with Sklearn.linear_model.Ridge() Function.

I solved Mechanics, the Lagrangian way during my undergraduate days and little did I know, the equations of constraints are actually allowing us to realize the maxima or minima of a cost function in a way that the model derive do not overfit. Now, while doing Ridge Regression for ML , the penalty term brought back those memories. 

The blogs and texts that I read online and the videos that I watched on youtube in order to understand the actual meaning of adding and L2 or L1 norm to the cost function was futile. Noone went deep enough and simply concluded with the ambiguous term "Penalized". But in reality, there is lot more to it than simply saying penalizing the big coefficients and getting away.

What it actually does is, creates a predefined constraint that we can control with a parameter( mostly put as LAMBDA) which increases or decreases the strength of our constraint. This constraint could be anything, a straight line, a parabola , a circle with origin as centre or a quadilateral with sides meeting at axes. And we put the values of our ground truth or actual data and the predicted or calculated data i.e. y and y_hat(I consider y as my ground truth and y_hat as my predicted or calculated truth) to the error or loss function, we can actually create a contour with the parameters on the axes and the loss or error calculated as our z value that defines different contour levels. Now, in the case of minimizing our loss function which is mostly the case, the ideal value is zero which is the centre of the contour at infinity. But, we don't want that. We don't want a perfect model because, perfection comes at a cost of simplicity. It comes with high variance and fails to generalize the model. Rather than learning, it memorizes the training data points which means capturing noises that has no value. So, with the constraint we tell the function to stop approacing zero and rather stop at an acceptable value which is mostly the point of intersection of our constraint and the lucky contour line. And volia the point where the tangent of both the level surface is same is our desired error value. This is amazing. 

This small notebook is a way of implementing Least square Method with ridge in an analytical way without using the gradient descent. Have fun.


```python
from scipy.stats import zscore
z = zscore(X)
y= zscore(Y)
 
```
Why Z-score?
Well, Z-score is a standard way of presenting a data value as it is easier to make comparsion with other datasets of similar types.
It simply means how many standard deviation far is a value from it's mean. Negative values mean they are less than certain stds less than mean and positive is the other way around. So each value also gives the idea of spread in a normal distribution and also shows the variance in the data.

Another concept, not used here is the relative z-score where we compare two datasets. In that case, we standardize the datapoints in such a way that the mean is zero and std is 1. Scipy.stats in python has a function called "zmap" that does this in a single line of code.


```python
intercept = np.ones(len(X))
X =np.append(intercept,X)
 

X = np.reshape(X,(442,11))
```

why the intercept?
Well, it seems more practical to give a bias to the dataset as it is very rare to get a centered dataset in real world.
    


```python
def RidgeRegression(x,y,L):
  X = np.matrix(x)
  Y= np.matrix(y).transpose()
  XTX = X.transpose()*X
  XTY =X.transpose()*Y
  LI = L * np.matrix(np.identity(len(XTX)))
  LI[0,0] = 0
  BETA = linalg.inv(XTX-LI)*XTY
  y_hat = np.dot(X,BETA)
  residuals = y_hat - Y
  SSE = residuals.transpose()*residuals
  MSE  = SSE/len(x)
  print('the parameter is :::',BETA)
  print('the MSE is :::' ,MSE)
```


```python
RidgeRegression(z,y,0.1)
```

    the parameter is ::: [[ 0.05696372]
     [-0.0960474 ]
     [-0.12554397]
     [ 0.24121324]
     [-0.07822764]
     [ 0.37552932]
     [-0.10470504]
     [ 0.25816443]
     [-0.40455371]
     [-0.28117194]
     [ 0.14147782]]
    the MSE is ::: [[0.98895015]]
    


```python
from sklearn.linear_model import Ridge
clf = Ridge(alpha = 0.1 ,fit_intercept=True)
clf.fit(z,y)
```




    Ridge(alpha=0.1, copy_X=True, fit_intercept=True, max_iter=None,
          normalize=False, random_state=None, solver='auto', tol=0.001)




```python
clf.coef_
```




    array([ 0.05542193, -0.09434325, -0.12226245,  0.23451536, -0.07528716,
            0.36776402, -0.10115816,  0
            .25178491, -0.39594385, -0.27345596,
            0.13606568])




So, we see that the coefficients that we calculated by the analytical function are similar to that of Ridge( ) function in sklearn.linear_model class which approaches numerically using gradient descent.


*The codes here written with the help of the blog post by [this](https://statcatinthehat.wordpress.com/2014/07/16/regularized-regression-ridge-in-python/)