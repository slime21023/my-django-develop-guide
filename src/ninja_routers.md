# Routers

## 創建和配置路由器

在您的應用程序中創建一個新的 Python 文件（例如 events/api.py），並定義一個路由器：
```python
from ninja import Router
from .models import Event

router = Router()

@router.get('/')
def list_events(request):
    return [{"id": e.id, "title": e.title} for e in Event.objects.all()]

@router.get('/{event_id}')
def event_details(request, event_id: int):
    event = Event.objects.get(id=event_id)
    return {"title": event.title, "details": event.details}
```

## 添加路由器到 API：
在您的主 API 文件中（例如 api.py），導入並添加路由器：
```python
from ninja import NinjaAPI
from events.api import router as events_router

api = NinjaAPI()
api.add_router("/events/", events_router)
```
您還可以通過 Python 路徑添加路由器：
```python
api.add_router("/news/", "news.api.router")
api.add_router("/blogs/", "blogs.api.router")
```

## 使用標籤和身份驗證
您可以使用 tags 參數為路由器中的所有操作添加標籤，並使用 auth 參數應用身份驗證：

```python
from ninja import NinjaAPI, Router
from ninja.security import BasicAuth

router = Router(tags=["events"])

@router.get('/')
def list_events(request):
    return [{"id": e.id, "title": e.title} for e in Event.objects.all()]

api = NinjaAPI()
api.add_router("/events/", router, auth=BasicAuth())
```

## Nested routers
```python
from django.contrib import admin
from django.urls import path
from ninja import NinjaAPI, Router

api = NinjaAPI()

first_router = Router()
second_router = Router()
third_router = Router()


@api.get("/add")
def add(request, a: int, b: int):
    return {"result": a + b}


@first_router.get("/add")
def add(request, a: int, b: int):
    return {"result": a + b}


@second_router.get("/add")
def add(request, a: int, b: int):
    return {"result": a + b}


@third_router.get("/add")
def add(request, a: int, b: int):
    return {"result": a + b}


second_router.add_router("l3", third_router)
first_router.add_router("l2", second_router)
api.add_router("l1", first_router)

urlpatterns = [
    path("admin/", admin.site.urls),
    path("api/", api.urls),
]
```