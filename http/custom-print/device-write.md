---
title: TSPL / ESC 指令打印
url: /api/cloud/print/deviceWrite
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
  - name: instructs
    type: String
    required: true
    desc_zh: "打印指令；若 prereq=FFFF01 则传 base64 编码后的字节"
    desc_en: "Print commands; base64 bytes if prereq=FFFF01"
  - name: prereq
    type: String
    required: false
    desc_zh: "`FFFF01`=instructs 为 base64；`FFFFFF`=明文（默认）"
    desc_en: "`FFFF01`=base64 · `FFFFFF`=plaintext (default)"
  - name: extra
    type: String
    required: false
    desc_zh: "`0`=TSPL（默认）/ `2`=ESC"
    desc_en: "`0`=TSPL (default) / `2`=ESC"
requestExample: |
  {
    "sn": "1609142313064",
    "instructs": "Q0xTDQpTSVpFIDMwbW0sMzBtbQ0KRElSRUNUSU9OIDAsMA0KQk9MRCAwDQpURVhUIDQ3LDI5LCJUU1MyNC5CRjIiLDAsMSwxLCKy4srUtPLToc7Esb4iDQpQUklOVCAxLDENCg==",
    "prereq": "FFFF01",
    "extra": "0",
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
    desc_zh: "jobId 数组"
    desc_en: "Array of jobIds"
responseExample: |
  {
    "status": true,
    "data": ["job_001"],
    "message": null,
    "code": null,
    "exceptionMessage": null
  }
errors:
  - code: "4001"
    desc_zh: 序列号不存在或未激活
    desc_en: SN not found / not activated
    fix_zh: 确认 SN 正确且设备已激活
    fix_en: Verify SN and activation status
  - code: "4005"
    desc_zh: 设备离线
    desc_en: Device offline
    fix_zh: 检查设备网络
    fix_en: Check device network
  - code: "6019"
    desc_zh: "打印内容过大（≤ 100KB）"
    desc_en: "Content too large (max 100KB)"
    fix_zh: 缩减指令内容大小
    fix_en: Reduce instruction payload size
---

# TSPL / ESC 指令打印

**适用机型**：KM118 系列（除 EG / MEG）/ KME31 / KME41 / KME20

::: warning 编码提示
打印机按 **GBK 编码**解析原生指令。`instructs` 含中文等非 ASCII 内容时，需要按 GBK 生成指令字节；当 `prereq = FFFF01` 时，需对 GBK 字节做 base64 编码后传入，否则会打印乱码。
:::

<ApiPage />
