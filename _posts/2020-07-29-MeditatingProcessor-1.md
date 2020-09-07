---
layout: post
title:  "Meditating with microprocessors Series: Part-1: Artificial Intelligence based Hardware(Microprocessor) tuning: Implementing a very simple idea"
excerpt: "In the <strong><a href='/tags/#Meditating-with-microprocessors'>current series</a></strong> titled <strong><a href='/tags/#Meditating-with-microprocessors'>Meditating-with-microprocessors</a></strong>, I demonstrate the use of <strong>Artificial Intelligence</strong> to tune <strong>microprocessors</strong> for <strong>Ultra-Low-Latency</strong> and <strong>Realtime</strong> loads. The techniques, in general, can be extended to other components of a computer system like the storage devices, memory etc. However, the article series and my work is currently restricted to <strong>Intel microprocessors only</strong>. <strong>In future</strong>, we may extend this to other hardware components of a computer system. This is a very <strong>specialized</strong> and <strong>intense</strong> field and hence I intend to break it down using first principles approach into simpler pieces of technology that are easy to understand. There are 5 parts to the series <strong><a href='/articles/2020-07/MeditatingProcessor-1'>Artificial Intelligence based Hardware(Microprocessor) tuning: Implementing a very simple idea(part-1)</a></strong>, <strong><a href='/articles/2020-07/MeditatingProcessor-2'>A crashcourse in Microarchitecture and Linux CPUIDLE interface(part-2)</a></strong>, <strong><a href='/articles/2020-07/MeditatingProcessor-3'>Trading off power for UltraLowLatency (part-3)</a></strong>, <strong><a href='/articles/2020-07/MeditatingProcessor-4'>Artificial Intelligence guided Predictive MicroProcessor tuning (part-4)</a></strong>, <strong><a href='/articles/2020-07/MeditatingProcessor-5'>Appendix:Tools of the trade(part-5) </a></strong>. <strong>In the balance then, this is a documentation of my journey navigating these utterly specialized fields ( microarchitecture and Artificial Intelligence ), how to marry them together, the issues I faced, the respective solutions, what (how much) are the benefits if any, and what to have in the toolbox.<strong> "
mathjax: true
date:   2020-07-29 15:07:19
categories: [article]
tags: Artificial-Intelligence Deep-Learning LSTM NLP Microarchitecture UltraLowLatency RealTime Performance Meditating-with-microprocessors
image:
  feature: mwprocs/c-states3.jpeg
comments: true
---

### [Meditating with microprocessors][Meditating-with-microprocessors]
1. [Artificial Intelligence based Hardware(Microprocessor) tuning: Implementing a very simple idea][Meditating-with-microprocessors Part-1] <strong>([part-1][Meditating-with-microprocessors Part-1])<strong>
    * [Introduction][Introduction1]
    * [Meditation and microprocessors: An extremely simple idea]
    * [MicroProcessor C-states]
    * [A quick Summary of wakeup_latency]
    * [Artificial Intelligence based Hardware(Miroprocessor) tuning]
    * [Summary][Summary1]
2. [A crashcourse in Microarchitecture and Linux CPUIDLE interface][Meditating-with-microprocessors Part-2] <strong>([part-2][Meditating-with-microprocessors Part-2])<strong>
    * [Introduction][Introduction2]
    * [MicroProcessor Components]
      * [Core]
        * [Sandy Bridge Pipeline:Frontend(Instruction load, decode, cache)]
        * [Sandy Bridge Pipeline:Execution]
        * [Sandy Bridge Pipeline:Backend (Data load and Store)]
        * [Haswell Pipeline]  
      * [Uncore]
    * [Linux Inteface to CPUIDLE]
      * [CPUIDLE subsystem]  
      * [CPUIDLE subsystem:Driver load]
      * [CPUIDLE subsystem:Call the Driver]
      * [CPUIDLE subsystem:Governor]
      * [CPUIDLE subsystem:Gathering and undertanding latency data]
    * [Summary][Summary2]  
