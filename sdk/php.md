# PHP SDK

::: tip SDK 下载
[GitHub 仓库](https://github.com/xuli2016/php-kuaimai)
:::

::: info Demo 仓库
PHP 完整示例见 `php-cloud-demo`，SDK 源码位于 `php-kuaimai-core`。
:::

## 环境要求

- **PHP ≥ 8.0**；启用扩展 `curl`、`gd`、`zlib`、`json`、`mbstring`。

  - 模板打印使用 `image=true` 时会在本地渲染，还需要 `chillerlan/php-qrcode`、`picqer/php-barcode-generator`；渲染中文时 GD 需要 FreeType 支持且系统装有中文字体。

  - PDF 打印功能需要安装 **Ghostscript**（`apt-get install -y ghostscript` 或 `yum install -y ghostscript`）。

## 安装：Composer path 仓库

::: warning 不要执行 `composer require kuaimai/php-kuaimai-core`
SDK **未发布到 Packagist**，直接 require 会报 `Package not found`。请把随 demo 提供的 `php-kuaimai-core/` 目录与你的项目放在同层级，然后用 `path` 仓库引入。
:::

```目录结构
your-workspace/
├── php-kuaimai-core/        # SDK 源码目录（不要改动）
└── your-project/            # 你自己的项目
    └── composer.json
```

```composer.json
{
    "require": {
        "php": ">=8.0",
        "kuaimai/php-kuaimai-core": "*"
    },
    "repositories": [
        { "type": "path", "url": "../php-kuaimai-core" }
    ],
    "minimum-stability": "dev",
    "prefer-stable": true
}
```

```安装
composer install
```

## 初始化客户端

```php
<?php
use Kuaimai\\KuaimaiClient;

$client = KuaimaiClient::createClient($appId, $appSecret);
```

## 命名空间约定

Request 类按模块分布在四个命名空间下：

| 命名空间 | 包含的接口 |
|---|---|
| Kuaimai\\Request\\Device\\* | 绑定、解绑、查询状态 |
| Kuaimai\\Request\\Tspl\\* | 标签模板 / TSPL XML / TSPL 图片 / TSPL PDF |
| Kuaimai\\Request\\Esc\\* | 小票模板 / ESC XML / ESC 图片 / ESC PDF |
| Kuaimai\\Request\\Misc\\* | 结果查询、语音、取消任务、菜鸟三件套 |

SDK 使用**公共属性直接赋值**，无 setter 方法。所有 request 属性用 `$req->xxx = value` 的方式设置。

## 1. 绑定设备

```php
<?php
use Kuaimai\\Request\\Device\\BindDeviceRequest;

$bindReq = new BindDeviceRequest();
$bindReq->sn = 'KM118DW123';
$bindReq->deviceKey = '606F8C';   // 机身贴有密钥时必传
$bindReq->bindName = 'user_10086'; // 可选
$bindReq->noteName = '杭州分店';    // 可选

$resp = $client->getAcsResponse($bindReq);
```

## 2. 解绑 / 3. 查询状态

```php
<?php
use Kuaimai\\Request\\Device\\UnbindDeviceRequest;
use Kuaimai\\Request\\Device\\QueryDeviceStatusRequest;

// 解绑
$u = new UnbindDeviceRequest();
$u->sn = 'KM118DW123';
$u->deviceKey = '123456';
$client->getAcsResponse($u);

// 批量状态（≤ 100 台，sns 是 JSON 数组字符串）
$q = new QueryDeviceStatusRequest();
$q->sns = json_encode(['KM118DW123', 'KM118DW456']);
$resp = $client->getAcsResponse($q);
// $resp->data → 数组，每项 { sn, status, deviceStatus, cloudServiceExpire }
```

## 4. 标签模板打印（间隙纸）

::: tip image=true
PHP SDK 支持在本地渲染模板为图片后下发。开启前请确认 GD + FreeType 与中文字体已就绪；下方给出排查步骤。
:::

```php
<?php
use Kuaimai\\Request\\Tspl\\TsplTemplatePrintRequest;

$req = new TsplTemplatePrintRequest();
$req->sn = 'KM118DW123';
$req->templateId = 1001;
$req->renderDataArray = '[{"table_test":[{"key_test":"3449394"}]}]';
$req->printTimes = 1;
$req->image = true;

$resp = $client->getAcsResponse($req);
```

## 4.1 本地渲染排查（image=true）

```环境检查
# 依赖
composer show chillerlan/php-qrcode
composer show picqer/php-barcode-generator
php -m | grep -E "curl|gd|zlib|json|mbstring"

# GD 与 FreeType
php -r 'echo "gd=" . (extension_loaded("gd") ? "yes" : "no") . PHP_EOL;
        echo "imagettftext=" . (function_exists("imagettftext") ? "yes" : "no") . PHP_EOL;'
```

```中文字体
fc-list :lang=zh | head -20

# Ubuntu/Debian
apt-get install -y fonts-wqy-microhei fonts-wqy-zenhei fonts-noto-cjk
# CentOS/RHEL
yum install -y wqy-microhei-fonts wqy-zenhei-fonts

fc-cache -fv   # 刷新字体缓存后重启 PHP-FPM / Web 服务 / 容器
```

```自定义字体目录/调试
# SDK 扫描额外字体目录
export KUAIMAI_FONT_DIRS=/usr/share/fonts:/usr/local/share/fonts:/path/to/fonts

# 落地 SDK 本地渲染的 PNG（在 PHP 里）
<?php putenv('KUAIMAI_RENDER_DEBUG_DIR=/tmp/kuaimai-render');
// mkdir -p /tmp/kuaimai-render && chmod 777 /tmp/kuaimai-render
```

若暂时无法修复本地环境，可关闭本地渲染 `$req->image = false` 走服务端渲染。

## 5-6. 小票模板

```esctemplateprint(连续纸)
<?php
use Kuaimai\\Request\\Esc\\EscTemplatePrintRequest;

$req = new EscTemplatePrintRequest();
$req->sn = 'KM118DW123';
$req->templateId = 1001;
$req->renderData = '{"table_test":[{"key_test":"3449394"}]}';
$req->volume = 80;
$req->cut = true;
$req->endFeed = 9;

$client->getAcsResponse($req);
```

```tspltemplatewrite(间隙纸)
<?php
use Kuaimai\\Request\\Tspl\\TsplTemplateWriteRequest;

$req = new TsplTemplateWriteRequest();
$req->sn = 'KM118DW123';
$req->templateId = 1001;
$req->renderData = '{"table_test":[{"key_test":"3449394"}]}';
$req->printTimes = 2;

$client->getAcsResponse($req);
```

## 7-8. 自定义 XML 打印

```tsplxml(间隙纸)
<?php
use Kuaimai\\Request\\Tspl\\TsplXmlWriteRequest;

$req = new TsplXmlWriteRequest();
$req->sn = 'KM118DW123';
$req->xmlStr = "<page><render><t>hello,world</t></render></page>";
$req->printTimes = 2;
// 批量：$req->jobs = [$xml1, $xml2, ...]; 最多 20（KMSX320 最多 5）

$client->getAcsResponse($req);
```

```escxml(连续纸)
<?php
use Kuaimai\\Request\\Esc\\EscXmlWriteRequest;

$req = new EscXmlWriteRequest();
$req->sn = 'KM118DW123';
$req->instructions = "<page><render><t size='01' feed='9'>hello,world</t></render></page>";

$client->getAcsResponse($req);
```

## 9-10. 图片打印

```间隙纸
<?php
use Kuaimai\\Request\\Tspl\\TsplImageRequest;

$req = new TsplImageRequest();
$req->sn = 'KM118DW123';
$req->imageBase64 = 'data:image/png;base64,iVBORw0KGgo...';
$req->setWidth = 40;
$req->setHeight = 30;
$req->printTimes = 1;
$req->dpi = 300;

$client->getAcsResponse($req);
```

```连续纸
<?php
use Kuaimai\\Request\\Esc\\EscImageRequest;

$req = new EscImageRequest();
$req->sn = 'KM118DW123';
$req->imageBase64 = 'data:image/png;base64,iVBORw0KGgo...';
$req->endFeed = 3;
$req->printWidth = 58;

$client->getAcsResponse($req);
```

## 11-12. PDF 打印

::: tip 依赖
PHP 端使用 **Ghostscript** 进行 PDF → 图片转换。Java 端使用 PDFBox；两端不共用依赖。
:::

```间隙纸
<?php
use Kuaimai\\Request\\Tspl\\TsplPdfPrintRequest;

$req = new TsplPdfPrintRequest();
$req->sn = 'KM118DW123';
$req->filePath = '/path/to/label.pdf';
$req->dpi = 203;
$req->width = 75.0;
$req->height = 100.0;

$resp = $client->getAcsResponse($req);   // 单页
$resp = $client->tsplPdfsPrint($req);    // 多页
```

```连续纸
<?php
use Kuaimai\\Request\\Esc\\EscPdfPrintRequest;

$req = new EscPdfPrintRequest();
$req->sn = 'KM118DW123';
$req->filePath = '/path/to/receipt.pdf';
$req->printWidth = 58.0;
$req->endFeed = 3;

$client->getAcsResponse($req);           // 单页
$client->escPdfsPrint($req);             // 多页
```

## 13-15. 结果 / 语音 / 取消

```php
<?php
use Kuaimai\\Request\\Misc\\ResultRequest;
use Kuaimai\\Request\\Misc\\BroadcastRequest;
use Kuaimai\\Request\\Misc\\CancelJobRequest;

// 结果查询（同一 SN ≤ 2 次/秒；单次 ≤ 50 个）
$r = new ResultRequest();
$r->sn = 'KM118DW123';
$r->jobIds = ['1718335259087'];
$resp = $client->getAcsResponse($r);
// $resp->data → [{ jobId, desc, code }]，code: 2000 成功/2006 等待/2007 失败/2004 异常

// 语音（KM118MGL / KME31GP，最长 50 字）
$b = new BroadcastRequest();
$b->sn = 'KM118MGL123';
$b->volume = 80;
$b->volumeContent = '你好来单了';
$client->getAcsResponse($b);

// 取消队列任务
$c = new CancelJobRequest();
$c->sn = 'KM118MGL123';
$client->getAcsResponse($c);
```

## 16-18. KM360C 三段式接入

```php
<?php
use Kuaimai\\Request\\Misc\\GetCainiaoCodeRequest;
use Kuaimai\\Request\\Misc\\CainiaoBindRequest;
use Kuaimai\\Request\\Misc\\CainiaoPrintRequest;

// 16. 获取 code（有效期 5 分钟；机器会打印一张带 code 的纸）
$c = new GetCainiaoCodeRequest();
$c->imei = '865988000000001';
$client->getAcsResponse($c);

// 17. 绑定
$b = new CainiaoBindRequest();
$b->imei = '865988000000001';
$b->code = '7764';
$client->getAcsResponse($b);

// 18. 图片打印
$p = new CainiaoPrintRequest();
$p->imei = '865988000000001';
$p->imageBase64Data = 'data:image/png;base64,...';
$client->getAcsResponse($p);
```

## 异步回调（推荐）

::: tip 回调优于轮询
登录快麦管理后台配置回调 URL，任务终态时平台会主动 POST `{ jobId, sn, code, desc }`。接入方必须返回 `HTTP 200` + `{"data":"OK"}`。详见[异步回调推送](#callback)页面。
:::

## 全局错误码

::: warning 错误码提示
4001（SN 未激活）· 4005（离线）· 4009（密钥错）· 6009（renderData 结构错）· 6010（模版类型不匹配）· 6015（无权访问模版）· 6019（内容过大 > 100KB）· 6021（查询过频）· 6022（>50 单）· 6024（xml jobs >20）· 6026（jobId >50）· 6035（机型不支持语音）· 6036（语音内容 >50 字）。完整表见[错误码总表](#error-codes)。
:::
