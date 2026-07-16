# 平台概述

快麦云开放平台为开发者提供云打印能力，支持标签打印机、小票打印机等多种机型，通过 HTTP API 或多语言 SDK 接入。

## 能力概览

| 能力 | 说明 |
|------|------|
| **HTTP 直连** | 标准 REST 接口，appId + MD5 签名，22 个接口全覆盖 |
| **多语言 SDK** | Java / PHP / Python / Android / iOS / UniApp / JS / 微信小程序 |
| **模板打印** | 在后台设计模板，传渲染数据即可打印 |
| **XML 自定义** | 直接下发 TSPL / ESC XML 指令 |
| **图片 / PDF** | Base64 图片打印 / PDF 直接打印 |
| **云服务充值** | 4G 机型流量券充值与查询 |

## 快速开始

::: tip 推荐路径
1. **申请凭证** — 前往 [快麦管理后台](http://admin.iot.kuaimai.com/) 申请 appId 和 appSecret
2. **了解签名** — 阅读 [签名规则](./sign)，所有接口都需要签名
3. **选择接口** — 参考 [接口选择指南](./api-guide) 按业务场景选接口
4. **发起第一个请求** — 用 [绑定打印机](/zh/http/device/bind-device) 开始测试
:::

## 机型支持

- **KM118 系列**（KM118DW / KM118MW 等）：标签 + 小票全功能
- **KME 系列**（KME31 / KME41 等）：标签 + 小票全功能
- **KMDP 系列**（KMDP158 / KMDP358 / KMDP458）：小票连续纸
- **KMSX320 / KMUL410**：标签纸，XML jobs ≤ 5 单
- **KM118MGL / KME31GP**：额外支持语音播报
- **KM360C**：图片打印，需先调用 `getDeviceCode`（SDK 专属流程）
