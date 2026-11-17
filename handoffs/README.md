# 多模型情报交接规范

每次运行按一个 `run_id` 生成一份 Markdown 和一份同名 JSON。

## 文件位置

```
handoffs/YYYY-MM-DD/<run_id>.md
handoffs/YYYY-MM-DD/<run_id>.json
```

Markdown 供人工和模型阅读。JSON 供网页写入、去重、统计和后续自动化读取。两份文件包含同一个 `run_id`、采集时间和来源 URL。

## 运行阶段

| 阶段 | 责任 | 输入 | 输出 |
|---|---|---|---|
| 获取 | 平台 API、公开网页、搜索 API | 竞品清单、关键词 | 原始 URL、抓取时间、可见文本、可见指标、HTTP 状态 |
| 规范化 | GPT 快速模型 | 原始来源 | 去重来源清单、车型或品牌实体、字段缺失说明 |
| 拆解 | Claude 主模型 | 规范化来源 | 内容机制、受众张力、证据边界 |
| 质检 | GPT 推理模型 | 前两项输出 | 冲突、无证据主张、是否可进入选题 |
| 选题 | GPT 主模型 | 已通过质检的证据 | 中英文选题卡、平台与形式、来源引用 |
| 存档 | 本机运行器 | 全部结构化结果 | Markdown、JSON、GitHub 提交 SHA |

## 原则

- 模型不被当作爬虫。竞品价格、库存、评论、视频指标和帖子正文来自平台 API、合法数据源或公开网页。
- DeepSeek Web Search 用于低成本发现公开链接。它返回的日期未知结果仍保存，标记为 `date_status: unknown`，不能被称为今日热点。
- GPT 与 Claude 接收同一份已抓取证据，分别分析，不能自行补全页面或帖子不存在的数据。
- 有分歧时只把分歧条目交给推理模型裁定，避免每条数据被三个模型重复消耗。
- 任何模型输出必须带 `source_urls`、`observed_at` 和 `evidence_limit`。缺其中一项不能进入选题库。

## JSON 最小结构

```json
{
  "run_id": "competitor-2026-11-17-001",
  "kind": "competitor_monitor",
  "observed_at": "2026-11-17T00:00:00Z",
  "sources": [
    {
      "url": "https://example.com/product",
      "platform": "website",
      "observed_at": "2026-11-17T00:00:00Z",
      "date_status": "known|unknown",
      "raw_excerpt": "公开页面可见文本",
      "visible_metrics": {},
      "fetch_status": 200
    }
  ],
  "handoffs": {
    "normalizer": {"model": "gpt...", "output": {}, "source_urls": []},
    "analyst": {"model": "claude...", "output": {}, "source_urls": []},
    "reviewer": {"model": "gpt...", "verdict": "pass|hold", "blocking_gaps": []}
  },
  "write_status": "local_only|github_pushed",
  "github_commit": null
}
```

## 竞品监测的最低有效数据

1. 产品页和价格页
2. 品牌账号或公开帖子 URL
3. 页面抓取时间
4. 价格、规格、促销、评论数等可见字段
5. 无法取得的字段必须写为缺失，不能由模型推测

网页只能消费 `reviewer.verdict = pass` 的记录。所有原始来源和 Markdown 仍保留，供之后的 GPT、Claude、DeepSeek 复查。
