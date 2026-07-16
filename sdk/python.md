# Python SDK

::: tip SDK 下载
[GitHub 仓库](https://github.com/xuli2016/python-kuaimai)
:::

::: info Demo 仓库
完整可运行示例见 `python-cloud-demo/CloudExample.py`。每个接口都有一个对应的 `*_example` 函数，改一下顶部 appid/secret 直接运行。
:::

## 环境要求

- 推荐 **Python 3.10 及以上**。

  - 图片打印 / PDF 打印场景推荐提前安装 **Ghostscript** 与模板依赖字体，避免不同机器字体缺失。

  - 本地 PDF 路径推荐用绝对路径（`Path("/abs/path/demo.pdf")` 或字符串均可）。

## 安装

```linux/macos
cd python-cloud-demo
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

```windows
cd python-cloud-demo
python -m venv .venv
.venv\\Scripts\\activate
pip install -r requirements.txt
```

## 初始化客户端

```python
from kuaimai_core import KuaimaiClient

client = KuaimaiClient.create_client(appid, secret)
```

## 通用响应格式

```调用样例
import json
from kuaimai_core import KuaimaiClient
from kuaimai_core.request import QueryDeviceStatusRequest

client = KuaimaiClient.create_client(appid, secret)

request = QueryDeviceStatusRequest(
    sns=json.dumps(["KM118DW123"], ensure_ascii=False),
)
response = client.get_acs_response(request)
print(response.to_dict())
# → {"status": True, "data": [...], "message": None, "code": None}
```

| 字段 | 类型 | 说明 |
|---|---|---|
| status | bool | True = 成功 |
| data | object | 由具体接口决定的结构 |
| message | str | 失败时的原因说明 |
| code | int | 业务错误码 |

## API 一览

所有 Request 类都在 `kuaimai_core.request` 包下。

| Request 类 | 描述 |
|---|---|
| BindDeviceRequest / UnbindDeviceRequest | 绑定 / 解绑 |
| QueryDeviceStatusRequest | 批量查询状态（≤ 100 台） |
| TsplTemplatePrintRequest | 标签模板（间隙纸） |
| EscTemplatePrintRequest | 小票模板（连续纸） |
| TsplTemplateWriteRequest | 小票模板（间隙纸） |
| TsplXmlWriteRequest / EscXmlWriteRequest | 自定义 XML 打印 |
| TsplImageRequest / EscImageRequest | 图片直接打印 |
| TsplPdfPrintRequest / EscPdfPrintRequest | PDF 直接打印（支持单页与多页） |
| ResultRequest | 任务结果查询 |
| BroadcastRequest | 语音播报 |
| CancelJobRequest | 取消打印任务 |
| AdjustDeviceDensityRequest | 调节浓度（1~15，默认 8；机器需处于标签模式） |
| GetCainiaoCodeRequest / CainiaoBindRequest / CainiaoPrintRequest | KM360C 三段式接入 |

## 1-2. 绑定 / 解绑

```python
from kuaimai_core.request import BindDeviceRequest, UnbindDeviceRequest

# 绑定
resp = client.get_acs_response(BindDeviceRequest(
    sn="KM118DW123",
    deviceKey="123456",
    bindName="user_10086",
    noteName="杭州分店",
))

# 解绑
resp = client.get_acs_response(UnbindDeviceRequest(
    sn="KM118DW123",
    deviceKey="123456",
))
```

## 3. 批量设备状态

`sns` 是 JSON 数组字符串，一次最多 100 台。

```python
import json
from kuaimai_core.request import QueryDeviceStatusRequest

resp = client.get_acs_response(QueryDeviceStatusRequest(
    sns=json.dumps(["KM118DW123", "KM118DW456"], ensure_ascii=False),
))
# resp.data → [{ sn, status, deviceStatus, cloudServiceExpire }, ...]
# status: ONLINE / OFFLINE / UNACTIVE / DISABLE
```

## 4. 标签模板打印（间隙纸）

::: tip Python 特有
`renderDataArray` 可直接传 JSON 字符串；也支持 `renderDataJsonArray` 直接传 Python list/dict。`image=True` 会本地渲染，运行环境需装模板对应字体。
:::

```普通sn
from kuaimai_core.request import TsplTemplatePrintRequest

resp = client.get_acs_response(TsplTemplatePrintRequest(
    sn="KM118DW123",
    templateId=1634989639,
    renderDataArray='[{"table_test":[{"key_test":"3449394"}]}]',
    printTimes=1,
    image=True,
))
```

```km360c(用imei)
from kuaimai_core.request import TsplTemplatePrintRequest

resp = client.get_acs_response(TsplTemplatePrintRequest(
    imei="863130068516971",
    templateId=1634989639,
    renderDataArray='[{"table_test":[{"key_test":"3449394"}]}]',
))
```

## 5-6. 小票模板

```esc(连续纸)
from kuaimai_core.request import EscTemplatePrintRequest

resp = client.get_acs_response(EscTemplatePrintRequest(
    sn="KM118DW123",
    templateId=1634960019,
    renderData='{"table_test":[{"key_test":"3449394"}]}',
    cut=True,
    endFeed=9,
))
```

```tspl(间隙纸)
from kuaimai_core.request import TsplTemplateWriteRequest

resp = client.get_acs_response(TsplTemplateWriteRequest(
    sn="KM118DW123",
    templateId=1634960019,
    renderData='{"table_test":[{"key_test":"3449394"}]}',
    printTimes=1,
))
```

## 7-8. 自定义 XML 打印

```tspl(间隙纸)
from kuaimai_core.request import TsplXmlWriteRequest

resp = client.get_acs_response(TsplXmlWriteRequest(
    sn="KM118DW123",
    xmlStr='<page><render width="100" height="150"><t x="20" y="20">生产加工单</t></render></page>',
    printTimes=1,
))
# 批量：传 jobs=[xml1, xml2, ...] 最多 20 单（KMSX320 最多 5 单）
```

```esc(连续纸)
from kuaimai_core.request import EscXmlWriteRequest

resp = client.get_acs_response(EscXmlWriteRequest(
    sn="KM118DW123",
    instructions="<page><render><t size='01' feed='9'>hello,world</t></render></page>",
    volume=80,
    cut=1,
))
```

## 9-10. 图片打印

```间隙纸
from kuaimai_core.request import TsplImageRequest

resp = client.get_acs_response(TsplImageRequest(
    sn="KM118DW123",
    imageBase64="data:image/png;base64,......",
    printTimes=1,
    setWidth=40,
    setHeight=30,
    dpi=203,
))
```

```连续纸
from kuaimai_core.request import EscImageRequest

resp = client.get_acs_response(EscImageRequest(
    sn="KM118DW123",
    imageBase64="data:image/png;base64,......",
    printWidth=58,
    endFeed=3,
))
```

## 11-12. PDF 打印（含多页）

::: tip 多页
用 `client.tsplPdfsPrint(request)` / `client.escPdfsPrint(request)`，返回结果的 `data` 包含逐页信息。
:::

```tsplpdf(单页)
from pathlib import Path
from kuaimai_core.request import TsplPdfPrintRequest

resp = client.get_acs_response(TsplPdfPrintRequest(
    sn="KM118DW123",
    file=Path("/pdf/demo.pdf"),
    width=75,
    height=100,
    dpi=203,
))
```

```tsplpdf(多页)
from pathlib import Path
from kuaimai_core.request import TsplPdfPrintRequest

resp = client.tsplPdfsPrint(TsplPdfPrintRequest(
    sn="KM118DW123",
    file=Path("/pdf/demo.pdf"),
))
```

```escpdf
from pathlib import Path
from kuaimai_core.request import EscPdfPrintRequest

resp = client.get_acs_response(EscPdfPrintRequest(
    sn="KM118DW123",
    file=Path("/pdf/demo.pdf"),
    printWidth=58,
    endFeed=3,
))

# 多页
resp = client.escPdfsPrint(EscPdfPrintRequest(sn="KM118DW123", file=Path("/pdf/demo.pdf"), printWidth=58))
```

## 13. 任务结果查询

::: warning 频率限制
同一 SN 每秒最多 2 次；单次最多 50 个 jobId。**建议使用异步回调代替轮询**。
:::

```python
from kuaimai_core.request import ResultRequest

resp = client.get_acs_response(ResultRequest(
    sn="KM118DW123",
    jobIds=["1775636782780"],
))
# resp.data → [{ jobId, desc, code }]
# code: 2000 成功 / 2006 等待 / 2007 失败 / 2004 异常
```

## 14-16. 语音 / 取消 / 调浓度

```python
from kuaimai_core.request import (
    BroadcastRequest, CancelJobRequest, AdjustDeviceDensityRequest,
)

# 语音（KM118MGL / KME31GP；音量 0~99；内容 ≤ 50 字）
client.get_acs_response(BroadcastRequest(
    sn="KM118DW123",
    volume=80,
    volumeContent="测试语言播报",
))

# 取消队列任务
client.get_acs_response(CancelJobRequest(sn="KM118DW123"))

# 调节浓度（KM118 / KME31 / KME41；1~15，推荐 8；机器需处于标签模式）
client.get_acs_response(AdjustDeviceDensityRequest(sn="KM118DW123", density=8))
```

## 17-19. KM360C 三段式接入

```python
from kuaimai_core.request import (
    GetCainiaoCodeRequest, CainiaoBindRequest, CainiaoPrintRequest,
)

# 17. 获取 code（有效期 5 分钟，机器会吐纸打印 code）
client.get_acs_response(GetCainiaoCodeRequest(imei="863130068516971"))

# 18. 绑定
client.get_acs_response(CainiaoBindRequest(
    imei="863130068516971",
    code="4841",
))

# 19. 图片打印
client.get_acs_response(CainiaoPrintRequest(
    imei="863130068516971",
    imageBase64Data="data:image/png;base64,......",
))
```

## 与 CloudExample.py 的对应关系

| 函数 | Request 类 / 调用方式 |
|---|---|
| bind_device_example / unbind_device_example / query_device_status_example | BindDeviceRequest / UnbindDeviceRequest / QueryDeviceStatusRequest |
| tspl_template_print_example / esc_template_print_example / tspl_template_write_example | 相应 Request + get_acs_response |
| tspl_xml_write_example / esc_xml_write_example | TsplXmlWriteRequest / EscXmlWriteRequest |
| tspl_image_print_example / esc_image_print_example | TsplImageRequest / EscImageRequest |
| tspl_pdf_print_example / tspl_pdfs_print_example | TsplPdfPrintRequest + get_acs_response / client.tsplPdfsPrint(...) |
| esc_pdf_print_example / esc_pdfs_print_example | EscPdfPrintRequest + get_acs_response / client.escPdfsPrint(...) |
| result_example / broadcast_example / cancel_job_example / adjust_device_density_example | ResultRequest / BroadcastRequest / CancelJobRequest / AdjustDeviceDensityRequest |
| get_cainiao_code_example / cainiao_bind_example / cainiao_print_example | KM360C 三段式 |
| cainiao_tspl_template_print_example | TsplTemplatePrintRequest(imei=...) |

## 常见错误码

::: warning 错误码提示
4001 · 4005 · 4009 · 6009 · 6010 · 6015 · 6019 · 6021 · 6022 · 6024 · 6026 · 6035 · 6036。完整表见[错误码总表](#error-codes)。公开文档对少数接口未单独列出错误码；调用失败时以实际返回的 `code` / `message` 为准。
:::
