# Input Handling

## 基本輸入處理

Django Ninja 基於 pydantic 開發，使用 Python 的類型提示來定義輸入數據的結構。你可以使用 Schema 類來定義輸入數據的模式。

```python
from ninja import Schema

class ItemSchema(Schema):
    name: str
    price: float
    quantity: int
```

## 使用 Schema 驗證輸入

你可以在 API 端點中使用 `Schema` 來驗證輸入數據。以下是一個示例，展示如何在 POST 請求中使用 `Schema` 來驗證輸入數據：

```python
from ninja import Schema
from typing import List

class AddressSchema(Schema):
    street: str
    city: str
    zipcode: str

class UserSchema(Schema):
    name: str
    age: int
    addresses: List[AddressSchema]
```

## 處理嵌套結構

Django Ninja 還支持處理嵌套結構。你可以在 `Schema` 中定義嵌套的 `Schema` 來處理複雜的輸入數據。

```python
from ninja import Schema
from typing import List

class AddressSchema(Schema):
    street: str
    city: str
    zipcode: str

class UserSchema(Schema):
    name: str
    age: int
    addresses: List[AddressSchema]
```


## 自定義驗證

你可以通過自定義驗證方法來添加額外的驗證邏輯。以下是一個示例，展示如何添加自定義驗證方法：

```python
from ninja import Schema
from pydantic import validator

class UserSchema(Schema):
    name: str
    age: int

    @validator('age')
    def check_age(cls, v):
        if v < 0:
            raise ValueError('age must be positive')
        return v
```


## 處理 Path Parameters

Path parameters 是 URL 路徑中的變量，用於動態生成 URL。Django Ninja 允許你在路由中定義路徑參數，並在視圖中訪問這些參數。

```python
from ninja import NinjaAPI

api = NinjaAPI()

@api.get("/items/{item_id}")
def get_item(request, item_id: int):
    # 在這裡處理路徑參數
    return {"item_id": item_id}
```


## 查詢參數介紹

查詢參數是 API 設計的基本部分，允許客戶端通過 URL 向伺服器傳遞額外的數據。在 Django Ninja 中，查詢參數會自動從不屬於路徑參數的函數參數中解釋出來。

```python
from ninja import NinjaAPI

api = NinjaAPI()

@api.get("/items")
def list_items(request, limit: int = 10, offset: int = 0):
    # 處理查詢參數
    return {"limit": limit, "offset": offset}
```

在這個示例中，`limit` 和 `offset` 是具有預設值的查詢參數。


你還可以使用 Schema 來封裝查詢參數，這提供了更好的組織和驗證。以下是使用 Schema 的示例：

```python
from datetime import date
from typing import List
from pydantic import Field
from ninja import NinjaAPI, Query, Schema

api = NinjaAPI()

class Filters(Schema):
    limit: int = 100
    offset: int = None
    query: str = None
    category__in: List[str] = Field(None, alias="categories")

@api.get("/filter")
def events(request, filters: Filters = Query(...)):
    return {"filters": filters.dict()}
```


## 處理 FormData

Django Ninja 支援解析和驗證 `request.POST` 資料（即 `application/x-www-form-urlencoded` 或 `multipart/form-data`）以下是如何使用 `Form` 來處理表單資料的範例：

```python
from ninja import NinjaAPI, Form

api = NinjaAPI()

@api.post("/login")
def login(request, username: Form[str], password: Form[str]):
    return {'username': username, 'password': '*****'}
```


您也可以使用 `Schema` 來組織您的參數：

```python
from ninja import Form, Schema

api = NinjaAPI()
class Item(Schema):
    name: str
    description: str = None
    price: float
    quantity: int


@api.post("/items")
def create(request, item: Form[Item]):
    return item
```

## 過濾查詢集

Django Ninja 提供了一個強大的過濾機制，允許您使用 `FilterSchema` 來處理查詢集的過濾。`FilterSchema` 是一個常規的 Schema，利用 Pydantic 的所有必要功能，並添加一些額外的特性來簡化將用戶面對的過濾參數轉換為數據庫查詢。


#### **使用 FilterSchema**

如果您希望用戶能夠根據多個不同的屬性過濾查詢集，將過濾器封裝到 `FilterSchema` 類中是有意義的。這樣可以方便地管理和應用過濾條件。

```python
from ninja import FilterSchema, Field
from typing import Optional

class BookFilterSchema(FilterSchema):
  name: Optional[str] = None
  author: Optional[str] = None
  created_after: Optional[datetime] = None

@api.get("/books")
def list_books(request, filters: BookFilterSchema = Query(...)):
    books = Book.objects.all()
    books = filters.filter(books)
    return books
```

#### **自定義過濾邏輯**

您還可以獲取準備好的 Q 表達式並自行執行過濾。這允許您根據需要自定義過濾邏輯。

```python
class BookFilterSchema(FilterSchema):
    search: Optional[str] = Field(None, q=['name__icontains',
                                     'author__name__icontains',
                                     'publisher__name__icontains'])
```


