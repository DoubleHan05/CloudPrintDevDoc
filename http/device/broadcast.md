---
title: 语音播报
url: /api/cloud/device/broadcast
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
  - name: volume
    type: Number
    required: true
    desc_zh: "音量 0~99"
    desc_en: "Volume 0~99"
  - name: volumeContent
    type: String
    required: true
    desc_zh: "语音内容，最长 50 字符"
    desc_en: "Speech content, max 50 chars"
requestExample: |
  {
    "sn": "KME31G",
    "appId": "1612694567132",
    "timestamp": "2020-08-24 11:11:59",
    "sign": "testsignvalue",
    "volume": 80,
    "volumeContent": "你好来单了"
  }
responseExample: |
  {
    "status": true,
    "data": "操作成功",
    "message": null,
    "code": null,
    "exceptionMessage": null
  }
errors:
  - code: "6036"
    desc_zh: 内容超过 50 字符
    desc_en: Content exceeds 50 characters
    fix_zh: 缩短 volumeContent 至 50 字符以内
    fix_en: Shorten volumeContent to 50 chars or less
  - code: "6035"
    desc_zh: 机型不支持语音播报
    desc_en: Model does not support voice broadcast
    fix_zh: 仅 KM118MGL / KME31GP 支持
    fix_en: Only KM118MGL / KME31GP supported
  - code: "4005"
    desc_zh: 设备离线
    desc_en: Device offline
    fix_zh: 检查设备网络连接
    fix_en: Check device network connection
---

# 语音播报

**适用机型**：KM118MGL / KME31GP

::: info 说明
让打印机语音播报订单信息，适用于门店到单提醒等场景。
:::

<ApiPage />
