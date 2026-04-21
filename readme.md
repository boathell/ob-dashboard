# OceanBase Grafana Panel 生成项目

## 项目概述

本项目基于 OceanBase 云平台 V4.4.1 参考指南的 markdown 文档，批量生成了 **50 个 Grafana Panel JSON** 文件，用于监控 OceanBase 集群、租户、OBProxy、主机和 OCP Agent 等指标。

这些 Panel 共用同一个 Grafana Prometheus/VictoriaMetrics 数据源，但它们依赖的 exporter endpoint 并不相同，不能只抓 `/metrics/ob/basic`。

## 仓库交付边界

当前代码库只把 `dashboards/*.json` 视为最终交付产物并纳入版本管理。

- `dashboards/`：最终可导入的 Grafana Dashboard JSON
- `panel/`：中间生成产物，不纳入版本管理
- `.omx/`、缓存文件、本地环境文件：本地运行痕迹，不纳入版本管理

## 文档来源

| 文档 | 页码范围 | 生成 Panel 数量 |
|-----|---------|----------------|
| `ocp_reference_pages_2423-2442.md` | 2423-2442 | 8 |
| `ocp_reference_pages_2443-2500.md` | 2443-2500 | 16 |
| `ocp_reference_pages_2501-2600.md` | 2501-2600 | 8 |
| `ocp_reference_pages_2601-2700.md` | 2601-2700 | 6 |
| `ocp_reference_pages_2701-2800.md` | 2701-2800 | 2 |
| `ocp_reference_pages_2801-2900.md` | 2801-2900 | 4 |
| `ocp_reference_pages_2901-2965.md` | 2901-2965 | 6 |
| **合计** | | **50** |

## 生成的 Panel 列表

### 核心性能指标（QPS/TPS/SQL）
| 文件名 | 说明 |
|-------|------|
| `panel_ob_cluster_qps.json` | 集群 QPS 分类（SELECT/INSERT/UPDATE/DELETE/REPLACE/OTHER） |
| `panel_ob_cluster_sql_rt.json` | SQL 响应时间趋势 |
| `panel_ob_cluster_tps.json` | TPS 趋势（TPS/Commit/Rollback/Timeout） |
| `panel_ob_cluster_transaction_rt.json` | 事务响应时间 |
| `panel_ob_cluster_transaction_count.json` | 事务数量（单分区/多分区） |
| `panel_ob_cluster_sql_rt_percentile.json` | SQL 响应时间 TP99/TP95 |
| `panel_ob_cluster_sql_plan.json` | SQL 执行计划类别（本地/远程/分布式） |
| `panel_ob_cluster_sql_execution_phase.json` | SQL 执行阶段 |

### IO 和存储指标
| 文件名 | 说明 |
|-------|------|
| `panel_ob_cluster_io_count.json` | 物理 IO 次数 |
| `panel_ob_cluster_io_throughput.json` | IO 吞吐量 |
| `panel_ob_cluster_clog.json` | 事务日志指标 |
| `panel_ob_cluster_clog_sync_delay.json` | Clog 同步延迟 |
| `panel_ob_cluster_disk_usage.json` | 磁盘使用（日志盘/数据盘） |
| `panel_ob_cluster_memstore.json` | MEMStore 使用 |
| `panel_ob_cluster_cache_size.json` | 缓存大小 |
| `panel_ob_cluster_cache_hit_ratio.json` | 缓存命中率 |

### 会话和工作负载
| 文件名 | 说明 |
|-------|------|
| `panel_ob_cluster_sessions.json` | 会话数（活跃/总数） |
| `panel_ob_cluster_foreground_workload.json` | 前台工作负载 |
| `panel_ob_cluster_background_workload.json` | 后台工作负载 |
| `panel_ob_cluster_wait_events.json` | 等待事件 |
| `panel_ob_cluster_wait_event_time.json` | 等待事件耗时 |
| `panel_ob_cluster_request_queue.json` | 请求等待队列 |
| `panel_ob_cluster_opened_cursors.json` | 打开游标数 |
| `panel_ob_cluster_px_worker.json` | 并行执行工作线程使用率 |

### 资源和进程指标
| 文件名 | 说明 |
|-------|------|
| `panel_ob_cluster_process_resource.json` | 进程资源使用（内存/CPU/FD/线程） |
| `panel_ob_cluster_tenant_resource.json` | 租户资源使用率 |
| `panel_ob_cluster_tenant500_memory.json` | 500 租户内存 |
| `panel_ob_cluster_db_time.json` | DB Time |
| `panel_ob_cluster_rpc.json` | RPC 包指标 |
| `panel_ocp_agent_process.json` | OCP Agent 进程指标 |

