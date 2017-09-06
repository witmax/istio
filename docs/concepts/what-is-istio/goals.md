# 设计目标

本节概述指导Istio设计的核心原则。

Istio的架构有几个关键设计目标，这些目标对于使系统能够大规模和高性能地处理服务至关重要。

- **最大化透明度**.

要采用Istio，运维人员或开发人员应该被要求尽可能做最少的工作，以获得系统的真正价值。为此，Istio可以自动将自身注入到服务之间的所有网络路径中。Istio使用sidecar代理来捕获流量，并且在可能的地方自动编程网络层，以通过这些代理路由流量，而不对所部署的应用程序代码进行任何修改。在Kubernetes中，代理被注入到pod中，流量是通过编码iptables规则来捕获的。一旦sidecar代理被注入并且流量路由被编程，Istio能够调解所有流量。这个原则也适用于性能。当将Istio应用于部署时，运维人员应该看到用于提供功能的资源成本增长很小。所有组件和API在设计时都必须设考虑到性能和规模。

- **增量**.

随着运维人员和开发人员越来越依赖Istio提供的功能，系统必须随着他们的需求而增长。虽然我们期望自己继续添加新功能，但我们预计最大的需求将是扩展策略系统，与其他政策和控制来源整合的能力，并将关于网格行为的信号传播到其他系统进行分析。策略运行时支持插入其他服务的标准扩展机制。此外，它允许扩展其词汇表，以允许基于网格生成的新信号来执行策略。

- **便携性**.

使用Istio的生态系统将在许多方面有所不同。Istio必须以最少的努力运行在任何云或预置环境中。将基于Istio的服务移植到新环境的任务应该是微不足道的，应该可以使用Istio操作部署到多个环境中的单个服务（例如，在多个云上进行冗余）。

- **策略一致性**.

服务之间的API调用的策略应用提供了对网格行为的大量控制，但将策略应用于不必在API级别表达的资源也同样重要。例如，将配额应用于由ML训练任务消耗的CPU数量比对启动工作的调用应用配额更为有用。为此，将策略系统作为具有自己的API的独特服务来维护，而不是放到代理/sidecar中，使服务能够根据需要直接集成。