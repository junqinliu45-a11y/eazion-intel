# EAZION 情报同步库

EAZION 电动自行车社媒运营情报库，由 Muse（糯米）每日维护。

## 文件说明

| 文件 | 内容 | 更新频率 |
|---|---|---|
| `viral-library.json` | 爆款灵感同步：爆款拆解条目（九维拆解＋效果证据等级＋输入阶段标记），供 EAZION Ops 网页「AI 情报同步」区拉取渲染 | 每日 |
| `daily/爆款日报_YYYY-MM-DD.md` | 每日爆款日报 | 每日 |
| `竞品数据_总览.md` | 四车型竞品一页纸总览 | 手动更新 |
| `竞品数据_N9PRO.md` / `_K9.md` / `_D9.md` / `_T6.md` | 20 家竞品公开页数据（价格/规格/认证/差评/话术） | 手动更新 |
| `社媒对标_N9PRO组.md` / `_K9组.md` / `_T6组.md` | 竞品社媒运营对标（账号/粉丝/爆款/打法/强度评级） | 手动更新 |

## 给 EAZION Ops 网页的接入说明

在爆款灵感模块新增「AI 情报同步」分区，页面加载时从以下地址拉取 JSON：

```
https://raw.githubusercontent.com/junqinliu45-a11y/eazion-intel/main/viral-library.json
```

字段映射：`platform`→平台徽章、`url`→标题外链、`observation`→你的观察、`stats`→互动数据、`breakdown`→九维拆解卡片、`evidence_tier`→效果证据等级徽章、`input_stage`→输入阶段标记。拉取失败时显示「同步暂不可用」，不影响本地条目。

## 给 GPT 的读取说明

所有 `.md` 文件可直接用联网能力按 raw 链接读取，例如：

```
https://raw.githubusercontent.com/junqinliu45-a11y/eazion-intel/main/竞品数据_总览.md
```

（中文文件名需做 URL 编码后访问）
