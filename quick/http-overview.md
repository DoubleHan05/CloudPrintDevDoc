# 接口调用流程

## 对接流程

```
[1] 注册账号
      │
      ▼
[2] 后台 → 系统集成 → 获取 appId / appSecret
      │
      ▼
[3] 调用 bindDevice 绑定打印机
      │
      ▼
[4] 是否使用模板？
      ├─ 是 → 后台 → 模板管理 → 建模板 → 拿 templateId
      └─ 否 → 直接组装 XML 或原生指令
      │
      ▼
[5] 调用打印接口 → 获得 jobIds
      │
      ▼
[6] 获取打印结果
      ├─ 推荐：配置回调地址，接收推送
      └─ 备用：轮询 result 接口
```

## 请求结构

| 项 | 取值 |
|----|------|
| 域名 | `cloud.kuaimai.com` |
| 协议 | HTTP 或 HTTPS（推荐 HTTPS）|
| 请求方式 | POST |
| 请求头 | `Content-Type: application/json`（`shareFilePrint` 例外，使用 `multipart/form-data`）|
| 响应格式 | JSON |

## 公共请求参数

每个接口都需要携带以下三个公共参数：

| 参数名 | 类型 | 必传 | 说明 |
|--------|------|------|------|
| `appId` | String | ✓ | 后台「系统集成」中的 appId |
| `timestamp` | String | ✓ | 当前时间 `yyyy-MM-dd HH:mm:ss` |
| `sign` | String | ✓ | 签名值，详见[签名规则](./sign) |

## 公共响应结构

所有接口返回统一 JSON 信封：

```json
{
  "status": true,
  "data": {},
  "message": null,
  "code": null,
  "exceptionMessage": null
}
```

| 字段 | 类型 | 说明 |
|------|------|------|
| `status` | boolean | `true` 为成功，`false` 为失败 |
| `data` | object | 具体接口的返回数据 |
| `message` | String | `status` 为 `false` 时返回信息 |
| `code` | Number | 业务错误码，见[错误码总表](../ref/error-codes) |

## 关键概念

| 概念 | 含义 |
|------|------|
| `appId` | 接入方应用唯一标识，用于鉴权 |
| `appSecret` | 接入方密钥，参与签名，**严禁客户端泄露** |
| `sn` | 打印机序列号，机器底部或自检页可查 |
| `deviceKey` | 设备密钥，部分设备机身贴有，绑定/解绑时校验 |
| `templateId` | 模板 id，在后台模板管理创建后获取 |
| `jobId` | 打印任务 id，打印接口返回，用于结果查询或回调匹配 |
| `renderData` | 小票模板动态数据（JSONObject 字符串）|
| `renderDataArray` | 标签模板动态数据（JSONArray 字符串，每个元素一张标签）|

::: tip 获取渲染数据骨架
后台模板管理页面提供 **"获取渲染数据"** 按钮，点击后弹窗展示该模板需要的 `renderData`/`renderDataArray` JSON 骨架，替换业务数据即可。
:::

## 5 分钟快速开始

1. **申请凭证** — 注册登录[快麦管理后台](http://admin.iot.kuaimai.com/)，进入「系统集成」获取 appId 与 appSecret
2. **绑定打印机** — 确认打印机已联网，调用 [bindDevice](../http/device/bind-device) 绑定
3. **建模板**（仅模板打印需要）— 进入「模板管理」新建，记录 `templateId`
4. **调用打印接口** — 参考[接口选择指南](./api-guide)按场景选接口
5. **获取结果** — 在后台配置回调地址，或用 [result 接口](../http/task/print-result) 轮询

```bash
# 绑定打印机 curl 示例
curl -X POST 'https://cloud.kuaimai.com/api/cloud/device/bindDevice' \
  -H 'Content-Type: application/json' \
  -d '{
    "sn": "KM118933",
    "bindName": "demoUser",
    "noteName": "杭州分店",
    "appId": "1612694567132",
    "timestamp": "2026-04-30 15:29:29",
    "sign": "请按签名规则计算"
  }'
```
