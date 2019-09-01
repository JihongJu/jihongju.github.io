---
layout: post
title: Conditional Random Field, the factor graph representation
author: Jihong Ju

---

In problems where we have a set of observed variables **X** and a set of target variables **Y**: 

Observed variables **X**={xi|i=1,2,...,M}

Target variables **Y**={yj|j=1,2,...,N}

The goal is to model the conditional distribution P(**Y**|**X**). There are many application of such a problem in practice, like [image segmentation](https://en.wikipedia.org/wiki/Image_segmentation), [Part-Of-Speech tagging](https://en.wikipedia.org/wiki/Part-of-speech_tagging), etc.



## Condition Random Field as Markov Network

Conditional Random Fields (CRFs) model the conditional distribution P(**Y**|**X**) and can be represented by a special form of Markov Network (Figure 1).

![crf-as-markov-network](https://www.dropbox.com/s/m1fa744wr6uz8i7/crf-as-markov-network.png?dl=1)

Figure 1. An example Markov Network representation of Conditional Random Field. [1] The circles and the double circles denote the the target variables Y and the observed variables X respectively. The links refer to the dependencies among the random variables. 

As we can see from Figure 1, there is no edge among observed variables. This doesn't mean the observed variables **X** are independent of each other. The dependencies are just not interesting in representing the conditional distribution P(**Y**|**X**).



## Condition Random Field as Factor Graph

The Markov Network representation is ambiguous. Figure 2 gives a simple example about the ambiguity of the Markov Network.

![factor-graph-vs-markov-network](https://www.dropbox.com/s/g5h9szgsx0u0iry/factor-graph-vs-markov-network.png?dl=1)

Figure 2. Both of the factor graphs (middle and right) correspond to the same Markov Network (left). [2]

To disambiguate the Markov Network representation of Conditional Random Field is the factor graph representation. Figure 3 demonstrates the same Conditional Random Field represented as a factor graph.

![crf-as-factor-graph](https://www.dropbox.com/s/uux4f4ye594lcqz/crf-as-factor-graph.png?dl=1)

Figure 3. An example Factor graph representation of Conditional Random Field. [1] The circles and the double circles denote the the target variables Y and the observed variables X respectively. The solid squares denote the factors. 



References:

\[1\]: Hugo Larochelle. ["Neural networks [3.9] : Conditional random fields - factor graph"]([Neural networks [3.9\] : Conditional random fields - factor graph - YouTube](https://www.youtube.com/watch?v=Q5GTCHVsHXY))

\[2\]: Charles Sutton and Andrew McCallum. "An introduction to conditional random fields." *Foundations and Trends® in Machine Learning* 4.4 (2012): 267-373.

