# Creating Data with Django Models

一旦模型定義好並應用了遷移，就可以使用模型來創建和操作數據。Django ORM 提供了多種方法來創建和保存數據庫記錄。

## 創建對象

可以通過實例化模型類並調用 `save()` 方法來創建新的數據庫記錄。
```python
from myapp.models import Book

# 創建一個新的 Book 對象
book = Book(title="Django for Beginners", author="William S. Vincent", published_date="2020-01-01")
# 保存對象到資料庫
book.save()
```

## 使用 `create()` 方法
也可以使用模型的 `objects.create()` 方法來創建對象，這種方法會自動保存對象到資料庫。
```python
from myapp.models import Book

# 使用 create() 方法創建並保存一個新的 Book 對象
Book.objects.create(title="Two Scoops of Django", author="Audrey Roy Greenfeld", published_date="2019-01-01")
```

## 批量創建對象
可以使用 `bulk_create()` 方法來批量創建對象。這種方法可以顯著提高性能，特別是在需要創建大量對象時。
```python
from myapp.models import Book

# 創建多個 Book 對象
books = [
    Book(title="Django for Professionals", author="William S. Vincent", published_date="2021-01-01"),
    Book(title="Full-Stack Django and React", author="Andrew Farmer", published_date="2020-05-01"),
]
# 批量創建並保存對象到數據庫
Book.objects.bulk_create(books)
```

## 創建關聯對象
如果模型之間有關聯（如外鍵關係），可以通過關聯對象來創建和保存數據。
```python
from myapp.models import Author, Book

# 創建一個新的 Author 對象
author = Author.objects.create(name="William S. Vincent")

# 創建一個新的 Book 對象，並與 Author 對象關聯
book = Book.objects.create(title="Django for Beginners", author=author, published_date="2020-01-01")
```

## 更新對象
可以通過修改對象的屬性並調用 `save()` 方法來更新現有的資料庫記錄。
```python
from myapp.models import Book

# 獲取一個現有的 Book 對象
book = Book.objects.get(id=1)
# 修改對象的屬性
book.title = "Django for Beginners (Updated)"
# 保存更新到數據庫
book.save()
```

## 刪除對象
可以通過調用對象的 `delete()` 方法來刪除資料庫記錄。
```python
from myapp.models import Book

# 獲取一個現有的 Book 對象
book = Book.objects.get(id=1)
# 刪除對象
book.delete()
```