### 事务和锁
| 文件名 | 说明 |
|-------|------|
| `panel_ob_cluster_lock_waits.json` | 事务锁等待 |
| `panel_ob_cluster_xa_transaction.json` | XA 事务 |
| `panel_ob_cluster_tx_data_hit.json` | 事务表读请求命中 |
| `panel_ob_cluster_partition_status.json` | 分区状态 |

### 主机指标
| 文件名 | 说明 |
|-------|------|
| `panel_host_system_load.json` | 系统负载 |
| `panel_host_cpu_io.json` | CPU 和 IO 使用率 |
| `panel_host_disk_io.json` | 磁盘 IO |
| `panel_host_network.json` | 网络指标 |
| `panel_host_memory.json` | 内存使用 |
| `panel_host_inode_disk.json` | Inode 和磁盘使用 |

### OBProxy 指标
| 文件名 | 说明 |
|-------|------|
| `panel_obproxy_qps.json` | OBProxy QPS |
| `panel_obproxy_connections.json` | OBProxy 连接数 |
| `panel_obproxy_response_time.json` | OBProxy 响应时间 |
| `panel_obproxy_process_resource.json` | OBProxy 进程资源 |
| `panel_obproxy_routing.json` | OBProxy 路由表 |
| `panel_obproxy_network.json` | OBProxy 网络流量 |
| `panel_obproxy_error_logs.json` | OBProxy 错误日志 |

### 其他指标
| 文件名 | 说明 |
|-------|------|
| `panel_ob_cluster_error_logs.json` | 错误日志（Observer/Election/Rootservice） |
| `panel_ob_cluster_binlog.json` | Binlog 指标 |
| `panel_obkv_hbase_qps.json` | OBKV HBase QPS |

## 技术规范

### 维护规则

- `ocp_reference_pages_*.md` 只用于**生成第一版** Panel JSON。
- 第一版生成完成后，后续所有联调、修正、验证、重写，都必须以**真实环境**为准：
  - 真实 endpoint
  - 真实 metrics 文本
  - 真实 metric 名
  - 真实 label
  - 真实 VictoriaMetrics / Grafana 查询结果
- 当参考文档与真实环境冲突时，以真实环境为主，不再强行回贴文档表达式。
- 参考文档在后续阶段只作为背景材料，不再是修正阶段的最高真值。

### 当前已验证环境

- **Prometheus 兼容数据源**: VictoriaMetrics
- **Grafana 数据源 UID**: `VictoriaMetrics`
- **Grafana 版本**: 已在 Grafana 13 验证导入与查询，Panel JSON 仍保持 Grafana 9.x 兼容格式
- **真实集群名**: `obcluster_pri`
- **真实租户名**: `tenant_mysql`
- **真实 label**
  - `cluster`: `ob_cluster_name`
  - `zone`: `obzone`
  - `tenant`: `tenant_name`
- **重要差异**
  - 当前环境中 `tenant_name` 可用
  - 当前环境中 `ob_tenant_name` 不可用

### OCP Agent exporter endpoint

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

### 当前变量约定

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

### PromQL 模板替换规则

| 模板 | 替换为 |
|-----|-------|
| `@LABELS` | `ob_cluster_name="$cluster", obzone=~"$zone"` |
| `@GBLABELS` | `ob_cluster_name, obzone` |
| `@INTERVAL` | `$interval` |

说明：

- 当前仓库多数 Panel 只按集群和 Zone 聚合，没有直接把 `$tenant` 写进 PromQL。
- 如果后续生成租户维度 Dashboard，tenant 相关变量与查询应优先使用 `tenant_name`，而不是 `ob_tenant_name`。

### Panel 统一规格

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

## 项目经验总结

### 1. 文档结构分析

OCP 参考指南文档采用统一的表格格式描述指标：
- 指标分组
- 指标名称
- 指标解释
- 计算表达式（PromQL）

这种结构非常适合批量提取和转换。

### 2. 生成策略

采用**按文档顺序、按指标类别**的生成策略：
1. 先读取文档，提取所有指标
2. 按类别分组（QPS、IO、事务、主机等）
3. 每个类别生成一个 Panel
4. 保持 PromQL 表达式的准确性

### 3. 变量替换

所有 Panel 统一使用 Grafana 变量：
- 集群过滤: `ob_cluster_name="$cluster"`
- Zone 过滤: `obzone=~"$zone"`（使用正则匹配支持多选）
- 时间间隔: `[$interval]`

### 4. 单位设置

根据指标类型设置合适的单位：
- QPS/TPS: `reqps` 或 `short`
- 响应时间: `µs`（微秒）
- 吞吐量: `Bps` 或 `MBs`
- 内存: `bytes` 或 `GB`
- 百分比: `percent`

### 5. 遇到的问题及解决

**问题 1**: 部分指标表达式过长，需要正确转义引号
**解决**: 使用 `"` 转义双引号，确保 JSON 格式正确

