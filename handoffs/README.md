# 多模型情报交接规范

每次运行只生成一份 JSON，以 `run_id` 为唯一标识。

## 文件位置

```
handoffs/runs/YYYY-MM-DD/<run_id>.json
```

JSON 同时供网页、Atlas 和各模型读取。不得为同一运行另写 Markdown 副本。

## 路由规则

| 工作 | 最小输入 | 模型协作 |
|---|---|---|
| 选题推荐 | 今日公开线索、已批准事实、近 20 条选题 | DeepSeek 发现 → GPT Terra 规范化 → Claude 复核 |
| 内容日历 | 已采用选题、排期、可比表现 | GPT Terra |
| 封面工坊 | 已采用选题、已批准事实、封面表现 | GPT Terra → Claude |
| 爆款灵感 | 被选中的公开视频 URL 与可见文本 | DeepSeek 发现 → Claude 拆解 |
| Reddit 写作 | 用户中文要点、参考帖、已批准事实 | GPT Terra → Claude |
| 竞品监测 | 已登记竞品 URL、公开页面文本、历史差异 | GPT Terra → Claude → GPT Sol（只裁决分歧） |
| 标签库 | 标签库、近期已采用选题标签 | GPT Terra |

## 原则

- 模型不是爬虫。价格、库存、评论、帖子指标与正文只来自合法 API、公开页面或已存来源。
- DeepSeek Web Search 只负责低成本发现公开链接。不能确认日期的结果必须标记 `date_status: "unknown"`，不得称为今日热点。
- GPT 与 Claude 接收相同、已抓取的证据，不得补造页面或帖子内容。
- 推理模型只处理冲突和高价值变更，不能给所有记录重复调用。
- 每个可消费记录必须带 `source_urls`、`observed_at`、`evidence_limit`。缺一项只可保存为待核验，不能进入选题库。
- 密钥只存在本机，JSON、日志和 GitHub 不保存任何密钥。

## 最小 JSON 结构

```json
{
  "run_id": "competitor-2026-11-17-001",
  "kind": "竞品监测",
  "agent": "Scout",
  "observed_at": "2026-11-17T00:00:00Z",
  "scope": "本次读取的最小数据范围",
  "sources": [
    {
      "url": "https://example.com/product",
      "captured_at": "2026-11-17T00:00:00Z",
      "fetch_status": 200,
      "title": "公开页面标题",
      "raw_excerpt": "公开页面可见文本",
      "evidence_limit": "公开页面文本，发布前仍需人工核验。"
    }
  ],
  "handoffs": [
    {
      "model": "gpt-6.1-terra",
      "tokens": 0,
      "elapsed_ms": 0,
      "output": {}
    }
  ],
  "github": {
    "status": "local_only|github_pushed"
  }
}
```

网页只能消费已通过本页数据校验的字段。原始 URL 和证据边界必须随 JSON 保留，供后续 GPT、Claude、DeepSeek 复查。