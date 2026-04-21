# OceanBase Grafana Dashboards

这个仓库提供可直接导入 Grafana 的 OceanBase 监控 dashboard JSON，数据源面向 VictoriaMetrics / Prometheus 兼容接口。

## 包含内容

仓库当前提供 7 份 dashboard：

| Dashboard | 文件 | 说明 |
| --- | --- | --- |
| Host Infra | `dashboards/dashboard_host_infra.json` | 主机基础设施指标，依赖 `/metrics/node/host` |
| Observer Node | `dashboards/dashboard_observer_node.json` | Observer 节点进程与错误日志，依赖 `/metrics/node/ob` |
| OceanBase Database | `dashboards/dashboard_oceanbase_database.json` | OceanBase 数据库核心指标，依赖 `/metrics/ob/basic` 与 `/metrics/ob/extra` |
| OBProxy | `dashboards/dashboard_obproxy.json` | OBProxy 业务与节点状态，依赖 `/metrics/obproxy` 与 `/metrics/node/obproxy` |
| OCP Agent | `dashboards/dashboard_ocp_agent.json` | OCP Agent 自监控，依赖 `:62888/metrics/stat` 与 `:62889/metrics/stat` |
| Binlog | `dashboards/dashboard_ob_binlog.json` | Binlog 专项监控，依赖实际环境支持 |
| OBKV HBase | `dashboards/dashboard_obkv_hbase.json` | OBKV HBase 专项监控，依赖实际环境支持 |

## 快速开始

1. 在 Grafana 中创建 Prometheus 兼容数据源，UID 使用 `VictoriaMetrics`。
2. 确认采集链路覆盖所需 endpoint，而不是只抓 `/metrics/ob/basic`。
3. 从 `dashboards/` 选择需要的 JSON 文件导入 Grafana。
4. 导入后设置变量 `cluster`、`zone`、`tenant`、`interval` 并验证查询返回数据。

## 使用约束

- 第一版 dashboard 可以参考 OCP 文档生成。
- 后续联调、修正、重写必须以真实环境为准，包括真实 endpoint、metric 名、label 和查询结果。
- 当前环境中租户 label 以 `tenant_name` 为准，不应默认回退到 `ob_tenant_name`。

## 详细说明

工程化部署、真实环境验证结果、endpoint 矩阵、变量约定和维护规则见 [SETUP.md](/Users/dingfeng/Documents/vibe-coding/ob-dashboard/SETUP.md)。
