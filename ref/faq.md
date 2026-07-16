# 常见问题 FAQ

## 凭证与签名

::: details 如何获取 appId / appSecret？
登录 [http://admin.iot.kuaimai.com](http://admin.iot.kuaimai.com) → 进入 **系统集成** → 复制 appId 与 appSecret；appSecret 只在后台展示一次，妥善保存。
:::

::: details 接口总是返回 code 4015（签名错误）怎么办？
按以下步骤排查：1. 确认 appSecret 从后台复制时无多余空格
2. 参数必须按 ASCII 字典序排序（a < b < c ...）
3. 排除空值参数（null 或空字符串不参与签名）
4. MD5 结果取 32 位**小写**十六进制
5. 使用官网 API 调试工具对比生成的签名字符串
:::

::: details 前端能直接调用 API 吗？appSecret 会不会泄漏？
**不建议**。前端调用会在网络请求里暴露 appSecret。推荐做法：- 后端服务器代理签名后再转发到快麦云
- 或使用官方 微信小程序插件（插件容器内代签，前端不接触 appSecret）
:::

::: details 各 SDK 是否会自动帮我生成签名？
Java / PHP / Python / iOS / Android / UniApp / WeChat 插件都会**自动签名**；只需要传 appId + secret 即可。裸 HTTP 调用需要自己按[签名规则](#sign)生成 sign。
:::

## 打印问题

::: details 为什么每次打印都会出两张纸？
通常是**模板中设置的纸张高度大于实际纸张高度**导致打印机分页。解决：进入模板编辑器 → 调小模板高度 → 保存并重新测试。
:::

::: details code 6010：模板类型不匹配是什么意思？
接口与模板类型必须对应：`tsplTemplatePrint` 只能用**标签模板**；`escTemplatePrint` / `tsplTemplateWrite` 只能用**小票模板**。
:::

::: details 接口返回成功但没打印出任何内容？
依次排查：1. 调 `batchStatus` 确认设备在线且 `deviceStatus=0`（无异常）
2. 用 `result` 查 jobId：如果没有终态 code，任务可能还在队列或已丢失
3. 检查模板尺寸与实际纸张匹配
4. WiFi 机型确认蓝灯亮；4G 机型确认云服务未过期
:::

::: details code 6018：图片 Base64 数据错误怎么办？
检查图片格式与大小：建议 ≤ 50KB；使用图片打印接口时，`extra` 参数必须区分 `"0"`（TSPL 标签机）或 `"2"`（ESC 小票机）。
:::

::: details code 6019 打印内容过大 / code 6032 单日超限？
大小限制：`deviceWrite` ≤ 100KB；UP 系列 `escWrite` ≤ 30KB；`shareFilePrint` ≤ 150KB（zlib 压缩后）。频次：单设备 QPS ≤ 6（`6027`）；共享云打印单设备单日 ≤ 1000 次（`6032`）。
:::

## 数据格式

::: details renderData 和 renderDataArray 有什么区别？
`renderDataArray` 用于**标签模板**（多张，JSON 数组，每元素 = 一张标签）；`renderData` 用于**小票模板**（单张，JSON 对象）。
:::

::: details 怎样获取模板的正确渲染数据格式？
快麦管理后台 → 模板管理 → 选择你的模板 → 点击 **「获取渲染数据」** 按钮，弹出的 JSON 就是骨架，替换 value 即可。
:::

::: details 小票动态表格如何打印多行数据？
在 `escTemplatePrint` 的 `renderData` 里，把动态表格 bindTable 对应的**数组填多个对象**即可，每个对象打印一行：JSON{
  "goodsList": [
    { "goodsName": "拿铁",   "num": "2", "amount": "28.00" },
    { "goodsName": "三明治", "num": "1", "amount": "16.00" }
  ]
}
:::

## 设备管理

::: details 设备突然不响应了怎么排查？
1. 调用 `batchStatus` 确认设备在线状态和 deviceStatus
2. WiFi 机型：确认蓝灯亮（=配网成功）
3. 4G 机型：确认设备已开机、云服务未过期
4. 确认设备已被该 appId 绑定
:::

::: details KM360C 怎么绑定？
用 IMEI 替代 SN。流程：① 调用 `getDeviceCode` 传 IMEI → ② 设备打印 code（5 分钟有效）→ ③ 调用 `bindDevice` 传 `imei` + `code`。
:::

::: details 取消队列任务 cancelJob 有哪些限制？
只能取消**还在设备打印队列里、未开始打印**的任务；已开始或已完成的任务无法回退，纸张也无法回收。
:::

::: details 共享云打印和普通云打印的 SN 有什么不同？
共享云打印的 sn 以 `share_` 前缀开头，由番茄打印管家在开启共享时生成；请使用打印管家里显示的这个 SN，不要用打印机原本的 SN。
:::

## 回调与查询

::: details 异步回调如何配置？
登录后台 → **打印回调配置** → 填写公网可达 URL（建议 HTTPS）。收到回调后必须以 HTTP 200 返回 `{"data":"OK"}`；否则平台会重推 2 次（最多 3 次）。详见 异步回调推送。
:::

::: details code 6021：查询过于频繁怎么办？
同一 SN 每秒最多 `result` 查询 2 次。生产环境建议改用**异步回调**为主，`result` 仅做兜底扫描。
:::

::: details 怎样区分测试环境与生产环境？
微信小程序插件通过 `plugin.setOnline(true|false)` 切换；其他 SDK 默认走生产环境；快麦云 HTTP 直连都指向 `https://label-open.kuaimai.com`。
:::
