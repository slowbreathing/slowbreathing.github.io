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






##### Feed-Forward Network layer(Step-1)
> * We have already done this derivation for "[softmax]" and "[cross_entropy_loss]".
> * The final derived form in [eq-3]. Which in turn depends on [eq-1][eq-1] and [eq-2][eq-2]. <strong>We are calling it "pred" instead of "logits" in figure-5</strong>.
{%
    include image.html
    src="/img/rnn/lstmback5.jpg"
    caption="figure-8: <strong>The proof, intuition and program implementation</strong> of the derivation can be found at<strong><a href='/articles/2019-05/softmax-and-its-gradient'>softmax</a></strong> and <strong><a href='/articles/2019-05/softmax-and-cross-entropy'>cross_entropy_loss</a></strong>."
    hight="110%"
    width="110%"
%}

> * <strong>"DBy"</strong> is straight forward, once the gradients for pred is clear in the previous step.
> * This is just gradient calculation, the weights aren't  adjusted just yet. They will be adjusted together in the end as a matter of convenience by an optimizer such as GradientDescentOptimizer.
> * The complete code listing can be found at [code-1].
{%
    include image.html
    src="/img/rnn/lstmback6.jpg"
    caption="figure-9: <strong>DBy</strong>"
    hight="110%"
    width="110%"
%}

> * <strong>"DWy"</strong> is straight forward too, once the gradients for pred is clear in the previous step(figure-5).
> * The complete code listing can be found at [code-2].
{%
    include image.html
    src="/img/rnn/lstmback7.jpg"
    caption="figure-10: <strong>DWy</strong>"
    hight="110%"
    width="110%"
%}

> * <strong>"DWy"</strong> and <strong>"Dby"</strong>
{%
    include image.html
    src="/img/rnn/lstmback8.jpg"
    caption="figure-11: <strong>DWy</strong> and <strong>Dby</strong>. Only the gradients are calculated, the weights are adjusted once in the end."
    hight="110%"
    width="110%"
%}

##### Backward propagation through LSTMCell(Step-2)
> * Probably the most confusing step backward propagation because there is an element of time.
> * There were 2 recursive elements h-state, c-state, similarly there 2 recursive elements while deriving the gradient backwards. We call it dh_next and dc_next, as you will see later both and many other gradients are derived from <strong>dHt</strong>.
{%
    include image.html
    src="/img/rnn/lstmback9.jpg"
    caption="figure-12: <strong>dHt</strong>"  
    hight="110%"
    width="110%"
%}

> * Complete code <strong>DHt</strong> can be found at [code-3],[code-4] and [code-5].
{%
    include image.html
    src="/img/rnn/lstmback10.jpg"
    caption="figure-10: <strong>DHt</strong>"
    hight="110%"
    width="110%"
%}

> * <strong>dHt</strong> schematically.<strong>Shape is the same as h, for this example it is (5X1)</strong>
{%
    include image.html
    src="/img/rnn/lstmback11.jpg"
    caption="figure-11: <strong>DHt</strong> Shape is the same as h, for this example it is (5X1)."
    hight="110%"
    width="110%"
%}

> * <strong>Shape of <strong>DOt</strong> is the same as h, for this example it is (5X1)</strong>
> * The complete listing is at [code-6].
{%
    include image.html
    src="/img/rnn/lstmback12.jpg"
    caption="figure-12: <strong>DOt</strong>."
    hight="110%"
    width="110%"
%}

> * <strong>DOt</strong> schematically.
{%
    include image.html
    src="/img/rnn/lstmback13.jpg"
    caption="figure-13: <strong>DOt</strong> schematically."
    hight="110%"
    width="110%"
%}

> * <strong>DOt</strong>, <strong>DWo</strong> and <strong>DBo</strong>
> * Complete listing can be found at [code-7]
{%
    include image.html
    src="/img/rnn/lstmback14.jpg"
    caption="figure-14: <strong>DOt</strong>, <strong>DWo</strong> and <strong>DBo</strong> and there shapes"
    hight="110%"
    width="110%"
%}

> * <strong>Shape of <strong>DCt</strong> is the same as h, for this example it is (5X1)</strong>
> * The complete listing is at [code-8].
{%
    include image.html
    src="/img/rnn/lstmback15.jpg"
    caption="figure-15: <strong>DCt</strong>."
    hight="110%"
    width="110%"
%}

