---
layout: post
title:  "Artificial Intelligence based chip design: Transformers on Chip"
excerpt: "This is departure from my usual tech blog. In the <strong><a href='/tags/#Artificial-Intelligence-based-chip-design'>current series</a></strong> titled <strong><a href='/tags/#Artificial-Intelligence-based-chip-design'>Artificial-Intelligence-based-chip-design</a></strong>, I/we present the use of <strong>Artificial Intelligence</strong> to <strong>design FPGA chip</strong> for <strong>Ultra-Low-Latency</strong> based speech transformers. This showcases our products as well as our skills in the respective domains. This started as an experiment to lower latency for a seq-to-seq LSTM based speech synthesis system. Post a thorough analysis, we broke it down into <strong>1. mapping the matrix multiplication of deep learning transformers into an FPGA chip</strong>(<strong><a href='articles/2021-07/Transformers-On-Chip'>Transformers On Chip(part-1)</a></strong>) and <strong>2. co-designing the FPGA chip for the appropriate workload</strong>(<strong><a href='articles/2021-07/'>ChipDesign(part-2)</a></strong>). In part-1 we look specifically at resources available on an FPGA chip and a few different strategies of <strong>hardware mapping</strong> of the workload(matrix multiplication) on the above chip."
mathjax: true
date:   2021-07-03 15:07:19
categories: [article]
tags: Artificial-Intelligence Deep-Learning LSTM NLP Microarchitecture UltraLowLatency RealTime Performance Meditating-with-microprocessors Artificial-Intelligence-based-chip-design Transformers-On-chips ChipDesign
image:
  feature: chipdesign/transformersonchip.png
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
We have taken a traditional <strong>(TTS)Text-to-speech</strong> and <strong>(STT)Speech-to-text</strong> system and spun it on an <strong>FPGA microchip</strong>. In the pipeline of a traditional TTS system, there is a Seq-to-Seq LSTM layer rearing its “ugly latency head”. It takes approximately <strong>tens of thousands</strong> of iterations of this network to generate a second of audio.Sometimes this latency is not acceptable. Sometimes, this latency is not acceptable. Latency of these systems can be majorly attributed to the LSTMs, the technology of the day. LSTMs still have their uses, but for speech-based latency sensitive systems, they have limitations. LSTMs have played their part in the Deep Learning history and it is time for them to pass on the baton to the new King, The [Transformers].   

# <a name="STT(Speech-to-Text)"></a>
#### STT(Speech-to-Text)
Firstly, we took the STT based on <strong>[Conformer]</strong> (a <strong>[transformer]</strong> variant) and rewrote it using Trax.  
Why Trax? Well, it has an optimizing <strong>Just-In-Time compiler</strong> for Machine learning. We have <strong>extended that for FPGA</strong>. But more on that later.

It was a huge improvement on the LSTM variant. While there was a marked improvement, it wasn’t enough. And this was running on a 2020 GPU.

Transformers are essentially <strong>([(MHSA)Multi-headed-Self-Attention])</strong> and a <strong>(FFM)feed forward Module<strong>. Both these are matrix multiplications of grand and varying scale. The Conformer has an additional CM(Convolutional Module) which is a matrix filter operation.

{%
    include image.html
    src="/img/chipdesign/speechtotext.png"
    caption="figure-1: <strong>For most part this is a regular transfomer with a convolution in between</strong>"
    hight="110%"
    width="110%"
%}

# <a name="TTS(Text-to-Speech)"></a>
#### TTS(Text-to-Speech)

Secondly, we took the TTS based on the transformer and did exactly the same. Again it was a decent improvement on the LSTM version. But, not enough. Again, this was running on a 2020 GPU.

{%
    include image.html
    src="/img/chipdesign/texttospeech.png"
    caption="figure-2: <strong>This is a regular transformer with speech fed into the decoder, similar to NMT decoder</strong>"
    hight="110%"
    width="110%"
%}

GPUs are great at handling the “grand” part despite some transformers having billions to even trillions of parameters.

