---
title: 解绑打印机
url: /api/cloud/device/unbindDevice
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
  - name: deviceKey
    type: String
    required: true
    desc_zh: 设备密钥
    desc_en: Device key
requestExample: |
  {
    "sn": "KM118DW123456",
    "deviceKey": "123456",
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
    fix_zh: 检查 SN 是否与机器底部一致
    fix_en: Verify SN matches the device label
  - code: "4009"
    desc_zh: 设备密钥不正确
    desc_en: Invalid device key
    fix_zh: 检查机器底部密钥与 `deviceKey` 一致
    fix_en: "Check the key label on device bottom matches `deviceKey`"
---

# 解绑打印机

**适用机型**：所有机型

::: info 说明
解除设备与 appId 的绑定关系。解绑后该 SN 变为"公开"状态，任何 appId 均可绑定和调用。
:::

<ApiPage />
