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
  + [Uniform Filler](#uniform-filler)
  + [Gaussian Filler](#gaussian-filler)
  + [Positive Unitball Filler](#positive-unitball-filler)
  + [Xavier Filler](#xavier-filler)
  + [MSRA Filler](#msra-filler)
  + [Bilinear Filler](#bilinear-filler)

# Specify initializers

In Caffe, initializers for layers with trainable parameters can be specified while defining a neural network.

## On defining nets with Caffe prototxt

Caffe provides an interface to define the architecture of a neural network with a simple `.prototxt` file. If you are not familiar with how it works, please refer to the [Caffe documentation](http://caffe.berkeleyvision.org/tutorial/net_layer_blob.html).


In the following example, I take the definition of the first convolutional layer of  [LeNet](http://www.dengfanxin.cn/wp-content/uploads/2016/03/1998Lecun.pdf) from [Caffe examples](https://github.com/BVLC/caffe/blob/master/examples/mnist/lenet.prototxt).

Here, the weights of this layer is initialized with [Xavier initialization](http://proceedings.mlr.press/v9/glorot10a/glorot10a.pdf) and the bias is initialized with a constant (default value is 0).

```protobuf
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

Caffe uses `weight_filler` to indicate the initializer being used for the weights and `bias_filler` for the bias.

## On defining nets with Pycaffe

Defining complex networks with `.prototxt` files could be problematic.  Therefore writing the `.prototxt` files programatically with a script could be beneficial in practice. Fortunately, [Pycaffe](https://github.com/BVLC/caffe/blob/master/python/caffe/pycaffe.py) provides an interface to do so. Again, if you are not very familiar with Pycaffe, [This blog](http://www.tarekallamjr.com/blog/posts/Programatically-define-net-with-PyCaffe/) by Tarek Allam Jr may be helpful.

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

Well, as a rule of thumbs, the "xavier" filler [[Glorot & Bengio](http://jmlr.org/proceedings/papers/v9/glorot10a/glorot10a.pdf), [He et. al.](https://arxiv.org/pdf/1502.01852v1.pdf)] works just fine most of the times.

# Caffe Initializers

Source codes, together with brief docstring, of all the weight/bias fillers can be found at in the [filler.hpp](https://github.com/BVLC/caffe/blob/master/include/caffe/filler.hpp)

This section aims at briefly introducing the idea accompanied with the different initialization methods.

Ongoing......

## Constant Filler

 - Type: "constant"
 - Simply set $$ x = const. $$.

## Uniform Filler
 - Type: "uniform"
 - Sample small values from a uniform distribution.
 - $$x\sim U(a, b)$$ where $$a$$, $$b$$ determines the sampling range.

## Gaussian Filler

 - Type: "gaussian"
 - Sample small values from a gaussian distribution. $$x \sim N(\mu, \sigma)$$ where $$\mu$$, $$sigma$$ are fixed values for all the layers.

## Positive Unitball Filler

 - Type: "positive_unitball"

## Xavier Filler

 - Type: "xavier"
 - Sample values from a uniform distribution
 - $$x \sim U(-a, +a)$$, where $$a=\sqrt{\frac{3}{n}}$$
 - Here, $$n$$ can be `fan_in`, `fan_out` or their average
 - `fan_in` refers to the number of inputs of a neuron and `fan_out` is the number of outputs (and same below)
 - Xavier initialization makes sure the initial values of the weights for each layer are:
    + neither __too small__ so that the signal passed through __shrinks__
    + nor __too large__ so that the signal __explodes__
 - [He et. al](https://arxiv.org/pdf/1502.01852v1.pdf) found $$a=\sqrt{\frac{6}{n}}$$ works better for ReLU nonlinearity
 - Similiar to:
    + `keras.initializers.glorot_uniform`
    + `tensorflow.contrib.layers.xavier_initializer`


## MSRA Filler

 - Type: "msra"
 - $$x \sim N(0, \sigma^2)$$ where $$\sigma \propto \frac{1}{n}$$

## Bilinear Filler

key: "bilinear"
