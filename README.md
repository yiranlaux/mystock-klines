# mystock-klines

mystock App 的历史日 K 数据仓库。数据来自腾讯行情接口，**不复权**（`day`，不是 `qfqday`）。

## 内容

| 路径 | 内容 |
|---|---|
| `a/*.json` | 沪深300 成分股 + 上证指数（`sh000001`），300 + 1 个文件 |
| `hk/*.json` | 恒生指数成分股 + 恒生指数（`hkHSI`），88 + 1 个文件 |
| `manifest.json` | 导出时间、区间、每只标的的根数与首末日期 |

文件内容保持上游原始结构，字段顺序为 `[日期, 开, 收, 高, 低, 量]`：

```json
{ "code": 0, "msg": "", "data": { "sz000001": { "day": [["2016-11-08", "11.750", "11.740", "11.860", "11.680", "1437906.000"], ...] } } }
```

## 直读

仓库公开，raw 直读带 `access-control-allow-origin: *`，浏览器与原生客户端都能直接 fetch：

```
https://raw.githubusercontent.com/yiranlaux/mystock-klines/<commit-sha>/a/sz000001.json
```

**请固定到具体 commit SHA 而不是 `main`**：SHA 一旦固定，文件内容永不变，CDN 可以长期缓存，也避免上游更新时读到一半新一半旧的数据。`manifest.json` 里记着本次导出的信息。

## 更新

由 mystock 仓库的 `npm run export:klines` 生成后推上来。腾讯接口有风控（连续取数会按 IP + 路径返回 HTTP 501 拦截页），导出脚本内建了限速、熔断与端点轮转。

## 口径

- 成分名单用**导出当时**的名单，存在幸存者偏差：已退市或被剔除的成分不在样本内。
- 不复权：历史价就是当时看到的价。
