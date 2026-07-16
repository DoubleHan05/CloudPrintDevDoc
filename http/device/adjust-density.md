---
title: 调节打印机浓度
url: /api/cloud/device/adjustDeviceDensity
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
  - name: density
    type: Number
    required: true
    desc_zh: "浓度值 1~15"
    desc_en: "Density value 1~15"
requestExample: |
  {
    "sn": "KME31G",
    "appId": "1612694567132",
    "timestamp": "2020-08-24 11:11:59",
    "density": 8,
    "sign": "testsignvalue"
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
  - code: "6039"
    desc_zh: 该机型不支持调节浓度
    desc_en: Density not supported on this model
    fix_zh: 确认机型为 KM118 / KME31 / KME41 系列
    fix_en: Confirm device is KM118 / KME31 / KME41 series
---

# 调节打印机浓度

**适用机型**：KM118 系列（除 KM118EG / KM118MEG）/ KME31 系列 / KME41 系列

::: tip 前置要求
设备需要处于标签模式（自检页指令为 `tspl`）。
:::

::: info SDK 支持
目前只有 Python SDK 提供对应的 `AdjustDeviceDensityRequest`；其他 SDK 暂未封装该接口，请直接走 HTTP 直连。
:::

<ApiPage />
