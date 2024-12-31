# Testing

## 測試範例 API

```python
from ninja import NinjaAPI, Schema

api = NinjaAPI()
router = Router()

class HelloResponse(Schema):
    msg: str

@router.get("/hello", response=HelloResponse)
def hello(request):
    return {"msg": "Hello World"}

api.add_router("", router)
```

使用 Django `TestCase`

```python
from django.test import TestCase
from ninja.testing import TestClient

class HelloTest(TestCase):
    def test_hello(self):
        client = TestClient(router)
        response = client.get("/hello")

        self.assertEqual(response.status_code, 200)
        self.assertEqual(response.json(), {"msg": "Hello World"})
```

訪問反序列化數據

```python
self.assertEqual(response.data, {"msg": "Hello World"})
```

## 添加自定義屬性到請求對象

```python
class HelloTest(TestCase):
    def test_hello(self):
        client = TestClient(router)
        # request.company_id 將在視圖中設置
        response = client.get("/hello", company_id=1)
```

## 指定標頭
您可以在測試類實例化和實際請求中指定標頭：

```python
client = TestClient(router, headers={"A": "a", "B": "b"})
# 請求將使用 {"A": "na", "B": "b", "C": "nc"} 標頭
response = client.get("/test-headers", headers={"A": "na", "C": "nc"})
```