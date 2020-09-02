---
layout: post
title:  "Meditating with microprocessors Series: Part-4: Artificial Intelligence guided Predictive MicroProcessor tuning"
excerpt: "The current article is part of a bigger <strong><a href='/tags/#Meditating-with-microprocessors'>series</a></strong> titled <strong><a href='/tags/#Meditating-with-microprocessors'>Meditating-with-microprocessors</a></strong>,in which I demonstrate the use of <strong>Artificial Intelligence</strong> to tune <strong>microprocessors</strong> for <strong>Ultra Low Latency</strong> and <strong>Realtime</strong> loads. There are 5 parts to the series <strong><a href='/articles/2020-07/MeditatingProcessor-1'>Artificial Intelligence based Hardware(Microprocessor) tuning: Implementing a very simple idea(part-1) </a></strong>, <strong><a href='/articles/2020-07/MeditatingProcessor-2'>A crashcourse in Microarchitecture and Linux CPUIDLE interface(part-2)</a></strong>, <strong><a href='/articles/2020-07/MeditatingProcessor-3'>Trading off power for UltraLowLatency(part-3)</a></strong>, <strong><a href='/articles/2020-07/MeditatingProcessor-4'>Artificial Intelligence guided Predictive MicroProcessor tuning(part-4)</a></strong>, <strong><a href='/articles/2020-07/MeditatingProcessor-5'>Appendix:Tools of the trade (part-5) </a></strong>. In the current article, we explore the use of Artificial Intelligence to reduce latency and save power at the same time. As we have understood, left to itself, the microprocessor will sense the load and configure itself for it(suitable c-states, P-states, etc. ). <strong>However, this process is reactive at best, and if latency matters then it is already late. By reactive, I mean the arrival of a remote message triggers an interrupt and from there, one thing leads to another before the microprocessor configures itself for the load. Causal is another word for it.</strong> <strong>With an Artificial Intelligence based model can we reduce latency by predicting load and preconfigure the microprocessor for load and then postconfigure to save power once the load has tided over. In other words making the process more proactive and predictive.</strong>"
mathjax: true
date:   2020-07-29 15:07:19
categories: [article]
tags: Artificial-Intelligence Deep-Learning LSTM NLP Microarchitecture UltraLowLatency RealTime Performance Meditating-with-microprocessors
image:
  feature: mwprocs/asymmetrictransformer3.png
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
The current article is part of a bigger <strong>[series][Meditating-with-microprocessors]</strong> titled <strong>[Meditating-with-microprocessors][Meditating-with-microprocessors]</strong>, in which I demonstrate the use of <strong>Artificial Intelligence</strong> to tune <strong>microprocessors</strong> for <strong>Ultra Low Latency</strong> and <strong>Realtime</strong> loads. There are 5 parts to the series, <strong>[Meditating with Microprocessors: An essentially simple idea(part-1)][Meditating-with-microprocessors Part-1]</strong> , <strong>[A crashcourse in Microarchitecture and Linux CPUIDLE interface(part-2)][Meditating-with-microprocessors Part-2]</strong>, <strong>[Core (part-3)][Meditating-with-microprocessors Part-3]</strong> , <strong>[Uncore (part-4)][Meditating-with-microprocessors Part-4]</strong>, <strong>[Appendix:Tools (part-5)][Meditating-with-microprocessors Part-5]</strong>.
In the current article, we explore the use of Artificial Intelligence to reduce latency and save power at the same time. As we have understood, left to itself, the microprocessor will sense the load and configure itself for it(suitable c-states, P-states, etc. ). <strong>However, this process is reactive at best, and if latency matters then it is already late. By reactive, I mean the arrival of a remote message triggers an interrupt and from there, one thing leads to another before the microprocessor configures itself for the load. Causal is another word for it.</strong> Let me put this in perspective. On a modern microprocessor with an average clock speed of roughly 2.5 gigahertz, there are 2500000000 cycles executed per second or 2.5 cycles per nanoseconds. A single instruction can take anywhere between a single cycle to a few tens of cycles depending upon the complexity of the instruction. At this granularity, the moment the CPU can spare a few hundred to a few thousands of nanoseconds of no work, it looks for an opportunity to get into a deep c-state or a low p-state. This is all guided by the policy of the system enforced by the OS. As a result, there are far too many transitions in a short time, costing valuable time in terms of latency and inducing jitter. We are not even talking about a millisecond granularity. Here is <strong>[an example]</strong> of roughly 25000 transitions in a period of 15 seconds. <strong>With an Artificial Intelligence based model can we reduce latency by predicting load and preconfigure the microprocessor for load and then postconfigure it to save power once the load has tided over. In other words making the process more proactive and predictive.</strong>  