> * <strong>DCt</strong> schematically.
{%
    include image.html
    src="/img/rnn/lstmback16.jpg"
    caption="figure-16: <strong>DCt</strong> schematically."
    hight="110%"
    width="110%"
%}

> * <strong>Shape of <strong>DCprojt</strong> is the same as h, for this example it is (5X1)</strong>
> * The complete listing is at [code-9].
{%
    include image.html
    src="/img/rnn/lstmback17.jpg"
    caption="figure-17: <strong>DCprojt</strong>."
    hight="110%"
    width="110%"
%}

> * <strong>DCprojt</strong> schematically.
{%
    include image.html
    src="/img/rnn/lstmback18.jpg"
    caption="figure-18: <strong>DCprojt</strong> schematically."
    hight="110%"
    width="110%"
%}

> * <strong>DCprojt</strong>
> * Complete listing can be found at [code-10]
{%
    include image.html
    src="/img/rnn/lstmback19.jpg"
    caption="figure-19: <strong>DCprojt</strong>"
    hight="110%"
    width="110%"
%}

> * <strong>Shape of <strong>DFt</strong> is the same as h, for this example it is (5X1)</strong>
> * The complete listing is at [code-11].
{%
    include image.html
    src="/img/rnn/lstmback20.jpg"
    caption="figure-20: <strong>DFt</strong>."
    hight="110%"
    width="110%"
%}

> * <strong>DFt</strong> schematically.
{%
    include image.html
    src="/img/rnn/lstmback21.jpg"
    caption="figure-21: <strong>DFt</strong> schematically."
    hight="110%"
    width="110%"
%}

> * <strong>DFt</strong>
> * Complete listing can be found at [code-12]
{%
    include image.html
    src="/img/rnn/lstmback22.jpg"
    caption="figure-22: <strong>DFt</strong>"
    hight="110%"
    width="110%"
%}

> * <strong>Shape of <strong>DIt</strong> is the same as h, for this example it is (5X1)</strong>
> * The complete listing is at [code-13].
{%
    include image.html
    src="/img/rnn/lstmback23.jpg"
    caption="figure-23: <strong>DIt</strong>."
    hight="110%"
    width="110%"
%}

> * <strong>DIt</strong> schematically.
{%
    include image.html
    src="/img/rnn/lstmback24.jpg"
    caption="figure-24: <strong>DIt</strong> schematically."
    hight="110%"
    width="110%"
%}

> * <strong>DIt</strong>
> * Complete listing can be found at [code-14]
{%
    include image.html
    src="/img/rnn/lstmback25.jpg"
    caption="figure-25: <strong>DIt</strong>"
    hight="110%"
    width="110%"
%}

> * <strong>Dhx</strong>, <strong>Dh_next</strong>, <strong>dxt</strong>.
> * Complete listing can be found at [code-15]
{%
    include image.html
    src="/img/rnn/lstmback26.jpg"
    caption="figure-26: <strong>Dhx</strong>, <strong>Dh_next</strong>, <strong>dxt</strong>."
    hight="110%"
    width="110%"
%}

> * Many models like Neural Machine Translators(NMT), Bidirectional Encoder Representation from Transformers(BERT) use word embeddings as their inputs(Xs) which often times need learrning and that is where we need <strong>dxt</strong>
{%
    include image.html
    src="/img/rnn/lstmback27.jpg"
    caption="figure-27: <strong>Dhx</strong>, <strong>Dh_next</strong>, <strong>dxt</strong>"
    hight="110%"
    width="110%"
%}

> * <strong>DCt</strong>.
> * Complete listing can be found at [code-16]
{%
    include image.html
    src="/img/rnn/lstmback28.jpg"
    caption="figure-28: <strong>DCt</strong>."
    hight="110%"
    width="110%"
%}

### Summary
In summary then, that was the walk though of LSTM's forward pass. As a study in contrast, if building a Language model that predicts the next word in the sequence, the training would be similar but we'll calculate loss at every step. The label would be the 'X' just one time step advanced. However, Lets not get ahead of ourselves, before we get to a language model, lets look at the backward pass(Back Propagation) for LSTM in the next post.
