# URLs

## 什麼是 Django URL 配置？

Django URL 配置是將 URL 模式映射到視圖的過程。它允許你定義 URL 模式，並指定當這些模式匹配時應該調用哪個視圖。

## 基本 URL 配置

Django 使用 `urls.py` 文件來配置 URL。這個文件通常位於每個應用的目錄中，並且包含 URL 模式(urlpatterns)和視圖的映射。

### URL 配置示例

```python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.home, name='home'),
    path('about/', views.about, name='about'),
]
```

## URL 模式

URL 模式定義了 URL 的結構。Django 使用 path 和 re_path 函數來定義 URL 模式。

### 使用 `path`

`path` 函數用於定義簡單的 URL 模式:
```python
from django.urls import path
from . import views

urlpatterns = [
    path('articles/<int:year>/', views.year_archive, name='year_archive'),
    path('articles/<int:year>/<int:month>/', views.month_archive, name='month_archive'),
]
```

### 使用 `re_path`

`re_path` 函數用於定義更複雜的 URL 模式，它使用正則表達式:
```python
from django.urls import re_path
from . import views

urlpatterns = [
    re_path(r'^articles/(?P<year>[0-9]{4})/$', views.year_archive, name='year_archive'),
    re_path(r'^articles/(?P<year>[0-9]{4})/(?P<month>[0-9]{2})/$', views.month_archive, name='month_archive'),
]
```

## URL 命名

為 URL 模式命名可以使你在模板和視圖中更容易地引用它們。你可以使用 name 參數來命名 URL 模式：

```python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.home, name='home'),
    path('about/', views.about, name='about'),
    path('articles/<int:year>/', views.year_archive, name='year_archive'),
]
```

## 包含其他 URL 配置
使用 include 函數來包含其他 URL 配置，這允許你將 URL 配置分割到多個文件中，從而使代碼更具模塊化。

```python
from django.urls import include, path

urlpatterns = [
    path('articles/', include('articles.urls')),
    path('blog/', include('blog.urls')),
]
```

## 命名空間
命名空間允許你在包含的 URL 配置中使用相同的 URL 名稱，而不會發生衝突。使用 namespace 參數來定義命名空間：

```python
from django.urls import include, path

urlpatterns = [
    path('articles/', include(('articles.urls', 'articles'), namespace='articles')),
    path('blog/', include(('blog.urls', 'blog'), namespace='blog')),
]
```

## 動態 URL 路由
在 URL 模式中使用動態參數，並在視圖中訪問這些參數。

#### 動態 URL 路由示例
```python
from django.urls import path
from . import views

urlpatterns = [
    path('articles/<int:year>/', views.year_archive, name='year_archive'),
    path('articles/<int:year>/<int:month>/', views.month_archive, name='month_archive'),
]
```

#### 在視圖中訪問動態參數
```python
from django.http import HttpResponse

def year_archive(request, year):
    return HttpResponse(f"Year: {year}")

def month_archive(request, year, month):
    return HttpResponse(f"Year: {year}, Month: {month}")
```

## 默認參數
在 URL 模式中定義默認參數，這些參數將在未提供時使用。

#### 默認參數示例

```python
from django.urls import path
from . import views

urlpatterns = [
    path('articles/<int:year>/', views.year_archive, {'month': 1}, name='year_archive'),
]
```

#### 在視圖中訪問默認參數
```python
from django.http import HttpResponse

def year_archive(request, year, month):
    return HttpResponse(f"Year: {year}, Month: {month}")
```
