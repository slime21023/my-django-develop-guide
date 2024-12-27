# Admin

## 什麼是 Django Admin？
Django Admin 是一個自動生成的管理界面，它允許你通過 Web 界面來管理 Application 的資料。它是 Django 的一部分，並且可以自動生成基於你的模型的管理界面。

## 啟用 Django Admin

要啟用 Django Admin，你需要確保 `django.contrib.admin` 和 `django.contrib.auth` 已經添加到你的 `INSTALLED_APPS` 設置中。

```python
# settings.py
INSTALLED_APPS = [
    ...
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    ...
]
```

## 創建管理員用戶

你需要創建一個管理員用戶來訪問 Django Admin 界面。你可以使用以下命令來創建管理員用戶：
```bash
python manage.py createsuperuser
```

## 配置 Django Admin 介面 URL
你需要在你的 urls.py 文件中配置 Django Admin 的 URL。
```python
# urls.py
from django.contrib import admin
from django.urls import path

urlpatterns = [
    path('admin/', admin.site.urls),
    ...
]
```

## 註冊模型
要在 Django Admin 中管理你的模型，你需要在 admin.py 文件中註冊它們。
```python
# admin.py
from django.contrib import admin
from .models import MyModel

admin.site.register(MyModel)
```

## 自定義 Admin 介面

通過創建自定義的 ModelAdmin 類來自定義 Admin 介面:
```python
# admin.py
from django.contrib import admin
from .models import MyModel

class MyModelAdmin(admin.ModelAdmin):
    list_display = ('name', 'created_at')
    list_filter = ('created_at',)
    search_fields = ('name',)

admin.site.register(MyModel, MyModelAdmin)
```

#### 自定義列表顯示

你可以使用 `list_display` 選項來自定義列表頁面顯示的字段。
```python
class MyModelAdmin(admin.ModelAdmin):
    list_display = ('name', 'created_at')
```

#### 添加過濾器

你可以使用 `list_filter` 選項來添加過濾器到列表頁面。
```python
class MyModelAdmin(admin.ModelAdmin):
    list_filter = ('created_at',)
```

#### 添加搜索功能
你可以使用 `search_fields` 選項來添加搜索功能到列表頁面。
```python
class MyModelAdmin(admin.ModelAdmin):
    search_fields = ('name',)
```

#### 自定義表單
你可以使用 form 選項來自定義表單。

```python
from django import forms

class MyModelForm(forms.ModelForm):
    class Meta:
        model = MyModel
        fields = ['name', 'created_at']

class MyModelAdmin(admin.ModelAdmin):
    form = MyModelForm
```

#### 自定義動作
你可以添加自定義動作到 Admin 界面。

```python
from django.contrib import admin
from django.utils.translation import gettext_lazy as _

def make_published(modeladmin, request, queryset):
    queryset.update(status='p')
make_published.short_description = _("Mark selected stories as published")

class MyModelAdmin(admin.ModelAdmin):
    actions = [make_published]
```

#### 自定義網站資訊
你可以自定義 Admin 站點的標題和標頭。

```python
# admin.py
from django.contrib.admin import AdminSite

class MyAdminSite(AdminSite):
    site_header = 'My Admin'
    site_title = 'My Admin Portal'
    index_title = 'Welcome to My Admin Portal'

admin_site = MyAdminSite(name='myadmin')
admin_site.register(MyModel, MyModelAdmin)
```