**问题 2**: 不同指标的单位不同，需要分别设置
**解决**: 使用 `fieldConfig.overrides` 为特定系列设置单位

**问题 3**: 部分指标涉及除法运算，需要处理除零情况
**解决**: 依赖 Prometheus 的默认行为，除零时返回空值

### 6. 最佳实践

1. **生成与修正分离**: 第一版可按文档生成；后续修正必须以真实 endpoint 和真实查询结果为准
2. **统一 Panel 样式**: 所有 Panel 使用相同的尺寸（24x8）和样式
3. **图例位置**: 统一放在底部，方便阅读
4. **多系列支持**: 使用 `palette-classic` 自动分配颜色
5. **数据源指定**: 每个 target 都明确指定 datasource，避免依赖默认数据源

## 使用说明

1. 导入 Panel 之前，先确认采集链路覆盖了对应 endpoint，而不是只抓 `/metrics/ob/basic`。
2. 配置 VictoriaMetrics 数据源，UID 必须为 `VictoriaMetrics`。
3. 推荐的 Dashboard 变量：
   - `cluster`: `label_values(ob_sysstat, ob_cluster_name)`
   - `zone`: `label_values(ob_sysstat{ob_cluster_name="$cluster"}, obzone)`
   - `tenant`: `label_values(ob_sysstat{ob_cluster_name="$cluster"}, tenant_name)`
   - `interval`: `30s`、`1m`、`5m`

### Panel 与 endpoint 的对应关系

- `ob/basic`
  - 典型覆盖：QPS、TPS、事务数量、RPC、DB Time、SQL 执行计划、SQL 执行阶段、请求队列、XA、IO 次数、IO 吞吐量
- `ob/extra`
  - 典型覆盖：分区状态、日志流状态、部分磁盘与扩展系统表指标
- `node/host`
  - 典型覆盖：`panel_host_*`
- `node/ob`
  - 典型覆盖：错误日志、observer 进程资源、部分主机侧 OceanBase 指标
- `node/obproxy`
  - 典型覆盖：OBProxy 进程资源、错误日志
- `obproxy`
  - 典型覆盖：`panel_obproxy_*` 业务指标
- `metrics/stat`
  - 典型覆盖：`panel_ocp_agent_process.json`

如果某类 endpoint 未抓取，对应 Panel 会“查询成功但无数据”，这不代表 PromQL 一定错误。

## 当前验证结果

基于真实环境 `obcluster_pri / tenant_mysql`，在宿主机 `vmagent-ob` 补齐多 endpoint 抓取、按真实 endpoint 重写 OBProxy 组并修正 sessions / sql_rt_percentile 指标名后的最新结果：

- 全部 target 都有数据：34 个
- 部分 target 有数据：14 个
- 查询成功但全部为空：2 个

### 已验证全部有数据的 Panel

- `panel_host_cpu_io.json`
- `panel_host_disk_io.json`
- `panel_host_inode_disk.json`
- `panel_host_memory.json`
- `panel_host_system_load.json`
- `panel_ob_cluster_db_time.json`
- `panel_ob_cluster_disk_usage.json`
- `panel_ob_cluster_io_count.json`
- `panel_ob_cluster_io_throughput.json`
- `panel_ob_cluster_lock_waits.json`
- `panel_ob_cluster_opened_cursors.json`
- `panel_ob_cluster_process_resource.json`
- `panel_ob_cluster_px_worker.json`
- `panel_ob_cluster_qps.json`
- `panel_ob_cluster_request_queue.json`
- `panel_ob_cluster_rpc.json`
- `panel_ob_cluster_sql_execution_phase.json`
- `panel_ob_cluster_sql_plan.json`
- `panel_ob_cluster_sessions.json`
- `panel_ob_cluster_sql_rt_percentile.json`
- `panel_ob_cluster_tenant500_memory.json`
- `panel_ob_cluster_tenant_resource.json`
- `panel_ob_cluster_tps.json`
- `panel_ob_cluster_transaction_count.json`
- `panel_ob_cluster_tx_data_hit.json`
- `panel_ob_cluster_xa_transaction.json`
- `panel_obproxy_connections.json`
- `panel_obproxy_error_logs.json`
- `panel_obproxy_network.json`
- `panel_obproxy_process_resource.json`
- `panel_obproxy_qps.json`
- `panel_obproxy_response_time.json`
- `panel_obproxy_routing.json`
- `panel_ocp_agent_process.json`

### 部分有数据的 Panel

- `panel_host_network.json`
- `panel_ob_cluster_background_workload.json`
- `panel_ob_cluster_cache_hit_ratio.json`
- `panel_ob_cluster_cache_size.json`
- `panel_ob_cluster_clog.json`
- `panel_ob_cluster_clog_sync_delay.json`
- `panel_ob_cluster_error_logs.json`
- `panel_ob_cluster_foreground_workload.json`
- `panel_ob_cluster_memstore.json`
- `panel_ob_cluster_partition_status.json`
- `panel_ob_cluster_sql_rt.json`
- `panel_ob_cluster_transaction_rt.json`
- `panel_ob_cluster_wait_event_time.json`
- `panel_ob_cluster_wait_events.json`

