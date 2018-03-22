---
layout: post
title: "[Paper Review] Bidirectional LSTM-CRF Models for Sequence Tagging"
date: 2018-03-22
tags: [Deep Learning, NLP]
excerpt: "Bidirectional LSTM-CRF Models for Sequence Tagging"
comments: true
---

### 1. Overview

- Long Short-term Memory (LSTM) based models for sequence tagging.
  - Part of Speech tagging
  - Named Entity Recognition
- Compare the performance of aforementioned models on NLP tagging data sets.
  - Convolutional CRF Network, etc ...
- This paper proposed `A Bidirectional LSTM-CRF Model`.

### 2. Tagging

Basically, there's named entity recognition system in which each word is tagged with other (O) or one of four entity types: Person (PER), Location (LOC), Organization (ORG), and Miscellaneous (MISC).

### 3. Recurrent Neural Network

Traditionally, Recurrent Neural Network(RNN) introduced the connection between the previous hidden state and current hidden state. If input sequence is getting longer and longer, this is the reason of **vanishing gradient**.

As a result, LSTM came out. Basically, it is the same as RNNs except that the hidden layer updates replaces by purpose-built memory cells. As a result, they may be better at finding and exploiting long range dependencies in data.

> But, unfortunately, LSTM is not perfect solution as always we were.

### 4. Bi-Directional LSTM

Generally, LSTM only does forward pass. But, Bi-LSTM does **forward pass and backward pass**, exactly both. It means, we calculate forward state and backward state.

### 5. Conditional Random Field

There are two different ways to make useof neighbor tag information in predicting current tags. 
The first is to predict a distribution oftags for each time step and then use beam-like decoding to find optimal tag sequences. The second one is to focus on sentencelevel instead of individual positions.
The inputs and outputs are directly connected. That is the difference between LSTM, CRF.

### 6. Training Procedure - LSTM

- All models used in this paper share a generic Stochastic Gradient Descent.
- Batch size set 100
  - It means, each sentence's total length is no greater than 100.
- As a result, we get the output score for all tags at all positions.

### 7. Training Procedure - CRF 

- Run CRF layer forward and backward pass to compute gradients for network output and state transition edges.
- Back-propagate the errors from the output to input
  - includes the both forward and backward states of LSTM
- Update the network parameters
  - Includes state transition matrix, bi-LSTM parameters

### 8. Dataset

- Penn TreeBankPOS Tagging
- CoNLL 2000 Chunking
- CoNLL 2003 named entity tagging

### 9. Features

- Word embedding

  - Senna embedding

    - > where is the GloVe, Word2Vec.

- Spelling features

  - Where start with a capital letter
  - letters only.

- Context features

  - Uni-gram, bi-gram, tri-gram ...

### 10. Conclusion

Conv-CRF model relies on Senna embedding to get good tagging accuracy. But, Bi-LSTM doesn't reliy on embedding also it got a good tagging accuracy.

### 11. Reference

[Bidirectional LSTM-CRF Models for Sequence Tagging] https://arxiv.org/abs/1508.01991