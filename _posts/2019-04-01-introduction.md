---
layout: post
title:  "Introduction: Why another technical blog?"
excerpt: "Pictures say a thousand words is a cliche, but I believe in it. I have made career out of it. Complex programming or logical concepts should be understood with the help of pictures ."
mathjax: true
date:   2019-05-01 15:07:19
categories: [article]
tags: Introduction
comments: true
---

<strong>Artificial Intelligence especially deep learning employs models which are extremely complex.</strong> Complexity stems from two major sources.
> * <strong>Firstly</strong>, Models that have multiple dimensional representation have to be expressed mathematically. Often these models(RNNs, LSTMs, NMTs, BERTs) also have a time dimension that have to be represented as well adding to the complexity.
> * <strong>Secondly</strong>, the mathematical model has to be translated to code and executed on CPUs/GPUs. When I illustrate these models in later articles, one realizes the importance of looking at the whole stack or at least have an understanding of the whole stack. This enables, in the very least, easier debugging. At best, one understands the model’s execution and behavior and modifications that require to be done to suit our specific use-case(s).
<p>In my experience, for most clients I have work for, the single most important reason why models underperform is because the hyperparameters is not understood well and hence most often mis-configured. If, from hardware to the software model and everything in between is understood well, then Deep Learning become fun and magic. <strong>This blog over the next couple of month will try to lower the barrier to entry.</strong></p>
<p>Being in the Optimisation field for quite some time, I have understood the importance of how  stuff works under-the-hood. I have a microarchitecture background, specifically X86 and vector based architectures. Microarchitecture has been my guiding light.  It has helped me solve many complex optimization problems despite some of them being programmed in high level languages like Java or python.</p>
In this short post, <strong>which has nothing to do with Artificial Intelligence just yet</strong>, I demonstrate the <strong>importance of understanding stuff under-the-hood</strong> and <strong>thinking in pictures and then linking it to the code</strong>.  


### Importance of understanding standing stuff under-the-hood
Lets say that one wants understand the underlying assembler for a high level language and the way it executes on a CPU( atleast theoretically ;-)). An easy way to understand this is to look at the usage convention for x86 general purpose registers and do a light reading on the various operations support by the x86 architecture. Once this is known the code becomes extremely clear. Lets understand this with a very simple C program before we do this for a higher level language.
{% highlight c %}
  int main() {
      long d;
      multstore(2, 3, &d);
      printf("2 * 3 --> %ld\n", d);
      return 0;
  }

  long mult2(long a, long b) {
      long s = a * b;
      return s;
   }

   void multstore(long x, long y, long *dest) {
       long t = mult2(x, y);
       *dest = t;
   }
Listing-1
{% endhighlight %}

Consider the above code where, "main" calls "multstore" and "multstore" calls "mult2".

{%
    include image.html
    src="/img/introduction/x86reg.jpg"
    caption="figure-1"
    hight="110%"
    width="110%"
%}

Compilers have the above convention in mind for x86 register usage. This is usually done so that software written by different people and often compiled by different compilers can interact with each other.
hight="110%" width="110%"
> * callee-saved: If a function A calls function B function B <strong>cannot</strong> change the values of these registers. This can be done by either not changing values at all or pushing values from these registers to the stack on entry and restoring them on exit.
> * caller-saved: If a function A calls function B then, it means it <strong>can</strong> be modified by anyone. Caller-saved because if the caller has some local data in these registers then it is caller's responsibility to save it before making the call.

![softmax grad]({{ site.url }}/img/introduction/assemblercall.jpg){: hight="110%" width="110%" figcaption="figure-1" }
<center>figure-2</center>

The first part demonstrates how x86 registers are used and second demonstrates how to extend the knowledge to a high level language like Java or python.
