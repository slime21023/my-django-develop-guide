# Defining Django Models


在 Django 中，模型是數據的單一、明確的來源。每個模型映射到數據庫中的一個表，模型的屬性映射到表的列。要定義一個 Django 模型，需要繼承 django.db.models.Model 類，並定義模型的字段。

## 創建模型類
在 Django 應用的 models.py 文件中，創建一個繼承自 django.db.models.Model 的類。
```python
from django.db import models

class Book(models.Model):
    title = models.CharField(max_length=100)
    author = models.CharField(max_length=100)
    published_date = models.DateField()
```

## 定義字段
每個模型字段映射到數據庫表中的一列。Django 提供了多種字段類型，如 `CharField`、`DateField`、`IntegerField` 等。以下是一些常見的字段類型：
* `CharField`：用於存儲字符串數據，需要指定 max_length 參數。
* `TextField`：用於存儲大量文本數據。
* `IntegerField`：用於存儲整數數據。
* `DateField` 和 `DateTimeField`：用於存儲日期和日期時間數據。
* `BooleanField`：用於存儲布爾值（True/False）。
* `ForeignKey`：用於定義一對多關係。
* `ManyToManyField`：用於定義多對多關係。
* `OneToOneField`：用於定義一對一關係。

## 添加元數據
可以通過內部的 Meta 類來添加模型的元數據。例如，可以指定模型的排序順序、數據庫表名等。
```python
class Book(models.Model):
    title = models.CharField(max_length=100)
    author = models.CharField(max_length=100)
    published_date = models.DateField()

    class Meta:
        ordering = ['-published_date']
        db_table = 'book_table'
```

## 運行遷移
定義模型後，需要創建並應用數據庫遷移來創建相應的數據庫表。Django 提供了遷移工具來管理數據庫結構的變化。
```python
python manage.py makemigrations
python manage.py migrate
```

* `makemigrations`: 命令會生成遷移文件，這些文件記錄了模型的變化。
* `migrate`: 命令會應用遷移文件，將變化應用到數據庫中。

## 自定義方法
可以在模型類中添加自定義方法來擴展模型的功能。例如，可以添加一個方法來返回模型的字符串表示。
```python
class Book(models.Model):
    title = models.CharField(max_length=100)
    author = models.CharField(max_length=100)
    published_date = models.DateField()

    def __str__(self):
        return f"{self.title} by {self.author}"
```