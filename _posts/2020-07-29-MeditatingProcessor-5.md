---
layout: post
title:  "Meditating with microprocessors Series: Part-5: Appendix:Tools of the trade(part-5)"
excerpt: "The current article is part of a bigger <strong><a href='/tags/#Meditating-with-microprocessors'>series</a></strong> titled <strong><a href='/tags/#Meditating-with-microprocessors'>Meditating-with-microprocessors</a></strong>,in which I demonstrate the use of <strong>Artificial Intelligence</strong> to tune <strong>microprocessors</strong> for <strong>Ultra Low Latency</strong> and <strong>Realtime</strong> loads. There are 5 parts to the series <strong><a href='/articles/2020-07/MeditatingProcessor-1'>Artificial Intelligence based Hardware(Microprocessor) tuning: Implementing a very simple idea(part-1) </a></strong>, <strong><a href='/articles/2020-07/MeditatingProcessor-2'>A crashcourse in Microarchitecture and Linux CPUIDLE interface(part-2)</a></strong>, <strong><a href='/articles/2020-07/MeditatingProcessor-3'>Trading off power for UltraLowLatency(part-3)</a></strong>, <strong><a href='/articles/2020-07/MeditatingProcessor-4'>Artificial Intelligence guided Predictive MicroProcessor tuning(part-4)</a></strong>, <strong><a href='/articles/2020-07/MeditatingProcessor-5'>Appendix:Tools of the trade(part-5) </a></strong>. In the current article, we drill down into the inner workings of <strong>Ftrace</strong> and <strong>EBPF</strong>. With <strong>EBPF based techniques and frameworks</strong> Linux tooling has gained <strong>tracing superpowers</strong>. It has capabilities that now exceed that of Dtrace on Solaris. This is not an exhaustive section and it does not do a breadth-first scan of Linux tooling. I am highlighting a couple of tools, why they work for us, and what makes them special. Moreover, tools play a vital role in verifying or rebutting the theories we make about the systems we design. Building/designing a system is like building a beehive, little blocks you put together making it a whole. But these little blocks fit more snuggly if the theory is rock-solid. Tools provide us the evidence for that."
mathjax: true
date:   2020-07-29 15:07:19
categories: [article]
tags: Artificial-Intelligence Deep-Learning LSTM NLP Microarchitecture UltraLowLatency RealTime Performance Meditating-with-microprocessors
image:
  feature: mwprocs/bpfsw2.png
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
The current article is part of a bigger <strong>[series][Meditating-with-microprocessors]</strong> titled <strong>[Meditating-with-microprocessors][Meditating-with-microprocessors]</strong>, in which I demonstrate the use of <strong>Artificial Intelligence</strong> to tune <strong>microprocessors</strong> for <strong>Ultra Low Latency</strong> and <strong>Realtime</strong> loads. There are 5 parts to the series, <strong>[Meditating with Microprocessors: An essentially simple idea(part-1)][Meditating-with-microprocessors Part-1]</strong> , <strong>[A crashcourse in Microarchitecture and Linux CPUIDLE interface(part-2)][Meditating-with-microprocessors Part-2]</strong>, <strong>[Core (part-3)][Meditating-with-microprocessors Part-3]</strong> , <strong>[Uncore (part-4)][Meditating-with-microprocessors Part-4]</strong>, <strong>[Appendix:Tools (part-5)][Meditating-with-microprocessors Part-5]</strong>. In the current article, we drill down into the inner workings of <strong>Ftrace</strong> and <strong>EBPF</strong>. With <strong>EBPF based techniques and frameworks</strong> Linux tooling has gained <strong>tracing superpowers</strong>. It has capabilities that now exceed that of Dtrace on Solaris. This is not an exhaustive section and it does not do a breadth-first scan of Linux tooling. I am highlighting a couple of tools, why they work for us, and what makes them special. Moreover, tools play a vital role in verifying or rebutting the theories we make about the systems we design. Building/designing a system is like building a beehive, little blocks you put together making it a whole. But these little blocks fit more snuggly if the theory is rock-solid. Tools provide us the evidence for that.

# <a name="Linux Tools:An incisive but limited view"></a>
#### Linux Tools:An incisive but limited view

There is a [great article][Linux Tooling] that takes an expansive look at linux tooling and declutters the theory behind it. Here we look at the details of how a couple of them work and what makes them special.

