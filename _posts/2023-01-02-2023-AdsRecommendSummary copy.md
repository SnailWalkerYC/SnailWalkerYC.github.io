---
layout: post
title: On the Factory Floor ML Engineering for industrial-scale Ads Recommendation Models
date: 2022-01-02
categories: [technology]
tags: [interview]
comments: false
---

- Online Optimization
Single pass of training data, the metric calculated before training. 

- ML efficiency
Bandwidth(number of models can train concurrently); Latency(end-to-end evaluation time for a new model), throughput(models that can be trained per unit time)
1. bottlenecks: wider is better, but reduce the embedding dimension is enough. Replace HW to HUV matrix factorization.
2. AutoML. Weight-sharing network, RL controller, constraints.
3. Data sampling. Re-balancing/Loss-based sampling.

- Loss Engineering
Distillatiion/Shampoo/DCN.
1. Rankloss.Pariwise logic; combining rankloss with logitic loss.
2. Distillation. 
3. Curriculums of losses.
2nd order optimization: Shampoo.

- Irreproducibility
ReLU -> SmeLU

