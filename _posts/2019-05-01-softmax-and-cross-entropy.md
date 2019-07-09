---
layout: post
title:  "Softmax and Cross-entropy"
excerpt: "The marriage of Softmax and CrossEntropy. This is a loss calculating function post the yhat(predicted value) that measures the difference between Labels and predicted value(yhat). "
mathjax: true
date:   2019-05-03 15:07:19
categories: [article]
tags: Artificial-Intelligence Deep-learning
comments: true
---

### Introduction
This is a loss calculating function post the yhat(predicted value) that measures the difference between Labels and predicted value(yhat). Cross entropy is a loss function that is defined as $$\Large E = -y .log ({\hat{Y}})$$ where $$\Large E$$, is defined as error, $$\Large y$$ is the label and $$\Large \hat{Y}$$ is defined as $$\Large softmax_j (logits)$$ and logits are weighted sum. One of the reason to choose cross entropy alongside softmax is that because softmax has an exponential element inside it the, an element log provides for convex cost function. This is similar to logistic regression which uses sigmoid. Mathematically expressed as below.  

$$\Large \hat{Y}=softmax_j (logits)$$

$$\Large E = -y .log ({\hat{Y}})$$  

where $$ \Large y $$ is the label and Yhat $$\Large \hat{Y}$$ the predicted value.

{% highlight python %}
def loss(pred,labels):
  """
  One at a time.
  args:
      pred-(seq=1),input_size
      labels-(seq=1),input_size
  """
  return np.multiply(labels, -np.log(pred)).sum(1)

{% endhighlight %}

> * Loss function: Example-1
{%
    include image.html
    src="/img/cel.jpg"
    caption="figure-1:Cost is low because, the prediction is closer to the truth."
    hight="110%"
    width="110%"
%}

> * Loss function: Example-2
{%
    include image.html
    src="/img/cel2.jpg"
    caption="figure-2:Cost is high because, the prediction is far away from the truth."
    hight="110%"
    width="110%"
%}

### CrossEntropy Loss Derivative
One of the tricks I have learnt to get back propagation right is to write the equations backwards. This becomes especially useful when the model is more complex in later articles. A trick that I use a lot.  

$$\Large \hat{Y}=softmax_j (logits)$$

$$\Large E = -y .log ({\hat{Y}})$$  

The above equation is writen like so below while calculating the gradients.

$$\Large E = -y .log ({\hat{Y}})$$

$$\Large \hat{Y}=softmax_j (logits)$$

> * Gradient: Example-3
{%
    include image.html
    src="/img/cel3.jpg"
    caption="figure-3:The red arrow follows the gradient."
    hight="110%"
    width="110%"
%}

> * Deriving the gradients now.  

$$\Large \frac{\partial {E}}{\partial {logits}} = \frac{\partial {E}}{\partial {\hat{Y}}}.\frac{\partial {\hat{Y}}}{\partial {logits}}$$  

# <a name="eq-2"></a>  

$$\Large \frac{\partial {E}}{\partial {logits}} = -\frac{y}{\hat{y}}.\frac{\partial {\hat{Y}}}{\partial {logits}} \:\:\:\: eq(2)$$  

> * For calculating $$\Large \frac{\partial {\hat{Y}}}{\partial {logits}}$$ where $$\Large \hat{y}$$ is the softmax and its derivative is given by [eq-1][eq-1]. Combining [eq-1][eq-1] and [eq-2][eq-2].  

$$\Large \frac{\partial {E}}{\partial {logits}} = -\frac{y}{\hat{y}}.\begin{Bmatrix}
softmax_i(1-softmax_j) & i= j \\
{-softmax_j}.{softmax_i} & i\neq j
\end{Bmatrix}$$  

$$\Large \frac{\partial {E}}{\partial {logits}} = -\frac{y}{\hat{y}}.\hat{y_t}.(1-\hat{y_t}) + \sum_{i\neq j}(\frac{-y_t}{\hat{y_t}})(-\hat{y_t}\hat{y_t})$$  

$$\Large \frac{\partial {E}}{\partial {logits}} = -y.(1-\hat{y_t}) + \sum_{i\neq j}y_t.\hat{y_t}$$  

$$\Large \frac{\partial {E}}{\partial {logits}} = -y+y.\hat{y_t} + \sum_{i\neq j}y_t.\hat{y_t}$$  

$$\Large \frac{\partial {E}}{\partial {logits}} = y.\hat{y_t} + \sum_{i\neq j}y_t.\hat{y_t} -y$$  

$$\Large \frac{\partial {E}}{\partial {logits}} = \hat{y_t}.(y + \sum_{i\neq j}y_t) -y $$  

Since Y is a one hot vector, the term "$$\Large (y + \sum_{i\neq j}y_t)$$" sums up to one.  

$$\Large \frac{\partial {E}}{\partial {logits}} = (\hat{y_t} -y) \:\:\:\: eq(3) $$

[eq-1]: softmax-and-its-gradient#eq-1
[eq-2]: softmax-and-cross-entropy#eq-2
