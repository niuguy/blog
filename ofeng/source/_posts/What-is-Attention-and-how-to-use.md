---
title: What is Attention and how to use
date: 2018-09-20 10:06:45
tags: 'Algorithm'
categories: 'Data&AI'

---

### Introduction

Attention or Bahdanau Attention is getting more and more interest in Neural Machine Translation(NMT)  and other sequence prediction research, in this article I will briefly introduce what is Attention mechanism, why important it is and how do we use it(in Tensorflow)

### Why Attention

Attention is a mechanism derived from the seq-seq model which started the era of NMT,  in this [paper](https://arxiv.org/pdf/1409.3215.pdf) Sutskever proposed a novel RNN network called encode-decode network to tackle seq-seq prediction problems such as translation.

![Example of an Encoder-Decode Network, from "Sequence to Sequence Learning with Neural Network" 2014](https://3qeqpr26caki16dnhd19sv6by6v-wpengine.netdna-ssl.com/wp-content/uploads/2017/10/Example-of-an-Encoder-Decode-Network.png)

 

The model performed well in many translation tasks, but it turned out to be limited to very long sequences. The reason lies in this network needs to be able to capture all information about the source sentence, that is easy to long sentences, especially those that are longer than sentences in the training corpus.

Attention provides a solution to this problem, and its core idea is to focus on a relevant part of the source sequence on each step of the decoder.

Maybe unexpectedly, Attention also benefits seq2seq model in other ways, the first one is that it helps with vanishing gradient problem by providing a shortcut to faraway states; the second one is that it gives some interpretability which I will illustrate in the following sector.

### What is Attention

Attention is merely a context vector that provides a richer encoding of the source sequence. **The vector is computed at every decoder time step.**

![img](https://github.com/tensorflow/nmt/raw/master/nmt/g3doc/img/attention_mechanism.jpg)

As illustrated in the figure above, the attention computation can be summarized into the following three steps:

1. Compute attention weights based on the current target hidden state and all source state(Figure 1)

2. The weighted average of the source states based on the attention weights are then computed, and the result is a context vector(Figure 2)

3. Context vector combined with the current target hidden state yields the attention vector(Figure 3)

   The attention vector is then fed to the next decoding step. 

   ![img](https://github.com/tensorflow/nmt/raw/master/nmt/g3doc/img/attention_equation_0.jpg)

   the score in Figure 1 is computed as follows:

   ![img](https://github.com/tensorflow/nmt/raw/master/nmt/g3doc/img/attention_equation_1.jpg)

Regarding the score,  the methods by which it is calculated lead to different performance. 

### Coding Attention with Tensorflow

Suppose we have already got an encoder-decoder implementation, what we need to do is trivial because Tensorflow has realized in advance the most of the attention building process(Figure 1-3).

```Python
# Transfer encoder_outputs to attention_states 
attention_states = tf.transpose(encoder_outputs, [1, 0, 2])

# Apply existing attention mechanism 
attention_mechanism = tf.contrib.seq2seq.BahdanauAttention(
    num_units, attention_states,
    memory_sequence_length=source_sequence_length)

# Feed to the decoder
decoder_cell = tf.contrib.seq2seq.AttentionWrapper(
    decoder_cell, attention_mechanism,
    attention_layer_size=num_units)
```

The rest codes are mostly the same as standard encoder-decoder.