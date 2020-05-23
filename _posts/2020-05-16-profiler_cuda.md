
---
layout: post

title:  "profiler_cuda"

date:   2020-05-16 14:29

excerpt:

tag:

- GPU
---

[TOC]



# 简介

主要用于针对于cuda函数进行性能测量

本文档介绍了NVIDIA的剖析工具，使您能够了解并优化您的CUDA、OpenACC或OpenMP应用程序的性能。Visual Profiler是一个图形化的剖析工具，它可以显示应用程序的CPU和GPU活动的时间轴，其中包括一个自动分析引擎，可识别出优化机会。nvprof profiling工具可以让你从命令行收集和查看分析数据。

建议使用下一代工具NVIDIA Nsight Compute进行GPU分析，以及NVIDIA Nsight Systems进行GPU和CPU采样和追踪。

NVIDIA Nsight Compute是一款针对CUDA应用程序的交互式内核分析器。它通过一个用户界面和命令行工具提供详细的性能指标和API调试。此外，其基线功能允许用户在工具内比较结果。Nsight Compute提供了一个可定制的、数据驱动的用户界面和**指标收集**，并可通过**分析脚本**进行后处理结果的扩展。

NVIDIA Nsight Systems是一款全系统的性能分析工具，其设计目的是将应用的算法可视化，帮助您识别出最大的优化机会，并在任何数量或大小的CPU和GPU上进行有效的调整，从大型服务器到最小的SoC。

- [ ] 这两者的区别是什么呢？



# 术语

event是指设备上的**可计数**的活动、动作以及发生。它对应于内核执行过程中收集的单个硬件**计数器值**。要查看特定NVIDIA GPU上所有可用事件的列表，请键入 nvprof --query-events。

metric是一个应用程序的特性，它是**由一个或多个事件值计算**出来的。要查看一个特定的NVIDIA GPU上所有可用的指标列表，请键入 nvprof --query-metrics。你也可以参考度量指标参考。



# 性能报告分析



## 聚焦特定的部分上

默认情况下，剖析工具会在整个应用程序的运行过程中收集配置文件数据。但是，正如下面所解释的，通常只想对应用程序中包含部分或全部性能关键代码的区域进行剖析。将剖面分析限制在性能关键区域，可以减少工具必须处理的剖面数据量，并将注意力集中在优化能带来最大性能提升的代码上。

- 当应用程序的每个阶段的性能都可以独立于其他阶段进行优化时，你要对每个阶段分别进行剖析，以便集中优化工作。

```c++
 cudaProfilerStart();
 ...
 cudaProfilerStop();
```

**注意事项:**

> When using the start and stop functions, you also need to instruct the profiling tool to disable profiling at the start of the application. For nvprof you do this with the --profile-from-start off flag. For the Visual Profiler you use the Start execution with profiling enabled checkbox in the Settings View.



## 标记CPU的活动

要了解应用程序的CPU线程在CUDA函数调用之外的工作，可以使用NVIDIA Tools Extension API (NVTX)。



## 刷新Profile Data

为了减少剖析开销，剖析工具收集和记录剖析信息到内部缓冲区。然后这些缓冲区被异步冲洗到磁盘上，并以低速 优先级，以避免扰乱应用行为。为了避免丢失配置文件信息 还没有被冲洗，被剖析的应用程序应确保在 退出，所有的GPU工作都已完成（使用CUDA同步调用），然后 调用 cudaProfilerStop() 或 cuProfilerStop()。这样做会迫使缓冲profile 相应的上下文信息。

注意：要是没有调用stop的函数，会导致丢失大量profile 数据。

**还有另一种办法：使用 nvprof 选项 --timeout 或在 Visual Profiler 中设置 "Execution timeout"。分析器将在超时前强制进行数据刷新。**



# Visual Profiler的使用

NVIDIA Visual Profiler 可让您将应用程序的性能可视化并进行优化。

Visual Profiler 的独立版本，即 **nvvp**，包含在所有支持的操作系统的 CUDA 工具包中。



## session

会话包含与您的应用程序关联的设置，数据和结果。

session文件有两种：

1. 直接用visual Profiler生成的
2. 直接用nvprof来生成



### timeline view(时间轴)

- [ ] 如何使用时间轴



### 分析窗口

分析视图用于控制应用程序分析并显示分析结果。 有两种分析模式：引导模式和非引导模式。 在引导模式下进行分析系统将指导您完成多个分析阶段，以帮助您了解应用程序中可能存在的性能限制因素和优化机会。 在非引导模式下，您可以手动浏览为您收集的所有分析结果应用。



### PC采样窗口





### 内存分析

