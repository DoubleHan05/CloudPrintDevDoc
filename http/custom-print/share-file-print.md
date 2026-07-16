---
title: 共享云打印
url: /api/cloud/print/shareFilePrint
method: POST
noCurl: true
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
    desc_zh: 共享云打印机序列号
    desc_en: Shared printer SN
  - name: file
    type: File
    required: true
    desc_zh: "PNG 图片经 zlib 压缩后的数据文件，≤ 150KB"
    desc_en: "zlib-compressed PNG, ≤ 150KB"
  - name: width
    type: Number
    required: true
    desc_zh: "打印宽度，单位 mm"
    desc_en: Print width in mm
  - name: height
    type: Number
    required: true
    desc_zh: "打印高度，单位 mm"
    desc_en: Print height in mm
requestExample: |
  {
    "sn": "1609142313064",
    "file": "(图片 zlib 压缩数据)",
    "width": 70,
    "height": 130,
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
    fix_zh: 确认 SN 正确
    fix_en: Verify SN
  - code: "4005"
    desc_zh: 设备离线
    desc_en: Device offline
    fix_zh: 检查设备网络
    fix_en: Check device network
  - code: "6029"
    desc_zh: "文件大小超过 150KB"
    desc_en: "File exceeds 150KB"
    fix_zh: 压缩后确保 ≤ 150KB
    fix_en: Ensure compressed size ≤ 150KB
  - code: "6030"
    desc_zh: 当前设备不是共享云打印机
    desc_en: Device is not a shared cloud printer
    fix_zh: 确认设备已在番茄打印管家中开通共享
    fix_en: Ensure device is shared via Print Manager
  - code: "6032"
    desc_zh: "单设备单日超过 1000 次"
    desc_en: "Exceeds 1000 prints/day per device"
    fix_zh: 控制每日打印频率
    fix_en: Reduce daily print volume
  - code: "6034"
    desc_zh: 文件不是 zlib 压缩
    desc_en: File is not zlib-compressed
    fix_zh: 确保上传前对 PNG 做 zlib 压缩
    fix_en: Apply zlib compression to PNG before upload
---

# 共享云打印

**适用机型**：电脑安装番茄打印管家并开通共享打印的 USB 连接机器

::: warning 特殊 Content-Type
本接口使用 `multipart/form-data`，与其他 JSON 接口不同。
:::

<ApiPage />