3. [Trading off power for UltraLowLatency][Meditating-with-microprocessors Part-3] <strong>([part-3][Meditating-with-microprocessors Part-3])<strong>
  * [Introduction][Introduction3]
  * [Core and Uncore:Core]
    * [Power]
      * [Power:Turn things off]
      * [Power:c-states]
      * [Power:Tuned]
        * [Power:Tuned:c-states requests]  
        * [Power:Tuned:Hardware State Residency]
        * [Power:Tuned:Influx:Grafana:c-states]
        * [Power:Tuned:Measuring Latency]
        * [Power:Tuned:Hardware Latency]
        * [Power:Tuned:Wakeup Latency]
        * [Power:Tuned:Influx:Grafana:latency]
      * [Power:PMQOS]
      * [Power:Turn things down]
        * [Power:Turn things down:P-states:Hardware Latency]
      * [Core and Uncore:Uncore]
        * [Core and Uncore:Uncore:montioring and Tuning]
        * [Core and Uncore:Uncore:montioring and Tuning:Hardware Latency]
        * [Core and Uncore:Uncore:montioring and Tuning:Wakeup Latency]
      * [Core and Uncore:How much power can be saved]  
   * [Summary][Summary3]  
4. [Artificial Intelligence guided Predictive MicroProcessor tuning][Meditating-with-microprocessors Part-4] <strong>([part-4][Meditating-with-microprocessors Part-4])<strong>
  * [Introduction][Introduction4]
  * [Is there a solution?]
  * [Is there a smarter solution?: Artificial Intelligence model based proactive descision making]
  * [Recognizing the pattern]
  * [Transformer as the backbone]
  * [Transfer Learning inspiration from NLP]
  * [Fine tuning pre-trained networks]  
  * [Artificial Intelligence model based proactive and predictive descision making]
  * [The Final Cut]
  * [Summary][Summary4]
5. [Appendix:Tools of the trade][Meditating-with-microprocessors Part-5] <strong>([part-5][Meditating-with-microprocessors Part-5])<strong>
  * [Introduction][Introduction5]
  * [Linux Tools:An incisive but limited view]
    * [Linux Tools:Event Sources:uprobes and kprobes]
    * [Linux Tools:Event Sources:Tracepoints]
    * [Linux Tools:Extraction Tools]
    * [Linux Tools:Extraction Tools:Ftrace:The coolest tracing dude on the planet]
      * [Linux Tools:Extraction Tools:Ftrace:Engineering]  
      * [Linux Tools:Extraction Tools:Ftrace:Summary]
    * [Linux Tools:Extraction Tools:BPF:The most flexible tracer]
      * [Linux Tools:Extraction Tools:BPF:BPF Virtual Machine]
      * [Linux Tools:Extraction Tools:BPF:In Kernel rich Data Structures]  
      * [Linux Tools:Extraction Tools:BPF:Stack Trace Walking]
      * [Linux Tools:Extraction Tools:BPF:A defining example]
      * [Linux Tools:Extraction Tools:Ftrace:Summary]

   * [Summary][Summary5]

# <a name="Introduction"></a>
### Introduction
In the <strong>[multi-part series][Meditating-with-microprocessors]</strong> titled <strong>[Meditating-with-microprocessors][Meditating-with-microprocessors]</strong>, I demonstrate the use of <strong>Artificial Intelligence</strong> to tune <strong>microprocessors</strong> for <strong>Ultra-Low-Latency</strong> and <strong>Realtime</strong> loads. The techniques, in general, can be extended to other components of a computer system like the storage devices, memory etc. However, the article series and my work is currently restricted to <strong>Intel microprocessors only</strong>. <strong>In future</strong>, we may extend this to other hardware components of a computer system. This is a very <strong>specialized</strong> and <strong>intense</strong> field and hence I intend to break it down using first principles approach into simpler pieces of technology that are easy to understand. There are 5 parts to the series, <strong>[Meditating with Microprocessors: An essentially simple idea(part-1)][Meditating-with-microprocessors Part-1]</strong> , <strong>[A crashcourse in Microarchitecture and Linux CPUIDLE interface(part-2)][Meditating-with-microprocessors Part-2]</strong>, <strong>[Trading off power for UltraLowLatency (part-3) (part-3)][Meditating-with-microprocessors Part-3]</strong> , <strong>[Artificial Intelligence guided Predictive MicroProcessor tuning (part-4)][Meditating-with-microprocessors Part-4]</strong>, <strong>[Appendix:Tools of the trade (part-5)][Meditating-with-microprocessors Part-5]</strong>. <strong>In the balance then, this is a documentation of my journey navigating these utterly specialized fields ( microarchitecture and Artificial Intelligence ), how to marry them together, the issues I faced, the respective solutions, what (how much) are the benefits if any, and what to have in the toolbox.<strong>

