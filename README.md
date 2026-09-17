# 接码平台对比与接码教程（美国实卡 / 长效接码）

> 一个持续更新的接码资料库：号码类型对比、价格参考、收码成功率优化、批量与 API 对接示例。
> 维护者：sms688 短信接码平台（<https://sms688.com>）｜Telegram 频道：[@qy688sms](https://t.me/qy688sms)（库存播报 / 补货通知 / 接码教程）— 本站主打美国 T-Mobile 实卡号码，2-30 天档位，支持续费与 API 对接。

## 这个仓库有什么

- 接码号码类型对比（实卡 / 虚拟号 / VoIP / 公共免费号）
- 常见平台的收码成功率经验与踩坑记录
- 价格参考区间（按季度更新）
- 批量接码与 API 对接示例代码（Python）
- 新手教程与常见问题

## 快速开始

### 1. 选号思路

| 场景 | 建议号码类型 | 建议档位 |
|---|---|---|
| ChatGPT / Codex / Claude 注册 | 美国实卡（非 VoIP） | 21-30 天 |
| Google Voice 申请与保号 | 美国实卡 | 21-30 天 |
| Wise / 银行 / 支付类验证 | 美国实卡 | 21-30 天，到期续费 |
| 普通论坛、小站注册 | 虚拟号即可 | 2-10 天 |
| 批量注册、工作室 | 实卡 + API 对接 | 按项目批量下单 |

### 2. 判断号码质量的三条标准

1. **是否实卡**：VoIP 号段会被 Google、OpenAI、银行类直接识别并拒绝。
2. **是否独享**：公共免费号短信对所有人可见，等于把验证码公开。
3. **是否可续费**：注册只是开始，二次验证才是关键；号码过期等于账号失联。

### 3. API 对接示例（Python）

```python
import time
import httpx

BASE = "https://sms688.com"
API_KEY = "sk_你的APIKey"  # 登录后在「个人资料 / API 文档」生成，只显示一次
HEADERS = {"X-Api-Key": API_KEY}


def stock():
    """查询在售库存与号段"""
    return httpx.get(f"{BASE}/api/open/stock", headers=HEADERS, timeout=30).json()


def buy(item_key: str, quantity: int = 1, prefixes=None):
    """下单取号：item_key 用市场卡片里的档位键，例如 openai_21_30"""
    payload = {"item_key": item_key, "quantity": quantity}
    if prefixes:
        payload["prefixes"] = prefixes
    return httpx.post(f"{BASE}/api/open/buy", headers=HEADERS, json=payload, timeout=60).json()


def latest_sms(number: str) -> str:
    """读取某个号码最新一条短信（含验证码）"""
    data = httpx.get(
        f"{BASE}/api/open/sms", headers=HEADERS, params={"number": number}, timeout=30
    ).json()
    return data.get("content") or ""


def wait_code(number: str, timeout: int = 180, interval: int = 5) -> str:
    """轮询等待验证码出现"""
    deadline = time.time() + timeout
    while time.time() < deadline:
        content = latest_sms(number)
        if content:
            return content
        time.sleep(interval)
    return ""


if __name__ == "__main__":
    order = buy("openai_21_30", 1)
    number = order["numbers"][0]["phone"]
    print("号码:", number, "接码链接:", order["numbers"][0]["link"])
    print("短信:", wait_code(number))
```

### 4. 接码链接怎么用

每个号码自带一条公开接码链接（形如 `https://sms688.cc/sms/<token>`），**打开就是最新验证码，不需要 API Key**，可以直接发给终端客户；API 适合程序化批量取码。

## 收码成功率优化清单

- 优先实卡号段：真实运营商号段兼容性远好于 VoIP
- 号码地区与网络节点保持一致（美区服务用美国节点）
- 一个号码对应一个账号，不要复用
- 收不到码先同步最新短信，再换号段，不要反复点重发
- 重要账号直接选 21-30 天档位，并在到期前续费
- 批量场景做好号码与账号的对应关系记录

## 常见问题

**接码平台合法吗？**
接码属于账号验证工具，请在遵守目标平台条款与当地法律的前提下使用；不要用于欺诈、洗钱等非法用途。

**为什么实卡比虚拟号贵？**
实卡是真实运营商号码，成本更高，但在 Google Voice、OpenAI、银行类场景的通过率明显更好。

**号码到期后还能收短信吗？**
不能。到期后号码回收，需要继续使用请在到期前续费。

**可以给下游站做上游吗？**
可以。sms688 支持下游站点对接，用 API 取号取码，对外域名可隐藏上游信息。

## 目录

- [号码类型对比](./docs/number-types.md)
- [收码成功率优化](./docs/success-rate.md)
- [API 对接文档](./docs/api.md)
- [常见问题](./docs/faq.md)

## 免责声明

本仓库内容为技术资料整理，价格与库存会随时间变化，以平台页面的实时数据为准。请合规使用相关服务。

---

官网：<https://sms688.com>　客服 Telegram：[@qinyu688](https://t.me/qinyu688)
