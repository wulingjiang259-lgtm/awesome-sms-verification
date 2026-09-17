# API 对接文档（sms688）

Base URL：`https://sms688.com`

鉴权：请求头 `X-Api-Key: sk_xxxxxxxx`（登录后在「个人资料 / API 文档」生成，明文只显示一次）

## 接口一览

| 方法 | 路径 | 说明 |
|---|---|---|
| GET | `/api/open/stock` | 查询在售库存与号段 |
| POST | `/api/open/buy` | 下单取号（返回号码与接码链接） |
| GET | `/api/open/orders` | 查询最近订单 |
| GET | `/api/open/sms?number=xxx` | 读取号码最新一条短信 |

## 示例

```bash
curl -H "X-Api-Key: sk_xxx" https://sms688.com/api/open/stock

curl -X POST https://sms688.com/api/open/buy \
  -H "X-Api-Key: sk_xxx" -H "Content-Type: application/json" \
  -d '{"item_key":"openai_21_30","quantity":2}'

curl -H "X-Api-Key: sk_xxx" "https://sms688.com/api/open/sms?number=131xxxxxxxx"
```

## 限流

- 下单：60 次/小时、500 次/天（按账号计）
- 建议轮询间隔 ≥ 5 秒，避免无效请求

## 接码链接（给终端客户）

`https://sms688.cc/sms/<token>` —— 打开即最新验证码，不需要 API Key，可直接发给客户。