# <a name="Meditation and microprocessors: An extremely simple idea"></a>
#### Meditation and microprocessors: An extremely simple idea
The idea is so simple, it is actually ridiculous. Also, it pays to know of an analogy to compare it to just in case the technology sounds complicated. In our daily lives, we go through cycles of work time and relaxation ( I’ll call it meditation, you may call it meditation or sleep ). The work time is our livelihood, pays our bills but also stresses us out. The meditation time is the recuperation time. It helps us stay sane.

As it turns out, microprocessors, for lack of a better phrase, have a similar sleeping pattern. In fact, modern microprocessors have a very sophisticated sleeping pattern. When they have work to do (execute instructions) they are in waking state (duh) getting vital work done but expending much energy. On the other hand, when there is a lack of work (no instructions to be executed) they spiral into deeper and deeper sleeping states with the intention of saving power and conserving energy. The catch is that the deeper they go into sleep states, the more is the time they take to come back to full awareness to execute instructions again.

# <a name="MicroProcessor C-states"></a>
#### MicroProcessor C-states

> * This is c-states as documented in the [Intel 8th and 9th datasheeet manual].
> * The actual states may differ depending on the exact model of the Microprocessor.   

{%
    include image.html
    src="/img/mwprocs/c-states.png"
    caption="figure-1: <strong>C-states from Intel's Documentation</strong>"
    hight="110%"
    width="110%"
%}

> * This can be queried using the "/sys/devices/system" interface on Linux
> * The actual states may differ depending on the exact model of the Microprocessor.
> * Names can be confusing too. So there are 9 c-states(c-state0-c-state8) and they are called C0,C1,C1E,C3,C6,C7s,C8,C9,C10.

{%
    include image.html
    src="/img/mwprocs/c-states2.png"
    caption="figure-2: <strong>c-states on my current microprocessor</strong>"
    hight="110%"
    width="110%"
%}

> * Each of the green lines ending on a positive number represents entry into a c-state.  
> * Again, Each one of the green line ending on a negative number (-5) represents the exit from the previous c-state.
> * The value returned to denote an exit is actually 4294967295 which is -1. size_t is an unsigned integral type, it is the largest possible representable value for this type

# <a name="c-state transitions"></a>
{%
    include image.html
    src="/img/mwprocs/c-states3.png"
    caption="figure-3: <strong>c-states transitions</strong>"
    hight="110%"
    width="110%"
%}

> * The C0 is the state where the code is executing. So there is O 'wakeup latency'. 'Wakeup latency' refers to the time it takes for the microprocessor to wake up from a sleep state. What you see marked in red is application latency.

> * The rest of the picture c-state1(C1) to c-state8(C10) refers to the 'wakeup latency' and it goes up as the microprocessor goes into deeper c-states   

{%
    include image.html
    src="/img/mwprocs/c-states_latency.png"
    caption="figure-4: <strong>Latency(us) on x axis C-state on y axis</strong>"
    hight="110%"
    width="110%"
%}