> * The original picture can be found [here][Linux Tooling pic]. I have modified it to bit highlight our focus in this article.

{%
    include image.html
    src="/img/mwprocs/linuxtooling.png"
    caption="figure-1: <strong>Linux Tooling: Our focus</strong>"
    hight="110%"
    width="110%"
%}

# <a name="Linux Tools:Event Sources:uprobes and kprobes"></a>
#### Linux Tools:Event Sources:uprobes and kprobes
The technique of dynamic instrumentation is quite old(1990s if my memory serves me right) and reasonably mature. However, its application to Linux is relatively recent(mid 2000). Similar to techniques used in debuggers. They are called uprobes , uretprobes, kprobes and kretprobes in Linux. uprobes and uretprobes are for user-space processes. uprobes are function entries and uretprobes are function exits. kprobes and kretprobes are for kernel space. The technique of application is nearly identical.

> * When a CPU hits the breakpoint instruction, a trap occurs, the CPU's registers are saved, and control passes to kprobes/uprobe via the notifier_call_chain mechanism.
> * The original instructions are then executed, and instruction flow resumes.
> * When the kprobe/uprobe is no longer needed, the original bytes are copied back to the target address, restoring the instructions to their original state.
> * Since <strong>kprobes</strong> can probe into a running kernel code, it can change the register set, including instruction pointer. This operation requires maximum care, such as keeping the stack frame, recovering the execution path etc. Since it operates on a running kernel and needs <strong>deep knowledge of computer architecture and concurrent computing</strong>, <strong>you can easily shoot your foot</strong>.

> * Instrumenting bash's readline with bpftrace.

{% highlight shell %}
sudo gdb -p <bashpid>
file /usr/lib/debug/bin/bash
disas /r readline

sudo bpftrace -e 'uprobe:/bin/bash:readline { @ = count() }'

sudo gdb -p <bashpid>
file /usr/lib/debug/bin/bash
disas /r readline
{% endhighlight %}

> * The figure below shows before and after instrumentation.
> * Notice the insertion of int3 breakpoint instruction.

{%
    include image.html
    src="/img/mwprocs/linuxtooling2.png"
    caption="figure-2: <strong>Uprobe in action</strong>"
    hight="110%"
    width="110%"
%}

> * When instruction flow hits this breakpoint, the breakpoint handler checks if it was installed by kprobes, and, if so, executes a kprobe handler.

{%
    include image.html
    src="/img/mwprocs/linuxtooling3.png"
    caption="figure-3: <strong>Uprobe in action</strong>"
    hight="110%"
    width="110%"
%}

# <a name="Linux Tools:Event Sources:Tracepoints"></a>
#### Linux Tools:Event Sources:Tracepoints

Tracepoints are used for kernel static instrumentation. They involve tracing calls that developers have inserted into the kernel code at logical places, which are then compiled into the kernel binary. <strong>Tracepoints are a burden for kernel developers</strong> to maintain, and tracepoints are far more limited in scope than kprobes. The advantage is that tracepoints provide a <strong>stable API: Tools written to use tracepoints should continue working across newer kernel versions<strong>, whereas those written using kprobes may break if the traced function is renamed or changed.

> * <strong>Try using tracepoints first, if available and sufficient, and turn to kprobes only as a backup.
> * A tracepoint is inserted using the TRACE_EVENT macro.
> * It inserts a callback in the kernel source that gets called with the tracepoint parameters as arguments.
> * Tracepoints added with the TRACE_EVENT macro allow ftrace or any other tracer to use them.
> * The callback inserts the trace at the calling tracer's ring buffer.–To insert a new tracepoint into the Linux kernel, define a new header file with a special format.
> * By default, tracepoint kernel files are located in include/trace/events(scheduling related tracepoints are declared in (“include/trace/events/sched.h”)). If they are not located in this directory, then other configurations are necessary.

{%
    include image.html
    src="/img/mwprocs/linuxtools4.png"
    caption="figure-4: <strong>Tracepoints:TRACE_EVENT macro for sched_switch</strong>"
    hight="110%"
    width="110%"
%}

> * Scheduling trace captured using tracepoint.
> * This particular tracepoint is <strong>sched_switch</strong>.
> * <strong>trace_sched_switch</strong> is statically placed in the code by the TRACE_EVENT macro.

