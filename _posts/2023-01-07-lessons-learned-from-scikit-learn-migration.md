---
layout: post
title:  "Lessons Learned Upgrading scikit-learn to 1.X"
date:   2023-01-07
categories: python machine-learning scikit-learn numpy pandas
---


Its the first week of 2023, by now, `scikit-learn` version 1.X has been out for [over a year](https://scikit-learn.org/stable/whats_new/v1.0.html#version-1-0-0) now, and has crippled our workplace productivity for roughly 9 months due to my workplace graciously upgrading our laptops to M1s which can't install `scikit-learn < 1.x`! 

This is a story talking about the steps we took to upgrade - not necessarily to help readers in upgrading, but more so as an exercise to think through why sometimes thing are so much more difficult that first blush. 

## The Setup - Solving Model Imports

Inertia is a difficult beast to beat. If something is working fine, there is no reason to upgrade anyway! But being crippled/reduce to relying on Docker to run older versions of scikit-learn was certainly a painful experience in developing machine learning model hosting solutions (you may be wondering why this was the solution - we'll leave that for another post!).

When dealing with changes in scikit-learn APIs we first tried to patch up the `sys` modules for APIs/imports that have been changed, for example:

```py
from sklearn.linear_model
sys.modules['sklearn.linear_model.logistic'] = linear_model
```

This at least allowed models to be imported to be served - but this lead to our first roadblock - the actual breaking APIs! With over 100 models hosted, this was simply deprioritized and left by the way-side.

**Possible Solutions**

- Retrain all the models - costly and requires heavy auditing of all models as they are considered business critical
- Rewrite all models by extracting artifacts - fragile due to introducing hand-crafted code

## Overcoming Inertia

As with many organisations prioritising this kind of engineering excellence is often left by the wayside with priority going towards initiatives like improving value through deploy more models or deploying models quicker. 

Instead the way we eventually had time to invest in looking into a solution came in the form of adding support for deep learning models through [ONNX](https://onnx.ai/)

ONNX offers an opportunity to provide a language agnostic way for running models - very similar to PMML, with the ability to export models, it gave us a gateway to export models from different versions of scikit-learn to run in a unified ONNX runtime. ONNX objects also resulted in smaller binaries and used less memory during inference!

ONNX is not without issues. Performing it naively meant losing precision. For example, the default precision when using ONNX was `f32`, whereas many models operating at `f64`. One obvious solution was to selectively migrate transformers like one-hot encoding transformers which obviously wouldn't suffer from this problem.

Unfortunately the conclusion is rather boring - we deprecated some problematic models in favour of newer deep learning models, and made practical decisions to upgrade. The end result (although it took 9 months of "real-time" and perhaps 4 weeks of developer effort and 4 weeks of data scientist's auditing efforts) was successfully upgrading `scikit-learn` via inclusion of ONNX with no downtime. 

As a side benefit we actually improved performance through some dedicated refactoring efforts that occured at the same time. We'll leave that as a discussion in a future post!