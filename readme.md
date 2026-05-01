# OceanBase Grafana Dashboards

这个仓库提供可直接导入 Grafana 的 OceanBase 监控 dashboard JSON，数据源面向 VictoriaMetrics / Prometheus 兼容接口。向 Pigsty 致敬，借鉴了该项目的想法。祝贺 Pigsty 突破 5k Star。

## 包含内容

仓库当前提供 11 份 dashboard，覆盖性能监控分类：数据库性能、主机性能、OBServer 性能。

| Dashboard | 文件 | 维度 | 说明 |
| --- | --- | --- | --- |
| 主机 & Observer 节点 | `dashboards/dashboard_observer_node.json` | 主机/节点 | 主机资源（CPU/内存/磁盘/网络）+ Observer 进程 + 错误日志，依赖 `/metrics/node/host`、`/metrics/node/ob` |
| OceanBase 数据库概览 | `dashboards/dashboard_oceanbase_database.json` | 集群 | OceanBase 数据库核心指标，依赖 `/metrics/ob/basic` 与 `/metrics/ob/extra` |
| OBProxy | `dashboards/dashboard_obproxy.json` | 集群 | OBProxy 业务与节点状态，依赖 `/metrics/obproxy` 与 `/metrics/node/obproxy` |
| OCP Agent | `dashboards/dashboard_ocp_agent.json` | 集群 | OCP Agent 自监控，依赖 `:62888/metrics/stat` 与 `:62889/metrics/stat` |
| Binlog | `dashboards/dashboard_ob_binlog.json` | 集群 | Binlog 专项监控，依赖实际环境支持 |
| OBKV HBase | `dashboards/dashboard_obkv_hbase.json` | 集群 | OBKV HBase 专项监控，依赖实际环境支持 |
| 告警响应 | `dashboards/ob-alert.json` | 集群 | 告警处置首选，Last 1h，30s 刷新，ROW 按 SQL/存储/网络/Clog 分层 |
| 日常巡检 | `dashboards/ob-inspect.json` | 集群/租户 | 趋势优先，Last 24h，5min 刷新，含节点健康矩阵 |
| 容量规划 | `dashboards/ob-capacity.json` | 集群 | Last 30d，1h 刷新，predict_linear 存储外推 |
| 数据库性能 · 租户维度 | `dashboards/ob-tenant.json` | 租户 | 所有指标按 `tenant_name` 下钻，覆盖 SQL/事务/资源/存储/日志 |
| 数据库性能 · Unit 维度 | `dashboards/ob-unit.json` | tenant × svr_ip | 以 `tenant_name × svr_ip` 近似 unit 粒度，覆盖 CPU/内存/SQL/IO |

## 快速开始

1. 在 Grafana 中创建 Prometheus 兼容数据源，UID 使用 `VictoriaMetrics`。
2. 确认采集链路覆盖所需 endpoint，而不是只抓 `/metrics/ob/basic`。
3. 从 `dashboards/` 选择需要的 JSON 文件导入 Grafana。
4. 导入后设置变量 `cluster`、`zone`、`tenant`、`interval` 并验证查询返回数据。

## 使用约束

- 第一版 dashboard 可以参考 OCP 文档生成。
- 后续联调、修正、重写必须以真实环境为准，包括真实 endpoint、metric 名、label 和查询结果。
- 当前环境中租户 label 以 `tenant_name` 为准。

## 详细说明

工程化部署、真实环境验证结果、endpoint 矩阵、变量约定和维护规则见 [SETUP.md](./SETUP.md)。

## Provisioning 自动导入

1. 复制 provisioning/dashboards.yaml 到 Grafana provisioning/dashboards 目录
2. 复制 dashboards/*.json 到 dashboards.yaml 中 options.path 指定的路径
3. 重启 Grafana 或等待 updateIntervalSeconds 秒自动加载

## 截图

### OceanBase 告警响应

![ob-alert](docs/screenshots/ob-alert.png)

### OceanBase 日常巡检

![ob-inspect](docs/screenshots/ob-inspect.png)

### OceanBase 容量规划

![ob-capacity](docs/screenshots/ob-capacity.png)

### OceanBase 数据库性能 · 租户维度

![ob-tenant](docs/screenshots/ob-tenant.png)

### OceanBase 数据库性能 · Unit 维度

![ob-unit](docs/screenshots/ob-unit.png)
