---
layout: post
title:  "Artificial Intelligence based chip design: Chip Design"
excerpt: "This is a departure from my usual tech blog. In the <strong><a href='/tags/#Artificial-Intelligence-based-chip-design'>current series</a></strong> titled <strong><a href='/tags/#Artificial-Intelligence-based-chip-design'>Artificial-Intelligence-based-chip-design</a></strong>, I/we present the use of <strong>Artificial Intelligence</strong> to <strong>design FPGA chips</strong> for <strong>Ultra-Low-Latency</strong> based speech transformers. This showcases our products as well as our skills in the respective domains. This started as an experiment to lower latency for a seq-to-seq LSTM based speech synthesis system. Post a thorough analysis, we broke it down into <strong>1. mapping the matrix multiplication of deep learning transformers into an FPGA chip</strong>(<strong><a href='articles/2021-07/Transformers-On-Chip'>Transformers On Chip(part-1)</a></strong>) and <strong>2. co-designing the FPGA chip for the appropriate workload</strong>(<strong><a href='articles/2021-07/ChipDesign'>ChipDesign(part-2)</a></strong>). In part-2 we treat the resources on FPGA chip, Workload, other design parameters as <strong>design variables</strong>. Finally, we feed the design variables into the Deep Reinforcement Learning agent to learn. Post learning the expectation from Deep Reinforcement Learning agent being the <strong>optimal placement</strong> of blocks to maximize certain goals like latency, etc. The Deep Reinforcement Learning Algorithm is supposed to figure out a balance that <strong>speeds up computation for tolerable accuracy losses(if at all)</strong>.To use a boxing term, <strong>pound for pound</strong>, the <strong>same hardware micro-designed by a Deep Reinforcement Learning Agent for carrying a specific load</strong>."
mathjax: true
date:   2021-07-04 15:07:19
categories: [article]
tags: Artificial-Intelligence Deep-Learning LSTM NLP Microarchitecture UltraLowLatency RealTime Performance Meditating-with-microprocessors Artificial-Intelligence-based-chip-design Transformers-On-chips ChipDesign
image:
  feature: chipdesign/chipdesignai.png
comments: true
---

### [Artificial Intelligence based chip design][Artificial Intelligence based chip design]
1. [Artificial Intelligence based chip design: Transformers on Chip][Transformers On Chip] <strong>([part-1][Transformers On Chip])</strong>
    * [Introduction][Introduction1]
    * [STT(Speech-to-Text)]
    * [TTS(Text-to-Speech)]
    * [FPGAs as modern digital ShapeShifters]
    * [Optimizing on Hardware Accelerator]
    * [Optimizing on Hardware Accelerator(MAC Style)]
    * [Optimizing on Hardware Accelerator(Systolic Array Style)]
    * [Variables in the design]
    * [Summary][Summary1]
2. [Artificial Intelligence based chip design: Chip Design][Chip Design] <strong>([part-2][Chip Design])<strong>
    * [Introduction][Introduction2]
    * [Chip Design: Layout, Locality and Resource Allocation]
    * [Deep Reinforcement Learning Agent]
    * [Our Rules of the Game][Our Rules of the Game]
    * [Summary]
    * [Future]

# <a name="Introduction"></a>
### Introduction
In part-2 we treat the resources on FPGA chips, Workload, other design parameters as design variables. Finally, we feed the <strong>design variables</strong> into the Deep Reinforcement Learning agent to learn, and then suggest <strong>optimal placement</strong> of blocks to maximize certain goals like latency, etc. This part describes a few of the hardware variables and how to <strong>co-express</strong> the <strong>software and hardware</strong> variables that share a complex relationship. This co-expression is vital. For example, <strong>reducing bit width costs accuracy but frees up hardware resources that could be used for compute or cache</strong>. The Deep Reinforcement Learning Algorithm is supposed to figure out a balance that <strong>speeds up computation for tolerable accuracy losses(if at all)</strong>. To use a boxing term, <strong>pound for pound</strong>, the <strong>same hardware micro-designed by a Deep Reinforcement Learning Agent for carrying a specific load</strong>.

