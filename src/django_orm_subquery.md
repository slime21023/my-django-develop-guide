# Subqueries, Conditional Expressions

Django ORM 提供了強大的子查詢和條件表達式功能，允許開發者進行複雜的數據查詢和操作。子查詢可以用來引用其他查詢的結果，而條件表達式可以用來根據特定條件動態生成查詢結果。

## 子查詢
子查詢允許在一個查詢中引用另一個查詢的結果。這在需要引用相關模型的數據或進行複雜查詢時非常有用。

* 基本子查詢：
  使用 `Subquery` 和 `OuterRef` 來創建子查詢。
  ```python
  from django.db.models import OuterRef, Subquery
  from myapp.models import Author, Book

  # 獲取每個作者最新出版的書籍標題
  latest_books = Book.objects.filter(author=OuterRef('pk')).order_by('-published_date').values('title')[:1]
  authors_with_latest_book = Author.objects.annotate(latest_book_title=Subquery(latest_books))

  for author in authors_with_latest_book:
      print(f"{author.name}: {author.latest_book_title}")
  ```

* 子查詢過濾:
  可以在子查詢中使用過濾條件來進行更複雜的查詢。
  ```python
  from django.db.models import OuterRef, Subquery, Count
  from myapp.models import Author, Book

  # 獲取每個作者在 2020 年之後出版的書籍數量
  recent_books_count = Book.objects.filter(author=OuterRef('pk'), published_date__gt="2020-01-01").values('author').annotate(count=Count('id')).values('count')
  authors_with_recent_books_count = Author.objects.annotate(recent_books_count=Subquery(recent_books_count))

  for author in authors_with_recent_books_count:
      print(f"{author.name}: {author.recent_books_count} recent books")
  ```


## 條件表達式
條件表達式允許根據特定條件動態生成查詢結果。這在需要根據數據的不同狀態進行不同操作時非常有用。

* 基本條件表達式：
  使用 `When` 和 `Case` 來創建條件表達式。
  ```python
  from django.db.models import Case, When, Value, IntegerField
  from myapp.models import Book

  # 根據出版日期將書籍分類為新書或舊書
  books_with_status = Book.objects.annotate(
      status=Case(
          When(published_date__lt="2020-01-01", then=Value('Old')),
          When(published_date__gte="2020-01-01", then=Value('New')),
          output_field=IntegerField(),
      )
  )

  for book in books_with_status:
      print(f"{book.title}: {book.status}")
  ```

* 複雜條件表達式：
  可以結合多個條件表達式來進行更複雜的查詢。
  ```python
  from django.db.models import Case, When, Value, CharField
  from myapp.models import Book

  # 根據出版日期和價格將書籍分類
  books_with_category = Book.objects.annotate(
      category=Case(
          When(published_date__lt="2020-01-01", price__lt=50, then=Value('Old and Cheap')),
          When(published_date__lt="2020-01-01", price__gte=50, then=Value('Old and Expensive')),
          When(published_date__gte="2020-01-01", price__lt=50, then=Value('New and Cheap')),
          When(published_date__gte="2020-01-01", price__gte=50, then=Value('New and Expensive')),
          output_field=CharField(),
      ) 
  )

  for book in books_with_category:
      print(f"{book.title}: {book.category}")
  ```

## 結合子查詢和條件表達式
可以結合子查詢和條件表達式來進行更靈活和強大的查詢。
```python
from django.db.models import OuterRef, Subquery, Case, When, Value, CharField
from myapp.models import Author, Book

# 獲取每個作者最新出版的書籍標題，並根據出版日期將書籍分類
latest_books = Book.objects.filter(author=OuterRef('pk')).order_by('-published_date').values('title', 'published_date')[:1]
authors_with_latest_book = Author.objects.annotate(
    latest_book_title=Subquery(latest_books.values('title')),
    latest_book_status=Case(
        When(latest_books__published_date__lt="2020-01-01", then=Value('Old')),
        When(latest_books__published_date__gte="2020-01-01", then=Value('New')),
        output_field=CharField(),
    )
)

for author in authors_with_latest_book:
    print(f"{author.name}: {author.latest_book_title} ({author.latest_book_status})")
```