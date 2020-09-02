---
layout: post
title:  "Meditating with microprocessors Series: Part-3: Trading off power for UltraLowLatency"
excerpt: "The current article is part of a bigger <strong><a href='/tags/#Meditating-with-microprocessors'>series</a></strong> titled <strong><a href='/tags/#Meditating-with-microprocessors'>Meditating-with-microprocessors</a></strong>,in which I demonstrate the use of <strong>Artificial Intelligence</strong> to tune <strong>microprocessors</strong> for <strong>Ultra Low Latency</strong> and <strong>Realtime</strong> loads. There are 5 parts to the series <strong><a href='/articles/2020-07/MeditatingProcessor-1'>Artificial Intelligence based Hardware(Microprocessor) tuning: Implementing a very simple idea(part-1) </a></strong>, <strong><a href='/articles/2020-07/MeditatingProcessor-2'>A crashcourse in Microarchitecture and Linux CPUIDLE interface(part-2)</a></strong>, <strong><a href='/articles/2020-07/MeditatingProcessor-3'>Trading off power for UltraLowLatency(part-3)</a></strong>, <strong><a href='/articles/2020-07/MeditatingProcessor-4'>Artificial Intelligence guided Predictive MicroProcessor tuning(part-4)</a></strong>, <strong><a href='/articles/2020-07/MeditatingProcessor-5'>Appendix:Tools of the trade(part-5) </a></strong>. In the current article, I present irrefutable evidence that if the processor is configured correctly with a goal in mind(for e.g. UltraLowLatency), the OS jitter caused by the processor can nearly be eliminated. The system can be much more predictable. Furthermore, It has a huge impact on the latency of the system for good. A <strong>substantial</strong> improvement in latency due to configuration on the <strong>Core</strong>, but beyond substantial due to  <strong>Uncore</strong>. <strong>The improvement due to Uncore is to be expected because there is a whole lot more circuitry on the Uncore</strong>. However, this is a trade off that comes at the cost of expending more power."
mathjax: true
date:   2020-07-29 15:07:19
categories: [article]
tags: Artificial-Intelligence Deep-Learning LSTM NLP Microarchitecture UltraLowLatency RealTime Performance Meditating-with-microprocessors
image:
  feature: mwprocs/pcm-inf-graf2.png
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
The current article is part of a bigger <strong>[series][Meditating-with-microprocessors]</strong> titled <strong>[Meditating-with-microprocessors][Meditating-with-microprocessors]</strong>, in which I demonstrate the use of <strong>Artificial Intelligence</strong> to tune <strong>microprocessors</strong> for <strong>Ultra Low Latency</strong> and <strong>Realtime</strong> loads. There are 5 parts to the series, <strong>[Meditating with Microprocessors: An essentially simple idea(part-1)][Meditating-with-microprocessors Part-1]</strong> , <strong>[A crashcourse in Microarchitecture and Linux CPUIDLE interface(part-2)][Meditating-with-microprocessors Part-2]</strong>, <strong>[Trading off power for UltraLowLatency (part-3)][Meditating-with-microprocessors Part-3]</strong> , <strong>[Artificial Intelligence guided Predictive MicroProcessor tuning (part-4)][Meditating-with-microprocessors Part-4]</strong>, <strong>[Appendix:Tools of the trade (part-5)][Meditating-with-microprocessors Part-5]</strong>.
In the current article, I present irrefutable evidence that if the processor is configured correctly with a goal in mind(for e.g. UltraLowLatency), the OS jitter caused by the processor can nearly be eliminated. The system can be much more predictable. Furthermore, It has a huge impact on the latency of the system for good. A <strong>substantial</strong> improvement in latency due to configuration on the <strong>Core</strong>, but beyond substantial due to  <strong>Uncore</strong>. <strong>The improvement due to Uncore is to be expected because there is a whole lot more circuitry on the Uncore</strong>. However, this is a trade off that comes at the cost of expending more power.  

# <a name="Core and Uncore:Core"></a>
#### Core and Uncore:Core
We'll look at [Core] and [Uncore] again but this time from the perspective of the power interface. How much
Power can be saved and how much is the tradeoff in terms of latency.

