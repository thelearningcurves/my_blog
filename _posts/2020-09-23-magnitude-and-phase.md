---
layout: post
title:  "Magnitude and Phase Spectra Explained"
date:   2020-09-23 15:51:38 +0545
categories: jekyll update
---
# Magnitude and Phase Spectra

Anyone familiar about the Fourier Transform  knows what it does. It takes a time domain signal and changes it into frequency domain. It's cool when we understand it but might be terrifying when we only see the transform expressions.

Here , I attempt to visually interpret the two results of Fourier transform viz. The Magnitude Spectrum AND The Phase Spectrum. The magnitude spectrum is easy and straight forward to understand. It plots individual signals of particular frequency in a decreasing order i.e on X-axis and amplitude on the Y-axis. 

The confusing part is the Phase Spectrum. Here is how I understand it. When a complex or composite signal is broken into its constituent frequencies, each sine wave might be delayed in time scale from zero phase. And this delay is recorded in form of angles or radians for each component signal.

![The decomposition of a complex signal](https://github.com/thelearningcurves/my_blog/blob/master/images/fourier.PNG?raw=true)

> Here we see the the Fourier transform of a complex signal. The decomposition is shown on two X-axes of frequencies when one has amplitude on Y-axis another has phase on Y-axis in terms of radians. The upper decomposition shows each component with single phase of  180 degrees and the amplitude is in decreasing order. On the lower diagram, we have the same wave where the phases are zero for all the components.

On python we can see a more practical visualization.

```python
import numpy as np
import matplotlib.pyplot as plt

```

```python
dt =0.01
fs = 1/dt
t = np.arange(0,10,dt)
signal =0.1* np.sin(4*np.pi*t)

#the random noise
nse = np.random.randn(len(t))
r = np.exp(-t / 0.05)
cnse = np.convolve(nse, r) * dt
cnse = cnse[:len(t)]
new_signal = signal + cnse

```

Up to this point , **matplotlib**-- the fundamental visualization library and **numpy**  were imported.

We defined a time interval **dt ** and defined a sinusoid signal. One step further, we created some random noise and convolved it with an exponential signal. Then, finally we created a new compound signal by adding the sinusoid with the noise infected exponential signal.

```python
fig, axs = plt.subplots(nrows=3, ncols=3, figsize=(10, 10))

axs[0,0].plot(t,signal) #plotting the pure signal
axs[0,0].set_title('The pure sine signal')

axs[0,1].plot(t,nse)  #plotting the random noise
axs[0,1].set_title('the random noise')

axs[0,2].plot(t,r) #plotting the exponential function
axs[0,2].set_title('An Exponential function')
#plotting the convolution of the noise with theexponential function
axs[1,0].plot(t,cnse)
axs[1,0].set_title('exponential func * random noise')
axs[1,1].plot(t, new_signal) #plotting the new noisy sine signal
axs[1,1].set_title('pure sine wave + exponential func')
axs[1,2].magnitude_spectrum(new_signal, Fs = fs, color = 'red')
axs[1,2].set_title('The magnitude spectrum')
axs[2,0].magnitude_spectrum(new_signal , Fs =fs , color ='green',scale = 'dB')
axs[2,0].set_title('logarithmic magnitude spectrum in decibel')
axs[2,1].magnitude_spectrum(signal, Fs = fs)
axs[2,1].set_title('Magnitude spectrum (pure sine)')
axs[2,2].phase_spectrum(new_signal,Fs = fs , color = 'lime')
axs[2,2].set_title('phase spectrum of combined signal')
fig.tight_layout()
```

![Fourier](https://github.com/thelearningcurves/my_blog/blob/master/images/1.png?raw=true)



Here, it is pretty simple to understand.  In row-2 column-2 we constructed a signal by adding the noisy exponential signal to the pure sinusoid. And when we visualize it's magnitude spectra in red plot, we see the exponential part dominating so much that we hardly see the sinusoid. So, we made another plot but this time in green and with a logarithmic scale. We can see the different frequencies arising from noise, sinusoid and exponential function all combined. At the bottom row, the middle one is magnitude spectrum of the sine wave only. And it is pretty clear that it has only one amplitude as we defined in our code.

The last plot is lime color is the phase spectra of the combined signal and we see as the frequency increases, the phase is increasing. The initial part at around 0.5hz frequency should belong to the sine wave and remaining is the noisy exponential function.

To get a final conclusive understanding Here is the phase spectrum of the pure sine wave.

![sada](https://github.com/thelearningcurves/my_blog/blob/master/images/sada.PNG?raw=true)



Since, the signal has a single frequency it has single phase point.



















```

```