{%
    include image.html
    src="/img/mwprocs/linuxtools5.png"
    caption="figure-5: <strong>Tracepoints:sched_switch trace by ftrace</strong>"
    hight="110%"
    width="110%"
%}

# <a name="Linux Tools:Extraction Tools"></a>
#### Linux Tools:Extraction Tools

We will look at 2 of these tools. One of them quite old and other one relatively new.

{%
    include image.html
    src="/img/mwprocs/linuxtools6.png"
    caption="figure-6: <strong>Extraction Tools</strong>"
    hight="110%"
    width="110%"
%}

# <a name="Linux Tools:Extraction Tools:Ftrace:The coolest tracing dude on the planet"></a>
#### Linux Tools:Extraction Tools:Ftrace:The coolest tracing dude on the planet

Ftrace as the coolest tracing dude on the planet. What makes ftrace such a fire-cracker.
> * It is a <strong>part of Linux kernel</strong> since 2.6.28 (which in ancient times) and is the <strong>official Linux tracer</strong>.
> * It means there is <strong>no installation or compatibility</strong> overhead at all.
> * People in the <strong>embedded domain</strong> <strong>([Raspberry Pi])</strong> or even distributions like <strong>[Busybox]</strong> adore ftrace as it’s <strong>foot print</strong> is <strong>negligible</strong> and <strong>absolutely NIL installation requirements</strong>.

# <a name="Linux Tools:Extraction Tools:Ftrace:Engineering"></a>
#### Linux Tools:Extraction Tools:Ftrace:Engineering

How is it possible to instrument all(most) kernel functions with almost nil overhead. The way this is done, in my mind, is one of the best pieces of engineering that I have seen. Here is the [original video] by [Steven Rostedt] that describes ftrace engineering in all it's gory details. Below is much simplified version of the same.

> * When the Linux kernel is compiled with <strong>"-pg -mfentry"</strong> option, the GCC compiler adds special <strong>“\__fentry__”</strong> call to all non-inlined functions.
> * All non inlined functions call <strong>“\__fentry__”</strong> function at the <strong>beginning of the function</strong>.
> * The figure below shows <strong>"schedule"</strong> when compiled with the above option.

{%
    include image.html
    src="/img/mwprocs/ftrace.png"
    caption="figure-7: <strong>Ftrace Engineering: __fentry__</strong>"
    hight="110%"
    width="110%"
%}

> * Recall, that the broad goal here is the trace kernel functions with minimal overhead when turned off. “\__fentry__” is placed for exactly the same reason.
> * <strong>But calling “\__fentry__” function from every non inlined function</strong> is going to be <strong>huge overhead</strong> even if the function does not do anything.
> * The cost of calling and returning from nearly 40 thousand kernel function is nearly <strong>13-15%</strong> and clearly not acceptable.
> * <strong>"recordmcount.c"</strong> reads the object file one at a time and finds the <strong>“\__fentry__” call location(call-site address)</strong> and creates an array called <strong>"mcount_loc</strong>."
> * <strong>"\__start_mcount_loc" and "\__stop_mcount_loc" are variables</strong> that define the start and stop of a section in vmlinux object file. These are inserted into the vmlinux file by the linker and have all the call-site addresses.
> * <strong>At boot time</strong> we find the addresses of the functions in the array between "\__start_mcount_loc" and "\__stop_mcount_loc" and <strong>convert them to NOPs(figure-8)</strong>.
> * GCC-5 adds -mnop-mcount for the same purpose.

{%
    include image.html
    src="/img/mwprocs/ftrace2.png"
    caption="figure-8: <strong>Ftrace Engineering: NOPs at boot time using array section at __start_mcount_loc </strong>"
    hight="110%"
    width="110%"
%}

> * However, The array between "\__start_mcount_loc" and "\__stop_mcount_loc" is not enough for ftrace framework to work.
> * We need <strong>state information</strong> along with the <strong>function addresses</strong> to be stored per each kernel function to track the functions to be dynamically traced.
> * This state information is stored in pages using struct <strong>"ftrace_page"</strong>.
> * Each <strong>"ftrace_page"</strong> has multiple <strong>"dyn_ftrace"</strong> as an array of structs. <strong>"ip" stores the class-site addresses</strong> and the <strong>flags stores the state of that particular call-site</strong>.   

