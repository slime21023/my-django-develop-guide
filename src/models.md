# Models

## 基礎

Django Models 用來定義資料庫中資料表的資訊、及對映的關係。每個模型是 `django.db.models.Model` 的子類，並且每個模型的 `Field` 對應於資料庫表的 `Column` 

## 定義模型

模型是通過繼承 django.db.models.Model 類來定義的。以下是一個簡單的模型示例：
```python
from django.db import models

class MyModel(models.Model):
    name = models.CharField(max_length=100)
    description = models.TextField()
    created_at = models.DateTimeField(auto_now_add=True)

    def __str__(self):
        return self.name
```

## Fields 的定義與種類

Django 提供了多種 Field 的定義方式，以下是一些常見的 Field 的定義方式：
* **CharField**: 用於存儲字符串數據。
  ```python
  name = models.CharField(max_length=100)
  ```

* **TextField**: 用於存儲大量文本數據。
  ```python
  description = models.TextField()
  ```

* **IntegerField**: 用於存儲整數數據。
  ```python
  age = models.IntegerField()
  ```

* **DateTimeField**: 用於存儲日期和時間數據。
  ```python
  created_at = models.DateTimeField(auto_now_add=True)
  ```

* **BooleanField**:  用於存儲布爾值（True/False）。
  ```python
  is_active = models.BooleanField(default=True)
  ```

* **FloatField**: 用於存儲浮點數數據。
  ```python
  price = models.FloatField()
  ```

* **EmailField**: 用於存儲電子郵件地址。
  ```python
  email = models.EmailField()
  ```

* **URLField**: 用於存儲網址。
  ```python
  website  = models.URLField()
  ```


**Field 選項**: 每個字段都可以有一組選項來控制其行為。以下是一些常見的字段選項：

* `null`: 如果為 True，則允許欄位的空值為NULL。
  ```python
  name = models.CharField(max_length=100, null=True)
  ```

* `blank`: 如果為 True，則允許欄位的空值。
  ```python
  name = models.TextField(max_length=100, blank=True)
  ```

* `default`: 如果欄位未被填寫，則允許欄位的預設值。
  ```python
  is_active = models.BooleanField(default=True)
  ```

* `unique`: 如果為 True，則允許欄位的值僅能在資料庫中唯一。
  ```python
  email = models.EmailField(unique=True)
  ```

* `choices`: 如果欄位的值可以從一組可選值中選擇，則可以使用 `choices` 選項。
  ```python
  COLOR_CHOICES = [
    ('red', 'Red'), 
    ('green', 'Green'), 
    ('blue', 'Blue')
  ]
  color = models.CharField(max_length=100, choices=COLOR_CHOICES)
  ```

## 主鍵與外鍵

主鍵 (Primary Key): 每個模型都有一個主鍵字段，默認情況下是 id 字段。你可以通過設置 primary_key=True 來指定其他字段作為主鍵。
```python
id = models.AutoField(primary_key=True)
```

外鍵 (Foreign Key): 用於定義模型之間的一對多關係。
```python
category = models.ForeignKey('Category', on_delete=models.CASCADE)
```

## Relationships 關係

Django 支持定義模型之間的關係，包括一對一（OneToOneField）、一對多（ForeignKey）和多對多（ManyToManyField）關係。

* 一對一關係:
  ```python
  class Profile(models.Model):
    user = models.OneToOneField(User, on_delete=models.CASCADE)
    bio = models.TextField()
  ```

* 一對多關係:
  ```python
  class Category(models.Model):
    name = models.CharField(max_length=100)

  class Product(models.Model):
    name = models.CharField(max_length=100)
    category = models.ForeignKey(Category, on_delete=models.CASCADE)
  ```

* 多對多關係:
  ```python
  class Author(models.Model):
    name = models.CharField(max_length=100)

  class Book(models.Model):
    title = models.CharField(max_length=100)
    authors = models.ManyToManyField(Author)
  ```

## Model Meta

模型元數據選項允許你自定義模型的行為。以下是一些常見的模型元數據選項：
* `ordering`: 指定模型的默認排序順序。
  ```python
  class Meta:
    ordering = ['-created_at']
  ```

* `verbose_name`: 指定模型的名稱。
  ```python
  class Meta:
    verbose_name = 'My Model'
  ```

* `verbose_name_plural`: 指定模型的複數名稱。
  ```python
  class Meta:
    verbose_name_plural = 'My Models'
  ```

* `unique_together`: 指定多個Field的組合必須是唯一的。
  ```python
  class Meta:
    unique_together = ('name', 'description')
  ```

## Model Methods
你可以在模型中定義方法來添加自定義行為。以下是一些常見的模型方法：

* 自定義方法:
  ```python
  class MyModel(models.Model):
    name = models.CharField(max_length=100)
    created_at = models.DateTimeField(auto_now_add=True)

    def is_recent(self):
        return self.created_at >= timezone.now() - datetime.timedelta(days=1)
  ```

* `__str__` 方法: 返回模型實例的字符串表示。
  ```python
  class MyModel(models.Model):
    name = models.CharField(max_length=100)

    def __str__(self):
        return self.name
  ```

* `save` 方法: 在保存模型實例之前或之後執行自定義邏輯。
  ```python
  class MyModel(models.Model):
    name = models.CharField(max_length=100)

    def save(self, *args, **kwargs):
        self.name = self.name.upper()
        super().save(*args, **kwargs)
  ```