# <a name="A quick Summary of wakeup_latency"></a>
#### A quick Summary of wakeup_latency:
Hopefully, the above illustrations were helpful in understanding the crux of the matter, and references to meditation were not distracting. Here is a quick summary, modern Microprocessors switch states(deeper c-states as illustrated in figure-{1,2,3,4}) to save energy when there are no instructions to execute. And fling back to C0 when there are instructions to execute. This flinging back time is called <strong>‘wakeup_latency’</strong> and is higher for deeper states as illustrated in figure-4. And this 'wakeup_latency' is problematic for Ultra-Low-Latency applications. The source of the problem   

# <a name="Artificial Intelligence based Hardware(Miroprocessor) tuning"></a>
#### Artificial Intelligence based Hardware(Miroprocessor) tuning
If you consider figure-3 there were close to 25000 transitions (give or take) in a stretch of 15 seconds. Each of those costing valuable hundreds of microseconds. The effect can also get magnified because there might be multiple application threads vying for microprocessor resources. It can get worse. There might be a [Domino] effect of these occurrences. There is a specific term coined for Domino effect in the software world. I believe it is called [coordinated omission] and [here is a great reference to it].

> * Is there a microprocessor setting that restricts spiraling into deeper c-states so that the wakeup latency is effectively ‘0’? Yes, there is, but it will drain a lot of energy/power and may be detrimental to the microprocessor’s health in the long run ( Similar to life without meditation ).
> * Can we look at the historical load and predict when it might occur again. If yes, then can we configure it to a setting which is suited to Ultra-Low-Latency or Realtime workloads. <strong>The answer to that is a huge yes.</strong> Understandably this has to be done on the fly at runtime using the software controls provided by the Hardware and the OS.
> * <strong>This is one of the things that our software intends to do</strong>. So once we have the load being predicted by Artificial Intelligence based model, we tune the microprocessor to the settings which reduces latency if the goal is to lower latency.
> * We reset the settings to power save(normal mode) when high load had tided over. <strong>As of now, the implementation is only for Intel microprocessors.</strong> But in future, we believe we can extend it to Memory chips, HDDs and SSDs, and so forth.
> * <strong>There is something else.</strong> Just like there is unique signature that every person has, in a similar manner there is a unique signature for a particular load. This signature can be recognized using pre-training using techniques similar to <strong>transfer-learning</strong> in NLP and BERT.  

<a name="Summary">
### Summary

In summary then, modern microprocessors have power saving states they enter into when there is no work to do. All very good, till you consider that they have to get back to “full click” when they have to perform work. Ordinarily, this is not a problem but for some latency(performance) sensitive application. This approach is reactive or causal. Can we make this proactive based on some Artificial Intelligence based load prediction. Rest of the articles in the series is an under-the-hood look at how the processors interface with software(OS) and if there is a case to be made for Artificial Intelligence. Also there are things to explore for performance minded programmers. So get your hands dirty.



<!--Series-->
[Stillwaters]: https://www.stillwaters.ai
[Meditating-with-microprocessors]: /tags/#Meditating-with-microprocessors
[Meditating-with-microprocessors Part-1]: MeditatingProcessor-1
[Meditating-with-microprocessors Part-2]: MeditatingProcessor-2
[Meditating-with-microprocessors Part-3]: MeditatingProcessor-3
[Meditating-with-microprocessors Part-4]: MeditatingProcessor-4
[Meditating-with-microprocessors Part-5]: MeditatingProcessor-5

<!--Doc1-->
[Introduction1]: MeditatingProcessor-1#Introduction
[Meditation and microprocessors: An extremely simple idea]: MeditatingProcessor-1#Meditation and microprocessors: An extremely simple idea
[MicroProcessor C-states]: MeditatingProcessor-1#MicroProcessor C-states
[A quick Summary of wakeup_latency]: MeditatingProcessor-1#A quick Summary of wakeup_latency
[Artificial Intelligence based Hardware(Miroprocessor) tuning]: MeditatingProcessor-1#Artificial Intelligence based Hardware(Miroprocessor) tuning
[Summary1]: MeditatingProcessor-1#Summary

