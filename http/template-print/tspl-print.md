---
title: 标签模板打印（间隙纸）
url: /api/cloud/print/tsplTemplatePrint
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
  - name: renderDataArray
    type: String
    required: true
    desc_zh: "JSONArray 字符串，一次最多 50 张标签"
    desc_en: "JSON array string, max 50 labels per request"
  - name: printTimes
    type: Number
    required: false
    desc_zh: "每张打印份数，默认 1"
    desc_en: "Copies per label, default 1"
requestExample: |
  {
    "sn": "KM118DW123456",
    "templateId": 11,
    "renderDataArray": "[{\"labelInfo\":[{\"productName\":\"拿铁\",\"sku\":\"SKU001\",\"barcode\":\"6931234567890\"}]},{\"labelInfo\":[{\"productName\":\"三明治\",\"sku\":\"SKU002\",\"barcode\":\"6931234567891\"}]}]",
    "printTimes": 1,
    "appId": "1612694567132",
    "timestamp": "2020-08-24 11:11:59",
    "sign": "testsignvalue"
  }
responseRows:
  - name: status
    type: boolean
    desc_zh: true 为成功
    desc_en: true = success
  - name: data
    type: Array
    desc_zh: "jobId 数组，每张标签对应一个 jobId"
    desc_en: "Array of jobIds, one per label"
responseExample: |
  {
    "status": true,
    "data": ["job_001", "job_002"],
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
  - code: "6022"
    desc_zh: renderDataArray 格式错误
    desc_en: Invalid renderDataArray format
    fix_zh: "确保 JSON 合法且内部引号转义正确"
    fix_en: "Ensure valid JSON with properly escaped quotes"
  - code: "6015"
    desc_zh: 打印数量超过限制
    desc_en: Print quantity exceeds limit
    fix_zh: 单次最多 50 张
    fix_en: Max 50 labels per request
---

# 标签模板打印（间隙纸）

**适用机型**：KM118 系列（除 KM118EG / KM118MEG）/ KME31 / KME41 / KME20 / KMSX320 / KMUL410

::: info renderDataArray 提示
字符串内部双引号必须转义。建议在代码里先构建数组对象，再统一 `JSON.stringify`。每个数组元素对应一张标签。
:::

<ApiPage />