# <a name="Chip Design: Layout, Locality and Resource Allocation"></a>
#### Chip Design: Layout, Locality and Resource Allocation
Although current FPGA accelerators have demonstrated better performance over generic processors for specific workloads, the <strong>accelerator design space</strong> has not been <strong>well exploited</strong>. One critical problem is that the <strong>computation throughput</strong> may not well match the memory bandwidth provided by FPGA platforms.

But the bigger issue is Hardware design or even certain elements in it are usually not a variable in application design. Hardware design is a bubble, and so is software design but these don't meet. There is a case to be made for <strong>hardware and software co-design</strong>. But a few more hardware-specific design variables first.
> * <strong>Matrix Sparsity(MS)</strong> is known to cost a lot. Checks can be built into the PE to reduce the overall cache requirement, but it comes at the cost of chip fabric space and end-to-end latency. It's a classic trade-off.
> * <strong>Reducing Bit Width(BW) costs accuracy</strong>
    * Recent developments point toward next-generation Deep Learning algorithms that exploit <strong>extremely compact data types (e.g., 1bit, 2bit, 4bit, 8bit, 12bit, etc.)</strong>.  
> * This is FPGAs’ backyard but was never done dynamically and as a whole with software in the loop.

# <a name="Deep Reinforcement Learning Agent"></a>
#### Deep Reinforcement Learning Agent

With so many <strong>design variables</strong> identified above, this essentially is a <strong>deep search</strong> problem. Many of these design variables can assume millions to billions of values and the combinatorial space is practically impossible to go through with an exhaustive search policy. Last but not least, the entire argument for Artificial Intelligence is generalization, which is the <strong>whole point of the Design Variables</strong>.

{%
    include image.html
    src="/img/chipdesign/chipdesign.png"
    caption="figure-1: <strong>For most part this is a regular transfomer with a convolution in between</strong>"
    hight="110%"
    width="110%"
%}

# <a name="Our Rules of the Game"></a>
#### Our Rules of the Game

Essentially these are the <strong>boundary conditions</strong> asserted by the design and/or inherent in the <strong>chip</strong>. Add to that the <strong>design variables</strong> and we have the complete rules of the game.

> * Express the constraints and composition of the microarchitecture as a <strong>[GAT(Graph Attention Network)]</strong>
    * Such complex arrangements that take locality, composition, arrangement, and other constraints into account are easier to express using GATs. Our current implementation is custom but for the next iteration, we’d like to use the [graph neural network].
> * Express the Resources and Constraints of the FPGA Chip as the "<strong>[Rules of the Game]</strong>(Alphago Zero learned the game of go from scratch, with the rules of GO as boundary constraints)"
    * As an example, the <strong>dark blue(DSP)</strong> and the <strong>bottle green (BRAM)</strong> are pre-arranged in columns right beside each other on the FPGA chip to aid lower latency and easier integration.
    * The <strong>logic fabric</strong>(FPGA fabric) can be used in <strong>compute</strong> or as <strong>RAM</strong>(LUTRAM). Both Altera/Intel and Xilinx have elected to make only <strong>half of their logic blocks LUT-RAM capable</strong> in their recent architectures. This is another “rule of the game”. Similar to <strong>[chinese chess]</strong>, where a piece is captured and reprogrammed rather than removed from the board. A little like saying if you are not logic you are ram. And the <strong>Deep Reinforcement Learning Agent is expected to learn it over multiple training iterations</strong>.
    * As a general rule, the <strong>design variables identified</strong> earlier and the Resources and Constraints of the FPGA Chip are  expressed as the "Rules of Game".
> * We generate combined embeddings that are fed into the <strong>Deep Reinforcement Learning Agent for Training</strong>.
> * The <strong>Deep Reinforcement Agent</strong> with multiple training iterations learns not just the placement of the tiles but also the relative sizing of caches and indeed the values of the <strong>design variables</strong> identified above.
    * It goes without saying that the prediction for most design variables more than satisfies the <strong>primary goal(latency)</strong> and shatters the <strong>secondary goal(energy) and tertiary goal(throughput)</strong>.
    * But the result is fascinating for a completely different reason. This is the beginning of a recipe for the future of chip design.

