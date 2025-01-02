# Query and Filter Records

Django ORM 提供了強大的查詢功能，允許開發者使用 Python 代碼來查詢和過濾數據庫記錄。這使得數據操作更加直觀和高效。

## 基本查詢
使用 `objects.all()` 方法來獲取所有記錄。
```python
from myapp.models import Book

# 獲取所有 Book 對象
all_books = Book.objects.all()
```

## 過濾紀錄
使用 `filter()` 方法來過濾記錄。`filter()` 方法返回一個查詢集，包含所有匹配條件的記錄。
```python
from myapp.models import Book

# 獲取所有由 William S. Vincent 撰寫的 Book 對象
books_by_author = Book.objects.filter(author="William S. Vincent")
```

## 獲取單個對象
使用 `get()` 方法來獲取單個對象。`get()` 方法返回唯一匹配條件的記錄，如果找不到或找到多個記錄，將引發異常。
```python
from myapp.models import Book

# 獲取 ID 為 1 的 Book 對象
book = Book.objects.get(id=1)
```

## 排序記錄
使用 order_by() 方法來對查詢結果進行排序。可以按一個或多個字段進行排序。
```python
from myapp.models import Book

# 按 published_date 字段升序排序
books_ordered = Book.objects.all().order_by('published_date')

# 按 published_date 字段降序排序
books_ordered_desc = Book.objects.all().order_by('-published_date')
```

## 限制記錄數量
使用切片來限制查詢結果的數量。
```python
from myapp.models import Book

# 獲取前 5 個 Book 對象
first_five_books = Book.objects.all()[:5]

# 獲取第 6 到第 10 個 Book 對象
books_6_to_10 = Book.objects.all()[5:10]
```

## 複雜查詢
可以使用鏈式調用來組合多個查詢方法，從而實現複雜的查詢。
```python
from myapp.models import Book

# 獲取所有由 William S. Vincent 撰寫且在 2020 年之後出版的 Book 對象，並按 published_date 降序排序
books_by_author_after_2020 = Book.objects.filter(author="William S. Vincent", published_date__gt="2020-01-01").order_by('-published_date')
```

## 查詢集操作
查詢集支持多種操作，如並集、交集和差集。
```python
from myapp.models import Book

# 獲取所有由 William S. Vincent 撰寫的 Book 對象
books_by_william = Book.objects.filter(author="William S. Vincent")

# 獲取所有在 2020 年之後出版的 Book 對象
books_after_2020 = Book.objects.filter(published_date__gt="2020-01-01")

# 獲取兩個查詢集的並集
union_books = books_by_william | books_after_2020

# 獲取兩個查詢集的交集
intersection_books = books_by_william & books_after_2020

# 獲取兩個查詢集的差集
difference_books = books_by_william.difference(books_after_2020)
```

## 使用 Q 對象進行複雜查詢
Q 對象可以用來構建複雜的查詢條件，支持邏輯運算符如 AND、OR 和 NOT。
```python
from myapp.models import Book
from django.db.models import Q

# 獲取所有由 William S. Vincent 撰寫或在 2020 年之後出版的 Book 對象
books = Book.objects.filter(Q(author="William S. Vincent") | Q(published_date__gt="2020-01-01"))
```

## 查詢關聯對象
可以通過關聯字段來查詢關聯對象。例如，查詢與特定作者相關的所有書籍。
```python
from myapp.models import Author, Book

# 獲取 ID 為 1 的 Author 對象
author = Author.objects.get(id=1)

# 獲取與該作者相關的所有 Book 對象
books_by_author = Book.objects.filter(author=author)
```

