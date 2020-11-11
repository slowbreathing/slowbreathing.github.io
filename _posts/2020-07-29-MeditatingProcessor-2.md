---
layout: post
title:  "Meditating with microprocessors Series: Part-2: A crashcourse in Microarchitecture and Linux CPUIDLE interface"
excerpt: "The current article is part of a bigger <strong><a href='/tags/#Meditating-with-microprocessors'>series</a></strong> titled <strong><a href='/tags/#Meditating-with-microprocessors'>Meditating-with-microprocessors</a></strong>,in which I demonstrate the use of <strong>Artificial Intelligence</strong> to tune <strong>microprocessors</strong> for <strong>Ultra Low Latency</strong> and <strong>Realtime</strong> loads. There are 5 parts to the series <strong><a href='/articles/2020-07/MeditatingProcessor-1'>Artificial Intelligence based Hardware(Microprocessor) tuning: Implementing a very simple idea(part-1) </a></strong>, <strong><a href='/articles/2020-07/MeditatingProcessor-2'>A crashcourse in Microarchitecture and Linux CPUIDLE interface(part-2)</a></strong>, <strong><a href='/articles/2020-07/MeditatingProcessor-3'>Trading off power for UltraLowLatency(part-3)</a></strong>, <strong><a href='/articles/2020-07/MeditatingProcessor-4'>Artificial Intelligence guided Predictive MicroProcessor tuning(part-4)</a></strong>, <strong><a href='/articles/2020-07/MeditatingProcessor-5'>Appendix:Tools of the trade(part-5) </a></strong>. In the current article I lay the  technical groundwork for later articles in the series to build on. The term's and technologies I'll be using later must be understood really well. So we look at 3 concepts primarily. Firstly, Architecture of a modern microprocessor in short ,really really short. Just enough to make a mental model of the microprocessor to work with. Secondly, How does does software(Linux) interface with the microprocessor. Again just enough to make sense of the data we gather form the microprocessor using various UltraLowLatency profilers and tracers. Thirdly, help a programmers like you to get started. "
mathjax: true
date:   2020-07-29 15:07:19
categories: [article]
tags: Artificial-Intelligence Deep-Learning LSTM NLP Microarchitecture UltraLowLatency RealTime Performance Meditating-with-microprocessors
image:
  feature: mwprocs/cpuidle-sub1.png
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
        * [Core and Uncore:Uncore:monitoring and Tuning]
        * [Core and Uncore:Uncore:monitoring and Tuning:Hardware Latency]
        * [Core and Uncore:Uncore:monitoring and Tuning:Wakeup Latency]
    * [Core and Uncore:How much power can be saved]  
  * [Summary][Summary3]  
4. [Artificial Intelligence guided Predictive MicroProcessor tuning][Meditating-with-microprocessors Part-4] <strong>([part-4][Meditating-with-microprocessors Part-4])<strong>
  * [Introduction][Introduction4]
  * [Is there a solution?]
  * [Is there a smarter solution?: Artificial Intelligence model based proactive decision making]
  * [Recognizing the pattern]
  * [Transformer as the backbone]
  * [Transfer Learning inspiration from NLP]
  * [Fine tuning pre-trained networks]  
  * [Artificial Intelligence model based proactive and predictive decision making]
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
    * [Linux Tools:Extraction Tools:BPF:The most powerful and flexible tracer]
      * [Linux Tools:Extraction Tools:BPF:BPF Virtual Machine]
      * [Linux Tools:Extraction Tools:BPF:In Kernel rich Data Structures]  
      * [Linux Tools:Extraction Tools:BPF:Stack Trace Walking]
      * [Linux Tools:Extraction Tools:BPF:A defining example]
      * [Linux Tools:Extraction Tools:Ftrace:Summary]

   * [Summary][Summary5]


