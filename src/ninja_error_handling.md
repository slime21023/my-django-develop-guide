# Error Handling

## 定義 Schema
在 Django Ninja 中，您可以使用 Pydantic 模型來定義 Schema。這個 Schema 指定了 API 返回的數據結構。以下是一個例子：

```python
from ninja import Schema

class UserIn(Schema):
    username: str
    password: str


class UserOut(Schema):
    id: int
    username: str


@api.post("/users/", response=UserOut)
def create_user(request, data: UserIn):
    user = User(username=data.username)
    user.set_password(data.password)
    user.save()
    return user
```

## 修改 Response 
您可以添加標頭、更改狀態碼或修改響應數據：

```python
from django.http import HttpRequest, HttpResponse

@api.get("/cookie/")
def feed_cookiemonster(request: HttpRequest, response: HttpResponse):
    # Set a cookie.
    response.set_cookie("cookie", "delicious")
    # Set a header.
    response["X-Cookiemonster"] = "blue"
    return {"cookiemonster_happy": True}
```

## 從 Django Model 中定義回傳的 Schema

如果您想要從 Django Model 中定義回傳的 Schema，您可以使用 `ModelSchema`:
```python
from django.contrib.auth.models import User
from ninja import ModelSchema

class UserSchema(ModelSchema):
    class Meta:
        model = User
        fields = ['id', 'username', 'first_name', 'last_name']
```

## 生成動態 Schema

`ModelSchema` 使用 `create_schema` 函數來生成動態 Schema，它是一種進階用法，需要小心使用。

```python
from django.contrib.auth.models import User
from ninja.orm import create_schema

# Using `fields`
UserSchema = create_schema(User, fields=['id', 'username'])

# Using `exclude`
UserSchema = create_schema(User, exclude=[
    'password', 'last_login', 'is_superuser', 'is_staff', 'groups', 'user_permissions']
)
```

## 覆寫 Pydantic 設定

您可以在 `Schema` 中覆寫 Pydantic 設定，例如：
```python
from ninja import Schema


def to_camel(string: str) -> str:
    return ''.join(word.capitalize() for word in string.split('_'))


class CamelModelSchema(Schema):
    str_field_name: str
    float_field_name: float

    class Config(Schema.Config):
        alias_generator = to_camel
```

## 分頁

Django Ninja 支援分頁，你可以使用 `paginate` 函數來分頁查詢集：
```python
from ninja.pagination import paginate

@api.get('/users', response=List[UserSchema])
@paginate
def list_users(request):
    return User.objects.all()
```

內建的 `Pagination` 類別
```python
from ninja.pagination import paginate, LimitOffsetPagination

@api.get('/users', response=List[UserSchema])
@paginate(LimitOffsetPagination)
def list_users(request):
    return User.objects.all()
```

```python
from ninja.pagination import paginate, PageNumberPagination

@api.get('/users', response=List[UserSchema])
@paginate(PageNumberPagination)
def list_users(request):
    return User.objects.all()
```

**使用 `RouterPaginated` 路由器**: 自動加上分頁功能

```python
from ninja.pagination import RouterPaginated

router = RouterPaginated()


@router.get("/items", response=List[MySchema])
def items(request):
    return MyModel.objects.all()

@router.get("/other-items", response=List[OtherSchema])
def ohter_items(request):
    return OtherModel.objects.all()
```

## Response Renderers

Django Ninja 支援多種 Response Renderers，你可以使用 `renderer` 參數來指定 Response Renderer：

自訂 Response Renderer，要實現 `render` 介面:
```python
from ninja import NinjaAPI
from ninja.renderers import BaseRenderer


class MyRenderer(BaseRenderer):
    media_type = "text/plain"

    def render(self, request, data, *, response_status):
        return ... # your serialization here

api = NinjaAPI(renderer=MyRenderer())
```

`render` 函數接受三個主要的參數：
*  request -> HTTP Request object
*  data -> 需要被序列化的 object
*  response_status -> `int` 型別，HTTP status code 回傳給客戶端

自訂 Response Renderer 使用 `orjson` 庫，以下是一個示例：
```python
import orjson
from ninja import NinjaAPI
from ninja.renderers import BaseRenderer


class ORJSONRenderer(BaseRenderer):
    media_type = "application/json"

    def render(self, request, data, *, response_status):
        return orjson.dumps(data)

api = NinjaAPI(renderer=ORJSONRenderer())
```

自訂 XML Response Renderer: 
```python
from io import StringIO
from django.utils.encoding import force_str
from django.utils.xmlutils import SimplerXMLGenerator
from ninja import NinjaAPI
from ninja.renderers import BaseRenderer


class XMLRenderer(BaseRenderer):
    media_type = "text/xml"

    def render(self, request, data, *, response_status):
        stream = StringIO()
        xml = SimplerXMLGenerator(stream, "utf-8")
        xml.startDocument()
        xml.startElement("data", {})
        self._to_xml(xml, data)
        xml.endElement("data")
        xml.endDocument()
        return stream.getvalue()

    def _to_xml(self, xml, data):
        if isinstance(data, (list, tuple)):
            for item in data:
                xml.startElement("item", {})
                self._to_xml(xml, item)
                xml.endElement("item")

        elif isinstance(data, dict):
            for key, value in data.items():
                xml.startElement(key, {})
                self._to_xml(xml, value)
                xml.endElement(key)

        elif data is None:
            # Don't output any value
            pass

        else:
            xml.characters(force_str(data))


api = NinjaAPI(renderer=XMLRenderer())
```