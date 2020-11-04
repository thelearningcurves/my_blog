---
layout: post
title:  "FWI I "
date:   2020-11-04 15:51:38 +0545
categories: INVERSION
mathjax: true
---





```python
 
import matplotlib.pyplot as plt
import numpy as np
%matplotlib inline
```

# This Jupyter notebook presents forward modelling of acoustic wave propagation into earth.

 The output here has been reproduced with the help of paper called [this awesome paper](https://library.seg.org/doi/abs/10.1190/tle36121033.1) by by Mathias Louboutin, Philipp Witte, Michael Lange, Navjot Kukreja, Fabio Luporini, Gerard Gorman, and Felix J. Herrmann.


We start with a simple velocity model and a domain definition for the simulation of the propagation. The dimensions and different parameters have been provided as comments along with the related codes.


```python
 
from examples.seismic import Model, plot_velocity

#Definition of the physical space
#FIRST, WE DEFINE NUMBER OF GRID POINTS (xn,yn). here y is in depth direction
shape = (101, 101) 
#the spacing between two points in the grid that makes the total dimension of 1000m x 1000m
spacing = (10., 10.)   
#origin defines the top left corner point. this is necessary for defining the absolute position of source and receivers
origin = (0., 0.)   
# Define a velocity profile. The velocity is in km/s
v = np.empty(shape, dtype=np.float32)
v[:, :51] = 1.5
v[:, 51:] = 2.5

#now we make use of the Model class which defines a domain with the parameters defined above. the #nbl gives number of 
#grid points taken by the absorbing layer
model = Model(vp=v, origin=origin, shape=shape, spacing=spacing,
              space_order=2, nbl=10, bcs="damp")

plot_velocity(model)
```

    Operator `initdamp` run in 0.01 s
    Operator `padfunc` run in 0.01 s




![png](output_3_1.png)
    


Since our wave propagates forward (in depth) in time we need to define a timeaxis for the simulation. Here the time is taken in ms.
### The model.critical_dt attribute
The dt or timestep for the wave propagation is chosen according to the The Courant–Friedrichs–Lewy or CFL condition which makes sure that the timestep is smaller than the distance between two grid points. This aids in the stability of the numerical modelling of wave or any fluid propagation model in accordance with the physical law guiding it.
For more regarding the expression and description
 The CFL coefficients are described in:
        - [For elastic case](https://doi.org/10.1137/0916052 ) 
        -[for the acoustic case](https://library.seg.org/doi/pdf/10.1190/1.1444605)


```python
from examples.seismic import TimeAxis

t0 = 0.   
tn = 1000.  
dt = model.critical_dt   
#the RickerSource takes in a TimeAxis object[a devito thing!! nothing special. Just a fancy sequence of time instances!!] that is defined below as time_range
time_range = TimeAxis(start=t0, stop=tn, step=dt)
```


```python
 
from examples.seismic import RickerSource
#The dominant or the peak frequency is in KHz
f0 = 0.010   
#A RickerSource is defined at a single point
src = RickerSource(name='src', grid=model.grid, f0=f0,
                   npoint=1, time_range=time_range)

# First, position source centrally in all dimensions, then set depth
src.coordinates.data[:, 0] = np.array(model.domain_size) * .5
src.coordinates.data[:,1] = 20.  # Depth is 20m

# We can plot the time signature to see the wavelet
src.show()
```


![png](output_6_0.png)
    



```python
 
from examples.seismic import Receiver

# Create symbol for 101 receivers
rec = Receiver(name='rec', grid=model.grid, npoint=101, time_range=time_range)

# Prescribe even spacing for receivers along the x-axis
rec.coordinates.data[:, 0] = np.linspace(0, model.domain_size[0], num=101)
rec.coordinates.data[:, 1] = 20.  # Depth is 20m

#1. the red dot shows the source and the green points show the receivers
plot_velocity(model, source=src.coordinates.data,
              receiver=rec.coordinates.data[::4, :])
```


![png](output_7_0.png)
    


### TimeFunction defines a time varying field and space varying field.
We define u that varies with time and the slowness(m) that varies with the depth or space.


```python

from devito import TimeFunction

# Define the wavefield with the size of the model and the time dimension
u = TimeFunction(name="u", grid=model.grid, time_order=2, space_order=2)

# We can now write the PDE
pde = model.m * u.dt2 - u.laplace + model.damp * u.dt

# The PDE representation is as on paper
pde
```

$$
\displaystyle \operatorname{damp}{\left(x,y \right)} \frac{\partial}{\partial t} u{\left(t,x,y \right)} - \frac{\partial^{2}}{\partial x^{2}} u{\left(t,x,y \right)} - \frac{\partial^{2}}{\partial y^{2}} u{\left(t,x,y \right)} + \frac{\frac{\partial^{2}}{\partial t^{2}} u{\left(t,x,y \right)}}{\operatorname{vp}^{2}{\left(x,y \right)}}$
$$




```python
# This discrete PDE can be solved in a time-marching way updating u(t+dt) from the previous time step
# Devito as a shortcut for u(t+dt) which is u.forward. We can then rewrite the PDE as 
# a time marching updating equation known as a stencil using customized SymPy functions
from devito import Eq, solve

stencil = Eq(u.forward, solve(pde, u.forward))
```


```python
 stencil
```

$$
\displaystyle u{\left(t + dt,x,y \right)} = \frac{- \frac{- \frac{2.0 u{\left(t,x,y \right)}}{dt^{2}} + \frac{u{\left(t - dt,x,y \right)}}{dt^{2}}}{\operatorname{vp}^{2}{\left(x,y \right)}} - \frac{2.0 u{\left(t,x,y \right)}}{h_{y}^{2}} + \frac{u{\left(t,x,y - h_{y} \right)}}{h_{y}^{2}} + \frac{u{\left(t,x,y + h_{y} \right)}}{h_{y}^{2}} - \frac{2.0 u{\left(t,x,y \right)}}{h_{x}^{2}} + \frac{u{\left(t,x - h_{x},y \right)}}{h_{x}^{2}} + \frac{u{\left(t,x + h_{x},y \right)}}{h_{x}^{2}} + \frac{\operatorname{damp}{\left(x,y \right)} u{\left(t,x,y \right)}}{dt}}{\frac{\operatorname{damp}{\left(x,y \right)}}{dt} + \frac{1}{dt^{2} \operatorname{vp}^{2}{\left(x,y \right)}}}
$$

# # The field is ready!!!
 Now, we have defined a grid space with its properties of how a wave will change in time and space. The field is set and now we will inject or simply add the Ricker source we defined and make our receiver move downward (not actually!!, infact we just interpolate the receiver position from 0 to 1000ms so as to capture the simulation) and add it to the stencil.


```python
# Finally we define the source injection and receiver read function to generate the corresponding code
src_term = src.inject(field=u.forward, expr=src * dt**2 / model.m)

# Create interpolation expression for receivers
rec_term = rec.interpolate(expr=u.forward)
```

with our stencil, source and receiver ready we create our operator.The argument *subs=model.spacing_map* causes the operator to substitute values for our current grid spacing into the expressions before code generation.


```python
 
from devito import Operator

op = Operator([stencil] + src_term + rec_term, subs=model.spacing_map)
```


```python
 
op(time=time_range.num-1, dt=model.critical_dt)
```

    Operator `Kernel` run in 0.02 s





    PerformanceSummary([(PerfKey(name='section0', rank=None),
                         PerfEntry(time=0.010302999999999982, gflopss=0.0, gpointss=0.0, oi=0.0, ops=0, itershapes=[])),
                        (PerfKey(name='section1', rank=None),
                         PerfEntry(time=0.004876999999999995, gflopss=0.0, gpointss=0.0, oi=0.0, ops=0, itershapes=[])),
                        (PerfKey(name='section2', rank=None),
                         PerfEntry(time=0.0034380000000000218, gflopss=0.0, gpointss=0.0, oi=0.0, ops=0, itershapes=[]))])



when we run the code the rec.data is filled with data points which is plotted below.


```python
 
from examples.seismic import plot_shotrecord

plot_shotrecord(rec.data, model, t0, tn)
```


![png](output_18_0.png)
    



```python
rec.data
```




    Data([[0.        , 0.        , 0.        , ..., 0.        , 0.        ,
           0.        ],
          [0.        , 0.        , 0.        , ..., 0.        , 0.        ,
           0.        ],
          [0.        , 0.        , 0.        , ..., 0.        , 0.        ,
           0.        ],
          ...,
          [0.02417953, 0.02025361, 0.01816917, ..., 0.01816656, 0.0202527 ,
           0.02417842],
          [0.01702699, 0.0120742 , 0.00884167, ..., 0.00884011, 0.0120723 ,
           0.01702717],
          [0.01058217, 0.00480712, 0.00067352, ..., 0.0006734 , 0.00480482,
           0.01058283]], dtype=float32)




```python
recd = list(rec.data)
len(recd[3])
```




    101



# EXTRAAAAAA

This is to show how receiver interpolation has worked!!

so as the wave moves downward, so does the receiver array. And as the wave attenuates as it moves downward so we see a decaying array of traces that is received by arrays at different positions.


```python
plt.plot(recd[25:200])
plt.tight_layout()
```


![png](output_22_0.png)
    



```python

```
