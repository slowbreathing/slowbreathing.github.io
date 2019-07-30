---
layout: post
title:  "Introduction: Why another technical blog?"
excerpt: "Pictures say a thousand words is a cliche, but I believe in it. I have made a career out of it. Complex programming or logical concepts should be understood with the help of pictures."
mathjax: true
date:   2019-05-01 15:07:19
categories: [article]
tags: Introduction
comments: true
---

<strong>Artificial Intelligence especially deep learning employs extremely complex models.</strong> Complexity stems from two major sources.
> * <strong>Firstly</strong>, Models that have multiple dimensional representations have to be expressed mathematically. Often these models(RNNs, LSTMs, NMTs, BERTs) also has a time dimension that have to be represented as well adding to the complexity.
> * <strong>Secondly</strong>, the mathematical model has to be translated to code and executed on CPUs/GPUs. When I illustrate these models in later articles, one realizes the importance of looking at the whole stack or at least have an understanding of the whole stack. This enables, in the very least, easier debugging. At best, one understands the model’s execution and behavior and modifications that require to be done to suit our specific use-case(s).
<p>In my experience, for most clients I have work for, the single most important reason why models underperform is that the hyperparameters are not understood well and hence most often misconfigured. If from hardware to the software model and everything in between is understood well, then Deep Learning becomes fun and magic. <strong>This blog over the next couple of months will try to lower the barrier to entry.</strong></p>
<p>Being in the Optimisation field for quite some time, I have understood the importance of how stuff works under-the-hood. I have a microarchitecture background, specifically X86 and vector-based architectures. Microarchitecture has been my guiding light. It has helped me solve many complex optimization problems despite some of them being programmed in high-level languages like Java or python.</p>
In this short post, <strong>which has nothing to do with Artificial Intelligence just yet</strong>, I demonstrate the <strong>importance of understanding stuff under-the-hood</strong> and <strong>thinking in pictures and then linking it to the code</strong>.  


### Importance of understanding stuff under-the-hood
Let us say that one wants to understand the underlying assembler for a high-level language and the way it executes on a CPU( at least theoretically ;-)). An easy way to understand this is to look at the usage convention for x86 general-purpose registers and do a light reading on the various operations support by the x86 architecture. Once this is known the code becomes extremely clear. Let us understand this with a very simple C program before we do this for a higher-level language.
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

Consider the above code where "main" calls "multstore" and "multstore" calls "mult2".

{%
    include image.html
    src="/img/introduction/x86reg.jpg"
    caption="figure-1"
    hight="110%"
    width="110%"
%}

Compilers have the above convention in mind for x86 register usage. This is usually done so that software written by different people and often compiled by different compilers can interact with each other.

> * callee-saved: If a function A calls function B function B <strong>cannot</strong> change the values of these registers. This can be done by either not changing values at all or pushing values from these registers to the stack on entry and restoring them on exit.
> * caller-saved: If a function A calls function B then, it means it <strong>can</strong> be modified by anyone. Caller-saved because if the caller has some local data in these registers then it is caller's responsibility to save it before making the call.

{%
    include image.html
    src="/img/introduction/assemblercall.jpg"
    caption="figure-2"
    hight="110%"
    width="110%"
%}

For the most part, the above figure should clarify the use of registers in x86. Since the basic idea is clear, the same can be extended for higher-level languages.

