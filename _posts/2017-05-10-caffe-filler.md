---
layout: post
title:  "Caffe Initializers"
author: "Jihong"
---

I found __Caffe Initializers__ are hidden from the [Caffe documentation](http://caffe.berkeleyvision.org/). So in this post I would like to summarize the available initializers of  [Caffe](https://github.com/BVLC/caffe).


# Table of Contents

- [Specify initializers](#specify-initializers)
  + [On defining nets with Caffe prototxt](#on-defining-nets-with-caffe-prototxt)
  + [On defining nets with Pycaffe](#on-defining-nets-with-pycaffe)
- [Caffe Initializers](#caffe-initializers)
  + [Constant Filler](#constant-filler)
  + [Gaussian Filler](#gaussian-filler)
  + [Positive Unitball Filler](#positive-unitball-filler)
  + [Xavier Filler](#xavier-filler)
  + [MSRA Filler](#msra-filler)
  + [Bilinear Filler](#bilinear-filler)

# Specify initializers

In Caffe, initializers for layers with trainable parameters can be specified while defining a neural network.

## On defining nets with Caffe prototxt

Caffe provides an interface to define the architecture of a neural network with a simple _prototxt_ file. If you are not familiar with how it works, please refer to the [Caffe documentation](http://caffe.berkeleyvision.org/tutorial/net_layer_blob.html).


In the following example, I take the definition of the first convolutional layer of  [LeNet](http://www.dengfanxin.cn/wp-content/uploads/2016/03/1998Lecun.pdf) from [Caffe examples](https://github.com/BVLC/caffe/blob/master/examples/mnist/lenet.prototxt).

Here, the weights of this layer is initialized with [Xavier initialization](http://proceedings.mlr.press/v9/glorot10a/glorot10a.pdf) and the bias is initialized with a constant (default value is 0).

```
layer {
  name: "conv1"
  type: "Convolution"
  bottom: "data"
  top: "conv1"
  convolution_param {
    num_output: 20
    kernel_size: 5
    stride: 1
    weight_filler {
      type: "xavier"
    }
    bias_filler {
      type: "constant"
    }
  }
}
```

Caffe uses `weight_filler` to indicate the initialization method for the weights and `bias_filler` to indicate the initial filling method for the bias.

## On defining nets with Pycaffe

Defining complex networks with _prototxt_ file could be problematic.  Therefore writing _prototxt_ file programatically with a script could be beneficial in practice. Fortunately, [Pycaffe](https://github.com/BVLC/caffe/blob/master/python/caffe/pycaffe.py) provides an interface to do so. Again, if you are not very familiar with Pycaffe, you may want to check out [this blog](http://www.tarekallamjr.com/blog/posts/Programatically-define-net-with-PyCaffe/) by Tarek Allam Jr.

The following python snippet defines a same convolutional layer as the layer aforementioned.

```python
import sys
import caffe
from caffe import layers

net = caffe.NetSpec()
net.data = ...
net.conv1 = layers.Convolution(net.data, num_output=20, kernel_size=5,
                               stride=1, weight_filler='xavier',
                               bias_filler='constant')
...
```

Now you may want to know what are these fillers and how do I know which one is appropriate?

 Long story in short, __xavier__ ([Glorot & Bengio](http://jmlr.org/proceedings/papers/v9/glorot10a/glorot10a.pdf)) and __msra__ ([He et. al.](https://arxiv.org/pdf/1502.01852v1.pdf)) fillers work just fine most of the times.

# Caffe Initializers

All the weight/bias fillers available in Caffe with descriptive docstring can be found at in the [header file](https://github.com/BVLC/caffe/blob/master/include/caffe/filler.hpp)

This section aims at briefly introducing the idea accompanied with the different initialization methods.

Ongoing......

## Constant Filler

key: "constant"

## Gaussian Filler

key: "gaussian"

## Positive Unitball Filler

key: "positive_unitball"

## Xavier Filler

key: "xavier"

## MSRA Filler

key: "msra"

## Bilinear Filler

key: "bilinear"
