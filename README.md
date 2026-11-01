# meter-log

预付费电表（辰域智控，一村92号201 北次卧）的每小时自动抄表记录。

## 工作方式

- GitHub Actions cron（每小时第 5 分钟）抓取表计充值页并解析剩余金额/电量/单价
- 表计站在阿里云 WAF 后面，会拒数据中心 IP（405），故经 r.jina.ai 等公共读取代理获取，失败自动降级重试
- 读数追加进 [history.json](history.json)（10 分钟内重复抄写自动合并，最多保留 2200 条 ≈ 90 天）
- 数据经 jsDelivr CDN 分发（`https://cdn.jsdelivr.net/gh/Raing5Days/meter-log@<commit>/history.json`）

## 字段

`history.json` 为数组，每条：`{ "ts": 毫秒时间戳, "money": 剩余金额(元), "kwh": 剩余电量, "price": 综合费用(元/kWh) }`

手动触发一次抄表：Actions → read-meter → Run workflow。