<a name="Summary2">
### Summary
What started as an experiment led to something as fascinating as this was something beyond expectation at the beginning. Now that it has, it can be extended to all kinds of FPGA chips and beyond. The idea of "AI Accelerator" is not new but <strong>placing it in the same design bubble as the AI software</strong> has advantages not exploited earlier to the best of my knowledge. This is how we exploit the limits of the hardware. Specialized, purpose-built chips are set to become commonplace, not at the cost of general-purpose chips though. Deep Reinforcement Learning has been a revelation. I feel in the chip design and similar domains this is a technique that is <strong>severely underexploited</strong>.

<a name="Future">
### Future
This is the first iteration, and the rush to the finish line hurried us up. The future looks mouthwatering and majorly because of the tech we had to leave out for this iteration. Here are a few.
> * The modern <strong>[FPGA ditches the traditional DSPs for tensor blocks]</strong> that are purpose-built for Deep Learning.

> * <strong>[Processing near memory]</strong> is moving compute to memory literally. Processing-in-memory architectures propose <strong>moving the compute into the memory that literally stores the weight matrix</strong>. Processing in memory architectures can also increase the memory bandwidth, as the number of weights that can be accessed in parallel is no longer limited by the memory interface.

### References
1. <a href='https://lwn.net/Articles/250967/'><strong>What every programmer should know about memory:</strong></a> <strong>(This is a definitive 9 part(the links for the rest of the parts are embedded in the first one.) article on how the hardware works and, how software and data structure design can exploit it. It had a huge impact on the way I thought and still do think about design. The article first came out in 2007 but it is still relevant which is proof that basics don't change very often.)</strong>


<!--Series-->
[Stillwaters]: https://www.stillwaters.ai
[Artificial Intelligence based chip design]: /tags/#Artificial-Intelligence-based-chip-design
[Transformers On Chip]: Transformers On Chip
[Chip Design]: ChipDesign

<!--Doc1-->
[Introduction1]: Transformers-On-Chip#Introduction
[STT(Speech-to-Text)]: Transformers-On-Chip#STT(Speech-to-Text)
[TTS(Text-to-Speech)]: Transformers-On-Chip#TTS(Text-to-Speech)
[FPGAs as modern digital ShapeShifters]: Transformers-On-Chip#FPGAs as modern digital ShapeShifters
[Optimizing on Hardware Accelerator]: Transformers-On-Chip#Optimizing on Hardware Accelerator
[Optimizing on Hardware Accelerator(MAC Style)]: Transformers-On-Chip#Optimizing on Hardware Accelerator(MAC Style)
[Optimizing on Hardware Accelerator(Systolic Array Style)]: Optimizing on Hardware Accelerator(Systolic Array Style)
[Variables in the design]: Transformers-On-Chip#Variables in the design
[Summary1]: Transformers-On-Chip#Summary1

<!--Doc2-->
[Introduction2]: ChipDesign#Introduction
[Chip Design: Layout, Locality and Resource Allocation]: ChipDesign#Chip Design: Layout, Locality and Resource Allocation
[Deep Reinforcement Learning Agent]: ChipDesign#Deep Reinforcement Learning Agent
[Our Rules of the Game]: ChipDesign#Our Rules of the Game
[Summary]: ChipDesign#Summary2
[Future]: ChipDesign#Future

<!--External references-->
[Conformer]: https://arxiv.org/abs/2005.08100
[Transformer]: https://ai.googleblog.com/2017/08/transformer-novel-neural-network.html
[Transformers]: https://ai.googleblog.com/2017/08/transformer-novel-neural-network.html
[(MHSA)Multi-headed-Self-Attention]: https://paperswithcode.com/method/multi-head-attention
[GAT(Graph Attention Network)]: https://arxiv.org/abs/1710.10903
[here is a great reference to it]: https://www.azul.com/files/HowNotToMeasureLatency_LLSummit_NYC_12Nov2013.pdf
[graph neural network]: https://github.com/google-research/google-research/tree/master/graph_embedding
[Rules of the Game]: https://deepmind.com/blog/article/alphago-zero-starting-scratch
[chinese chess]: https://en.wikipedia.org/wiki/Xiangqi
[FPGA ditches the traditional DSPs for tensor blocks]: https://www.hpcwire.com/2020/06/18/intel-debuts-stratix-10-nx-fpgas-targeting-ai-workloads/
[Processing near memory]: https://arxiv.org/abs/2012.03112
