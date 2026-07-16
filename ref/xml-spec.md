# XML 指令参考

::: info 使用
下方两大块分别对应**间隙纸标签机（TSPL）**和**连续纸小票机（ESC）**。两套元素同名（`page/render/t/bc/qrc/img`）但属性不同，切勿混用。
:::

## TSPL · 标签打印（间隙纸）

适用接口：[tsplXmlWrite](/zh/http/custom-print/tspl-xml-write)。适用机型：KM118 系列（除 EG/MEG）/ KME31 / KME41 / KME20 / KMSX320 / KMUL410。

### 整体模板

```xml
<page>
    <render>
        <t>hello</t>
        <bc x='50' y='50'>209490224913</bc>
        <qrc x='100' y='100'>529348520501</qrc>
    </render>
</page>
```

### render 元素

```
<render width='75' height='100' direction='0'></render>
```

| 属性 | 说明 |
|------|------|
| width | 渲染宽度，单位 mm，默认 75 |
| height | 渲染高度，单位 mm，默认 100，最大 220 |
| direction | 出纸方向，可选 0 / 180，默认 0 |

### t 文本元素

```xml
<t x='1' y='1' font='TSS16.BF2' textWidth='40' bold='0'
   xMultiple='1' yMultiple='1' rotate='0'>hello</t>
```

| 属性 | 说明 |
|------|------|
| x, y | 起始坐标（8 像素 = 1mm） |
| font | 字体（TSS16.BF2 / TSS24.BF2） |
| textWidth | 文本展示宽度（mm），超出自动换行 |
| bold | 0 不加粗 / 1 加粗 |
| xMultiple | 宽的倍数（整数） |
| yMultiple | 高的倍数（整数） |
| rotate | 旋转角度 0 / 90 / 180 / 270 |

### bc 条码元素

```xml
<bc x='1' y='1' codeType='128' bcWidth='40'
    bcHeight='10' fontSize='3' style='2' rotate='0'>209490224913</bc>
```

| 属性 | 说明 |
|------|------|
| codeType | 128（Code 128） |
| bcWidth | 条码总宽度 mm |
| bcHeight | 条码高度 mm |
| fontSize | 文本字号 mm，默认 3 |
| style | 0 不显示 / 2 下方 / 4 上方 |
| rotate | 0 / 90 / 270 |

### qrc 二维码元素

```xml
<qrc x='1' y='1' qrcWidth='20'>529348520501</qrc>
```

| 属性 | 说明 |
|------|------|
| x, y | 起始坐标（8dot = 1mm） |
| qrcWidth | 宽度 / 高度，单位 mm |

### img / box / circle / bar

```xml
<img x='1' y='1'>base64</img>
<!-- 图片 base64 数据，建议 ≤ 50KB -->

<box x='1' y='1' xEnd='100' yEnd='50' thickness='1' radius='0' />
<!-- 线框：起止点、线宽、圆角半径 -->

<circle x='1' y='1' diameter='100' thickness='1'/>
<!-- 圆：起点、直径、线宽 -->

<ellipse x='1' y='1' width='100' height='50' thickness='1'/>
<!-- 椭圆：起点、宽高、线宽 -->

<bar x='1' y='1' width='100' height='4'/>
<!-- 实心矩形（线条） -->
```

## ESC · 小票打印（连续纸）

适用接口：[escXmlWrite](/zh/http/custom-print/esc-xml-write)。适用机型：KM118 系列（除 EG/MEG）/ KME31 / KME41 / KME20 / KMDP 系列。

### 整体模板

```xml
<page>
    <render>
        <t size='10'>hello</t>
        <bc model='65' height='80' showT='down' wide='8'>209490224913</bc>
        <qrc size='5'>529348520501</qrc>
    </render>
</page>
```

### render 元素

```xml
<render width='70'></render>
```

| 属性 | 说明 |
|------|------|
| width | 打印宽度，单位 mm |

### t 文本元素

```xml
<t size='10' align='center' feed='1' bold='0'>hello</t>
```

| 属性 | 说明 |
|------|------|
| size | 00 默认（中文 3mm，英数 1.5mm）；10 = 2 倍宽；20 = 3 倍宽；11 = 2×2（先宽后高） |
| align | center / left / right（默认 center） |
| feed | 回车换行步数，默认 1 |
| bold | 1 加粗 / 0 不加粗 |

### bc 条码元素

```xml
<bc model='73' height='80' showT='down' wide='8'
    font='0' align='left' feed='1'>209490224913</bc>
```

| 属性 | 说明 |
|------|------|
| model | 0~6 或 65~73（默认 73 CODE128）。0 UPC-A · 1 UPC-E · 2 JAN13 · 3 JAN8 · 4 CODE39 · 5 ITF · 6 CODABAR · 72 CODE93 · 73 CODE128 |
| height | 条码高度 dot 1~255 |
| showT | down / up / false |
| wide | 宽条宽度 dot 2~6 |
| font | 0 标准 / 1 压缩 |

### qrc 二维码元素

```xml
<qrc size='5' align='left' feed='1'>529348520501</qrc>
```

| 属性 | 说明 |
|------|------|
| size | 0~15（默认 3） |
| align | center / left / right |

### pm 页模式

```xml
<pm width='70' height='30'>
  <qrc x='1' y='1'>123456</qrc>
  <qrc x='20' y='1'>789324</qrc>
</pm>
```

开启页模式，宽高单位 mm。内部元素需要显式指定 x / y（单位 mm），可实现并排排版。

::: tip SDK 场景
Java / Python / PHP / JS 等 SDK 里的 `TsplXmlWriteRequest` / `EscXmlWriteRequest` 的 `xmlStr` / `instructions` 字段直接接受本页 XML；SDK 会自动做转义与签名，不需要自己拼 `&lt;`。
:::