> * The “varying” part is proving extremely tricky for GPUs. Both MHSA and FFM have varying parallelism requirements(from one model to another).
> * Also, the standard precision leads to lots of wastage energy. GPU’s SIMD/SIMT lock-step style execution model makes matters worse. Lock step style execution is prohibitive in terms of energy wastage.  
> * Last but not least, the extra control structure on the GPU is not useful(wasteful) for matrix multiplication.
GPU is a general-purpose device and for something as specific as tuning a massive matrix multiplication it is found wanting. It is not a natural fit would be the right phrase.
> * All of the above contribute to tons of electricity, produce a lot of heat, and use fans for cooling.
    * And a large number of environments where we apply deep learning like speech devices, self-driving cars, production lines, etc, are not agreeable to it.
    * This also makes the maintenance and life expectancy of a GPU an issue.

# <a name="FPGAs as modern digital ShapeShifters"></a>
#### FPGAs as modern digital ShapeShifters

The CPUs and especially GPUs are nearing their transistor limit.
The world is moving toward specialized hardware to meet the exponentially growing demand for computers:

{%
    include image.html
    src="/img/chipdesign/fpga.png"
    caption="figure-3: <strong>FPGA fabric. While there are differences from one manufacturer to the next, The resources are similar.</strong>"
    hight="110%"
    width="110%"
%}

> * Field-programmable gate arrays (FPGAs) are reconfigurable computer chips that can be programmed to implement any digital hardware circuit.
> * FPGAs consist of an array of different types of programmable blocks (logic, IO, and others) that can be flexibly interconnected using prefabricated routing tracks with programmable switches between them.
> * The bit-level reconfigurability of FPGAs enables implementation of the exact hardware needed for each application (e.g. datapath bit-width, pipeline stages, number of parallel compute units, memory subsystem, etc.) instead of the fixed one-size-fits-all architecture of CPUs or GPUs.
  > * Consequently, they can achieve higher efficiency than CPUs or GPUs by implementing instruction-free streaming hardware
> * <strong>FPGA-RAM</strong>
    * An FPGA BRAM(BlockRAM) consists of an SRAM-based memory core, with additional peripheral circuitry to make them more configurable for multiple purposes and to connect them to the programmable routing FPGA vendors can add circuitry that allows designers to repurpose the LUTs(Look Up Tables) that form the logic fabric into additional RAM blocks.
> * <strong>DSP Blocks</strong>
    * With the prevalence of multipliers in FPGA designs from key application domains, and their lower area/delay/power efficiency when implemented in soft logic, they quickly became a candidate for hardening as dedicated circuits in FPGA architectures.

# <a name="Optimizing on Hardware Accelerator"></a>
#### Optimizing on Hardware Accelerator
The idea here is to hardware accelerate the huge matrix multiplication which is the core of Transformers. Given the size of the matrices in transformers ( a few billion parameters in 2020), there is practically no chance of them fitting inside a top end FPGA board, not for at least the next decade.
So the trick is to chop these huge matrices being multiplied into smaller tiles and reuse these tiles as much as possible before moving on to the next tile. One of the oldest tricks in a new Avatar.

<strong>There are 4 key points of FPGA Hardware Optimization</strong>
> * <strong>Play to FPGAs strength, no extra control logic</strong>
> * <strong>Minimize access to OFF-chip RAM by having multi level on chip buffer made of BRAM and LUTRAM</strong>
> * <strong>Maximize the compute parallelism (PE)ProcessingElements</strong>
> * <strong>And the most vital, balance the above 2.</strong>
    * <strong>PEs must be well fed and not starving</strong>
    * <strong>Balance cached data with streaming( weights(Mul1) may be cached more and the input will be streamed more(Mul2))</strong>

# <a name="Optimizing on Hardware Accelerator(MAC Style)"></a>
#### Optimizing on Hardware Accelerator(MAC Style)

The <strong>hardware <span style="color:#1950b6">ma</span><span style="color:#83f3f2">pp</span><span style="color:#f52a2a">ing</span></strong> is the most fabulous part carried out by our <strong><span style="color:#1950b6">ma</span><span style="color:#83f3f2">pp</span><span style="color:#f52a2a">er</span></strong>. It takes the code below and unrolls the MaxMul loop into the Hardware.

