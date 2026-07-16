# JavaScript SDK

::: info 获取 APPID
登录 [http://admin.iot.kuaimai.com/](http://admin.iot.kuaimai.com/) 申请应用，拿到固定的 `APPID`（对接方唯一标识）。
:::

::: warning 安全提示
浏览器端会直接暴露 `appId` 与 `secret`。生产环境中如果 `secret` 敏感，建议只在受信任的后台或桌面端使用 JS SDK；对外提供的 Web 页面请走服务端签名后代理调用。
:::

## 引入 SDK

推荐用 `<script>` 引入 UMD 包（暴露全局 `KM_SDK`）：

```html
<script src="https://label-open.kuaimai.com/cloudPrinterSDK/KM_SDK.umd.js"></script>

<script>
  // 把实例注册成全局变量
  const KMSDK = new KM_SDK();
</script>
```

## 调用约定

所有方法都是**参数对象 + callback** 的形式，无 Promise 封装。响应通过 `callback(res)` 回调，`res` 结构统一：

```回调响应
{
  status: true,        // boolean，成功/失败
  data: { ... },       // 具体接口的返回
  message: null,       // 失败时的描述
  code: null           // 业务错误码
}
```

## API 一览

| 方法 | 用途 |
|---|---|
| new KM_SDK() | 初始化 SDK 实例 |
| getNewTemplateId | 新增标签/小票前拿到唯一模板 id（每次新增都要取） |
| initNewLabel / initEditLabel | 打开标签编辑器 iframe（新增 / 编辑） |
| initNewTicket / initEditTicket | 打开小票编辑器 iframe（新增 / 编辑） |
| getTemplateList | 获取用户模板列表 |
| deleteTemplate / copyTemplate | 删除 / 复制模板 |
| bindDevice | 绑定打印机（含 KM360C 用 imei + code） |
| tsplTemplatePrint | 标签模板打印（间隙纸） |
| tsplTemplateWrite | 小票模板打印（间隙纸） |
| escTemplatePrint | 小票模板打印（连续纸） |
| tsplXmlWrite / escXmlWrite | 自定义 XML 打印 |
| deviceWrite / escWrite | 原生 TSPL / ESC 指令打印 |
| printImageWithBase64 | Base64 图片打印（TSPL 或 ESC，机型见正文） |
| deliveryNoteTemplatePrint | 发货单模板打印（KMH01 系列） |
| printResult | 打印任务结果查询 |
| batchStatus | 打印机状态查询 |
| printFile | PDF / 图片文件打印（直接传 File 对象） |
| getDeviceCode | KM360C 定制 API：获取 code，机器会吐纸打印 |

## 1. 编辑器全流程：新增标签模板

```javascript
// step 1: 拿到新的 templateId
KMSDK.getNewTemplateId({
  appId: APPID,
  itemsId: '1',
  callback: (newId) => {
    // step 2: 打开新增编辑器 iframe
    KMSDK.initNewLabel({
      id: newId,
      el: '#app',              // 编辑器挂载容器
      appId: APPID,
      itemsId: '1',
      userId: '13812341234',   // 用户唯一标识
      libraryType: '1',        // 1 用户模板 / 2 公共模板
      printFlag: false,        // 编辑器内是否显示打印按钮
      save: (res) => {
        // { id: 66, status: 'success' | 'fail' }
        console.log('saved', res);
      },
      close: () => console.log('closed'),
    });
  },
});
```

## 2. 编辑已有模板

```标签
KMSDK.initEditLabel({
  id: 66,                 // 现有模板 id（从列表拿）
  el: '#app',
  appId: APPID,
  itemsId: '1',
  userId: '13812341234',
  libraryType: '1',
  save: (res) => {},
});
```

```小票
KMSDK.initNewTicket({ /* 与 initNewLabel 参数一致 */ });
KMSDK.initEditTicket({ /* 与 initEditLabel 参数一致 */ });
```

## 3. 模板列表 / 删除 / 复制

```javascript
// 获取列表
KMSDK.getTemplateList({
  appId: APPID,
  userId: '13812341234',
  callback: (list) => console.log(list),
});

// 删除
KMSDK.deleteTemplate({ appId: APPID, id: 66, callback: () => {} });

// 复制
KMSDK.copyTemplate({ appId: APPID, id: 66, callback: () => {} });
```

## 4. 绑定设备

::: tip KM360C
需要传 `imei` 和 `code`（`code` 通过 `getDeviceCode` 获取，机器会吐纸打印 code）。其它机型只需要传 `sn`。
:::

```普通机型
KMSDK.bindDevice({
  appId: APPID,
  secret: SECRET,
  sn: 'km789838899',
  deviceKey: '',            // 机身贴有密钥时必传
  bindName: 'user_10086',
  noteName: '杭州分店',
  callback: (res) => console.log('bindDevice', res),
});
```

```km360c
// 1. 先获取 code（机器会吐纸打印 code）
KMSDK.getDeviceCode({
  appId: APPID, secret: SECRET, imei: '8888xxxx8888',
  callback: (res) => {
    // 2. 用 imei + code 完成绑定
    KMSDK.bindDevice({
      appId: APPID, secret: SECRET,
      imei: '8888xxxx8888',
      code: '7764',
      callback: (r) => console.log(r),
    });
  },
});
```

## 5. 标签模板打印（间隙纸）

适用机型：KM118DW / KM118MW / KMSX320 / KME20W / KM360C。

```javascript
KMSDK.tsplTemplatePrint({
  appId: APPID,
  secret: SECRET,
  sn: 'km789838899',
  // KM360C 场景：删掉 sn 改传 imei
  templateId: 1,
  renderDataArray: '[{"bindTable1":[{"bindkey1":"value1"}]}]',
  printTimes: 1,
  dpi: 203,       // 203 通用；300 高清机
  ribbon: 1,      // 传 1 = 碳带打印
  callback: (res) => console.log('tsplTemplatePrint', res),
});
```

## 6. 小票模板打印

```间隙纸tspltemplatewrite
KMSDK.tsplTemplateWrite({
  appId: APPID, secret: SECRET, sn: 'km789838899',
  templateId: 1,
  renderData: '{"bindTable1":[{"bindkey1":"value1"}]}',
  callback: (res) => console.log(res),
});
```

```连续纸esctemplateprint
KMSDK.escTemplatePrint({
  appId: APPID, secret: SECRET, sn: 'km789838899',
  templateId: 1,
  renderData: '{"bindTable1":[{"bindkey1":"value1"}]}',
  volume: 80,          // 语音机型
  volumeIndex: 2051,
  cut: true,           // 切刀机型
  endFeed: 9,
  dpi: 203,
  callback: (res) => console.log(res),
});
```

## 7. 自定义 XML 打印

```tsplxml
KMSDK.tsplXmlWrite({
  appId: APPID, secret: SECRET, sn: 'km789838899',
  xmlStr: "<page><render><t>hello,word</t></render></page>",
  image: false,
  printTimes: 1,
  callback: (res) => console.log(res),
});
```

```escxml
KMSDK.escXmlWrite({
  appId: APPID, secret: SECRET, sn: 'km789838899',
  instructions: "<page><render><t size='10'>hello,word</t></render></page>",
  volume: 80,
  volumeIndex: 2051,
  cut: 1,
  callback: (res) => console.log(res),
});
```

## 8. 原生指令打印

```tspldevicewrite
KMSDK.deviceWrite({
  appId: APPID, secret: SECRET, sn: 'km789838899',
  instructs: "CLS\\r\\nSIZE 75mm,100mm\\r\\nTEXT 50,50,\\"0\\",0,1,1,\\"Hello World!\\"\\r\\nPRINT 1\\r\\n",
  callback: (res) => console.log(res),
});
```

```escescwrite
KMSDK.escWrite({
  appId: APPID, secret: SECRET, sn: 'km789838899',
  // 已 base64 编码的 ESC 指令
  instructionsList: "H3MCAAAAAAD/AAAdIQAbYQ...",
  copies: 1,          // 最大 30
  callback: (res) => console.log(res),
});
```

## 9. 图片打印（Base64）

```javascript
KMSDK.printImageWithBase64({
  appId: APPID, secret: SECRET, sn: 'km789838899',
  // KM360C：改传 imei
  imageBase64: 'iVBORw0KGgoAAAANSUhEUgAA...',
  kmPrintTimes: 1,
  extra: '0',             // "0" = TSPL 标签机 / "2" = ESC 小票机
  printPreview: true,     // 是否缩放
  previewWidth: 100,      // 缩放宽度 mm
  previewHeight: 500,     // 缩放高度 mm
  dpi: 203,
  ribbon: 1,              // 碳带
  callback: (res) => console.log(res),
});
```

## 10. 发货单模板打印

支持动态表格模板打印（叠式热敏发货单、叠式发票纸、黑标发货单）。适用机型：KMH01。

```javascript
KMSDK.deliveryNoteTemplatePrint({
  appId: APPID, secret: SECRET, sn: 'km789838899',
  templateId: 1,
  renderData: '{"bindTable1":[{"bindkey1":"value1"}]}',
  dpi: 300,     // 默认 180
  callback: (res) => console.log(res),
});
```

## 11. PDF / 图片文件打印 printFile

直接传 File 对象数组（`<input type="file">` 或 拖拽拿到的 File）。

```javascript
const fileInput = document.querySelector('input[type=file]');
const file = fileInput.files[0];

KMSDK.printFile({
  appId: APPID, secret: SECRET, sn: 'km789838899',
  // KM360C：改传 imei
  file: [file],           // File 数组
  isPDF: true,            // 打印内容是否为 PDF
  kmPrintTimes: 1,
  extra: '0',             // "0" TSPL / "2" ESC
  printPreview: true,
  previewWidth: 100,
  previewHeight: 500,
  dpi: 203,
  ribbon: 1,
  callback: (res) => console.log(res),
});
```

## 12. 结果查询 / 状态查询

```printresult
KMSDK.printResult({
  appId: APPID, secret: SECRET, sn: 'km789838899',
  jobIdStr: '["111","222"]',  // JSON 数组字符串 ≤ 50
  callback: (res) => {
    // res.data: [{ jobId, desc, code }]
    // code: 2000 成功 / 2006 等待 / 2007 失败 / 2004 异常
  },
});
```

```batchstatus
KMSDK.batchStatus({
  appId: APPID, secret: SECRET,
  snsStr: '["222","444"]',   // JSON 数组字符串 ≤ 100
  callback: (res) => console.log(res),
});
```

## 13. KM360C：getDeviceCode

```javascript
KMSDK.getDeviceCode({
  appId: APPID, secret: SECRET,
  imei: '8888xxxx8888',     // 机器打印自检页查看
  callback: (res) => console.log('getDeviceCode', res),
});
// 机器会吐一张纸打印 code，5 分钟内用 bindDevice(imei, code) 完成绑定。
```

## 常见错误码

::: warning 错误码提示
4000（SN 为空）· 4001（未激活）· 4005（离线）· 4009（密钥错）· 4012（无权限设备）· 4015（签名错）· 6006（缺参数）· 6009（渲染数据错）· 6010（模版类型不匹配）· 6015（无权访问模版）· 6018（图片 base64 错）· 6019（内容过大：一般 100KB；UP 系列 30KB）· 6021（查询过频）· 6022（>50 单）· 6025（查设备 >100）· 6026（jobId >50）· 6027（打印接口过频）· 6029/6030/6031/6032/6034（共享云打印相关）· 6035/6036（语音）· 6039（不支持调浓度）。完整表见[错误码总表](#error-codes)。
:::
