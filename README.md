# 💸 Worst Traders Scraper

> 输入任意 Token 地址，自动抓取 GMGN Top 100 交易员数据，筛选亏损者并导出 Excel 报告。

**数据来源：** [GMGN.ai](https://gmgn.ai) via [Apify Actor](https://apify.com/muhammetakkurtt/gmgn-token-traders-scraper)

**支持链：** BSC · ETH · Solana · Base · Tron

---

## 🚀 快速开始

### 安装

```bash
pip install -r requirements.txt
```

### Demo 模式（无需 API Key）

```bash
python3 worst_traders.py --token 0x3645eF5eBb8F4219a00d13f4a345Eb4CfeFf7777 --chain bsc --demo
```

### 真实数据

```bash
python3 worst_traders.py \
  --token 0x3645eF5eBb8F4219a00d13f4a345Eb4CfeFf7777 \
  --chain bsc \
  --apify-key YOUR_APIFY_API_KEY
```

---

## 📋 参数说明

| 参数 | 说明 | 默认值 |
|------|------|--------|
| `--token` | Token 合约地址（必填） | — |
| `--chain` | 链名称 `bsc/eth/sol/base/tron` | `bsc` |
| `--apify-key` | Apify API Key | — |
| `--demo` | 使用模拟数据测试 | `False` |
| `--top` | 控制台显示前 N 名亏损者 | `20` |

---

## 📊 输出示例

```
=======================================================
  💸  Worst Traders Scraper  |  GMGN × APIFY
=======================================================

📊 数据摘要:
   总地址: 100
   亏损:   67 (67.0%)
   盈利:   33 (33.0%)

   总亏损金额:  $-148,302.50
   平均亏损:    $-2,212.73

💸 亏损最多 Top 20:
  排名  钱包地址                                      亏损 (USD)    胜率
  ─────────────────────────────────────────────────────────────────────
     1  0xabc...def                                    $-4,821.00     0%
     2  0x123...456                                    $-3,590.00    20%
   ...

✅ Excel 已导出: worst_traders_bsc_0x3645eF.xlsx
```

---

## 📁 Excel 报告结构

**Sheet 1 - Worst Traders**

| 字段 | 说明 |
|------|------|
| 排名 | 亏损排名（1 = 亏最多） |
| 钱包地址 | on-chain 地址 |
| 已实现亏损 (USD) | 已卖出部分的亏损 |
| 未实现盈亏 (USD) | 仍持有部分的浮盈/亏 |
| 总亏损 (USD) | 已实现 + 未实现 |
| 买入/卖出次数 | 交易次数 |
| 胜率 | 盈利交易占比 |
| 买入/卖出量 (USD) | 30天内交易量 |

**Sheet 2 - 汇总**

整体统计：亏损人数、总亏损、最大亏损地址等。

---

## 🔑 获取 Apify API Key

1. 注册 [Apify](https://apify.com) 免费账号
2. 进入 Settings → Integrations → API tokens
3. 新建 token 并复制

> Apify 免费套餐每月有 $5 额度，够跑约 100 次查询。

---

## 🛠 工作原理

```
用户输入 token 地址
       │
       ▼
  Apify Actor 运行
  (gmgn-token-traders-scraper)
       │
       ▼
  GMGN Top 100 交易员数据
  ┌─────────────────────┐
  │ 地址 / P&L / 胜率    │
  │ 买卖次数 / 交易量    │
  └─────────────────────┘
       │
       ▼
  筛选: realized_profit < 0
       │
       ▼
  排序 + Excel 导出
```

---

## 📄 License

MIT