<a name="Is there a solution?">
#### Is there a solution?

Well, one solution can be the other extreme. Configure the CPU such that it can never transition out to deeper c-states or lower p-states. This is what I did in the earlier articles. However, this causes a power drain, especially if done for a long time.
The other issue is crossing the Thermal Design Point(TDP). A TDP condition identifies this (power, frequency) pair. This is the limit, operating under which the CPU will never exceed its thermal envelope. TDP marks the worst-case real workload that a customer may run without causing thermal throttling. However, TDP is not a standard that every manufacturer agrees on and is often underestimated. This results in applications causing the CPU to exceed its TDP often times. On an average, everything still works fine but occasionally things may go wrong(I burnt one of my laptops(Not the CPU) 9-10 years ago testing things out).
Lastly, long term wear and tear may be an issue if the CPU deepest state is C0 and the CPU is overclocked for a long time or permanently. That being said, most modern devices are fairly tolerant of overclocking.

<a name="Is there a smarter solution?: Artificial Intelligence model based proactive descision making">
#### Is there a smarter solution?: Artificial Intelligence model based proactive descision making

The idea is quite simple. Use Artificial Intelligence to predict an oncoming load, and change the microprocessor settings to high performance to suit the load. Isolate the high-performance settings on a fraction of cores depending on the requirement and not the complete system. Reset it back to normal once the load has passed over. If an Artificial Intelligence model can recognize a pattern of load much before it occurs then we have a winner here. <strong>And we can then call it proactive instead of reactive or causal.</strong>

<a name="Recognizing the pattern">
#### Recognizing the pattern

<strong>This part is a bit proprietary so the details will be sketchy but the ideas will be clear.<strong> Just like a signature or fingerprint is unique to a person, is there unique signature to a load. And if a particular load has a unique signature, is there a way to identify it? Last but not least, the identification process must be least interfering. Remember, the idea is to reduce latency. If the whole process of identification of load has a huge observer effect, then it may not be worth the effort. <strong>As it turns out, there are ways to capture the pattern of load in a non intrusive way. And I take solid inspiration from NLP for that.</strong>

<a name="Transformer as the backbone">
#### Transformer as the backbone
[NeuralMachineTranslators][NMT] ([NMT]) based on [LSTM][LSTM Series] had excellent results but had few major challenges. Foremost was the <strong>inability</strong> to feed data parallelly that impacted not just the speed of training these models but also the amount of data fed in.
Transformer’s architecture in many ways changed the course of NLP with a lot of improvements over the previous generation [NMT] architectures that were based on [LSTM][LSTM Series]s. While Transformers retained the core encoder-decoder setup there were some vital changes.

> * First improvement was <strong>positional encoding</strong>. Allowing sequential data to be fed in parallelly and yet retain positional information is one of the greatest simple ideas in AI in along time.

> * Using <strong>sinusoidal wave embeddings as position markers</strong> is a solid idea. This allowed data to be fed in parallelly, but even more crucially a bidirectional context could be built without physically feeding the data in both directions.

> * In terms of <strong>training speed alone this was light years faster especially with GPUs.<strong>With much faster training speeds, there are other positive side effects like a much bigger corpus of data could be trained with.

{%
    include image.html
    src="/img/mwprocs/nlpmp0.png"
    caption="figure-1: <strong>Transformer:Encoder-Decoder</strong>"
    hight="110%"
    width="110%"
%}    

> * Second big change was <strong>Multi-Headed-Attention</strong>. Attention by itself has been in the industry for quite some time. However with Multi-Headed-Attention, the model can fixate on multiple patterns within a sequence. And the number of patterns depends on the configurable number of <strong>AttentionHeads</strong>

{%
    include image.html
    src="/img/mwprocs/nlpmp2.png"
    caption="figure-2: <strong>Multi-headed Attention visualization(One head being highlighted)</strong>"
    hight="110%"
    width="110%"
%}    


