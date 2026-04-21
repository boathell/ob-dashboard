# Setup And Maintenance Notes

本文件承接仓库的工程细节，包含真实环境约束、Grafana 导入前提和后续维护规则。

## 维护规则

- `ocp_reference_pages_*.md` 主要用于生成第一版 Panel / Dashboard JSON。
- 第一版生成完成后，后续联调、修正、验证、重写都必须以真实环境为准：
  - 真实 endpoint
  - 真实 metrics 输出
  - 真实 metric 名
  - 真实 label
  - 真实 VictoriaMetrics / Grafana 查询结果
- 当参考文档与真实环境冲突时，以真实环境为主，不再强行回贴文档表达式。

## 当前已验证环境

- Prometheus 兼容数据源：VictoriaMetrics
- Grafana 数据源 UID：`VictoriaMetrics`
- Grafana 版本：已在 Grafana 13 验证导入与查询，JSON 保持 Grafana 9.x 兼容格式
- 真实集群名：`obcluster_pri`
- 真实租户名：`tenant_mysql`
- 真实 label：
  - `cluster`: `ob_cluster_name`
  - `zone`: `obzone`
  - `tenant`: `tenant_name`
- 已确认差异：
  - 当前环境中 `tenant_name` 可用
  - 当前环境中 `ob_tenant_name` 不可用

## Endpoint 矩阵

以下端点来自当前环境实际验证与官方说明的交集：

| Endpoint | 推荐频率 | 内容 |
| --- | --- | --- |
| `http://<ip>:62889/metrics/node/host` | 1 ~ 15 秒 | 主机监控，来源于 node_exporter 主机指标 |
| `http://<ip>:62889/metrics/node/ob` | 1 ~ 15 秒 | OceanBase 所在主机层指标、observer 进程状态、错误日志等 |
| `http://<ip>:62889/metrics/ob/basic` | 1 ~ 15 秒 | OceanBase 系统表基础指标 |
| `http://<ip>:62889/metrics/node/obproxy` | 1 ~ 15 秒 | OBProxy 主机层与进程层指标 |
| `http://<ip>:62889/metrics/obproxy` | 1 ~ 15 秒 | OBProxy 业务指标，来源于 `http://localhost:2884/metrics` |
| `http://<ip>:62889/metrics/ob/extra` | 1 分钟 | OceanBase 系统表扩展指标 |
| `http://<ip>:62888/metrics/stat` | 1 分钟 | `ocp_mgragent` 自监控 |
| `http://<ip>:62889/metrics/stat` | 1 分钟 | `ocp_monagent` 自监控与采集流水线监控 |

如果这些端点没有被采集，对应 dashboard 即使 PromQL 正确也会返回空数据。

## 变量与 PromQL 约定

当前变量约定：

- `$cluster`
  - label: `ob_cluster_name`
- `$zone`
  - label: `obzone`
  - 支持多选
- `$tenant`
  - label: `tenant_name`
  - 支持多选
- `$interval`
  - 采样间隔，用于 `rate()`

PromQL 模板替换规则：

| 模板 | 替换为 |
| --- | --- |
| `@LABELS` | `ob_cluster_name="$cluster", obzone=~"$zone"` |
| `@GBLABELS` | `ob_cluster_name, obzone` |
| `@INTERVAL` | `$interval` |

说明：

- 当前仓库多数 Panel 只按集群和 Zone 聚合，没有直接把 `$tenant` 写进 PromQL。
- 如果后续生成租户维度 Dashboard，tenant 相关变量与查询应优先使用 `tenant_name`，而不是 `ob_tenant_name`。

## Dashboard 导入建议

1. 导入前先确认采集链路覆盖 dashboard 依赖的 endpoint。
2. Grafana 数据源 UID 必须为 `VictoriaMetrics`。
3. 导入后优先检查 `cluster`、`zone`、`tenant`、`interval` 变量是否能正确拉取值。
4. 如果导入成功但图表为空，先检查真实 metric 名和 label，而不是先回滚到文档里的历史表达式。

## 统一规格

Panel 默认遵循以下 Grafana 规格：

```json
{
  "type": "timeseries",
  "gridPos": {
    "h": 8,
    "w": 24,
    "x": 0,
    "y": 0
  },
  "datasource": {
    "type": "prometheus",
    "uid": "VictoriaMetrics"
  }
}
```
