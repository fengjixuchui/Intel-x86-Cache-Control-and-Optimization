# Intel x86 Cache Control and Optimization
Intel x86处理器的Cache控制与优化

<br />

# 第三卷第11章——存储器Cache控制

本章将描述Intel 64与IA-32处理器中的存储器Cache与Cache控制机制、TLB、以及存储缓存。这里也将描述存储器类型范围寄存器（**MTRRs**），这些寄存器在P6处理器家族中被引入，并且描述它们如何被用于控制对物理存储器位置进行高速缓存的。

<br />

## 11.1 内部Cache、TLB、以及缓存

Intel 64与IA-32架构支持Cache、翻译后备缓存（***translation look aside buffers***）（TLBs），还有一个存储缓存（***store buffer***）用于临时的对片上（和外部的）指令与数据的存储。（图11-1展示了用于奔腾4和Intel至强处理器的Cache、TLBs、以及存储缓存。）表11-1展示了用于奔腾4、Intel至强、P6家族以及奔腾处理器的Cache和缓存的特征。**这些单元的大小和特征是机器特定的，并且在处理器未来版本中可能会有所变化。** 当你使用CPUID指令时，该指令将会返回当前处理器的Cache和缓存的大小和特征。

<br />

![图11-1](https://github.com/zenny-chen/Intel-x86-Cache-Control-and-Optimization/blob/master/vol3_11-1.png)

<br />

![图11-2](https://github.com/zenny-chen/Intel-x86-Cache-Control-and-Optimization/blob/master/vol3_11-2.png)

<br />

图11-2展示了Intel Core i7处理器的Cache安排。

<br />

Intel 64和IA-32处理器可以实现四种类型的Cache：追踪Cache（***trace cache***），L1 Cache，L2 Cache，还有L3 Cache。见图11-1。Cache可用性如下描述：

• **基于Nehalem和Westmere Intel®微架构的Intel Core i7、i5、i3处理器家族以及Intel Xeon处理器家族** ——L1 Cache被划分为两个部分：一个部分专用于高速缓存指令（预解码的指令），另一部分专用于数据。L2 Cache是一个统一的数据和指令Cache。每个处理器核心有它自己的L1和L2 Cache。L3 Cache是一个内含的、统一的数据和指令Cache，由一个物理包内的所有处理器核心所共享。这些处理器架构没有实现追踪Cache。

• **基于Intel Core微架构的Intel Core 2处理器家族以及Intel Xeon处理器家族**——L1 Cache被划分为两部分：一部分是专用于高速缓存指令的（预解码的指令），另一部分用于高速缓存数据。L2 Cache是一个统一的数据和指令Cache，位于处理器片上；它在一个双核处理器实现中对两个处理器核心之间共享。四核处理器具有两个L2 Cache，每个L2 Cache被两个处理器核心共享。这些处理器架构没有实现追踪Cache。

• **Intel Atom处理器**——L1 Cache被划分为两部分：一部分是专用于高速缓存指令的（预解码的指令），另一部分用于高速缓存数据。L2 Cache是位于处理器片上的统一数据和指令Cache。该处理器架构没有实现追踪Cache。

• **Intel Solo以及Intel Core Duo处理器**——L1 Cache被划分为两部分：一部分是专用于高速缓存指令的（预解码的指令），另一部分用于高速缓存数据。L2 Cache是一个统一的数据和指令Cache，位于处理器片上。它在一个双核处理器实现中对两个处理器核心之间共享。这些处理器架构没有实现追踪Cache。

• **基于NetBurst微架构的Pentium 4与Intel Xeon处理器**——追踪Cache高速缓存来自指令译码器的已被译码的指令（即微操作，μops），而L1 Cache只包含数据。L2和L3 Cache是位于处理器片上的统一数据和指令Cache。双核处理器具有两个L2，每个核心独用一个。注意，L3 Cache只在某些Intel Xeon处理器上实现。

• **P6家族处理器**——L1 Cache被划分为两部分：一部分是专用于高速缓存指令的（预解码的指令），另一部分用于高速缓存数据。L2 Cache是位于处理器片上的统一数据和指令Cache。P6家族处理器没有实现追踪Cache。

• **Pentium处理器**——L1 Cache具有与P6家族相同的结构。同时没有实现追踪Cache。在早期的奔腾处理器上，L2 Cache是在处理器片外的统一数据和指令Cache，而在后期的奔腾处理器上则是处理器片内的统一数据和指令Cache。对于L2 Cache在处理器片外的奔腾处理器，它对Cache的访问通过系统总线。

对于基于Intel Core、Intel Atom、以及Intel NetBurst微架构的处理器以及Intel Core i7处理器，Intel Core Duo、Intel Core Solo以及Intel Pentium M处理器，L1和L2 Cache（还有L3 Cache，如果支持的话）的Cache行是64字节宽。处理器总是从起始于一个64字节边界的系统存储器读取一条Cache行。（一条64字节对齐的Cache行起始于一个地址，该地址的最低6位有效位被清零。）一条Cache行可以从存储器用一个8次传输的burst事务（即每次传输8个字节）进行填充。Cache并不支持部分填充的Cache行，因此即便要高速缓存一个双字（4字节）也需要高速缓存一整条Cache行。

<br />

# 第一卷10.4.6——Cache能力控制、预取、以及存储器次序指令

<br />

### 10.4.6 Cache能力控制、预取、以及存储器次序指令

<br />


# 架构优化手册第8章——优化Cache使用

在过去十年，处理器速度提升了。而存储器访问速度却以较慢的步伐前进。这种提升结果的不一致使得应用程序以下面两种方法的其中之一进行调优变得十分重要：(a)大部分的数据访问从处理器Cache中完成，或是(b)有效地掩盖掉存储器延迟，以尽可能多地利用峰值存储器带宽。

硬件预取机制对于利用方法(b)而言是微架构中的增强特性，并且当与软件调优结合使用时是最有效的。如果所需要的数据能够从处理器Cache中获取，或者如果存储器交通能有效地利用硬件预取，那么大部分应用程序的性能将获得相当大的提升。

在需要获得数据之前将它带入到处理器中的标准技术涉及到额外的编程，这可能会难以实现并且需要特殊步骤来防止性能下降。流式SIMD扩展指令集通过提供各种预取指令来解决这个问题。

流式SIMD扩展指令集引入了各种非临时存储指令。SSE2又对新的数据类型扩展了这种支持，并且还引入了非临时存储对32位整数寄存器的支持。

本章主要关注于：    
• 硬件预取机制、软件预取以及有Cache能力的指令——讨论允许你影响一个应用中对数据进行高速缓存的微架构特征和指令。    
• 使用硬件预取的存储器优化、软件预取以及有Cache能力的指令——讨论了使用上述指令实现存储器优化的技术。    
• 使用确定性的Cache参数来管理Cache层级。

<br />

## 8.1 通用的预取代码编写准则


