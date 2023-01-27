---
layout: post
title:  "A Quick PyTorch Implementation of 'Wide and Deep'"
date:   2023-01-27
categories: machine-learning onnx
---

In a previous post I mentioned 'Wide and Deep'. In this post, I'll present a quick "mostly correct" implemetation of the model using PyTorch with the ability to export to `onnx` for a portable deployment. Our implmentation is roughly speaking a port of [Structured Data Learning with Wide, Deep and Cross networks](https://keras.io/examples/structured_data/wide_deep_cross_networks/) presented on the Keras code examples. 

Let's begin:

```py
from typing import Optional, List
import torch
from torch import nn
from enum import Enum
from dataclasses import dataclass


class InputTypes(str, Enum):
	NUMERIC = "NUMERIC"
	SEQUENCE = "SEQUENCE"
	EMBEDDING = "EMBEDDING"
```

We first define the possible data types. Now in `pytorch`, it expects that models exported to ONNX already come preprocessed using numeric data. In a future state I'll think about how to incorporate those transformations into some kind of pipeline, though that is a separate discussion around Machine Learning Engineering. Here we have three data types:

- Numeric: which we accept and read "as-is"
- Sequence: which is typically used for text vectorizations in Deep Learning models
- Embedding: which is some kind of categorical transformation like one-hot encoding

The gist of the approach is that we encode our inputs into two subnetworks, a wide network and a deep network. Before we do that we need to convert our inputs into a useful representation. I'll call this the `InputEncoding` module

```py
class InputEncoding(nn.Module):
	def __init__(
		self,
		args_types: List[DataType],
		sequence_embedding_dim_size: int = 16,
		embedding_size: int = 16,
	):
		super().__init__()
		self.args_types = args_types
		self.sequence_embedding_dim_size = sequence_embedding_dim_size
		self.embedding_size = embedding_size
		
		# calculate the mebdding output size
		self.input_encoding_out_size = 0
		self.input_embedding_if_required: List[Optional[str]] = []
		for indx, arg_type in enumerate(self.args_types):
			if arg_type.types == InputTypes.NUMERIC:
				self.inpt_encoding_out_size += 1
				self.input_embedding_if_required.append(None)
			elif arg_type.types in [InputTypes.SEQUENCE, InputTypes.EMBEDDING]:
				# normally we would use EmbeddingBag for Sequence and Linear for Embedding
				# leave this as an exercise for the reader
				# pretend they are the same size as well...
				self.inpt_encoding_out_size += self.sequence_embedding_size
				layer_name = f"linear_{indx}"
				self.input_embedding_if_required.append(layer_name)
				# this is a hack to get onnx to work due to limitations of jit
				setattr(
					self,
					layer_name,
					nn.Linear(arg_type.input_size, self.sequence_embedding_dim_size)
				)
			else:
				raise Exception(f"Invalid input type provided: {arg_type}")
	
	def forward(self, *args):
		inputs = []
		for arg, layer in zip(args, self.input_embedding_if_required):
			if layer is None:
				inputs.append(args)
			else:
				inputs.append(getattr(self, layer)(arg))
		return torch.cat(inputs, 1)
```

Next we need to implement the deep portion of the network. This is a fairly standard setup consisting of an intermediary layer with an activation with batch normalization. 

```py
class DeepEncoder(nn.Module):
	def __init__(
		self, input_size, hidden_units: List[int] = [32, 32], dropout_p: float = 0.1
	):
		super().__init__()
		self.input_size = input_size
		self.hidden_units = hidden_units
		self.dropout_p = dropout_p
		
		in_size = input_sizeself.layer_names = []
		for indx, unit_size in enumerate(hidden_units):
			linear_name = f"linear_{indx}"
			layernorm_name = f"layernorm_{indx}"
			relu_name = f"relu_{indx}"
			dropout_name = f"dropout_{indx}"
			
			self.layer_names.extend(
				[linear_name, layernorm_name, relu_name, dropout_name]
			)
			setattr(self, linear_name, nn.Linear(in_size, unit_size))
			setattr(self, layernorm_name, nn.LayerNorm(unit_size))
			setattr(self, relu_name, nn.ReLU())
			setattr(self, dropout_name, nn.Dropout(dropout_p))
			in_size = unit_size
			
	def forward(self, x):
		for layer_name in self.layer_names:
			x = getattr(self, layer_name)(x)
		return x
```

With both pieces in place, finally we can put everything together

```py
class WideAndDeep(nn.Module):
	def __init__(
		self,
		input_encoding: InputEncoding,
		output_size: int = 2
		hidden_units: List[int] = [32, 32]
		dropout_p: float = 0.1,
	):
		super().__init__()
		self.wide_encoding = input_encoding
		self.deep_encoding = input_encoding
		
		self.wide_norm = nn.LayerNorm(self.wide_encoding.input_encoding_out_size)
		self.deep_norm = nn.LayerNorm(self.deep_encoding.inpt_encoding_out_size)
		self.deep_layers = DeepEncoder(self.deep_encoding.inpt_encoding_out_size, hidden_units, dropout_p)
		self.output_layer = nn.Linear(
			self.wide_encoding.input_encoding_out_size + hidden_units[-1], output_size
		)
		self.output_activation = nn.Softmax(dim=1)
	
	def forward(self, *args):
		wide = self.wide_encoding(*args)
		swide = self.wide_norm(wide)
		
		deep = self.deep_encoding(*args)
		deep = self.deep_norm(deep)
		deep = self.deep_layers(deep)
		
		merged_layer = torch.cat([wide, deep], 1)
		output = self.output_layer(merged_layer)
		predictions = self.output_activation(output)
		return predictions
```

As these only use the core pytorch layers, and take in pure numeric input, we can trivially export this out to ONNX and deploy it in other settings and frameworks. Notice that the total amount of code is relatively "small" - probably small enough to justify recoding it from scratch every time. 