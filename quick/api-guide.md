# 接口选择指南

## 按业务场景选接口

| 业务场景 | 推荐接口 | 备选 |
|---------|---------|------|
| 打小票（连续纸，最常用）| [escTemplatePrint](../http/template-print/esc-print) | escXmlWrite / escWrite |
| 打标签 / 面单（最常用）| [tsplTemplatePrint](../http/template-print/tspl-print) | tsplXmlWrite / deviceWrite |
| 打小票（间隙纸）| [tsplTemplateWrite](../http/template-print/tspl-template-write) | — |
| 共享云打印（电脑USB打印机）| [shareFilePrint](../http/custom-print/share-file-print) | — |
| 设备绑定 / 解绑 | [bindDevice](../http/device/bind-device) / [unbindDevice](../http/device/unbind-device) | — |
| 打印机状态查询 | [batchStatus](../http/device/batch-status) | — |
| 打印任务结果 | [callback（异步回调）](../http/task/callback)（推荐）| [result（轮询）](../http/task/print-result) |
| 调节打印浓度 | [adjustDeviceDensity](../http/device/adjust-density) | — |
| 取消打印队列 | [cancelJob](../http/task/cancel-job) | — |
| 语音播报来单 | [broadcast](../http/device/broadcast) | — |
| 模板列表查询 | [pageTemplate](../http/template-print/page-template) | — |
| 云服务流量券充值 | [cloudServiceRecharge](../http/billing/cloud-service-recharge) | — |

## 按机型反查推荐接口

| 机型 | 模板打印 | XML 打印 | 原生指令 | 备注 |
|------|---------|---------|---------|------|
| KM118 系列（除 KM118EG/MEG）| tsplTemplatePrint<br/>tsplTemplateWrite<br/>escTemplatePrint | tsplXmlWrite<br/>escXmlWrite | deviceWrite | 全功能 |
| KME31 / KME41 系列 | 同上 | 同上 | deviceWrite | 全功能 |
| KME20 系列 | tsplTemplatePrint<br/>tsplTemplateWrite<br/>escTemplatePrint | tsplXmlWrite<br/>escXmlWrite | — | — |
| KMSX320 | tsplTemplatePrint<br/>tsplTemplateWrite | tsplXmlWrite | — | XML jobs ≤ 5 单 |
| KMUL410 | tsplTemplatePrint<br/>tsplTemplateWrite | tsplXmlWrite | — | — |
| KMDP 系列（158/358/458）| escTemplatePrint | escXmlWrite | escWrite | 仅连续纸 |
| KMUP380C | — | — | escWrite | 仅原生 ESC |
| KM118MGL / KME31GP | 同 KM118/KME31 | 同 KM118/KME31 | 同 KM118/KME31 | 额外支持语音播报 |

::: tip 接口分类说明
- **模板打印**：在后台设计好模板，传 `templateId` + `renderData` 即可打印，门槛低
- **XML 打印**：直接下发 TSPL/ESC XML 指令，灵活但需要了解指令格式，见 [XML 指令参考](../ref/xml-spec)
- **原生指令**：直接下发设备指令字节，最灵活，需要 GBK 编码与 base64，适合已有指令生成库的场景
:::