<a name="Transfer Learning inspiration from NLP">
#### Transfer Learning inspiration from NLP
I have taken a lot of inspiration from the use of Transfer Learning in Artificial Intelligence and especially in Natural Language Processing.

<strong>Here are the key ideas</strong>.

> * Annotated and labeled data is difficult to find but <strong>unlabelled data is plentiful</strong>. Wikipedia, Online books are just some examples.
> * [Transfer Learning] exemplifies learning with unlabelled data.
> * The NLP breakthroughs of 2018 and 2019 were triggered in a big way by Transfer Learning’s application on [Transformers].
> * [BERT] was a breakthrough performer in NLP when it had come out in 2018.
> * The idea of <strong>masking</strong> was a breath of fresh air, inspiring other techniques like [SpecAugment] used in speech processing.
> * One of the ideas behind Random masking is that the context(self-attention calculations) can be bidirectional. This is one of the major reasons for [BERT] to outperform [GPT] which uses causal masking.

<a name="BERT:A Pretrained Tranformers">
{%
    include image.html
    src="/img/mwprocs/nlpmp1.png"
    caption="figure-3: <strong>BERT:A Pretrained Tranformers</strong>"
    hight="110%"
    width="110%"
%}    

> * The recent success of transfer learning was ignited in 2018 by <strong>[GPT]</strong>, <strong>[ULMFiT]</strong>, <strong>[ELMo]</strong>, and <strong>[BERT]</strong>, and 2019 saw the development of a huge diversity of new methods like <strong>[XLNet]</strong>, <strong>[RoBERTa]</strong>, <strong>[ALBERT]</strong>, <strong>[Reformer]</strong>, and <strong>[MT-DNN]</strong>.
> * The rate of progress in the field has made it difficult to evaluate which improvements are most meaningful and how effective they are when combined.
> * Most <strong>pre-trained models drop either encoder or decoder from the transformer stack</strong>. <strong>[BERT] drops the decoder</strong> and <strong>[GPT] drops the encoder</strong>.
> * However, our experiments have shown that a complete stack <strong>(encoder-decoder) generally out performs a decoder-only or an encoder-only stack</strong>. <strong>[T5]</strong> from google also supports a similar architecture for generally better results.
> * <strong>The above transformer networks are called pretrained networks</strong>. BERT, GPT, T5 are examples of pretrained networks.


<a name="Fine tuning pre-trained networks">
#### Fine tuning pre-trained networks

Maybe it is time for another quick analogy. Let's say that I need support staff to handle queries from our clients. When we do hire the support staff we assume a <strong>basic knowledge of conversational languages and current affairs</strong>. Post hiring however, we need to <strong>train them on our specific processes</strong>. For e.g. if a particular issue has been solved for another client, this information is likely to be found in JIRA, LOGs, mailtrail e.t.c.    
The <strong>pre-trained transformer</strong> of the previous section is analogous to <strong>basic knowledge of conversational languages</strong>.
The <strong>fine-tuned transformer</strong> is analogous to <strong>post hiring training specific to our processes</strong>. The fine-tuning takes for granted pre-training and does not have to start from scratch. Also, the flexibility to train the interns for different specific tasks.


{%
    include image.html
    src="/img/mwprocs/finetune.png"
    caption="figure-4: <strong>BERT:A Fine tuning example</strong>"
    hight="110%"
    width="110%"
%}    

<a name="Artificial Intelligence model based proactive and predictive descision making">
#### Artificial Intelligence model based proactive and predictive descision making

This journey has been exhausting but fruitful. Finally, We treat the sequence of events captured by a tracer similar to a sequence of words in a sentence in NLP. Apart from that, from an Artificial Intelligence perspective, the setup is nearly identical.

> * As discussed in the previous section, our transformer setup is asymmetric. We have more encoders than decoders.
> * The reason being, we wanted to exploit encoder-decoder attention, without paying the price of a complete decoder stack. I believe [T5] has a similar setup. It is a good trade-off in my opinion.

{%
    include image.html
    src="/img/mwprocs/asymmetrictransformer2.png"
    caption="figure-5: <strong>Custom Transformer setup</strong>"
    hight="110%"
    width="110%"
%}    

