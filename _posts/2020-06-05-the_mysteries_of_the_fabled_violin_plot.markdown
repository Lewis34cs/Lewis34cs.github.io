---
layout: post
title:      "The Mysteries of the Fabled Violin Plot"
date:       2020-06-05 20:22:02 -0400
permalink:  the_mysteries_of_the_fabled_violin_plot
---

What is the violin plot? Where did it come from? Can you play it? 

To the casual person, reading a violin plot may seem somewhat of an enigma at first, but if you harness its abilities and understand its strengths and weaknesses, it can be a powerful ally.

But, before we dive into the violin plot let's start off with the box-and-whisker plot. 

The box-and-whisker plot shows you many different points, including: all the quartiles, the median, the whiskers, the minimum and maximum values, and outliers of a dataset. This plot is heavily used in probability analysis and statistics. So why would you ever want to use a violin plot? For starters, the violin plot is now considered as the modern age box-and-whisker plot, and they are more artsy and appealing for people that don't live in a box-and-whisker plot kind of world.

The violin plot was created back in 1997 and introduced to the world through the NCSS statistical software. It received its name through one of the first analyses it produced which looked similar to - you guessed it - a violin. Violin plots may be intimidating at first glance, but can provide much more information than your average bar or box-and-whisker plot. Violin plots have the ability to display something like a box plot inside of their violin-shaped (some of the time) bodies. Not only that, but their body represents a density trace of the selected data you're working with, which can show absolute skewness or level of normality, along with whether or not it's multimodal data.

Ultimately, violin plots can give you everythig a box-and-whisker plot can give, and much more. Violin plots come with many parameters for us to use to analyze a dataset. Here is a direct quote from Seaborn's violin plot docstring:

"A violin plot plays a similar role as a box and whisker plot. It shows the
distribution of quantitative data across several levels of one (or more)
categorical variables such that those distributions can be compared. Unlike
a box plot, in which all of the plot components correspond to actual
datapoints, the violin plot features a kernel density estimation of the
underlying distribution.

This can be an effective and attractive way to show multiple distributions
of data at once, but keep in mind that the estimation procedure is
influenced by the sample size, and violins for relatively small samples
might look misleadingly smooth." (1)



Direction for blog post:
starting with box and whisker plots - heavily used in prob analysis and statistics
violin plot is the more modern version of box and whisker plots
they are more artsy and appealing for people that don't live in a box and whisker plot world
they represent the same basic thing, but the elements of the visualization are combined in the violin plot, versus the 5 or 6 different values that can be represented in a bo and whisker plot
more digestable and can represent a better graphical representation !

1. retrieved from https://seaborn.pydata.org/generated/seaborn.violinplot.html
