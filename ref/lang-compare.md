# 多语言对照速查

同一操作在各 SDK 里的写法并排对比。参数语义以 HTTP 接口文档为准，这里只展示调用语法差异。

## 初始化

::: code-group

```java [Java]
import com.kuaimai.core.KuaimaiClient;
// 一次初始化，复用整个生命周期
KuaimaiClient client = KuaimaiClient.createClient(appId, appSecret);
```

```python [Python]
from kuaimai_core import KuaimaiClient
client = KuaimaiClient.create_client(appid, secret)
```

```php [PHP]
<?php
use Kuaimai\KuaimaiClient;
$client = KuaimaiClient::createClient($appId, $appSecret);
```

```javascript [JS / Node]
// 引入后每次调用都要传 appId + secret（JS SDK 无全局 client 对象）
const KMSDK = new KM_SDK();
```

```kotlin [Android]
// Android 无初始化步骤；每次调用传 appId + secret
// 调用必须在子线程（SDK 内部发同步网络请求）
```

```swift [iOS]
// iOS 无初始化步骤；每次调用传 appId + secret
// 引入: #import "KM_SDK.h"
```

:::

## 绑定设备

::: code-group

```java [Java]
BindDeviceRequest req = new BindDeviceRequest();
req.setSn("KM118DW123");
req.setDeviceKey("606F8C");   // 可选
req.setBindName("user_10086");
KuaimaiResponse r = client.getAcsResponse(req);
```

```python [Python]
from kuaimai_core.request import BindDeviceRequest
r = client.get_acs_response(BindDeviceRequest(
    sn="KM118DW123", deviceKey="606F8C", bindName="user_10086"))
```

```php [PHP]
use Kuaimai\Request\Device\BindDeviceRequest;
$req = new BindDeviceRequest();
$req->sn = 'KM118DW123';  $req->deviceKey = '606F8C';
$r = $client->getAcsResponse($req);
```

```javascript [JS / Node]
KMSDK.bindDevice({ appId, secret, sn:'KM118DW123', deviceKey:'',
  callback: res => console.log(res) });
```

```kotlin [Android]
Map<String,Object> p = new HashMap<>();
p.put("appId",appId); p.put("secret",secret); p.put("sn","KM118DW123");
KM_SDK.bindDevice(p, new KM_SDK.KMSDKListener() {
  public void onResponse(String r) {}  public void onFailure(String e) {} });
```

```swift [iOS]
NSMutableDictionary *p = [NSMutableDictionary dictionary];
[p setValue:appId forKey:@"appId"]; [p setValue:secret forKey:@"secret"];
[p setValue:@"KM118DW123" forKey:@"sn"];
[KM_SDK bindDevice:p success:^(id r){} fail:^(NSError *e){}];
```

:::

## 标签模板打印

::: code-group

```java [Java]
TsplTemplatePrintRequest req = new TsplTemplatePrintRequest();
req.setSn("KM118DW123");
req.setTemplateId(1001L);
req.setRenderDataArray("[{\"t\":[{\"name\":\"商品A\"}]}]");
req.setPrintTimes(1);
KuaimaiResponse r = client.getAcsResponse(req);
List<String> jobIds = (List) r.getData().get("jobIds");
```

```python [Python]
from kuaimai_core.request import TsplTemplatePrintRequest
r = client.get_acs_response(TsplTemplatePrintRequest(
    sn="KM118DW123", templateId=1001,
    renderDataArray='[{"t":[{"name":"商品A"}]}]', printTimes=1))
```

```php [PHP]
use Kuaimai\Request\Tspl\TsplTemplatePrintRequest;
$req = new TsplTemplatePrintRequest();
$req->sn = 'KM118DW123';  $req->templateId = 1001;
$req->renderDataArray = '[{"t":[{"name":"商品A"}]}]';
$r = $client->getAcsResponse($req);
```

```javascript [JS / Node]
KMSDK.tsplTemplatePrint({ appId, secret, sn:'KM118DW123',
  templateId:1001, renderDataArray:'[{"t":[{"name":"商品A"}]}]',
  printTimes:1,
  callback: res => console.log(res.data?.jobIds) });
```

```kotlin [Android]
Map<String,Object> p = new HashMap<>();
p.put("appId",appId); p.put("secret",secret); p.put("sn","KM118DW123");
p.put("templateId",1001); p.put("renderDataArray","[{...}]");
p.put("printTimes","1");
KM_SDK.tsplTemplatePrint(p, listener);
```

```swift [iOS]
NSMutableDictionary *p = [NSMutableDictionary dictionary];
[p setValue:appId forKey:@"appId"]; [p setValue:secret forKey:@"secret"];
[p setValue:@"KM118DW123" forKey:@"sn"];
[p setValue:@1001 forKey:@"templateId"];
[p setValue:@"[{...}]" forKey:@"renderDataArray"];
[KM_SDK tsplTemplatePrint:p success:^(id r){} fail:^(NSError *e){}];
```

:::

## 小票模板打印（连续纸）

::: code-group

```java [Java]
EscTemplatePrintRequest req = new EscTemplatePrintRequest();
req.setSn("KM118DW123");  req.setTemplateId(1001L);
req.setRenderData("{\"orderInfo\":[{\"shopName\":\"店铺\"}]}");
req.setCut(true);  req.setEndFeed(9);
client.getAcsResponse(req);
```

```python [Python]
from kuaimai_core.request import EscTemplatePrintRequest
client.get_acs_response(EscTemplatePrintRequest(
    sn="KM118DW123", templateId=1001,
    renderData='{"orderInfo":[{"shopName":"店铺"}]}', cut=True, endFeed=9))
```

```php [PHP]
use Kuaimai\Request\Esc\EscTemplatePrintRequest;
$req = new EscTemplatePrintRequest();
$req->sn='KM118DW123'; $req->templateId=1001;
$req->renderData='{"orderInfo":[{"shopName":"店铺"}]}';
$req->cut=true; $req->endFeed=9;
$client->getAcsResponse($req);
```

```javascript [JS / Node]
KMSDK.escTemplatePrint({ appId, secret, sn:'KM118DW123',
  templateId:1001, renderData:'{"orderInfo":[{"shopName":"店铺"}]}',
  cut:true, endFeed:9, callback: res => {} });
```

:::

## 查询打印结果

::: code-group

```java [Java]
import com.alibaba.fastjson.JSONArray;
ResultRequest req = new ResultRequest();
req.setSn("KM118DW123");
JSONArray ids = new JSONArray(); ids.add("1718335259087");
req.setJobIds(ids);
KuaimaiResponse r = client.getAcsResponse(req);
// r.getData() → List[{ jobId, desc, code }]
```

```python [Python]
from kuaimai_core.request import ResultRequest
r = client.get_acs_response(ResultRequest(
    sn="KM118DW123", jobIds=["1718335259087"]))
```

```php [PHP]
use Kuaimai\Request\ResultRequest;
$req = new ResultRequest();
$req->sn = 'KM118DW123';
$req->jobIds = ['1718335259087'];
$r = $client->getAcsResponse($req);
```

```javascript [JS / Node]
KMSDK.printResult({ appId, secret, sn:'KM118DW123',
  jobIds:['1718335259087'],
  callback: res => console.log(res.data) });
```

:::