{% highlight shell %}
  struct ftrace_page {
  	struct ftrace_page *next;
  	struct dyn_ftrace	*records;
  	int			index;
  	int			size;
  }

  struct dyn_ftrace {
  	unsigned long		ip; /* address of mcount call-site */
  	unsigned long		flags;
  	struct dyn_arch_ftrace	arch;
  };

{% endhighlight %}

> * <strong>Figure-9</strong> below depicts <strong>"ftrace_page"</strong> and <strong>array of struct "dyn_ftrace"</strong>.
> * The memory consumed by <strong>"ftrace_pages"</strong> is <strong>roughly 630 kilobytes</strong> in approximately <strong>154 pages</strong> for close to <strong>40 thousand kernel functions</strong>.

{%
    include image.html
    src="/img/mwprocs/ftrace3.png"
    caption="figure-9: <strong>Ftrace Engineering</strong>"
    hight="110%"
    width="110%"
%}

> * struct "dyn_ftrace" <strong>field flags</strong> holds the various flags for ftrace to work. For e.g. Bit 29 is to save the CPU registers while tracing, Bit 30 Needs to call ftrace_regs_caller, and Bit 31 means function is being traced.     
> * Very long story short, while enabling tracing sometimes CPU registers have to be saved and sometimes not. This results in slightly different ftrace callbacks as shown figure-10.


{%
  include image.html
  src="/img/mwprocs/ftrace4.png"
  caption="figure-10: <strong>Ftrace Engineering</strong>"
  hight="110%"
  width="110%"
  %}
# <a name="Linux Tools:Extraction Tools:Ftrace:Summary"></a>
#### Linux Tools:Extraction Tools:Ftrace:Summary

Nearly 40 thousand traceable functions in the kernel with negligible overhead is some engineering feet. As you opt-in for events or functions to be traced your overhead goes up in a pay-as-you-go manner. Add to that is the fact that it comes baked in with the kernel. This is not a tutorial of ftrace. Will think of planning one later.

> * Can be used with "cat" and "echo".
> * Interface is the tracefs/debugfs usually mounted at "/sys/kernel/debug/tracing". It may be slightly different depending on your distribution.
> * Last but not least, ftrace is not just a function tracer. It is a <strong>collection of tracers</strong> including <strong>Hardware Latency Tracer(hwlat)</strong>, <strong>Wakeup(wakeup)</strong> and many more.

{%
    include image.html
    src="/img/mwprocs/ftrace5.png"
    caption="figure-11: <strong>Ftrace Example</strong>"
    hight="110%"
    width="110%"
%}

> * Finally ftrace comes with a few front end tools like the [command-line trace-cmd] and a [visualizer called kernelshark]. However, understanding the data captured by a tracer is often much more important. The the subsystem being traced is understood, then the captured data can be [post processed][CPUIDLE subsystem:Gathering and undertanding latency data] to suit any visualizer.   

# <a name="Linux Tools:Extraction Tools:BPF:The most powerful and flexible tracer"></a>
#### Linux Tools:Extraction Tools:BPF:The most powerful and flexible tracer

<strong>EBPF(Extended Berkeley Packet Filters)</strong> or just <strong>BPF(Berkeley Packet Filters)</strong> is like a <strong>swiss army knife of performance tools</strong>. I am avoiding it's history completely in the interest of brevity. There is an excellent resource on [BPF][bpfexternal] by one of it's [authors][bpfauthor]. To be more precise, the author of tools like [bcc] and [bpftrace] built on top of BPF. As an aside, the only thing that does not resonate with me is its name(BPF...Really?? ). We could have a more attractive name but I guess that horse has already bolted. It is a highly capable tool and is one reason why it is a bit difficult to explain. This post is about <strong>it's engineering</strong>, what <strong>makes it capable</strong> and a few examples to <strong>illustrate it superpowers over other tools</strong>.

BPF has many capabilities. But here we focus on a couple of it's defining features.
  1. It's ability to post-process data in kernel safely and securely in a <strong>[BPF Virtual Machine][Linux Tools:Extraction Tools:BPF:BPF Virtual Machine]</strong>.
  2. It's <strong>[In Kernel rich Data Structures][Linux Tools:Extraction Tools:BPF:In Kernel rich Data Structures]</strong> for ease of processing.
  3. It's detailed <strong>[Stack Trace Walking][Linux Tools:Extraction Tools:BPF:Stack Trace Walking]</strong>  