# <a name="Introduction"></a>
### Introduction
The current article is part of a bigger <strong>[series][Meditating-with-microprocessors]</strong> titled <strong>[Meditating-with-microprocessors][Meditating-with-microprocessors]</strong>, in which I demonstrate the use of <strong>Artificial Intelligence</strong> to tune <strong>microprocessors</strong> for <strong>Ultra Low Latency</strong> and <strong>Realtime</strong> loads. There are 5 parts to the series, <strong>[Meditating with Microprocessors: An essentially simple idea(part-1)][Meditating-with-microprocessors Part-1]</strong> , <strong>[A crashcourse in Microarchitecture and Linux CPUIDLE interface(part-2)][Meditating-with-microprocessors Part-2]</strong>, <strong>[Trading off power for UltraLowLatency (part-3)][Meditating-with-microprocessors Part-3]</strong> , <strong>[Artificial Intelligence guided Predictive MicroProcessor tuning (part-4)][Meditating-with-microprocessors Part-4]</strong>, <strong>[Appendix:Tools of the trade (part-5)][Meditating-with-microprocessors Part-5]</strong>.

In the current article I lay the  technical groundwork for later articles in the series to build on. The terms and technologies I'll be using later must be understood really well. So we look at 3 concepts primarily.
> * Firstly, the architecture of a modern microprocessor in short, really really short. Just enough to make a mental model of the microprocessor to work with.
> * Secondly, How does the software(Linux) interface with the microprocessor. Again, just enough to make sense of the data we gather from the CPU using various UltraLowLatency profilers and tracers.
> * Thirdly, help programmers like you to get started quickly, explore further, and help us.

# <a name="MicroProcessor Components"></a>
#### MicroProcessor Components
> * <strong>Core:</strong> The modules/agents that compute are called the cores in a CPU. They may be general-purpose CPU cores or special purpose GPGPU cores. Cores take instructions from a software program and execute them loads, stores, "arithmetic and control flow".
> * <strong>Cache:</strong> Caches frequently used data so that expensive memory access can be avoided. There is usually a hierarchy of multiple levels of cache. L1 and L2 are quick access (static RAM chips) small in size. L3 and L4, slower to access (dynamic RAM chips) but comparatively larger in size. Also, L3 and L4 are usually on the die as the core.
> * <strong>On-chip-fabric:</strong> Interconnects exist on the CPU dies that are commonly called on-die or on-chip fabrics. These are not to be confused with fabrics that connect multiple CPU dies together at the data center level.
> * <strong>Memory controller:</strong> Memory controllers provide an interface to main memory (DDR in many recent processor generations).
> * <strong>PCIe:</strong> PCIe provides a mechanism to connect external devices such as network cards into the CPU.
> * <strong>Chipset:</strong> The chipset can be thought of as a support entity to the CPU. In addition to supporting the boot process, it can also provide additional capabilities such as PCIe, hard drive access, networking, and manageability. Chipset functionality is integrated into the same die or package as the cores in the microserver space.

# <a name="Core"></a>
#### Core
Full-length books and courses have been dedicated to microarchitecture, so to illustrate the idea in a few pictures is foolhardy, to say the least. The complexity of this subject is second to none. And yet, I’ll give it a try, because the essence is not that difficult and worth understanding. Also, I have marked some links that do an excellent treatment of the subject in the references below. <strong>The first thing we look at is the flow of instruction and data inside the core at a relatively high level.<strong>

Cores can be designed for general purpose usage where they work well for a wide range of workloads. The trade of with general purpose cores is a relatively higher cost of design and construction. As a result, there are special-purpose cores, that trade-off floating-point performance, or 64-bit precision e.t.c. for smaller form factor or cost. In this section, we look at general-purpose cores. Also, we look at <strong>Sandy Bridge</strong> as it is relatively modern and Intel makes evolutionary changes moving forward to <strong>Haswell</strong> and <strong>\*Lake pipelines</strong>.

<strong>Follow the color code.</strong>

> * <strong>Core:</strong> Identifying various modules/units in the core.  

{%
    include image.html
    src="/img/mwprocs/core.png"
    caption="figure-1: <strong>Oncore modules/units</strong>"
    hight="110%"
    width="110%"
%}     

# <a name="Sandy Bridge Pipeline:Frontend(Instruction load, decode, cache)"></a>
#### Sandy Bridge Pipeline:Frontend(Instruction load, decode, cache)
This details the frontend of the core. How the <strong>INSTRUCTIONS(NOT DATA)</strong> are loaded, decoded and cached.
They have been designed in such a way that multiple μop(micro-operations) are available for execution at every cycle to increase parallelism.

