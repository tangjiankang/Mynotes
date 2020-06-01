# prometheus的配置和服务发现

引用：[https://prometheus.io/docs/prometheus/latest/configuration/configuration/](https://prometheus.io/docs/prometheus/latest/configuration/configuration/)

资料[https://github.com/Alrights/prometheus](https://github.com/Alrights/prometheus)

Prometheus可以在运行时重新加载其配置。如果新配置格式不正确，则不会应用更改。通过向Prometheus进程发送`SIGHUP`或向`/-/reload`端点发送HTTP POST请求（启用`--web.enable-lifecycle`标志时）来触发配置reload。 这也将重新加载任何配置的规则文件。 prometheus使用的configmap修改后，k8s中的pod并不会自动重新读取，需要使用reload。 `curl -X POST "http://10.96.112.113:9090/-/reload"`

**全局配置指定在所有其他配置上下文中有效的参数。 它们也作为其他配置部分的默认值。**

* **global：全局配置**
* **alerting：告警配置**
* **rule\_files：告警规则**
* **scrape\_configs：配置数据源**，称为target，每个target用**job\_name**命名。又分为**静态配置和服务发现**

  \`\`\` global:

  **How frequently to scrape targets by default.**

  **默认情况下抓取目标的频率.**

  \[ scrape\_interval:  \| default = 1m \]

  **How long until a scrape request times out.**

  **抓取超时时间.**

  \[ scrape\_timeout:  \| default = 10s \]

  **How frequently to evaluate rules.**

  **评估规则的频率.**

  \[ evaluation\_interval:  \| default = 1m \]

  **The labels to add to any time series or alerts when communicating with**

  **external systems \(federation, remote storage, Alertmanager\).**

  **与外部系统通信时添加到任何时间序列或警报的标签**

  **（联合，远程存储，Alertmanager）.**

  external\_labels: \[ :  ... \]

## Rule files specifies a list of globs. Rules and alerts are read from

## all matching files.

## 规则文件指定了一个globs列表.

## 从所有匹配的文件中读取规则和警报.

rule\_files: \[ -  ... \]

* 'prometheus.rules'

## A list of scrape configurations.

## 抓取配置列表

scrape\_configs: \[ -  ... \]

## insecure\_skip\_verify: true

开启不验证证书。

## Alerting specifies settings related to the Alertmanager.

## 警报指定与Alertmanager相关的设置.

alerting: alert\_relabel\_configs: \[ -  ... \] alertmanagers: \[ -  ... \]

## Settings related to the remote write feature.

## 与远程写入功能相关的设置.

remote\_write: \[ -  ... \]

## Settings related to the remote read feature.

## 与远程读取功能相关的设置.

remote\_read: \[ -  ... \] \`\`\`