### Extending the general idea of registers to higher level languages(Java)
{% highlight Java %}
private static class Shape{
      public Shape(List<Point> shape) {
          for (Point shape1 : shape) {
              if(this.head==null){
                  this.head=this.tail=shape1;
                  size++;
              }
              else{
                  this.tail.next=shape1;
                  size++;    
                  this.tail=shape1;
              }
          }
      }
      Shape next;
      Point head;
      Point tail;
      int size;
      void movex(int offset){
          Point tmpnext=this.head;
          while(tmpnext!=null){
              tmpnext.x=tmpnext.x+offset;
              tmpnext=tmpnext.next;
          }
      }
      void movey(int offset){
          Point tmpnext=this.head;
          while(tmpnext!=null){
              tmpnext.y=tmpnext.y+offset;
              tmpnext=tmpnext.next;
          }
      }
Listing-2
{% endhighlight %}

Consider the code above, We have a list of points making up a shape and when “movex” or “movey” function gets called with an offset it simply moves all the points’ X or Y coordinates by that offset. Now because it is a list of points that make up the shape, moving through that list going to be expensive because the CPU prefetchers cannot “guess” the pattern of load and cannot preload to cache. This results in resource stalls, which is another way of saying that the CPU is sitting idle.

{% highlight Java linenos %}
  public static void main(String [] args){
    long start=System.nanoTime();
    for (int i = 0; i < 1000000; i++) {
        int totalx=0;
        int totaly=0;
        int totalpoints=0;
        Shape s=shapelist.head;
        while(s!=null){
            totalx += s.xsum();
            totaly += s.ysum();
            totalpoints += s.numpoints();
            s=s.next;
        }
        int offsetx=totalx/totalpoints;
        int offsety=totaly/totalpoints;
        s=shapelist.head;
        while(s!=null){
            s.movex(offsetx);
            s.movey(offsety);
            s=s.next;
        }
    }
  }
Listing-3
{% endhighlight %}

Line 16-21 is where the call is being made to functions "movex" and "movey". The question is can a profiler pinpoint how the java code executes and the resource stalls that this code is going to encounter. Once this is understood, a similar analogy can be extended to more complex code.
Just before we get started a few things to keep in mind.
> * The profiler being used is <a href="https://www.oracle.com/technetwork/server-storage/solarisstudio/features/performance-analyzer-2292312.html">Performance Analyzer</a> also called <a href="https://www.oracle.com/technetwork/server-storage/solarisstudio/features/performance-analyzer-2292312.html">Solaris Analyzer</a>. Probably one of the best profilers to deep dive into the code at an instruction level. <a href="https://www.oracle.com/technetwork/server-storage/solarisstudio/features/performance-analyzer-2292312.html">Performance Analyzers</a> would require a post in itself.
> * The <a href="https://www.oracle.com/technetwork/server-storage/solarisstudio/features/performance-analyzer-2292312.html">performance analyzer</a> for this run has be configured to capture resource stalls for the above code.
> * Despite Java being an interpreted language, in practice, it is optimized on-the-fly by a <a href="https://en.wikipedia.org/wiki/Just-in-time_compilation">JIT compiler</a>. These are usually profile guided optimizations and topic for another day. The code shown by <a href="https://www.oracle.com/technetwork/server-storage/solarisstudio/features/performance-analyzer-2292312.html">Performance Analyzers</a> is <a href="https://en.wikipedia.org/wiki/Just-in-time_compilation">JIT compiler</a> generated. Typically we get this profile when the code has run for some time for it to be JIT-compiled.

### Code walk through using Solaris Analyzer
> * Measuring CPU cycles for the code
{%
    include image.html
    src="/img/introduction/sol1.jpe"
    caption="figure-3:CPU Cycles for the code in listing 1 and 2."
    hight="110%"
    width="110%"
%}

> * Walk through:Step-1
{%
    include image.html
    src="/img/introduction/sol2.jpeg"
    caption="figure-4:Bottom of the screen is the size of the object using <a href='https://openjdk.java.net/projects/code-tools/jmh/' >JMH</a>. "
    hight="110%"
    width="110%"
%}

> * Walk through:Step-2
{%
    include image.html
    src="/img/introduction/sol3.jpeg"
    caption="figure-5"
    hight="110%"
    width="110%"
%}

> * Walk through:Step-3
{%
    include image.html
    src="/img/introduction/sol4.jpeg"
    caption="figure-6"
    hight="110%"
    width="110%"
%}

> * Walk through:Step-4
{%
    include image.html
    src="/img/introduction/sol5.jpeg"
    caption="figure-7:This is the big culprit, loading the next pointer. And this will show up as major resource consumer in figure-17."
    hight="110%"
    width="110%"
%}

> * Walk through:Step-5
{%
    include image.html
    src="/img/introduction/sol6.jpeg"
    caption="figure-8: A <a href='https://medium.com/software-under-the-hood/under-the-hood-java-peak-safepoints-dd45af07d766'>JVM safepoint</a> is a range of execution where the state of the executing thread is well described."
    href="https://medium.com/software-under-the-hood/under-the-hood-java-peak-safepoints-dd45af07d766"
    hight="110%"
    width="110%"
%}

> * Walk through:Step-6
{%
    include image.html
    src="/img/introduction/sol7.jpeg"
    caption="figure-9"
    hight="110%"
    width="110%"
%}

> * Walk through:Step-7
{%
    include image.html
    src="/img/introduction/sol8.jpeg"
    caption="figure-10"
    hight="110%"
    width="110%"
%}

> * Walk through:Step-8
{%
    include image.html
    src="/img/introduction/sol9.jpeg"
    caption="figure-11"
    hight="110%"
    width="110%"
%}

> * Walk through:Step-9
{%
    include image.html
    src="/img/introduction/sol10.jpeg"
    caption="figure-12"
    hight="110%"
    width="110%"
%}

> * Walk through:Step-10
{%
    include image.html
    src="/img/introduction/sol11.jpeg"
    caption="figure-13"
    hight="110%"
    width="110%"
%}

> * Walk through:Step-11
{%
    include image.html
    src="/img/introduction/sol12.jpeg"
    caption="figure-14:This is the big culprit, loading the next pointer except it is for y. This too will show up as major resource consumer in figure-17."
    hight="110%"
    width="110%"
%}

> * Walk through:Step-12
{%
    include image.html
    src="/img/introduction/sol13.jpeg"
    caption="figure-15"
    hight="110%"
    width="110%"
%}

> * Walk through:Step-13
{%
    include image.html
    src="/img/introduction/sol14.jpeg"
    caption="figure-16"
    hight="110%"
    width="110%"
%}

> * Walk through:Step-14
{%
    include image.html
    src="/img/introduction/sol15.jpeg"
    caption="figure-17"
    hight="110%"
    width="110%"
%}

### Summary

In summary, then, the figure-17 should clarify the resource stalls exhibited by the code. It is time-consuming to take the first-principles approach to understand deep technical concepts but ultimately it brings great joy. I would like to bring similar understanding to concepts in general and Deep Learning Concepts in particular. In fact, I have seen Neural Machine Translation Based systems grossly underperform and, it was simply because most of the hyperparameters were not understood at all. <strong><a href="https://github.com/slowbreathing/Deep-Breathe">Deep-Breathe</a></strong> is a complete and pure python implementation of these models, especially but not limited to <strong><a href="https://github.com/slowbreathing/Deep-Breathe/blob/master/org/mk/training/dl/manualmachinetranslation.py">Neural Machine Translator</a></strong>. <strong>In the next few articles, I will go into the details of their inner workings. And in this blog, I will focus more on Pictures and Code.</strong>
