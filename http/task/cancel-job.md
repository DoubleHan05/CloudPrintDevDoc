---
title: 取消队列任务
url: /api/cloud/print/cancelJob
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
requestExample: |
  {
    "sn": "KME31G",
    "appId": "1612694567132",
    "timestamp": "2020-08-24 11:11:59",
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
  - code: "4012"
    desc_zh: 无权限设备
    desc_en: No device permission
    fix_zh: 确认该 appId 已绑定此设备
    fix_en: Ensure the appId is bound to this device
---

# 取消队列任务

**适用机型**：所有机型

::: info 说明
取消指定设备打印队列中所有未打印的任务。已在打印中或已完成的任务不受影响。
:::

<ApiPage />
