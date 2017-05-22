---
layout: post
title:  "Caffe weights initializations"
categories: ML
---

I found model initialization is a bit hidden from [Caffe's documentation](http://caffe.berkeleyvision.org/). So I'd like to summarize what optimiztion methods are provided by [Caffe](https://github.com/BVLC/caffe).

I will start with how to configure model initialization while defining nets both with PyCaffe and with `deploy.prototxt` file. Then I will shortly go through each of the initialization options in [Caffe](https://github.com/BVLC/caffe) and when it is proper to use.

## Configure initialization methods

Initialization methods can be configured while defining a neural network in Caffe.

### Define Net with _prototxt_ file

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

### Define Net with Pycaffe

Defining complex networks with _prototxt_ file is problematic so writing _prototxt_ file programatically with a script could be beneficial. Fortunately, [Pycaffe](https://github.com/BVLC/caffe/blob/master/python/caffe/pycaffe.py) provides an interface to do so. Again, if you are not very familiar with Pycaffe, you may want to check out [this blog](http://www.tarekallamjr.com/blog/posts/Programatically-define-net-with-PyCaffe/) by Tarek Allam Jr.

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

The bottom-line answer is that the __xavier__ filler works for most of the cases, though there are better choices in some circumstances, for instance, __msra__  filler by [He et. al.](https://arxiv.org/pdf/1502.01852v1.pdf).

## Available initialization methods

All the weight/bias fillers available in Caffe with descriptive docstring can be found at in the [header file](https://github.com/BVLC/caffe/blob/master/include/caffe/filler.hpp)

This section aims at briefly introducing the idea accompanied with the different initialization methods.

Ongoing......

### Constant Filler

key: "constant"

### Gaussian Filler

key: "gaussian"

### Positive Unit ball Filler

key: "positive_unitball"

### Xavier Filler

key: "xavier"

Refering to [andy's blog](http://andyljones.tumblr.com/post/110998971763/an-explanation-of-xavier-initialization)

### MSRA Filler

key: "msra"

### Bilinear Filler

key: "bilinear"
