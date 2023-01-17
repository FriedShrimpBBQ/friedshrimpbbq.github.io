---
layout: post
title:  "A Gentle guide to Boosting"
date:   2023-01-13
categories: machine-learning paper research
---

Often in academic literature, boosting is introduced through [AdaBoost](https://en.wikipedia.org/wiki/AdaBoost) or maybe sometimes they jump straight into [Gradient Boosting](https://en.wikipedia.org/wiki/Gradient_boosting). In this post, I thought I'll introduce it in a different way, using "Gentle Boosting", which is far easier to implement and understand. 

In Python-esque code, Gentle Boost is implemented as follows:

```py
# with training set, <X, y>
learning_penalty = 1/1.1  # a parameter of interest
w = np.array([1.0 for record in X.shape[0]])
for model in models:
	model.fit(X, y, weights=w)
	# update w based on error
	# set record 1 if correctly predicted, -1 if incorrect
	weight_update_factor = ((y == model.predict(X)) * 2) -1
	w = w * np.power(learning_penalty, weight_update_factor)

# inference
np.mean([model.predict(X) for model in models])
```
  
 What's going on here?
 
 1. We train the model based on weights of the observation (higher weights of previous model got the prediction wrong, lower weight if the previous model got it correct)
 2. At each step we augment the weights by a fixed pre-known penalty, by `alpha` for correct predictions and `1/alpha` for incorrect predictions.
 3. For prediction, simply take the average
 
 This works in the gentle boosting framework by using newton steps to update the weights of the model. By having fixed step sizes for updating the weights it ensures that the weights are bounded and outliers won't over-weigh the model. The original formulation uses scaling in the form of `alpha = exp(1)`, based on the expoential objective function. In the above formation the scaling is a parameter of the model. This can be interpretted as a modified objective variation to gentle boost instead.
 
 I think this variation is very simple to understand and implement whilst still being somewhat more effective than not using boosting at all. 