> * On the core we have L1(data), L1(instruction), L2, Execution-units, and a whole lot of buffers. Maybe, you could quickly refresh the discussion on [Core] before you proceed.
> * On the uncore we have L3 cache, integrated memory controller, QuickPath Interconnect (QPI; for multi-socket communication), and an interconnect that tied it all together. In the Sandy Bridge generation over and above Sandy Bridge, PCIe was integrated into the CPU uncore.
> * The complete package is called a package(core + uncore). <strong>core + uncore=package</strong>

# <a name="Power"></a>
#### Power
Active power of the CPU is when executing logic it transitions between ‘0’ and ‘1’. The transistors are charging and discharging to represent states. There are 2 primary components
> * The power that runs the clock of the CPU
> * The power consumed by execution of logic

There are 2 ways to save power.

> * [Turn things off][Power:Turn things off]
> * [Turn things down][Power:Turn things down]

# <a name="Power:Turn things off"></a>
#### Power:Turn things off
Within the CPU there are 2 ways of doing this. <strong>(1)</strong> Clock gating stops the clock, saving active power. The latency incurred is approximately 10ns-1μs. <strong>(2)</strong> Power gating removes all power, saving both leakage and active power. The latency incurred is approximately 1μs-10μs.

# <a name="Power:c-states"></a>
#### Power:c-states
Core c-states and package(core+uncore) c-states. Abbreviated as cc-states(c-states) and pc-states. So CPU designers have developed ways for the processor to go into a lower-power state when there is nothing for it to do. Typically, when put into this state, the CPU will stop clocks and power down part or all of its circuitry until the next interrupt arrives. Intel servers commonly target a worst case of about 40 microseconds in order to restore the path to main memory for PCIe devices.

{%
    include image.html
    src="/img/mwprocs/ccpc.png"
    caption="figure-1: <strong>core c-states and package c-states</strong>"
    hight="110%"
    width="110%"
%}

# <a name="Power:Tuned"></a>
#### Power:Tuned
Tuned-adm, developed by Redhat, allows tuning of certain settings in the operating system to suit certain use-cases. There are many predefined profiles to cater to common use cases. Custom profiles can also be defined from scratch or built on existing profiles. Few example profiles are “Low latency for storage and network”,  “high throughput for storage and network” etc.

> * List of profiler.
> * Active Profile is <strong>spindown-disk</strong>.
> * <strong>"/dev/cpu_dma_latency"</strong> is the response time for the cpu in the current configuration.

{%
    include image.html
    src="/img/mwprocs/tuned1.png"
    caption="figure-2: <strong>Tuned:Active:Spindown-disk</strong>"
    hight="110%"
    width="110%"
%}

> * Active Profile is <strong>latency-performance</strong>.
> * <strong>"/dev/cpu_dma_latency"</strong> is the response time for the cpu in the current configuration.

{%
    include image.html
    src="/img/mwprocs/tuned2.png"
    caption="figure-3: <strong>Tuned:Active:latency-performance</strong>"
    hight="110%"
    width="110%"
%}

# <a name="Power:Tuned:c-states requests"></a>
#### Power:Tuned:c-states requests
But does this change have an impact on c-state request. Measurements are done using an excellent tool called [turbostat].

> * Active Profile is <strong>spindown-disk</strong>.
> * <strong>c-state residency</strong>.

{%
    include image.html
    src="/img/mwprocs/tuned-cstate1.png"
    caption="figure-4: <strong>Tuned:Active:Spindown-disk</strong>"
    hight="110%"
    width="110%"
%}

> * Active Profile is <strong>spindown-disk</strong>.
> * <strong>c-state residency</strong>.
> * <strong>c-state residency: C10 has the highest number of requests. It is the lowest powersave mode or borderline Mokshaa.</strong>.
> * <strong>[Glossary of terms]</strong>

{%
    include image.html
    src="/img/mwprocs/tuned-cstate2.png"
    caption="figure-5: <strong>Tuned:Active:Spindown-disk</strong>"
    hight="110%"
    width="110%"
%}

> * Active Profile is <strong>latency-performance</strong>.
> * <strong>c-state residency</strong>.

{%
    include image.html
    src="/img/mwprocs/tuned-cstate1.png"
    caption="figure-6: <strong>Tuned:Active:latency-performance</strong>"
    hight="110%"
    width="110%"
%}

