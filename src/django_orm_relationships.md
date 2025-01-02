# Relationships in the Django ORM

Django ORM 支持定義和管理模型之間的關係，這使得數據庫設計更加靈活和強大。Django 支持三種主要的關係類型：一對多、多對多和一對一。

## 一對多關係
一對多關係是最常見的關係類型，通常用於表示一個模型與多個其他模型之間的關係。例如，一個作者可以撰寫多本書。

* 定義一對多關係：
  使用 `ForeignKey` 字段來定義一對多關係。
  ```python
  from django.db import models

  class Author(models.Model):
      name = models.CharField(max_length=100)

  class Book(models.Model):
      title = models.CharField(max_length=100)
      author = models.ForeignKey(Author, on_delete=models.CASCADE)
      published_date = models.DateField()
  ```

* 查詢一對多關係：
  可以通過關聯字段來查詢一對多關係。
  ```python
  # 獲取 ID 為 1 的 Author 對象
  author = Author.objects.get(id=1)

  # 獲取與該作者相關的所有 Book 對象
  books_by_author = Book.objects.filter(author=author)

  # 反向查詢：獲取特定 Book 對象的作者
  book = Book.objects.get(id=1)
  author_of_book = book.author
  ```

## 多對多關係
多對多關係用於表示兩個模型之間的多重關聯。例如，一本書可以有多個作者，一個作者也可以撰寫多本書。

* 定義多對多關係：
  使用 `ManyToManyField` 字段來定義多對多關係。
  ```python
  from django.db import models

  class Author(models.Model):
      name = models.CharField(max_length=100)

  class Book(models.Model):
      title = models.CharField(max_length=100)
      authors = models.ManyToManyField(Author)
      published_date = models.DateField()
  ```

* 查詢多對多關係：
  可以通過多對多字段來查詢關聯對象。
  ```python
  # 獲取 ID 為 1 的 Book 對象
  book = Book.objects.get(id=1)

  # 獲取與該書籍相關的所有 Author 對象
  authors_of_book = book.authors.all()

  # 反向查詢：獲取特定 Author 對象撰寫的所有書籍
  author = Author.objects.get(id=1)
  books_by_author = author.book_set.all()
  ```

## 一對一關係
一對一關係用於表示兩個模型之間的唯一關聯。例如，一個人只有一本護照。
* 定義一對一關係：
  使用 OneToOneField 字段來定義一對一關係。
  ```python
  from django.db import models

  class Person(models.Model):
      name = models.CharField(max_length=100)

  class Passport(models.Model):
      person = models.OneToOneField(Person, on_delete=models.CASCADE)
      number = models.CharField(max_length=100)
  ```

* 查詢一對一關係：
  可以通過一對一字段來查詢關聯對象。
  ```python
  # 獲取 ID 為 1 的 Person 對象
  person = Person.objects.get(id=1)

  # 獲取與該人相關的 Passport 對象
  passport = person.passport

  # 反向查詢：獲取特定 Passport 對象的人
  passport = Passport.objects.get(id=1)
  person_of_passport = passport.person
  ```

## 自引用關係
自引用關係是指模型與自身之間的關係。例如，員工模型中的經理字段可以引用同一模型中的另一個員工。

* 定義自引用關係：
  使用 `ForeignKey` 字段來定義自引用關係。
  ```python
  from django.db import models

  class Employee(models.Model):
      name = models.CharField(max_length=100)
      manager = models.ForeignKey('self', on_delete=models.SET_NULL, null=True, blank=True)
  ```

* 查詢自引用關係：
  可以通過自引用字段來查詢關聯對象。
  ```python
  # 獲取 ID 為 1 的 Employee 對象
  employee = Employee.objects.get(id=1)

  # 獲取該員工的經理
  manager = employee.manager

  # 反向查詢：獲取特定經理管理的所有員工
  manager = Employee.objects.get(id=2)
  employees_managed = manager.employee_set.all()
  ```