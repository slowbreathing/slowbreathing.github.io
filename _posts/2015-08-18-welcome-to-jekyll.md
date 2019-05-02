---
layout: post
title:  "Softmax and its Gradient"
mathjax: true
date:   2015-08-18 15:07:19
categories: [tutorial]
comments: true
---
Softmax is essentially a vector function. It takes n inputs and produces and n outputs.  
$$softmax(a)=\begin{bmatrix}
a_1\\
a_2\\
\cdots\\
a_N
\end{bmatrix}\rightarrow \begin{bmatrix}
S_1\\
S_2\\
\cdots\\
S_N
\end{bmatrix}$$

And the actual per-element formula is:   
$$softmax_j = \frac{e^{a_j}}{\sum_{k=1}^{N} e^{a_k}}$$

As one can see the output function can only be positive because of the exponential and the values range between 0 and 1. Also,  as the value  appears in the denominator summed up with other positive numbers.

{% highlight python %}
def softmax(logits):
    #row represents num classes but they may be real numbers
    #So the shape of input is important
    #([[1, 3, 5, 7],
    #  [1,-9, 4, 8]]

    #softmax will be for each of the 2 rows
    #[[2.14400878e-03 1.58422012e-02 1.17058913e-01 8.64954877e-01]
    #[8.94679461e-04 4.06183847e-08 1.79701173e-02 9.81135163e-01]]
    #respectively But if the input is Tranposed clearly the answer
    #will be wrong.

    #That needs to be converted to probability
    #column represents the vocabulary size.

    r, c = logits.shape
    predsl = []
    for row in logits:
        inputs = np.asarray(row)
        #print("inputs:",inputs)
        predsl.append(np.exp(inputs) / float(sum(np.exp(inputs))))
    return np.array(predsl)
{% endhighlight %}

Above is a [softmax][softmax] implementation.
{% highlight python %}
    x = np.array([[1, 3, 5, 7],
          [1,-9, 4, 8]])
    print("x:",x)
    sm=softmax(x)
    print("softmax:",sm)
    #prints out
    #[[2.14400878e-03 1.58422012e-02 1.17058913e-01 8.64954877e-01],
    #[8.94679461e-04 4.06183847e-08 1.79701173e-02 9.81135163e-01]]
{% endhighlight %}
Above is a [softmaxtest][softmaxtest] implementation.

The softmax function is very similar to the Logistic regression cost function. The only difference being that the sigmoid makes the output binary interpretable whereas, softmax's output can be interpreted as a multiway shootout. With the the above two rows individually summing up to one.






![Smithsonian Image]({{ site.url }}/img/self.png)
That being me.


[softmax]: https://github.com/slowbreathing/Deep-Breathe/blob/master/org/mk/training/dl/common.py
[softmaxtest]: https://github.com/slowbreathing/Deep-Breathe/blob/master/org/mk/training/dl/softmaxtest.py
