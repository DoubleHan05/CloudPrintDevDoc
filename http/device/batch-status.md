---
title: 打印机状态查询
url: /api/cloud/device/batchStatus
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
  - name: snsStr
    type: String
    required: true
    desc_zh: "SN 数组 JSON 字符串，单次 ≤ 100 台"
    desc_en: "JSON string of SN array, max 100"
requestExample: |
  {
    "snsStr": "[\"222\",\"444\"]",
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
    desc_zh: 设备状态数组
    desc_en: Device status array
  - name: "data[].sn"
    type: String
    desc_zh: 序列号
    desc_en: Serial number
  - name: "data[].status"
    type: String
    desc_zh: "`ONLINE` / `OFFLINE` / `UNACTIVE` / `DISABLE`"
    desc_en: "`ONLINE` / `OFFLINE` / `UNACTIVE` / `DISABLE`"
  - name: "data[].deviceStatus"
    type: Number
    desc_zh: "0 正常 / 1 开盖 / 2 粘纸 / 3 缺纸"
    desc_en: "0 normal / 1 cover open / 2 paper jam / 3 out of paper"
  - name: "data[].cloudServiceExpire"
    type: String
    desc_zh: 云服务过期时间（仅 4G 机型有值）
    desc_en: Cloud service expiry (4G models only)
responseExample: |
  {
    "status": true,
    "data": [
      { "sn": "KM1111", "status": "ONLINE", "deviceStatus": 0, "cloudServiceExpire": "2026-09-13 18:00:00" },
      { "sn": "KM2222", "status": "ONLINE", "deviceStatus": 1, "cloudServiceExpire": null }
    ],
    "message": null,
    "code": null,
    "exceptionMessage": null
  }
errors:
  - code: "6025"
    desc_zh: 查询设备数超过限制
    desc_en: Too many SNs in one request
    fix_zh: 单次请求最多 100 个 SN
    fix_en: Max 100 SNs per request
---

# 打印机状态查询

**适用机型**：所有机型

::: warning 限制
一次请求最多查询 100 个设备。
:::

<ApiPage />
