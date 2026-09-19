# API 对接文档（sms688）

Base URL：`https://sms688.com`

鉴权：请求头 `X-Api-Key: sk_xxxxxxxx`（登录后在「个人资料 / API 文档」生成，明文只显示一次）

## 接口一览

| 方法 | 路径 | 说明 |
|---|---|---|
| GET | `/api/open/stock` | 查询在售库存与号段 |
| POST | `/api/open/buy` | 下单取号（**支持一次买多个**，返回号码与接码链接） |
| GET | `/api/open/orders` | 查询最近订单 |
| GET | `/api/open/sms?number=xxx` | 读取号码最新一条短信 |
| GET | `/api/open/cdk/projects` | CDK 可兑换项目与档位（卡密业务用） |
| POST | `/api/open/cdk/query` | 按卡密查询兑换到的号码与接码链接 |

## 示例

```bash
curl -H "X-Api-Key: sk_xxx" https://sms688.com/api/open/stock

curl -X POST https://sms688.com/api/open/buy \
  -H "X-Api-Key: sk_xxx" -H "Content-Type: application/json" \
  -d '{"item_key":"openai_21_30","quantity":20}'

curl -H "X-Api-Key: sk_xxx" "https://sms688.com/api/open/sms?number=131xxxxxxxx"
```

### 批量下单说明

- `quantity` 支持 1–5000，一次提交即可拿到多个号码；
- 取号顺序：**本站号池优先 → 不足再向上游补货**，凑不满整单会失败并退回预扣金额（不会只发一半）；
- 可选 `prefixes: ["1515","1231"]` 指定号段，不传表示不限号段；
- 可选 `customer`（下游自己的客户编号），方便区分「这一单是谁的用户下的」。

```json
{
  "ok": true,
  "order_id": 236,
  "quantity": 20,
  "amount_usdt": 5.0,
  "numbers": [{"number": "15153374827", "link": "https://sms688.com/sms/xxxx", "prefix": "1515"}]
}
```

## 限流

- 下单：60 次/小时、500 次/天（按账号计）
- 建议轮询间隔 ≥ 5 秒，避免无效请求

## 接码链接（给终端客户）

`https://sms688.com/sms/<token>` —— 打开即最新验证码，不需要 API Key，可直接发给客户；页面同时显示号码项目与到期时间。
