---
layout: post
title:  "Notes on 'Applying Deep Learning To Airbnb Search'"
date:   2023-01-06
categories: machine-learning paper research
---

I'm fascinated by deep learning advances - perhaps in a really weird way betting _against_ deep learning for tabular learning simply because how difficult it is to produce a competitive benchmark against tree-based without serious tuning and compute effort. 

One reason why I'm interested in this paper is the recommendations on _feature_ _engineering_ to help deep learning models. Now if you are familiar with Deep Learning for images and text, you'll realise this is a really strange irony! Afterall the purpose of _deep_ _learning_ is so you don't have to do it in the first place. This works better for images due to known range of pixel values (0-255), and also text as the vocabulary is often fixed and mapped to an embedding (which would have a "nice" distribution). 

What does ['Applying Deep Learning to Airbnb Search'](https://arxiv.org/abs/1810.09591) recommend? 

## Univariate Analysis

- For normally distributed variables, simply apply a standard scaler
- For variables distributed by the power law: `log((1+value)/median(value))`

Why do these work? The key observation is to control the gradient explosion and gradient vanishing problem - by rescaling back the possible values it increases the stability of the gradients that the network needs to learn. 

The goal of this is the control the majority of the values to be between `{-1, 1}` with the median to be close to `0`. 

Of course, not all features fit "nicely" in these two categories of features. As they point out, things like geolocation around long/lat needed custom treatment. I surmise features which are bimodal in nature may need particular splits and flags to ensure they operate correctly. 

## Categorical Variables

One interesting aspect that was pointed out was learning embeddings directly for categorical data rather than pre-processing like how we might do this for tree-based models. The rough workflow used is:

- Hash the categorical variable
- Feed the hashed value to an embedding

and use this setup to learn the relevant information. 

Furthermore, they also combined relatively static variables to be embedded as well, treating them as a categorical variable. 

## Looking Forward

There are new models coming all the time. For example TabNet is a model that promises competitive performance to tree-based models. Maybe at some stage I will actually give these recommendations a go. Though from a high level perspective, we require a lot of hand-holding to ensure Deep Learning "has a chance".

Let's see what happens in the future. Perhaps tree-based models may eventually fall by the wayside in the near future.
