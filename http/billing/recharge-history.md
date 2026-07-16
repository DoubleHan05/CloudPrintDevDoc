---
title: 充值历史查询
url: /api/cloud/device/pageCloudServiceRechargeHistory
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
  - name: pageNo
    type: Number
    required: true
    desc_zh: "页码，从 1 开始"
    desc_en: "Page number (from 1)"
  - name: pageSize
    type: Number
    required: true
    desc_zh: "每页条数，最大 100"
    desc_en: "Page size, max 100"
  - name: sn
    type: String
    required: false
    desc_zh: "设备序列号；不传则查询全部"
    desc_en: "Filter by SN; omit = all"
  - name: status
    type: Number
    required: false
    desc_zh: "1=处理中 / 2=成功 / 3=失败 / 4=异常"
    desc_en: "1=processing / 2=success / 3=fail / 4=abnormal"
  - name: externalNo
    type: String
    required: false
    desc_zh: 对接方业务单号
    desc_en: Merchant order id
requestExample: |
  {
    "appId": "1612694567132",
    "timestamp": "2026-05-14 15:29:29",
    "pageNo": 1,
    "pageSize": 10,
    "sn": "KME31G",
    "status": 2,
    "externalNo": "OUT202605140001",
    "sign": "testsignvalue"
  }
responseRows:
  - name: status
    type: boolean
    desc_zh: true 为成功
    desc_en: true = success
  - name: data.total
    type: Number
    desc_zh: 总记录数
    desc_en: Total rows
  - name: data.pageNum
    type: Number
    desc_zh: 当前页码
    desc_en: Current page
  - name: data.pageSize
    type: Number
    desc_zh: 每页条数
    desc_en: Page size
  - name: "data.list[]"
    type: Object
    desc_zh: "充值记录，字段同 cloudServiceRecharge 出参 + created"
    desc_en: "Records (same fields as recharge response + created)"
responseExample: |
  {
    "status": true,
    "data": {
      "total": 1,
      "pageNum": 1,
      "pageSize": 10,
      "pages": 1,
      "list": [
        {
          "appId": "1612694567132",
          "sn": "KME31G",
          "months": 3,
          "externalNo": "OUT202605140001",
          "localOrderNo": "CSR1715671769000abcdef12",
          "status": 2,
          "oldExpireTime": "2026-06-13 18:00:00",
          "newExpireTime": "2026-09-13 18:00:00",
          "created": "2026-05-14 15:29:29"
        }
      ]
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
  - code: "6006"
    desc_zh: 必传参数为空
    desc_en: Missing required parameter
    fix_zh: 检查 pageNo 和 pageSize
    fix_en: Ensure pageNo and pageSize are provided
---

# 充值历史查询

**适用机型**：4G 机型

::: info 说明
分页查询当前 appId 下的云服务流量券充值记录，支持按 SN、状态、业务单号筛选。
:::

<ApiPage />
