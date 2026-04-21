本项目基于 markdown 文档批量生成 Grafana JSON 。
你需要：
第一：遵循下面生成 dashboard 的环境说明文档，也就是第一个一级标题的内容；
第二：遵循单个 Panel 的生成 Prompt，去生成具体的Panel，也就是第二个一级标题的内容
第三：批量生成整个 Dashboard 的 Prompt，也就是第三个一级标题的内容

重要维护规则：

- `AGENTS.md` 中的环境说明和 `ocp_reference_pages_*.md` 主要用于**生成第一版** Panel / Dashboard JSON。
- 当第一版生成完成后，后续验证、联调、修正、重写时，必须以**真实环境**为准：
  - 真实 endpoint
  - 真实 metrics 输出
  - 真实 metric 名
  - 真实 label
  - 真实 VictoriaMetrics / Grafana 查询结果
- 如果本文档中的示例、历史假设或参考页与真实环境冲突，以真实环境为主。
- 例如：
  - tenant label 不再固定假设为 `ob_tenant_name`
  - endpoint 不再固定假设只抓 `:8088/metrics/ob/basic`
  - OBProxy 组不再强制假设一定存在文档里的 `odp_*` 查询面
---
# obsty OceanBase 监控环境说明

## 数据源
- Prometheus 兼容数据源：VictoriaMetrics，datasource name = "VictoriaMetrics"
- MySQL 数据源（用于查询 OB 内部视图）：datasource name = "OceanBase"

## OBAgent metrics 的实际 label 名称
（这里填你从真实 exporter 端点看到的 label；不要再默认假设是 `:8088/metrics/ob/basic`）
例如：
- ob_cluster_name：集群名
- ob_tenant_id：租户 ID  
- tenant_name 或 ob_tenant_name：租户名，以真实环境为准
- svr_ip：节点 IP
- obzone：Zone 名称

## Grafana 变量（Variables）
Dashboard 统一使用以下变量，PromQL 中用这些变量做过滤：
- $cluster：集群名，对应 label ob_cluster_name
- $zone：Zone 名，对应 label obzone，可多选
- $tenant：租户名，对应真实环境中的 tenant label，可多选
- $interval：采样间隔，用于 rate() 函数，固定值如 1m

## PromQL 模板替换规则
文档中的 @LABELS 替换为：
  ob_cluster_name="$cluster", obzone=~"$zone"
文档中的 @GBLABELS 替换为：
  ob_cluster_name, obzone
文档中的 @INTERVAL 替换为：
  $interval

## Grafana JSON 版本
使用 Grafana 9.x 兼容的 JSON 格式（不使用已废弃的 graph panel，使用 timeseries panel）


---
[引用上面的环境说明]

现在帮我生成一个 Grafana Panel 的 JSON 片段。

## Panel 要求

**Panel 名称**：OceanBase 集群 QPS 分类

**图表类型**：timeseries（折线图），多条线叠加，不堆叠

**数据系列**（每个系列一条折线）：
| 系列名称 | PromQL 表达式（来自官方文档） |
|---------|--------------------------|
| SELECT  | sum(rate(ob_sysstat{stat_id="40000",ob_cluster_name="$cluster",obzone=~"$zone"}[$interval])) by (ob_cluster_name) |
| INSERT  | sum(rate(ob_sysstat{stat_id="40002",ob_cluster_name="$cluster",obzone=~"$zone"}[$interval])) by (ob_cluster_name) |
| UPDATE  | sum(rate(ob_sysstat{stat_id="40006",ob_cluster_name="$cluster",obzone=~"$zone"}[$interval])) by (ob_cluster_name) |
| DELETE  | sum(rate(ob_sysstat{stat_id="40008",ob_cluster_name="$cluster",obzone=~"$zone"}[$interval])) by (ob_cluster_name) |
| REPLACE | sum(rate(ob_sysstat{stat_id="40004",ob_cluster_name="$cluster",obzone=~"$zone"}[$interval])) by (ob_cluster_name) |
| OTHER   | sum(rate(ob_sysstat{stat_id="40018",ob_cluster_name="$cluster",obzone=~"$zone"}[$interval])) by (ob_cluster_name) |

**Y 轴单位**：times/s（Grafana 单位选 "short" 或 "reqps"）

**图例**：显示在底部，格式为系列名称

**尺寸**：宽 24（满宽），高 8

请只输出这个 Panel 的 JSON 对象，不要输出完整 Dashboard JSON，不要有任何解释文字。

---

[引用环境说明]

帮我生成一个完整的 Grafana Dashboard JSON。

## Dashboard 基本信息
- title: "OceanBase 集群概览"
- uid: "obsty-ob-overview"
- 包含以下变量（Variables）：
  - cluster：Query 变量，查询所有集群名，PromQL = label_values(ob_sysstat, ob_cluster_name)
  - zone：Query 变量，查询当前集群的 Zone，PromQL = label_values(ob_sysstat{ob_cluster_name="$cluster"}, obzone)，支持多选，默认 All
  - tenant：Query 变量，查询当前集群的租户，PromQL 以真实环境中的 tenant label 为准，支持多选，默认 All
  - interval：Interval 变量，可选值 30s、1m、5m，默认 1m

## 布局（从上到下，每行描述）

**第一行：关键指标 Stat 卡片**（每个宽 4，高 3，共 6 个）
1. 集群总 QPS：表达式用 all 的计算表达式
2. SELECT QPS
3. INSERT QPS  
4. UPDATE QPS
5. DELETE QPS
6. 事务回滚率：rollback/(commit+rollback)*100，单位 %

**第二行：QPS 趋势**（满宽，高 8）
- Panel 名称：SQL 执行趋势
- 类型：timeseries，堆叠面积图（stack: normal）
- 包含 SELECT/INSERT/UPDATE/DELETE/REPLACE/OTHER 六条线

**第三行：事务指标**（各宽 12，高 8）
- 左：Commit/Rollback 趋势（timeseries）
- 右：事务回滚率趋势（timeseries，单位 %）

## 所有 PromQL 表达式
[把你从官方文档提取的所有表达式粘贴在这里]

## 输出要求
- 输出完整可导入的 Dashboard JSON
- JSON 中的 id 设为 null，version 设为 1
- 不要有任何解释文字，只输出 JSON

---

