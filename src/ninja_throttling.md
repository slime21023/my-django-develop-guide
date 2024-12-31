# Throttling

## 節流概述
Django Ninja 的流量限制功能可以幫助控制 API 的請求頻率，避免過多請求對伺服器造成負擔。流量限制可以在以下層級進行設置：
1. 全局層級：透過 throttle 參數設定，適用於所有 API 請求。
2. 路由層級：在路由註冊時設定流量限制。
3. 操作層級：在具體操作上設置特定的流量限制，會覆蓋全局與路由層級的設定。

## 內建流量限制
Django Ninja 提供了多種內建流量限制器，例如：
* `AnonRateThrottle`：限制未認證用戶的請求頻率。
* `UserRateThrottle`：根據用戶 ID 限制已認證用戶的請求頻率。
* `AuthRateThrottle`：根據已認證用戶的身份進行限制。

```python
from ninja.throttling import AnonRateThrottle, AuthRateThrottle

api = NinjaAPI(
    throttle=[
        AnonRateThrottle('10/s'),
        AuthRateThrottle('100/s'),
    ],
)
```