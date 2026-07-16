---
search: false
title: 图片打印（Base64）
url: /api/cloud/print/printImageWithBase64
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
  - name: imageBase64
    type: String
    required: true
    desc_zh: "图片 Base64 字符串，建议 ≤ 50KB"
    desc_en: "Base64 image string, ≤ 50KB recommended"
  - name: kmPrintTimes
    type: Number
    required: true
    desc_zh: 打印份数
    desc_en: Copies
  - name: extra
    type: String
    required: true
    desc_zh: "`0`=TSPL 标签机 / `2`=ESC 小票机"
    desc_en: "`0`=TSPL label / `2`=ESC receipt"
  - name: printPreview
    type: Number
    required: false
    desc_zh: "传 1 则按下方尺寸缩放"
    desc_en: "1 to scale to specified size"
  - name: previewWidth
    type: String
    required: false
    desc_zh: 缩放宽度（dot）
    desc_en: Scaled width (dots)
  - name: previewHeight
    type: String
    required: false
    desc_zh: 缩放高度（dot）
    desc_en: Scaled height (dots)
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
    fix_zh: 确认 SN 正确
    fix_en: Verify SN
  - code: "4005"
    desc_zh: 设备离线
    desc_en: Device offline
    fix_zh: 检查设备网络
    fix_en: Check device network
  - code: "6018"
    desc_zh: 图片数据错误
    desc_en: Invalid image data
    fix_zh: 确认 Base64 编码正确且图片格式合法
    fix_en: Verify Base64 encoding and image format
---

# 图片打印（Base64）

::: warning SDK 专属接口
本接口**未收录在 HTTP 协议文档**中，仅通过多语言 SDK（Java / Python / JS / iOS / Android / UniApp / 微信小程序插件）封装调用；参数签名逻辑由 SDK 内部处理。

HTTP 直连场景请改用 [deviceWrite](/zh/http/custom-print/device-write) / [escWrite](/zh/http/custom-print/esc-write)（TSPL/ESC 指令）或 [shareFilePrint](/zh/http/custom-print/share-file-print)（共享云打印）。
:::

<ApiPage />
