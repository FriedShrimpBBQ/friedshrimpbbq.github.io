---
layout: post
title:  "Practical thoughts on 'Wide and Deep' machine learning learning"
date:   2023-01-26
categories: machine-learning onnx
---

The 'Wide and Deep' deep learning model is not a new model, its another older approach for attempting to move deep learning to the tabular setting. Of course being an older approach it perhaps is unsurprising that the benchmarks at the time (and even now) do not surpasss a naively tuned gradient boosting machine (GBM). 

Nevertheless it is worthwhile thinking about, as it offers opportunities to perform mini-batch learning over extremely large datasets which may be important in resource constrained environments. At the time of writing 'Wide and Deep' is over 5 years old! As such the explosion or expectation of having large resource GPUs was not an embedded assumption. This means that 'Wide and Deep' can theoretically be trained on fairly modest hardware (though of course still slower than GBMs if the dataset can fit completely in memory). 

## Broad Idea

'Wide and Deep' focuses on a fairly simple concept: build two neural architectures, one that is wide and one that is deep and combine them together to learn a particular concept or idea. 

The wide model is your linear model, whereas your deep model is comprised of creating dense embeddings of various concepts and using a multi-layer perceptron (MLP) to determine the final prediction. 

The advantages of this approach is it can be applied to out-of-the-box deep learning frameworks without custom layers or approaches.

## Handling Data Types

'Wide and Deep' doesn't explicitly specify how all data types are to be handled, but it provides overview and guidance. 

- String variables can be handled through typical string venctorization approaches which are common in the deep learning space. To reduce the dimensionality, dense embeddings are created before the MLP stage
- Similarly categorical variables can be combined with an embedding or simply one-hot encoded based on heuristics such as number of categories

This provides a flexible and consistent choice for building out the neural network. 

When deploying these models, it can be easily exported to formats such as ONNX (though we may need to pre-process the inputs to be ONNX friendly - more on that later)

## Looking Forward

Maybe at some stage, I'll explore how we deploy a generic end to end pipeline using 'Wide and Deep' and compare it with GBM using ONNX as the backbone.

One of the challenges I observe is the lack of official Golang support for ONNX. In this instance, I may rely on more "vanilla" Python and try to have a generic approach to perform preprocessing and deployments in this framework.