> * The pre-training is standard. We have been doing performance work for quite some time. So we have lots of data. And the great thing about pre-training is it needs to be done off-line. This is the beauty of [Transfer Learning].
> * <strong>Elapsed time is the implicit time signal</strong> and <strong>no explicit positional encoding is applied</strong> unlike NLP.
> * <strong>The only possible trap is running pre-training with less data that there are chances of overfit.</strong>


{%
    include image.html
    src="/img/mwprocs/asymmetrictransformer3.png"
    caption="figure-6: <strong>System event sequence: Pretraining</strong>"
    hight="110%"
    width="110%"
%}    

<a name="The Final Cut">
#### The Final Cut

Regardless of the fine-tuning process, the pre-trained checkpoint remains the same. The fine tuning process itself can have many variations. The variations may be in terms of the goal, regression or classification with both having their pros and cons. I'll illustrate a regression version here.

> * The idea here is to select a state based on estimated idle time.
> * The [target residency] is the minimum time the hardware must spend in the given state, including the time needed to enter it (which may be substantial), in order to save more energy than it would save by entering one of the shallower idle states instead.

{%
    include image.html
    src="/img/mwprocs/asymmetrictransformer5.png"
    caption="figure-7: <strong>System event sequence: Fine-tuning for regression</strong>"
    hight="110%"
    width="110%"
%}   

<a name="Summary">
### Summary
In summary, <strong>this is the bird’s-eye view of the working of our system</strong>. We kept our focus on the core CPU for now but we plan to extend it to other components of the system. The Artificial Intelligence bit is quite interesting and we were helped a great deal by work done in the past on NLP. <strong>To the best of our knowledge, this has not been attempted before</strong>.
Transformers are not the only model we have tried. We tried LSTMs with very encouraging results. However, as the data grew bigger, and with an inherent lack of parallelism in training it was a tad bit slow. Inferencing was OK though. Last but not least, Deep Reinforcement Learning is another approach that we are currently integrating. We will post results in time to come.
Last year has been phenomenal in terms of Artificial Intelligence in general and Natural Language in Particular. But many of these models can be used in cross-discipline domains. They are just waiting to be tried out.

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
[an example]: MeditatingProcessor-1#c-state transitions
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
[target residency]: MeditatingProcessor-2#State of initialized device through sysfs:Residency States
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
[Glossary of terms]: MeditatingProcessor-3#Glossary of terms
[figure-8]: MeditatingProcessor-3#figure-8
[figure-9]: MeditatingProcessor-3#figure-9

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


<!-- NMT LSTM -->
[NMT]: /tags/#NMT
[Attention]: /tags/#Attention
[LSTM series]: /tags/#LSTM


<!--External-->

[Intel 8th and 9th datasheeet manual]: [https://www.intel.com/content/dam/www/public/us/en/documents/datasheets/8th-gen-core-family-datasheet-vol-1.pdf]
[Domino]: https://en.wikipedia.org/wiki/Domino_effect
[coordinated omission]: https://www.azul.com/files/HowNotToMeasureLatency_LLSummit_NYC_12Nov2013.pdf
[here is a great reference to it]: https://www.azul.com/files/HowNotToMeasureLatency_LLSummit_NYC_12Nov2013.pdf
[hwloc lstopo tool]: [https://linux.die.net/man/1/lstopo]
[die]: https://en.wikipedia.org/wiki/Die_(integrated_circuit)
[SpecAugment]: https://arxiv.org/abs/1904.08779
[BERT]: https://ai.googleblog.com/2018/11/open-sourcing-bert-state-of-art-pre.html
[GPT]: https://s3-us-west-2.amazonaws.com/openai-assets/research-covers/language-unsupervised/language_understanding_paper.pdf
[ULMFiT]: https://arxiv.org/abs/1801.06146
[ELMo]: https://arxiv.org/abs/1802.05365
[XLNet]: https://arxiv.org/abs/1906.08237
[RoBERTa]: https://arxiv.org/abs/1907.11692
[ALBERT]: https://ai.googleblog.com/2019/12/albert-lite-bert-for-self-supervised.html
[Reformer]: https://ai.googleblog.com/2020/01/reformer-efficient-transformer.html
[MT-DNN]: https://arxiv.org/abs/1901.11504
[Transfer Learning]: https://ruder.io/transfer-learning/
[T5]: https://ai.googleblog.com/2020/02/exploring-transfer-learning-with-t5.html
[Transformers]: https://en.wikipedia.org/wiki/Transformer_(machine_learning_model)