绿色节点表示逻辑存储空间，而蓝色表示芯片的实际硬件单元



### 源码和汇编的分析

注意：To be able to view the kernel source you need to compile the code using the **-lineinfo **option.



### GPU详细窗口





### CPU窗口



# nvprof的使用

 使用nvprof分析工具，您可以从命令行收集和查看分析数据。 nvprof支持在CPU和GPU上收集与CUDA相关的活动的时间表，包括**内核执行，内存传输，内存集和CUDA API调用以及CUDA内核**的事件或指标。 提供了分析选项，通过命令行选项访问nvprof。 收集分析数据后，分析结果将显示在控制台中，也可以保存以供以后由nvprof或Visual Profiler查看。

```shell
Usage: nvprof [options] [application] [application-arguments]
```



## profile模式



### 概要模式

摘要模式是nvprof的默认操作模式。 在此模式下，nvprof输出每个内核功能和每种CUDA内存复制/类型的单个结果行设置由应用程序执行。 对于每个内核，nvprof输出的总时间为内核或内存副本类型的所有实例以及平均值，最小值，和最长的时间。 内核时间是设备上内核执行时间。默认情况下，nvprof还会打印所有CUDA运行时/驱动程序API调用的摘要。



### GPU-跟踪和API模式

可以单独或一起启用GPU-Trace和API-Trace模式。 GPU跟踪模式提供了按时间顺序显示GPU上发生的所有活动的时间表。 输出中显示了每个内核执行和内存复制/设置实例。 对于每个内核或内存副本，将显示详细信息，例如内核参数，共享内存使用率和内存传输吞吐量。 内核名称后方括号中显示的数字与启动该内核的CUDA API相关。**--print-gpu-trace**

```shell
nvprof --print-gpu-trace ./vectorAdd
[Vector addition of 50000 elements]
==7522== NVPROF is profiling process 7522, command: ./vectorAdd
Copy input data from the host memory to the CUDA device
CUDA kernel launch with 196 blocks of 256 threads
Copy output data from the CUDA device to the host memory
Test PASSED
Done
==7522== Profiling application: ./vectorAdd
==7522== Profiling result:
   Start  Duration            Grid Size      Block Size     Regs*    SSMem*    DSMem*      Size  Throughput  SrcMemType  DstMemType           Device   Context    Stream  Name
330.01ms  16.992us                    -               -         -         -         -  195.31KB  10.962GB/s    Pageable      Device  GeForce GTX 108         1         7  [CUDA memcpy HtoD]
330.05ms  16.416us                    -               -         -         -         -  195.31KB  11.347GB/s    Pageable      Device  GeForce GTX 108         1         7  [CUDA memcpy HtoD]
330.08ms  2.7520us            (196 1 1)       (256 1 1)         8        0B        0B         -           -           -           -  GeForce GTX 108         1         7  vectorAdd(float const *, float const *, float*, int) [111]
330.09ms  16.128us                    -               -         -         -         -  195.31KB  11.549GB/s      Device    Pageable  GeForce GTX 108         1         7  [CUDA memcpy DtoH]

Regs: Number of registers used per CUDA thread. This number includes registers used internally by the CUDA driver and/or tools and can be more than what the compiler shows.
SSMem: Static shared memory allocated per CUDA block.
DSMem: Dynamic shared memory allocated per CUDA block.
SrcMemType: The type of source memory accessed by memory operation/copy
DstMemType: The type of destination memory accessed by memory operation/copy

```



### 事件和metric模式

>nvprof is able to collect multiple events/metrics at the same time. 



```c++
Q:How do you get a detailed profile of CUDA kernel?
A:When profiling I use this :
nvprof --kernels "kernelName" --metrics "listOfMetrics" your application
Now to the useful metrics:
eligible_warps_per_cycle
achieved_occupancy
sm_efficiency,
alu_fu_utilization,
dram_utilization,
inst_replay_overhead,
gst_transactions_per_request,
l2_utilization,
gst_requested_throughput,
flop_count_dp,
gld_transactions_per_request,
global_cache_replay_overhead,
flop_dp_efficiency,
gld_efficiency,
gld_throughput,
l2_write_throughput,
l2_read_throughput
```

>To collect all events available on each device, use the option --events all.
>To collect all metrics available on each device, use the option --metrics all.



### event/metric 跟踪模式



## profiling控制



### Unified Memory Profiling





### CPU Thread Tracing





## 输出



### 重定向输出

```
--log-file %1 ： output to stdout
--logfile <filename> : ouput to file
```



## Dependency Analysis



## CPU采样





# 远程prifiling

两种远程profiler的方式

- 配置nsight或visual prolifer
- 使用nvprof来收集数据，然后用nvvp来展示



