---
layout: post
title:  "Softmax and Cross-entropy"
excerpt: "Demo post displaying the various ways of highlighting code in Markdown."
mathjax: true
date:   2019-05-03 15:07:19
categories: [article]
comments: true
---

Cross entropy is a loss function that is defined as $$\Large E = -y_t .log ({\hat{Y_t}})$$ where $$\Large E$$, is difined as error, $$\Large y$$ is the label and $$\Large \hat{Y_t}$$ is defined as $$\Large softmax_j (logits)$$ and logits are weighted sum. One of the reason to choose cross entropy alongside softmx is that because softmax has an exponential element inside it the, an element log provides for convex cost function. This is similar to logistic regression which uses sigmoid. Mathematically expressed,

$$\Large E = -y_t .log ({\hat{Y_t}})$$  
$$\Large \hat{Y_t}=softmax_j (logits)$$

### CrossEntropy Loss Derivative

$$\Large \frac{\partial {E}}{\partial {logits}} = \frac{\partial {E}}{\partial {\hat{Y_t}}}.\frac{\partial {\hat{Y_t}}}{\partial {logits}}$$  
$$\Large \frac{\partial {E}}{\partial {logits}} = -\frac{y_t}{\hat{y_t}}.\frac{\partial {\hat{Y_t}}}{\partial {logits}}$$


![figure-1]({{ site.url }}/img/introduction/x86reg.jpg){: hight="110%" width="110%" name="figure-1"}<center>figure-1</center>
