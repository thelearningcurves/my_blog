---
layout: post
title:  "Seismic Migration"
date:   2020-10-01 15:51:38 +0545
categories: Seismic
usemathjax: true
---



# Why Migration Velocity should be chosen carefully during Seismic Migration?



Migration is one of the conclusive step in the processing of seismic data. To understand it simply, during migration we try to bring the reflector to its original geological location from recorded apparent location. But, Why do we get it in an apparent location in the first place?

Well, this is due to the fact that we want the traces to be recorded with zero offset setting. And , during such setting, we only see the first arrivals that are exactly below the (source,receiver) point. If the reflector is exactly flat, then we don't have a problem. But, with a slight bit of dip, the recorded trace becomes an apparent one.  The reflection that originates at a much shallower point appears to arrive from a much deeper point because the deeper point lies just below the (s,r) point. In reality, the wave-front that move toward reflector from source strike multiple points  on a reflector at different time interval if the reflector has a considerable dip. 

![migration](https://www.researchgate.net/profile/Paul_Veeken/publication/326208399/figure/fig38/AS:652969859088386@1532691554723/The-Kirchhoff-migration-uses-the-Huygens-Principle-for-a-diffraction-point-A.png)



The diagram is easy to understand for a single point. While in depth( real geologic setting) the same point is reflecting rays of all the receivers. But in time section, due to our zero offset condition, each ray appears to arise from just below each receiver, thus forming a diffraction hyperbola.

In order to remove the clouds of confusion, please understand, that migration of a single point is often explained in textbooks to make you understand the Kirchhoff's migration algorithm. And to understand the basic general ideas of migration, we always should start with migration of a dipping event.

A migrated section is more or less ready for interpretation and location of various layers. Evidently, it is also one of those process that needs extreme care while choosing the parameters for it.

When Migration is performed, two displacement takes place for a reflector. One in the vertical direction which is displacement of time interval and the horizontal spatial displacement. And for a fixed dip in unmigrated section, the dip gradually increases with depth in migrated section.

Chun and Jacewitz derived some awesome formulas:

$$
d_x = \frac {v^2} t * \frac {\Delta t} {\Delta x}       \tag {1 (Horizonatal/spatial displacement)}
$$

$$
d_t = t  \left[1- \sqrt{ 1- \left( \frac {v}2 \frac{\Delta t} {\Delta x}  \right)^2}\right] \tag{2(temporal/time displacement)}
$$

$$
\frac {\Delta \tau} {\Delta x} = \frac {1}{\sqrt {1- \left( \frac {v}2 \frac{\Delta t} {\Delta x} \right)^2}} \tag{3(change in dip in migrated section)}
$$

here   in the 3rd equation tau is simply a representation of actual travel time interval in a time migration scenario.



Now to see the importance of Migration velocity, equation (1) has it all.  The spatial displacement is proportional to the medium velocity squared. So a 20 percent error in the velocity propagates to a 44 percent error in displacement. So, it is a wise decision to spend substantial amount of time on velocity modelling and semblance analysis. An accurate velocity model can save us from a lot of loss.

Another aspect to see is in equation (3) which the dip in the migrated section. Even if the dip in unmigrated is kept a constant, we can see that it increases due to it's dependence on velocity as well. So, we say as depth increases migrated reflectors get steeper.





Reference: Seismic Data Analysis (Oz Yilmaz)