### 当前环境为空的 Panel

这些 Panel 在补齐 `node/host`、`node/ob`、`node/obproxy`、`obproxy`、`ob/extra`、`metrics/stat` 后仍然为空，需要继续区分“当前环境没有业务数据”和“查询口径与当前 exporter 版本不匹配”：

- `panel_ob_cluster_binlog.json`
- `panel_obkv_hbase_qps.json`

### 对剩余空结果的当前判断

- `panel_ob_cluster_binlog.json`
  - 当前环境大概率没有对应 binlog 指标数据
- `panel_obkv_hbase_qps.json`
  - 当前环境大概率没有对应 OBKV HBase 指标数据

### 本轮已修正的指标名错配

- `panel_ob_cluster_sessions.json`
  - `ob_active_session_num` -> `ob_session_active_num`
  - `ob_all_session_num` -> `ob_session_all_num`
- `panel_ob_cluster_sql_rt_percentile.json`
  - `ob_query_rt_cumulative_count` -> `ob_query_rt_total_cumulative_count`
  - 分位数查询改为按 `le, ob_cluster_name` 聚合后再做 `histogram_quantile`

### OBProxy 组修正结果

当前真实 exporter 输出与原始文档中的 `odp_*` 查询面不一致，因此仓库中的 `panel_obproxy_*.json` 已经改为以真实 endpoint 为准：

- `/metrics/obproxy`
  - `exposer_request_latencies`
  - `exposer_scrapes_total`
  - `exposer_transferred_bytes_total`
- `/metrics/node/obproxy`
  - `obproxy_log_level_count`
  - `process_*`
  - `net_connectivity`
  - `obproxy_coredump_time_seconds`

也就是说，当前 OBProxy Panel 语义已经不再强制复刻原文档中的 `odp_sql_request_total`、`odp_current_session`、`odp_entry_total`、`odp_request_byte`、`odp_sql_cost_total`。

## 文件清单

```
.
├── AGENTS.md                              # 项目规范说明
├── dashboards/                            # 按 endpoint / 组件拆分后的 Dashboard JSON
├── dashboard_panel_mapping.md             # Dashboard 与 panel / endpoint 对应关系
├── generate_split_dashboards.py           # Dashboard 生成脚本
├── ocp_reference_pages_2423-2442.md       # 源文档 1
├── ocp_reference_pages_2443-2500.md       # 源文档 2
├── ocp_reference_pages_2501-2600.md       # 源文档 3
├── ocp_reference_pages_2601-2700.md       # 源文档 4
├── ocp_reference_pages_2701-2800.md       # 源文档 5
├── ocp_reference_pages_2801-2900.md       # 源文档 6
├── ocp_reference_pages_2901-2965.md       # 源文档 7
├── panel_*.json                           # 50 个生成的 Panel
└── readme.md                              # 本文件
```

## 拆分后的 Dashboard

基于真实环境 exporter endpoint 的差异，当前仓库已经将 50 个 panel 拆分为 8 个 Dashboard：

- `dashboards/dashboard_host_infra.json`
  - `/metrics/node/host`
- `dashboards/dashboard_observer_node.json`
  - `/metrics/node/ob`
- `dashboards/dashboard_oceanbase_database.json`
  - `/metrics/ob/basic`
  - `/metrics/ob/extra`
- `dashboards/dashboard_obproxy_business.json`
  - `/metrics/obproxy`
- `dashboards/dashboard_obproxy_node.json`
  - `/metrics/node/obproxy`
- `dashboards/dashboard_ocp_agent.json`
  - `:62888/metrics/stat`
  - `:62889/metrics/stat`
- `dashboards/dashboard_ob_binlog.json`
  - feature / environment dependent
- `dashboards/dashboard_obkv_hbase.json`
  - feature / environment dependent

说明：

- 这 8 个 Dashboard 的 panel 映射关系见 `dashboard_panel_mapping.md`
- 若需要重新生成 Dashboard，请运行：

```bash
python3 generate_split_dashboards.py
```

## 总结

本仓库已完成 50 个 Grafana Panel 的生成，并完成了一轮真实环境验证。验证表明：

- 参考文档适合作为第一版生成输入，但不适合作为后续修正的最高真值
- 仓库早期环境说明过于简化，实际需要按 exporter endpoint 分层采集
- 当前环境 tenant label 应使用 `tenant_name`
- 后续维护必须优先相信真实 endpoint、真实 metrics 和真实查询结果
- “无数据”需要优先从采集面排查，而不是默认认定为 Panel 查询错误