> * <strong>Instruction fetch(figure-2(yellow)):</strong><span style="color:#ff9900">Instruction codes are fetched from the code cache(L1i-cache) in aligned 16-byte chunks into a double buffer that can hold two 16-byte chunks. The purpose of the double buffer is to make it possible to decode an instruction that crosses a 16-byte boundary.</span>
> * <strong>Instruction decoding(figure-2(yellow)):</strong><span style="color:#ff9900">This is a very critical stage in the pipeline because it limits the degree of parallelism that can be achieved. We want to fetch more than one instruction per clock cycle, decode more than one instruction per clock cycle, and execute more than one μop per clock cycle in order to gain speed. But decoding instructions in parallel is difficult when instructions have different lengths. You need to decode the first instruction in order to know how long it is and where the second instruction begins before you can start to decode the second instruction. So a simple instruction length decoder would only be able to handle one instruction per clock cycle. There are four decoders, which can handle instructions generating one or more ]=ops(Micro-operation) according to certain patterns.</span>
> * <strong>Micro-operation Cache(figure-2(yellow)):</strong><span style="color:#ff9900"> The decoded μops(Micro-operation) are cached in a μops(Micro-operation) cache after the decoders in Sandy Bridge. This is useful because the limitation of 16 bytes per clock cycle in the fetch/decode units is a serious bottleneck if the average instruction length is more than four bytes. The throughput is doubled to 32 bytes per clock for code that fits into the μop(Micro-operation) cache.</span>


# <a name="Sandy Bridge Pipeline:Execution"></a>
#### Sandy Bridge Pipeline:Execution
This is where the execution happens. This is the critical source of performance in modern microprocessors. AFAIK, [P5] is the first model from Intel that had multiple ports and an <strong>out-of-order( also called dynamic execution)</strong> scheduler. This scheme is used in most high-performance CPUs. <strong>In this scheme, the order of execution of instructions is determined by the availability of data and execution units and not the source order of the program(Thread).</strong> What this means is instructions within a thread may be reordered to suit the availability of data and execution units. The capacity(parallelism) of Sandy Bridge's execution unit is quite high. Many μops have two or three execution ports to choose between and each unit.  

> * <strong>Register Alias Table(RAT)(figure-2(green)):</strong><span style="color:#669900"> The assembly code references logical/architectural registers(Think of them as place holders). Every logical register has a set of physical registers associated with it. The processor re-maps/transposes this name(for e.g. %rax) to one specific physical register on the fly. The physical registers are opaque and cannot be referenced directly but only via the canonical names.</span>
> * <strong>Register allocation and renaming(figure-1(green)):</strong><span style="color:#669900"> Register renaming is controlled by the register alias table (RAT) and the reorder buffer (ROB).</span>
> * <strong>Execution Units(EU)(figure-2(green)):</strong><span style="color:#669900"> The Sandy Bridge and Ivy Bridge have six execution ports. Port 0, 1 and 5 are for arithmetic and logic operations (ALU). There are two identical memory read ports on port 2 and 3 where previous processors had only one. Port 4 is for memory write. The memory write unit on port 4 has no address calculation. All write operations use port 2 or 3 for address calculation. The maximum throughput is one unfused μop on each port per clock cycle.</span>

# <a name="Sandy Bridge Pipeline:Backend (Data load and Store)"></a>
#### Sandy Bridge Pipeline:Backend (Data load and Store)
> * <strong>Memory access(figure-2(pink)):</strong> <span style="color:#cc0099">The Sandy Bridge has two memory read ports where previous processors have only one. Cache bank conflicts are quite common when the maximum memory bandwidth is utilized.</span>

{%
    include image.html
    src="/img/mwprocs/microarchflow.png"
    caption="figure-2: <strong>Core</strong>"
    hight="110%"
    width="110%"
%}


> * <strong>You can generate the below SOC layout using [hwloc lstopo tool]</strong>

{%
  include image.html
  src="/img/mwprocs/microarchflow3.png"
  caption="figure-3: <strong>Entire SOC(System on chip)</strong>"
  hight="110%"
  width="110%"
%}

