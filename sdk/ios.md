# iOS SDK

::: tip SDK 下载
[下载 iOS SDK](https://kuaimai-public.oss-cn-shanghai.aliyuncs.com/public/document/CloudPrint_sdk_iOS.zip)
:::

::: info 获取 APPID
登录 [http://admin.iot.kuaimai.com/](http://admin.iot.kuaimai.com/) 申请，拿到固定的 `APPID`。
:::

## 环境要求 / 引入

- SDK 名称：**CloudPrint_sdk**（framework 或 static library，随 demo 交付）

  - iOS 9.0+ 建议

  - ATS 例外：SDK 请求域名如为 HTTP 需在 `Info.plist` 里配置 `NSAppTransportSecurity`

```导入sdk
// Objective-C
#import "KM_SDK.h"

// Swift（bridging header 里加入 #import "KM_SDK.h"）
import CloudPrint_sdk   // 若以 framework 形式引入
```

## 调用约定

`KM_SDK` 提供类方法：`[KM_SDK methodName:params success:^(id response) {} fail:^(NSError *e) {}]`。`params` 是 `NSMutableDictionary`；`response` 是接口返回的 JSON 字典 / 数组。

## API 一览

| 方法 | 描述 | 适用机型 |
|---|---|---|
| bindDevice | 绑定打印机 | 所有机型 |
| tsplTemplatePrint | 标签模板打印（间隙纸） | KM118DW / KM118MW / KMSX320 / KME20W |
| tsplTemplateWrite | 小票模板打印（间隙纸） | KM118DW / KM118MW / KMSX320 / KME20W |
| escTemplatePrint | 小票模板打印（连续纸） | KM118DW / KM118MW / KMSX320 / KME20W / KMDP158 / KMDP358 / KMDP458 |
| tsplXmlWrite / escXmlWrite | 自定义 XML 打印 | 见上 |
| deviceWrite / escWrite | 原生 TSPL / ESC 指令打印 | 见上 |
| printImageWithBase64 | Base64 图片打印 | KM118DW / KM118MW |
| printResult | 打印任务结果查询 | 所有机型 |
| batchStatus | 批量设备状态查询 | 所有机型 |

## 1. 绑定打印机

```objc
NSMutableDictionary *params = [NSMutableDictionary dictionary];
[params setValue:appId forKey:@"appId"];
[params setValue:secret forKey:@"secret"];
[params setValue:@"km789838899" forKey:@"sn"];
[params setValue:@"" forKey:@"deviceKey"];
[params setValue:@"user_10086" forKey:@"bindName"];
[params setValue:@"杭州分店" forKey:@"noteName"];

[KM_SDK bindDevice:params
           success:^(id  _Nonnull response) {
    NSLog(@"%@", response);
} fail:^(NSError * _Nonnull error) {
    NSLog(@"%@", error);
}];
```

```swift
let params = NSMutableDictionary()
params["appId"]     = appId
params["secret"]    = secret
params["sn"]        = "km789838899"
params["deviceKey"] = ""
params["bindName"]  = "user_10086"
params["noteName"]  = "杭州分店"

KM_SDK.bindDevice(params, success: { response in
    print(response as Any)
}, fail: { error in
    print(error)
})
```

## 2. 标签模板打印（间隙纸）

```objc
NSString *renderDataArray =
    @"[{\\"bindTable1\\":[{\\"bindkey1\\":\\"value1\\"}]}]";

NSMutableDictionary *params = [NSMutableDictionary dictionary];
[params setValue:appId forKey:@"appId"];
[params setValue:secret forKey:@"secret"];
[params setValue:@"km789838899" forKey:@"sn"];
[params setValue:@1 forKey:@"templateId"];
[params setValue:renderDataArray forKey:@"renderDataArray"];
[params setValue:@1 forKey:@"printTimes"];

[KM_SDK tsplTemplatePrint:params success:^(id resp) {
    NSLog(@"%@", resp);
} fail:^(NSError *e) {}];
```

## 3. 小票模板打印

```间隙纸tspltemplatewrite
NSMutableDictionary *params = [NSMutableDictionary dictionary];
[params setValue:appId forKey:@"appId"];
[params setValue:secret forKey:@"secret"];
[params setValue:@"km789838899" forKey:@"sn"];
[params setValue:@1 forKey:@"templateId"];
[params setValue:@"{\\"bindTable1\\":[{\\"bindkey1\\":\\"value1\\"}]}"
            forKey:@"renderData"];

[KM_SDK tsplTemplateWrite:params success:^(id resp) {} fail:^(NSError *e) {}];
```

```连续纸esctemplateprint
NSMutableDictionary *params = [NSMutableDictionary dictionary];
[params setValue:appId forKey:@"appId"];
[params setValue:secret forKey:@"secret"];
[params setValue:@"km789838899" forKey:@"sn"];
[params setValue:@1 forKey:@"templateId"];
[params setValue:@"{\\"bindTable1\\":[{\\"bindkey1\\":\\"value1\\"}]}" forKey:@"renderData"];
[params setValue:@80 forKey:@"volume"];        // 语音机型
[params setValue:@2051 forKey:@"volumeIndex"];
[params setValue:@YES forKey:@"cut"];          // 切刀机型
[params setValue:@9 forKey:@"endFeed"];

[KM_SDK escTemplatePrint:params success:^(id resp) {} fail:^(NSError *e) {}];
```

## 4. 自定义 XML 打印

```tsplxmlwrite
NSMutableDictionary *params = [NSMutableDictionary dictionary];
[params setValue:appId forKey:@"appId"];
[params setValue:secret forKey:@"secret"];
[params setValue:@"km789838899" forKey:@"sn"];
[params setValue:@"<page><render><t>hello,word</t></render></page>" forKey:@"xmlStr"];
[params setValue:@NO forKey:@"image"];
[params setValue:@1 forKey:@"printTimes"];

[KM_SDK tsplXmlWrite:params success:^(id resp) {} fail:^(NSError *e) {}];
```

```escxmlwrite
NSMutableDictionary *params = [NSMutableDictionary dictionary];
[params setValue:appId forKey:@"appId"];
[params setValue:secret forKey:@"secret"];
[params setValue:@"km789838899" forKey:@"sn"];
[params setValue:@"<page><render><t size='10'>hello</t></render></page>"
            forKey:@"instructions"];
[params setValue:@80 forKey:@"volume"];
[params setValue:@1 forKey:@"cut"];

[KM_SDK escXmlWrite:params success:^(id resp) {} fail:^(NSError *e) {}];
```

## 5. 原生指令打印

```tspldevicewrite
NSMutableDictionary *params = [NSMutableDictionary dictionary];
[params setValue:appId forKey:@"appId"];
[params setValue:secret forKey:@"secret"];
[params setValue:@"km789838899" forKey:@"sn"];
[params setValue:@"CLS\\r\\nSIZE 75mm,100mm\\r\\nTEXT 50,50,\\"0\\",0,1,1,\\"Hello\\"\\r\\nPRINT 1\\r\\n"
            forKey:@"instructs"];

[KM_SDK deviceWrite:params success:^(id resp) {} fail:^(NSError *e) {}];
```

```escescwrite
NSMutableDictionary *params = [NSMutableDictionary dictionary];
[params setValue:appId forKey:@"appId"];
[params setValue:secret forKey:@"secret"];
[params setValue:@"km789838899" forKey:@"sn"];
// 已 base64 编码的 ESC 指令
[params setValue:@"H3MCAAAAAAD/AAAdIQAbYQ..." forKey:@"instructionsList"];
[params setValue:@1 forKey:@"copies"];   // 最大 30

[KM_SDK escWrite:params success:^(id resp) {} fail:^(NSError *e) {}];
```

## 6. 图片打印 printImageWithBase64

适用机型：KM118DW / KM118MW。`extra` 传 `@"0"` 走 TSPL 标签管线，`@"2"` 走 ESC 小票管线。

```objc
NSMutableDictionary *params = [NSMutableDictionary dictionary];
[params setValue:appId forKey:@"appId"];
[params setValue:secret forKey:@"secret"];
[params setValue:@"km789838899" forKey:@"sn"];
[params setValue:@"iVBORw0KGgoAAAANSUhEUgAA..." forKey:@"imageBase64"];
[params setValue:@1 forKey:@"kmPrintTimes"];
[params setValue:@"0" forKey:@"extra"];
// 缩放：不传 = 打印原图；传 1 则按下方尺寸缩放
[params setValue:@1 forKey:@"printPreview"];
[params setValue:@800 forKey:@"previewWidth"];
[params setValue:@600 forKey:@"previewHeight"];

[KM_SDK printImageWithBase64:params success:^(id resp) {} fail:^(NSError *e) {}];
```

## 7. 打印任务结果查询

同一 SN 每秒最多 2 次；单次最多 50 个 jobId。

```objc
NSMutableDictionary *params = [NSMutableDictionary dictionary];
[params setValue:appId forKey:@"appId"];
[params setValue:secret forKey:@"secret"];
[params setValue:@"km789838899" forKey:@"sn"];
[params setValue:@"[\\"111\\",\\"222\\"]" forKey:@"jobIdStr"];

[KM_SDK printResult:params success:^(id resp) {
    // resp[@"data"] → 数组 [{ jobId, desc, code }]
    // code: 2000 成功 / 2006 等待 / 2007 失败 / 2004 异常
} fail:^(NSError *e) {}];
```

## 8. 批量状态查询

```objc
NSMutableDictionary *params = [NSMutableDictionary dictionary];
[params setValue:appId forKey:@"appId"];
[params setValue:secret forKey:@"secret"];
[params setValue:@"[\\"222\\",\\"444\\"]" forKey:@"snsStr"];  // ≤ 100

[KM_SDK batchStatus:params success:^(id resp) {
    // resp[@"data"] → [{ sn, status }]
    // status: ONLINE / OFFLINE / UNACTIVE / DISABLE
} fail:^(NSError *e) {}];
```

## Swift 通用样例

```swift
let params = NSMutableDictionary()
params["appId"]  = appId
params["secret"] = secret
params["sn"]     = "km789838899"
params["templateId"] = 1
params["renderData"] = "{\\"bindTable1\\":[{\\"bindkey1\\":\\"value1\\"}]}"

KM_SDK.tsplTemplateWrite(params, success: { response in
    // 处理 response
}, fail: { error in
    // 处理 error
})
```

## 错误码

::: warning 错误码提示
response 中的 `code` 与 HTTP 直连一致：4001 / 4005 / 4009 / 4015 / 6006 / 6009 / 6010 / 6015 / 6019 / 6021 / 6022 / 6024 / 6026。完整表见[错误码总表](#error-codes)。
:::
