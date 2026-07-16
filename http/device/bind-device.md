---
title: 绑定打印机
url: /api/cloud/device/bindDevice
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
    desc_en: Device serial number
  - name: deviceKey
    type: String
    required: false
    desc_zh: 设备密钥，机身贴有密钥时必传
    desc_en: "Device key (required if a key label is on the device)"
  - name: bindName
    type: String
    required: false
    desc_zh: 绑定名称，如用户名/用户 ID
    desc_en: "Binding name, e.g. user ID"
  - name: noteName
    type: String
    required: false
    desc_zh: 备注名称，如门店名
    desc_en: "Note name, e.g. store name"
requestExample: |
  {
    "sn": "KM118DW123456",
    "deviceKey": "123456",
    "bindName": "18934769875",
    "noteName": "杭州分店",
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
    fix_zh: "检查 SN 是否与机器底部一致；WiFi 机型确认蓝灯配网成功；4G 机型确认开机"
    fix_en: "Verify SN matches device label; WiFi: blue LED on; 4G: powered on"
  - code: "4009"
    desc_zh: 设备密钥不正确
    desc_en: Invalid device key
    fix_zh: 检查机器底部密钥与 `deviceKey` 一致
    fix_en: "Check the key label on device bottom matches `deviceKey`"
---

# 绑定打印机

**适用机型**：所有机型

::: info 说明
绑定是打印机与云平台之间的绑定，与业务侧无关。绑定成功后只有该 appId 可以调用该打印机；未绑定时任何 appId 都可以调用。
:::

::: tip KM360C（SDK 专属流程）
KM360C 使用 IMEI 绑定的流程**不在 HTTP 协议文档范围内**，仅通过 Java / Python / PHP / JS / 微信小程序等 SDK 提供。
:::

<ApiPage />
