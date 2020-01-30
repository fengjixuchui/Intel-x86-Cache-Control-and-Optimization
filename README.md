# Intel x86 Cache Control and Optimization
Intel x86处理器的Cache控制与优化

<br />

# 第三卷第11章——存储器Cache控制

本章将描述Intel 64与IA-32处理器中的存储器Cache与Cache控制机制、TLB、以及存储缓存。这里也将描述存储器类型范围寄存器（**MTRRs**），这些寄存器在P6处理器家族中被引入，并且描述它们如何被用于控制对物理存储器位置进行高速缓存的。

<br />

## 11.1 内部Cache、TLB、以及缓存

Intel 64与IA-32架构支持Cache、翻译后备缓存（***translation look aside buffers***）（TLBs），还有一个存储缓存（***store buffer***）用于临时的对片上（和外部的）指令与数据的存储。（图11-1展示了用于奔腾4和Intel至强处理器的Cache、TLBs、以及存储缓存。）表11-1展示了用于奔腾4、Intel至强、P6家族以及奔腾处理器的Cache和缓存的特征。**这些单元的大小和特征是机器特定的，并且在处理器未来版本中可能会有所变化。** 当你使用CPUID指令时，该指令将会返回当前处理器的Cache和缓存的大小和特征。


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


