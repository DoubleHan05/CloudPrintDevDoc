# 模板打印通用说明

## bindTable / bindKey / value 的含义

模板里的每个动态组件（文本、条码、二维码、动态图片、动态表格）都会绑定一个 `bindTable` 和 `bindKey`：

- `bindTable` → 对应 `renderData` / `renderDataArray` 中的**外层 key**（数据组名）
- `bindKey` → 对应该数据组的数组对象里的 **key**（字段名）

```json
// renderDataArray 示例（标签模板，数组里每个元素对应一张标签）
[
  {
    "goods": [                 // ← bindTable = "goods"
      { "name": "商品A" },     // ← bindKey = "name"
      { "price": "29.9" }      // ← bindKey = "price"
    ]
  }
]
```

::: tip 如何获取正确的骨架
进入后台 → 模板管理 → 找到模板 → 点「**获取渲染数据**」，弹窗内容即为该模板需要的数据骨架，替换业务值即可。
:::

## 标签 vs 小票的数据结构差异

| 特性 | 标签模板（`tsplTemplatePrint`）| 小票模板（`escTemplatePrint`）|
|------|----------------------------|-----------------------------|
| 参数名 | `renderDataArray` | `renderData` |
| 结构 | **JSONArray 字符串**，每个元素 = 一张标签 | **JSONObject 字符串**，整体一份数据 |
| 批量 | 支持一次最多 50 张 | 不支持批量（一次一份）|

## 标签模板平铺规则

标签模板的 `renderDataArray` 每个元素是一张标签的完整数据，所有 `bindTable` 的数据**平铺在同一个对象中**：

```json
[
  {
    "info": [{ "orderNo": "A001", "shopName": "杭州店" }],
    "goods": [{ "name": "商品A", "price": "29.9" }]
  },
  {
    "info": [{ "orderNo": "A002", "shopName": "杭州店" }],
    "goods": [{ "name": "商品B", "price": "19.9" }]
  }
]
```

## 小票多行动态表格示例

```json
{
  "orderInfo": [{ "shopName": "快麦便利店", "orderNo": "A001" }],
  "goodsList": [
    { "goodsName": "拿铁", "num": "2", "unitPrice": "14.00", "amount": "28.00" },
    { "goodsName": "三明治", "num": "1", "unitPrice": "12.00", "amount": "12.00" }
  ]
}
```

## 常见错误

| 现象 | 原因 | 处理 |
|------|------|------|
| 6009 动态渲染数据结构错误 | renderData 不是合法 JSON 字符串 | 先用 `JSON.stringify` 序列化再传 |
| 6010 模版类型不匹配 | 用了标签模板调了小票接口（或反之）| 确认 templateId 对应的模板类型 |
| 6015 无权限访问模版 | templateId 不属于该 appId | 在该 appId 下创建模板 |
| 6022 批量渲染数量过大 | renderDataArray 超过 50 个元素 | 拆批，每次 ≤ 50 张 |
| 打印内容为空 / 乱码 | bindTable / bindKey 拼写错误 | 对照「获取渲染数据」弹窗里的骨架 key 逐字符核对 |
