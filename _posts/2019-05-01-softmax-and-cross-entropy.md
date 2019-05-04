---
layout: post
title:  "Softmax and Cross-entropy"
excerpt: "Demo post displaying the various ways of highlighting code in Markdown."
mathjax: true
date:   2019-05-03 15:07:19
categories: [article]
comments: true
---

Cross entropy is a loss function that is defined as $$\Large E = -y .log ({\hat{Y}})$$ where $$\Large E$$, is difined as error, $$\Large y$$ is the label and $$\Large \hat{Y}$$ is defined as $$\Large softmax_j (logits)$$ and logits are weighted sum. One of the reason to choose cross entropy alongside softmx is that because softmax has an exponential element inside it the, an element log provides for convex cost function. This is similar to logistic regression which uses sigmoid. Mathematically expressed,

$$\Large E = -y .log ({\hat{Y}})$$  
$$\Large \hat{Y}=softmax_j (logits)$$

### CrossEntropy Loss Derivative

$$\Large \frac{\partial {E}}{\partial {logits}} = \frac{\partial {E}}{\partial {\hat{Y}}}.\frac{\partial {\hat{Y}}}{\partial {logits}}$$  
# <a name="eq-2"></a>
$$\Large \frac{\partial {E}}{\partial {logits}} = -\frac{y}{\hat{y}}.\frac{\partial {\hat{Y}}}{\partial {logits}} \:\:\:\: eq(2)$$  
For calculating $$\Large \frac{\partial {\hat{Y}}}{\partial {logits}}$$ we need to reference [eq-1][eq-1]. Combining [eq-1][eq-1] and [eq-2][eq-2] which is softmax.
$$\Large \frac{\partial {E}}{\partial {logits}} = -\frac{y}{\hat{y}}.\begin{Bmatrix}
softmax_i(1-softmax_j) & i= j \\
{-softmax_j}.{softmax_i} & i\neq j
\end{Bmatrix}$$  
$$\Large \frac{\partial {E}}{\partial {logits}} = -\frac{y}{\hat{y}}.\begin{Bmatrix}
softmax_i(1-softmax_j) & i= j \\
{-softmax_j}.{softmax_i} & i\neq j
\end{Bmatrix}$$




[eq-1]: softmax-and-its-gradient/#eq-1
[eq-2]: softmax-and-cross-entropy#eq-2