## visual profier 远程profiler

>To use the Visual Profiler remote profiling you must install the same version of the CUDA Toolkit on both the host and remote systems. 



## 远程nvprof



# NVTX

NVIDIA Tools Extension（NVTX）是基于C的应用程序编程接口（API），用于注释应用程序中的事件，代码范围和资源。 集成了NVTX的应用程序可以使用Visual Profiler捕获和可视化这些事件和范围。 NVTX API提供两项核心服务：

1. 追踪CPU事件和时间范围
2. os的名字以及cuda的资源



## API浏览



- [ ] 这个是怎么用呢？有什么用处呢？



# MPI profiler

[博客中的例子](https://devblogs.nvidia.com/gpu-pro-tip-track-mpi-calls-nvidia-visual-profiler/)



# MPS profiler

- [ ] 如何使用



# metrics profiler



## 3.x计算力的metrics



| achieved_occupancy              | 每个活动cycle的平均活动warp数与一个多处理器上支持的最大扭曲数之比 |      |
| ------------------------------- | ------------------------------------------------------------ | ---- |
| alu_fu_utilization              | 执行整数和浮点算术指令的多处理器功能单元的利用率级别（范围0-10） |      |
| atomic_replay_overhead          | 由于执行的每条指令的原子和reduction bank冲突导致的平均重播次数 |      |
| atomic_throughput               | 全局内存原子和reduction的吞吐量                              |      |
| atomic_transactions             | 全局内存原子和reduction的事务                                |      |
| atomic_transactions_per_request | 每个原子和约简指令执行的全局内存原子和约简事务的平均数量     |      |



- [ ] 针对于不同的计算能力有不同的metrics



# warp状态

- 指令发射状态：一条指令或一对独立的指令是由warp发出的。

- 停滞：在**PC采样视图**中的源级或内核级的延迟分析中使用 "Examine Stall Reasons "可以看到滞留原因的分布。

  - 停滞不前的指令提取----下一条指令还没来得及发出。
    - 如果内核中的大循环已经被解压，请尝试减少它们。
    - 如果内核中包含许多小函数的调用，尝试内联更多的小函数。用 __inline__ 或 __forceinline__ 修饰符。反之，如果内联许多函数或大型函数，请尝试__noinline__来禁用内嵌式的这些函数。
    - 对于非常短的内核，考虑融合成一个单一的内核。
    - 如果使用较少的线程块，考虑使用更少的线程块。线程。偶尔调用 __syncthreads()将使经线程保持同步。这可能会提高指令缓存的命中率。

- 因执行依赖性而停滞 - --下一条指令正在等待一个或多个输入被前面的指令计算。

  为了减少执行依赖性停顿，可以尝试增加指令级并行化（ILP）。例如，可以通过增加循环解卷或每个线程处理几个元素来实现。这可以防止线程通过每条指令的全部延迟来防止线程闲置。

- 因内存依赖而停滞--下一条指令正在等待前一条内存访问完成。

  - 试着提高内存凝聚力和/或读取字节的效率。(对齐等)。看源头层面的分析 "全局内存访问"。
    模式 "和/或指标gld_efficiency和gst_efficiency。
  - 尝试增加内存级并行度（MLP）：内存级并行度（MLP）的数量。每个线程飞行中的独立内存操作。循环开卷。加载向量类型，如float4，并对每个元素进行多元素处理。线程都是增加内存级并行性的方法。
  - 考虑将经常访问的数据移到更接近SM，例如通过使用 共享内存或只读数据缓存。
  - 考虑尽可能地重新计算数据，而不是从 设备内存。
  - 如果本地存储器访问量大，可考虑增加每个寄存器的数量。线路，以减少溢出，即使是以占用为代价，因为当地的 内存访问只缓存在L2中，对于具有计算能力的GPU来说，内存访问只缓存在L2中。主版本=5。

- 因内存节流而停滞不前 - --大量未完成的内存请求阻碍了前向进展。在具有计算能力major = 3的GPU上，内存节流表示内存重播数量较多。

- 纹理停滞--纹理子系统已被充分利用或有太多的未完成请求。

- 停滞不前的同步----warp在同步指令后等待所有线程同步。

- 因持续依赖内存而停滞不前

- 因管道繁忙而停滞----因为执行下一条指令所需的功能单元忙，所以warp停滞。

- 停滞的原因是未被选中--warp已经准备好了，但由于其他warp被选中发行，所以没有机会发行。这个原因一般说明内核可能优化得很好，但在某些情况下，你可以在不影响延迟隐藏的情况下降低占用率，这样做可能有助于提高缓存命中率。



- [ ] 如何分析一个kernel函数？







