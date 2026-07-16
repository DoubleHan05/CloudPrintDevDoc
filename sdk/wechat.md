# 微信小程序插件

::: info 插件性质
本插件是快麦 JS SDK 的**微信小程序版本**，以小程序插件形式同时提供云打印与蓝牙打印。云打印通过网络下发；蓝牙走 BLE 直连打印机。
:::

## 前提条件

- 在快麦管理后台（[http://admin.iot.kuaimai.com/](http://admin.iot.kuaimai.com/)）取得 **APPID** 与 **Secret**。

  - 已申请插件使用权限，插件 AppID：`wx82f4257ee8e405e5`。

  - 调试阶段在微信开发者工具打开「不校验合法域名」。

## 接入步骤

```1.app.json声明插件
{
  "plugins": {
    "km-plugin": {
      "version": "2.0.1",
      "provider": "wx82f4257ee8e405e5"
    }
  }
}
```

```2.页面中引入
const plugin = requirePlugin('km-plugin');

// 环境（默认线上）：建议在 App.onLaunch 里设置
plugin.setOnline(true);   // 线上
// plugin.setOnline(false); // 测试

// 云打印实例
const sdk = new plugin.KM_SDK();

// 蓝牙打印（单例）
const bluetooth = plugin.KM_Bluetooth;
```

## callback 响应格式

所有云打印 API 的 `callback` 都收到相同结构：`{ status, data, message, code }`。

## 云打印 API 一览

| 方法 | 描述 |
|---|---|
| new plugin.KM_SDK() | 创建 SDK 实例 |
| plugin.setOnline(bool) | 切换线上 / 测试环境 |
| sdk.getDeviceCode | KM360C 专用：请求 code（机器吐纸打印） |
| sdk.bindDevice | 绑定打印机 |
| sdk.tsplTemplatePrint | 标签模板打印（间隙纸） |
| sdk.tsplTemplateWrite | 小票模板打印（间隙纸） |
| sdk.escTemplatePrint | 小票模板打印（连续纸）；SN 以 `share_` 开头的共享设备不支持 |
| sdk.tsplXmlWrite / sdk.escXmlWrite | 自定义 XML 打印 |
| sdk.deviceWrite / sdk.escWrite | 原生 TSPL / ESC 指令打印 |
| sdk.printImageWithBase64 | Base64 图片打印（KMH01 云盒和 share_ 设备各有特殊处理） |
| sdk.printFile | 图片文件打印（文件路径 / ArrayBuffer；暂不支持 PDF） |
| sdk.tsplTemplatePrintWeb | 前端渲染版模板打印（OffscreenCanvas + 图片打印管线） |
| sdk.needleMachineTemplatePrint | 针式机器模板打印（ESC2 指令 + OffscreenCanvas 渲染） |
| sdk.printResult | 结果查询 |
| sdk.batchStatus | 状态查询 |

::: warning 不支持
`deliveryNoteTemplatePrint` 等依赖 fabricjs/Vue 组件的方法在小程序插件里暂不可用。`printFile` 目前只支持图片，不支持 PDF。
:::

## 1. 绑定 / KM360C 三段式

```普通机型
sdk.bindDevice({
  appId: 'YOUR_APP_ID',
  secret: 'YOUR_SECRET',
  sn: 'KME41G245xxxxx5',
  deviceKey: '',
  bindName: 'user_10086',
  noteName: '杭州分店',
  callback: (res) => console.log(res),
});
```

```km360c
// 1. 请求 code（机器打印出 code）
sdk.getDeviceCode({
  appId, secret, imei: '8888xxxx8888',
  callback: () => {
    // 2. 用 imei + code 绑定
    sdk.bindDevice({
      appId, secret,
      imei: '8888xxxx8888',
      code: '7764',
      callback: (r) => console.log(r),
    });
  },
});
```

## 2. 标签模板打印（间隙纸）

适用机型：KM118DW / KM118MW / KMSX320 / KME20W / KM360C。

```javascript
sdk.tsplTemplatePrint({
  appId: 'YOUR_APP_ID',
  secret: 'YOUR_SECRET',
  sn: 'KME41G245xxxxx5',       // KM360C: 改传 imei
  templateId: 1634973609,
  renderDataArray: '[{"tableName":[{"key1":"数据3"}]}]',
  printTimes: 1,
  dpi: 203,          // 203 通用 / 300 高清机
  ribbon: 1,         // 1 = 碳带
  callback: (res) => console.log(res),
});
```

## 3. 小票模板打印

```间隙纸tspltemplatewrite
sdk.tsplTemplateWrite({
  appId, secret, sn: 'KME41G245xxxxx5',
  templateId: 1634955259,
  renderData: '{"tableName":[{"key1":"数据3"}]}',
  callback: (res) => console.log(res),
});
```

```连续纸esctemplateprint
sdk.escTemplatePrint({
  appId, secret, sn: 'KME41G245xxxxx5',
  templateId: 1634959703,
  renderData: '{"223":[{"344":"数据3"}]}',
  volume: 80,
  volumeIndex: 2051,
  cut: true,
  endFeed: 9,
  callback: (res) => console.log(res),
});
```

## 4. 自定义 XML 打印

```tsplxmlwrite
sdk.tsplXmlWrite({
  appId, secret, sn: 'KME41G245xxxxx5',
  xmlStr: '<page><render><t>hello,world</t></render></page>',
  image: false,
  printTimes: 2,
  callback: (res) => console.log(res),
});
```

```escxmlwrite
sdk.escXmlWrite({
  appId, secret, sn: 'KME41G245xxxxx5',
  instructions: "<page><render><t size='10'>hello,world</t></render></page>",
  volume: 80,
  volumeIndex: 2051,
  cut: 1,
  callback: (res) => console.log(res),
});
```

## 5. 原生指令打印

```tspldevicewrite
sdk.deviceWrite({
  appId, secret, sn: 'KME41G245xxxxx5',
  instructs: 'CLS\\r\\nSIZE 75mm,100mm\\r\\nTEXT 50,50,"0",0,1,1,"Hello World!"\\r\\nPRINT 1\\r\\n',
  callback: (res) => console.log(res),
});
```

```escescwrite
sdk.escWrite({
  appId, secret, sn: 'KME41G245xxxxx5',
  instructionsList: 'H3MCAAAAAAD/AAAdIQAbYQ...',   // base64 编码
  copies: '1',    // 最大 30
  callback: (res) => console.log(res),
});
```

## 6. Base64 图片打印

::: tip 机型差异
TSPL 机型 `extra` 传 `'0'`，ESC 机型传 `'2'`。KMH01 云盒使用 gzip + 专属 prereq；`share_` 共享设备用 BMP+deflate 走文件上传（压缩后 ≤ 150KB）。
:::

```javascript
sdk.printImageWithBase64({
  appId, secret, sn: 'KME41G245xxxxx5',
  imageBase64: 'iVBORw0KGgoAAAANSUhEUgAA...',
  kmPrintTimes: 1,
  extra: '0',              // "0" TSPL / "2" ESC
  printPreview: true,
  previewWidth: 100,       // mm
  previewHeight: 500,      // mm
  dpi: 203,
  ribbon: 1,
  callback: (res) => console.log(res),
});
```

## 7. 结果 / 状态查询

```printresult
sdk.printResult({
  appId, secret, sn: 'KME41G245xxxxx5',
  jobIdStr: '["1703052433359","1703052433360"]',
  callback: (res) => console.log(res),
});
```

```batchstatus
sdk.batchStatus({
  appId, secret,
  snsStr: JSON.stringify(['KME41G245xxxxx5', 'KMDP358xxxx501']),  // ≤ 100
  callback: (res) => console.log(res),
});
```

## 完整使用示例

```pages/index/index.js
const plugin = requirePlugin('km-plugin');

Page({
  onLoad() {
    plugin.setOnline(true);
    this.sdk = new plugin.KM_SDK();
  },
  onBindDevice() {
    this.sdk.bindDevice({
      appId: 'YOUR_APP_ID',
      secret: 'YOUR_SECRET',
      sn: 'KME41G245xxxxx5',
      callback: (res) => {
        if (res.status) console.log('绑定成功', res.data);
        else console.error('绑定失败', res.message, res.code);
      },
    });
  },
  onPrint() {
    this.sdk.tsplTemplatePrint({
      appId: 'YOUR_APP_ID',
      secret: 'YOUR_SECRET',
      sn: 'KME41G245xxxxx5',
      templateId: 1634973609,
      renderDataArray: '[{"tableName":[{"key1":"数据3"}]}]',
      printTimes: 1,
      callback: (res) => {
        if (res.status) console.log('打印任务已提交', res.data);
        else console.error('打印失败', res.message);
      },
    });
  },
});
```

## 蓝牙打印（可选）

插件同时提供 `KM_Bluetooth` 模块，可通过 BLE 直连打印机发送 TSPL / CPCL 指令。适合无网络场景。

```典型流程
const bluetooth = plugin.KM_Bluetooth;

// 1. 开启蓝牙适配器
bluetooth.openBluetoothAdapter({ success() {}, fail() {} });

// 2. 扫描
bluetooth.startBluetoothDevicesDiscovery({ /* onDeviceFound 回调 */ });

// 3. 连接
bluetooth.createBLEConnection({ deviceId, success() {}, fail() {} });

// 4. 发送
bluetooth.sendString(tsplString);         // 字符串
bluetooth.sendArrayBuffer(arrayBuffer);   // 推荐：字节流

// 5. 断开 / 关闭适配器
bluetooth.closeBLEConnection({ deviceId });
bluetooth.closeBluetoothAdapter();
```

完整蓝牙 API（`openBluetoothAdapter` / `startBluetoothDevicesDiscovery` / `stopBluetoothDevicesDiscovery` / `createBLEConnection` / `closeBLEConnection` / `onBLEConnectionStateChange` / `sendString` / `sendArrayBuffer`）用法与微信开放蓝牙 API 一致，插件负责封装设备发现回调与 characteristic 写入。

## 错误码

::: warning 错误码提示
4000（SN 为空）· 4001（未激活）· 4005（离线）· 4009（密钥错）· 4012（无权限）· 4015（签名错）· 6006 / 6009 / 6010 / 6015 / 6018 / 6019 / 6021 / 6022 / 6025 / 6026 / 6027 / 6029 / 6030 / 6031 / 6032 / 6034 / 6035 / 6036 / 6039。完整表见[错误码总表](#error-codes)。
:::
