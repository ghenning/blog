---
layout: post
title: "Some basics on Fast Radio Burst searching"
date: 2022-07-11
description: Looking for signals in time-series data 
tags:
  - science
  - tech
---

Most of my academic career was spent looking for Fast Radio Bursts (FRBs). What are FRBs you ask? FRBs are millisecond-duration, extragalactic radio signals. That's as far as I'll go in describing what they are because FRBs are still an unresolved mystery and I could write multiple, long-winded posts discussing what they could or could not be.

No, in this post I just wanted to showcase the basics of how we go about finding an FRB in data.

First, how does typical FRB data look like? Simply put it's a time-series of signal strength over time (think of the needle of a seismic monitor moving up and down like you've seen in the movies). But for FRB data we usually observe over many frequencies, meaning that we have as many lines as we have frequency channels. All these channels are averaged together, forming a single time-series. 

Electrons in space have a frequency-dependent effect on radio signals, making the signals arrive at slightly different times across the observed frequencies. This is called dispersion and needs to be accounted for when searching for FRBs. This means that in order to search the data, they need to be corrected for this delay (de-dispersed) over multiple trials. Let's imagine we live in a world where dispersion doesn't exist and our data are not dispersed.

We now have a single time-series that is "not dispersed" (i.e. we're assuming we have de-dispersed to a correct value). Now we just have to perform what we call single pulse searching. That's usually done by matched filtering which convolves the time-series with a signal of a known shape. The convolution can be computationally expensive so usually the shape used is a boxcar filter. 

Note that I'll be mentioning stuff like samples, time samples, and widths. These all refer to time. Radio data is not continuous in time, but rather a collection of discrete samples, adjacent in time. The size of a time sample differs between observing setups, so it's easier to simply think in samples. This leads to widths, simply meaning X number of samples.

There are two main convolution methods. The first one is implemented by computing a running sum for the data with a given boxcar filter size and subtracting it from the data at plus/minus the filter width. The second one is done by performing a fast Fourier transform (FFT) convolution. The magic here is that multiplication in the frequency domain corresponds to convolution in the time domain. So the time-series are transformed to the frequency domain with an FFT, multiplied by the filter, and then transformed back to the time domain with an inverse FFT. Below you'll find a figure showing how convolution works.

| ![](https://drive.google.com/uc?export=view&id=1YV70K-0eeVyTUZHDRiBDZPL_B_CDnlbO) |
| ![](https://drive.google.com/uc?export=view&id=1Y-rIToKvXKLfbyMBpV3viqdBAfldUXYS) |
| Convolution of time-series with a boxcar filter. *Left*: Data time-series as standard deviation from the median value over time samples. *Center*: Boxcar filter to convolve with the time-series. *Right*: Convolution of the previous two panels. The top row shows the convolution for a time-series containing an FRB, while the bottom row shows the process for a time-series containing only noise. |

Although the duration of FRBs is on the scale of milliseconds, their actual duration varies a bit. This means that one boxcar width does not work for all bursts. Therefore we must try a bunch of different boxcar widths in order to see the signal spike of the FRB. The closer the boxcar width is to the actual duration of the burst, the stronger the signal looks and the chance of actually detecting the FRB increases. This is visualized in the figure below.

| ![](https://drive.google.com/uc?export=view&id=1InzjWlZUt-Z01oxfDJIR35EYq1qEDcD5) |
| Peak signal-to-noise as a function of trial boxcar widths which are convolved with time-series containing a 20-sample wide, simulated burst. The vertical dashed line indicates a boxcar width of 20 samples. We're assuming here that the data have been de-dispersed to the correct value. |

So, we've gone through the basics of searching for an FRB: matched filtering and trial boxcar widths. We've conveniently ignored dispersion, which adds another layer of complexity, and we've only looked at what we call "total intensity data" that does not factor in polarization. At its core, searching is not so difficult, but can easily become way more cumbersome.  

This post is based on a subsection of **[my thesis](https://ui.adsabs.harvard.edu/abs/2021PhDT........12H/abstract)**. Hopefully you learned and/or enjoyed this topic. If you're interested in more, the second chapter of my thesis is on data acquisition and searching of radio data. I might also pick more topics from my thesis to write posts about later on.

Ok, bye.
