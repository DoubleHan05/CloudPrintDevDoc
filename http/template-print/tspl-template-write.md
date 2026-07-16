---
title: 小票模板打印（间隙纸）
url: /api/cloud/print/tsplTemplateWrite
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
  - name: templateId
    type: Number
    required: true
    desc_zh: 模版 ID
    desc_en: Template ID
  - name: renderData
    type: String
    required: true
    desc_zh: JSONObject 字符串
    desc_en: JSON object string
  - name: printTimes
    type: Number
    required: false
    desc_zh: "重复打印份数，默认 1"
    desc_en: "Copies, default 1"
requestExample: |
  {
    "sn": "KM118DW123456",
    "templateId": 11,
    "renderData": "{\"orderInfo\":[{\"shopName\":\"快麦便利店\",\"orderNo\":\"A202604300001\",\"payTime\":\"2026-04-30 18:20:00\"}],\"goodsList\":[{\"goodsName\":\"拿铁\",\"num\":\"2\",\"unitPrice\":\"14.00\",\"amount\":\"28.00\"}]}",
    "printTimes": 1,
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
    fix_zh: 确认 SN 正确且设备已激活
    fix_en: Verify SN is correct and device is activated
  - code: "4005"
    desc_zh: 设备离线
    desc_en: Device offline
    fix_zh: 检查设备网络
    fix_en: Check device network
  - code: "6009"
    desc_zh: 模版 ID 不存在
    desc_en: Template ID not found
    fix_zh: 在管理后台确认模版 ID
    fix_en: Confirm template ID in management console
---

# 小票模板打印（间隙纸）

**适用机型**：KM118 系列（除 KM118EG / KM118MEG）/ KME31 / KME41 / KME20 / KMSX320 / KMUL410

::: info 说明
在标签机上打印小票内容。与 `tsplTemplatePrint` 的区别：`renderData` 为 JSONObject 字符串（单张），非 Array。
:::

<ApiPage />
