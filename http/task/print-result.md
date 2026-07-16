---
title: 打印任务结果查询
url: /api/cloud/print/result
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
  - name: jobIdStr
    type: String
    required: true
    desc_zh: "jobId 数组 JSON 字符串，单次 ≤ 50 个"
    desc_en: "JSON array string of job ids, max 50"
requestExample: |
  {
    "sn": "KM111112222",
    "jobIdStr": "[\"111\",\"222\"]",
    "appId": "1612694567132",
    "timestamp": "2020-08-24 11:11:59",
    "sign": "testsignvalue"
  }
responseRows:
  - name: status
    type: boolean
    desc_zh: true 为成功
    desc_en: true = success
  - name: data
    type: Array
    desc_zh: 任务结果数组
    desc_en: Task result array
  - name: "data[].jobId"
    type: String
    desc_zh: 打印任务 id
    desc_en: Job id
  - name: "data[].desc"
    type: String
    desc_zh: 结果描述
    desc_en: Result description
  - name: "data[].code"
    type: Number
    desc_zh: "任务结果码：3000=下发成功 / 2000=打印成功 / 2007=打印失败 / 2004=打印异常 / 3001=模式错误失败"
    desc_en: "Task code: 3000=dispatched / 2000=succeeded / 2007=failed / 2004=error / 3001=mode error"
responseExample: |
  {
    "status": true,
    "data": [
      { "code": 2000, "desc": "打印成功", "jobId": "1612514721477" }
    ],
    "message": null,
    "code": null,
    "exceptionMessage": null
  }
errors:
  - code: "6021"
    desc_zh: 查询过于频繁
    desc_en: Rate limited
    fix_zh: 同一 SN 每秒最多 2 次
    fix_en: Max 2 queries/sec per SN
  - code: "6026"
    desc_zh: 单次 jobId 数超限
    desc_en: Too many jobIds per request
    fix_zh: 单次最多 50 个 jobId
    fix_en: Max 50 jobIds per call
---

# 打印任务结果查询

**适用机型**：KM118DW / KM118MW / KMSX320 / KME20W / KMUL410 / KM118DG

::: warning 频率限制
同一 SN 每秒最多 2 次查询；单次最多 50 个 jobId。推荐使用[异步回调](/zh/http/task/callback)代替轮询。
:::

<ApiPage />