# <a name="Haswell Pipeline"></a>
#### Haswell Pipeline

Intel followed up Sandy and Ivy bridge with Haswell. The Haswell has several <strong>important but evolutionary</strong> improvements over previous designs. With a few changes, everything has been increased a little bit. Like number of execution units are 8. Compare figure-2 and figure-4 side by side.

> * <strong>Evolutionary changes in Haswell. </strong>

{%
    include image.html
    src="/img/mwprocs/microarchflow4.png"
    caption="figure-4: <strong>Core</strong>"
    hight="110%"
    width="110%"
%}

> * <strong>Evolutionary changes in Haswell. This SOC(System on Chip) view generated by [hwloc lstopo tool].</strong>

{%
    include image.html
    src="/img/mwprocs/microarchflow5.png"
    caption="figure-5: <strong>Entire SOC(System on chip)</strong>"
    hight="110%"
    width="110%"
%}

# <a name="Uncore"></a>
#### Uncore
All logic that is on the [die] is referred to as Uncore by Intel. As you may have guessed, that is quite a flimsy definition. But it makes sense because, as we integrate more into the single <strong>die</strong> or <strong>package</strong>, it results in a denser design and consumes much less power.

In the Nehalem generation(preceding Sandy bridge ), this included the <strong>L3 cache</strong>, <strong>integrated memory controller</strong>, <strong>QuickPath Interconnect (QPI; for multi-socket communication)</strong>, and an <strong>interconnect</strong> that tied it all together. In the Sandy Bridge generation over and above Sandy Bridge, <strong>PCIe</strong> was integrated into the CPU uncore.

> * <strong>L3 cache</strong>
> * <strong>integrated memory controller</strong>
> * <strong>QuickPath Interconnect (QPI; for multi-socket communication)</strong>
> * <strong>interconnect</strong>
> * <strong>PCIe</strong>
> * <strong>Integrated Grafics</strong>

{%
    include image.html
    src="/img/mwprocs/uncore.png"
    caption="figure-6: <strong>Uncore</strong>"
    hight="110%"
    width="110%"
%}

# <a name="Linux Inteface to CPUIDLE"></a>
#### Linux Inteface to CPUIDLE
The idea here is to control the deeper c-states( deeper meditative states if you recall ) dynamically at run time. But before that, we need to understand Linux’s interface to a microprocessor. The reason why this is important is that it will help us understand the data gathered by the tracers on the microprocessor’s latency. Understanding this process of interaction allows us to visualize data in more flexible ways. Like mapping c-states to latencies etc.

On most systems the microprocessor is doing some task, editing documents, sending or receiving emails, etc. But what does a microprocessor do when there is nothing to execute.
As it turns out, doing nothing is quite complicated. In the olden days idling(doing nothing) was merely the lowest priority thread in a system on a busy spin. Until a new task popped in.
Sooner rather than later, someone was going to realize that it is quite inefficient to expend power without any logical work being done. And this is where deeper c-states come in.

{%
    include image.html
    src="/img/mwprocs/c-states-desc.png"
    caption="figure-7: <strong>c-states description</strong>"
    hight="110%"
    width="110%"
%}

# <a name="CPUIDLE subsystem"></a>
#### CPUIDLE subsystem

Different processors may have different idle-state characteristics. In fact, even for the same manufacturer, there may be differences from one model to another. “cpuidle.h”(“include/linux/cpuidle.h”) is the generic framework defined for the purpose. “cpuidle.h” acts as an interface to separate platform-independent(rest of the kernel) code from manufacture-dependant(CPU Drivers) ones.

# <a name="CPUIDLE subsystem:Driver load"></a>
#### CPUIDLE subsystem:Driver load
The implementation has been handled in <strong>2 different ways.</strong>

> * For a hardware platform (for e.g. x86), a generic set of idle states may be supported in a manufacture independent way. This is implemented in <strong>“acpi_idle”(“drivers/acpi/processor_idle.c”)</strong> available at implements this behavior for all/most x86 vendors(Intel/AMD)
> * The other choice is to use the <strong>Intel driver</strong> which is implemented in <strong>“intel_idle”(“drivers/idle/intel_idle.c”)</strong>

