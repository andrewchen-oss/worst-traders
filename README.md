# BSC Token Loss Finder

> 🔍 找出 BSC 链上任意代币的亏损地址 — 链上数据，精准计算

---

## 功能

- 输入代币合约地址 → 自动找 PancakeSwap V2 WBNB 交易对
- 扫描全部历史 Swap 事件（BscScan V2 API，免费）
- 计算每个地址的 BNB 净盈亏（买入花费 vs 卖出收入）
- 导出 Excel：亏损排行 / 全部交易者 / 汇总三张 Sheet

---

## 快速开始

### 1. 安装依赖

```bash
pip install requests openpyxl
```

### 2. 获取免费 API Key

1. 注册 [etherscan.io](https://etherscan.io/register)（免费）
2. Profile → API Keys → Create New Key
3. 同一个 Key 支持 BSC、ETH、Base 等所有链

### 3. 运行

```bash
# 通过代币地址（自动查 Pair）
python3 bsc_losers.py --token 0x代币地址 --key YOUR_KEY

# 直接提供 Pair 地址
python3 bsc_losers.py --pair 0xPair地址 --key YOUR_KEY

# 显示 Top 50 亏损者 + 自定义文件名
python3 bsc_losers.py --token 0xABC... --key YOUR_KEY --top 50 --out result.xlsx

# 演示模式（无需 API Key）
python3 bsc_losers.py --demo
```

---

## 输出示例

```
=========================================================
  💸  BSC Token Loss Finder  |  On-chain Analysis
=========================================================

🔗 查找 0x3645ef5e... 的 WBNB 交易对...
✅ Pair: 0x750A4abF...
   token0: 0x3645ef5e...（代币）
   token1: 0xbb4cdb9c...（WBNB）

📡 获取 Swap 历史... p1(1000) p2(847) → 共 1847 条

🔢 计算各地址盈亏...

=========================================================
  📊  统计结果
=========================================================
  总交易地址:       847
  亏损人数:         523   (61.7%)
  盈利人数:         324   (38.3%)
  总亏损:        -182.4312 BNB
  总盈利:          98.7651 BNB
  平均亏损:         -0.3489 BNB
  最大亏损:         -8.2100 BNB  (0x1a2b3c4d...)

💸 亏损 Top 10:
  排名  地址                                                 亏损(BNB)    买/卖
  ─────────────────────────────────────────────────────────────────────────
     1  0x1a2b3c4d...                                     -8.210000   12/3
     2  0x5e6f7a8b...                                     -6.543200    8/4
     ...

✅ Excel 已导出: bsc_losers_0x750A4abF.xlsx
```

---

## Excel 输出格式

| Sheet | 内容 |
|-------|------|
| 亏损排行 | 按亏损从大到小排列，红色标注 |
| 全部交易者 | 所有地址（亏损红色，盈利绿色） |
| 汇总 | 代币信息 + 统计数字 |

字段说明：
- `净亏损 (BNB)` — 已实现 + 未卖出仓位的 BNB 净流出（负数 = 亏损）
- `买入量 / 卖出量` — 累计 BNB 花费 / 累计 BNB 收入
- 注意：持仓未卖者会显示亏损（因为买入 BNB 花出去了还没收回来）

---

## 工作原理

1. 通过 PancakeSwap V2 Factory 合约找到 token/WBNB pair
2. 调用 BscScan V2 `getLogs` API 获取该 pair 所有 Swap 事件
3. 解析每条 Swap 的 `amount0In / amount1In / amount0Out / amount1Out`
4. 按 `to` 地址聚合：`net = Σ(BNB 卖出收入) - Σ(BNB 买入花费)`
5. 排序筛选负值 → 亏损地址

---

## 注意事项

- **只统计已发生的链上 Swap**，不包含 CEX 交易
- 公共 RPC 节点（如 publicnode.com）**只保留最近 ~5 天历史**，需要 BscScan API
- 如果 pair 在 PancakeSwap V3 或其他 DEX，需要额外适配

---

## 文件结构

```
bsc_losers.py      — 主脚本（BscScan V2 API + Excel 导出）
worst_traders.py   — GMGN Top Traders 版本（Apify Actor，适合快速查看）
requirements.txt   — 依赖
```
