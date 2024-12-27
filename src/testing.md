# Testing

## 什麼是 Django 測試？

Django 測試框架是一個基於 Python 的 unittest 模塊的測試框架。它允許你編寫和運行測試來驗證你的應用程序的行為。測試可以幫助你確保你的代碼在不同情況下都能正常工作。

## 設置測試環境

可以在 `settings.py` 文件中配置測試數據庫：
```python
# settings.py
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': BASE_DIR / "db.sqlite3",
        'TEST': {
            'NAME': BASE_DIR / "test_db.sqlite3",
        },
    }
}
```

## 編寫測試

Django 測試框架允許你編寫不同類型的測試，包括單元測試和集成測試。

#### 編寫單元測試
單元測試用於測試單個函數或方法的行為，以下是一個簡單的單元測試示例：

```python
# tests.py
from django.test import SimpleTestCase

class MyTestCase(SimpleTestCase):
    def test_addition(self):
        self.assertEqual(1 + 1, 2)
```

#### 編寫集成測試
集成測試用於測試多個組件之間的交互，以下是一個簡單的集成測試示例：
```python
# tests.py
from django.test import TestCase
from myapp.models import MyModel

class MyModelTestCase(TestCase):
    def setUp(self):
        MyModel.objects.create(name="Test", description="This is a test")

    def test_my_model(self):
        obj = MyModel.objects.get(name="Test")
        self.assertEqual(obj.description, "This is a test")
```

## 測試視圖
使用 TestCase 類來測試視圖，以下是一個測試視圖的示例：
```python
# tests.py
from django.test import TestCase
from django.urls import reverse

class MyViewTests(TestCase):
    def test_my_view(self):
        response = self.client.get(reverse('my_view'))
        self.assertEqual(response.status_code, 200)
        self.assertContains(response, "Hello, world!")
```

## 測試模型
使用 TestCase 類來測試模型，以下是一個測試模型的示例：
```python
# tests.py
from django.test import TestCase
from myapp.models import MyModel

class MyModelTests(TestCase):
    def test_my_model(self):
        obj = MyModel.objects.create(name="Test", description="This is a test")
        self.assertEqual(obj.name, "Test")
        self.assertEqual(obj.description, "This is a test")
```

## 測試表單
使用 TestCase 類來測試表單，以下是一個測試表單的示例：
```python
# tests.py
from django.test import TestCase
from myapp.forms import MyForm

class MyFormTests(TestCase):
    def test_my_form(self):
        form_data = {'name': 'Test', 'description': 'This is a test'}
        form = MyForm(data=form_data)
        self.assertTrue(form.is_valid())
```

## 運行測試

可以使用以下命令來運行測試：
```bash
python manage.py test
```

## 測試覆蓋率
使用 coverage 工具來測量你的測試覆蓋率。首先，安裝 coverage 工具：
```bash
pip install coverage
```
然後運行以下命令來測量測試覆蓋率：
```bash
coverage run --source='.' manage.py test
coverage report
```

## 進階測試
Django 測試框架還提供了一些測試技術，例如模擬請求和響應、測試客戶端和測試用戶。

#### 模擬請求和響應
使用 `RequestFactory` 來模擬請求和響應：
```python
# tests.py
from django.test import RequestFactory, TestCase
from myapp.views import my_view

class MyViewTests(TestCase):
    def setUp(self):
        self.factory = RequestFactory()

    def test_my_view(self):
        request = self.factory.get('/my-view/')
        response = my_view(request)
        self.assertEqual(response.status_code, 200)
```


#### 測試客戶端
使用 `Client` 類來模擬用戶與應用程序的交互：
```python
# tests.py
from django.test import Client, TestCase

class MyViewTests(TestCase):
    def setUp(self):
        self.client = Client()

    def test_my_view(self):
        response = self.client.get('/my-view/')
        self.assertEqual(response.status_code, 200)
```


#### 測試用戶
使用 User 模型來測試用戶相關的功能：
```python
# tests.py
from django.contrib.auth.models import User
from django.test import TestCase

class MyViewTests(TestCase):
    def setUp(self):
        self.user = User.objects.create_user(username='testuser', password='12345')

    def test_my_view(self):
        self.client.login(username='testuser', password='12345')
        response = self.client.get('/my-view/')
        self.assertEqual(response.status_code, 200)
```