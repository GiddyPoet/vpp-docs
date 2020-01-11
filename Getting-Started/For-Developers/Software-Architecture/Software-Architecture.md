## 软件架构

FD.io VPP是第三代矢量数据包处理的实现，具体与美国专利7,961,636及早期工作相关。请注意，Apache-2许可证专门授予非专有的专利许可证；我们将此专利作为历史关注点。

为了提高性能，vpp数据平面由转发节点的有向图组成，该转发图每次调用处理多个数据包。这种模式可实现多种微处理器优化：流水线和预取以覆盖相关的读取延迟，固有的I-cache阶段行为，矢量指令。除了硬件输入和硬件输出节点之外，整个转发图都是可移植代码。

根据当前的情况，我们经常启动多个工作线程，这些线程使用相同的转发图副本处理来自多个队列的ingress-hashes数据包。

### VPP层-实施分类法

![](https://github.com/penybai/vpp-docs/blob/master/images/vpp-layers.png)

* VPP Infra-VPP基础设施层，其中包含核心库源代码。该层执行存储功能，与向量和环配合使用，在哈希表中执行键查找，并与用于调度图节点的计时器一起使用。
* VLIB-向量处理库。vlib层还处理各种应用程序管理功能：缓冲区，内存和图形节点管理，维护和导出计数器，线程管理，数据包跟踪。 Vlib实现了调试CLI（命令行界面）。
* VNET-与VPP的网络接口（第2层，第3层和第4层）一起执行会话和流量管理，并与设备和数据控制平面一起使用。
* 插件-包含越来越丰富的数据平面插件集，如上图所示。
* VPP-与以上所有内容链接的容器应用程序。

重要的是要对每个层次都有一定的了解。最好在API级别处理大部分实现，剩下的就不去管它了。