{%
    include image.html
    src="/img/mwprocs/idle.png"
    caption="figure-8: <strong>CPUIdle_driver</strong>"
    hight="110%"
    width="110%"
%}

> * <strong>The figure-9</strong> shows the implementation of <strong>"intel_idle"</strong> which is implemented in <strong>“intel_idle.c”(“drivers/idle/intel_idle.c”)</strong>

{%
    include image.html
    src="/img/mwprocs/intel_idle.png"
    caption="figure-9: <strong>cpuidle_state</strong>"
    hight="110%"
    width="110%"
%}

> * <strong>CPUIDLE driver</strong> from Intel <strong>initialization function.</strong>

{%
    include image.html
    src="/img/mwprocs/cpuinit.png"
    caption="figure-10: <strong>CPU driver initialization(intel_idle)-1</strong>"
    hight="110%"
    width="110%"
%}

> * <strong>CPUIDLE driver</strong> from Intel initialization function. <strong>Figure-(8,9,10,11,12)</strong> are to be looked at <strong>together</strong> to make sense.

{%
    include image.html
    src="/img/mwprocs/cpuinit2.png"
    caption="figure-11: <strong>cpuidle_device</strong>"
    hight="110%"
    width="110%"
%}

> * Finally the result of initialzation can be seen. The idle-states of a modern <strong>Intel processor</strong> through <strong>Linux 'sysfs' interface.</strong>  

# <a name="State of initialized device through sysfs:Residency States"></a>
{%
    include image.html
    src="/img/mwprocs/cpuinit3.png"
    caption="figure-12: <strong>State of initialized device through sysfs</strong>"
    hight="110%"
    width="110%"
%}

# <a name="CPUIDLE subsystem:Call the Driver"></a>
#### CPUIDLE subsystem:Call the Driver
The kernel's scheduler cannot find a task to run on a CPU so it goes ahead and calls “do_idle”(“kernel/sched/idle.c”).
Which idle-state is chosen is a matter of policy, but how then is communicated to the processor is illustrated next.

> * When the kernel scheduler cannot find work to do, it calls <strong>"do_idle()" from "kernel/sched/idle.c".</strong>
> * The <strong>picture illustrates</strong> the interaction.
> * At the bottom right, is a trace generated from <strong>[ftrace] for "intel_idle"</strong>. It shows the stack trace leading upto <strong>intel_idle</strong>.
> * <strong>[ftrace] is almost a magical tracer.</strong>  

{%
    include image.html
    src="/img/mwprocs/cpuidle-sub1.png"
    caption="figure-13: <strong>CPUIDLE:Tracing intel_idle</strong>"
    hight="110%"
    width="110%"
%}

> * This one shows the entry to <strong>processor specific</strong> code of <strong>"intel_idle"</strong> from <strong>"cpuidle_enter_state" ("drivers/cpuidle/cpuidle.c")</strong>.
> * The target state accessed from driver(struct drv). This state structure is of type <strong>"cpuidle_state" shown earlier.</strong>
> * I traced <strong>"sched_idle_set_state"</strong> into which <strong>"target_state"</strong> is getting passed.
> * I traced using <strong>[EBPF]</strong>. <strong>[EBPF]</strong> allows great <strong>flexibility in tracing</strong>. As you can see I am accessing the struct <strong>"cpuidle_state"</strong> at run time on a running kernel and printing it out.

{%
    include image.html
    src="/img/mwprocs/cpuidle-intelidle1.png"
    caption="figure-14: <strong>CPUIDLE:Tracing device_state</strong>"
    hight="110%"
    width="110%"
%}

> * Finally, call to <strong>"intel_idle"</strong> from <strong>"cpuidle_enter_state" ("drivers/cpuidle/cpuidle.c")</strong>.

{%
    include image.html
    src="/img/mwprocs/cpuidle-intelidle2.png"
    caption="figure-15: <strong>CPUIDLE:intel_idle</strong>"
    hight="110%"
    width="110%"
%}

> * <strong>Rubber hits the road or the code hits the metal?</strong>.

{%
    include image.html
    src="/img/mwprocs/cpuidle-intelidle4.png"
    caption="figure-16: <strong>CPUIDLE: code hits the metal</strong>"
    hight="110%"
    width="110%"
%}

