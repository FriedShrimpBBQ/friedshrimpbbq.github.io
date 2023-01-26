---
layout: post
title:  "Creating Portable Transformers via ONNX"
date:   2023-01-25
categories: machine-learning paper research
---

One thing that I'm passionate about is creating things that a portable and can run in a variety of platforms. One such framework in the Deep Learning space is [ONNX](https://onnx.ai/). Although it is optimised or targetting Deep Learning and Machine Learning, the truth is it can more or less act as a method for efficiently creating functions that can run or target a variety of platforms, including C, C++, Java, JavaScript and Python (as long as the onnxruntime is present). 

In particular, the `ai.onnx.ml` domain of ONNX is interesting in that it extends beyond Deep Learning. To create these graphs or models in Python, there are several components:

* ensure you mark the `domain` to be `ai.onnx.ml`
* when creating the model, set the `opset_imports` to have the `ai.onnx.ml` domain`

For example, if we wanted to one-hot encode a string using this approach it would look like the following:

```py
import numpy as np
import onnx
from onnx import TensorProto
from onnx.checker import check_model
from onnxruntime import InferenceSession

node = onnx.helper.make_node(
	"OneHotEncoder",
	inputs=["x"],
	outputs=["y"],
	cats_strings=["Amy", "Sally"],
	domain="ai.onnx.ml",
)

graph = onnx.helper.make_graph(
	[node],
	"one_hot_encoder",
	[onnx.helper.make_tensor_value_info("x", TensorProto.STRING, [None])],
	[onnx.helper.make_tensor_value_info("y", TensorProto.FLOAT, [None])],
)

onnx_model = onnx.helper.make_model(
	graph,
	opset_imports=[
		onnx.helper.make_opsetid("ai.onnx.ml", 2),
	]
)

check_model(onnx_model)

sess = InferenceSession(onnx_model.SerializeToString())
output = sess.run(None, {"x": np.array(["Dori", "Amy", "Sally"])})

assert np.array_equal(output[0], np.array([[0., 0.], [1., 0.], [0., 1.]]).astype(np.float32))
```

This should provide a setup then enables us to create portable transformations via ONNX. We can extract out particular nodes and rebuild the graph as needed as well. Suppose, that we wanted to use an existing node in the model above with a new graph, we could accomplish this by extracting the `NodeProto` object and combining them, or alternatively leverage the `onnx.compose.merge_graphs` to accomplish this. 

Unfortunately, the tooling currently is a bit lacklustre and non-trivial to figure out how to build complex pipelines across frameworks and setup. 

My opinion is people are still figuring out the correct patterns and abstractions to safely convert to ONNX format, and perhaps more practically, the number of people who _care_ (because most of the time just dumping a model in a docker container in the cloud is quicker and "good enough") is not particularly high. 

I'm looking forward to how the space evolves overtime - hopefully dedicated WASM ABI support comes as well. 

