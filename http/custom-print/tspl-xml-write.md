---
title: 自定义 XML 打印（间隙纸）
url: /api/cloud/print/tsplXmlWrite
params:
  - name: appId
    type: String
    required: true
    desc_zh: "公共参数：应用唯一标识"
    desc_en: Application ID
  - name: timestamp
    type: String
    required: true
    desc_zh: "公共参数：`yyyy-MM-dd HH:mm:ss`"
    desc_en: "`yyyy-MM-dd HH:mm:ss`"
  - name: sign
    type: String
    required: true
    desc_zh: "公共参数：签名值"
    desc_en: Signature
  - name: sn
    type: String
    required: true
    desc_zh: 设备序列号
    desc_en: Device SN
  - name: xmlStr
    type: String
    required: false
    desc_zh: "打印 XML 字符串，与 jobs 二选一"
    desc_en: "Print XML string (either this or jobs)"
  - name: jobs
    type: Array
    required: false
    desc_zh: "批量打印 XML；最多 20 单；KMSX320 最多 5 单"
    desc_en: "Batch XML jobs; max 20; KMSX320 max 5"
  - name: printTimes
    type: Number
    required: false
    desc_zh: "重复打印份数，默认 1"
    desc_en: "Copies, default 1"
requestExample: |
  {
    "sn": "KM110h45932",
    "xmlStr": "<page><render><t>hello,world</t></render></page>",
    "appId": "1612694567132",
    "timestamp": "2020-08-24 11:11:59",
    "sign": "testsignvalue"
  }
responseExample: |
  {
    "status": true,
    "data": {},
    "message": null,
    "code": null,
    "exceptionMessage": null
  }
errors:
  - code: "4001"
    desc_zh: 序列号不存在或未激活
    desc_en: SN not found / not activated
    fix_zh: 确认 SN 正确
    fix_en: Verify SN
  - code: "4005"
    desc_zh: 设备离线
    desc_en: Device offline
    fix_zh: 检查设备网络
    fix_en: Check device network
  - code: "6024"
    desc_zh: xml jobs 超过限制
    desc_en: XML jobs exceeded limit
    fix_zh: "普通机型 ≤ 20 单，KMSX320 ≤ 5 单"
    fix_en: "Normal models ≤ 20, KMSX320 ≤ 5"
---

# 自定义 XML 打印（间隙纸）

**适用机型**：KM118 系列（除 EG / MEG）/ KME31 / KME41 / KME20 / KMSX320 / KMUL410

::: info XML 指令参考
TSPL XML 元素（`page/render/t/bc/qrc/img/box/circle/ellipse/bar`）的完整属性说明，请参见 [XML 指令参考](/zh/ref/xml-spec)。
:::

<ApiPage />