> * <strong>"mwait_idle_with_hints"</strong> and <strong>\__mwait</strong> are implemented in <strong>inline assembly.</strong>

{% highlight c %}
static inline void mwait_idle_with_hints(unsigned long eax, unsigned long ecx)
{
  if (static_cpu_has_bug(X86_BUG_MONITOR) || !current_set_polling_and_test()) {
    if (static_cpu_has_bug(X86_BUG_CLFLUSH_MONITOR)) {
      mb();
      clflush((void *)&current_thread_info()->flags);
      mb();
    }

    __monitor((void *)&current_thread_info()->flags, 0, 0);
    if (!need_resched())
      __mwait(eax, ecx);
  }
  current_clr_polling();
}
Listing-1
{% endhighlight %}

{% highlight c %}
static inline void __mwait(unsigned long eax, unsigned long ecx)
{
  /* "mwait %eax, %ecx;" */
  asm volatile(".byte 0x0f, 0x01, 0xc9;"
         :: "a" (eax), "c" (ecx));
}
Listing-2
{% endhighlight %}

# <a name="CPUIDLE subsystem:Call the Driver"></a>
#### CPUIDLE subsystem:Governor

Governor is a part of the CPUIDLE subsystem and implements the policy part. Policies can be expressed in many ways.

> * One policy can be, restrict deeper idle-states for lower latency. These policies are more direct in nature.
> * Others can be based on some QOS requiremment. For example, respond within a latency of a few 100 microseconds or powersave.    

> * <strong>Many governors may exist in a kernel but only one of them can be in control.</strong>.

{%
    include image.html
    src="/img/mwprocs/governor.png"
    caption="figure-17: <strong>CPUIDLE:Governor</strong>"
    hight="110%"
    width="110%"
%}

# <a name="CPUIDLE subsystem:Gathering and undertanding latency data"></a>
#### CPUIDLE subsystem:Gathering and undertanding latency data

The next step is to gather and understand latency data. There are two challenges here. First,  to find a tracer that can gather CPUIDLE latency data without a huge observer effect. This can be achieved by using “[ftrace]”. [“ftrace” is the coolest tracing dude on the planet]. Second, is to make sense of the data.  <strong>At a very high level, what we want to understand is the impact of deep idle-states on latency.</strong>

> * <strong>[ftrace] event tracing</strong> for <strong>cpu_idle</strong>. Which state a <strong>cpu enters and exits</strong> with respect to time lapse as duration.

{%
    include image.html
    src="/img/mwprocs/latency1.png"
    caption="figure-18: <strong>CPUIDLE:ftrace raw data</strong>"
    hight="110%"
    width="110%"
%}

> * <strong>Filtered by cpu.</strong>.

{%
    include image.html
    src="/img/mwprocs/latency2.png"
    caption="figure-19: <strong>CPUIDLE:ftrace:Filtered by cpu</strong>"
    hight="110%"
    width="110%"
%}

> * What the numbers mean, and <strong>corresponding latency calculation.</strong>.

{%
    include image.html
    src="/img/mwprocs/latency3.png"
    caption="figure-20: <strong>CPUIDLE:ftrace:latency calculation</strong>"
    hight="110%"
    width="110%"
%}

> * Latency by <strong>state exit and entry</strong> respectively in CSV form.
> * The <strong>time series property</strong> has been retained. That will tell us if a <strong>configuration change has effected latency.</strong>

{%
    include image.html
    src="/img/mwprocs/latency4.png"
    caption="figure-17: <strong>CPUIDLE:ftrace:csv</strong>"
    hight="110%"
    width="110%"
%}

> * Pushed to <strong>influxdb and then visualized by grafana.</strong>.

{%
    include image.html
    src="/img/mwprocs/latency5.png"
    caption="figure-17: <strong>CPUIDLE:ftrace:influx:grafana:visualization</strong>"
    hight="110%"
    width="110%"
%}

