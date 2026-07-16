---
title: ESC 指令打印
url: /api/cloud/print/escWrite
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
  - name: instructionsList
    type: String
    required: true
    desc_zh: base64 编码后的 ESC 指令
    desc_en: Base64-encoded ESC commands
  - name: copies
    type: Number
    required: false
    desc_zh: "重复打印次数，最大 30"
    desc_en: "Copies, max 30"
requestExample: |
  {
    "sn": "KM110h45932",
    "instructionsList": "H3MCAAAAAAD/AAAdIQAbYQHJzLzSwaqjqLK5tPKjqQ0KHSERG2EBz7rB+tC3t++6o8/K1uDB+s+6",
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
    desc_zh: "打印内容过大（UP 系列 ≤ 30KB / 其他 ≤ 100KB）"
    desc_en: "Content too large (UP series ≤ 30KB; others ≤ 100KB)"
    fix_zh: 缩减指令大小
    fix_en: Reduce payload size
---

# ESC 指令打印

**适用机型**：KMDP158 / KMDP358 / KMDP458 / KMUP380C

::: info 说明
连续纸小票机的原生 ESC/POS 指令打印，需 base64 编码后传入。
:::

<ApiPage />
