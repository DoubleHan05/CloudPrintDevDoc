---
title: 分页获取模版列表
url: /api/cloud/print/pageTemplate
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
  - name: applicationId
    type: Number
    required: false
    desc_zh: 应用管理的 id
    desc_en: Application record id
  - name: applicationName
    type: String
    required: false
    desc_zh: 应用名称（精准匹配）
    desc_en: Exact-match app name
  - name: templateName
    type: String
    required: false
    desc_zh: 模版名称（精准匹配）
    desc_en: Exact-match template name
  - name: templateType
    type: Number
    required: false
    desc_zh: "1=标签 / 2=小票"
    desc_en: "1=label / 2=receipt"
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
requestExample: |
  {
    "applicationId": 12345,
    "applicationName": "我的应用",
    "templateName": "商品",
    "pageNo": 1,
    "pageSize": 10,
    "appId": "1612694567132",
    "timestamp": "2020-08-24 11:11:59",
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
  - name: data.pages
    type: Number
    desc_zh: 总页数
    desc_en: Total pages
  - name: "data.list[].templateId"
    type: Number
    desc_zh: 模版 ID
    desc_en: Template ID
  - name: "data.list[].templateName"
    type: String
    desc_zh: 模版名称
    desc_en: Template name
responseExample: |
  {
    "status": true,
    "data": {
      "total": 2,
      "pageNum": 1,
      "pageSize": 10,
      "pages": 1,
      "list": [
        { "templateId": 1001, "templateName": "商品标签_A" },
        { "templateId": 1002, "templateName": "商品标签_B" }
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
    fix_zh: 检查 pageNo 和 pageSize 是否传递
    fix_en: Ensure pageNo and pageSize are provided
---

# 分页获取模版列表

**适用机型**：所有机型

::: info 说明
查询当前应用下的模版列表，返回模版 ID 和名称。用于在打印前动态获取可用模版。
:::

<ApiPage />
