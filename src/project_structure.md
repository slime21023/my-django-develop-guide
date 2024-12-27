# Project Structure

## 專案結構

一個典型的 Django 專案結構如下：
```
myproject/
    manage.py
    myproject/
        __init__.py
        settings.py
        urls.py
        wsgi.py
    myapp/
        __init__.py
        admin.py
        apps.py
        migrations/
            __init__.py
        models.py
        tests.py
        views.py
```

## 創建視圖

在 `myapp/views.py` 文件中定義視圖：
```python
from django.http import HttpResponse

def index(request):
    return HttpResponse("Hello, world!")
```

## 配置應用程式的 URL 路徑

在 `myapp/urls.py` 文件中定義應用的 URL：
```python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.index, name='index'),
]
```

將應用程式的 URL 路徑添加到 `myproject/urls.py` 文件(執行環境)中：
```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('myapp/', include('myapp.urls')),
]
```

在 `http://127.0.0.1:8000/myapp/` 上執行應用程式的 URL 路徑，你應該會看到定義好的視圖 "Hello, world!"。

