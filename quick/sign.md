# 签名规则

所有 HTTP 接口请求都需要签名（`sign` 字段），算法为 MD5。

::: warning 安全提示
`appSecret` 是签名密钥，**请勿在前端/客户端代码中暴露**。签名应在服务端生成。
:::

## 算法公式

```
x    = key1 + value1 + key2 + value2 + ...   // 所有参数 key 按 ASCII 升序排列
sign = MD5( appSecret + x + appSecret )       // 32 位小写十六进制
```

## 签名步骤

1. **收集参数** — 把所有**非 sign** 的参数（公共参数 + 接口参数）放到一个 Map
2. **剔除空值** — 剔除 value 为 `null` 或 `""` 的参数（`0` / `false` 不是空，必须参与签名）
3. **ASCII 升序排序** — 将剩余参数的 key 按 ASCII 升序排列
4. **拼接 key+value** — 依次拼接：`key1value1key2value2...`（无分隔符）
5. **首尾包 appSecret** — 在拼好的字符串前后各加一次 `appSecret`
6. **MD5 32 位小写** — 得到最终 `sign`

## 逐步演算示例

假设：`appId="123456"`, `timestamp="2020-10-10 15:29:29"`, `sn="KM118MW"`, `appSecret="abc"`

| 步骤 | 操作 | 结果 |
|------|------|------|
| ① 收集 | 所有非 sign 参数 | `appId, timestamp, sn` |
| ② 剔除空值 | 无空值 | `appId, timestamp, sn` |
| ③ ASCII 排序 | a < s < t | `appId, sn, timestamp` |
| ④ 拼接 | | `appId123456snKM118MWtimestamp2020-10-10 15:29:29` |
| ⑤ 包 secret | | `abc` + 上面的字符串 + `abc` |
| ⑥ MD5 | 32 位小写 | `sign = MD5("abcappId123456snKM118MWtimestamp2020-10-10 15:29:29abc")` |

::: tip 调试工具
登录快麦管理后台，在「调试工具 → 查看签名」里输入相同参数，对比你的代码输出，快速定位偏差。
:::

## 代码实现

::: code-group

```java [Java]
import java.util.*;
import org.apache.commons.codec.digest.DigestUtils;

public static String createSign(Map<String, Object> paramMap, String secret) {
    StringBuilder sb = new StringBuilder(secret);
    paramMap.entrySet().stream()
        .filter(e -> e.getValue() != null && !"".equals(e.getValue().toString()))
        .sorted(Map.Entry.comparingByKey())           // ASCII 升序
        .forEach(e -> sb.append(e.getKey()).append(e.getValue()));
    sb.append(secret);
    return DigestUtils.md5Hex(sb.toString());
}
```

```python [Python]
import hashlib, json

def sign_value_to_string(value):
    if isinstance(value, bool):
        return "true" if value else "false"    # ⚠️ bool → string
    if isinstance(value, (dict, list)):
        return json.dumps(value, ensure_ascii=False, separators=(",", ":"))
    return str(value)

def create_sign(params: dict, secret: str) -> str:
    filtered = {
        k: v for k, v in params.items()
        if k != "sign" and v is not None and v != ""  # 0/False 保留
    }
    sign_str = "".join(
        k + sign_value_to_string(filtered[k])
        for k in sorted(filtered.keys())
    )
    return hashlib.md5((secret + sign_str + secret).encode("utf-8")).hexdigest()
```

```php [PHP]
<?php
function signValueToString($value): string {
    if (is_bool($value)) return $value ? 'true' : 'false';
    if (is_array($value) || is_object($value))
        return json_encode($value, JSON_UNESCAPED_UNICODE | JSON_UNESCAPED_SLASHES);
    return (string) $value;
}

function createSign(array $params, string $secret): string {
    $filtered = [];
    foreach ($params as $k => $v) {
        if ($k === 'sign' || $v === null || $v === '') continue;
        $filtered[$k] = $v;
    }
    ksort($filtered, SORT_STRING);
    $str = '';
    foreach ($filtered as $k => $v) $str .= $k . signValueToString($v);
    return md5($secret . $str . $secret);
}
```

```javascript [JS / Node]
const crypto = require('crypto');

function signValueToString(value) {
    if (typeof value === 'boolean') return value.toString();
    if (typeof value === 'object' && value !== null) return JSON.stringify(value);
    return String(value);
}

function createSign(paramMap, secret) {
    const parts = Object.keys(paramMap)
        .filter(k => k !== 'sign' && paramMap[k] != null && paramMap[k] !== '')
        .sort()
        .map(k => k + signValueToString(paramMap[k]))
        .join('');
    return crypto.createHash('md5').update(secret + parts + secret).digest('hex');
}
```

```go [Go]
import ("crypto/md5"; "encoding/json"; "fmt"; "reflect"; "sort"; "strings")

func createSign(params map[string]interface{}, secret string) string {
    keys := []string{}
    for k, v := range params {
        if k == "sign" || v == nil { continue }
        if s, ok := v.(string); ok && s == "" { continue }
        keys = append(keys, k)
    }
    sort.Strings(keys)
    var b strings.Builder
    for _, k := range keys {
        b.WriteString(k)
        b.WriteString(fmt.Sprint(params[k]))
    }
    sum := md5.Sum([]byte(secret + b.String() + secret))
    return fmt.Sprintf("%x", sum)
}
```

:::

## 常见错误

| 现象 | 根因 | 处理方式 |
|------|------|----------|
| 4015 签名错误 | 多种可能 | 先用调试工具验证，再逐条排查 |
| `0` / `false` 被丢掉 | `if (val)` 过滤了 falsy | 只剔除 `null` / `""`，保留 `0` / `false` |
| `sign` 本身参与了签名 | 未从 Map 移除 | 签名前先移除 `sign` key |
| 嵌套对象变 `[object Object]` | 未序列化 | 嵌套对象先 `JSON.stringify`（紧凑格式）|
| 时区不一致 | 本地时间 vs 服务端时区 | 统一用 UTC+8 `yyyy-MM-dd HH:mm:ss` |
| MD5 取了 16 位 | 短格式 MD5 | 必须取 32 位十六进制，全小写 |