# <a name="Linux Tools:Extraction Tools:BPF:BPF Virtual Machine"></a>
#### Linux Tools:Extraction Tools:BPF:BPF Virtual Machine

> * The post-processing carried out by <strong>BPF Virtual Machine</strong> has been attempted by many techniques before. One such technique was to use kernel modules. However, they were all not safe in production because they were all executed in a running kernel with practically no checks.
> * BPF provides a <strong>BPF Virtual Machine</strong> with a verifier that checks for harmful code before executing them.

{%
    include image.html
    src="/img/mwprocs/bpf11.png"
    caption="figure-12: <strong>BPF Virtual Machine</strong>"
    hight="110%"
    width="110%"
%}

> * The <strong>BPF Virtual Machine</strong> has a verifier that reject unsafe operations.
> * A Virtual Machine does not necessarily mean slow execution.
> * A JIT compiler generates native instruction for direct and faster execution.

{%
    include image.html
    src="/img/mwprocs/bpf12.png"
    caption="figure-13: <strong>BPF Virtual Machine</strong>"
    hight="110%"
    width="110%"
%}

> * Figure-14 shows a classic BPF before and after example.
> * In kernel summary and in general in kernel pre/post execution/processing opens up enough and more avenues for making excellent tools for all kinds of purposes.

{%
    include image.html
    src="/img/mwprocs/bpf13.png"
    caption="figure-14: <strong>BPF Virtual Machine</strong>"
    hight="110%"
    width="110%"
%}

# <a name="Linux Tools:Extraction Tools:BPF:In Kernel rich Data Structures"></a>
#### Linux Tools:Extraction Tools:BPF:In Kernel rich Data Structures

BPF provides <strong>rich data structures</strong> via <strong>maps(Figure-12,13), histograms(Figure-12,13)</strong>, e.t.c to process in kernel summaries. Many tools like [Perf] can gather data from [PMC][Performance Monitoring Counters]s but they have to push it the user space to be post-processed. What this means is a lot more data get transferred to the user-land. With rich data structures and in-kernel processing, this can be minimized. Also, the ability to in-kernel processing by itself is awesome and opens up huge possibilities.

# <a name="Linux Tools:Extraction Tools:BPF:Stack Trace Walking"></a>
#### Linux Tools:Extraction Tools:BPF:Stack Trace Walking

Stack traces are an invaluable tool for understanding the code path that led to an event, as well as profiling kernel and user code to observe where execution time is spent. While there are other ways to walk the stack, framepointer based stack walking is quite popular and BPF uses it.

> * This is how a call is setup in X86.
> * The figure-15 depicts the address to jump back to when the callee has finished execution.

{%
    include image.html
    src="/img/mwprocs/bpfsw1.png"
    caption="figure-15: <strong>Coming Soon</strong>"
    hight="110%"
    width="110%"
%}

> * This figure-16 depicts the stack trace walking using frame pointers.
> * This will show the trace leading up to the event.

{%
    include image.html
    src="/img/mwprocs/bpfsw2.png"
    caption="figure-16: <strong>Coming Soon</strong>"
    hight="110%"
    width="110%"
%}

> * Some code in C++ to traverse the frame, however, this would usually be acomplished by a tool. BPF in our case.

{%
    include image.html
    src="/img/mwprocs/bpfsw3.png"
    caption="figure-17: <strong>Coming Soon</strong>"
    hight="110%"
    width="110%"
%}

# <a name="Linux Tools:Extraction Tools:BPF:A defining example"></a>
#### Linux Tools:Extraction Tools:BPF:A defining example

This example explores the tracing of kernel function and even traversing and processing kernel structures. This would not have been possible earlier without compromising the safety of the kernel. BPF makes this possible.

> * Consider <strong>"sched_idle_set_state"</strong> kernel function. It is a part of CPUIDLE generic driver interface. It takes the <strong>struct "cpuidle_state" as parameter</strong>.
> * Tracing the above function with ftrace I am only able to access the "cpuidle_state" <strong>parameter as an address</strong> as it is a structure that is of no practical use. This is illustrated in figure-18 below.

{%
    include image.html
    src="/img/mwprocs/bpf1.png"
    caption="figure-18: <strong>Tracing sched_idle_set_state parameters</strong>"
    hight="110%"
    width="110%"
%}