<a name="Summary">
### Summary
We looked at 2 primary concepts. Firstly, a sketch of what units a CPU Core has and how it executes code and processes data. The pipeline of the core should provide a high-level idea of the flow instruction and data. Secondly, the interaction of software(Linux) with the CPU. This information is really interesting and in the field of performance absolutely indispensable. Once understood well, debugging becomes a lot easier. Fancy profilers are all very good but have a huge observer effect. ‘Ftrace’ like tools are built for this purpose. However, the data they generate may not be easily digestible. Now that we have a basic understanding, we can use these tools to measure after-effects of performance configurations in the rest of the [series][Meditating-with-microprocessors].

### References
1. <a href='https://lwn.net/Articles/250967/'><strong>What every programmer should know about memory:</strong></a> <strong>(This is a definitive 9 part(the links for the rest of the parts are embedded in the first one.) article on how the hardware works and, how software and data structure design can exploit it. It had a huge impact on the way I thought and still do think about design. The article first came out in 2007 but it is still relevant which is proof that basics don't change very often.)</strong>
2. <a href='https://software.intel.com/content/www/us/en/develop/articles/intel-sdm.html'><strong>Intels documenation:</strong></a><strong> (Intel's documentation is the authentic source here. But, it is incredibly difficult to read. It is as if Intel's employees were given a raise to make it "impossible to comprehend" kind of document.)</strong>
3. <a href='https://www.agner.org/optimize/'><strong>Agner Fog:</strong></a><strong> (He benchmarks microprocessors using forward and reverse engineering techniques. My bible.)</strong>
4. <a href='https://github.com/torvalds/linux'><strong>Linux Source:</strong></a><strong> (If you are going to trace your programs/applications then having the Linux source is a must. Tracers will tell you half the story, the other half will come from here. )</strong>
5. <a href='https://ai.googleblog.com/2017/08/transformer-novel-neural-network.html'><strong>Transformer:</strong></a><strong> (Modern Natural Language processing, in general, must be indebted to Transformer architecture. We however, use an asymmetric transformer setup.)</strong>
6. <a href='https://arxiv.org/pdf/2009.14794.pdf'><strong>Performer-A Sparse Transformer:</strong></a><strong> (This is as cutting edge as it can get. The transformer at the heart of it is a stacked multi-headed-attention unit. As the sequences(of words or System events or stock prices or vehicle positions e.t.c.) get longer, the quadratic computation and quadratic memory for matrix cannot keep up. Performer, a Transformer architecture with attention mechanisms that scale linearly. The framework is implemented by Fast Attention Via Positive Orthogonal Random Features (FAVOR+) algorithm.)</strong>
7. <a href='https://www.youtube.com/watch?v=93uE_kWWQjs&t=1178s'><strong>Ftrace: The Inner workings</strong></a><strong> ( I dont think there is a better explaination of Ftrace's inner workings.)</strong>
8. <a href='https://lwn.net/Articles/608497/'><strong>Ftrace: The hidden light switch:</strong></a><strong> ( This article demonstrates the tools based on Ftrace.)</strong>
9. <a href='http://www.brendangregg.com/ebpf.html'><strong>BPF:</strong></a><strong> ( eBPF or just BPF is changing the way programming is done on Linux. Linux now has observability superpowers beyond most OSes. A detailed post on BPF is the need of the hour and I am planning as much. In the meantime, the attached link can be treated as a virtual BPF homepage.)</strong>


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
[Core and Uncore:Uncore:monitoring and Tuning]: MeditatingProcessor-3#Core and Uncore:Uncore:monitoring and Tuning
[Core and Uncore:Uncore:monitoring and Tuning:Hardware Latency]: MeditatingProcessor-3#Core and Uncore:Uncore:monitoring and Tuning:Hardware Latency
[Core and Uncore:Uncore:monitoring and Tuning:Wakeup Latency]: MeditatingProcessor-3#Core and Uncore:Uncore:monitoring and Tuning:Wakeup Latency
[Core and Uncore:How much power can be saved]: MeditatingProcessor-3#Core and Uncore:How much power can be saved
[Summary3]: MeditatingProcessor-3#Summary

<!--Doc4-->
[Introduction4]: MeditatingProcessor-4#Introduction
[Is there a solution?]: MeditatingProcessor-4#Is there a solution?
[Is there a smarter solution?: Artificial Intelligence model based proactive decision making]: MeditatingProcessor-4#Is there a smarter solution?: Artificial Intelligence model based proactive decision making
[Recognizing the pattern]: MeditatingProcessor-4#Recognizing the pattern
[Transformer as the backbone]: MeditatingProcessor-4#Transformer as the backbone
[Transfer Learning inspiration from NLP]: MeditatingProcessor-4#Transfer Learning inspiration from NLP
[Fine tuning pre-trained networks]: MeditatingProcessor-4#Fine tuning pre-trained networks
[Artificial Intelligence model based proactive and predictive decision making]: MeditatingProcessor-4#Artificial Intelligence model based proactive and predictive decision making
[The Final Cut]: MeditatingProcessor-4#The Final Cut
[Summary4]: MeditatingProcessor-4#Summary

<!--Doc5-->
[ftrace]: MeditatingProcessor-5#Ftrace:The coolest tracing dude on the planet
[EBPF]: MeditatingProcessor-5#Linux Tools:Extraction Tools:BPF:The most powerful and flexible tracer
[“ftrace” is the coolest tracing dude on the planet]: MeditatingProcessor-5#Ftrace:The coolest tracing dude on the planet
[Introduction5]: MeditatingProcessor-5#Introduction
[Linux Tools:An incisive but limited view]: MeditatingProcessor-5#Linux Tools:An incisive but limited view
[Linux Tools:Event Sources:uprobes and kprobes]: MeditatingProcessor-5#Linux Tools:Event Sources:uprobes and kprobes
[Linux Tools:Event Sources:Tracepoints]: MeditatingProcessor-5#Linux Tools:Event Sources:Tracepoints
[Linux Tools:Extraction Tools]: MeditatingProcessor-5#Linux Tools:Extraction Tools
[Linux Tools:Extraction Tools:Ftrace:The coolest tracing dude on the planet]: MeditatingProcessor-5#Linux Tools:Extraction Tools:Ftrace:The coolest tracing dude on the planet
[Linux Tools:Extraction Tools:Ftrace:Engineering]: MeditatingProcessor-5#Linux Tools:Extraction Tools:Ftrace:Engineering
[Linux Tools:Extraction Tools:Ftrace:Summary]: MeditatingProcessor-5#Linux Tools:Extraction Tools:Ftrace:Summary
[Linux Tools:Extraction Tools:BPF:The most powerful and flexible tracer]: MeditatingProcessor-5#Linux Tools:Extraction Tools:BPF:The most powerful and flexible tracer
[Linux Tools:Extraction Tools:BPF:BPF Virtual Machine]: MeditatingProcessor-5#Linux Tools:Extraction Tools:BPF:BPF Virtual Machine
[Linux Tools:Extraction Tools:BPF:In Kernel rich Data Structures]: MeditatingProcessor-5#Linux Tools:Extraction Tools:BPF:In Kernel rich Data Structures
[Linux Tools:Extraction Tools:BPF:Stack Trace Walking]: MeditatingProcessor-5#Linux Tools:Extraction Tools:BPF:Stack Trace Walking
[Linux Tools:Extraction Tools:BPF:A defining example]: MeditatingProcessor-5#Linux Tools:Extraction Tools:BPF:A defining example
[Linux Tools:Extraction Tools:Ftrace:Summary]: MeditatingProcessor-5#Linux Tools:Extraction Tools:Ftrace:Summary
[Summary5]: MeditatingProcessor-5#Summary

<!--External-->

[Intel 8th and 9th datasheeet manual]: [https://www.intel.com/content/dam/www/public/us/en/documents/datasheets/8th-gen-core-family-datasheet-vol-1.pdf]

[Domino]: https://en.wikipedia.org/wiki/Domino_effect
[coordinated omission]: https://www.azul.com/files/HowNotToMeasureLatency_LLSummit_NYC_12Nov2013.pdf
[here is a great reference to it]: https://www.azul.com/files/HowNotToMeasureLatency_LLSummit_NYC_12Nov2013.pdf
[hwloc lstopo tool]: [https://linux.die.net/man/1/lstopo]
[die]: https://en.wikipedia.org/wiki/Die_(integrated_circuit)
[p5]: https://en.wikipedia.org/wiki/P5_(microarchitecture)
