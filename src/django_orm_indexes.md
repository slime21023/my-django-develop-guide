# Indexes, Constraints

在資料庫設計中，索引和約束是確保查詢性能和數據完整性的重要工具。Django ORM 提供了多種方法來定義和管理索引和約束，從而優化查詢性能並確保數據的一致性和完整性。

## 索引
索引可以顯著提高資料庫查詢的性能，特別是在處理大量數據時。Django ORM 允許在模型中定義索引，以便在資料庫層面創建相應的索引。

* 單列索引：
  使用 `db_index=True` 參數來為模型字段創建單列索引。
  ```python
  from django.db import models

  class Book(models.Model):
      title = models.CharField(max_length=100, db_index=True)
      author = models.CharField(max_length=100)
      published_date = models.DateField()
  ```

* 多列索引：
  使用 `Meta` 類中的 `indexes` 選項來定義多列索引。
  ```python
  from django.db import models

  class Book(models.Model):
      title = models.CharField(max_length=100)
      author = models.CharField(max_length=100)
      published_date = models.DateField()

      class Meta:
          indexes = [
              models.Index(fields=['title', 'author']),
          ]
  ```

* 部分索引：
  使用 `condition` 參數來定義部分索引，這些索引只在滿足特定條件的行上創建。
  ```python
  from django.db import models
  from django.db.models import Q

  class Book(models.Model):
      title = models.CharField(max_length=100)
      author = models.CharField(max_length=100)
      published_date = models.DateField()

      class Meta:
          indexes = [
              models.Index(fields=['published_date'], condition=Q(published_date__gt='2020-01-01')),
          ]
  ```

## 約束
約束用於確保數據的完整性和一致性。Django ORM 提供了多種約束類型，如唯一約束、檢查約束和外鍵約束。
* 唯一約束：
  使用 `unique=True` 參數來為模型字段創建唯一約束。
  ```python
  from django.db import models

  class Book(models.Model):
      isbn = models.CharField(max_length=13, unique=True)
      title = models.CharField(max_length=100)
      author = models.CharField(max_length=100)
      published_date = models.DateField()
  ```

* 檢查約束：
  使用 `Meta` 類中的 `constraints` 選項來定義檢查約束。檢查約束可以用來強制數據滿足特定條件。
  ```python
  from django.db import models
  from django.db.models import Q, CheckConstraint

  class Book(models.Model):
      title = models.CharField(max_length=100)
      author = models.CharField(max_length=100)
      published_date = models.DateField()

      class Meta:
          constraints = [
              CheckConstraint(check=Q(published_date__lte='2021-01-01'), name='published_date_check'),
          ]
  ```

* 外鍵約束：
  使用 `on_delete` 參數來定義外鍵約束，這些約束決定了當引用的對象被刪除時應該如何處理外鍵關係。
  ```python
  from django.db import models

  class Author(models.Model):
      name = models.CharField(max_length=100)

  class Book(models.Model):
      title = models.CharField(max_length=100)
      author = models.ForeignKey(Author, on_delete=models.CASCADE)
      published_date = models.DateField()
  ```

* 複合唯一約束：
  使用 `UniqueConstraint` 來定義複合唯一約束，這些約束確保多個字段的組合是唯一的。
  ```python
  from django.db import models
  from django.db.models import UniqueConstraint

  class Book(models.Model):
      title = models.CharField(max_length=100)
      author = models.CharField(max_length=100)
      published_date = models.DateField()

      class Meta:
          constraints = [
              UniqueConstraint(fields=['title', 'author'], name='unique_book'),
          ]
  ```

