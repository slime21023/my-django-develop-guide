# Aggregating and Grouping data

Django ORM 提供了強大的聚合和分組功能，允許開發者對查詢結果進行聚合操作，如計數、求和、平均值等，並且可以根據特定字段對數據進行分組。

## 聚合函數
Django ORM 提供了多種聚合函數，如 `Count`、`Sum`、`Avg`、`Max` 和 `Min`。這些函數可以用來對查詢結果進行聚合操作。
* 計數:
  使用 `Count` 函數來計算查詢結果中的記錄數量。
  ```python
  from django.db.models import Count
  from myapp.models import Book

  # 計算所有 Book 對象的數量
  total_books = Book.objects.aggregate(Count('id'))
  print(total_books)  # 輸出: {'id__count': 數量}
  ```

* 求和:
  使用 `Sum` 函數來計算查詢結果中特定字段的總和。  
  ```python
  from django.db.models import Sum
  from myapp.models import Book

  # 計算所有 Book 對象的總價格
  total_price = Book.objects.aggregate(Sum('price'))
  print(total_price)  # 輸出: {'price__sum': 總價格}
  ```

* 平均值:
  使用 Avg 函數來計算查詢結果中特定字段的平均值。
  ```python
  from django.db.models import Avg
  from myapp.models import Book

  # 計算所有 Book 對象的平均價格
  average_price = Book.objects.aggregate(Avg('price'))
  print(average_price)  # 輸出: {'price__avg': 平均價格}
  ```

* 最大值和最小值：
  使用 `Max` 和 `Min` 函數來計算查詢結果中特定字段的最大值和最小值。
  ```python
  from django.db.models import Max, Min
  from myapp.models import Book

  # 計算所有 Book 對象的最高價格和最低價格
  price_range = Book.objects.aggregate(Max('price'), Min('price'))
  print(price_range)  # 輸出: {'price__max': 最高價格, 'price__min': 最低價格}
  ```

## 分組
可以使用 `annotate()` 方法來根據特定字段對查詢結果進行分組，並對每組數據進行聚合操作。

* 基本分組：
  使用 `annotate()` 方法來對查詢結果進行分組，並計算每組的記錄數量。
  ```python
  from django.db.models import Count
  from myapp.models import Author

  # 計算每個作者撰寫的書籍數量
  authors_with_book_count = Author.objects.annotate(num_books=Count('book'))
  for author in authors_with_book_count:
      print(f"{author.name}: {author.num_books} books")
  ```

* 複雜分組：
  可以結合多個聚合函數來對查詢結果進行複雜的分組和聚合操作。
  ```python
  from django.db.models import Count, Avg
  from myapp.models import Author

  # 計算每個作者撰寫的書籍數量和平均價格
  authors_with_book_stats = Author.objects.annotate(num_books=Count('book'), avg_price=Avg('book__price'))
  for author in authors_with_book_stats:
      print(f"{author.name}: {author.num_books} books, average price: {author.avg_price}")
  ```

## 分組和過濾
可以結合 `filter()` 和 `annotate()` 方法來對查詢結果進行分組和過濾。
```python
from django.db.models import Count
from myapp.models import Author

# 計算每個作者在 2020 年之後出版的書籍數量
authors_with_recent_books = Author.objects.filter(book__published_date__gt="2020-01-01").annotate(num_recent_books=Count('book'))
for author in authors_with_recent_books:
    print(f"{author.name}: {author.num_recent_books} recent books")
```

## 使用 `values()` 進行分組
可以使用 `values()` 方法來對查詢結果進行分組，並返回分組後的字典列表。
```python
from django.db.models import Count
from myapp.models import Book

# 計算每個作者撰寫的書籍數量，並返回分組後的字典列表
book_count_by_author = Book.objects.values('author').annotate(num_books=Count('id'))
for entry in book_count_by_author:
    print(f"Author ID {entry['author']}: {entry['num_books']} books")
```

