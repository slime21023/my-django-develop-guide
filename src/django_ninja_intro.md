# Django Ninja Intro

## Django Ninja
Django Ninja 是一個高性能、易學、快速編碼的 Django REST 框架。它旨在提供一個簡單且強大的方式來構建 API，並與 Django 核心和 ORM 有良好的集成。

## 為什麼選擇 Django Ninja？

* 高性能：Django Ninja 設計用於高性能應用，相對於 flask 能夠處理較大量請求。
* 易學易用：它提供了簡單的 API 來定義和處理 HTTP 請求和響應。
* 快速編碼：使用 Django Ninja，你可以快速構建和部署 API。

## 安裝 Django Ninja

使用 pip 來安裝 Django Ninja：
```bash
pip install django-ninja
```

## 基本配置

在你的 Django 項目中，你需要配置 Django Ninja。以下是基本配置步驟：
1. 添加到 INSTALLED_APPS：
   
   雖然不是必須的，但你可以將 `ninja` 添加到你的 `INSTALLED_APPS` 中：
   ```python
   # settings.py
   INSTALLED_APPS = [
       ...
       'ninja',
   ]
   ```

2. 創建 API 模塊
   
   在你的 Django 項目中創建一個 `api.py` 文件來定義你的 API 端點。
   ```python
   # api.py
   from ninja import NinjaAPI

   api = NinjaAPI()

   @api.get("/hello")
   def hello(request):
       return {"message": "Hello, world!"}
   ```

3. 配置 URL
   
   在 `urls.py` 文件中配置 API 的 URL 路徑。
   ```python
   # urls.py
   from django.urls import path
   from .api import api

   urlpatterns = [
       path("api/", api.urls),
   ]
   ```

4. 運行 API
   
   啟動 Django 開發伺服器來運行你的 API：
   ```bash
   python manage.py runserver
   ```

