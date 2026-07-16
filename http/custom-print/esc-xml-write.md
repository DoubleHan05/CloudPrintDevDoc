---
title: 自定义 XML 打印（连续纸）
url: /api/cloud/print/escXmlWrite
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
  - name: instructions
    type: String
    required: true
    desc_zh: 打印指令内容（XML 字符串）
    desc_en: Print XML string
  - name: volume
    type: Number
    required: false
    desc_zh: "音量 0~99（带语音机器）"
    desc_en: "Volume 0~99 (voice models)"
  - name: volumeIndex
    type: Number
    required: false
    desc_zh: 语言索引
    desc_en: Language index
  - name: cut
    type: Number
    required: false
    desc_zh: "是否切刀，0 / 1"
    desc_en: "0 / 1 auto cut"
  - name: openDrawer
    type: Number
    required: false
    desc_zh: "1 = 开钱箱"
    desc_en: "1 = open cash drawer"
requestExample: |
  {
    "sn": "KM110h45932",
    "instructions": "<page><render><t size='10'>hello,world</t></render></page>",
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
    fix_zh: 确认 SN 正确
    fix_en: Verify SN
  - code: "4005"
    desc_zh: 设备离线
    desc_en: Device offline
    fix_zh: 检查设备网络
    fix_en: Check device network
  - code: "6019"
    desc_zh: 打印内容过大
    desc_en: Content too large
    fix_zh: 缩减 XML 内容大小
    fix_en: Reduce XML payload size
---

# 自定义 XML 打印（连续纸）

**适用机型**：KM118 系列（除 EG / MEG）/ KME31 / KME41 / KME20 / KMDP 系列

::: info XML 指令参考
ESC XML 元素（`page/render/t/bc/qrc/pm`）的完整属性说明，请参见 [XML 指令参考](/zh/ref/xml-spec)。
:::

<ApiPage />
