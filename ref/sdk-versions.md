# SDK 版本清单

::: info 版本口径
下表版本号取自随 demo 交付的 SDK 包名与官方 markdown 文档；实际以对接同学口径为准。
发布新版本时只需修改本页 `docs/ref/sdk-versions.js`。
:::

## 服务端 SDK

| SDK | 版本 | 运行环境 | 依赖 / 说明 | 获取 |
|---|---|---|---|---|
| Java | kuaimai-core-*.jar | JDK 8+ | PDF 打印依赖 `org.apache.pdfbox:pdfbox`；jar 未上 Maven Central | demo lib/ |
| PHP | php-kuaimai-core（未上 Packagist） | PHP ≥ 8.0 | 扩展 `curl / gd / zlib / json / mbstring`；PDF 需 Ghostscript；本地渲染需 chillerlan/php-qrcode + picqer/php-barcode-generator | 随 demo 交付 · 用 `composer path` 引入 |
| Python | kuaimai_core（随 demo 交付） | Python 3.10+ | PDF 建议装 Ghostscript；requirements.txt 声明的其他依赖用 venv 装即可 | demo `python-cloud-demo/` |

## Web / 桌面 / 跨端

| SDK | 版本 / 入口 | 运行环境 | 说明 |
|---|---|---|---|
| JavaScript (UMD) | `KM_SDK.umd.js` | 浏览器 | CDN：`https://label-open.kuaimai.com/cloudPrinterSDK/KM_SDK.umd.js`。secret 会暴露在客户端，生产环境建议走服务端签名代理。 |
| UniApp 原生插件 | `KmUniPrinter` | App 端（iOS / Android） | 需要打自定义基座；H5 请改用 JS SDK，微信小程序请用 km-plugin。 |
| 微信小程序插件 | km-plugin v2.0.1（provider `wx82f4257ee8e405e5`） | 微信小程序 | 同时提供云打印 + BLE 蓝牙打印；接入前需在 app.json 声明。 |

## 移动端 SDK

| SDK | 版本 / 包名 | 运行环境 | 说明 |
|---|---|---|---|
| Android | cloudprintsdk_1.0.1（AAR / JAR） | Android 5.0+ 建议 | 需要 INTERNET 权限；调用要在子线程。 |
| iOS | CloudPrint_sdk（framework / static lib） | iOS 9.0+ | HTTP 场景需在 Info.plist 添加 ATS 例外。 |

## 变更策略

- **接口新增字段**：属于向后兼容，直接在对应 SDK 里增加 setter；旧代码不受影响。

  - **错误码新增**：同步更新[错误码总表](#error-codes)并在版本变更里备注。

  - **破坏性变更**：SDK 主版本号 +1（如 1.x → 2.x），并在此页顶部加显眼 callout；老版本至少保留一个季度。

::: tip 如何反馈版本问题
发现某个 SDK 版本行为与本站描述不一致，请提供：SDK 具体版本、平台、接口名、请求 / 响应报文。可以联系对接同学或提交到 [demo 仓库 issues](https://github.com/xuli2016/kuaimai-cloud-demo/issues)。
:::
