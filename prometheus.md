### prometheus


Prometheus 是一个开源的系统监控和警报工具包，最初由SoundCloud开发，并于2012年公开发布。Prometheus 是一个社区驱动的项目，并且在CNCF（云原生计算基金会）中作为一个孵化项目被维护。它设计用于高维数据模型，可以处理非常大量的时间序列数据，并且具备灵活的查询能力、良好的性能和可靠的存储特性。


2. 基本概念
2.1 时间序列数据库
Prometheus 主要用于存储时间序列数据，每个时间序列由唯一的名称和一组标签（键值对）标识。时间序列数据库允许高效地存储和检索大量时间序列数据，并支持对这些数据进行复杂的查询和分析。

2.2 指标（Metrics）
指标是Prometheus监控系统的核心，它们表示一组测量值。Prometheus 支持四种类型的指标：

Counter：一个单调递增的计数器，只有增量操作。例如，HTTP请求的数量。
Gauge：一个可以任意增减的数值。例如，当前内存使用量。
Histogram：记录值的分布情况，并进行分桶统计。例如，请求延迟的分布。
Summary：类似于Histogram，但更侧重于分位数计算。
2.3 标签（Labels）
标签是Prometheus数据模型中的关键概念，通过标签，用户可以对同一指标的不同维度进行细分。例如，HTTP请求数量可以按路径、状态码等维度进行区分。

2.4 抓取（Scraping）
Prometheus通过HTTP请求抓取目标端点（通常是/exporter暴露的端点）来收集指标数据。抓取操作会定期进行，以确保数据的实时性。

3. 架构
Prometheus 的架构设计非常模块化，主要包括以下组件：

3.1 Prometheus 服务器
Prometheus 服务器负责抓取和存储时间序列数据。它定期从配置的目标端点抓取指标数据，并将这些数据存储在本地存储中。Prometheus 服务器还提供强大的查询引擎，允许用户使用PromQL进行复杂的数据分析。

3.2 Exporters
Exporters 是用于暴露指标数据的程序。常见的exporters包括Node Exporter（用于监控操作系统级别的指标）、Cadvisor（用于监控容器）、Blackbox Exporter（用于探测服务的可用性）等。每个exporter通常会在一个特定的端点上暴露指标数据。

3.3 Pushgateway
Pushgateway 用于临时性或批量作业的指标收集。通常情况下，Prometheus 是通过抓取目标来获取数据的，但对于一些短命任务，Pushgateway 提供了一种推送数据的方式。

3.4 Alertmanager
Alertmanager 负责接收Prometheus发送的告警，进行去重、分组、路由和通知。它支持多种通知方式，如电子邮件、Slack、PagerDuty等。

3.5 客户端库
Prometheus 提供了多种编程语言的客户端库，方便开发者在应用程序中直接暴露自定义的指标数据。常见的客户端库包括Go、Java、Python等。

3.6 Service Discovery
Prometheus 支持多种服务发现机制，如Kubernetes、Consul、Etcd等，使其可以动态地发现监控目标，适应高度动态的云原生环境。

4. 数据模型
Prometheus 数据模型基于时间序列，每个时间序列由一个度量名称和一组标签标识。时间序列数据模型的基本元素包括：

4.1 度量名称（Metric Name）
度量名称是时间序列的基本标识符，用于描述数据的意义。比如，http_requests_total 表示HTTP请求总数。

4.2 标签（Labels）
标签是键值对，附加在度量名称上，用于进一步区分数据。例如，http_requests_total{method="GET", handler="/api"}。

4.3 时间戳（Timestamp）
每个时间序列点（样本）都有一个时间戳，表示该数据点的时间。

4.4 值（Value）
值是时间序列点的具体数值，通常为浮点数。

4.5 示例
plaintext
复制代码
http_requests_total{method="GET", handler="/api"}  1027  1622549160
表示在时间戳 1622549160 时，/api 端点的GET请求总数为1027。

5. 监控类型
Prometheus 支持多种类型的监控，包括：

5.1 基础设施监控
通过Node Exporter和Cadvisor等exporters，可以监控主机和容器的资源使用情况，如CPU、内存、磁盘、网络等。

5.2 应用程序监控
使用客户端库，可以在应用程序中嵌入自定义指标，监控应用的内部状态和性能。例如，HTTP请求响应时间、数据库查询时间等。

5.3 服务可用性监控
通过Blackbox Exporter，可以监控服务的可用性和响应时间。它可以模拟HTTP请求、Ping、DNS查询等，检查服务的健康状态。

5.4 业务指标监控
Prometheus 不仅适用于技术指标的监控，还可以用于业务指标的监控。例如，电商网站的订单数量、支付成功率等。

6. Prometheus 安装与配置
6.1 安装
Prometheus 提供了多种安装方式，包括从二进制文件、Docker镜像和Kubernetes Helm Chart等。

6.2 基本配置
Prometheus 的配置文件通常为 prometheus.yml，它包含抓取配置、告警规则和服务发现等信息。

示例配置：
yaml
复制代码
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'node'
    static_configs:
      - targets: ['localhost:9100']
6.3 服务发现配置
Prometheus 支持多种服务发现机制，如Kubernetes、Consul、DNS等。以下是Kubernetes服务发现配置示例：

yaml
复制代码
scrape_configs:
  - job_name: 'kubernetes-apiservers'
    kubernetes_sd_configs:
      - role: endpoints
7. 实例管理与服务发现
Prometheus 提供了灵活的实例管理和服务发现机制，适应云原生环境中的动态变化。

7.1 静态配置
最简单的配置方式是静态配置，将所有目标显式地列在配置文件中。适用于小规模或变化不频繁的环境。

7.2 动态服务发现
对于大规模或高度动态的环境，Prometheus 支持多种动态服务发现机制，如Kubernetes、Consul、DNS等。动态服务发现可以自动发现新的监控目标，并调整抓取配置。

8. Prometheus 查询语言（PromQL）
Prometheus 提供了强大的查询语言PromQL，用于对时间序列数据进行检索和分析。PromQL 支持多种查询操作，如聚合、过滤、计算等。

8.1 基本查询
promql
复制代码
http_requests_total
返回所有 http_requests_total 时间序列。

8.2 条件过滤
promql
复制代码
http_requests_total{method="GET"}
返回所有 method 为 GET 的 http_requests_total 时间序列。

8.3 聚合操作
promql
复制代码
sum(rate(http_requests_total[5m]))
计算每秒的HTTP请求总数，时间窗口为5分钟。

8.4 复杂查询示例
promql
复制代码
sum by (handler) (rate(http_requests_total[5m]))
按 handler 维度，计算每秒的HTTP请求数。

9. 告警管理
Prometheus 提供了强大的告警功能，允许用户根据监控数据定义告警规则，并通过Alertmanager进行告警管理和通知。

9.1 告警规则
告警规则定义在Prometheus的配置文件中，当监控数据满足告警条件时，生成告警。

示例告警规则：