> * Identical setup as above, except this time I use a BPF script to trace and traverse the struct "cpuidle_state".

{%
    include image.html
    src="/img/mwprocs/bpf2.png"
    caption="figure-19: <strong>Tracing sched_idle_set_state parameters with BPF</strong>"
    hight="110%"
    width="110%"
%}

# <a name="Linux Tools:Extraction Tools:Ftrace:Summary"></a>
#### Linux Tools:Extraction Tools:Ftrace:Summary

BPF performance tools make use of many technologies, including extended BPF, kernel and user dynamic instrumentation (kprobes and uprobes), kernel and user static tracing (tracepoints and user markers), and perf_events.

<a name="Summary">
### Summary
These are not the only tools. There are a couple of <strong>notable omissions</strong>. The biggest one being <strong>[Performance Monitoring Counters]</strong> as an event source and <strong>[PERF]</strong> and an extraction tool. It is a <strong>frequency-based profiling tool</strong> and something we use heavily as one can <strong>control the frequency of recording to vary the overhead of the recording being done</strong>.  

In summary, these are tools that we take to the trenches of performance tuning and monitoring. The important thing being that if used right they have the capability to transform your understanding and take it to the next level. <strong>By the next level I mean, the ability to use this information to impress upon the design stage of your application</strong>.

"[ftrace]" for example is an always installed <strong>(on most Linux distributions)</strong> and can be used with just <strong>"echo"</strong> and <strong>"cat"</strong>. Yes, It does not have a fancy UI but the captured data can be post-processed to be rendered by any visualizer. <strong>Here are  few sample csv files([file1],[file2])</strong>.

[BPF] is a different beast altogether. It can look inside the kernel and most userland applications including Java/JVM python with kprobes/uprobes/USDTs respectively. <strong>It's safe in-kernel/in-application processing is a powerful feature that lends modern Linux Kernel it's amazing superpowers</strong>.

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
[EBPF]: MeditatingProcessor-5#EBPF: The most powerful and flexible tracer
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
[file1]: /img/mwprocs/cpu0.csv
[file2]: /img/mwprocs/cpu5.csv
[Summary5]: MeditatingProcessor-5#Summary
<!--External-->
[Intel 8th and 9th datasheeet manual]: [https://www.intel.com/content/dam/www/public/us/en/documents/datasheets/8th-gen-core-family-datasheet-vol-1.pdf]
[Domino]: https://en.wikipedia.org/wiki/Domino_effect
[coordinated omission]: https://www.azul.com/files/HowNotToMeasureLatency_LLSummit_NYC_12Nov2013.pdf
[here is a great reference to it]: https://www.azul.com/files/HowNotToMeasureLatency_LLSummit_NYC_12Nov2013.pdf
[hwloc lstopo tool]: [https://linux.die.net/man/1/lstopo]
[die]: https://en.wikipedia.org/wiki/Die_(integrated_circuit)
[turbostat]: https://www.linux.org/docs/man8/turbostat.html
[Intel's documentation]: https://software.intel.com/content/www/us/en/develop/articles/intel-sdm.html
[Cannonlake]: https://en.wikipedia.org/wiki/Cannon_Lake_(microarchitecture)
[likwid]: https://github.com/RRZE-HPC/likwid
[Linux Tooling]: https://jvns.ca/blog/2017/07/05/linux-tracing-systems/
[Linux Tooling pic]: https://drawings.jvns.ca/drawings/linux-tracing-1.png
[Raspberry Pi]: https://www.raspberrypi.org/
[Busybox]: https://www.busybox.net/
[Steven Rostedt]: https://blogs.vmware.com/opensource/author/steven-rostedt/
[original video]: https://www.youtube.com/watch?v=93uE_kWWQjs&t=333s
[command-line trace-cmd]: https://github.com/rostedt/trace-cmd
[visualizer called kernelshark]: https://kernelshark.org/
[Performance Monitoring Counters]: https://en.wikipedia.org/wiki/Hardware_performance_counter
[PERF]: https://perf.wiki.kernel.org/index.php/Tutorial
[bpfexternal]: http://www.brendangregg.com/ebpf.html
[bpfauthor]: http://www.brendangregg.com/
[bcc]: https://github.com/iovisor/bcc
[bpftrace]: https://github.com/iovisor/bpftrace
