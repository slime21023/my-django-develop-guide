# Database Setup

## 配置資料庫
Django 默認使用 SQLite 數據庫，但你可以根據需要配置其他數據庫，例如 PostgreSQL、MySQL 或 Oracle。

SQLite 是 Django 的默認數據庫，不需要額外配置。數據庫文件將存儲在專案目錄中的 db.sqlite3 文件中。

### 配置 PostgreSQL

首先，安裝 PostgreSQL 和 psycopg2 驅動程式：
```bash
pip install psycopg2-binary
```

然後在 `settings.py` 文件中配置資料庫：
```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': 'mydatabase',
        'USER': 'mydatabaseuser',
        'PASSWORD': 'mypassword',
        'HOST': '127.0.0.1',
        'PORT': '5432',
    }
}
```

### 配置 MySQL

首先，安裝 MySQL 和 mysqlclient 驅動程式：
```bash
pip install mysqlclient
```

然後在 `settings.py` 文件中配置資料庫：
```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'mydatabase',
        'USER': 'mydatabaseuser',
        'PASSWORD': 'mypassword',
        'HOST': '127.0.0.1',
        'PORT': '3306',
    }
}
```

## 配置資料庫模型

在 `myapp/models.py` 文件中定義資料庫模型：
```python
from django.db import models

class MyModel(models.Model):
    name = models.CharField(max_length=100)
    description = models.TextField()

    def __str__(self):
        return self.name
```

## 創建和執行資料庫 migrations

創建初始數據庫遷移文件：
```bash
python manage.py makemigrations
```

執行遷移：
```bash
python manage.py migrate
```

## 配置數據庫連接池（可選）

如果你使用的是 PostgreSQL 或 MySQL，你可能希望配置數據庫連接池以提高性能。你可以使用 `django-db-geventpool` 或其他連接池庫來實現這一點。