<!--Doc2-->
[Introduction2]: MeditatingProcessor-2#Introduction
[MicroProcessor Components]: MeditatingProcessor-2#MicroProcessor Components
[Core]: MeditatingProcessor-2#Core
[Sandy Bridge Pipeline:Frontend(Instruction load, decode, cache)]: MeditatingProcessor-2#Sandy Bridge Pipeline:Frontend(Instruction load, decode, cache)
[Sandy Bridge Pipeline:Execution]: MeditatingProcessor-2#Sandy Bridge Pipeline:Execution
[Sandy Bridge Pipeline:Backend (Data load and Store)]: MeditatingProcessor-2#Sandy Bridge Pipeline:Backend (Data load and Store)
[Haswell Pipeline]: MeditatingProcessor-2#Haswell Pipeline
[Uncore]: MeditatingProcessor-2#Uncore
[Linux Inteface to CPUIDLE]: MeditatingProcessor-2#Linux Inteface to CPUIDLE
[CPUIDLE subsystem]: MeditatingProcessor-2#CPUIDLE subsystem  
[CPUIDLE subsystem:Driver load]: MeditatingProcessor-2#CPUIDLE subsystem:Driver load
[CPUIDLE subsystem:Call the Driver]: MeditatingProcessor-2#CPUIDLE subsystem:Call the Driver
[CPUIDLE subsystem:Governor]: MeditatingProcessor-2#CPUIDLE subsystem:Governor
[CPUIDLE subsystem:Gathering and undertanding latency data]: MeditatingProcessor-2#CPUIDLE subsystem:Gathering and undertanding latency data
[Summary2]: MeditatingProcessor-2#Summary

<!--Doc3-->
[Introduction3]: MeditatingProcessor-3#Introduction
[Core and Uncore:Core]: MeditatingProcessor-3#Core and Uncore:Core
[Power]: MeditatingProcessor-3#Power
[Power:Turn things off]: MeditatingProcessor-3#Power:Turn things off
[Power:c-states]: MeditatingProcessor-3#Power:c-states
[Power:Tuned]: MeditatingProcessor-3#Power:Tuned
[Power:Tuned:c-states requests]: MeditatingProcessor-3#Power:Tuned:c-states requests
[Power:Tuned:Hardware State Residency]: MeditatingProcessor-3#Power:Tuned:Hardware State Residency
[Power:Tuned:Influx:Grafana:c-states]: MeditatingProcessor-3#Power:Tuned:Influx:Grafana:c-states
[Power:Tuned:Measuring Latency]: MeditatingProcessor-3#Power:Tuned:Measuring Latency
[Power:Tuned:Hardware Latency]: MeditatingProcessor-3#Power:Tuned:Hardware Latency
[Power:Tuned:Wakeup Latency]: MeditatingProcessor-3#Power:Tuned:Wakeup Latency
[Power:Tuned:Influx:Grafana:latency]: MeditatingProcessor-3#Power:Tuned:Influx:Grafana:latency
[Power:PMQOS]: MeditatingProcessor-3#Power:PMQOS
[Power:Turn things down]: MeditatingProcessor-3#Power:Turn things down
[Power:Turn things down:P-states:Hardware Latency]: MeditatingProcessor-3#Power:Turn things down:P-states:Hardware Latency
[Core and Uncore:Uncore]: MeditatingProcessor-3#Core and Uncore:Uncore
[Core and Uncore:Uncore:montioring and Tuning]: MeditatingProcessor-3#Core and Uncore:Uncore:montioring and Tuning
[Core and Uncore:Uncore:montioring and Tuning:Hardware Latency]: MeditatingProcessor-3#Core and Uncore:Uncore:montioring and Tuning:Hardware Latency
[Core and Uncore:Uncore:montioring and Tuning:Wakeup Latency]: MeditatingProcessor-3#Core and Uncore:Uncore:montioring and Tuning:Wakeup Latency
[Core and Uncore:How much power can be saved]: MeditatingProcessor-3#Core and Uncore:How much power can be saved
[Summary3]: MeditatingProcessor-3#Summary

