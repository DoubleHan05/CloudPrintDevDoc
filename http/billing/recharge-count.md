---
title: 查询剩余充值券月数
url: /api/cloud/device/cloudServiceRechargeAvailableCount
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
requestExample: |
  {
    "appId": "1612694567132",
    "timestamp": "2026-05-14 15:29:29",
    "sign": "testsignvalue"
  }
responseRows:
  - name: status
    type: boolean
    desc_zh: true 为成功
    desc_en: true = success
  - name: data.appId
    type: String
    desc_zh: 开放平台 appId
    desc_en: Application ID
  - name: data.availableCount
    type: Number
    desc_zh: "剩余充值券月数；无可用券为 0"
    desc_en: "Remaining months; 0 if none"
responseExample: |
  {
    "status": true,
    "data": {
      "appId": "1612694567132",
      "availableCount": 27
    },
    "message": null,
    "code": null,
    "exceptionMessage": null
  }
errors:
  - code: "4015"
    desc_zh: 签名错误
    desc_en: Signature error
    fix_zh: "参考[签名规则](/zh/quick/sign)排查"
    fix_en: "Refer to [Sign Rules](/en/quick/sign)"
  - code: "4024"
    desc_zh: 券配置异常
    desc_en: Credit config error
    fix_zh: 联系运营确认配置
    fix_en: Contact operations to verify config
---

# 查询剩余充值券月数

**适用机型**：4G 机型

::: info 说明
返回当前 appId 可用的云服务流量券数量（单位：月）。充值前建议先调用此接口确认余额。
:::

<ApiPage />
