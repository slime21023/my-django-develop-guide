# Transactions

在資料庫操作中，事務是一組原子操作，這些操作要麼全部成功，要麼全部失敗。事務管理是確保數據一致性和完整性的重要手段。Django ORM 提供了強大的事務管理功能，允許開發者在數據庫操作中使用事務來確保數據的一致性。

## 基本事務管理
Django 提供了 `transaction.atomic()` 上下文管理器來創建事務塊。在事務塊內的所有數據庫操作要麼全部成功，要麼全部失敗。
```python
from django.db import transaction
from myapp.models import Book

try:
    with transaction.atomic():
        # 創建一個新的 Book 對象
        book1 = Book.objects.create(title="Book 1", author="Author 1", published_date="2021-01-01")
        # 創建另一個新的 Book 對象
        book2 = Book.objects.create(title="Book 2", author="Author 2", published_date="2021-01-01")
        # 如果這裡發生異常，事務將回滾
        raise ValueError("Something went wrong")
except ValueError:
    # 事務回滾，數據庫狀態保持不變
    print("Transaction failed and rolled back")
```

## 事務隔離級別

可以使用 `transaction.set_autocommit()` 方法來設置事務的隔離級別。隔離級別決定了事務之間的可見性和並發控制。
```python
from django.db import transaction

# 設置事務的隔離級別為 READ COMMITTED
transaction.set_autocommit(False)

try:
    # 開始事務
    with transaction.atomic():
        # 執行數據庫操作
        book = Book.objects.create(title="Book 3", author="Author 3", published_date="2021-01-01")
        # 提交事務
        transaction.commit()
except:
    # 事務回滾
    transaction.rollback()
finally:
    # 恢復自動提交模式
    transaction.set_autocommit(True)
```

## 嵌套事務
Django 支持嵌套事務，這允許在一個事務塊內創建另一個事務塊。內部事務塊的操作將在外部事務塊提交或回滾時一起提交或回滾。
```python
from django.db import transaction
from myapp.models import Book

try:
    with transaction.atomic():
        # 外部事務塊
        book1 = Book.objects.create(title="Book 4", author="Author 4", published_date="2021-01-01")

        try:
            with transaction.atomic():
                # 內部事務塊
                book2 = Book.objects.create(title="Book 5", author="Author 5", published_date="2021-01-01")
                # 如果這裡發生異常，內部事務將回滾
                raise ValueError("Inner transaction failed")
        except ValueError:
            # 內部事務回滾，外部事務繼續
            print("Inner transaction rolled back")

        # 外部事務塊繼續執行
        book3 = Book.objects.create(title="Book 6", author="Author 6", published_date="2021-01-01")
except ValueError:
    # 外部事務回滾
    print("Outer transaction rolled back")
```

## 事務保存點
可以使用保存點來部分回滾事務。保存點允許在事務中標記特定點，並在需要時回滾到該點。

```python
from django.db import transaction
from myapp.models import Book

sid = transaction.savepoint()

try:
    # 執行數據庫操作
    book1 = Book.objects.create(title="Book 7", author="Author 7", published_date="2021-01-01")
    # 如果這裡發生異常，回滾到保存點
    raise ValueError("Something went wrong")
except ValueError:
    # 回滾到保存點
    transaction.savepoint_rollback(sid)
    print("Rolled back to savepoint")

# 繼續執行其他操作
book2 = Book.objects.create(title="Book 8", author="Author 8", published_date="2021-01-01")
```

## 事務鎖
在某些情況下，可能需要在事務中鎖定特定的數據行，以防止其他事務修改這些數據。Django 提供了 `select_for_update()` 方法來鎖定查詢集中的數據行。
```python
from django.db import transaction
from myapp.models import Book

with transaction.atomic():
    # 鎖定特定的 Book 對象
    book = Book.objects.select_for_update().get(id=1)
    # 執行數據庫操作
    book.title = "Updated Title"
    book.save()
```