---
title: 云服务流量券充值
url: /api/cloud/device/cloudServiceRecharge
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
  - name: months
    type: Number
    required: true
    desc_zh: "充值月数，正整数"
    desc_en: "Months to recharge (positive integer)"
  - name: externalNo
    type: String
    required: false
    desc_zh: "对接方业务单号，可重复；用于历史查询和对账"
    desc_en: "Merchant order id (may repeat); for history and reconciliation"
requestExample: |
  {
    "appId": "1612694567132",
    "timestamp": "2026-05-14 15:29:29",
    "sn": "KME31G",
    "months": 3,
    "externalNo": "OUT202605140001",
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
  - name: data.sn
    type: String
    desc_zh: 设备序列号
    desc_en: Device SN
  - name: data.months
    type: Number
    desc_zh: 本次充值月数
    desc_en: Months recharged
  - name: data.localOrderNo
    type: String
    desc_zh: 平台本地充值单号（对账用）
    desc_en: Platform local order id
  - name: data.status
    type: Number
    desc_zh: "1=处理中 / 2=成功 / 3=失败 / 4=未知"
    desc_en: "1=processing / 2=success / 3=fail / 4=unknown"
  - name: data.oldExpireTime
    type: String
    desc_zh: 充值前云服务到期时间
    desc_en: Expiry before recharge
  - name: data.newExpireTime
    type: String
    desc_zh: 充值后云服务到期时间
    desc_en: Expiry after recharge
  - name: data.availableCount
    type: Number
    desc_zh: 当前 appId 剩余充值券月数
    desc_en: Remaining recharge months on appId
responseExample: |
  {
    "status": true,
    "data": {
      "appId": "1612694567132",
      "sn": "KME31G",
      "months": 3,
      "externalNo": "OUT202605140001",
      "localOrderNo": "CSR1715671769000abcdef12",
      "status": 2,
      "oldExpireTime": "2026-06-13 18:00:00",
      "newExpireTime": "2026-09-13 18:00:00",
      "availableCount": 27
    },
    "message": null,
    "code": null,
    "exceptionMessage": null
  }
errors:
  - code: "4020"
    desc_zh: 充值券不足
    desc_en: Insufficient recharge credits
    fix_zh: 确认 appId 下还有足够的充值月数
    fix_en: Verify available months on the appId
  - code: "4022"
    desc_zh: "60 分钟内不能重复充值"
    desc_en: "Cannot recharge same SN within 60 minutes"
    fix_zh: 等待 60 分钟后再试
    fix_en: Wait 60 minutes before retrying
  - code: "4018"
    desc_zh: 设备未激活
    desc_en: Device not activated
    fix_zh: 确认设备已激活
    fix_en: Ensure device is activated
  - code: "4019"
    desc_zh: 无可用充值券
    desc_en: No recharge credits available
    fix_zh: 联系运营充值
    fix_en: Contact operations to purchase credits
---

# 云服务流量券充值

**适用机型**：4G 机型

::: warning 限流
单 appId ≤ 10 次/秒；同一 SN **60 分钟内只能充值 1 次**（重复提交返回 `code=4022`）。
:::

<ApiPage />