> * <strong>There are 2 Styles</strong>
    * <strong>MAC Style(Multiply Accumulator)</strong>
      * For a 4X4 matrix a 2X2 PE array as shown will take 24 cycles give or take.
      * For a 4X4 matrix a 4X4 PE array as shown will take 6 cycles give or take.
    * <strong>SA Style (Systolic Array)</strong>
      * For a 4X4 matrix a 2X2 PE array as shown will take 24 cycles give or take.
      * For a 4X4 matrix a 4X4 PE array as shown will take 6 cycles give or take.
> * We retrofit this <strong><span style="color:#1950b6">ma</span><span style="color:#83f3f2">pp</span><span style="color:#f52a2a">er</span></strong> to trax.


{%
    include image.html
    src="/img/chipdesign/macstyle.png"
    caption="figure-4: <strong>Hardware mapping MAC(Multiply Accumulator) Style</strong>"
    hight="110%"
    width="110%"
%}

<a name="Optimizing on Hardware Accelerator(Systolic Array Style)">
#### Optimizing on Hardware Accelerator(Systolic Array Style)

Same as above except the microarchitecture uses systolic array design with The PEs being pipelined. Follow the color coding especially the data movement(light and dark blue boxes and arrows) into the <strong>PEs</strong>(Processing Elements) and data forwarding among PEs. The <strong>NOC</strong>(Network On Chip) architecture which is not shown, is slightly different for both the <strong>MAC Style(Multiply Accumulator)</strong> and the <strong>SA Style (Systolic Array)</strong>.

{%
    include image.html
    src="/img/chipdesign/SAStyle.png"
    caption="figure-1: <strong>Hardware mapping SA(Systolic Array) Style</strong>"
    hight="110%"
    width="110%"
%}

<a name="Variables in the design">
#### Variables in the design

The SA Style (Systolic Array) works better for bigger tiles when it’s pipelines are fully fed for longer durations. But not too big as the NOC feeding the buffers has its own limitations. The <strong>size of the tile</strong> and indeed the <strong>style of the microarchitecture( MAC or SA)</strong> are one of the many design variables that need to be configured for best results. These in turn depend on quite a few more design variables, some of them are due to <strong>software constraints</strong> (Matrix size etc.) and others are <strong>hardware constraints</strong> (BRAM etc). Here are a few important design variables.    

> * The <strong>SGB</strong>(size of Global Buffer) based on available BRAM and LUTRAM.
    * More LUTRAM would mean bigger buffers, better reuse, but fewer PEs and lesser parallelism.
> * The <strong>LGB</strong>(Layout(Locality) of GLobal Buffer)
> * The size of the <strong>MT</strong> (Matrix Tile) and <strong>PET</strong>(PE Tile)
> * The size of <strong>RF</strong>(RegisterFile)
    * As a general rule, bigger/multi-level caching(SGB/NOC/RF are like L3/L2/L1) would better lookup performance but increase data replication and eat into logic space(PEs)
> * The layout of <strong>NOC</strong>(Network on Chip)   
> * <strong>With 100s of MBs to 1000 GBs of matrix sizes to choose from in modern transformers, myriad different Accelerator boards, Shape shifting configuration options on these boards, all the above variables to choose from, the configuration space is a serious problem of plenty.</strong>
    * A general configuration done by a domain expert(on Transformers and FPGA board) will give us <strong>decent improvement</strong> over a software only implementation but, <strong>nowhere near what the hardware is capable of<strong> unless the configurations are done by either Ramanujam or Tesla(Only these 2, others were normal).

<a name="Summary1">
### Summary

Migrating compute that requires extreme parallelism, or compute that can benefit from custom data path or precision should be moved to FPGA chips. Infact this is FPGA's backyard. The overall throughput can be a few hundred times better. Latency can be a bit tricky, but still can be a lot better with the flexibility that FPGA chips offer.  

### References
1. <a href='https://lwn.net/Articles/250967/'><strong>What every programmer should know about memory:</strong></a> <strong>(This is a definitive 9 part(the links for the rest of the parts are embedded in the first one.) article on how the hardware works and, how software and data structure design can exploit it. It had a huge impact on the way I thought and still do think about design. The article first came out in 2007 but it is still relevant which is proof that basics don't change very often. )</strong>


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
