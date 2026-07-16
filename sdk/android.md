# Android SDK

::: tip SDK 下载
[下载 Android SDK](https://kuaimai-public.oss-cn-shanghai.aliyuncs.com/public/document/CloudPrint_sdk_Android.zip)
:::

::: info 获取 APPID
登录 [http://admin.iot.kuaimai.com/](http://admin.iot.kuaimai.com/) 申请，拿到固定的 `APPID`（对接方唯一标识）。
:::

## 环境要求 / 引入

- SDK 包：`cloudprintsdk_1.0.1`（AAR / JAR，随 demo 交付）

  - 需要网络权限：`<uses-permission android:name="android.permission.INTERNET" />`

  - 调用需要在**子线程**执行（内部走 OkHttp 同步网络请求），回调也会回到子线程

```gradle
// Module build.gradle
dependencies {
    // 把 SDK 交付的 aar / jar 放到 libs/ 后引入
    implementation fileTree(dir: 'libs', include: ['*.aar', '*.jar'])
}
```

## 调用约定

SDK 通过静态方法调用 `KM_SDK.methodName(params, listener)`。`params` 是 `Map<String,Object>`，`listener` 是 `KMSDKListener`，成功回调 `onResponse(String jsonString)`，失败回调 `onFailure(String reason)`。

```listener
KM_SDK.KMSDKListener listener = new KM_SDK.KMSDKListener() {
    @Override public void onResponse(String response) {
        // response 是 JSON 字符串：
        // {"status":true, "data":{...}, "message":null, "code":null}
    }
    @Override public void onFailure(String e) {
        // 网络 / 参数异常
    }
};
```

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

## 1. 绑定打印机 bindDevice

```java
Map<String,Object> params = new HashMap<>();
params.put("appId", appId);
params.put("secret", secret);
params.put("sn", "km789838899");
params.put("deviceKey", "");     // 机身贴有密钥时必传
params.put("bindName", "user_10086");
params.put("noteName", "杭州分店");

KM_SDK.bindDevice(params, new KM_SDK.KMSDKListener() {
    @Override public void onResponse(String response) {
        Log.d("KM", response);
    }
    @Override public void onFailure(String e) {
        Log.e("KM", e);
    }
});
```

## 2. 标签模板打印（间隙纸） tsplTemplatePrint

```java
String renderDataArray =
    "[{\\"bindTable1\\":[{\\"bindkey1\\":\\"value1\\"}]}]";

Map<String,Object> params = new HashMap<>();
params.put("appId", appId);
params.put("secret", secret);
params.put("sn", "km789838899");
params.put("templateId", 1);
params.put("renderDataArray", renderDataArray);
params.put("printTimes", "1");

KM_SDK.tsplTemplatePrint(params, listener);
```

## 3. 小票模板打印

```间隙纸tspltemplatewrite
Map<String,Object> params = new HashMap<>();
params.put("appId", appId);
params.put("secret", secret);
params.put("sn", "km789838899");
params.put("templateId", 1);
params.put("renderData", "{\\"bindTable1\\":[{\\"bindkey1\\":\\"value1\\"}]}");

KM_SDK.tsplTemplateWrite(params, listener);
```

```连续纸esctemplateprint
Map<String,Object> params = new HashMap<>();
params.put("appId", appId);
params.put("secret", secret);
params.put("sn", "km789838899");
params.put("templateId", 1);
params.put("renderData", "{\\"bindTable1\\":[{\\"bindkey1\\":\\"value1\\"}]}");
params.put("volume", 80);        // 有语音机器
params.put("volumeIndex", 2051);
params.put("cut", true);         // 切刀机型
params.put("endFeed", 9);

KM_SDK.escTemplatePrint(params, listener);
```

## 4. 自定义 XML 打印

```tsplxmlwrite
Map<String,Object> params = new HashMap<>();
params.put("appId", appId);
params.put("secret", secret);
params.put("sn", "km789838899");
params.put("xmlStr", "<page><render><t>hello,word</t></render></page>");
params.put("image", false);
params.put("printTimes", 1);

KM_SDK.tsplXmlWrite(params, listener);
```

```escxmlwrite
Map<String,Object> params = new HashMap<>();
params.put("appId", appId);
params.put("secret", secret);
params.put("sn", "km789838899");
params.put("instructions", "<page><render><t size='10'>hello</t></render></page>");
params.put("volume", 80);
params.put("cut", 1);            // 0 不切 / 1 切

KM_SDK.escXmlWrite(params, listener);
```

## 5. 原生指令打印

```tspldevicewrite
Map<String,Object> params = new HashMap<>();
params.put("appId", appId);
params.put("secret", secret);
params.put("sn", "km789838899");
params.put("instructs", "CLS\\r\\nSIZE 75mm,100mm\\r\\nTEXT 50,50,\\"0\\",0,1,1,\\"Hello\\"\\r\\nPRINT 1\\r\\n");

KM_SDK.deviceWrite(params, listener);
```

```escescwrite
Map<String,Object> params = new HashMap<>();
params.put("appId", appId);
params.put("secret", secret);
params.put("sn", "km789838899");
// 已 base64 编码的 ESC 指令
params.put("instructionsList", "H3MCAAAAAAD/AAAdIQAbYQ...");
params.put("copies", 1);         // 最大 30

KM_SDK.escWrite(params, listener);
```

## 6. 图片打印 printImageWithBase64

适用机型：KM118DW / KM118MW。`extra` 传 `"0"` 走 TSPL 标签管线，`"2"` 走 ESC 小票管线。

```java
Map<String,Object> params = new HashMap<>();
params.put("appId", appId);
params.put("secret", secret);
params.put("sn", "km789838899");
params.put("imageBase64", "iVBORw0KGgoAAAANSUhEUgAA...");
params.put("kmPrintTimes", 1);
params.put("extra", "0");
// 缩放：不传 = 打印原图；传 1 则按下方尺寸缩放
params.put("printPreview", 1);
params.put("previewWidth", 800);
params.put("previewHeight", 600);

KM_SDK.printImageWithBase64(params, listener);
```

## 7. 打印任务结果查询 printResult

同一 SN 每秒最多 2 次；单次最多 50 个 jobId。建议配合后台配置的打印回调地址。

```java
Map<String,Object> params = new HashMap<>();
params.put("appId", appId);
params.put("secret", secret);
params.put("sn", "km789838899");
params.put("jobIdStr", "[\\"111\\",\\"222\\"]");

KM_SDK.printResult(params, listener);
// data: [{ jobId, desc, code }]，code: 2000 成功 / 2006 等待 / 2007 失败 / 2004 异常
```

## 8. 批量状态查询 batchStatus

```java
Map<String,Object> params = new HashMap<>();
params.put("appId", appId);
params.put("secret", secret);
params.put("snsStr", "[\\"222\\",\\"444\\"]");  // ≤ 100

KM_SDK.batchStatus(params, listener);
// data: [{ sn, status }]，status: ONLINE / OFFLINE / UNACTIVE / DISABLE
```

## 线程与生命周期建议

::: warning 不要在主线程调用
SDK 内部发起网络请求，直接在 UI 线程调用会抛 `NetworkOnMainThreadException`。建议用 `Executors.newSingleThreadExecutor()` 或 Kotlin 协程发起。
:::

```kotlin协程
viewLifecycleOwner.lifecycleScope.launch(Dispatchers.IO) {
    val params = hashMapOf<String, Any>(
        "appId" to appId,
        "secret" to secret,
        "sn" to "km789838899",
        "templateId" to 1,
        "renderData" to "{\\"bindTable1\\":[{\\"bindkey1\\":\\"value1\\"}]}"
    )
    KM_SDK.tsplTemplateWrite(params, object : KM_SDK.KMSDKListener {
        override fun onResponse(response: String) { /* 处理 */ }
        override fun onFailure(e: String) { /* 处理 */ }
    })
}
```

## 混淆规则

```proguard-rules.pro
# 保留 SDK 主类和 Listener
-keep class com.kuaimai.** { *; }
-keepclassmembers class * implements com.kuaimai.KM_SDK$KMSDKListener {
    <methods>;
}
```

具体包名以 SDK 交付版本为准，需要请咨询快麦对接。

## 错误码

::: warning 错误码提示
回调 `response` 中的 `code` 与 HTTP 直连一致：4001 / 4005 / 4009 / 4015 / 6006 / 6009 / 6010 / 6015 / 6019 / 6021 / 6022 / 6024 / 6026。完整表见[错误码总表](#error-codes)。
:::
