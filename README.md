# Brightdata_QPS

Bright Data SERP 性能测试工具 - 支持经济模式的 QPS 压力测试

## 功能特性

### 核心功能
- 支持多种搜索引擎：Google Search, Maps, Trends, Reviews, Lens, Hotels, Flights, Bing, Yandex, DuckDuckGo
- 并发测试：支持多个并发级别
- 详细统计：成功率、响应时间、延迟百分位数等
- 格式支持：Raw HTML 和 JSON 响应格式

### 经济模式（新功能）
为了解决高并发测试时消耗大量 API 额度的问题，新增了两个经济模式参数：

1. **`--target-qps`**: 限制请求速率（QPS）
   - 精确控制每秒请求数，避免过度消耗
   - 可用于测试不同 QPS 下的系统表现
   - 帮助找到最优的 QPS 值

2. **`--max-requests`**: 限制总请求数量
   - 设定测试的最大请求数
   - 与 `--target-qps` 配合使用可精确控制成本
   - 适合预算有限的测试场景

## 安装依赖

```bash
pip install requests
```

## 使用示例

### 基础测试
```bash
# 测试单个引擎 60 秒
python brigtdata_serp.py -t YOUR_TOKEN -z serp_api1 -e search -d 60 -c 5

# 测试多个引擎
python brigtdata_serp.py -t YOUR_TOKEN -z serp_api1 -e search maps -d 30 -c 3
```

### 经济模式测试
```bash
# 限制 QPS 为 5，避免过度消耗
python brigtdata_serp.py -t YOUR_TOKEN -z serp_api1 -e search --target-qps 5 -d 60 -c 3

# 限制总请求数为 100，精确控制成本
python brigtdata_serp.py -t YOUR_TOKEN -z serp_api1 -e search --max-requests 100 -c 3

# 组合使用：每秒 10 个请求，总共 200 个请求
python brigtdata_serp.py -t YOUR_TOKEN -z serp_api1 -e search --target-qps 10 --max-requests 200 -c 5
```

### 测试不同 QPS 值以找到最优性能
```bash
# 逐步测试不同的 QPS 值
python brigtdata_serp.py -t YOUR_TOKEN -z serp_api1 -e search --target-qps 5 -d 30 -c 3
python brigtdata_serp.py -t YOUR_TOKEN -z serp_api1 -e search --target-qps 10 -d 30 -c 3
python brigtdata_serp.py -t YOUR_TOKEN -z serp_api1 -e search --target-qps 20 -d 30 -c 3
```

### 其他选项
```bash
# 使用 JSON 格式并保存详细记录
python brigtdata_serp.py -t YOUR_TOKEN -z serp_api1 -e search --format json --save-details

# 测试多个并发级别
python brigtdata_serp.py -t YOUR_TOKEN -z serp_api1 -e search -c 1 5 10 -d 30

# 列出所有支持的引擎
python brigtdata_serp.py --list-engines
```

## 参数说明

| 参数 | 说明 | 默认值 |
|------|------|--------|
| `-t, --api-token` | Bright Data API Token | 必需 |
| `-z, --zone` | Bright Data zone 名称 | serp_api1 |
| `-e, --engines` | 要测试的引擎列表 | 必需 |
| `-d, --duration` | 测试时长（秒） | 60 |
| `-c, --concurrency` | 并发数 | 3 |
| `--target-qps` | 目标 QPS（经济模式） | 无限制 |
| `--max-requests` | 最大请求数（经济模式） | 无限制 |
| `--format` | 响应格式（raw/json） | raw |
| `--save-details` | 保存详细 CSV 记录 | False |

## 经济模式使用场景

### 场景 1：预算有限，需要控制成本
```bash
# 只发送 50 个请求进行快速验证
python brigtdata_serp.py -t TOKEN -z zone -e search --max-requests 50 -c 3
```

### 场景 2：找到最优 QPS 值
```bash
# 测试不同 QPS，观察成功率和延迟的变化
for qps in 5 10 15 20; do
    python brigtdata_serp.py -t TOKEN -z zone -e search --target-qps $qps --max-requests 100 -c 5
done
```

### 场景 3：长时间稳定性测试（低速率）
```bash
# 以较低 QPS 运行较长时间，避免快速消耗额度
python brigtdata_serp.py -t TOKEN -z zone -e search --target-qps 2 -d 300 -c 2
```

## 输出说明

程序会生成汇总统计表，包含：
- 请求总数、并发数、成功率
- 实际 QPS 和目标 QPS
- 响应时间统计（平均值、P50、P75、P90、P95、P99）
- 响应大小统计

统计数据会保存为 CSV 文件，方便后续分析。