<!--Doc4-->
[Introduction4]: MeditatingProcessor-4#Introduction
[Is there a solution?]: MeditatingProcessor-4#Is there a solution?
[Is there a smarter solution?: Artificial Intelligence model based proactive descision making]: MeditatingProcessor-4#Is there a smarter solution?: Artificial Intelligence model based proactive descision making
[Recognizing the pattern]: MeditatingProcessor-4#Recognizing the pattern
[Transformer as the backbone]: MeditatingProcessor-4#Transformer as the backbone
[Transfer Learning inspiration from NLP]: MeditatingProcessor-4#Transfer Learning inspiration from NLP
[Fine tuning pre-trained networks]: MeditatingProcessor-4#Fine tuning pre-trained networks
[Artificial Intelligence model based proactive and predictive descision making]: MeditatingProcessor-4#Artificial Intelligence model based proactive and predictive descision making
[The Final Cut]: MeditatingProcessor-4#The Final Cut
[Summary4]: MeditatingProcessor-4#Summary

<!--Doc5-->
[ftrace]: MeditatingProcessor-5#Ftrace:The coolest tracing dude on the planet
[EBPF]: MeditatingProcessor-5#EBPF: The most flexible tracer
[“ftrace” is the coolest tracing dude on the planet]: MeditatingProcessor-5#Ftrace:The coolest tracing dude on the planet
[Introduction5]: MeditatingProcessor-5#Introduction
[Linux Tools:An incisive but limited view]: MeditatingProcessor-5#Linux Tools:An incisive but limited view
[Linux Tools:Event Sources:uprobes and kprobes]: MeditatingProcessor-5#Linux Tools:Event Sources:uprobes and kprobes
[Linux Tools:Event Sources:Tracepoints]: MeditatingProcessor-5#Linux Tools:Event Sources:Tracepoints
[Linux Tools:Extraction Tools]: MeditatingProcessor-5#Linux Tools:Extraction Tools
[Linux Tools:Extraction Tools:Ftrace:The coolest tracing dude on the planet]: MeditatingProcessor-5#Linux Tools:Extraction Tools:Ftrace:The coolest tracing dude on the planet
[Linux Tools:Extraction Tools:Ftrace:Engineering]: MeditatingProcessor-5#Linux Tools:Extraction Tools:Ftrace:Engineering
[Linux Tools:Extraction Tools:Ftrace:Summary]: MeditatingProcessor-5#Linux Tools:Extraction Tools:Ftrace:Summary
[Linux Tools:Extraction Tools:BPF:The most flexible tracer]: MeditatingProcessor-5#Linux Tools:Extraction Tools:BPF:The most flexible tracer
[Linux Tools:Extraction Tools:BPF:BPF Virtual Machine]: MeditatingProcessor-5#Linux Tools:Extraction Tools:BPF:BPF Virtual Machine
[Linux Tools:Extraction Tools:BPF:In Kernel rich Data Structures]: MeditatingProcessor-5#Linux Tools:Extraction Tools:BPF:In Kernel rich Data Structures
[Linux Tools:Extraction Tools:BPF:Stack Trace Walking]: MeditatingProcessor-5#Linux Tools:Extraction Tools:BPF:Stack Trace Walking
[Linux Tools:Extraction Tools:BPF:A defining example]: MeditatingProcessor-5#Linux Tools:Extraction Tools:BPF:A defining example
[Linux Tools:Extraction Tools:Ftrace:Summary]: MeditatingProcessor-5#Linux Tools:Extraction Tools:Ftrace:Summary
[Summary5]: MeditatingProcessor-5#Summary
<!--External references-->
[Intel 8th and 9th datasheeet manual]: [https://www.intel.com/content/dam/www/public/us/en/documents/datasheets/8th-gen-core-family-datasheet-vol-1.pdf]
[Domino]: https://en.wikipedia.org/wiki/Domino_effect
[coordinated omission]: https://www.azul.com/files/HowNotToMeasureLatency_LLSummit_NYC_12Nov2013.pdf
[here is a great reference to it]: https://www.azul.com/files/HowNotToMeasureLatency_LLSummit_NYC_12Nov2013.pdf
