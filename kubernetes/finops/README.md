# FinOps

>FinOps Foundation: https://www.finops.org

FinOps 是一种不断发展的云财务管理学科和文化实践，通过帮助工程、财务、技术和业务团队在数据驱动的预算分配上进行协作，使组织能够获得最大的业务价值。


## 我曾接触过的云原生财务工作

在2021 年下半年一直到现在，我一直在参与公司的成本分析与管控工作。
我们通过 AWS CUR 账单、Kubernetes 集群的 Spot 节点池伸缩组件等多个维度获取成本数据，并依靠不断完善的资源标签机制，对成本进行细致的分析，然后输出各个维度的成本分析报表：

- 总成本趋势报表
- 各业务团队的成本报表
- 以服务为维度进行拆分的 K8s 计算成本/网络成本报表
- 更细致的成本分析报表 - 用于分析成本异常，免去手写 SQL 的烦恼

数据保存为 csv 格式，使用 Google BigQuery 或 AWS Athena 查询分析，通过 Grafana 或者 Google Data Studio 等报表工具，构建出易于理解的交互式成本报表。

对这些报表的分析，都依赖于我们成本标签的正确性。我们需要确保大部分成本对应的资源标签都是正确的。
可以定一个阈值比如 80%，一旦低于这个阈值，就会触发我们的资源标签修复工作。
另外在人员变动过组织结构变化时，我们也需要及时更新对应的资源标签。

我做过的成本工作的几个目标：

- 分析并归因成本变化
  - 在发现成本有预期外的上升或下降趋势时，分析并归因这些成本变化，在必要时跟各个业务团队确认这些变化是否符合预期。
- 持续优化成本
  - 分析现有成本中存在哪些优化点，估算能节约多少成本，ROI 多高。
  - 在实施了某项成本优化工作后，确定其效益。


上述这些都是我司以及我个人在成本工作上的一些方法论以及最佳实践。
我觉得我们也算是走在 FinOps 这条路上，我们搭建了自己的成本服务、资源标签管理服务，构建了许多有用的成本分析报表，而且这一套机制确实起到了不错的效果。

不过学一学业界的最佳实践，学一学 FinOps 的方法论、文化、组织架构，我觉得对我们也是有帮助的，这就是我为什么对 FinOps 感兴趣的原因。

