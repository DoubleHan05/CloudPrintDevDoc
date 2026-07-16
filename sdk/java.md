# Java SDK

::: tip SDK 下载
[GitHub 仓库](https://github.com/xuli2016/kuaimai-cloud-demo)
:::

::: info Demo 仓库
完整示例见 [kuaimai-cloud-demo](https://github.com/xuli2016/kuaimai-cloud-demo/tree/main)（Java 分支）。
:::

## 环境要求

- **JDK 8 及以上**。JDK 11 / 17 均可运行。

  - SDK 以本地 jar 交付：`kuaimai-core-*.jar`，位于 demo 仓库的 `lib/` 目录，**未发布到 Maven Central**。

  - PDF 打印能力需要额外引入 `PDFBox`（PDF 转图片时使用）。

## 初始化客户端

```java
import com.kuaimai.core.KuaimaiClient;

KuaimaiClient client = KuaimaiClient.createClient(appId, appSecret);
```

整个应用可以复用同一个 `client` 实例，内部维护连接池；不要为每次调用都新建。

## API 一览

| Request 类 | 描述 | 适用机型 |
|---|---|---|
| BindDeviceRequest | 绑定设备 | 所有机型 |
| UnbindDeviceRequest | 解绑设备 | 所有机型 |
| QueryDeviceStatusRequest | 批量查询设备状态（单次最多 100 台） | 所有机型 |
| TsplTemplatePrintRequest | 标签模板打印（间隙纸） | KM118 / KME31 / KME41 / KME20 / KMSX 系列 |
| EscTemplatePrintRequest | 小票模板打印（连续纸） | KM118 / KME31 / KME41 / KME20 系列 |
| TsplTemplateWriteRequest | 小票模板打印（间隙纸） | KM118 / KME31 / KME41 / KME20 系列 |
| TsplXmlWriteRequest | 自定义 XML 打印（间隙纸） | KM118 / KME31 / KME41 / KME20 系列 |
| EscXmlWriteRequest | 自定义 XML 打印（连续纸） | KM118 / KME31 / KME41 / KME20 / KMDP / KMUP 系列 |
| TsplImageRequest | 图片直接打印（间隙纸） | KM118 / KME31 / KME41 系列 |
| EscImageRequest | 图片直接打印（连续纸） | KM118 / KME31 / KME41 系列 |
| TsplPdfPrintRequest | PDF 直接打印（间隙纸），依赖 PDFBox | KM118 / KME31 / KME41 系列 |
| EscPdfPrintRequest | PDF 直接打印（连续纸），依赖 PDFBox | KM118 / KME31 / KME41 系列 |
| ResultRequest | 打印任务结果查询 | KM118 / KME31 / KME41 / KME20 系列 |
| BroadcastRequest | 语音播报 | KM118MGL / KME31GP |
| CancelJobRequest | 取消待打印任务 | KM118 / KME31 / KME41 / KME20 系列 |
| GetCainiaoCodeRequest | KM360C 获取云打印机 code（有效期 5 分钟） | KM360C |
| CainiaoBindRequest | KM360C 绑定云打印机 | KM360C |
| CainiaoPrintRequest | KM360C 图片打印 | KM360C |

## 响应对象

::: info KuaimaiResponse
所有接口返回统一封装。业务成功以 `isSuccess()` 判断，业务失败时 `getCode()` 与 `getMessage()` 有值。
:::

```java
KuaimaiResponse resp = client.getAcsResponse(request);

resp.isSuccess();       // boolean，等价于 status 字段
resp.getCode();         // Integer，业务错误码；成功时为 null
resp.getMessage();      // String，失败描述
resp.getData();         // Map<String,Object>，如 { "jobIds": ["..."] }
resp.getExceptionMessage();  // 异常堆栈文本（网络/序列化异常时）
```

## 1. 绑定 / 解绑

::: tip 说明
绑定是打印机和云平台之间的绑定，与业务侧无关。绑定成功后只有该 appId 可以调用该打印机；未绑定时任何 appId 都可以调用。
:::

```java
BindDeviceRequest req = new BindDeviceRequest();
req.setSn("KM118DW123");
req.setDeviceKey("606F8C");   // 机身贴有密钥时必传
req.setBindName("用户ID_10086"); // 可选
req.setNoteName("杭州分店");    // 可选

KuaimaiResponse resp = client.getAcsResponse(req);
if (!resp.isSuccess()) {
    System.err.println("绑定失败 code=" + resp.getCode() + " msg=" + resp.getMessage());
}
```

::: warning 错误码提示
常见错误：4001（序列号不存在或未激活）· 4009（设备密钥不正确）
:::

<div class="ct-label">解绑 UnbindDeviceRequest</div>

```java
UnbindDeviceRequest req = new UnbindDeviceRequest();
req.setSn("KM118DW123");
req.setDeviceKey("606F8C");

client.getAcsResponse(req);
```

## 2. 查询设备状态

一次请求最多查询 100 个设备。`sns` 是 JSON 数组字符串。

```java
import com.alibaba.fastjson.JSONArray;

JSONArray array = new JSONArray();
array.add("KM118DW123");
array.add("KM118DW456");

QueryDeviceStatusRequest req = new QueryDeviceStatusRequest();
req.setSns(array.toString());

KuaimaiResponse resp = client.getAcsResponse(req);
// resp.getData() → List of { sn, status, deviceStatus, cloudServiceExpire }
// status: ONLINE 在线 / OFFLINE 离线 / UNACTIVE 未激活 / DISABLE 已禁用
```

## 3. 标签模板打印（间隙纸）

::: tip Java 特有参数
`image=true`：本地渲染为图片后下发（服务器需装模板对应字体）。`dpi=300`：配合高清云盒（如 ET411）。`imei`：KM360C 专用，与 sn 二选一。这三个参数 HTTP 协议不支持。
:::

```java
TsplTemplatePrintRequest req = new TsplTemplatePrintRequest();
req.setSn("KM118DW123");
req.setTemplateId(1001L);
// renderDataArray 是 JSONArray 字符串，一次最多 50 张
req.setRenderDataArray("[{\\"table_test\\":[{\\"key_test\\":\\"3449394\\"}]}]");
req.setPrintTimes(1);

KuaimaiResponse resp = client.getAcsResponse(req);
if (resp.isSuccess()) {
    List<String> jobIds = (List<String>) resp.getData().get("jobIds");
    // 后续可用 ResultRequest 或异步回调匹配 jobIds
}
```

::: warning 错误码提示
常见错误：4001 · 4005 · 6009（renderData 结构错） · 6022（> 50 单） · 6015（模版无权限） · 6010（模版类型不匹配）
:::

单设备打印接口 **QPS 上限 6**。`bindTable` 与 `bindKey` 对应字段管理中的表名和字段名，包含图片时传 base64（≤ 50KB 最佳）。

## 4. 小票模板打印

```java
EscTemplatePrintRequest req = new EscTemplatePrintRequest();
req.setSn("KM118DW123");
req.setTemplateId(1001L);
req.setRenderData("{\\"orderInfo\\":[{\\"shopName\\":\\"快麦便利店\\",\\"orderNo\\":\\"A001\\"}]}");
req.setVolume(80);       // 有语音机器（如 KM118MGL）
req.setCut(true);        // 有切刀机器
req.setEndFeed(9);       // 末尾走行
req.setOpenDrawer(1);    // 1 = 开钱箱

client.getAcsResponse(req);
```

单设备 QPS 上限 3。

<div class="ct-label">TsplTemplateWriteRequest（间隙纸）</div>

```java
TsplTemplateWriteRequest req = new TsplTemplateWriteRequest();
req.setSn("KM118DW");
req.setTemplateId(1001L);
req.setRenderData("{\\"orderInfo\\":[{\\"shopName\\":\\"店铺\\",\\"orderNo\\":\\"A001\\"}]}");
req.setPrintTimes(2);

client.getAcsResponse(req);
```

## 5. 自定义 XML 打印

```java
TsplXmlWriteRequest req = new TsplXmlWriteRequest();
req.setSn("KM118DW");
req.setXmlStr("<page><render><t>hello,world</t></render></page>");
req.setPrintTimes(1);

// 批量场景：jobs 数组（最多 20 单；KMSX320 最多 5 单）
// req.setJobs(jsonArrayOfXmlStrings);

client.getAcsResponse(req);
```

XML 元素参考[自定义 XML 打印（间隙纸）](#tspl-xml-write)接口文档；支持 `page/render/t/bc/qrc/img/box/circle/ellipse/bar`。

<div class="ct-label">EscXmlWriteRequest（连续纸）</div>

```java
EscXmlWriteRequest req = new EscXmlWriteRequest();
req.setSn("KM118DW123");
req.setInstructions(
    "<page><render><t size='10' feed='2'>快麦云打印</t>" +
    "<bc model='73' height='60'>1234567890</bc></render></page>");
req.setVolume(80);
req.setCut(1);   // 0 不切 / 1 切

client.getAcsResponse(req);
```

支持 `page/render/t/bc/qrc/img/pm`，`pm` 可开启页模式定坐标。

## 6. 图片打印

```java
TsplImageRequest req = new TsplImageRequest();
req.setSn("KM118DW");
req.setImageBase64("data:image/png;base64,iVBORw0KGgo...");
req.setSetWidth(40);   // 可选 mm
req.setSetHeight(30);  // 可选 mm
req.setPrintTimes(1);
req.setDpi(300);       // 高清机型

client.getAcsResponse(req);
```

::: warning 错误码提示
6019：打印内容过大，需控制在 100KB 以内。
:::

<div class="ct-label">EscImageRequest（连续纸）</div>

```java
EscImageRequest req = new EscImageRequest();
req.setSn("KM118DW");
// 可传 imageBase64 或 bufferedImage，都传时 bufferedImage 优先
req.setImageBase64("data:image/png;base64,iVBORw0KGgo...");
req.setEndFeed(3);
req.setPrintWidth(58);

client.getAcsResponse(req);
```

## 7. PDF 打印（依赖 PDFBox）

::: tip 依赖
Java SDK 使用 PDFBox 把 PDF 转成图片后下发，需要在项目里加入 PDFBox 依赖（`org.apache.pdfbox:pdfbox`）。
:::

```java
TsplPdfPrintRequest req = new TsplPdfPrintRequest();
req.setSn("KM118");
req.setFile(new File("/path/to/label.pdf"));
req.setWidth(75);
req.setHeight(100);
req.setDpi(203);   // 300 = 高清机

client.getAcsResponse(req);
```

<div class="ct-label">EscPdfPrintRequest（连续纸）</div>

```java
EscPdfPrintRequest req = new EscPdfPrintRequest();
req.setSn("KM118");
req.setFile(new File("/path/to/receipt.pdf"));
req.setPrintWidth(58);
req.setEndFeed(3);

client.getAcsResponse(req);
```

## 8. 打印结果查询

::: warning 频率限制
同一 SN 每秒最多 2 次；一次最多查询 50 个 jobId。**建议使用异步回调代替轮询**。
:::

```java
import com.alibaba.fastjson.JSONArray;

JSONArray jobIds = new JSONArray();
jobIds.add("1718335259087");

ResultRequest req = new ResultRequest();
req.setSn("KM118DW123");
req.setJobIds(jobIds);

KuaimaiResponse resp = client.getAcsResponse(req);
// resp.getData() → List of { jobId, desc, code }
// code: 2000 成功 / 2006 等待中 / 2007 失败 / 2004 异常 / 3000 已下发
```

## 9. 语音 / 取消任务

```java
BroadcastRequest req = new BroadcastRequest();
req.setSn("KM118MGL123");
req.setVolume(80);              // 0~99
req.setVolumeContent("你好来单了"); // 最长 50 字

client.getAcsResponse(req);
```

<div class="ct-label">取消 CancelJobRequest</div>

```java
CancelJobRequest req = new CancelJobRequest();
req.setSn("KM118MGL123");
client.getAcsResponse(req);
```

<div class="ct-label">KM360C 三段式</div>
KM360C 使用 IMEI 而非 SN。绑定流程：**获取 code → 机器打印 code → 用 imei + code 绑定 → 后续用 imei 打印**。`code` 有效期 5 分钟。

```java
// 16. 获取 code — 机器会打印一张带 code 的纸
GetCainiaoCodeRequest codeReq = new GetCainiaoCodeRequest();
codeReq.setImei("865988000000001");
client.getAcsResponse(codeReq);

// 17. 用 imei + 打印出的 code 完成绑定
CainiaoBindRequest bindReq = new CainiaoBindRequest();
bindReq.setImei("865988000000001");
bindReq.setCode("7764");
client.getAcsResponse(bindReq);

// 18. 图片打印
CainiaoPrintRequest printReq = new CainiaoPrintRequest();
printReq.setImei("865988000000001");
printReq.setImageBase64Data("data:image/png;base64,...");
client.getAcsResponse(printReq);
```

## 异步回调（推荐）

::: tip 回调优于轮询
登录快麦管理后台配置回调 URL，任务终态时平台会主动 POST `{ jobId, sn, code, desc }`。接入方必须返回 `HTTP 200` + `{"data":"OK"}`。详见[异步回调推送](#callback)页面。
:::

## 错误码速查

::: warning 错误码提示
完整错误码见[错误码总表](#error-codes)。SDK 层最常见：4001 / 4005 / 4009 / 4015 / 6009 / 6010 / 6015 / 6019 / 6021 / 6022 / 6024 / 6026 / 6035 / 6036。
:::