# <a name="Power:Tuned:Hardware State Residency"></a>
#### Power:Tuned:Hardware State Residency
The hardware c-state are requests made by the operating system on behalf of software control policies. However, not all of them may be granted by hardware. Part of the reason for this is 2 logical processors ( Hyper Threads on the same physical core ) may not agree upon the c-states requested. The lower(C1 for e.g.) of the 2 c-state may be granted and the higher one(C6) would report as not granted. The core after all is the same unit. And unlike quantum physics, there is no duality here.

The important thing is how do we measure hardware state residency. Well, there are <strong>MSRs (model-specific registers )</strong> on the CPU that can be queried for this.

> * <strong>MSRs (model-specific registers )</strong> introduced in Sandy Bridge carried through to later microarchitecture pipelines.
> * <strong>Some register for measuring these values may change.</strong>.
> * <strong>[Intel's documentation] is the authentic source here. But, it is incredibly difficult to read. It is as if Intel's employee were given a raise to make it "impossible to comprehend" kind of document.</strong>
> * <strong>Prior to [Cannonlake] there was no MSR to measure C1 and had to be measure via softawre means. Post [Cannonlake] it can be measured 0x660H register.</strong>

{%
    include image.html
    src="/img/mwprocs/tuned-cstate-hsr1.png"
    caption="figure-7: <strong>Tuned:Active:Hardware-State-Residency</strong>"
    hight="110%"
    width="110%"
%}

> * Active Profile is <strong>spindown-disk</strong>.
> * <strong>Hardware State Residency</strong>.

# <a name="figure-8"></a>
{%
    include image.html
    src="/img/mwprocs/tuned-cstate-hsr2.png"
    caption="figure-8: <strong>Tuned:Active:spindown-disk:Tuned:Active::spindown-disk:Hardware-State-Residency</strong>"
    hight="110%"
    width="110%"
%}

> * Active Profile is <strong>latency-performance</strong>.
> * <strong>Hardware State Residency</strong>.

# <a name="figure-9"></a>
{%
    include image.html
    src="/img/mwprocs/tuned-cstate-hsr3.png"
    caption="figure-9: <strong>Tuned:Active:spindown-disk:Tuned:Active::latency-performance:Hardware-State-Residency</strong>"
    hight="110%"
    width="110%"
%}

# <a name="Power:Tuned:Influx:Grafana:c-states"></a>
#### Power:Tuned:Influx:Grafana:c-states

This is visualizing <strong>c-states transitions</strong> as a time series. We looked at this in the [CPUIDLE subsystem:Gathering and undertanding latency data] section. The processed CSV files are then moved to influxdb and visualized with grafana.

> * <strong>Tuned Low latency setting applied midway through the 30 second [ftrace] recording</strong>.

{%
    include image.html
    src="/img/mwprocs/influx-graf.png"
    caption="figure-10: <strong>Tuned:ftrace-influx-grafana</strong>"
    hight="110%"
    width="110%"
%}

> * <strong>This is a recording with [PCM] from Intel now open-sourced.</strong>.
> * <strong>It has many dashboards showing various metrics. I am showing a couple of relevant ones like c-state Hardware Residency and L2/L3 Misses</strong>.
> * <strong>These experiments have been done on my laptop with coffeelake client.(I have not been going to office because of covid19. The point is, that the difference is more stark on Xeon CPUs).</strong>

{%
    include image.html
    src="/img/mwprocs/pcm-inf-graf1.png"
    caption="figure-11: <strong>Tuned:PCM-influx-grafana</strong>"
    hight="110%"
    width="110%"
%}

> * <strong>With Low latency setting applied.</strong>.

{%
    include image.html
    src="/img/mwprocs/pcm-inf-graf2.png"
    caption="figure-12: <strong>Tuned:PCM-influx-grafana</strong>"
    hight="110%"
    width="110%"
%}


# <a name="Power:Tuned:Measuring Latency"></a>
#### Power:Tuned:Measuring Latency

Measuring latency is relatively easy. Just so that we are on the same page. Latency is the elapsed time between when an event should occur and it actually occurs. What is relatively more difficult is apportioning it to a subsystem. In modern computer systems, how or what do we apportion the measured latency to? Is it the Hardware, Operating System, or the Application causing it. And yet, there are underrated tools(ftrace) that are exactly for that purpose. But mind you, ftrace is a low-level tool, and post-processing of data may be required.

# <a name="Power:Tuned:Hardware Latency"></a>
#### Power:Tuned:Hardware Latency

This is [ftrace]'s hwlat tracer from the ftrace collection of tracers. What is does is simple yet elegant. It hogs a CPU in a busy loop taking timestamps for a configured amount of time. It does so with interrupts disabled. It then reports the gaps in the timestamp. Not all code paths are covered obviously but give us a fair idea of hardware latency.

> * Active Profile is <strong>spindown-disk</strong>.
> * <strong>Hardware Latency of the system</strong>.

{%
    include image.html
    src="/img/mwprocs/tuned-cstate-hl1.png"
    caption="figure-13: <strong>Tuned:Active:spindown-disk:Tuned:Active:spindown-disk:Hardware Latency</strong>"
    hight="110%"
    width="110%"
%}

> * Active Profile is <strong>latency-performance</strong>.
> * <strong>Hardware Latency of the system</strong>.

{%
    include image.html
    src="/img/mwprocs/tuned-cstate-hl2.png"
    caption="figure-14: <strong>Tuned:Active:spindown-disk:Tuned:Active:latency-performance:Hardware Latency</strong>"
    hight="110%"
    width="110%"
%}

# <a name="Power:Tuned:Wakeup Latency"></a>
#### Power:Tuned:Wakeup Latency

Wakeup tracer from [ftrace] traces the elapsed time from a task being woken up to actually waking up. There are too many variables here, for e.g. other tasks in the queue. In order for this to make sense, one has to look at this relatively and pick the latency numbers for the same task.  

> * Active Profile is <strong>spindown-disk</strong>.
> * <strong>Wakeup Latency of the system</strong>.
> * <strong>Notice the latency of "\__schedule", you can interpret that as the latency of the OS scheduler.</strong>.

{%
    include image.html
    src="/img/mwprocs/tuned-cstate-wul1.png"
    caption="figure-15: <strong>Tuned:Active:spindown-disk:Tuned:Active:spindown-disk:Wakeup Latency</strong>"
    hight="110%"
    width="110%"
%}

> * Active Profile is <strong>latency-performance</strong>.
> * <strong>Wakeup Latency of the system</strong>.
> * <strong>Notice the latency of "\__schedule", you can interpret that as the latency of the OS scheduler.</strong>.

{%
    include image.html
    src="/img/mwprocs/tuned-cstate-wul2.png"
    caption="figure-16: <strong>Tuned:Active:spindown-disk:Tuned:Active:latency-performance:Wakeup Latency</strong>"
    hight="110%"
    width="110%"
%}

# <a name="Power:Tuned:Influx:Grafana:latency"></a>
#### Power:Tuned:Influx:Grafana:latency

This is visualizing <strong>latency</strong> as a time series. We looked at this in the [CPUIDLE subsystem:Gathering and undertanding latency data] section. The processed CSV files are then moved to influxdb and visualized with grafana.

> * <strong>Tuned Low latency setting applied midway through the 22 second [ftrace] recording</strong>.

{%
    include image.html
    src="/img/mwprocs/influx-graf2.png"
    caption="figure-17: <strong>Tuned:ftrace-influx-grafana:latency</strong>"
    hight="110%"
    width="110%"
%}


# <a name="Power:PMQOS"></a>
#### Power:PMQOS

This interface provides a kernel and user mode interface for registering performance expectations by drivers, subsystems and user space applications on one of the parameters. Two different PM QoS(Power Management Quality of Service) frameworks are available:
1. PM QoS classes for <strong>cpu_dma_latency, network_latency, network_throughput, memory_bandwidth.</strong>
2. The per-device PM QoS framework provides the API to manage the <strong>per-device latency constraints and PM QoS flags</strong>.

Here are the benefits
1. Provides flexibility, variety and degree of options
2. Uses available tools and infrastructure
3. Scalable and can be easily included early in application design

Let me simplify this. Refer back to [figure-8] and [figure-9]. You’ll notice that the settings take effect for all the cores in the system. “pm_qos” is a much more fine-grained interface. Now let’s look at figure-10 below especially virtual cores 3 and 9.

> * <strong>Hardware-State-Residency with "pm_qos"</strong>.
> * <strong>Notice the latency of "\__schedule", you can interpret that as the latency of the OS scheduler.</strong>.
> * <strong>Allows user to specify a resume latency constraint</strong>
> * <strong>CPU idle governor limits C states with exit latencies lower than the constraint</strong>
> * <strong>Application can change constraint at different phases</strong>
> * <strong>C states can be controlled in each core independently</strong>

{%
    include image.html
    src="/img/mwprocs/tuned-cstate-pmqos.png"
    caption="figure-18: <strong>pm_qos:Hardware-State-Residency</strong>"
    hight="110%"
    width="110%"
%}

# <a name="Power:Turn things down"></a>
#### Power:Turn things down
Changing the frequency and voltage of a subset of the system: Traditionally these states have been focused on the cores, but changing the frequencies of other components of the CPU is also possible (such as a shared L3 cache). Execution can continue at varied performance and power levels when using <strong>P-states</strong>. Voltage/frequency scaling If high frequency is not required, it can be dynamically reduced in order to achieve a lower power level. When frequency is reduced, it may also be possible to reduce the voltage. Voltage/frequency(vret) required to maintain state in a circuit can be lower than the voltage required to operate that circuit.

# <a name="Power:Turn things down:P-states:Hardware Latency"></a>
#### Power:Turn things down:P-states:Hardware Latency
> * Frequency can be changed using tools like [likwid]

{%
    include image.html
    src="/img/mwprocs/pstate-lw1.png"
    caption="figure-19: <strong>likwid-setfrequency</strong>"
    hight="110%"
    width="110%"
%}

> * <strong>Low Frequency</strong>.
> * <strong>Hardware Latency of the system</strong>.

{%
    include image.html
    src="/img/mwprocs/pstate-lw2.png"
    caption="figure-20: <strong>Hardware Latency: Low frequency</strong>"
    hight="110%"
    width="110%"
%}

> * <strong>High Frequency</strong>.
> * <strong>Hardware Latency of the system</strong>.

{%
    include image.html
    src="/img/mwprocs/pstate-lw3.png"
    caption="figure-21: <strong>Hardware Latency: High frequency</strong>"
    hight="110%"
    width="110%"
%}

# <a name="Core and Uncore:Uncore"></a>
#### Core and Uncore:Uncore
We'll look at [Core] and [Uncore] again but this time from the perspective of the power interface. How much
Power can be saved and how much is the tradeoff in terms of latency.

> * On the uncore we have L3 cache, integrated memory controller, QuickPath Interconnect (QPI; for multi-socket communication), and an interconnect that tied it all together. In the Sandy Bridge generation over and above Sandy Bridge, PCIe was integrated into the CPU uncore.
> * However, just bu looking at the size and number or units on the [Uncore], the potential of latency improvements here are huge

# <a name="Core and Uncore:Uncore:montioring and Tuning"></a>
#### Core and Uncore:Uncore:montioring and Tuning
Unlike core performance monitoring, the performance monitoring architecture in the uncore is not standardized across all generations and product lines. A common architecture is used on Xeon E5/E7 products starting in the Sandy Bridge generation. Very few <strong>uncore</strong> performance monitoring capabilities have been productized on other Intel products. We read from and write to the <strong>MSRs</strong> directly. This is one of the reasons why less importance is paid to <strong>uncore</strong>. The interface to <strong>uncore</strong> is not standard. But good news is on the horizon, the Intel's UNCORE frequency driver may be part of mainline kernel as soon as <strong>kernel version 5.6</strong>. Till such time <strong>MSRs</strong> are our only hope as we are still on <strong>kernel version 4.15</strong>. But we are itching to get on to 5 series.

The <strong>uncore</strong> makes a far bigger difference because of it's sheer size.  

# <a name="Core and Uncore:Uncore:montioring and Tuning:Hardware Latency"></a>
#### Core and Uncore:Uncore:montioring and Tuning:Hardware Latency

> * <strong>Low Frequency</strong>.
> * <strong>Hardware Latency of the system</strong>.

{%
    include image.html
    src="/img/mwprocs/uncore1.png"
    caption="figure-22: <strong>Uncore:Hardware Latency:Low frequency</strong>"
    hight="110%"
    width="110%"
%}

> * <strong>High Frequency</strong>.
> * <strong>Hardware Latency of the system</strong>.

{%
    include image.html
    src="/img/mwprocs/uncore2.png"
    caption="figure-23: <strong>Uncore:Hardware Latency:High frequency</strong>"
    hight="110%"
    width="110%"
%}

# <a name="Core and Uncore:Uncore:montioring and Tuning:Wakeup Latency"></a>
#### Core and Uncore:Uncore:montioring and Tuning:Wakeup Latency

> * <strong>Low Frequency</strong>.
> * <strong>Wakeup Latency of the system</strong>.

{%
    include image.html
    src="/img/mwprocs/uncore3.png"
    caption="figure-24: <strong>Uncore:Wakeup Latency:Low frequency</strong>"
    hight="110%"
    width="110%"
%}

> * <strong>High Frequency</strong>.
> * <strong>Wakeup Latency of the system</strong>.

{%
    include image.html
    src="/img/mwprocs/uncore4.png"
    caption="figure-25: <strong>Uncore:Wakeup Latency:High frequency</strong>"
    hight="110%"
    width="110%"
%}

# <a name="Core and Uncore:How much power can be saved"></a>
#### Core and Uncore:How much power can be saved

But what about power are we saving? The whole premise here is that once the load has passed over, post-configure the setting back to power save settings again based on load predictions.

> * This has been measured in quad socket system using [PMC].

{%
    include image.html
    src="/img/mwprocs/power.png"
    caption="figure-26: <strong>Power Save</strong>"
    hight="110%"
    width="110%"
%}



<a name="Summary">
### Summary
Based on the experiments that I ran with my team, our conclusions are simple. If we consider latency, the OS jitter caused by the microprocessor per se can be almost completely eliminated. I only presented the evidence related to c-states and p-states. But this must be combined with <strong>isolcpus, affinity, irqaffinity, nohz_full, rcu_nocb</strong>, etc to have a holistic approach to eliminating jitter. A similar approach can be used for storage and network devices.  
We can meditate with the whole computer system, but that would be a much bigger article series than what it already is. Last but not least, latency is the goal for this article series and the most crucial type of load we handle. But it is not the only one, it could very well be throughput or something more custom.

<a name="Glossary of terms">
### Glossary of terms
1. <strong>PLL:</strong>A phase locked loop, or PLL, is a circuit that is used to generate a stable frequency that has a specific mathematical relationship to some reference frequenc
2. <strong>VCCSA:</strong> Starting with the second-generation Core i processors (“Sandy Bridge”), the VTT voltage was renamed to VCCSA, and is called “system agent(SA).”It feeds the integrated PCI Express controller, memory controller, and display engine (i.e., the “2D” part of the graphics engine)
3. <strong>VCCIO:</strong> Available starting with the second-generation Core i CPUs (“Sandy Bridge”), this voltage is used for feeding all input/output (I/O) pins of the CPU, except memory-related pins.
4. <strong>GT:</strong> Integrated Grafics unit
5. <strong>IA:</strong> Core CPU
6. <strong>PS4:</strong> Low power mode
7. <strong>VR:</strong>  Voltage regulators

### References
1. <a href='https://software.intel.com/content/www/us/en/develop/articles/intel-sdm.html'><strong>Intels documenation: (The source and comprehensive, but difficult to read)</strong></a>
2. <a href='https://www.agner.org/optimize/'><strong>Agner Fog: (He benchmarks microprocessors using forward and reverse engineering techniques. My bible.)</strong></a>

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


<!--External-->

[Intel 8th and 9th datasheeet manual]: [https://www.intel.com/content/dam/www/public/us/en/documents/datasheets/8th-gen-core-family-datasheet-vol-1.pdf]
[PCM]: [https://github.com/opcm/pcm]
[Domino]: https://en.wikipedia.org/wiki/Domino_effect
[coordinated omission]: https://www.azul.com/files/HowNotToMeasureLatency_LLSummit_NYC_12Nov2013.pdf
[here is a great reference to it]: https://www.azul.com/files/HowNotToMeasureLatency_LLSummit_NYC_12Nov2013.pdf
[hwloc lstopo tool]: [https://linux.die.net/man/1/lstopo]
[die]: https://en.wikipedia.org/wiki/Die_(integrated_circuit)
[turbostat]: https://www.linux.org/docs/man8/turbostat.html
[Intel's documentation]: https://software.intel.com/content/www/us/en/develop/articles/intel-sdm.html
[Cannonlake]: https://en.wikipedia.org/wiki/Cannon_Lake_(microarchitecture)
[likwid]: https://github.com/RRZE-HPC/likwid
