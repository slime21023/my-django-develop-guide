# Static Files

## 什麼是 Django 靜態文件？

Django 靜態文件是那些不會隨請求改變的文件，例如 CSS、JavaScript 和圖像。這些文件通常用於前端設計和用戶界面。

## 配置靜態文件設定
要在 Django 中使用靜態文件，你需要在設置文件中進行一些配置。

### 設置靜態文件目錄

在 `settings.py` 文件中，配置 `STATIC_URL` 和 `STATICFILES_DIRS`：
```python
# settings.py
import os

BASE_DIR = os.path.dirname(os.path.dirname(os.path.abspath(__file__)))

STATIC_URL = '/static/'

# BASE_DIR 是專案的絕對路徑
STATIC_ROOT = os.path.join(BASE_DIR, 'collect_static')

# 非必須
STATICFILES_DIRS = [os.path.join(BASE_DIR, 'common_static')]
```

## 組織靜態文件

你可以在應用的 static 目錄中組織靜態文件。例如：

```
myproject/
    myapp/
        static/
            myapp/
                css/
                js/
                images/
        templates/
        ...
    static/
        css/
        js/
        images/
    ...
```

## 引用靜態文件

在模板中，你可以使用 `{% static %}` 模板標籤來引用靜態文件：
```html
{% load static %}
<!DOCTYPE html>
<html>
<head>
    <link rel="stylesheet" type="text/css" href="{% static 'myapp/css/styles.css' %}">
    <script src="{% static 'myapp/js/scripts.js' %}"></script>
</head>
<body>
    <img src="{% static 'myapp/images/logo.png' %}" alt="Logo">
</body>
</html>
```

## 服務靜態文件

在開發過程中，Django 會自動服務靜態文件。然而，在生產環境中，你應該使用一個專門的 Web 服務器（如 Nginx 或 Apache）來服務靜態文件。

#### 開發環境
在開發環境中，Django 會自動服務靜態文件。你只需確保 `DEBUG` 設置為 `True`：
```python
# settings.py
DEBUG = True
```

#### 生產環境
在生產環境中，你應該配置 Web 服務器來服務靜態文件。以下是一個使用 Nginx 的示例配置：
```nginx
server {
    listen 80;
    server_name myapp.com;

    location /static/ {
        alias /path/to/myproject/static/;
    }

    location / {
        proxy_pass http://127.0.0.1:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

## 使用第三方存儲服務

你還可以使用第三方存儲服務（如 Amazon S3）來存儲和服務靜態文件。你需要安裝 `django-storages` 和 `boto3` 包：

```bash
pip install django-storages boto3
```

然後在 `settings.py` 文件中配置存儲後端：

```python
# settings.py
INSTALLED_APPS += ['storages']

AWS_ACCESS_KEY_ID = 'your-access-key-id'
AWS_SECRET_ACCESS_KEY = 'your-secret-access-key'
AWS_STORAGE_BUCKET_NAME = 'your-bucket-name'
AWS_S3_CUSTOM_DOMAIN = f'{AWS_STORAGE_BUCKET_NAME}.s3.amazonaws.com'
AWS_S3_FILE_OVERWRITE = False
AWS_DEFAULT_ACL = None

STATICFILES_STORAGE = 'storages.backends.s3boto3.S3Boto3Storage'
```

