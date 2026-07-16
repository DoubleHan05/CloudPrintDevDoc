# UniApp SDK

::: tip SDK 下载
[下载 UniApp 插件](https://kuaimai-public.oss-cn-shanghai.aliyuncs.com/public/document/uniplugin_km_sdk.zip)
:::

::: info 获取 APPID
登录 [http://admin.iot.kuaimai.com/](http://admin.iot.kuaimai.com/) 申请，拿到固定的 `APPID`。
:::

## 环境要求 / 引入

- UniApp 项目需引入快麦的**原生插件** `KmUniPrinter`（无法通过纯 JS 使用，需要打自定义基座）。

  - 只支持 App 端（iOS / Android），H5 / 微信小程序请分别用 [JS SDK](#) / [微信小程序插件](#)。

```全局引用
// 引入原生插件（打自定义基座后可用）
const kmUniPrinter = uni.requireNativePlugin("KmUniPrinter");
```

## 调用约定

所有方法以 `cloud_` 为前缀，接受一个 `options` 对象和 **回调函数**。回调收到的 `result` 是 JSON 字符串或对象（依平台而定），结构与 HTTP 直连一致：`{ status, data, message, code }`。

## API 一览

| 方法 | 描述 | 适用机型 |
|---|---|---|
| cloud_bindDevice | 绑定打印机 | 所有机型 |
| cloud_tsplTemplatePrint | 标签模板打印（间隙纸） | KM118DW / KM118MW / KMSX320 / KME20W |
| cloud_tsplTemplateWrite | 小票模板打印（间隙纸） | 同上 |
| cloud_escTemplatePrint | 小票模板打印（连续纸） | KM118DW / KM118MW / KMSX320 / KME20W / KMDP158 / KMDP358 / KMDP458 |
| cloud_tsplXmlWrite / cloud_escXmlWrite | 自定义 XML 打印 | 见上 |
| cloud_printImageWithBase64 | Base64 图片打印 | KM118DW / KM118MW |
| cloud_printResult | 打印任务结果查询 | 所有机型 |
| cloud_batchStatus | 批量设备状态查询 | 所有机型 |

## 1. 绑定打印机

```uniapp
kmUniPrinter.cloud_bindDevice({
  sn: 'km789838899',
  appId,
  secret,
  deviceKey: '',
  bindName: 'user_10086',
  noteName: '杭州分店',
}, (result) => {
  console.log(result);
});
```

## 2. 标签模板打印（间隙纸）

```uniapp
const renderDataArray =
  '[{"bindTable1":[{"bindkey1":"value1"}]}]';

kmUniPrinter.cloud_tsplTemplatePrint({
  sn: 'km789838899',
  appId, secret,
  templateId: 1,
  renderDataArray,
  printTimes: '1',
}, (result) => console.log(result));
```

## 3. 小票模板打印

```间隙纸
kmUniPrinter.cloud_tsplTemplateWrite({
  sn: 'km789838899', appId, secret,
  templateId: 1,
  renderData: '{"bindTable1":[{"bindkey1":"value1"}]}',
}, (result) => console.log(result));
```

```连续纸
kmUniPrinter.cloud_escTemplatePrint({
  sn: 'km789838899', appId, secret,
  templateId: 1,
  renderData: '{"bindTable1":[{"bindkey1":"value1"}]}',
  volume: '80',
  volumeIndex: '2051',
  cut: true,
  endFeed: '9',
}, (result) => console.log(result));
```

## 4. 自定义 XML 打印

```tsplxmlwrite
kmUniPrinter.cloud_tsplXmlWrite({
  sn: 'km789838899', appId, secret,
  xmlStr: "<page><render><t>hello,word</t></render></page>",
  image: false,
  printTimes: '1',
}, (result) => console.log(result));
```

```escxmlwrite
kmUniPrinter.cloud_escXmlWrite({
  sn: 'km789838899', appId, secret,
  instructions: "<page><render><t size='10'>hello</t></render></page>",
  volume: '80',
  volumeIndex: '2051',
  cut: 1,
}, (result) => console.log(result));
```

## 5. 图片打印（Base64）

```uniapp
kmUniPrinter.cloud_printImageWithBase64({
  sn: 'km789838899', appId, secret,
  imageBase64: 'iVBORw0KGgoAAAANSUhEUgAA...',
  kmPrintTimes: 1,
  extra: '0',              // "0" TSPL / "2" ESC
  printPreview: 1,         // 1 = 缩放
  previewWidth: 800,       // dot
  previewHeight: 600,      // dot
}, (result) => console.log(result));
```

## 6. 结果 / 状态查询

```printresult
kmUniPrinter.cloud_printResult({
  sn: 'km789838899', appId, secret,
  jobIdStr: '["111","222"]',   // JSON 数组字符串 ≤ 50
}, (result) => console.log(result));
```

```batchstatus
kmUniPrinter.cloud_batchStatus({
  appId, secret,
  snsStr: '["222","444"]',     // ≤ 100
}, (result) => console.log(result));
```

## 错误码

::: warning 错误码提示
回调 `result` 中的 `code` 与 HTTP 直连一致：4001 / 4005 / 4009 / 4015 / 6006 / 6009 / 6010 / 6015 / 6019 / 6021 / 6022 / 6024 / 6026。完整表见[错误码总表](#error-codes)。
:::
